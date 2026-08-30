# Day 33 – Docker Compose: Multi-Container Basics

## Task
Today's goal is to **run multi-container applications with a single command**.

Yesterday you manually created networks and volumes and ran containers one by one. Docker Compose does all of that in one YAML file.

---

## Expected Output
- A markdown file: `day-33-compose.md`
- All `docker-compose.yml` files you create

---

## Challenge Tasks

### Task 1: Install & Verify
1. Check if Docker Compose is available on your machine
sudo apt install docker-compose-plugin -y

2. Verify the version

ubuntu@devops-ai-powered:~/Docker/day33$ docker compose version
Docker Compose version v5.5.0

---

### Task 2: Your First Compose File
1. Create a folder `compose-basics`

2. Write a `docker-compose.yml` that runs a single **Nginx** container with port mapping
ubuntu@devops-ai-powered:~/Docker/day33$ cat docker-compose.yml 
services:
  web:
    image: nginx:latest #reusing the image we alreayd have
    ports:
      - "8080:80" #host:container


3. Start it with `docker compose up`
done
4. Access it in your browser
accessible
5. Stop it with `docker compose down`
done

---

### Task 3: Two-Container Setup
Write a `docker-compose.yml` that runs:
- A **WordPress** container
- A **MySQL** container

 cat docker-compose.yml 
service: #equivalent of docker run. defines each container you want runing.
  db:
    image: mysql:8.0
    restart: always #if ever stopped or crashes, Docker to auto restart it
    environment: #these 4 variable tells MySQL image to auto create a DB called wordpress and user wpuser
      MYSQL_ROOT_PASSWORD: rootpass123
      MYSQL_DATABASE: wordpress 
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass123
    volumes:
      - db_data:/var/lib/mysql #named volume

  wordpress:
    image: wordpress:latest
    restart: always
    depends_on: #tells compose to start db container before starting wordpress
      - db
    ports:
      - "8080:80" #HOST:CONTAINER mapping 
    environment:
      WORDPRESS_DB_HOST: db:3306 #wordpress cont. connects to it database using 'db' as hostname. it works because Compose auto. create s ahsred n/w for all service in the file and give each one DNS resolution by it service name.
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass123
      WORDPRESS_DB_NAME: wordpress


volumes: #named volume declaration
  db_data:


They should:
- Be on the same network (Compose does this automatically) - DONE
- MySQL should have a named volume for data persistence - DONE
- WordPress should connect to MySQL using the service name - DONE

Start it, access WordPress in your browser, and set it up.

#NOTE: faced below real world errors:
hit and diagnosed three real, separate failures in sequence: a stale volume from an earlier bad config, a MySQL 8 authentication plugin mismatch with WordPress's driver, and an EC2 memory limit causing MySQL to get OOM-killed mid-startup.



**Verify:** Stop and restart with `docker compose down` and `docker compose up` — is your WordPress data still there?

Yes.

To be clear on what happened: docker compose down removed the containers, but your named volume db_data kept every bit of your WordPress data intact underneath. When the containers came back up with docker compose up -d, WordPress found the same database still sitting there, same site, same admin account, so it skipped the install wizard entirely and went straight to asking you to log in, which you did successfully. That log-in prompt is just normal session behaviour, not data loss.

This confirms Task 3's persistence test passed. Containers are disposable, but the data outlived them, precisely the lesson from Day 32 carried through into Compose.




---

### Task 4: Compose Commands
Practice and document these:
# 1. Start in detached mode
docker compose up -d

# 2. View running services
docker compose ps

# 3. Logs of all services, following live
docker compose logs -f

# 4. Logs of one specific service
docker compose logs -f wordpress

# 5. Stop without removing containers/network
docker compose stop

# (services still exist, just stopped; start them again without recreating)
docker compose start

# 6. Remove containers and network entirely
docker compose down

# 7. Rebuild after a change (relevant once you have a custom Dockerfile in the mix)
docker compose up -d --build
---

### Task 5: Environment Variables
1. Add environment variables directly in your `docker-compose.yml`
created .env file and added the creds and in the yml file configured accorindgly.

2. Create a `.env` file and reference variables from it in your compose file
created .env file.

3. Verify the variables are being picked up
checked it using docker compose config
Yes.
---

## Hints
- Start: `docker compose up -d`
- Stop: `docker compose down`
- Logs: `docker compose logs -f`
- Compose creates a default network for all services automatically
- Service names in compose are the DNS names containers use to talk to each other

---