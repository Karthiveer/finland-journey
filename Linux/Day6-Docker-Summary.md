# Day 6 Docker Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer
**Date:** July 6, 2026

---

## What You Accomplished on Day 6

- Installed Docker Engine officially on Stockholm server
- Ran first container — hello-world
- Pulled images from Docker Hub
- Ran Nginx inside a Docker container
- Built your own custom Docker image with your webpage
- Wrote your first Dockerfile from scratch
- Ran multiple containers simultaneously
- Monitored container resource usage with docker stats
- Managed full container lifecycle — stop, start, restart, remove
- Cleaned up Docker resources with system prune

---

## READY-MADE PROMPT FOR NEXT AI SESSION

Copy and paste this into any AI platform to continue from Day 7:

---

Hi! I am Karthiveer from Chennai, India. I am on a 6-month focused journey to get a Cloud DevOps job in Finland (Helsinki). Please continue my day-by-day learning from Day 7.

COMPLETED SO FAR:
- Day 1: Linux CLI — Navigation, Files, Permissions, Processes, Bash Scripting
- Day 2: grep, find, pipes, networking, GitHub push
- Day 3: AWS EC2 launch, SSH, Nginx web server, Security Groups, live webpage
- Day 4: Environment variables, Cron jobs, AWS CLI v2, AWS IAM basics
- Day 5: Advanced Bash scripting, AWS S3 storage, System monitoring, Nginx advanced config
- Day 6: Docker install, images, containers, Dockerfile, custom image build, container management

CONTINUE FROM: Day 7 — Docker Compose, pushing image to Docker Hub, Docker networking, multi-container apps

MY COMPLETE SETUP:
- MacBook Air (Apple Silicon, zsh shell)
- Homebrew 6.0.5, Git 2.50.1 installed
- SSH shortcut: ssh finland-server
- AWS EC2: finland-devops-server, Elastic IP configured, eu-north-1 Stockholm
- Ubuntu 24.04 LTS, t3.micro free tier
- Nginx running on port 80 — personal webpage live
- Docker installed and working — ubuntu user in docker group
- Custom image built: karthiveer-app:v1
- Scripts folder: ~/Scripts/ with health-check.sh, backup.sh, monitor.sh
- S3 bucket: karthiveer-finland-1783147889
- GitHub: github.com/Karthiveer/finland-journey
- Total XP earned: 3,395 XP across 6 days

LEARNING STYLE: Gamified with XP points and levels, theory first (2-3 mins), then hands-on terminal practice, screenshot errors for help, daily GitHub push.

Please start Day 7 with a quick recap of Day 6 then launch the gamified missions with interactive game widget!

---

END OF PROMPT

---

## Mission 1 — Install Docker

### Installation Commands

```bash
# Step 1 — Update packages
sudo apt update

# Step 2 — Install dependencies
sudo apt install ca-certificates curl gnupg -y

# Step 3 — Create keyrings directory
sudo install -m 0755 -d /etc/apt/keyrings

# Step 4 — Add Docker GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Step 5 — Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Step 6 — Update with Docker repo
sudo apt update

# Step 7 — Install Docker Engine
sudo apt install docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin -y

# Step 8 — Verify Docker running
sudo systemctl status docker

# Step 9 — Run first container
sudo docker run hello-world
```

### What Each Component Does

| Component | Purpose |
|-----------|---------|
| docker-ce | Docker Engine — the core |
| docker-ce-cli | Command line interface |
| containerd.io | Container runtime |
| docker-buildx-plugin | Build images for multiple platforms |
| docker-compose-plugin | Run multi-container apps |

---

## Mission 2 — Docker Fundamentals

### Add User to Docker Group

```bash
# Add ubuntu user to docker group
sudo usermod -aG docker ubuntu

# Exit and reconnect for change to take effect
exit
ssh finland-server

# Verify — should work without sudo now
docker --version
```

### Core Docker Commands

```bash
# Pull image from Docker Hub
docker pull nginx

# List downloaded images
docker images

# Run container in background
# -d = detached (background)
# -p = port mapping (host:container)
# --name = give it a name
docker run -d -p 8081:80 --name my-nginx nginx

# See running containers
docker ps

# See ALL containers including stopped
docker ps -a

# Test containerised Nginx
curl http://localhost:8081

# See container logs
docker logs my-nginx

# Go inside running container
docker exec -it my-nginx bash
# Type exit to come back out
```

### Port Mapping Explained

```
docker run -d -p 8081:80 nginx

Host port 8081 → Container port 80

You access: http://server-ip:8081
Container hears it on: port 80 internally

This is how multiple containers run on same server
Each gets different host port
```

---

## Mission 3 — Build Your Own Docker Image

### Your App Files

#### index.html
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Karthiveer Docker App</title>
  <style>
    body{background:#0F172A;color:#E2E8F0;font-family:Arial;
         text-align:center;padding:60px}
    h1{color:#3B82F6;font-size:40px}
    h2{color:#10B981}
    p{color:#94A3B8;font-size:18px}
    .badge{background:#1E293B;border:1px solid #3B82F6;
           padding:8px 20px;border-radius:999px;
           display:inline-block;margin:8px;color:#60A5FA}
  </style>
</head>
<body>
  <h1>Karthiveer 🐳</h1>
  <h2>Running inside a Docker Container!</h2>
  <p>Deployed from Stockholm Server — eu-north-1</p>
  <p>Chennai → Helsinki 🇫🇮</p>
  <div class="badge">Docker</div>
  <div class="badge">AWS EC2</div>
  <div class="badge">Nginx</div>
  <div class="badge">Linux</div>
</body>
</html>
```

#### Dockerfile
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Dockerfile Instructions Explained

| Instruction | Meaning |
|-------------|---------|
| FROM nginx:alpine | Start with official Nginx on Alpine Linux (tiny OS) |
| COPY index.html /usr/share/nginx/html/ | Copy your file into container |
| EXPOSE 80 | Document that container uses port 80 |
| CMD [...] | Command to run when container starts |

### Build and Run Commands

```bash
# Create app folder
mkdir ~/myapp && cd ~/myapp

# Build image — . means use current folder
# -t = tag/name your image
docker build -t karthiveer-app:v1 .

# Run your custom container
docker run -d -p 8082:80 --name karthiveer-container karthiveer-app:v1

# Test it
curl http://localhost:8082

# See both containers running
docker ps
```

### Image vs Container — Key Difference

```
Image = Blueprint (like a class in programming)
        Stored on disk
        Cannot be modified while running
        One image → infinite containers

Container = Running instance (like an object)
            Uses RAM
            Can be stopped and started
            Isolated from other containers
```

---

## Mission 4 — Docker Management

### Full Container Lifecycle

```bash
# See live resource usage — Ctrl+C to exit
docker stats

# Stop container gracefully
docker stop karthiveer-container

# Start stopped container
docker start karthiveer-container

# Restart container
docker restart my-nginx

# Stop multiple containers at once
docker stop my-nginx karthiveer-container

# Remove stopped containers
docker rm my-nginx karthiveer-container

# Remove an image
docker rmi nginx

# See remaining images
docker images

# Clean ALL unused Docker resources
docker system prune
# Type y to confirm
```

### Docker Stats Output Explained

```
CONTAINER ID  NAME                  CPU%   MEM USAGE/LIMIT
c5410452871b  karthiveer-container  0.00%  3.199MiB/911.3MiB
240114b45550  my-nginx              0.00%  6.52MiB/911.3MiB

CPU% — how much processor the container uses
MEM USAGE — how much RAM the container uses
MEM LIMIT — maximum RAM allowed
NET I/O — network traffic in and out
BLOCK I/O — disk read and write activity
PIDS — number of processes inside container
```

### Container Resource Monitoring — What to Watch

| Metric | Normal | Warning |
|--------|--------|---------|
| CPU% | 0-50% | Above 80% |
| MEM% | 0-60% | Above 85% |
| NET I/O | Steady | Sudden spike |

---

## Key Docker Concepts Learned

### Docker Architecture

```
Docker Hub (internet)
    ↓ docker pull
Docker Images (local disk)
    ↓ docker run
Docker Containers (running in memory)
    ↓ docker stop
Stopped Containers (still on disk)
    ↓ docker rm
Deleted (gone)
```

### Why Docker is #1 DevOps Skill in Finland

```
Without Docker:              With Docker:
"Works on my machine"        Works everywhere
Manual server setup          docker run — instant
Different versions clash     Each container isolated
Scale = buy more servers     Scale = docker run x100
Deploy takes hours           Deploy takes seconds
```

### Common Docker Workflow at Finnish Companies

```bash
# Developer builds image
docker build -t myapp:v2 .

# Test locally
docker run -d -p 8080:80 myapp:v2

# Push to registry
docker push registry/myapp:v2

# Deploy to production server
docker pull registry/myapp:v2
docker stop myapp-old
docker run -d -p 80:80 --name myapp myapp:v2
```

---

## Your Docker Setup — Current State

```
Stockholm Server (eu-north-1)
├── Docker Engine installed ✅
├── ubuntu user in docker group ✅
├── Images:
│   └── karthiveer-app:v1 (your custom image)
├── ~/myapp/
│   ├── index.html
│   └── Dockerfile
└── Containers: cleaned up with system prune ✅
```

---

## Day 6 XP Earned

| Mission | Topic | XP |
|---------|-------|-----|
| Mission 1 | Install Docker | 315 XP |
| Mission 2 | Docker Fundamentals | 400 XP |
| Mission 3 | Build Custom Image | 400 XP |
| Mission 4 | Docker Management | 350 XP |
| **Day 6 Total** | | **640 XP** |

---

## Cumulative XP — 6 Days

| Day | Topic | XP |
|-----|-------|-----|
| Day 1 | Linux CLI | 555 XP |
| Day 2 | grep, Pipes, GitHub | 480 XP |
| Day 3 | AWS EC2, Nginx | 560 XP |
| Day 4 | Env Vars, Cron, AWS CLI, IAM | 560 XP |
| Day 5 | Bash Scripts, S3, Monitoring, Nginx | 600 XP |
| Day 6 | Docker | 640 XP |
| **Total** | | **3,395 XP** |

---

## Day 7 Preview — Docker Compose + Docker Hub

| Topic | What You Will Build |
|-------|-------------------|
| Docker Hub | Push your image to the world |
| Docker Compose | Run multi-container apps with one command |
| Docker Networking | Containers talking to each other |
| Docker Volumes | Persist data between container restarts |
| Real Project | Web app + Database running in Docker |

---

## LinkedIn Milestone Post — Post This!

> Day 6 complete — Docker fundamentals mastered 🐳
>
> Today on my Finland DevOps journey:
> — Installed Docker on my AWS Stockholm server
> — Built my first custom Docker image from scratch
> — Ran multiple containers simultaneously
> — Monitored container resource usage live
>
> Two containers running on one server using less than 10MB RAM combined.
> That is why Finnish companies love Docker.
>
> Chennai → Helsinki 🇫🇮
> github.com/Karthiveer
>
> #Docker #DevOps #AWS #Finland #LearningInPublic #CloudComputing

---

## Push to GitHub

```bash
cd ~/finland-journey/linux
git add .
git commit -m "Day 6 - Docker fundamentals complete"
git push
```

---

*Finland DevOps Journey — Karthiveer*
*github.com/Karthiveer*
*Chennai → Helsinki 🇫🇮*
*Day 6 completed: July 6, 2026*
