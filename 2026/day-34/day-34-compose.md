# Day 34: Docker Compose, Real World Multi Container Apps

## Goal
Move from basic Compose files to something closer to production: an app plus database plus cache stack, with healthchecks, dependency ordering, and restart policies.

## The Stack

Three services: Flask app, Postgres, Redis. Built the Flask app to actually connect to both, not just sit there.

### docker-compose.yml

```yaml
services:
  web:
    build: ./app
    container_name: day34-web
    ports:
      - "8080:5000"   # 8080 because Nginx already owns port 80 on this box
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: apppass123
    depends_on:
      db:
        condition: service_healthy   # waits for DB to be truly ready, not just started
    networks:
      - app-network
    labels:
      - "project=90daysofdevops"
      - "day=34"
      - "service=webapp"

  db:
    image: postgres:16   # deliberately not 18, see Day 32 notes on the data dir breaking change
    container_name: day34-db
    restart: always
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: apppass123
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser -d myapp"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - app-network
    labels:
      - "project=90daysofdevops"
      - "day=34"
      - "service=database"

  cache:
    image: redis:7-alpine
    container_name: day34-cache
    restart: always
    networks:
      - app-network
    labels:
      - "project=90daysofdevops"
      - "day=34"
      - "service=cache"

networks:
  app-network:
    driver: bridge

volumes:
  pgdata:
```

### app/app.py

```python
from flask import Flask
import psycopg2
import redis
import os

app = Flask(__name__)

# Connects to the 'cache' service by name, Docker's embedded DNS resolves it
cache = redis.Redis(host='cache', port=6379, decode_responses=True)

def get_db_connection():
    return psycopg2.connect(
        host="db",  # same story, service name resolves via user defined bridge network
        database=os.environ.get("POSTGRES_DB"),
        user=os.environ.get("POSTGRES_USER"),
        password=os.environ.get("POSTGRES_PASSWORD")
    )

@app.route('/')
def home():
    visits = cache.incr('visits')  # Redis handles the counter, proves cache is reachable
    return f"Hello from Flask! This page has been visited {visits} times."

@app.route('/db-check')
def db_check():
    try:
        conn = get_db_connection()
        cur = conn.cursor()
        cur.execute("SELECT version();")
        version = cur.fetchone()
        cur.close()
        conn.close()
        return f"Connected to Postgres: {version[0]}"
    except Exception as e:
        return f"DB connection failed: {str(e)}"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## What Actually Went Wrong (the useful part)

### 1. Port 8080 was already taken, and it took a while to find out why

First `docker compose up` failed with: Bind for 0.0.0.0:8080 failed: port is already allocated


Turned out Day 33's WordPress stack (`compose-wordpress-wordpress-1`) was still running, 40 hours later, still bound to 8080. Old stacks from previous days don't stop themselves. Had to explicitly:

```bash
cd ~/Docker/day33/compose-wordpress
docker compose down
```

Lesson: check `docker ps -a` for leftovers from earlier days before starting a new one. On a small EC2 box, old stacks silently eating memory and ports is a recurring problem, not a one off.

### 2. The web container came up "started" but wasn't actually listening

Even after freeing the port, `docker compose ps` showed `web` as `Up`, but `docker port day34-web` returned nothing at all. No port binding.

Root cause: the container had originally been *created* while port 8080 was still locked. `docker compose up -d` just started that same broken container rather than recreating it, since the compose file hadn't changed. Fix was:

```bash
docker compose up -d --force-recreate web
```

That rebuilt the container fresh with the port now available, and `docker port day34-web` correctly showed `5000/tcp -> 0.0.0.0:8080`.

Lesson: `docker compose up -d` will happily reuse a stale, half broken container if the compose file itself hasn't changed. If something looks "up" but isn't behaving, force recreate before assuming the config is wrong.

### 3. Restart policy testing revealed a real distinction

Set `restart: always` on the `db` service, then ran:

```bash
docker kill day34-db
```

Expected it to bounce back automatically. It didn't. Checked the restart count:

```bash
docker inspect day34-db --format='{{.RestartCount}}'
# 0
```

Dug into the Docker daemon logs:

```bash
sudo journalctl -u docker --since "20 minutes ago" --no-pager
```

Found the actual reason: error="restart canceled" hasBeenManuallyStopped=true restartCount=0



**`docker kill` and `docker stop` are treated as intentional actions, and `restart: always` deliberately does not override them.** The policy is meant to recover from crashes and daemon restarts, not to fight a human who explicitly told the container to stop.

To actually trigger the policy, killed the process inside the container instead of the container itself:

```bash
docker exec day34-db pkill postgres
```

This time:

```bash
docker inspect day34-db --format='{{.RestartCount}}'
# 1
```

It restarted on its own and came back `healthy`.

**Summary table:**

| Action | Restart policy triggers? |
|---|---|
| Container crashes internally (process dies, OOM) | Yes |
| Docker daemon restarts | Yes |
| `docker kill` / `docker stop` | No, treated as manual |
| Killing the process inside via `exec` | Yes, looks like a real crash to Docker |

## Resource Notes

This EC2 instance (`t3.micro`, 908 MiB RAM, no swap) genuinely struggled today running three new containers alongside leftovers from Day 33. `docker ps -a` hung entirely at one point rather than erroring. Considered adding a 2GB swap file to fix this permanently, decided against it for now, the workaround is discipline: `docker compose down` on finished stacks before starting the next day's work, rather than letting them pile up.

## Still To Do
- Task 3: compare `on-failure` against `always` with a genuine non-zero exit code
- Task 4: rebuild after a code change using `docker compose up --build`
- Task 5: networks and volumes already done as part of the base stack, revisit for depth
- Task 6: scaling with `--scale web=3` and the port conflict it causes