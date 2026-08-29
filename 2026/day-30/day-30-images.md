# Day 30 – Docker Images & Container Lifecycle

## Task
Today's goal is to **understand how images and containers actually work**.

You will:
- Learn the relationship between images and containers
- Understand image layers and caching
- Master the full container lifecycle

---

---

## Challenge Tasks

### Task 1: Docker Images
1. Pull the `nginx`, `ubuntu`, and `alpine` images from Docker Hub
docker pull <imagename>

2. List all images on your machine — note the sizes
ubuntu@devops-ai-powered:~$ docker images
                                                                                i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
alpine:latest        28bd5fe8b56d         13MB         3.93MB        
hello-world:latest   5dd0d3e6e255       25.9kB         9.49kB    U   
nginx:latest         b34848eff6db        241MB         66.2MB    U   
ubuntu:latest        2260313b31c8        160MB         45.3MB        


3. Compare `ubuntu` vs `alpine` — why is one much smaller?
Alpine Linux uses musl libc instead of the standard glibc that Ubuntu uses, a much smaller, stripped-down C library.

4. Inspect an image — what information can you see?
I can see JSON format fo the image. Architecture, oS, Size, metadata etc

5. Remove an image you no longer need
ubuntu@devops-ai-powered:~$ docker rmi alpine:latest
Untagged: alpine:latest
Deleted: sha256:28bd5fe8b56d1bd048e5babf5b10710ebe0bae67db86916198a6eec434943f8b
---

### Task 2: Image Layers
1. Run `docker image history nginx` — what do you see?
ubuntu@devops-ai-powered:~$ docker image history nginx:latest
IMAGE          CREATED      CREATED BY                                      SIZE      COMMENT
b34848eff6db   4 days ago   CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      4 days ago   STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      4 days ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      4 days ago   ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      4 days ago   COPY 30-tune-worker-processes.sh /docker-ent…   16.4kB    buildkit.dockerfile.v0
<missing>      4 days ago   COPY 20-envsubst-on-templates.sh /docker-ent…   12.3kB    buildkit.dockerfile.v0
<missing>      4 days ago   COPY 15-local-resolvers.envsh /docker-entryp…   12.3kB    buildkit.dockerfile.v0
<missing>      4 days ago   COPY 10-listen-on-ipv6-by-default.sh /docker…   12.3kB    buildkit.dockerfile.v0
<missing>      4 days ago   COPY docker-entrypoint.sh / # buildkit          8.19kB    buildkit.dockerfile.v0
<missing>      4 days ago   RUN /bin/sh -c set -x     && groupadd --syst…   87.6MB    buildkit.dockerfile.v0
<missing>      4 days ago   ENV DYNPKG_RELEASE=1~trixie                     0B        buildkit.dockerfile.v0
<missing>      4 days ago   ENV PKG_RELEASE=1~trixie                        0B        buildkit.dockerfile.v0
<missing>      4 days ago   ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      4 days ago   ENV NJS_RELEASE=1~trixie                        0B        buildkit.dockerfile.v0
<missing>      4 days ago   ENV NJS_VERSION=1.0.0                           0B        buildkit.dockerfile.v0
<missing>      4 days ago   ENV NGINX_VERSION=1.31.4                        0B        buildkit.dockerfile.v0
<missing>      4 days ago   LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      5 days ago   # debian.sh --arch 'amd64' out/ 'trixie' '@1…   87.5MB    debuerreotype 0.17


Understanding docker image history nginx:latest

Running docker image history on the Nginx image reveals its full layered structure, each row is one instruction from the Dockerfile that built it, read bottom to top in the order it was built.

Base layer (87.5MB): The image starts from a minimal Debian ("trixie") filesystem, this is the foundation everything else sits on top of.

Metadata layers (0B each): A series of ENV and LABEL instructions set version numbers (NGINX_VERSION, NJS_VERSION, etc) and maintainer info. These add no actual size since they only store metadata, not files.

The main install layer (87.6MB): A single large RUN command does the heavy lifting, installing Nginx itself, its dependencies, and setting up system users and groups. This one layer accounts for nearly half the image's total size.

Small script copies (a few KB each): Several COPY instructions add small helper scripts (docker-entrypoint.sh and related setup scripts) that run automatically when the container starts, handling things like IPv6 setup and config templating.

Runtime configuration (0B each): The final instructions, ENTRYPOINT, EXPOSE 80, STOPSIGNAL, and CMD, don't add files, they just configure container behaviour: what script runs on startup, which port the container listens on, and the default command (nginx -g "daemon off;") that keeps the container alive in the foreground.

Why some layers show <missing>: this isn't an error, intermediate layers don't get their own standalone image ID, only the final assembled image does.

Key takeaway: the base Debian filesystem (87.5MB) and the Nginx install step (87.6MB) together make up the bulk of the image's 241MB total size. This is exactly why lighter base images like Alpine exist, swapping the base OS and trimming the install step is what makes nginx:alpine significantly smaller than nginx:latest.



2. Each line is a **layer**. Note how some layers show sizes and some show 0B
The reason why is shows 0B is its justan ENV. It doesn;t adds up anything to filesystem. yes, the other command does the heavylifting. installs dependencies, libs etc

3. Write in your notes: What are layers and why does Docker use them?
refer above

---

### Task 3: Container Lifecycle
Practice the full lifecycle on one container:
1. **Create** a container (without starting it)
ubuntu@devops-ai-powered:~$ docker create --name lifecycle-demo nginx
dfe8d8ce43b0de63a8e883d8cec9d780df365aa886f58a78c905d9db6208858d

2. **Start** the container
ubuntu@devops-ai-powered:~$ docker start lifecycle-demo
lifecycle-demo
ubuntu@devops-ai-powered:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS             PORTS                                     NAMES
dfe8d8ce43b0   nginx     "/docker-entrypoint.…"   About a minute ago   Up 8 seconds       80/tcp                                    lifecycle-demo
55329516a630   nginx     "/docker-entrypoint.…"   About an hour ago    Up About an hour   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   webserver2
df07f7409ba8   nginx     "/docker-entrypoint.…"   About an hour ago    Up About an hour   80/tcp                                    webserver
8044a0904779   nginx     "/docker-entrypoint.…"   About an hour ago    Up About an hour   80/tcp                                    eager_haslett
baad00414820   nginx     "/docker-entrypoint.…"   2 hours ago          Up 2 hours         80/tcp                                    exciting_solomon



3. **Pause** it and check status
ubuntu@devops-ai-powered:~$ docker pause lifecycle-demo
lifecycle-demo
ubuntu@devops-ai-powered:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                           PORTS                                     NAMES
dfe8d8ce43b0   nginx          "/docker-entrypoint.…"   5 minutes ago       Up 3 minutes (Paused)            80/tcp                                    lifecycle-demo
81041f3c887c   hello-world    "/hello"                 46 minutes ago      Exited (0) 46 minutes ago                                                  stupefied_albattani
49a4e8f656d0   hello-world    "/hello"                 47 minutes ago      Exited (0) 47 minutes ago                                                  objective_nobel




4. **Unpause** it
ubuntu@devops-ai-powered:~$ docker unpause lifecycle-demo
lifecycle-demo
ubuntu@devops-ai-powered:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                           PORTS                                     NAMES
dfe8d8ce43b0   nginx          "/docker-entrypoint.…"   6 minutes ago       Up 4 minutes                     80/tcp                                    lifecycle-demo
81041f3c887c   hello-world    "/hello"                 47 minutes ago      Exited (0) 47 minutes ago                                                  stupefied_albattani
49a4e8f656d0   hello-world    "/hello"                 48 minutes ago      Exited (0) 48 minutes ago                                                  objective_nobel




5. **Stop** it
ubuntu@devops-ai-powered:~$ docker stop lifecycle-demo
lifecycle-demo
ubuntu@devops-ai-powered:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                           PORTS                                     NAMES
dfe8d8ce43b0   nginx          "/docker-entrypoint.…"   9 minutes ago       Exited (0) About a minute ago                                              lifecycle-demo
81041f3c887c   hello-world    "/hello"                 50 minutes ago      Exited (0) 50 minutes ago                                                  stupefied_albattani
49a4e8f656d0   hello-world    "/hello"                 50 minutes ago      Exited (0) 50 minutes ago                                                  objective_nobel



6. **Restart** it
ubuntu@devops-ai-powered:~$ docker restart lifecycle-demo
lifecycle-demo
ubuntu@devops-ai-powered:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                           PORTS                                     NAMES
dfe8d8ce43b0   nginx          "/docker-entrypoint.…"   20 minutes ago      Up 4 seconds                     80/tcp                                    lifecycle-demo
81041f3c887c   hello-world    "/hello"                 About an hour ago   Exited (0) About an hour ago                                               stupefied_albattani
49a4e8f656d0   hello-world    "/hello"                 About an hour ago   Exited (0) About an hour ago                                               objective_nobel
55329516a630   nginx          "/docker-entrypoint.…"   About an hour ago   Up About an hour                 0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   webserver2


7. **Kill** it
ubuntu@devops-ai-powered:~$ docker kill lifecycle-demo
lifecycle-demo
ubuntu@devops-ai-powered:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                           PORTS                                     NAMES
dfe8d8ce43b0   nginx          "/docker-entrypoint.…"   21 minutes ago      Exited (137) 3 seconds ago                                                 lifecycle-demo
81041f3c887c   hello-world    "/hello"                 About an hour ago   Exited (0) About an hour ago                                               stupefied_albattani
49a4e8f656d0   hello-world    "/hello"                 About an hour ago   Exited (0) About an hour ago                                               objective_nobel



8. **Remove** it
ubuntu@devops-ai-powered:~$ docker rm lifecycle-demo
lifecycle-demo
ubuntu@devops-ai-powered:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                         PORTS                                     NAMES
81041f3c887c   hello-world    "/hello"                 About an hour ago   Exited (0) About an hour ago                                             stupefied_albattani
49a4e8f656d0   hello-world    "/hello"                 About an hour ago   Exited (0) About an hour ago                                             objective_nobel
55329516a630   nginx          "/docker-entrypoint.…"   About an hour ago   Up About an hour               0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   webserver2
df07f7409ba8   nginx          "/docker-entrypoint.…"   About an hour ago   Up About an hour               80/tcp                                    webserver


Check `docker ps -a` after each step — observe the state changes.

---

### Task 4: Working with Running Containers
1. Run an Nginx container in detached mode

evops-ai-powered:~$ docker run -d -p 8081:80 --name lifecycle-nginx nginx
2b816df66bc0967b930c37b3de8c0db738d15a4ec15ba111608f6c82bab667a1
ubuntu@devops-ai-powered:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                     NAMES
2b816df66bc0   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   lifecycle-nginx
55329516a630   nginx     "/docker-entrypoint.…"   2 hours ago     Up 2 hours     0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   webserver2
df07f7409ba8   nginx     "/docker-entrypoint.…"   2 hours ago     Up 2 hours     80/tcp                                    webserver
8044a0904779   nginx     "/docker-entrypoint.…"   2 hours ago     Up 2 hours     80/tcp                                    eager_haslett
baad00414820   nginx     "/docker-entrypoint.…"   2 hours ago     Up 2 hours     80/tcp                                    exciting_solomon




2. View its **logs**
$ docker logs lifecycle-nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/08/29 07:28:24 [notice] 1#1: using the "epoll" event method
2026/08/29 07:28:24 [notice] 1#1: nginx/1.31.4
2026/08/29 07:28:24 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/08/29 07:28:24 [notice] 1#1: OS: Linux 7.0.0-1010-aws
2026/08/29 07:28:24 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:524288
2026/08/29 07:28:24 [notice] 1#1: start worker processes
2026/08/29 07:28:24 [notice] 1#1: start worker process 30
2026/08/29 07:28:24 [notice] 1#1: start worker process 31




3. View **real-time logs** (follow mode)
ubuntu@devops-ai-powered:~$ docker logs -f lifecycle-nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/08/29 07:28:24 [notice] 1#1: using the "epoll" event method
2026/08/29 07:28:24 [notice] 1#1: nginx/1.31.4
2026/08/29 07:28:24 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/08/29 07:28:24 [notice] 1#1: OS: Linux 7.0.0-1010-aws
2026/08/29 07:28:24 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:524288
2026/08/29 07:28:24 [notice] 1#1: start worker processes
2026/08/29 07:28:24 [notice] 1#1: start worker process 30
2026/08/29 07:28:24 [notice] 1#1: start worker process 31



4. **Exec** into the container and look around the filesystem
ubuntu@devops-ai-powered:~$ docker exec -it lifecycle-nginx bash
root@2b816df66bc0:/# whoami
root
root@2b816df66bc0:/# cat /etc/os-release 
PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
NAME="Debian GNU/Linux"
VERSION_ID="13"
VERSION="13 (trixie)"
VERSION_CODENAME=trixie
DEBIAN_VERSION_FULL=13.6
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
root@2b816df66bc0:/# 



5. Run a single command inside the container without entering it

ubuntu@devops-ai-powered:~$ docker exec lifecycle-nginx ls /usr/share/nginx/html
50x.html
index.html


6. **Inspect** the container — find its IP address, port mappings, and mounts
ubuntu@devops-ai-powered:~$ docker inspect lifecycle-nginx 


  "NetworkSettings": {
            "SandboxID": "39f84475aa88cc7c6c63648a67d6ffca2c5860f2b0cd32d2d1ce97a4d7618bc6",
            "SandboxKey": "/var/run/docker/netns/39f84475aa88",
            "Ports": {
                "80/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "8081"
                    },
                    {
                        "HostIp": "::",
                        "HostPort": "8081"
                    }
                ]
            },


 "Mounts": [],
        "Config": {
            "Hostname": "2b816df66bc0",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.31.4",
                "NJS_VERSION=1.0.0",
                "NJS_RELEASE=1~trixie",
                "ACME_VERSION=0.4.1",
                "PKG_RELEASE=1~trixie",
                "DYNPKG_RELEASE=1~trixie"
            ],





---

### Task 5: Cleanup
1. Stop all running containers in one command
ubuntu@devops-ai-powered:~$ docker stop $(docker ps -q)
2b816df66bc0
55329516a630
df07f7409ba8
8044a0904779
baad00414820


2. Remove all stopped containers in one command

ubuntu@devops-ai-powered:~$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
2b816df66bc0967b930c37b3de8c0db738d15a4ec15ba111608f6c82bab667a1
81041f3c887c64c189f43299a293ec279e9ae868e0c6dd71d58fbc5243dc6180
49a4e8f656d04d551ea30040bdef60aa0c104c69ce4fc12c663f8e83330fbda7
55329516a6307a7a04731581bbcb7be462e6e918c9152bfe96edf6dd12927696
df07f7409ba8d8c94b682786a8b6bcc114f929d8013cc24c936682220077bdfd
8044a0904779706fb161a841dc118b457ef2b45d3d7fd7fae0877cd9c4457cf4
d30ada4ede7dbba7e1c78d91b762afda61a977103abd0e40fdae1c0816dc42e0
7c7407ee528e2100d9985249d43ca81cd74879ce1e0a257c20a6af1c17482c1a
d2817108b2d4b3bbaf4b4309173e44259aeb1df73cc96169a24148b7e8b564be
d41d0850b87e59e28ccbfc782427aa2c85dd408b4367d0978de52d4528a8e635
e2c32bbb83445538f51c64a8d67d647cac10c0b53b6765cc42135bd47270e6db
baad0041482075955ba61aa0c63620288e19b0612a5507de42e63c3b78e6ee57
eee0722f68cf88fcf2a744c789a0770fc99bc3bb39f02b60aa32435ec58dbcc6
73e2d4d33f93af960f51e9c69763c189f8d7347347ad7f1f91345d7e74a41b78

3. Remove unused images

ubuntu@devops-ai-powered:~$ docker image prune -a
WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N] y
Deleted Images:
untagged: hello-world:latest
deleted: sha256:5dd0d3e6e255913fc30f90b9f2b1d359cc2cbdb48090cc4b65f1676e203243cc
deleted: sha256:d1a8d0a4eeb63aff09f5f34d4d80505e0ba81905f36158cc3970d8e07179e59e
deleted: sha256:8e752a1cddeafc02597e756f4a0ec96e29f63ac4bc4af87682daf3f1de843bb7
untagged: nginx:latest
deleted: sha256:b34848eff6db786b6b1282d3a9c3fd0b5563dfb6d261df4923378b419e0d24f0
deleted: sha256:90c24d7f26b915f11b1f8e621ead4706b7e933de986498120310a876bf30dc1f
deleted: sha256:ecda39113c8ab51728c68b85221c0288b5d9b609014ad01dd4292fcfbf74bdda
untagged: ubuntu:latest
deleted: sha256:2260313b31c8c011cd2eebe728008efac1b3982be73eb71348ea2648d2c0e09b
deleted: sha256:889d056d5c6c0bfb55789ff3710681d68e50713cb562d2196dc07110599c7a6f
deleted: sha256:c04e4a9725dcf7c1f12c04e1a6458ff38b3f4e7d4acc5f741bfa73c66cd9a9a2
untagged: sha256:678c6550cc43645e08669028bc177f50be4e7c5b8cca677067b1914d4afc7a03
deleted: sha256:678c6550cc43645e08669028bc177f50be4e7c5b8cca677067b1914d4afc7a03
deleted: sha256:7b202b0e2e0028c6250f5fcf41d04df492d145a1654c6995a6553f0c1f6f1960
deleted: sha256:522d96ea380735b5cf03988f84c2ac91c28d72f628c905881e78f7e9d67079ad

4. Check how much disk space Docker is using
ubuntu@devops-ai-powered:~$ docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          0         0         0B        0B
Containers      0         0         0B        0B
Local Volumes   0         0         0B        0B
Build Cache     0         0         0B        0B
---

## Hints
- Image history: `docker image history`
- Create without starting: `docker create`
- Follow logs: `docker logs -f`
- Inspect: `docker inspect`
- Cleanup: `docker system df`, `docker system prune`

---

## Submission
1. Add your `day-30-images.md` to `2026/day-30/`
2. Commit and push to your fork

---