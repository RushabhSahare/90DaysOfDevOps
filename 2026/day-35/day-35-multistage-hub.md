# Day 35 – Multi-Stage Builds & Docker Hub

## Task
Today's goal is to **build optimized images and share them with the world**.

Multi-stage builds are how real teams ship small, secure images. Docker Hub is how you distribute them. Both are interview favourites.

---

## Expected Output
- A markdown file: `day-35-multistage-hub.md`
- Dockerfiles demonstrating multi-stage builds
- An image pushed to your Docker Hub account

---

## Challenge Tasks

### Task 1: The Problem with Large Images
1. Write a simple Go, Java, or Node.js app (even a "Hello World" is fine)

const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello from Day 35, multi stage builds!');
});

app.listen(3000, () => {
  console.log('App running on port 3000');
});







2. Create a Dockerfile that builds and runs it in a **single stage**
FROM node:20

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000
CMD ["node", "index.js"]




3. Build the image and check its **size**

ubuntu@devops-ai-powered:~/Docker/day35-app$ docker images day35-app:single 
                                                                              i Info →   U  In Use
IMAGE              ID             DISK USAGE   CONTENT SIZE   EXTRA
day35-app:single   6f6b0c274a2a       1.59GB          400MB        



Note down the size — you'll compare it later.
400Mb
---

### Task 2: Multi-Stage Build
1. Rewrite the Dockerfile using **multi-stage build**:
   - Stage 1: Build the app (install dependencies, compile)
   - Stage 2: Copy only the built artifact into a minimal base image (`alpine`, `distroless`, or `scratch`)

# Stage 1: build
FROM node:20 AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

# Stage 2: run
FROM node:20-alpine

WORKDIR /app

# only copy what's needed from the builder stage, not the whole build environment
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package*.json ./
COPY --from=builder /app/index.js ./

EXPOSE 3000
CMD ["node", "index.js"]


   
2. Build the image and check its size again
ubuntu@devops-ai-powered:~/Docker/day35-app$ docker build -f Dockerfile.multi -t day35-app:multi .


3. Compare the two sizes

day35-app:multi            7723a5255efb        198MB           49MB        
day35-app:single           6f6b0c274a2a       1.59GB          400MB     



Write in your notes: Why is the multi-stage image so much smaller?

Multi-stage builds solve a real tension: you often need heavyweight tools to build something (compilers, package managers, dev dependencies) but none of that belongs in the container that actually runs it in production. This pattern, build fat, ship thin, is one of the most common real-world Docker optimisations.



---

### Task 3: Push to Docker Hub
1. Create a free account on [Docker Hub](https://hub.docker.com) (if you don't have one)
2. Log in from your terminal
docker login -u rushabhs7


3. Tag your image properly: `yourusername/image-name:tag`

ubuntu@devops-ai-powered:~/Docker/day35-app$ docker tag day35-app:multi rushabhs7/day35-app:v1
ubuntu@devops-ai-powered:~/Docker/day35-app$ docker tag day35-app:multi rushabhs7/day35-app:latest


4. Push it to Docker Hub

ubuntu@devops-ai-powered:~/Docker/day35-app$ docker push rushabhs7/day35-app:v1
The push refers to repository [docker.io/rushabhs7/day35-app]
6a0ac1617861: Pushed 
4feea04c1543: Pushed 
58e2c39089ab: Pushed 
0928bbd0e4b1: Pushed 
b2cbbfe903b0: Pushed 
fff4e2c1b189: Pushed 
1a6ead6c01d8: Pushed 
57dad25904ed: Pushed 
227a0eccbe2e: Pushed 
v1: digest: sha256:7723a5255efbb582a6bdd18267358e2ff603659b322d9446ff65f7c251732968 size: 856


ubuntu@devops-ai-powered:~/Docker/day35-app$ docker push rushabhs7/day35-app:latest
The push refers to repository [docker.io/rushabhs7/day35-app]
b2cbbfe903b0: Layer already exists 
1a6ead6c01d8: Layer already exists 
57dad25904ed: Layer already exists 
227a0eccbe2e: Layer already exists 
58e2c39089ab: Already exists 
0928bbd0e4b1: Layer already exists 
fff4e2c1b189: Layer already exists 
6a0ac1617861: Layer already exists 
4feea04c1543: Layer already exists 
latest: digest: sha256:7723a5255efbb582a6bdd18267358e2ff603659b322d9446ff65f7c251732968 size: 856


5. Pull it on a different machine (or after removing locally) to verify

ubuntu@devops-ai-powered:~/Docker/day35-app$ docker rmi rushabhs7/day35-app:v1 rushabhs7/day35-app:latest
Untagged: rushabhs7/day35-app:v1
Untagged: rushabhs7/day35-app:latest

Yes. it pulled successfully.



---

### Task 4: Docker Hub Repository
1. Go to Docker Hub and check your pushed image
yes it is there.

2. Add a **description** to the repository

3. Explore the **tags** tab — understand how versioning works
4. Pull a specific tag vs `latest` — what happens?

---

### Task 5: Image Best Practices
Apply these to one of your images and rebuild:
1. Use a **minimal base image** (alpine vs ubuntu — compare sizes)
2. **Don't run as root** — add a non-root USER in your Dockerfile
3. Combine `RUN` commands to **reduce layers**
4. Use **specific tags** for base images (not `latest`)

Check the size before and after.

---

## Hints
- Multi-stage: use `FROM ... AS builder` then `COPY --from=builder`
- Login: `docker login`
- Tag: `docker tag local-image:tag username/repo:tag`
- Push: `docker push username/repo:tag`
- Non-root user: `RUN adduser` + `USER`

---

## Submission
1. Add your Dockerfiles and `day-35-multistage-hub.md` to `2026/day-35/`
2. Include the link to your Docker Hub repo
3. Commit and push to your fork

---

## Learn in Public
Share your before/after image sizes on LinkedIn — the difference is always impressive.

`#90DaysOfDevOps` `#DevOpsKaJosh` `#TrainWithShubham`

Happy Learning!
**TrainWithShubham**
