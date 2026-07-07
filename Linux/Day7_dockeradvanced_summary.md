# Day 7 Docker Advanced Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer
**Date:** July 7, 2026

---

## What You Accomplished on Day 7

- Logged into Docker Hub using GitHub account
- Tagged Docker image correctly for Docker Hub
- Pushed custom image to Docker Hub (globally accessible)
- Learned Docker networking — custom networks, container DNS
- Connected containers together on same network
- Created and used Docker volumes for persistent storage
- Proved data survives container deletion with volumes
- Built multi-container app with Docker Compose
- Started and stopped entire app stack with one command
- Learned critical lesson — Docker Hub usernames must be lowercase

---

## READY-MADE PROMPT FOR NEXT AI SESSION

Copy and paste this into any AI platform to continue from Day 8:

---

Hi! I am Karthiveer from Chennai, India. I am on a 6-month focused journey to get a Cloud DevOps job in Finland (Helsinki). Please continue my day-by-day learning from Day 8.

COMPLETED SO FAR:
- Day 1: Linux CLI — Navigation, Files, Permissions, Processes, Bash Scripting
- Day 2: grep, find, pipes, networking, GitHub push
- Day 3: AWS EC2 launch, SSH, Nginx web server, Security Groups, live webpage
- Day 4: Environment variables, Cron jobs, AWS CLI v2, AWS IAM basics
- Day 5: Advanced Bash scripting, AWS S3 storage, System monitoring, Nginx advanced
- Day 6: Docker install, images, containers, Dockerfile, custom image build, management
- Day 7: Docker Hub push, Docker networking, Docker volumes, Docker Compose

CONTINUE FROM: Day 8 — Terraform fundamentals, Infrastructure as Code, deploy AWS resources with Terraform

MY COMPLETE SETUP:
- MacBook Air (Apple Silicon, zsh shell)
- Homebrew 6.0.5, Git 2.50.1 installed
- SSH shortcut: ssh finland-server
- AWS EC2: finland-devops-server, Elastic IP configured, eu-north-1 Stockholm
- Ubuntu 24.04 LTS, t3.micro free tier
- Nginx running on port 80 — personal webpage live
- Docker 29.6.1 installed — ubuntu user in docker group
- Custom image: karthiveer-app:v1 on Docker Hub
- Docker Hub username: karthiveer (lowercase)
- Docker Hub URL: hub.docker.com/r/karthiveer/karthiveer-app
- Docker Compose installed and working
- Scripts folder: ~/Scripts/ with health-check.sh, backup.sh, monitor.sh
- S3 bucket: karthiveer-finland-1783147889
- GitHub: github.com/Karthiveer/finland-journey
- Total XP earned: 4,075 XP across 7 days

LEARNING STYLE: Gamified with XP points and levels, theory first (2-3 mins), then hands-on terminal practice, screenshot errors for help, daily GitHub push.

Please start Day 8 with a quick recap of Day 7 then launch the gamified missions with interactive game widget!

---

END OF PROMPT

---

## Mission 1 — Docker Hub

### What is Docker Hub?
Public registry for Docker images — like GitHub but for containers.
Free account gives unlimited public repositories.
Anyone anywhere can pull your image with one command.

### Commands Practiced

```bash
# Login to Docker Hub
docker login
# Uses web-based authentication — one-time code in browser

# Tag image with Docker Hub username — MUST be lowercase
docker tag karthiveer-app:v1 karthiveer/karthiveer-app:v1

# Verify tag created
docker images

# Push image to Docker Hub
docker push karthiveer/karthiveer-app:v1

# Pull image back — simulates another server downloading
docker pull karthiveer/karthiveer-app:v1

# Run container from Docker Hub image
docker run -d -p 8083:80 --name hub-test karthiveer/karthiveer-app:v1

# Test it works
curl http://localhost:8083
```

### Critical Lesson — Lowercase Username

```bash
# WRONG — Capital K causes DNS failure
docker tag karthiveer-app:v1 Karthiveer/karthiveer-app:v1
docker push Karthiveer/karthiveer-app:v1
# Error: dial tcp: lookup Karthiveer: server misbehaving

# CORRECT — All lowercase
docker tag karthiveer-app:v1 karthiveer/karthiveer-app:v1
docker push karthiveer/karthiveer-app:v1
# Push succeeds ✅
```

### Fix Wrong Tag

```bash
# Remove wrong tag
docker rmi Karthiveer/karthiveer-app:v1

# Create correct lowercase tag
docker tag karthiveer-app:v1 karthiveer/karthiveer-app:v1

# Push correctly
docker push karthiveer/karthiveer-app:v1
```

### Your Docker Hub Details

```
Username: karthiveer
Image: karthiveer/karthiveer-app:v1
URL: hub.docker.com/r/karthiveer/karthiveer-app
Pull command: docker pull karthiveer/karthiveer-app:v1
```

Anyone in Helsinki can now run your app with:
```bash
docker pull karthiveer/karthiveer-app:v1
docker run -d -p 80:80 karthiveer/karthiveer-app:v1
```

---

## Mission 2 — Docker Networking

### What is Docker Networking?
Containers are isolated by default — cannot communicate.
Docker networks create private channels between containers.
Containers on same network find each other by NAME — not IP.

### Commands Practiced

```bash
# List existing networks
docker network ls
# Shows: bridge, host, none (defaults)

# Create custom network
docker network create finland-network

# Verify network created
docker network ls

# Run container connected to network
docker run -d \
  --name app-container \
  --network finland-network \
  karthiveer/karthiveer-app:v1

# Run second container on same network
docker run -d \
  --name db-simulator \
  --network finland-network \
  nginx:alpine

# Ping between containers by NAME — Docker DNS magic!
docker exec app-container ping db-simulator
# Press Ctrl+C to stop

# Inspect network — see all connected containers
docker network inspect finland-network

# Disconnect container from network
docker network disconnect finland-network db-simulator

# Remove network (all containers must be disconnected first)
docker network rm finland-network
```

### Docker DNS — Why It Matters

```
Without Docker networking:
Container A needs IP of Container B
IP changes when container restarts
Scripts break constantly

With Docker networking:
Container A just uses name "db-simulator"
Docker resolves name to correct IP automatically
Names never change — scripts always work
```

### Real Finnish App Architecture

```
finland-network
├── webapp-container (port 80)  ←→  Serves user requests
├── api-container (port 3000)   ←→  Business logic
└── db-container (port 5432)    ←→  PostgreSQL database

All on same network — communicate by name
Only webapp exposed to internet — others internal only
```

---

## Mission 3 — Docker Volumes

### What is a Docker Volume?
Persistent storage that survives container deletion.
Critical for databases, file uploads, logs.
Managed by Docker — stored on host filesystem.

### Commands Practiced

```bash
# Create volume
docker volume create finland-data

# List volumes
docker volume ls

# Inspect volume — see storage location
docker volume inspect finland-data

# Run container with volume mounted
# -v volumeName:containerPath
docker run -d \
  --name volume-test \
  -v finland-data:/usr/share/nginx/html \
  -p 8084:80 \
  nginx

# Write data inside container to volume
docker exec volume-test bash -c \
  "echo '<h1>Karthiveer Persistent Data!</h1>' \
  > /usr/share/nginx/html/index.html"

# Verify data served
curl http://localhost:8084

# Delete container
docker stop volume-test && docker rm volume-test

# Data survives! Run new container with same volume
docker run -d \
  --name volume-test2 \
  -v finland-data:/usr/share/nginx/html \
  -p 8085:80 \
  nginx

# Data still there!
curl http://localhost:8085
# Shows: Karthiveer Persistent Data!
```

### Volume vs Bind Mount

```bash
# Volume — Docker managed storage (recommended)
-v finland-data:/usr/share/nginx/html

# Bind Mount — map specific host folder
-v /home/ubuntu/html:/usr/share/nginx/html

# Use volumes for databases and persistent data
# Use bind mounts for development and config files
```

### When to Use Volumes

| Data Type | Use Volume? |
|-----------|-------------|
| Database files | ✅ Always |
| User uploads | ✅ Always |
| Application logs | ✅ Yes |
| Config files | Bind mount |
| Source code (dev) | Bind mount |

---

## Mission 4 — Docker Compose

### What is Docker Compose?
Tool for defining and running multi-container applications.
One YAML file describes your entire application stack.
One command starts everything perfectly configured.

### Your docker-compose.yml

```yaml
version: '3.8'
services:
  webapp:
    image: nginx:alpine
    ports:
      - "8086:80"
    volumes:
      - ./html:/usr/share/nginx/html
    networks:
      - app-network
    restart: always

  monitor:
    image: nginx:alpine
    ports:
      - "8087:80"
    networks:
      - app-network
    restart: always

networks:
  app-network:
    driver: bridge
```

### Commands Practiced

```bash
# Create project folder
mkdir ~/mycompose && cd ~/mycompose

# Create webpage
mkdir -p html
echo '<h1>Karthiveer Compose App - Helsinki Ready!</h1>' \
  > html/index.html

# Start entire application stack
docker compose up -d

# Check all services status
docker compose ps

# Test webapp
curl http://localhost:8086

# Test monitor
curl http://localhost:8087

# See combined logs from all containers
docker compose logs

# See logs for specific service
docker compose logs webapp

# Stop all services
docker compose down

# Start again
docker compose up -d

# Rebuild images and start
docker compose up -d --build

# Scale a service
docker compose up -d --scale webapp=3
```

### docker compose up vs down

```bash
docker compose up -d
# Creates containers, network, volumes
# Starts all services
# -d runs in background

docker compose down
# Stops all containers
# Removes containers and network
# KEEPS volumes (data safe)

docker compose down -v
# Also removes volumes (data deleted)
# Use only when you want clean slate
```

### Compose File Structure Explained

```yaml
version: '3.8'        # Compose file format version

services:             # Define your containers here
  webapp:             # Service name (also DNS name)
    image: nginx      # Which image to use
    ports:            # Port mapping host:container
      - "8086:80"
    volumes:          # Mount volumes or folders
      - ./html:/usr/share/nginx/html
    networks:         # Which networks to join
      - app-network
    restart: always   # Restart if container crashes

networks:             # Define custom networks
  app-network:
    driver: bridge
```

---

## Key Concepts Summary — Day 7

### Docker Hub Workflow

```
Build image → Tag with username → Push to Hub → Pull anywhere

docker build -t myapp:v1 .
docker tag myapp:v1 karthiveer/myapp:v1
docker push karthiveer/myapp:v1
# On any server in world:
docker pull karthiveer/myapp:v1
docker run -d -p 80:80 karthiveer/myapp:v1
```

### Docker Networking Rules

```
Same network = can communicate by name ✅
Different network = completely isolated ✅
No network specified = bridge network (default) ✅
host network = uses server's network directly ⚠️
```

### Volume Lifecycle

```
docker volume create → exists independently
container uses volume → data written
container deleted → VOLUME SURVIVES
new container + same volume → data still there
docker volume rm → data permanently deleted
```

---

## Struggles and Solutions — Day 7

### Struggle 1 — Docker Push DNS Error with Capital Username
**Error:**
```
dial tcp: lookup Karthiveer on 127.0.0.53:53: server misbehaving
```
**Cause:** Docker Hub username must be all lowercase.
Capital K in `Karthiveer` treated as hostname not username.

**Fix:**
```bash
docker rmi Karthiveer/karthiveer-app:v1
docker tag karthiveer-app:v1 karthiveer/karthiveer-app:v1
docker push karthiveer/karthiveer-app:v1
```

**Lesson:** Docker Hub, DockerHub registry URLs, image names — always lowercase.

---

## Your Docker Hub — Live!

```
Profile: hub.docker.com/u/karthiveer
Image: hub.docker.com/r/karthiveer/karthiveer-app

Anyone can run your app with:
docker pull karthiveer/karthiveer-app:v1
docker run -d -p 80:80 karthiveer/karthiveer-app:v1
```

---

## Day 7 XP Earned

| Mission | Topic | XP |
|---------|-------|-----|
| Mission 1 | Docker Hub | 320 XP |
| Mission 2 | Docker Networking | 320 XP |
| Mission 3 | Docker Volumes | 360 XP |
| Mission 4 | Docker Compose | 360 XP |
| **Day 7 Total** | | **680 XP** |

---

## Cumulative XP — 7 Days

| Day | Topic | XP |
|-----|-------|-----|
| Day 1 | Linux CLI | 555 XP |
| Day 2 | grep, Pipes, GitHub | 480 XP |
| Day 3 | AWS EC2, Nginx | 560 XP |
| Day 4 | Env Vars, Cron, AWS CLI, IAM | 560 XP |
| Day 5 | Bash Scripts, S3, Monitoring, Nginx | 600 XP |
| Day 6 | Docker Fundamentals | 640 XP |
| Day 7 | Docker Advanced | 680 XP |
| **Total** | | **4,075 XP** |

---

## Day 8 Preview — Terraform! 🏗️

| Topic | What You Will Build |
|-------|-------------------|
| What is Terraform | Infrastructure as Code concepts |
| Install Terraform | Setup on your Mac |
| First Terraform config | Deploy AWS resource with code |
| Variables and outputs | Make configs reusable |
| Terraform state | How Terraform tracks infrastructure |

Terraform is the **second most demanded skill** after Docker in Finnish DevOps jobs.
After Day 8 you can deploy entire AWS infrastructure with code — no clicking in browser!

---

## LinkedIn Milestone Post

> Week 1 complete on my Finland DevOps journey 🇫🇮
>
> In 7 days I went from:
> "What is pwd?" → Running Docker containers in Stockholm
>
> This week:
> — Linux CLI mastered
> — AWS EC2 server running in eu-north-1
> — Custom Docker image on Docker Hub
> — Multi-container apps with Docker Compose
> — 4,075 XP earned
>
> My image is live at:
> hub.docker.com/r/karthiveer/karthiveer-app
>
> Anyone can pull and run it anywhere in the world.
> That includes Helsinki. 🎯
>
> Chennai → Helsinki 🇫🇮
> github.com/Karthiveer
>
> #Docker #DevOps #AWS #Linux #Finland #LearningInPublic

---

## Push to GitHub

```bash
cd ~/finland-journey/linux
git add .
git commit -m "Day 7 - Docker Hub, Networking, Volumes, Compose complete"
git push
```

---

*Finland DevOps Journey — Karthiveer*
*github.com/Karthiveer*
*Chennai → Helsinki 🇫🇮*
*Day 7 completed: July 7, 2026*