# Day 31 – Dockerfile: Build Your Own Images

## Task
Today's goal is to **write Dockerfiles and build custom images**.

This is the skill that separates someone who uses Docker from someone who actually ships with Docker.

---

## Expected Output
- A markdown file: `day-31-dockerfile.md`
- All Dockerfiles you create

---

## Challenge Tasks

### Task 1: Your First Dockerfile
1. Create a folder called `my-first-image`
2. Inside it, create a `Dockerfile` that:
   - Uses `ubuntu` as the base image
   - Installs `curl`
   - Sets a default command to print `"Hello from my custom image!"`

ubuntu@devops-ai-powered:~/my-first-image$ vim Dockerfile
ubuntu@devops-ai-powered:~/my-first-image$ ls
Dockerfile
ubuntu@devops-ai-powered:~/my-first-image$ cat Dockerfile 
FROM ubuntu

RUN apt update && apt install -y curl

CMD ["echo" , "Hello from my custom image!"]




3. Build the image and tag it `my-ubuntu:v1`
ubuntu@devops-ai-powered:~/my-first-image$ docker build -t my-ubuntu:v1 .

ubuntu@devops-ai-powered:~/my-first-image$ docker images
                                                                              i Info →   U  In Use
IMAGE           ID             DISK USAGE   CONTENT SIZE   EXTRA
my-ubuntu:v1    2b921604f677        258MB         78.6MB        
ubuntu:latest   2260313b31c8        160MB         45.3MB  

4. Run a container from your image

ubuntu@devops-ai-powered:~/my-first-image$ docker images
                                                                              i Info →   U  In Use
IMAGE           ID             DISK USAGE   CONTENT SIZE   EXTRA
my-ubuntu:v1    2b921604f677        258MB         78.6MB        
ubuntu:latest   2260313b31c8        160MB         45.3MB        

ubuntu@devops-ai-powered:~/my-first-image$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

ubuntu@devops-ai-powered:~/my-first-image$ docker run my-ubuntu:v1
Hello from my custom image!

ubuntu@devops-ai-powered:~/my-first-image$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS                     PORTS     NAMES
a6ebc3524dcb   my-ubuntu:v1   "echo 'Hello from my…"   6 seconds ago   Exited (0) 6 seconds ago             optimistic_colden

**Verify:** The message prints on `docker run`

---

### Task 2: Dockerfile Instructions
Create a new Dockerfile that uses **all** of these instructions:
- `FROM` — base image
- `RUN` — execute commands during build
- `COPY` — copy files from host to image
- `WORKDIR` — set working directory
- `EXPOSE` — document the port
- `CMD` — default command

Build and run it. Understand what each line does.

ubuntu@devops-ai-powered:~/my-first-image/day31-task2$ cat Dockerfile 
#setting base image
FROM nginx:alpine 

#executing command echo at build time
RUN echo "Build step ran"

#copying a file from the host folder into the image
COPY index.html /usr/share/nginx/html/index.html

#sets the working directory
WORKDIR /usr/share/nginx/html

#documenting the port on which the container will listen on. Doesnt publish the port. -p at runtime does that
EXPOSE 80

#default command
CMD ["nginx", "-g", "deamon off;"]


ubuntu@devops-ai-powered:~/my-first-image/day31-task2$ docker build -t my-nginx-app:v1 .

Status: Downloaded newer image for nginx:alpine
 ---> db35bfc6b295
Step 2/6 : RUN echo "Build step ran"
 ---> Running in d291a022fb04
Build step ran
 ---> Removed intermediate container d291a022fb04
 ---> 0d53df3fe7cc
Step 3/6 : COPY index.html /usr/share/nginx/html/index.html
 ---> 2c47ed9854f5
Step 4/6 : WORKDIR /usr/share/nginx/html
 ---> Running in 904dd874b305
 ---> Removed intermediate container 904dd874b305
 ---> 15a7990076e7
Step 5/6 : EXPOSE 80
 ---> Running in 12e9bce0ac35
 ---> Removed intermediate container 12e9bce0ac35
 ---> aed2260e0b00
Step 6/6 : CMD ["nginx", "-g", "deamon off;"]
 ---> Running in d4129d4882ae
 ---> Removed intermediate container d4129d4882ae
 ---> 2e795e461633
Successfully built 2e795e461633
Successfully tagged my-nginx-app:v1
ubuntu@devops-ai-powered:~/my-first-image/day31-task2$ docker images
                                                                              i Info →   U  In Use
IMAGE             ID             DISK USAGE   CONTENT SIZE   EXTRA
my-nginx-app:v1   2e795e461633       93.4MB         26.3MB        
my-ubuntu:v1      2b921604f677        258MB         78.6MB    U   
nginx:alpine      db35bfc6b295       94.2MB         27.2MB        
ubuntu:latest     2260313b31c8        160MB         45.3MB        
ubuntu@devops-ai-powered:~/my-first-image/day31-task2$ 

docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@devops-ai-powered:~/my-first-image/day31-task2$ docker run -d -p 8082:80 --name task2-app my-nginx-app:v1 
50f52b9f475382ab8fe7a8b350dabdb63bf62519b3cc98a53942885e34a42311
ubuntu@devops-ai-powered:~/my-first-image/day31-task2$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@devops-ai-powered:~/my-first-image/day31-task2$ docker ps -a
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS                      PORTS     NAMES
50f52b9f4753   my-nginx-app:v1   "/docker-entrypoint.…"   7 seconds ago    Exited (1) 7 seconds ago              task2-app
a6ebc3524dcb   my-ubuntu:v1      "echo 'Hello from my…"   16 minutes ago   Exited (0) 16 minutes ago             optimistic_colden


---

### Task 3: CMD vs ENTRYPOINT
1. Create an image with `CMD ["echo", "hello"]` — run it, then run it with a custom command. What happens?
ubuntu@devops-ai-powered:~/Docker/my-first-image/dat31-task3-cmd$ docker build -t cmd-demo:v1 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM ubuntu
 ---> 2260313b31c8
Step 2/2 : CMD ["echo", "hello"]
 ---> Running in d61ea3d1e2b3
 ---> Removed intermediate container d61ea3d1e2b3
 ---> 7545ea6ef637
Successfully built 7545ea6ef637
Successfully tagged cmd-demo:v1


ubuntu@devops-ai-powered:~/Docker/my-first-image/dat31-task3-cmd$ docker images
                                                                              i Info →   U  In Use
IMAGE             ID             DISK USAGE   CONTENT SIZE   EXTRA
cmd-demo:v1       7545ea6ef637        157MB         41.6MB        
my-nginx-app:v1   2e795e461633       93.4MB         26.3MB    U   
my-ubuntu:v1      2b921604f677        258MB         78.6MB    U   
nginx:alpine      db35bfc6b295       94.2MB         27.2MB        
ubuntu:latest     2260313b31c8        160MB         45.3MB        

ubuntu@devops-ai-powered:~/Docker/my-first-image/dat31-task3-cmd$ docker run cmd-demo:v1
hello

So what we run above command we can see "hello" as output. So we did not specified any thing after a command hence it took the default command and gave output as 'hello.


Hovever, in below we have written 'overridden command' this means that it overridden the default command (but did not replaced anything in oroginal Dockerfile)

ubuntu@devops-ai-powered:~/Docker/my-first-image/dat31-task3-cmd$ docker run cmd-demo:v1 echo "overridden command"
overridden command

ubuntu@devops-ai-powered:~/Docker/my-first-image/dat31-task3-cmd$ docker run cmd-demo:v1 echo "overridden command"
overridden command
ubuntu@devops-ai-powered:~/Docker/my-first-image/dat31-task3-cmd$ docker run cmd-demo:v1
hello



2. Create an image with `ENTRYPOINT ["echo"]` — run it, then run it with additional arguments. What happens?

ubuntu@devops-ai-powered:~/Docker/my-first-image/day31-task3-entrypoint$ cat Dockerfile 
FROM ubuntu
ENTRYPOINT ["echo"]


ubuntu@devops-ai-powered:~/Docker/my-first-image/day31-task3-entrypoint$ docker build -t entrypoint-demo:v1 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM ubuntu
 ---> 2260313b31c8
Step 2/2 : ENTRYPOINT ["echo"]
 ---> Running in 98ac32ff6472
 ---> Removed intermediate container 98ac32ff6472
 ---> 324579053d22
Successfully built 324579053d22
Successfully tagged entrypoint-demo:v1


ubuntu@devops-ai-powered:~/Docker/my-first-image/day31-task3-entrypoint$ docker run entrypoint-demo:v1 "additional arguments here"
additional arguments here

3. Write in your notes: When would you use CMD vs ENTRYPOINT?

The core difference to note down:
CMD is a default that gets fully replaced if you provide anything on the command line. 

ENTRYPOINT is a fixed command that always runs, and anything you type on the command line just becomes arguments passed into it, appended rather than replacing.

Why this distinction matters in practice
This is exactly why so many real-world images use ENTRYPOINT for the main program and CMD for its default arguments, together. For example:
dockerfile
ENTRYPOINT ["echo"]
CMD ["hello"]
Running docker run image would print hello (CMD supplies the default argument to ENTRYPOINT). Running docker run image "custom text" would print custom text instead, since your command-line argument replaces just the CMD part, while ENTRYPOINT's echo stays fixed and unchangeable. This combination gives you a container with a locked-in purpose but a customisable default behaviour, which is how tools like the nginx image work too, ENTRYPOINT handles the startup scripts, CMD provides the default nginx -g "daemon off;" command that can still be overridden if needed.




---

### Task 4: Build a Simple Web App Image
1. Create a small static HTML file (`index.html`) with any content
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ cat index.html 
<!DOCTYPE html>
<html>
<head><title>My Docker Website: DAY 31-TASK 4</title></head>
<body>
  <h1>Hello from my custom Nginx image!</h1>
  <p>Built on Day 31 of 90DaysOfDevOps</p>
</body>
</html>



2. Write a Dockerfile that:
   - Uses `nginx:alpine` as base
   - Copies your `index.html` to the Nginx web directory

ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ cat Dockerfile 
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html


3. Build and tag it `my-website:v1`
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ docker build -t my-website:v1 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM nginx:alpine
 ---> db35bfc6b295
Step 2/2 : COPY index.html /usr/share/nginx/html/index.html
 ---> fbd3586ec78a
Successfully built fbd3586ec78a
Successfully tagged my-website:v1

4. Run it with port mapping and access it in your browser
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ docker run -d -p 8083:80 --name my-website-container  my-website:v1
5fbe73b70bcb948234ca2cdc272378a3f627b8988a630d2962ae3f3c97a44443

ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                                     NAMES
5fbe73b70bcb   my-website:v1   "/docker-entrypoint.…"   11 seconds ago   Up 10 seconds   0.0.0.0:8083->80/tcp, [::]:8083->80/tcp   my-website-container

---

### Task 5: .dockerignore
1. Create a `.dockerignore` file in one of your project folders
2. Add entries for: `node_modules`, `.git`, `*.md`, `.env`
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ cat .dockerignore 
node_modules
.git
*.md
.env
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ 

3. Build the image — verify that ignored files are not included


ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ docker build -t my-website:v2 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM nginx:alpine
 ---> db35bfc6b295
Step 2/2 : COPY index.html /usr/share/nginx/html/index.html
 ---> 196387f9ffca
Successfully built 196387f9ffca
Successfully tagged my-website:v2
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ docker images
                                                                              i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
cmd-demo:v1          7545ea6ef637        157MB         41.6MB    U   
entrypoint-demo:v1   324579053d22        157MB         41.6MB    U   
my-nginx-app:v1      2e795e461633       93.4MB         26.3MB    U   
my-ubuntu:v1         2b921604f677        258MB         78.6MB    U   
my-website:v1        fbd3586ec78a       93.4MB         26.3MB    U   
my-website:v2        196387f9ffca       93.4MB         26.3MB        
nginx:alpine         db35bfc6b295       94.2MB         27.2MB        
ubuntu:latest        2260313b31c8        160MB         45.3MB        


ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ 


ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ docker images
                                                                              i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
cmd-demo:v1          7545ea6ef637        157MB         41.6MB    U   
entrypoint-demo:v1   324579053d22        157MB         41.6MB    U   
my-nginx-app:v1      2e795e461633       93.4MB         26.3MB    U   
my-ubuntu:v1         2b921604f677        258MB         78.6MB    U   
my-website:v1        fbd3586ec78a       93.4MB         26.3MB    U   
my-website:v2        196387f9ffca       93.4MB         26.3MB        
nginx:alpine         db35bfc6b295       94.2MB         27.2MB        
ubuntu:latest        2260313b31c8        160MB         45.3MB        
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ docker run --rm my-website:v2 ls -la /usr/share/nginx/html
total 16
drwxr-xr-x    1 root     root          4096 Aug 29 10:46 .
drwxr-xr-x    1 root     root          4096 Aug 19 19:08 ..
-rw-r--r--    1 root     root           497 Aug 11 23:21 50x.html
-rw-rw-r--    1 root     root           201 Aug 29 10:27 index.html
ubuntu@devops-ai-powered:~/Docker/my-first-image/my-website$ 


---

### Task 6: Build Optimization
1. Build an image, then change one line and rebuild — notice how Docker uses **cache**
2. Reorder your Dockerfile so that frequently changing lines come **last**
3. Write in your notes: Why does layer order matter for build speed?

The core idea: 

Docker builds an image layer by layer, one layer per instruction in your Dockerfile. When you rebuild an image, Docker checks each layer from the top down and asks "has anything changed here compared to last time?" If a layer hasn't changed, Docker reuses the cached version instead of redoing the work. The moment it hits a layer that has changed, that layer and every layer after it get rebuilt from scratch, even if those later layers technically didn't need to change.
Why order matters: put things that change rarely near the top, and things that change often near the bottom.



Simple rule to remember: 

stuff that rarely changes (installing packages, setting up the OS) goes near the top of your Dockerfile. Stuff you edit all the time (your actual application code) goes near the bottom. That way your slow steps get cached and your fast rebuilds only touch the bit you're actively working on.
Try running the two builds again and paste the terminal output, you should be able to actually see Using cache appear on the good version and not the bad one, that's the proof it's working.




---

## Hints
- Build: `docker build -t name:tag .`
- The `.` at the end is the build context
- `COPY . .` copies everything from host to container
- Nginx serves files from `/usr/share/nginx/html/`

---
