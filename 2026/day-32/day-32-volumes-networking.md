# Day 32 – Docker Volumes & Networking

## Task
Today's goal is to **solve two real problems: data persistence and container communication**.

Containers are ephemeral — they lose data when removed. And by default, containers can't easily talk to each other. Today you fix both.

---

## Expected Output
- A markdown file: `day-32-volumes-networking.md`
- Screenshots of your experiments

---

## Challenge Tasks

### Task 1: The Problem
1. Run a Postgres or MySQL container
docker run -d --name pg-test -e POSTGRES_PASSWORD=test123 postgres




2. Create some data inside it (a table, a few rows — anything)

docker exec -it pg-test psql -U postgres

postgres=# CREATE TABLE notes (id SERIAL PRIMARY KEY, content TEXT);
INSERT INTO notes (content) VALUES ('this should survive... or should it');
CREATE TABLE
INSERT 0 1
postgres=# SELECT * FROM notes
postgres-# SELECT * FROM notes;
ERROR:  syntax error at or near "SELECT"
LINE 2: SELECT * FROM notes;
        ^
postgres=# SELECT * FROM notes;
 id |               content               
----+-------------------------------------
  1 | this should survive... or should it
(1 row)

postgres=# \q



3. Stop and remove the container


ubuntu@devops-ai-powered:~/Docker/day32$ docker stop pg-test
pg-test

ubuntu@devops-ai-powered:~/Docker/day32$ docker rm pg-test
pg-test

ubuntu@devops-ai-powered:~/Docker/day32$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES


4. Run a new one — is your data still there?

ubuntu@devops-ai-powered:~/Docker/day32$ docker run -d --name pg-test2 -e POSTGRES_PASSWORD=test123 postgres
94c3805154f50d1c3fe64a9dc8eb4ed5afb93961c3b14df11c1b6f375b31b3d0

ubuntu@devops-ai-powered:~/Docker/day32$ docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED         STATUS         PORTS      NAMES
94c3805154f5   postgres   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   5432/tcp   pg-test2


ubuntu@devops-ai-powered:~/Docker/day32$ docker exec -it pg-test2 psql -U postgres
psql (18.6 (Debian 18.6-1.pgdg13+2))
Type "help" for help.

postgres=# SELECT * FROM notes
postgres-# \q


###OR run this: 


ubuntu@devops-ai-powered:~/Docker/day32$ docker exec -it pg-test2 psql -U postgres -c "SELECT * FROM notes;"
ERROR:  relation "notes" does not exist
LINE 1: SELECT * FROM notes;



Write what happened and why.
The table was gone. because the container did not store it. The dta ais wiped out.
when we ran docker run without a -v flag, Postgres wrote all its database files inside the container's own writable layer, the layer that only exists as long as that specific container exists. docker rm pg-test deleted that container and its writable layer together, table and all. pg-test2 started from the clean postgres image again, no shared storage between the two containers at all.



---

### Task 2: Named Volumes
1. Create a named volume
ubuntu@devops-ai-powered:~/Docker/day32$ docker volume create pgdata
pgdata



2. Run the same database container, but this time **attach the volume** to it

NOTE: Make sure to check the verions of postgres. because in 18+ postgres version, its only /var/lib/postgres and NO /data

ubuntu@devops-ai-powered:~/Docker/day32$ docker run -d --name pg-vol -e POSTGRES_PASSWORD=test123 -v pgdata:/var/lib/postgresql postgres
9ea70115a5d0f6d0c63674b62f46bb3b76ff9663dfdbe69508f24f4828aa4efb

3. Add some data, stop and remove the container

ubuntu@devops-ai-powered:~/Docker/day32$ docker exec -it pg-vol psql -U postgres
psql (18.6 (Debian 18.6-1.pgdg13+2))
Type "help" for help.

postgres=# CREATE TABLE notes (id SERIAL PRIMARY KEY, content TEXT);
INSERT INTO notes (content) VALUES ('this time it should survive');
\q
CREATE TABLE
INSERT 0 1





4. Run a brand new container with the **same volume**

ubuntu@devops-ai-powered:~/Docker/day32$ docker run -d --name pg-vol2 -e POSTGRES_PASSWORD=test123 -v pgdata:/var/lib/postgresql postgres
8be87736e3fc08b81f29b4d16bb47b7a6af443bcc080d32ab7519fe5cf6be9ce

ubuntu@devops-ai-powered:~/Docker/day32$ docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED          STATUS          PORTS      NAMES
8be87736e3fc   postgres   "docker-entrypoint.s…"   4 seconds ago    Up 3 seconds    5432/tcp   pg-vol2
94c3805154f5   postgres   "docker-entrypoint.s…"   55 minutes ago   Up 55 minutes   5432/tcp   pg-test2

ubuntu@devops-ai-powered:~/Docker/day32$ docker exec -it pg-vol2 psql -U postgres
psql (18.6 (Debian 18.6-1.pgdg13+2))
Type "help" for help.

postgres=# SELECT * FROM notes
postgres-# \dt
          List of tables
 Schema | Name  | Type  |  Owner   
--------+-------+-------+----------
 public | notes | table | postgres
(1 row)

postgres-# \q




5. Is the data still there?

Yes the data is still there.


**Verify:** `docker volume ls`, `docker volume inspect`

ubuntu@devops-ai-powered:~/Docker/day32$ docker volume ls
DRIVER    VOLUME NAME
local     4a9d0e7a07e4fd60aaf169b137085b0672b12a6e58d26a30b7a9b19a210a7f29
local     8be14c311e11ba97dfd5abc67aaf8aba39d01f7bd1bcc59ee183cba2b7ef8402
local     a10bf22f8b1f7737ba39b8b9b344bc27e1548919e2c5e62996f880d95aaddd1a
local     pgdata

ubuntu@devops-ai-powered:~/Docker/day32$ docker volume inspect pgdata
[
    {
        "CreatedAt": "2026-08-30T08:34:24Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/pgdata/_data",
        "Name": "pgdata",
        "Options": null,
        "Scope": "local"
    }
]


---

### Task 3: Bind Mounts
1. Create a folder on your host machine with an `index.html` file

ubuntu@devops-ai-powered:~$ cd mybindsiteforcontainer/

ubuntu@devops-ai-powered:~/mybindsiteforcontainer$ echo "<h1>Version 1 fomr host</h1>" > index.html
ubuntu@devops-ai-powered:~/mybindsiteforcontainer$ ls 
index.html
ubuntu@devops-ai-powered:~/mybindsiteforcontainer$ cat index.html 
<h1>Version 1 fomr host</h1>


2. Run an Nginx container and **bind mount** your folder to the Nginx web directory

ubuntu@devops-ai-powered:~/Docker/day32$ docker run -d -p 8084:80 --name bind-demo -v ~/mybindsiteforcontainer:/usr/share/nginx/html nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
0a35a4e59186: Pull complete 
30576ad53d33: Pull complete 
657dd7fba849: Pull complete 
b8f66660faa6: Pull complete 
c90544874aaf: Pull complete 
8f655e1bd5c1: Pull complete 
e0649adc94d9: Download complete 
1cf64d45fa0f: Download complete 
Digest: sha256:b34848eff6db786b6b1282d3a9c3fd0b5563dfb6d261df4923378b419e0d24f0
Status: Downloaded newer image for nginx:latest
c5b1f70a39cd5d4fe7410687785bae8da49dcbe2fca5b58ea9e448623382a4bc

ubuntu@devops-ai-powered:~/Docker/day32$ docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED             STATUS             PORTS                                     NAMES
c5b1f70a39cd   nginx      "/docker-entrypoint.…"   7 seconds ago       Up 7 seconds       0.0.0.0:8084->80/tcp, [::]:8084->80/tcp   bind-demo

docker inspect c5b1f70a39cd

 "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/ubuntu/mybindsiteforcontainer",
                "Destination": "/usr/share/nginx/html",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],



3. Access the page in your browser

Yes its accessible:


4. Edit the `index.html` on your host — refresh the browser

Yes it got edited llive.

Write in your notes: What is the difference between a named volume and a bind mount?

#Named volume
Managed by:Docker itself
Location:Docker's internal storage area
Best for:Persisting app data. (db upload)
Portability: works the same across host machine


#Bind mount
Managed by: You, its path you choose on the host
Location: anywhere on the host filesystem
Best for: live development, editing files and seeing changes instantly
Portability: ties to a specific host path.



---

### Task 4: Docker Networking Basics
1. List all Docker networks on your machine

ubuntu@devops-ai-powered:~/Docker$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
d7f2a03fb6c8   bridge    bridge    local
5fd4753740be   host      host      local
393ebdf58d70   none      null      local




2. Inspect the default `bridge` network


3. Run two containers on the default bridge — can they ping each other by **name**?

ubuntu@devops-ai-powered:~/Docker$ docker run -d --name net-a alpine sleep 1000
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
f5124fb579e2: Download complete 
56dceff11b33: Download complete 
Digest: sha256:28bd5fe8b56d1bd048e5babf5b10710ebe0bae67db86916198a6eec434943f8b
Status: Downloaded newer image for alpine:latest
da6e5f41752f607a4cafc107cb683bc239479f3d3f852035f837ba47484d9e76
ubuntu@devops-ai-powered:~/Docker$ docker run -d --name net-b alpine sleep 1000
f3aa7c686c368c12d7190d8a958122533fdbcde7f873ca454cafd5e5498215ec


CANNOT PING USING NAME: 

ubuntu@devops-ai-powered:~/Docker$ docker exec -it net-a sh
/ # ping -c 3 net-b
ping: bad address 'net-b'




4. Run two containers on the default bridge — can they ping each other by **IP**?

YES. PINGING USING IPs.

Pinging form net-a to net-b with IP address


ubuntu@devops-ai-powered:~/Docker$ docker exec -it net-a sh
/ # ping -c 3 net-b
ping: bad address 'net-b'
/ # ping -c 3 172.17.0.6
PING 172.17.0.6 (172.17.0.6): 56 data bytes
64 bytes from 172.17.0.6: seq=0 ttl=64 time=0.107 ms
64 bytes from 172.17.0.6: seq=1 ttl=64 time=0.062 ms
64 bytes from 172.17.0.6: seq=2 ttl=64 time=0.058 ms

--- 172.17.0.6 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.058/0.075/0.107 ms

Pinging form net-b to net-a with IP address

ubuntu@devops-ai-powered:~/Docker$ docker exec -it net-b sh
/ # ping -c 3 172.17.0.5
PING 172.17.0.5 (172.17.0.5): 56 data bytes
64 bytes from 172.17.0.5: seq=0 ttl=64 time=0.053 ms
64 bytes from 172.17.0.5: seq=1 ttl=64 time=0.063 ms
64 bytes from 172.17.0.5: seq=2 ttl=64 time=0.057 ms

--- 172.17.0.5 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.053/0.057/0.063 ms









---

### Task 5: Custom Networks
1. Create a custom bridge network called `my-app-net`

ubuntu@devops-ai-powered:~/Docker$ docker network create my-app-net
c3129c6dfb73b0fe8748142c0f0eb640cf041c5ed476664ccd7fc28ae7bd9a6f

ubuntu@devops-ai-powered:~/Docker$ docker network ls
NETWORK ID     NAME         DRIVER    SCOPE
d7f2a03fb6c8   bridge       bridge    local
5fd4753740be   host         host      local
c3129c6dfb73   my-app-net   bridge    local
393ebdf58d70   none         null      local


2. Run two containers on `my-app-net`

ubuntu@devops-ai-powered:~/Docker$ docker run -d --name net-c --network my-app-net alpine sleep 100
5053f6d25cf362704f9a12af622a8200a0f305a295b1ba4e101c557065d83e72

ubuntu@devops-ai-powered:~/Docker$ docker run -d --name net-d --network my-app-net alpine sleep 100
b3322c46a4786a0318ba59efd74f12d7a675eac73358efa76cc39c34d3d9b913

3. Can they ping each other by **name** now?

Yes.


ubuntu@devops-ai-powered:~/Docker$ docker exec -it net-c sh
/ # ping -c 3 net-c
PING net-c (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.041 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.100 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.064 ms

--- net-c ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.041/0.068/0.100 ms
/ # exit
ubuntu@devops-ai-powered:~/Docker$ docker exec -it net-d sh
/ # ping -c 3 netubuntu@devops-ai-powered:~/Docker$ 
ubuntu@devops-ai-powered:~/Docker$ docker exec -it net-d sh
Error response from daemon: container b3322c46a4786a0318ba59efd74f12d7a675eac73358efa76cc39c34d3d9b913 is not running
ubuntu@devops-ai-powered:~/Docker$ docker restart net-d
net-d
ubuntu@devops-ai-powered:~/Docker$ docker restart net-c
net-c
ubuntu@devops-ai-powered:~/Docker$ docker exec -it net-d sh
/ # ping -c 3 net-c
PING net-c (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.083 ms
64 bytes from 172.18.0.3: seq=1 ttl=64 time=0.067 ms
64 bytes from 172.18.0.3: seq=2 ttl=64 time=0.201 ms

--- net-c ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.067/0.117/0.201 ms
/ # exit



4. Write in your notes: Why does custom networking allow name-based communication but the default bridge doesn't?

Docker's default bridge network doesn't run an embedded DNS resolver between containers on it, it only gives them IPs. A user defined network (anything you create with docker network create) gets Docker's built in DNS, which automatically resolves container names to their internal IPs. That's the whole reason custom networks are the recommended approach for anything with more than one container talking to another.


---

### Task 6: Put It Together
1. Create a custom network

ubuntu@devops-ai-powered:~/Docker/day32/task6-alltogether$ docker network create app-net
44670057c6ea995e8633d6a1d8592502b6f7dee01e8575a58c9fd1c020db9a1f

ubuntu@devops-ai-powered:~/Docker/day32/task6-alltogether$ docker network ls
NETWORK ID     NAME         DRIVER    SCOPE
44670057c6ea   app-net      bridge    local
d7f2a03fb6c8   bridge       bridge    local
5fd4753740be   host         host      local
c3129c6dfb73   my-app-net   bridge    local
393ebdf58d70   none         null      local


2. Run a **database container** (MySQL/Postgres) on that network with a volume for data

CREATING THE NAMED VOLUME FIRST:

ubuntu@devops-ai-powered:~/Docker/day32/task6-alltogether$ docker volume create app-db-data
app-db-data

ubuntu@devops-ai-powered:~/Docker/day32/task6-alltogether$ docker volume ls
DRIVER    VOLUME NAME
local     4a9d0e7a07e4fd60aaf169b137085b0672b12a6e58d26a30b7a9b19a210a7f29
local     8be14c311e11ba97dfd5abc67aaf8aba39d01f7bd1bcc59ee183cba2b7ef8402
local     a10bf22f8b1f7737ba39b8b9b344bc27e1548919e2c5e62996f880d95aaddd1a
local     app-db-data

docker run -d --name app-db --network app-net -v app-db-data:/var/lib/postgresql -e POSTGRES_PASSWORD=test123 postgres
cbe357a850d0c128928bbea857b0d2671d047cf45d41f821b7777a3569bbe0f7


3. Run an **app container** (use any image) on the same network

docker run -d --name app-server --network app-net alpine sleep 3000
750ce7e7efc827860bbd399a6fc6d1bdf8f5a31b4e26f9403ea54ad2e2ea68e5

ubuntu@devops-ai-powered:~/Docker/day32/task6-alltogether$ docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED             STATUS             PORTS                                     NAMES
750ce7e7efc8   alpine     "sleep 3000"             4 seconds ago       Up 4 seconds                                                 app-server
cbe357a850d0   postgres   "docker-entrypoint.s…"   4 minutes ago       Up 4 minutes       5432/tcp                                  app-db


4. Verify the app container can reach the database by container name

yes app-server is able to ping app-db

ubuntu@devops-ai-powered:~/Docker/day32/task6-alltogether$ docker exec -it app-server sh
/ # ping -c 3 app-db
PING app-db (172.19.0.2): 56 data bytes
64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.097 ms
64 bytes from 172.19.0.2: seq=1 ttl=64 time=0.072 ms
64 bytes from 172.19.0.2: seq=2 ttl=64 time=0.086 ms

--- app-db ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.072/0.085/0.097 ms



---

## Hints
- Volumes: `docker volume create`, `-v volume_name:/path`
- Bind mount: `-v /host/path:/container/path`
- Networking: `docker network create`, `--network`
- Ping: `docker exec container1 ping container2`
