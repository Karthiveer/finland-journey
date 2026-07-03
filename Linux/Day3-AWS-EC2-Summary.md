# Day 3 Linux Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer
**Date:** July 2, 2026

---

## What You Accomplished on Day 3

- Created AWS Free Tier account from scratch
- Enabled MFA (Multi-Factor Authentication) on root account
- Configured Zero spend budget alert
- Launched first EC2 server in Stockholm (eu-north-1)
- SSH'd into real cloud server from Chennai MacBook
- Created SSH shortcut — ssh finland-server
- Installed Nginx web server on cloud server
- Configured AWS Security Groups (firewall rules)
- Uploaded photo via scp command
- Built live personal webpage visible to entire world
- Understood Private IP vs Public IP difference
- Understood Security Groups vs local firewall

---

## Your AWS Server Details

| Detail | Value |
|--------|-------|
| Server name | finland-devops-server |
| Instance ID | i-00bf77c994894938f |
| Public IP | 16.171.138.163 |
| Private IP | 172.31.46.118 |
| Region | eu-north-1 Stockholm |
| Instance type | t3.micro (free tier) |
| OS | Ubuntu 24.04 LTS |
| SSH user | ubuntu |
| Key file | ~/.ssh/finland-key.pem |
| Web server | Nginx (running) |
| Live URL | http://16.171.138.163 |
| Security Group | Karthiveer_Finland_SG |

---

## SSH Shortcut Created

File location: ~/.ssh/config

```
Host finland-server
    HostName 16.171.138.163
    User ubuntu
    IdentityFile ~/.ssh/finland-key.pem
```

Connect anytime with just:
```bash
ssh finland-server
```

---

## Commands Learned on Day 3

### Launching and Connecting to Server
```bash
# Connect to Stockholm server
ssh -i ~/.ssh/finland-key.pem ubuntu@16.171.138.163

# Using shortcut after config setup
ssh finland-server

# Check who you are on server
whoami
# Output: ubuntu

# Check server details
uname -a

# Check how long server has been running
uptime

# Check disk space on cloud server
df -h

# Check memory usage
free -h
```

### Nginx Web Server Commands
```bash
# Update server packages
sudo apt update

# Install Nginx web server
sudo apt install nginx -y

# Check Nginx is running
sudo systemctl status nginx

# Start Nginx
sudo systemctl start nginx

# Stop Nginx
sudo systemctl stop nginx

# Restart Nginx
sudo systemctl restart nginx

# Test Nginx is responding locally
curl -I http://localhost
# Should show: HTTP/1.1 200 OK

# Edit your webpage
sudo nano /var/www/html/index.html
```

### SCP — Copying Files to Server
```bash
# Copy file from Mac to server home folder
scp ~/Downloads/filename.jpg finland-server:~/filename.jpg

# Then move to web folder with sudo
sudo mv ~/filename.jpg /var/www/html/filename.jpg

# Fix permissions on uploaded file
sudo chmod 644 /var/www/html/filename.jpg

# Verify files in web folder
ls -la /var/www/html/
```

### Security Group Understanding
```bash
# Private IP — only visible inside AWS
172.31.46.118    # Cannot access from browser

# Public IP — visible to entire internet
16.171.138.163   # Use this in browser

# Test webpage from server itself
curl -I http://localhost
```

---

## Private IP vs Public IP — Key Concept

```
Private IP (172.31.x.x)
= Internal address
= Only visible INSIDE AWS network
= Like your home WiFi IP (192.168.x.x)
= Other AWS servers use this to talk to each other
= CANNOT be accessed from outside internet

Public IP (16.171.138.163)
= External address
= Visible to ENTIRE internet
= Anyone in world can reach you via this IP
= Use this in browser to see your webpage
```

Real world analogy:
```
Private IP = Your desk number inside Helsinki office
             Only people inside building know this

Public IP  = The building's street address in Helsinki
             Anyone in Chennai can find it on Google Maps
```

---

## Port Numbers Learned

| Port | Service | Used For |
|------|---------|----------|
| 22 | SSH | Remote server access |
| 80 | HTTP | Web traffic (your webpage) |
| 443 | HTTPS | Secure web traffic |
| 3306 | MySQL | Database |
| 5432 | PostgreSQL | Database |

---

## HTTP Status Codes

| Code | Meaning |
|------|---------|
| 200 OK | Success — server is healthy |
| 301 | Redirect |
| 404 | Not found |
| 500 | Server error |

---

## Your Live Webpage HTML

File location on server: /var/www/html/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Karthiveer - Finland DevOps Journey</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: Arial, sans-serif;
            min-height: 100vh;
            position: relative;
        }
        .bg-photo {
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background-image: url('profile.jpg');
            background-size: cover;
            background-position: center top;
            z-index: 0;
        }
        .bg-overlay {
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(15, 23, 42, 0.82);
            z-index: 1;
        }
        .content {
            position: relative;
            z-index: 2;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 50px 24px;
            text-align: center;
        }
        .name {
            font-size: 48px;
            font-weight: 900;
            letter-spacing: 3px;
            margin-bottom: 8px;
            background: linear-gradient(90deg, #3B82F6, #10B981, #F59E0B);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .role { font-size: 20px; font-weight: 600; color: #94A3B8; margin-bottom: 6px; }
        .location { font-size: 16px; color: #64748B; margin-bottom: 32px; }
        .rainbow-bar {
            width: 250px; height: 4px;
            background: linear-gradient(90deg,#EF4444,#F97316,#EAB308,
                        #22C55E,#3B82F6,#8B5CF6,#EC4899);
            border-radius: 999px;
            margin: 0 auto 36px;
        }
        .love-card {
            background: rgba(255,255,255,0.06);
            border: 1px solid rgba(255,255,255,0.12);
            border-radius: 20px;
            padding: 36px 32px;
            max-width: 520px;
            width: 100%;
            margin-bottom: 32px;
        }
        .heart { font-size: 36px; margin-bottom: 16px; }
        .love-line-1 { font-size: 22px; font-weight: 700; margin-bottom: 14px; }
        .hi { color: #F472B6; }
        .love-line-2 { font-size: 17px; color: #CBD5E1; line-height: 1.8; margin-bottom: 16px; }
        .appu { font-size: 20px; font-weight: 800; }
        .ap1{color:#EF4444} .ap2{color:#F97316} .ap3{color:#EAB308}
        .ap4{color:#22C55E} .ap5{color:#3B82F6} .ap6{color:#8B5CF6}
        .ap7{color:#EC4899} .ap8{color:#14B8A6} .ap9{color:#F43F5E}
        .love-sign { font-size: 18px; font-weight: 700; margin-top: 16px; }
        .saru { color: #F472B6; }
        .always { color: #94A3B8; font-weight: 400; font-size: 15px; }
        .stack { display: flex; flex-wrap: wrap; justify-content: center; gap: 10px; margin-bottom: 24px; }
        .tag { padding: 6px 18px; border-radius: 999px; font-size: 13px; font-weight: 700; }
        .t1{background:#1E3A8A22;color:#60A5FA;border:1px solid #3B82F644}
        .t2{background:#06402022;color:#34D399;border:1px solid #10B98144}
        .t3{background:#78350F22;color:#FBBF24;border:1px solid #F59E0B44}
        .t4{background:#4C1D9522;color:#A78BFA;border:1px solid #8B5CF644}
        .t5{background:#83184622;color:#F9A8D4;border:1px solid #EC489944}
        .t6{background:#16425122;color:#67E8F9;border:1px solid #06B6D444}
        .github { color: #475569; font-size: 13px; margin-bottom: 10px; }
        .github a { color: #3B82F6; text-decoration: none; }
        .server-badge {
            font-size: 12px; color: #334155;
            border: 1px solid #1E293B;
            padding: 6px 16px; border-radius: 999px;
        }
    </style>
</head>
<body>
<div class="bg-photo"></div>
<div class="bg-overlay"></div>
<div class="content">
    <div class="name">Karthiveer</div>
    <div class="role">DevOps Engineer &bull; Finland Journey</div>
    <div class="location">Chennai, India &#8594; Helsinki, Finland &#127477;&#127470;</div>
    <div class="rainbow-bar"></div>
    <div class="love-card">
        <div class="heart">&#10084;&#65039;</div>
        <div class="love-line-1"><span class="hi">Hi Baby</span> &#128149;</div>
        <div class="love-line-2">
            We will have a Happy Family<br>with our little
        </div>
        <div class="appu">
            <span class="ap1">A</span><span class="ap2">p</span>
            <span class="ap3">p</span><span class="ap4">u</span>
            <span class="ap5">k</span><span class="ap6">u</span>
            <span class="ap7">t</span><span class="ap8">t</span>
            <span class="ap9">y</span> &#128522;
        </div>
        <div class="love-sign">
            <span class="always">Always in love,</span><br>
            <span class="saru">Saru &#128149;</span>
        </div>
    </div>
    <div class="rainbow-bar"></div>
    <div class="stack" style="margin-top:24px;">
        <span class="tag t1">Linux</span>
        <span class="tag t2">AWS</span>
        <span class="tag t3">Docker</span>
        <span class="tag t4">Terraform</span>
        <span class="tag t5">Kubernetes</span>
        <span class="tag t6">CI/CD</span>
    </div>
    <div class="github">
        <a href="https://github.com/Karthiveer">github.com/Karthiveer</a>
    </div>
    <div class="server-badge">
        &#9729; AWS EC2 &bull; eu-north-1 Stockholm &bull; Ubuntu 24.04
    </div>
</div>
</body>
</html>
```

---

## STRUGGLES AND HOW YOU OVERCAME THEM

### Struggle 1 — Bash vs Zsh Confusion
**What happened:**
When terminal opened it showed:
```
The default interactive shell is now zsh.
bash-3.2$
```
You were confused about which shell was running.

**How you overcame it:**
```bash
# Ran this to switch permanently
chsh -s /bin/zsh

# Then forced switch in current window
exec zsh

# Confirmed with
echo $SHELL
# Output: /bin/zsh
```
**Lesson learned:** Mac switched default shell from bash to zsh. Both work identically for DevOps commands. The % symbol means zsh is running.

---

### Struggle 2 — Got Stuck in Vim Editor
**What happened:**
Running chsh command accidentally opened vim editor. Screen showed ~ symbols everywhere and you couldn't type or exit.

**How you overcame it:**
```
Typed: :q!
Pressed Enter
```
Exited immediately and returned to normal terminal.

**Lesson learned:** vim is the most common trap for new Linux users. :q! is the universal escape. This is a famous developer joke — you solved it on Day 1 of your journey.

---

### Struggle 3 — Homebrew Not Found
**What happened:**
```
bash: brew: command not found
```
Tried to install git but terminal was still in old bash shell.

**How you overcame it:**
```bash
# Switched to zsh first
exec zsh

# Then installed Homebrew
/bin/bash -c "$(curl -fsSL \
https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Activated Homebrew
eval "$(/opt/homebrew/bin/brew shellenv zsh)"

# Then successfully installed git
brew install git
```
**Lesson learned:** Always confirm you are in zsh (% prompt) before running Homebrew commands.

---

### Struggle 4 — GitHub Push Permission Denied (403 Error)
**What happened:**
```
remote: Permission to Karthiveer/finland-journey.git denied
fatal: unable to access: The requested URL returned error: 403
```
GitHub rejected password login — this is because GitHub removed password authentication in 2021.

**How you overcame it:**
- Generated Personal Access Token (classic) on GitHub
- Settings → Developer Settings → Tokens (classic)
- Ticked repo scope
- Used token as password instead of account password

**Lesson learned:** GitHub requires Personal Access Token — never your account password. Token starts with ghp_ or github_pat_

---

### Struggle 5 — Accidentally Exposed GitHub Token
**What happened:**
Shared the full Personal Access Token publicly in chat:
```
github_pat_11BDNXWHY0UtQQQXJeY24K...
```
This gives anyone full access to your GitHub account.

**How you overcame it:**
- Immediately went to GitHub Settings
- Developer Settings → Tokens → Deleted exposed token
- Generated brand new token
- Used new token for push

**Lesson learned:** Never share tokens, passwords, or .pem files anywhere. Delete and regenerate immediately if accidentally exposed. This is a real security incident even professionals face — you handled it correctly and fast.

---

### Struggle 6 — SCP Permission Denied on Server
**What happened:**
```
scp: dest open "/var/www/html/profile.jpg": Permission denied
```
Tried to upload photo directly to web folder — but that folder is owned by root.

**How you overcame it:**
```bash
# Step 1 — Upload to home folder first (no permission needed)
scp ~/Downloads/VeeraPassportsizephoto.jpg \
    finland-server:~/profile.jpg

# Step 2 — SSH into server
ssh finland-server

# Step 3 — Move to web folder with sudo
sudo mv ~/profile.jpg /var/www/html/profile.jpg

# Step 4 — Fix permissions
sudo chmod 644 /var/www/html/profile.jpg
```
**Lesson learned:** /var/www/html/ is owned by root. Always upload to home folder (~/) first, then move with sudo. This is standard practice on all Linux servers.

---

### Struggle 7 — Webpage Not Accessible in Browser
**What happened:**
Nginx was running perfectly but browser showed connection refused when trying to open http://16.171.138.163

**Root cause:**
AWS Security Group was blocking HTTP port 80 from the internet. Also had created Security Group outside the EC2 instance — it was not attached to the server.

**How you overcame it:**
- Found the Security Group attached directly to the EC2 instance
- Added inbound rules:

| Type | Port | Source |
|------|------|--------|
| SSH | 22 | 0.0.0.0/0 |
| HTTP | 80 | 0.0.0.0/0 |
| HTTPS | 443 | 0.0.0.0/0 |

**Lesson learned:** AWS Security Groups are like a firewall around your server. Even if Nginx is running — AWS blocks all traffic by default. You must explicitly allow ports. Always attach Security Group to the correct EC2 instance.

---

### Struggle 8 — Emojis and Special Characters Not Rendering
**What happened:**
Webpage showed garbled text like:
```
ðŸ‡«ðŸ‡® instead of 🇫🇮
â€" instead of —
â†' instead of →
```

**How you overcame it:**
Added charset meta tag to HTML head:
```html
<meta charset="UTF-8">
```
Used HTML character codes instead of emoji:
```html
&#8594;   instead of →
&#127477;&#127470;  instead of 🇫🇮
&#10084;&#65039;    instead of ❤️
```
**Lesson learned:** Always add UTF-8 charset in HTML. Use HTML character codes for special symbols on servers — more reliable than direct emoji.

---

### Struggle 9 — Webpage Content Appearing Twice
**What happened:**
Page showed all content duplicated — appearing twice on screen.

**Root cause:**
Old Nginx default content was still partially in the file mixed with new content.

**How you overcame it:**
```bash
sudo nano /var/www/html/index.html
# Used Control+K repeatedly to clear entire file
# Then pasted completely fresh HTML
sudo systemctl restart nginx
```
**Lesson learned:** When editing HTML on server — always clear the entire file first before pasting new content. Control+K in nano deletes one line at a time.

---

## Key AWS Concepts Learned Day 3

### EC2 (Elastic Compute Cloud)
Virtual server running in AWS data center. Your finland-devops-server is an EC2 instance running Ubuntu Linux in Stockholm.

### Security Groups
Virtual firewall around your EC2 instance. Controls what traffic can enter (inbound) and leave (outbound) your server. Default blocks everything — you must explicitly open ports.

### Key Pair (.pem file)
Your server password in file format. Whoever has the .pem file can SSH into your server. Never share, never commit to GitHub, never delete.

### Regions and Availability Zones
```
eu-north-1 = Stockholm region (closest to Finland)
eu-north-1a, eu-north-1b, eu-north-1c = availability zones within Stockholm
```
Finnish companies use eu-north-1 as their primary AWS region.

### Free Tier Reminder
```
t3.micro = 750 hours free per month
750 hours = entire month running 24/7

IMPORTANT: Stop your instance when not studying
EC2 → Instances → Instance state → Stop instance
This preserves data but stops billing hours
```

---

## Important Security Rules Learned

| Rule | Why |
|------|-----|
| Never share .pem file | Anyone with it owns your server |
| Never commit .pem to GitHub | Public exposure = server compromised |
| Never share GitHub token | Anyone can push to your repos |
| Delete exposed tokens immediately | Security incident response |
| Add *.pem to .gitignore | Prevents accidental commit |
| MFA on AWS root account | Protects your entire cloud |
| Zero spend budget | Alerts on unexpected charges |

---

## Daily Server Management Commands

```bash
# Connect to server
ssh finland-server

# Check server health
sudo systemctl status nginx
df -h
free -h
uptime

# View live web server logs
sudo tail -f /var/log/nginx/access.log

# View error logs
sudo tail -f /var/log/nginx/error.log

# Restart web server
sudo systemctl restart nginx

# Exit server
exit
```

---

## Push Day 3 Summary to GitHub

```bash
cd ~/finland-journey
git add .
git commit -m "Added Day 3 summary - AWS EC2 and Nginx setup"
git push
```

---

## Day 4 Preview — What Comes Next

| Topic | Commands | Why It Matters |
|-------|----------|----------------|
| Environment Variables | export, echo $VAR | How apps store config and secrets |
| Cron Jobs | crontab -e | Schedule automated tasks |
| Advanced Bash | functions, arguments | Real automation scripts |
| AWS CLI | aws configure | Control AWS from terminal |
| AWS IAM | Users, roles, policies | Security foundation for all AWS |

---

## Your Live Server

```
URL: http://16.171.138.163
Server: AWS EC2 eu-north-1 Stockholm
OS: Ubuntu 24.04 LTS
Web server: Nginx 1.24.0
Photo: profile.jpg (your passport photo uploaded via scp)
```

---

## Milestone Achieved — Post This on LinkedIn

> Just hosted my first webpage on a real cloud server in Stockholm 🇸🇪
>
> Day 3 of my Finland DevOps journey:
> — Launched AWS EC2 server in eu-north-1 Stockholm
> — SSH'd into it from Chennai using terminal
> — Installed Nginx web server
> — Configured AWS Security Groups
> — Hosted a live webpage visible to entire world
>
> The server is physically closer to Helsinki than I am.
> But the skills I'm building will get me there.
>
> Chennai → Helsinki 🇫🇮
> github.com/Karthiveer
>
> #DevOps #AWS #Linux #CloudComputing #Finland #LearningInPublic

---

*Finland DevOps Journey — Karthiveer*
*github.com/Karthiveer*
*Chennai → Helsinki 🇫🇮*
*Day 3 completed: July 2, 2026*
