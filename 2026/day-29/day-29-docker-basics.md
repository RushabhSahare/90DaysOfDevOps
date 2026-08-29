# Day 29 – Introduction to Docker

## Task
Today's goal is to **understand what Docker is and run your first container**.

You will:
- Learn why containers exist and how they differ from VMs
- Install Docker on your machine
- Run and explore containers from Docker Hub

---

## Expected Output
- A markdown file: `day-29-docker-basics.md`
- Screenshots of your running containers

---

## Challenge Tasks

### Task 1: What is Docker?
Research and write short notes on:
- What is a container and why do we need them?

A container packages an application with everything it needs to run: code, runtime, libraries, and system tools, so it behaves the same on any machine. Before containers, "it works on my machine" was a constant problem because dependencies, OS versions, and configs drifted between dev, test, and production.

- Containers vs Virtual Machines — what's the real difference?
Here's the same content rewritten as prose for your markdown file.
Containers vs Virtual Machines
The core difference between the two comes down to what each one actually virtualises. A virtual machine virtualises the entire hardware layer and runs a full guest operating system on top of it. A container, by contrast, only virtualises the application layer, it shares the host's existing OS kernel rather than running its own.
This difference shows up in almost every practical measure. A VM typically takes minutes to boot, since it has to start up a whole operating system from scratch, while a container starts in seconds, since there's no OS to boot, just the application process itself. Size follows the same pattern, VMs run into gigabytes because a full OS is bundled in, while containers are usually just megabytes, containing only the app and its dependencies.
Isolation works differently too. VMs offer strong isolation, each one runs its own separate kernel, completely walled off from the others. Containers offer isolation at the process level instead, they're separated from each other but still share the host's single kernel underneath. This has a direct effect on resource use, VMs are heavy since every single one carries its own OS overhead, while containers are light, letting many of them run comfortably on one host at once.
These trade-offs point to different use cases. VMs make sense when you genuinely need to run different operating systems side by side on one machine. Containers make sense when the goal is packaging and shipping an application consistently, so it behaves the same way wherever it runs.
A useful way to think about it: a VM is like a separate house, its own foundation, plumbing, and walls, entirely self-contained. A container is more like a serviced apartment, it shares the building's core infrastructure, the kernel, but still has its own locked door, its own furniture, and its own belongings inside.




- What is the Docker architecture? (daemon, client, images, containers, registry)
Docker is made up of a few core components that work together. 

The Docker Client is the CLI you actually type commands into, things like docker run or docker ps, and its job is simply to send those requests off to the daemon.

The Docker Daemon (dockerd) is what does the real work, running quietly in the background on the host machine. It's responsible for building images, running containers, and managing networking and volumes behind the scenes.

Docker Images are read-only templates that already have the application code, libraries, and dependencies baked in. Containers don't exist independently, they're launched from an image.

Docker Containers are the running instances of those images, each one an isolated process with its own filesystem, its own network setup, and its own process space, even though it shares the host's kernel underneath.

Finally, the Docker Registry is where images are stored and shared so they can be pulled and reused elsewhere. Docker Hub is the default public registry most people start with, though private registries exist too for internal or proprietary images.

---

### Task 2: Install Docker
1. Install Docker on your machine (or use a cloud instance)
2. Verify the installation
3. Run the `hello-world` container

ubuntu@devops-ai-powered:~$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/



4. Read the output carefully — it explains what just happened

---

### Task 3: Run Real Containers
1. Run an **Nginx** container and access it in your browser
Added the screenshot in linkdin


2. Run an **Ubuntu** container in interactive mode — explore it like a mini Linux machine
screenshot in linkdind

3. List all running containers
ubuntu@devops-ai-powered:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                     NAMES
55329516a630   nginx     "/docker-entrypoint.…"   21 minutes ago   Up 21 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   webserver2
df07f7409ba8   nginx     "/docker-entrypoint.…"   23 minutes ago   Up 23 minutes   80/tcp                                    webserver
8044a0904779   nginx     "/docker-entrypoint.…"   23 minutes ago   Up 23 minutes   80/tcp                                    eager_haslett
baad00414820   nginx     "/docker-entrypoint.…"   55 minutes ago   Up 55 minutes   80/tcp                                    exciting_solomon

4. List all containers (including stopped ones)
ubuntu@devops-ai-powered:~$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                        PORTS                                     NAMES
81041f3c887c   hello-world   "/hello"                 3 minutes ago    Exited (0) 3 minutes ago                                                stupefied_albattani
49a4e8f656d0   hello-world   "/hello"                 4 minutes ago    Exited (0) 4 minutes ago                                                objective_nobel
55329516a630   nginx         "/docker-entrypoint.…"   21 minutes ago   Up 21 minutes                 0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   webserver2
df07f7409ba8   nginx         "/docker-entrypoint.…"   23 minutes ago   Up 23 minutes                 80/tcp                                    webserver
8044a0904779   nginx         "/docker-entrypoint.…"   23 minutes ago   Up 23 minutes                 80/tcp                                    eager_haslett
d30ada4ede7d   ubuntu        "bash"                   27 minutes ago   Exited (0) 27 minutes ago                                               xenodochial_shirley
7c7407ee528e   ubuntu        "bash"                   27 minutes ago   Exited (0) 27 minutes ago                                               epic_newton
d2817108b2d4   ubuntu        "bash"                   28 minutes ago   Exited (0) 28 minutes ago                                               flamboyant_murdock
d41d0850b87e   ubuntu        "bash"                   29 minutes ago   Exited (0) 28 minutes ago                                               eloquent_mcclintock
e2c32bbb8344   ubuntu        "/bin/bash"              29 minutes ago   Exited (127) 29 minutes ago                                             xenodochial_sinoussi
baad00414820   nginx         "/docker-entrypoint.…"   55 minutes ago   Up 55 minutes                 80/tcp                                    exciting_solomon
eee0722f68cf   hello-world   "/hello"                 59 minutes ago   Exited (0) 59 minutes ago                                               great_agnesi
73e2d4d33f93   ubuntu        "/bin/bash"              10 days ago      Exited (0) 10 days ago                                                  sharp_easley

5. Stop and remove a container

---

### Task 4: Explore
1. Run a container in **detached mode** — what's different?
2. Give a container a custom **name**
3. Map a **port** from the container to your host
4. Check **logs** of a running container
5. Run a command **inside** a running container

---

## Hints
- `docker run`, `docker ps`, `docker stop`, `docker rm`
- Interactive mode: `-it` flag
- Detached mode: `-d` flag
- Port mapping: `-p host:container`
- Naming: `--name`
- Logs: `docker logs`
- Exec into container: `docker exec`

---

## Why This Matters for DevOps
Docker is the foundation of modern deployment. Every CI/CD pipeline, Kubernetes cluster, and microservice architecture starts with containers. Today you took the first step.

---
