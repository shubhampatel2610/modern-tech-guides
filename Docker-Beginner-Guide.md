# Docker Complete Guide for Beginners

**Date:** February 5, 2026  
**Topics Covered:** Docker basics, Dockerfile explanation, Next.js integration, deployment options

---

## Table of Contents
1. [What is Docker?](#what-is-docker)
2. [Why We Use Docker](#why-we-use-docker)
3. [Understanding Your RBAC Dockerfile](#understanding-your-rbac-dockerfile)
4. [Dockerizing a Next.js Application](#dockerizing-a-nextjs-application)
5. [Running Docker Containers](#running-docker-containers)
6. [Deployment Options](#deployment-options)
7. [Development vs Production](#development-vs-production)
8. [Useful Docker Commands](#useful-docker-commands)

---

## What is Docker?

Think of Docker as a **shipping container for your software**. 

### Real-World Analogy

Imagine you're moving to a new house:

- **Without Docker**: You pack each item separately, worry about fragile items, need specific boxes for different things, and hope nothing breaks during the move.

- **With Docker**: Everything goes into standardized shipping containers that can be loaded on any truck, ship, or train. The contents are protected and isolated.

Similarly, Docker packages your application with everything it needs (code, libraries, dependencies) into a **container** that runs the same way everywhere.

---

## Why We Use Docker

### 1. The "It Works on My Machine" Problem

**Scenario without Docker:**
```
Developer A: "The app works fine on my laptop!"
Developer B: "It crashes on mine..."
Production Server: "Error: Node.js version mismatch"
```

**With Docker:**
```
Everyone uses the SAME container = Everyone gets the SAME result ✅
```

### 2. Easy Environment Setup

**Without Docker:**
```bash
# New developer joins the team
1. Install Node.js v20
2. Install PostgreSQL
3. Install Redis
4. Configure environment variables
5. Install specific library versions
6. Hope everything works together...
```

**With Docker:**
```bash
# New developer joins the team
docker run my-app    # Done! ✅
```

### 3. Isolation

Multiple projects on one computer don't interfere with each other:
- Project A needs Node.js v16
- Project B needs Node.js v20
- Both run in separate containers without conflict

### 4. Scalability

Need to handle more users?
```bash
# Run 1 instance
docker run my-app

# Need more power? Run 10 instances!
# Kubernetes or Docker Swarm handles this automatically
```

---

## Understanding Your RBAC Dockerfile

Let's break down the Dockerfile line by line:

### Complete Dockerfile with Explanations

```dockerfile
# ========================================
# STEP 1: Choose Base Image
# ========================================
FROM ba-common-docker-stable-local.artifactory-na.honeywell.com/ba-common.ba-node-v20:latest

# What this does:
# - Starts with a pre-built image that has Node.js v20 installed
# - Like starting with a pre-furnished apartment instead of an empty room
# - This is from Honeywell's internal Docker registry (Artifactory)
# - Contains all the basic tools and configurations needed


# ========================================
# STEP 2: Add Metadata Labels
# ========================================
LABEL pipelineName="pipeline-name" \
    pipelineKey="ZSHELGHC" \
    offeringKey="RIDOOYVI"

# What this does:
# - Adds identification tags to the container
# - Like putting name tags on your luggage
# - Used by deployment systems to track and manage containers
# - Helps in monitoring and logging


# ========================================
# STEP 3: System Updates
# ========================================
RUN echo 'http://dl-3.alpinelinux.org/alpine/v3.13/main' >> /etc/apk/repositories
RUN apk upgrade

# What this does:
# - Adds a package repository (source for software)
# - Updates all system packages to latest versions
# - Like running Windows Update
# - Ensures security patches are applied


# ========================================
# STEP 4: Set Working Directory
# ========================================
WORKDIR /app

# What this does:
# - Creates a folder called /app
# - Changes into that folder
# - All subsequent commands run from here
# - Like doing "mkdir /app && cd /app"


# ========================================
# STEP 5: Install Dependencies
# ========================================
COPY package.json yarn.lock ./
# Copies only the dependency files first (optimization trick!)

RUN yarn install --production && yarn cache clean
# Installs only production dependencies (not dev tools)
# Cleans cache to reduce image size

RUN rm -rf /app/node_modules/inflight
# Removes a specific problematic module (company-specific requirement)


# ========================================
# STEP 6: Copy Application Code
# ========================================
COPY ./dist ./dist

# What this does:
# - Copies the compiled/built application code
# - The ./dist folder contains the production-ready JavaScript
# - Only copies the compiled code, not source TypeScript files


# ========================================
# STEP 7: Expose Ports
# ========================================
EXPOSE 3000 7000

# What this does:
# - Declares that the app will listen on ports 3000 and 7000
# - Port 3000: Main application API
# - Port 7000: Likely metrics/monitoring endpoint
# - Like opening specific doors in a building


# ========================================
# STEP 8: Set User (Security)
# ========================================
USER node

# What this does:
# - Switches from root user to 'node' user
# - Security best practice (don't run as admin!)
# - Limits what the container can do if compromised


# ========================================
# STEP 9: Define Startup Command
# ========================================
CMD ["npm", "run", "start:prod"]

# What this does:
# - Specifies the command to run when container starts
# - Launches your application in production mode
# - This is the final step - your app is now running!
```

### Why This Order Matters

Docker builds images in **layers**. Each command creates a new layer:

```
Layer 1: Base image (Node.js)
Layer 2: Labels
Layer 3: System updates
Layer 4: Working directory
Layer 5: package.json copied
Layer 6: Dependencies installed  ← Cached if package.json unchanged
Layer 7: Application code copied ← Changes frequently
Layer 8: Expose ports
Layer 9: User setup
Layer 10: CMD command
```

**Key Optimization**: We copy `package.json` before application code because:
- Dependencies change rarely
- Application code changes frequently
- Docker caches layers that don't change
- Faster rebuilds!

---

## Dockerizing a Next.js Application

### Step 1: Create Project Structure

Your Next.js project should look like:
```
my-nextjs-app/
├── pages/
├── public/
├── styles/
├── package.json
├── next.config.js
└── [We'll add Dockerfile here]
```

### Step 2: Create Dockerfile (Simple Version)

Create a file named `Dockerfile` (no extension):

```dockerfile
# ========================================
# Start with official Node.js image
# ========================================
FROM node:20-alpine
# alpine = lightweight Linux (small image size)

# ========================================
# Set working directory
# ========================================
WORKDIR /app

# ========================================
# Install dependencies
# ========================================
COPY package.json package-lock.json* ./
# The * makes package-lock.json optional

RUN npm install
# Installs all dependencies from package.json

# ========================================
# Copy application files
# ========================================
COPY . .
# Copies everything from your project folder

# ========================================
# Build the Next.js application
# ========================================
RUN npm run build
# Creates optimized production build in .next folder

# ========================================
# Expose the port
# ========================================
EXPOSE 3000
# Next.js default port

# ========================================
# Start the application
# ========================================
CMD ["npm", "run", "start"]
# Runs the production server
```

### Step 3: Create .dockerignore

Create `.dockerignore` file (like .gitignore for Docker):

```
# Don't copy these into the container
node_modules
.next
.git
*.log
.env.local
.DS_Store
README.md
.gitignore
```

**Why?**
- `node_modules`: We'll install fresh in container
- `.next`: We'll build fresh in container
- Reduces image size and build time

### Step 4: Optimized Dockerfile (Multi-Stage Build)

For production, use this better version:

```dockerfile
# ========================================
# STAGE 1: BUILD
# ========================================
FROM node:20-alpine AS builder
# "AS builder" names this stage

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy source code and build
COPY . .
RUN npm run build

# ========================================
# STAGE 2: RUN (Final Image)
# ========================================
FROM node:20-alpine
# Start fresh with a clean image

WORKDIR /app

# Copy only what's needed from builder stage
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules

# Expose port
EXPOSE 3000

# Start production server
CMD ["npm", "start"]
```

**Benefits of Multi-Stage Build:**
- Final image is smaller (doesn't include build tools)
- More secure (fewer packages = fewer vulnerabilities)
- Faster deployment

**Size Comparison:**
- Single-stage: ~500 MB
- Multi-stage: ~200 MB

---

## Running Docker Containers

### Building Your Image

```bash
# Basic syntax
docker build -t image-name .

# Example
docker build -t my-nextjs-app .

# What happens:
# -t = "tag" (name) your image
# . = use current directory (where Dockerfile is)
```

**Build Output:**
```
Step 1/10 : FROM node:20-alpine
 ---> Pulling from library/node
Step 2/10 : WORKDIR /app
 ---> Running in abc123
Step 3/10 : COPY package.json
 ---> Successfully built xyz789
```

### Running Your Container

```bash
# Basic run
docker run my-nextjs-app

# Run with port mapping
docker run -p 3000:3000 my-nextjs-app
# -p 3000:3000 means:
#    ↑      ↑
#    |      └─ Container's port
#    └─ Your computer's port

# Run in background (detached mode)
docker run -d -p 3000:3000 my-nextjs-app
# -d = detached (runs in background)
# Returns container ID

# Run with name
docker run -d -p 3000:3000 --name my-app my-nextjs-app
# --name assigns a friendly name

# Run with environment variables
docker run -p 3000:3000 -e DATABASE_URL=postgres://db my-nextjs-app
# -e sets environment variable

# Run with volume (for development)
docker run -p 3000:3000 -v $(pwd):/app my-nextjs-app
# -v mounts current directory into container
# Changes on your computer reflect immediately
```

### Viewing Your Application

Once running, open browser:
```
http://localhost:3000
```

---

## Deployment Options

### Option 1: Vercel (Recommended for Next.js)

**⚠️ Note:** Vercel doesn't use Docker - it has its own system!

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Follow prompts - done!
```

**Pros:**
- ✅ Made by Next.js creators
- ✅ Automatic deployments from Git
- ✅ Free generous tier
- ✅ Global CDN
- ✅ Zero configuration

**Cons:**
- ❌ No Docker support
- ❌ Vendor lock-in

---

### Option 2: Railway.app (Docker Support)

**Railway automatically detects your Dockerfile!**

**Steps:**
1. Push code to GitHub
2. Go to [railway.app](https://railway.app)
3. Connect repository
4. Railway detects Dockerfile
5. Automatic deployment

**Pros:**
- ✅ Supports Docker
- ✅ Free tier ($5/month credit)
- ✅ Very simple UI
- ✅ Automatic HTTPS

**Cons:**
- ❌ Limited free tier
- ❌ Smaller than AWS/GCP

---

### Option 3: Google Cloud Run (Best for Docker)

```bash
# Build and push to Google Container Registry
gcloud builds submit --tag gcr.io/PROJECT_ID/my-app

# Deploy to Cloud Run
gcloud run deploy --image gcr.io/PROJECT_ID/my-app --platform managed

# Done! Get a URL like: https://my-app-xyz-uc.a.run.app
```

**Pros:**
- ✅ Fully managed Docker hosting
- ✅ Auto-scaling (0 to thousands)
- ✅ Pay only for actual usage
- ✅ Great free tier

**Cons:**
- ❌ Requires Google Cloud account
- ❌ Learning curve for GCP

---

### Option 4: AWS ECS (Enterprise Grade)

**More complex but very powerful:**

```bash
# 1. Build and tag image
docker build -t my-app .

# 2. Push to AWS ECR (container registry)
aws ecr get-login-password | docker login --username AWS --password-stdin
docker tag my-app:latest AWS_ACCOUNT.dkr.ecr.region.amazonaws.com/my-app
docker push AWS_ACCOUNT.dkr.ecr.region.amazonaws.com/my-app

# 3. Deploy to ECS (through AWS console or CLI)
```

**Pros:**
- ✅ Enterprise-grade reliability
- ✅ Integrates with all AWS services
- ✅ Highly scalable

**Cons:**
- ❌ Complex setup
- ❌ Expensive
- ❌ Steep learning curve

---

### Comparison Table

| Platform | Uses Docker | Difficulty | Free Tier | Best For |
|----------|-------------|-----------|-----------|----------|
| **Vercel** | ❌ No | ⭐ Easiest | Yes (generous) | Next.js apps |
| **Railway** | ✅ Yes | ⭐⭐ Easy | Yes ($5/mo) | Small to medium apps |
| **Render** | ✅ Yes | ⭐⭐ Easy | Yes (limited) | General Docker apps |
| **Google Cloud Run** | ✅ Yes | ⭐⭐⭐ Medium | Yes (good) | Scalable Docker apps |
| **AWS ECS** | ✅ Yes | ⭐⭐⭐⭐ Hard | Yes (limited) | Enterprise applications |
| **Azure AKS** | ✅ Yes | ⭐⭐⭐⭐ Hard | No | Large enterprises |

---

## Development vs Production

### Development Usage

**Why use Docker in development?**

1. **Consistent Environment**
   ```bash
   # Everyone on team runs:
   docker-compose up
   
   # No more:
   # "Did you update Node?"
   # "Did you install the new package?"
   # "Which database version are you using?"
   ```

2. **Easy Onboarding**
   ```bash
   # New developer's first day:
   git clone project
   docker-compose up
   # App is running! ✅
   ```

3. **Multiple Projects**
   ```
   Project A: Node v14 + PostgreSQL 12
   Project B: Node v20 + MongoDB 5
   Project C: Python 3.9 + Redis
   
   All running simultaneously without conflicts! ✅
   ```

**Example docker-compose.yml for Development:**

```yaml
# This file defines multiple services
version: '3.8'

services:
  # Your Next.js app
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app  # Live reload - changes reflect immediately
      - /app/node_modules  # Don't override node_modules
    environment:
      - DATABASE_URL=postgres://db:5432/mydb
    depends_on:
      - db  # Wait for database to start

  # PostgreSQL database
  db:
    image: postgres:15
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=mydb
    volumes:
      - db-data:/var/lib/postgresql/data  # Persist data

volumes:
  db-data:  # Named volume for database
```

**Run everything:**
```bash
docker-compose up
# Starts app + database together!
```

---

### Production Usage

**Your RBAC Application Example:**

Looking at your deployment files:

```yaml
# deploy/application/content/aks-deployment.yml
# This deploys to Azure Kubernetes Service (AKS)

apiVersion: apps/v1
kind: Deployment
metadata:
  name: rbac-api
spec:
  replicas: 3  # Run 3 containers for redundancy
  template:
    spec:
      containers:
      - name: rbac
        image: your-docker-image:latest
        ports:
        - containerPort: 3000
```

**What happens in production:**

1. **Build Phase**
   ```bash
   # CI/CD pipeline builds Docker image
   docker build -t rbac:v1.2.3 .
   ```

2. **Push to Registry**
   ```bash
   # Upload to container registry
   docker push registry.company.com/rbac:v1.2.3
   ```

3. **Deploy to Kubernetes**
   ```bash
   # Kubernetes pulls image and creates containers
   kubectl apply -f deployment.yml
   # Creates 3 containers running your app
   ```

4. **Auto-scaling**
   ```
   Normal load: 3 containers
   High traffic: Kubernetes automatically scales to 10 containers
   Low traffic: Scales back down to 3
   ```

5. **Self-healing**
   ```
   Container crashes → Kubernetes automatically starts new one
   Server fails → Kubernetes moves containers to healthy server
   ```

---

### Development vs Production Flow

```
┌─────────────────────────────────────────────────────┐
│ DEVELOPMENT                                         │
├─────────────────────────────────────────────────────┤
│ 1. Developer writes code                            │
│ 2. docker-compose up (local testing)                │
│ 3. Make changes → Auto-reload                       │
│ 4. Commit to Git                                    │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│ CI/CD PIPELINE                                      │
├─────────────────────────────────────────────────────┤
│ 1. Automated tests run                              │
│ 2. docker build (create image)                      │
│ 3. Push to registry                                 │
│ 4. Deploy to staging environment                    │
│ 5. Run integration tests                            │
└─────────────────────────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────┐
│ PRODUCTION                                          │
├─────────────────────────────────────────────────────┤
│ 1. Kubernetes pulls image                           │
│ 2. Creates containers                               │
│ 3. Load balancer distributes traffic                │
│ 4. Monitors health                                  │
│ 5. Auto-scales based on demand                      │
└─────────────────────────────────────────────────────┘
```

---

## Useful Docker Commands

### Image Management

```bash
# List all images
docker images

# Example output:
# REPOSITORY          TAG       IMAGE ID       SIZE
# my-nextjs-app      latest    abc123         250MB
# node               20-alpine xyz789         180MB

# Remove an image
docker rmi image-name

# Remove unused images
docker image prune

# Remove all unused images
docker image prune -a

# Build image
docker build -t my-app:v1.0 .

# Tag an image (create alias)
docker tag my-app:v1.0 my-app:latest
```

---

### Container Management

```bash
# List running containers
docker ps

# Example output:
# CONTAINER ID   IMAGE           STATUS        PORTS
# a1b2c3d4       my-nextjs-app   Up 5 mins     0.0.0.0:3000->3000/tcp

# List all containers (including stopped)
docker ps -a

# Start a container
docker start container-id

# Stop a container
docker stop container-id

# Stop gracefully (gives app 10 seconds to shut down)
docker stop -t 10 container-id

# Force stop (immediate)
docker kill container-id

# Remove a container
docker rm container-id

# Remove a running container (force)
docker rm -f container-id

# Remove all stopped containers
docker container prune
```

---

### Logs and Debugging

```bash
# View container logs
docker logs container-id

# Follow logs (like tail -f)
docker logs -f container-id

# Show last 100 lines
docker logs --tail 100 container-id

# Show logs with timestamps
docker logs -t container-id

# Execute command in running container
docker exec container-id ls /app

# Open shell in running container (debugging)
docker exec -it container-id sh
# -i = interactive
# -t = terminal
# sh = shell (bash for non-alpine images)

# Example debugging session:
docker exec -it my-app sh
/app # ls
dist  node_modules  package.json
/app # cat package.json
/app # exit
```

---

### Inspecting Containers

```bash
# Show detailed container info
docker inspect container-id

# Show resource usage (CPU, memory)
docker stats

# Example output:
# CONTAINER  CPU %   MEM USAGE / LIMIT    MEM %
# my-app     0.50%   150MiB / 2GiB        7.32%

# Show container processes
docker top container-id

# Show port mappings
docker port container-id
```

---

### Network Management

```bash
# List networks
docker network ls

# Create network
docker network create my-network

# Connect container to network
docker network connect my-network container-id

# Run container on specific network
docker run --network my-network my-app
```

---

### Volume Management

```bash
# List volumes
docker volume ls

# Create volume
docker volume create my-data

# Remove volume
docker volume rm my-data

# Remove unused volumes
docker volume prune

# Run container with volume
docker run -v my-data:/app/data my-app
```

---

### Docker Compose Commands

```bash
# Start all services
docker-compose up

# Start in background
docker-compose up -d

# Stop all services
docker-compose down

# View logs
docker-compose logs

# Follow logs
docker-compose logs -f

# Rebuild images
docker-compose build

# Rebuild and start
docker-compose up --build

# Scale a service
docker-compose up --scale app=3
# Runs 3 instances of 'app' service
```

---

### Cleanup Commands

```bash
# Remove all stopped containers
docker container prune

# Remove all unused images
docker image prune -a

# Remove all unused volumes
docker volume prune

# Remove all unused networks
docker network prune

# NUCLEAR OPTION - Clean everything
docker system prune -a --volumes
# ⚠️ Warning: This removes:
#   - All stopped containers
#   - All networks not used by containers
#   - All images without containers
#   - All build cache
#   - All volumes
```

---

## Real-World Example: Complete Workflow

### Scenario: You're building a blog with Next.js

**Step 1: Project Setup**

```bash
# Create Next.js app
npx create-next-app my-blog
cd my-blog
```

**Step 2: Create Dockerfile**

```dockerfile
# Dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["npm", "start"]
```

**Step 3: Create .dockerignore**

```
node_modules
.next
.git
*.log
```

**Step 4: Build Image**

```bash
docker build -t my-blog .

# Output:
# [+] Building 45.2s (15/15) FINISHED
# Successfully built abc123
# Successfully tagged my-blog:latest
```

**Step 5: Run Locally**

```bash
docker run -p 3000:3000 my-blog

# Open browser: http://localhost:3000
# Your blog is running! ✅
```

**Step 6: Test Changes**

```bash
# Make code changes
# Rebuild image
docker build -t my-blog .

# Stop old container
docker stop old-container-id

# Run new container
docker run -p 3000:3000 my-blog
```

**Step 7: Deploy to Production**

```bash
# Option A: Railway
git push origin main
# Railway auto-deploys

# Option B: Google Cloud Run
gcloud builds submit --tag gcr.io/my-project/my-blog
gcloud run deploy --image gcr.io/my-project/my-blog

# You get a URL: https://my-blog-xyz.a.run.app
```

**Done! Your blog is live! 🎉**

---

## Common Issues and Solutions

### Issue 1: Port Already in Use

```bash
Error: bind: address already in use

# Solution: Use different port
docker run -p 3001:3000 my-app
#                ↑
#          Use 3001 instead of 3000

# Or stop conflicting container
docker ps  # Find conflicting container
docker stop container-id
```

---

### Issue 2: Image Build Fails

```bash
Error: COPY failed: no source files found

# Solution: Check .dockerignore
# Make sure you're not excluding needed files

# Also check current directory
docker build -t my-app .
#                       ↑
#              Make sure you're in project root
```

---

### Issue 3: Container Stops Immediately

```bash
docker ps  # Container not listed

docker ps -a  # Shows stopped container
# STATUS: Exited (1) 2 seconds ago

# Check logs to see what went wrong
docker logs container-id

# Common causes:
# 1. Application crashed
# 2. Wrong CMD in Dockerfile
# 3. Missing environment variables
```

---

### Issue 4: Can't Access Application

```bash
# Container is running but localhost:3000 doesn't work

# Check port mapping
docker ps
# PORTS
# 3000/tcp  ← No port mapping! ❌

# Should be:
# 0.0.0.0:3000->3000/tcp  ✅

# Solution: Stop and restart with -p flag
docker stop container-id
docker run -p 3000:3000 my-app
```

---

### Issue 5: Changes Not Reflecting

```bash
# Made code changes but app still shows old version

# You need to rebuild!
docker build -t my-app .  # Rebuild image
docker stop old-container
docker run -p 3000:3000 my-app  # Run new container

# For development, use volumes instead:
docker run -p 3000:3000 -v $(pwd):/app my-app
# Now changes reflect immediately
```

---

## Best Practices Summary

### ✅ DO

1. **Use specific image versions**
   ```dockerfile
   FROM node:20-alpine  # Good - specific version
   FROM node:latest     # Bad - unpredictable
   ```

2. **Use .dockerignore**
   ```
   node_modules
   .git
   .env
   ```

3. **Use multi-stage builds**
   - Smaller final images
   - More secure

4. **Run as non-root user**
   ```dockerfile
   USER node
   ```

5. **Use volumes for development**
   ```bash
   docker run -v $(pwd):/app my-app
   ```

6. **Label your images**
   ```dockerfile
   LABEL version="1.0.0"
   LABEL description="My awesome app"
   ```

---

### ❌ DON'T

1. **Don't store secrets in Dockerfile**
   ```dockerfile
   # BAD!
   ENV PASSWORD=secret123
   
   # Good - use environment variables at runtime
   docker run -e PASSWORD=$PASSWORD my-app
   ```

2. **Don't use root user in production**
   ```dockerfile
   # BAD!
   CMD ["npm", "start"]
   
   # Good
   USER node
   CMD ["npm", "start"]
   ```

3. **Don't create huge images**
   ```dockerfile
   # BAD!
   FROM ubuntu  # 70 MB base
   
   # Good
   FROM alpine  # 5 MB base
   ```

4. **Don't copy everything unnecessarily**
   ```dockerfile
   # BAD!
   COPY . .  # Copies .git, node_modules, etc.
   
   # Good - use .dockerignore
   ```

---

## Quick Reference Card

### Most Common Commands

```bash
# BUILD
docker build -t my-app .

# RUN
docker run -p 3000:3000 my-app

# RUN IN BACKGROUND
docker run -d -p 3000:3000 --name my-app my-app

# LIST RUNNING
docker ps

# VIEW LOGS
docker logs container-id

# STOP
docker stop container-id

# REMOVE
docker rm container-id

# SHELL ACCESS
docker exec -it container-id sh

# CLEANUP
docker system prune
```

---

## Glossary

- **Image**: Blueprint/template for containers (like a recipe)
- **Container**: Running instance of an image (like a dish made from recipe)
- **Dockerfile**: Instructions to build an image
- **Registry**: Storage for Docker images (like GitHub for code)
- **Volume**: Persistent storage for containers
- **Network**: Allows containers to communicate
- **Tag**: Version label for images (like v1.0.0)
- **Layer**: Each instruction in Dockerfile creates a layer
- **Build Context**: Files sent to Docker daemon when building
- **Multi-stage Build**: Using multiple FROM statements to optimize image size

---

## Additional Resources

### Official Documentation
- Docker Docs: https://docs.docker.com
- Next.js Docker: https://nextjs.org/docs/deployment#docker-image

### Learning Resources
- Docker 101 Tutorial: https://www.docker.com/101-tutorial
- Play with Docker: https://labs.play-with-docker.com

### Tools
- Docker Desktop: https://www.docker.com/products/docker-desktop
- Docker Hub: https://hub.docker.com

---

## Conclusion

Docker is like a **universal shipping container for your code**:

1. **Development**: Everyone on your team uses identical environment
2. **Testing**: Test in same environment as production
3. **Production**: Deploy the exact same container that worked in testing
4. **Scaling**: Run multiple containers to handle more users

**Key Takeaway**: "Build once, run anywhere" ✅

---

**Created on:** February 5, 2026  
**Topics:** Docker, Dockerfile, Next.js, Containerization, Deployment  
**Skill Level:** Beginner to Intermediate

---

## Your Next Steps

1. ✅ Install Docker Desktop
2. ✅ Try the Next.js example from this guide
3. ✅ Build and run your first container
4. ✅ Explore docker-compose for multi-container apps
5. ✅ Deploy to Railway or Google Cloud Run

**Happy Dockerizing! 🐳**
