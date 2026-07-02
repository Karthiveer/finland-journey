# Day 2 Linux Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer/finland-journey

---

## What You Accomplished on Day 2

- Searched server logs using grep like a real DevOps engineer
- Chained commands together using pipes
- Tested network connections to real servers including helsinki.fi
- Pushed first repository live to GitHub
- Learned token-based GitHub authentication
- Understood port numbers used in real DevOps work

---

## Mission 1 — grep + find (Log Searching)

### What is grep?
grep searches inside files for matching text. On a Finnish company server with thousands of log lines, grep finds errors in seconds.

### Commands Practiced

```bash
# Create a fake server log
echo "error: connection failed" > server.log
echo "info: server started" >> server.log
echo "error: timeout reached" >> server.log

# Find all lines containing "error"
grep 'error' server.log

# Show line numbers with results
grep -n 'error' server.log

# Count how many errors exist
grep -c 'error' server.log

# Case-insensitive search
grep -i 'ERROR' server.log

# Search recursively through entire folder
grep -r 'error' ./

# Find all log files in current folder
find . -name '*.log'

# Find all text files
find . -name '*.txt'

# Find all shell scripts
find . -name '*.sh'

# Find files bigger than 100MB
find / -size +100M
```

### Key Difference — > vs >>
```bash
echo "Hello" > file.txt    # OVERWRITES everything — dangerous on logs
echo "Hello" >> file.txt   # APPENDS to end — always use for logs
```

---

## Mission 2 — Pipes and Filters

### What is a Pipe?
The pipe symbol | sends output of one command directly into another command. This is the heart of Linux power — chain as many commands as you need.

### Commands Practiced

```bash
# Find your own shell process
ps aux | grep zsh

# Filter log using pipe
cat server.log | grep error

# Count total lines in log
cat server.log | wc -l

# See only first 2 lines
cat server.log | head -2

# See only last line
cat server.log | tail -1

# Follow a live log in real time (Ctrl+C to stop)
tail -f server.log

# Sort files by size
ls -la | sort -k5 -n

# Triple pipe — count zsh processes
ps aux | grep zsh | wc -l
```

### Real World Use
```bash
# During a server incident in Finland
grep 'error' /var/log/app.log | tail -50 | wc -l
# This finds errors, shows last 50, counts them — one line
```

---

## Mission 3 — Networking Commands

### Commands Practiced

```bash
# Test internet connection — sends 4 pings then stops
ping -c 4 google.com

# Ping Finland directly from Chennai
ping -c 3 helsinki.fi

# Fetch a webpage from terminal
curl https://google.com

# See only server response headers
curl -I https://google.com

# Check HTTP status of any server
curl -I https://helsinki.fi

# Find your IP address
ifconfig | grep inet

# See open ports — what's listening on your machine
netstat -an | grep LISTEN | head -10
```

### netstat vs netstat -an Explained
```bash
netstat        # shows active connections only, uses hostnames (slow)
netstat -an    # shows ALL connections + listening ports, uses IP numbers (fast)

# -a = All connections and listening ports
# -n = Numeric IPs instead of hostnames (no DNS lookup = much faster)
```

### Port Numbers to Memorize
| Port | Service |
|------|---------|
| 22 | SSH — remote server access |
| 80 | HTTP — web traffic |
| 443 | HTTPS — secure web |
| 3306 | MySQL database |
| 5432 | PostgreSQL database |
| 6379 | Redis cache |
| 8080 | App servers / Docker |
| 27017 | MongoDB |

### HTTP Status Codes to Know
| Code | Meaning |
|------|---------|
| 200 | Success |
| 301 | Redirect |
| 404 | Not found |
| 500 | Server error |

---

## Mission 4 — GitHub Push

### Commands Practiced

```bash
# Set Git identity
git config --global user.name "Karthiveer"
git config --global user.email "your@email.com"

# Initialize repository
git init

# Check what Git sees
git status

# Stage all files
git add .

# Save work with message
git commit -m "Day 1 and 2 - Linux CLI fundamentals complete"

# See commit history
git log --oneline

# Connect to GitHub
git remote add origin https://github.com/Karthiveer/finland-journey.git

# Push to GitHub
git push -u origin main

# Daily push routine
git add .
git commit -m "Day X - describe what you learned"
git push
```

### Git Workflow Explained
```
Working Files → git add → Staging Area → git commit → Local Repo → git push → GitHub
```

### Important Security Lesson Learned
- GitHub no longer accepts passwords — use Personal Access Token
- Token starts with ghp_ or github_pat_
- NEVER share your token publicly — delete and regenerate immediately if exposed
- Save token safely in Notes app on Mac
- Run this so Mac remembers token automatically:
```bash
git config --global credential.helper osxkeychain
```

---

## Key Concepts Learned Day 2

### grep flags
| Flag | Meaning |
|------|---------|
| -n | Show line numbers |
| -c | Count matches |
| -i | Case insensitive |
| -r | Search recursively |

### find flags
| Flag | Meaning |
|------|---------|
| -name | Search by filename |
| -type f | Files only |
| -type d | Directories only |
| -size +100M | Larger than 100MB |

### curl flags
| Flag | Meaning |
|------|---------|
| -I | Headers only |
| -O | Download file |
| -L | Follow redirects |

---

## Day 2 Real World Scenario

When a Finnish server crashes at 2am, a DevOps engineer does this:

```bash
# Step 1 — SSH into the server
ssh ubuntu@server-ip

# Step 2 — Find recent errors in logs
grep 'error' /var/log/app.log | tail -100

# Step 3 — Count how many errors
grep -c 'error' /var/log/app.log

# Step 4 — Check if server is responding
curl -I https://company-website.fi

# Step 5 — Check what processes are running
ps aux | grep app

# Step 6 — Check open ports
netstat -an | grep LISTEN
```

That exact sequence — you can do all of it right now. That is Day 2 in action.

---

## Daily GitHub Habit

Every study day push your work:

```bash
cd ~/finland-journey
git add .
git commit -m "Day X - what you learned today"
git push
```

Your growing commit history proves consistency to Finnish recruiters.

---

## Your GitHub
**github.com/Karthiveer/finland-journey**

---

*Finland DevOps Journey — Karthiveer*
*Chennai → Helsinki 🇫🇮*
