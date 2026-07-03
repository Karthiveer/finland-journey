# Day 4 DevOps Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer
**Date:** July 3, 2026

---

## What You Accomplished on Day 4

- Learned environment variables — how servers store config and secrets
- Created custom environment variables on Stockholm server
- Set up first cron job — server logs heartbeat every minute automatically
- Installed AWS CLI v2 directly from Amazon (official method)
- Configured AWS CLI with access keys
- Controlled AWS infrastructure from terminal — no browser needed
- Listed EC2 instances, S3 buckets, IAM users from command line
- Generated AWS credential security report
- Understood AWS IAM — users, groups, policies, roles

---

## READY-MADE PROMPT FOR NEXT AI SESSION

Copy and paste this into any AI platform to continue from Day 5:

---

Hi! I am Karthiveer from Chennai, India. I am on a 6-month focused journey to get a Cloud DevOps job in Finland (Helsinki). Please continue my day-by-day learning from Day 5.

COMPLETED SO FAR:
- Day 1: Linux CLI — Navigation, Files, Permissions, Processes, Bash Scripting
- Day 2: grep, find, pipes, networking, GitHub push
- Day 3: AWS EC2 launch, SSH, Nginx web server, Security Groups, live webpage
- Day 4: Environment variables, Cron jobs, AWS CLI v2, AWS IAM basics

CONTINUE FROM: Day 5 — Advanced Bash Scripting, AWS S3, VPC basics, Nginx config, System monitoring

MY SETUP:
- MacBook Air (Apple Silicon, zsh shell)
- Homebrew 6.0.5, Git 2.50.1 installed
- SSH shortcut: ssh finland-server connects to AWS EC2
- AWS EC2: finland-devops-server, IP 16.171.138.163, eu-north-1 Stockholm
- Ubuntu 24.04 LTS, t3.micro free tier
- Nginx running, live webpage at http://16.171.138.163
- AWS CLI v2 installed and configured on server
- GitHub: github.com/Karthiveer/finland-journey
- Total XP earned: 2,155 XP across 4 days

LEARNING STYLE: Gamified with XP points and levels, theory first (2-3 mins), then hands-on terminal practice, screenshot errors for help, daily GitHub push.

Please start Day 5 with a quick recap then launch the gamified missions!

---

END OF PROMPT

---

## Mission 1 — Environment Variables

### What Are Environment Variables?
Variables stored in the server's memory that applications read automatically.
Think of them as sticky notes the server reads before starting any program.

### Commands Practiced

```bash
# Read built-in environment variables
echo $HOME          # /home/ubuntu — your home folder
echo $USER          # ubuntu — current username
echo $PATH          # List of folders Linux searches for commands

# Create your own environment variables
export APP_PORT=8080
export DB_HOST=localhost
export ENVIRONMENT=production

# Read your variables back
echo $APP_PORT      # 8080
echo $DB_HOST       # localhost

# List all environment variables
env

# Filter specific variables
env | grep APP

# Count total variables
env | grep -c ''
```

### Real World Use in Finland

```bash
# How Finnish companies configure apps
export DATABASE_URL=postgresql://user:pass@db.helsinki.fi:5432/app
export API_KEY=abc123secretkey
export APP_PORT=8080
export LOG_LEVEL=info

# App reads these automatically — no passwords in code
node server.js
```

### Important Rules
- Never hardcode passwords in scripts — always use environment variables
- Never commit .env files to GitHub — add to .gitignore
- Production vs development environments use different variable values

---

## Mission 2 — Cron Jobs

### What is a Cron Job?
A scheduled task that runs automatically at specified times.
Like setting an alarm — but for your server.

### Cron Syntax

```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0=Sunday, 6=Saturday)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)

* = every possible value
```

### Commands Practiced

```bash
# Check current server time (servers run in UTC)
date

# List current cron jobs
crontab -l

# Open cron editor
crontab -e
# Select 1 for nano editor

# Inside crontab — add this line
* * * * * echo 'Server is alive' >> /tmp/heartbeat.log

# Save: Control+O → Enter → Control+X

# Verify job was saved
crontab -l

# Wait 1 minute then check log
cat /tmp/heartbeat.log
```

### Real World Cron Examples

```bash
# Every day at midnight — backup database
0 0 * * * /home/ubuntu/backup-db.sh

# Every Monday at 9am — weekly report
0 9 * * 1 /home/ubuntu/weekly-report.sh

# Every 5 minutes — health check
*/5 * * * * curl -s http://localhost > /dev/null

# Every day at 6am Finnish time (UTC+3 = 3am UTC)
0 3 * * * /home/ubuntu/morning-backup.sh

# Every hour — clear temp files
0 * * * * rm -rf /tmp/cache/*
```

### Important Notes
- Server timezone is UTC — Finland is UTC+3
- Always use full paths in cron commands
- Test commands manually before adding to crontab
- Check /tmp/heartbeat.log after 1 minute to verify job runs

---

## Mission 3 — AWS CLI v2 Setup

### Why AWS CLI?

```
Before AWS CLI:
Browser → AWS Console → Click EC2 → Click Instances → See servers
(Slow, not scriptable, requires GUI)

After AWS CLI:
Terminal → one command → see everything instantly
(Fast, scriptable, works inside automation)
```

### Installation (Official Method — Ubuntu)

```bash
# Download AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" \
  -o "awscliv2.zip"

# Install unzip
sudo apt install unzip -y

# Unzip installer
unzip awscliv2.zip

# Run installer
sudo ./aws/install

# Verify installation
aws --version
# Output: aws-cli/2.x.x Python/3.x.x Linux/x86_64

# Clean up
rm -rf awscliv2.zip aws/
```

### Configuration

```bash
aws configure
# AWS Access Key ID: (from AWS Console → Security Credentials)
# AWS Secret Access Key: (from AWS Console)
# Default region name: eu-north-1
# Default output format: json
```

### Commands Practiced

```bash
# List your EC2 instances
aws ec2 describe-instances --region eu-north-1

# Clean table output
aws ec2 describe-instances \
  --region eu-north-1 \
  --query 'Reservations[].Instances[].{Name:Tags[0].Value,State:State.Name,IP:PublicIpAddress}' \
  --output table

# Check instance state only
aws ec2 describe-instances \
  --region eu-north-1 \
  --query 'Reservations[].Instances[].State.Name'

# List S3 buckets
aws s3 ls

# List all regions
aws ec2 describe-regions --output table
```

### How to Get Access Keys

1. Go to AWS Console in browser
2. Click account name top right
3. Click Security credentials
4. Scroll to Access keys section
5. Click Create access key
6. Choose CLI use case
7. Copy both keys — save safely in Notes app
8. Never commit keys to GitHub

---

## Mission 4 — AWS IAM Basics

### What is IAM?
Identity and Access Management — controls WHO can do WHAT on your AWS account.
The security foundation of everything in AWS.

### Key IAM Concepts

```
IAM User    = A person or application with AWS access
IAM Group   = Collection of users sharing same permissions
IAM Role    = Temporary permissions for services (EC2, Lambda)
IAM Policy  = JSON document defining exact permissions
```

### Commands Practiced

```bash
# See your current IAM user
aws iam get-user

# List all IAM users
aws iam list-users

# List all IAM groups
aws iam list-groups

# List custom policies
aws iam list-policies --scope Local

# Check password policy
aws iam get-account-password-policy

# Generate credential report
aws iam generate-credential-report

# Download and read credential report
aws iam get-credential-report \
  --output text \
  --query Content | base64 --decode
```

### IAM Best Practices (Finnish Companies Follow These)

| Practice | Why |
|----------|-----|
| Never use root account for daily work | Root has unlimited power — too risky |
| Create individual IAM users | Audit trail per person |
| Use groups for permissions | Easier to manage at scale |
| Enable MFA on all users | Second layer of security |
| Rotate access keys every 90 days | Limits damage if key leaked |
| Principle of least privilege | Give only permissions needed |
| Never commit access keys to GitHub | Immediate security incident |

### IAM Policy Example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*"
    }
  ]
}
```

This policy allows listing, starting and stopping EC2 — nothing else.
Finnish DevOps engineers write policies like this for each application.

---

## Key Concepts Learned Day 4

### Environment Variables vs Hardcoding

```bash
# BAD — never do this in production
DB_PASSWORD="mysecretpassword123"
mysql -u root -pmysecretpassword123

# GOOD — always use environment variables
export DB_PASSWORD="mysecretpassword123"
mysql -u root -p$DB_PASSWORD
```

### Cron Job Debugging

```bash
# Check if cron service is running
sudo systemctl status cron

# View cron execution logs
grep CRON /var/log/syslog | tail -20

# Test your command manually first
echo 'Server is alive' >> /tmp/heartbeat.log
cat /tmp/heartbeat.log
```

### AWS CLI Output Formats

```bash
# JSON (default) — detailed, machine readable
aws ec2 describe-instances --output json

# Table — human readable, great for quick checks
aws ec2 describe-instances --output table

# Text — simple, good for scripts
aws ec2 describe-instances --output text
```

---

## Daily GitHub Push

```bash
exit  # Exit server first
cd ~/finland-journey
git add .
git commit -m "Day 4 - Environment variables, Cron, AWS CLI, IAM complete"
git push
```

---

## Day 5 Preview

| Topic | Commands | Why It Matters |
|-------|----------|----------------|
| Advanced bash scripting | functions, arguments, error handling | Real automation scripts |
| AWS S3 | aws s3 cp, sync, mb | Store files, backups, static websites |
| VPC basics | subnets, route tables | Cloud networking foundation |
| Nginx configuration | server blocks, reverse proxy | Host multiple websites |
| System monitoring | top, htop, netstat, df | Keep server healthy |

---

## Your Progress — 4 Days In

| Skill | Level |
|-------|-------|
| Linux CLI | ⭐⭐⭐⭐ Strong |
| Git and GitHub | ⭐⭐⭐ Good |
| AWS EC2 | ⭐⭐⭐ Good |
| AWS CLI | ⭐⭐ Building |
| AWS IAM | ⭐⭐ Building |
| Nginx | ⭐⭐ Building |
| Bash Scripting | ⭐⭐ Building |
| Docker | ⭐ Month 4 |
| Terraform | ⭐ Month 4 |
| Kubernetes | ⭐ Month 5 |

---

*Finland DevOps Journey — Karthiveer*
*github.com/Karthiveer*
*Chennai → Helsinki 🇫🇮*
*Day 4 completed: July 3, 2026*
