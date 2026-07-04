# Day 5 DevOps Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer
**Date:** July 4, 2026

---

## What You Accomplished on Day 5

- Wrote 3 real automation bash scripts from scratch
- Created server health check script
- Created automated backup script with date-stamped files
- Created disk monitoring script with threshold alerts
- Mastered AWS S3 — created bucket, uploaded files, synced folders
- Learned professional system monitoring commands
- Configured Nginx advanced setup — multiple site configs
- Created status endpoint on port 8080
- Learned sudo tee for writing protected system files
- Fixed case sensitivity issue — Linux is case sensitive

---

## READY-MADE PROMPT FOR NEXT AI SESSION

Copy and paste this into any AI platform to continue from Day 6:

---

Hi! I am Karthiveer from Chennai, India. I am on a 6-month focused journey to get a Cloud DevOps job in Finland (Helsinki). Please continue my day-by-day learning from Day 6.

COMPLETED SO FAR:
- Day 1: Linux CLI — Navigation, Files, Permissions, Processes, Bash Scripting
- Day 2: grep, find, pipes, networking, GitHub push
- Day 3: AWS EC2 launch, SSH, Nginx web server, Security Groups, live webpage
- Day 4: Environment variables, Cron jobs, AWS CLI v2, AWS IAM basics
- Day 5: Advanced Bash scripting, AWS S3 storage, System monitoring, Nginx advanced config

CONTINUE FROM: Day 6 — Docker fundamentals, containerization concepts, Docker installation on EC2

MY COMPLETE SETUP:
- MacBook Air (Apple Silicon, zsh shell)
- Homebrew 6.0.5, Git 2.50.1 installed
- SSH shortcut: ssh finland-server
- AWS EC2: finland-devops-server, Elastic IP configured, eu-north-1 Stockholm
- Ubuntu 24.04 LTS, t3.micro free tier
- Nginx running with advanced config — port 80 and port 8080 status endpoint
- AWS CLI v2 installed and configured on server
- S3 bucket: karthiveer-finland-1783147889 with scripts backed up
- Scripts folder: ~/Scripts/ with health-check.sh, backup.sh, monitor.sh
- GitHub: github.com/Karthiveer/finland-journey
- Total XP earned: 2,755 XP across 5 days

LEARNING STYLE: Gamified with XP points and levels, theory first (2-3 mins), then hands-on terminal practice, screenshot errors for help, daily GitHub push.

Please start Day 6 with a quick recap of Day 5 then launch the gamified missions with interactive game widget!

---

END OF PROMPT

---

## Mission 1 — Advanced Bash Scripting

### Scripts Created on Stockholm Server

#### Health Check Script — ~/Scripts/health-check.sh
```bash
#!/bin/bash
echo "=== Server Health Check ==="
echo "Date: $(date)"
echo "Uptime: $(uptime)"
echo "Disk: $(df -h / | tail -1 | awk '{print $5}') used"
echo "Memory: $(free -h | grep Mem | awk '{print $3}') used"
echo "Nginx: $(systemctl is-active nginx)"
echo "========================"
```

#### Backup Script — ~/Scripts/backup.sh
```bash
#!/bin/bash
BACKUP_DIR="/home/ubuntu/backups"
DATE=$(date +%Y-%m-%d)
mkdir -p $BACKUP_DIR
cp /var/www/html/index.html $BACKUP_DIR/index-$DATE.html
echo "Backup completed: $BACKUP_DIR/index-$DATE.html"
```

#### Disk Monitor Script — ~/Scripts/monitor.sh
```bash
#!/bin/bash
THRESHOLD=80
USAGE=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')
if [ $USAGE -gt $THRESHOLD ]; then
  echo "WARNING: Disk usage is $USAGE% - above threshold!"
else
  echo "OK: Disk usage is $USAGE% - within limits"
fi
```

### Key Bash Script Commands

```bash
# Create scripts folder
mkdir ~/Scripts && cd ~/Scripts

# Make script executable
chmod +x scriptname.sh

# Run a script
./scriptname.sh

# Run two commands — second only runs if first succeeds
chmod +x backup.sh && ./backup.sh

# Check exit code — 0 means success
echo $?

# Verify backup files created
ls ~/backups/
```

### Bash Script Concepts Learned

```bash
# Variables
NAME="Karthiveer"
DATE=$(date +%Y-%m-%d)    # Command output stored in variable

# Conditions
if [ $USAGE -gt $THRESHOLD ]; then
  echo "Warning!"
else
  echo "OK"
fi

# Useful awk commands
df -h / | tail -1 | awk '{print $5}'    # Get 5th column
free -h | grep Mem | awk '{print $3}'   # Get memory used

# Remove % from output
echo "80%" | sed 's/%//'    # Output: 80
```

---

## Mission 2 — AWS S3 Storage

### What is S3?
Simple Storage Service — store any file in the cloud.
Accessible from anywhere, scales infinitely, extremely reliable.
Finnish companies use S3 for backups, website files, logs, data pipelines.

### Commands Practiced

```bash
# List all buckets
aws s3 ls

# Create a bucket — must be globally unique name
aws s3 mb s3://karthiveer-finland-$(date +%s) --region eu-north-1

# Create test file
echo 'Hello from Stockholm server to S3!' > test-file.txt

# Upload single file
aws s3 cp test-file.txt s3://YOUR-BUCKET-NAME/test-file.txt

# Upload script to S3
aws s3 cp ~/Scripts/health-check.sh s3://YOUR-BUCKET-NAME/scripts/health-check.sh

# List files in bucket
aws s3 ls s3://YOUR-BUCKET-NAME/

# Sync entire folder to S3 — only uploads changed files
aws s3 sync ~/Scripts/ s3://YOUR-BUCKET-NAME/scripts/

# Download file from S3
aws s3 cp s3://YOUR-BUCKET-NAME/test-file.txt downloaded-file.txt

# Delete file from S3
aws s3 rm s3://YOUR-BUCKET-NAME/test-file.txt

# Delete entire bucket
aws s3 rb s3://YOUR-BUCKET-NAME --force
```

### S3 cp vs S3 sync

```bash
# cp — copies everything every time
aws s3 cp ~/Scripts/ s3://bucket/scripts/ --recursive

# sync — only uploads NEW or CHANGED files
aws s3 sync ~/Scripts/ s3://bucket/scripts/
# Much more efficient for regular backups
```

### Your S3 Bucket Details
```
Bucket name: karthiveer-finland-1783147889
Region: eu-north-1 Stockholm
Contents: scripts/health-check.sh
          scripts/backup.sh
          scripts/monitor.sh
```

---

## Mission 3 — System Monitoring

### Commands Practiced

```bash
# Live process monitor — press Q to quit
top

# Better process monitor with colours
sudo apt install htop -y
htop

# Disk space on all drives
df -h

# RAM memory usage
free -h

# All open ports and listening services
netstat -tulpn

# Watch live web traffic in real time
sudo tail -f /var/log/nginx/access.log
# Press Ctrl+C to stop

# Watch live error log
sudo tail -f /var/log/nginx/error.log

# Top 10 CPU consuming processes
ps aux --sort=-%cpu | head -10

# Top 10 memory consuming processes
ps aux --sort=-%mem | head -10

# Check specific service status
sudo systemctl status nginx
sudo systemctl status cron
```

### Understanding top Output

```
top - 09:06:44 up 21:14, load average: 0.00, 0.00, 0.00
Tasks: 110 total, 1 running, 109 sleeping
%Cpu(s): 0.3 us, 0.0 sy — CPU usage
MiB Mem: 957 total, 200 free, 400 used

PID   USER    %CPU  %MEM  COMMAND
618   root    0.0   0.5   nginx
1234  ubuntu  0.0   0.2   bash
```

### netstat Flags Explained

```bash
netstat -tulpn
# -t = TCP connections
# -u = UDP connections
# -l = Listening ports only
# -p = Show process name
# -n = Numeric IPs (faster)
```

---

## Mission 4 — Nginx Advanced Config

### Key Lesson — sudo tee vs sudo cat

```bash
# WRONG — Permission denied for system files
sudo cat > /etc/nginx/sites-available/status << 'EOF'
content here
EOF

# CORRECT — Use tee for system files
sudo tee /etc/nginx/sites-available/status << 'EOF'
content here
EOF
```

### Commands Practiced

```bash
# Test Nginx config — always before reloading
sudo nginx -t
# Output: nginx: configuration file test is successful

# Read main config
sudo cat /etc/nginx/nginx.conf

# List available sites
sudo ls /etc/nginx/sites-available/

# List enabled sites
sudo ls /etc/nginx/sites-enabled/

# Read current site config
sudo cat /etc/nginx/sites-available/default

# Create new site config using tee
sudo tee /etc/nginx/sites-available/status << 'EOF'
server {
    listen 8080;
    location / {
        return 200 'Karthiveer Finland Server - All Systems OK';
        add_header Content-Type text/plain;
    }
}
EOF

# Enable site — create symlink
sudo ln -s /etc/nginx/sites-available/status \
           /etc/nginx/sites-enabled/status

# Remove broken symlink
sudo rm /etc/nginx/sites-enabled/status

# Test config
sudo nginx -t

# Reload without dropping connections
sudo systemctl reload nginx

# Test status endpoint
curl http://localhost:8080
# Output: Karthiveer Finland Server - All Systems OK
```

### sites-available vs sites-enabled

```
/etc/nginx/sites-available/    = All possible configs stored here
/etc/nginx/sites-enabled/      = Symlinks to active configs

# Enable a site
sudo ln -s /etc/nginx/sites-available/mysite \
           /etc/nginx/sites-enabled/mysite

# Disable a site (remove symlink, keep config)
sudo rm /etc/nginx/sites-enabled/mysite

# This way you can quickly enable/disable sites
# without deleting config files
```

### reload vs restart

```bash
sudo systemctl reload nginx    # Applies new config, keeps connections alive ✅
sudo systemctl restart nginx   # Full restart, drops all connections ⚠️
# Always use reload in production — never drops live traffic
```

---

## Struggles and Solutions — Day 5

### Struggle 1 — Case Sensitivity (Scripts vs scripts)
**Error:**
```
The user-provided path /home/ubuntu/scripts/health-check.sh does not exist
```
**Cause:** Folder created as `Scripts` (capital S) but command used `scripts` (lowercase)

**Fix:**
```bash
# Always check exact folder name first
ls ~
# Use exact case in commands
aws s3 cp ~/Scripts/health-check.sh s3://bucket/scripts/
```
**Lesson:** Linux is 100% case sensitive. `Scripts` and `scripts` are completely different folders.

---

### Struggle 2 — Path Duplication with ~ and /home/ubuntu
**Error:**
```
path /home/ubuntu/home/ubuntu/Scripts does not exist
```
**Cause:** Used `~/home/ubuntu/Scripts` which duplicated the path

**Fix:**
```bash
# ~ already means /home/ubuntu
~/Scripts        ✅ = /home/ubuntu/Scripts
/home/ubuntu/Scripts  ✅ = /home/ubuntu/Scripts
~/home/ubuntu/Scripts ❌ = /home/ubuntu/home/ubuntu/Scripts (WRONG)
```

---

### Struggle 3 — Permission Denied Writing Nginx Config
**Error:**
```
-bash: /etc/nginx/sites-available/status: Permission denied
```
**Cause:** `sudo cat >` doesn't pass sudo to the redirect operator

**Fix:**
```bash
# Use sudo tee instead
sudo tee /etc/nginx/sites-available/status << 'EOF'
server block content
EOF
```
**Lesson:** For writing to root-owned files, always use `sudo tee` — not `sudo cat >`

---

### Struggle 4 — Nginx Config Test Failed After Broken Symlink
**Error:**
```
open() "/etc/nginx/sites-enabled/status" failed (2: No such file or directory)
```
**Cause:** Symlink was created pointing to a file that didn't exist yet

**Fix:**
```bash
# Remove broken symlink
sudo rm /etc/nginx/sites-enabled/status
# Create config file first using tee
# Then create symlink
sudo ln -s /etc/nginx/sites-available/status \
           /etc/nginx/sites-enabled/status
```
**Lesson:** Always create the actual file BEFORE creating the symlink to it.

---

## Your Stockholm Server — Current State

```
finland-devops-server (eu-north-1 Stockholm)
├── Nginx running on port 80 — your personal webpage
├── Nginx status endpoint on port 8080
├── ~/Scripts/
│   ├── health-check.sh (server health automation)
│   ├── backup.sh (webpage backup automation)
│   └── monitor.sh (disk monitoring automation)
├── ~/backups/
│   └── index-2026-07-04.html (today's backup)
└── AWS CLI configured — control AWS from terminal
```

---

## S3 Bucket — Current State

```
s3://karthiveer-finland-1783147889/
└── scripts/
    ├── health-check.sh
    ├── backup.sh
    └── monitor.sh
```

---

## Add Backup Script to Cron — Recommended

Run this to automate daily backups:

```bash
crontab -e
```

Add this line:
```
0 2 * * * /home/ubuntu/Scripts/backup.sh >> /home/ubuntu/backups/backup.log 2>&1
```

This runs your backup script every day at 2am UTC automatically.

---

## Daily GitHub Push

```bash
# On your Mac
cd ~/finland-journey/linux
git add .
git commit -m "Day 5 - Bash scripting, S3, monitoring, Nginx advanced config"
git push
```

---

## Day 6 Preview — Docker! 🐳

| Topic | Why It Matters |
|-------|---------------|
| What is Docker | Most important DevOps tool after Linux |
| Install Docker on EC2 | Set up container environment |
| Docker images and containers | Build and run apps in containers |
| Dockerfile | Package your app for anywhere |
| Docker Hub | Share containers with the world |

Docker is the **most demanded skill** in Finnish DevOps job postings.
After Day 6 you will understand containers — the technology powering modern Finnish tech companies.

---

## Your Overall Progress — 5 Days

| Skill | Level |
|-------|-------|
| Linux CLI | ⭐⭐⭐⭐⭐ Expert |
| Git and GitHub | ⭐⭐⭐⭐ Strong |
| Bash Scripting | ⭐⭐⭐⭐ Strong |
| AWS EC2 | ⭐⭐⭐⭐ Strong |
| AWS CLI | ⭐⭐⭐ Good |
| AWS S3 | ⭐⭐⭐ Good |
| AWS IAM | ⭐⭐⭐ Good |
| Nginx | ⭐⭐⭐ Good |
| System Monitoring | ⭐⭐⭐ Good |
| Docker | ⭐ Day 6 next! |
| Terraform | ⭐ Month 4 |
| Kubernetes | ⭐ Month 5 |

---

## Total XP Earned — 5 Days

| Day | Topic | XP |
|-----|-------|-----|
| Day 1 | Linux CLI | 555 XP |
| Day 2 | grep, Pipes, GitHub | 480 XP |
| Day 3 | AWS EC2, Nginx | 560 XP |
| Day 4 | Env Vars, Cron, AWS CLI, IAM | 560 XP |
| Day 5 | Bash Scripts, S3, Monitoring, Nginx Advanced | 600 XP |
| **Total** | | **2,755 XP** |

---

*Finland DevOps Journey — Karthiveer*
*github.com/Karthiveer*
*Chennai → Helsinki 🇫🇮*
*Day 5 completed: July 4, 2026*