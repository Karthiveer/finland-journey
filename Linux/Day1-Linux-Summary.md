# Day 1 Linux Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer/finland-journey

---

## What You Accomplished on Day 1

- Opened Mac terminal for the very first time
- Switched shell from bash to zsh professionally
- Got stuck in vim and escaped it independently
- Completed all 5 Linux levels in one single session
- Installed Homebrew — Mac's package manager
- Installed Git — version control tool
- Took screenshots and debugged errors live
- Built a professional DevOps setup on MacBook from scratch

---

## Terminal Setup Completed

### Switched from Bash to Zsh
```bash
# Switch shell permanently
chsh -s /bin/zsh

# Force switch in current window
exec zsh

# Verify zsh is running
echo $SHELL
# Output: /bin/zsh
```

### Your Prompt Now Looks Like
```
veersaru@VeerSarus-MacBook-Air ~ %
```
The % symbol confirms zsh is running professionally.

---

## Installed Homebrew
Homebrew is the App Store for your terminal — install any DevOps tool with one command.

```bash
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Activate Homebrew
echo >> /Users/veersaru/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv zsh)"' >> /Users/veersaru/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv zsh)"

# Verify installation
brew --version
# Output: Homebrew 6.0.5

# Install any tool
brew install toolname
```

---

## Installed Git
```bash
# Install Git via Homebrew
brew install git

# Verify installation
git --version
# Output: git version 2.50.1
```

---

## Level 1 — Navigation

### What is pwd?
pwd = Print Working Directory
Answers one question: "Where am I right now?"
Like looking at the room number on a door in a Helsinki office building.

```bash
# Where am I right now?
pwd
# Output: /Users/veersaru

# See what is around you
ls

# See everything including hidden files with details
ls -la

# Go home from anywhere
cd ~

# Go to root of entire system
cd /

# Come back home
cd ~

# Move into a specific folder
cd Documents

# Go back one level
cd ..

# See folder structure
find . -type d | head -20
```

### Understanding the Filesystem
```
/                         ← root of entire Mac
└── Users/
    └── veersaru/         ← your home folder (cd ~ brings you here)
        ├── Desktop/
        ├── Downloads/
        ├── Documents/
        └── finland-journey/
```

---

## Level 2 — Files and Folders

```bash
# Create a new folder
mkdir finland-journey

# Create nested folders in one shot
mkdir -p finland/helsinki/jobs

# Move inside your folder
cd finland-journey

# Create an empty file
touch notes.txt

# Write text into a file
echo "My DevOps Journey to Finland" > notes.txt

# Read the file
cat notes.txt

# Append more text without deleting existing
echo "Week 1: Linux" >> notes.txt

# Verify both lines are there
cat notes.txt

# Copy a file
cp notes.txt backup.txt

# Move or rename a file
mv backup.txt finland/backup.txt

# Delete a file
rm notes.txt

# Delete a folder and everything inside
rm -rf finland

# Confirm deletion
ls
```

### Critical Difference — > vs >>
```bash
echo "Hello" > file.txt     # OVERWRITES everything — wipes existing content
echo "Hello" >> file.txt    # APPENDS to end — adds without deleting
```
Using > instead of >> on a production server log can wipe critical data.
Always double check which one you use.

---

## Level 3 — Permissions

### Understanding Permission String
When you run ls -la you see:
```
-rwxr-xr-x  1  veersaru  staff  myscript.sh
```

Read it in groups of 3:
```
-  rwx  r-x  r-x
|   |    |    |
|   |    |    └── Others (everyone else)
|   |    └─────── Group
|   └──────────── Owner (you)
└──────────────── File type (- = file, d = directory)
```

### Permission Letters
```
r = read    = 4
w = write   = 2
x = execute = 1
```

### Permission Numbers
```
7 = 4+2+1 = rwx = full access
5 = 4+0+1 = r-x = read and execute
4 = 4+0+0 = r-- = read only
0 = 0+0+0 = --- = no access
```

### Commands Practiced
```bash
# Create a script file
touch myscript.sh

# Check current permissions
ls -la myscript.sh
# Shows: -rw-r--r--

# Make it executable
chmod +x myscript.sh

# Verify permissions changed
ls -la myscript.sh
# Now shows: -rwxr-xr-x

# Common permission settings
chmod 777 myscript.sh   # everyone full access — avoid on servers
chmod 755 myscript.sh   # standard for scripts
chmod 644 myscript.sh   # standard for files
chmod 400 myscript.sh   # read only — used for AWS key files
```

---

## Level 4 — Processes

### What is a Process?
Every program running on your Mac or a Finnish server is a process.
Each process has a unique PID (Process ID).
DevOps engineers monitor processes to find what is using too much CPU or memory.

```bash
# See all running processes
ps aux

# Live process monitor — press Q to quit
top

# Find a specific process
ps aux | grep bash

# Run a process in background
sleep 30 &

# Find its process ID
pgrep sleep

# Kill a process by ID
kill $(pgrep sleep)

# Verify it is gone
ps aux | grep sleep

# Check disk space
df -h

# Check folder size
du -sh ~
```

### ps aux Columns Explained
```
USER    PID   %CPU  %MEM   COMMAND
veersaru 1234  0.1   0.5   /bin/zsh
```
- PID = Process ID — needed to kill a process
- %CPU = how much processor it is using
- %MEM = how much memory it is using

---

## Level 5 — Bash Script

### What is a Bash Script?
A bash script is a file containing Linux commands that run automatically.
This is the foundation of DevOps automation.

### Your First Script
```bash
# Create the script file
nano devops.sh
```

Type this inside nano:
```bash
#!/bin/bash

# My first bash script
echo "==============================="
echo "  Finland DevOps Journey"
echo "==============================="

# Variables
NAME="Karthiveer"
GOAL="Helsinki"
MONTHS=6

echo "Name: $NAME"
echo "Target City: $GOAL"
echo "Months to prepare: $MONTHS"

# Loop
echo ""
echo "Monthly milestones:"
for i in 1 2 3 4 5 6
do
  echo "  Month $i - Keep going!"
done

# Condition
if [ $MONTHS -eq 6 ]; then
  echo ""
  echo "6 months is enough. Stay consistent!"
fi

echo ""
echo "Journey started: $(date)"
```

Save with Ctrl+O → Enter → Ctrl+X

```bash
# Make script executable
chmod +x devops.sh

# Run your script
./devops.sh

# Check if it ran successfully — 0 means success
echo $?
```

### Bash Script Concepts Learned
```bash
# Variable
NAME="Karthiveer"
echo $NAME

# Loop
for i in 1 2 3
do
  echo "Number $i"
done

# Condition
if [ $MONTHS -eq 6 ]; then
  echo "Six months!"
fi

# Current date
echo $(date)
```

---

## Vim — The Lesson Nobody Forgets

You accidentally opened vim and escaped it yourself on Day 1.
This is a rite of passage every developer goes through.

```bash
# If you ever get stuck in vim again
:q!        # quit without saving — your emergency exit

# Other vim commands
:w         # save file
:wq        # save and quit
i          # enter insert mode to type
Esc        # exit insert mode
```

Getting stuck in vim and escaping it is something senior engineers joke about for years.
You solved it on Day 1. 🎯

---

## Screenshot Shortcuts Learned

| Shortcut | Action |
|----------|--------|
| Command + Shift + 3 | Capture whole screen |
| Command + Shift + 4 | Capture selected area |
| Command + Shift + 4 + Spacebar | Capture specific window |
| Command + Shift + 4 + hold Control | Copy to clipboard — paste directly in Claude |

---

## Key Concepts Summary

| Command | What It Does |
|---------|-------------|
| pwd | Show current location |
| ls -la | List all files with details |
| cd ~ | Go home |
| mkdir | Create folder |
| touch | Create empty file |
| echo "text" > file | Write text to file |
| echo "text" >> file | Append text to file |
| cat | Read file |
| cp | Copy file |
| mv | Move or rename |
| rm -rf | Delete folder and contents |
| chmod | Change permissions |
| ps aux | See all processes |
| kill | Stop a process |
| nano | Open text editor |
| ./script.sh | Run a script |

---

## Day 1 Real World Lesson

Everything you learned on Day 1 is what a DevOps engineer does
on their first day accessing a new Finnish company server:

```bash
# Connect to server and immediately run
pwd              # where am I?
ls -la           # what is here?
ps aux           # what is running?
df -h            # how much disk space?
./setup.sh       # run the setup script
```

You can do all of that right now. That is your Day 1 in action.

---

## Tools Installed on Your Mac

| Tool | Version | Purpose |
|------|---------|---------|
| zsh | Built in | Professional shell |
| Homebrew | 6.0.5 | Mac package manager |
| Git | 2.50.1 | Version control |

---

## Your Finland DevOps Setup

```
MacBook Air
└── zsh terminal
    ├── Homebrew (install any tool instantly)
    ├── Git (version control)
    └── finland-journey/ (your project folder)
        ├── notes.txt
        ├── myscript.sh
        └── devops.sh
```

---

*Finland DevOps Journey — Karthiveer*
*Chennai → Helsinki 🇫🇮*
