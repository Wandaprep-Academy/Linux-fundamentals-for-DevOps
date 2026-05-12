# Complete Linux Course Program
### From Fundamentals to Scripting & Automation

---

# Linux System Administration & Automation Masterclass

## Course Overview

This course takes students from complete beginners to advanced Linux system administration, shell scripting, and automation.

By the end of this course, students will be able to:

- Navigate and manage Linux systems confidently
- Work with files, directories, permissions, and processes
- Administer users, services, networking, and storage
- Write powerful Bash scripts
- Automate repetitive tasks
- Monitor and troubleshoot Linux systems
- Understand server security and best practices
- Build automation projects for real-world environments

---

## MODULE 1 — Introduction to Linux

**Topics Covered**
- What is Linux?
- History of Linux & Open Source
- Linux distributions (Ubuntu, CentOS, Debian, Rocky Linux, Kali)
- Linux architecture: Kernel, Shell, Terminal, File System
- GUI vs CLI
- Installing Linux & Virtual Machines (VirtualBox / VMware)

**Practical Lab**
- Install Ubuntu Server/Desktop
- Create a Virtual Machine
- Access terminal & basic navigation

---

## MODULE 2 — Linux File System & Navigation

### Directory Structure

| Directory | Purpose               |
|-----------|-----------------------|
| `/`       | Root directory        |
| `/home`   | User files            |
| `/etc`    | Configuration files   |
| `/var`    | Logs and variable data|
| `/bin`    | Essential commands    |
| `/usr`    | User applications     |
| `/tmp`    | Temporary files       |

### Essential Navigation Commands

**Print Working Directory**
```bash
pwd
# Output: /home/student
```

**List Files and Directories**

```bash
ls
```
Lists files and folders in the current directory.

```bash
ls -l
```
Long format listing — shows permissions, owner, size, and modification date for each file.

```bash
ls -a
```
Shows all files including hidden files (files starting with `.`).

```bash
ls -al
```
Combines `-a` and `-l` — shows all files (including hidden) in long format.

```bash
ls -ls
```
Displays files with their allocated block sizes alongside the long format listing.

```bash
ls -lh
```
Long format with human-readable file sizes (e.g. KB, MB, GB).

```bash
ls -d */
```
Lists only directories in the current location (the `*/` pattern matches directories only).

**Change Directory**
```bash
cd /home    # Move into /home
cd ..       # Go one directory back
cd ~        # Go to home directory
```

---

## MODULE 3 — File & Directory Management

### Creating Files

```bash
touch filename
```
Creates a new empty file. If the file already exists, it updates its timestamp without modifying the content.

---

### Creating Directories

```bash
mkdir test
```
Creates a single directory named `test`.

```bash
mkdir -p test/mark
```
Creates nested directories in one command. The `-p` flag creates parent directories as needed without throwing an error if they already exist.

```bash
mkdir -p a/b/c/d
```
Creates a full nested directory tree `a/b/c/d` in one go.

```bash
rmdir -pv a/b/c/d
```
Removes the directory `a/b/c/d` and all empty parent directories. `-p` removes parents, `-v` prints each directory as it is removed.

---

### Copying Files

```bash
cp file1.txt file2.txt
```
Copies `file1.txt` and saves it as `file2.txt` in the same directory.

```bash
cp file1.txt Test
```
Copies `file1.txt` into the directory `Test`.

```bash
cp file1.txt file2.txt Test
```
Copies multiple files (`file1.txt` and `file2.txt`) into the directory `Test`.

```bash
cp -R Test1 Test2
```
Recursively copies the entire directory `Test1` and its contents into `Test2`. If `Test2` does not exist, it is created.

---

### Moving & Renaming Files

```bash
mv file1.txt file2.txt
```
Renames `file1.txt` to `file2.txt`.

```bash
mv file1.txt Test
```
Moves `file1.txt` into the directory `Test`.

```bash
mv Test1 Test2
```
Renames directory `Test1` to `Test2`. If `Test2` already exists, `Test1` is moved inside it.

---

### Removing Files & Directories

```bash
rm file1.txt
```
Permanently deletes a file.

```bash
rm -r projects
```
Deletes a directory and all its contents recursively.

```bash
rm -rv a/b
```
Recursively deletes `a/b` and its contents. `-v` (verbose) prints each file and directory as it is removed, useful for tracking what was deleted.

---

## MODULE 4 — Viewing & Editing Files

### cat — Concatenate & Display Files

```bash
cat filename.txt
```
Displays the entire content of a file.

```bash
cat file1 file2
```
Displays the contents of `file1` followed by `file2` in sequence — useful for reading multiple files at once.

```bash
cat -b file1
```
Displays file content with line numbers added only to non-empty lines.

```bash
cat -n file1
```
Displays file content with line numbers on every line, including blank lines.

```bash
cat -s file1
```
Suppresses repeated empty lines — multiple consecutive blank lines are squeezed into a single blank line.

```bash
cat -E file1
```
Displays a `$` symbol at the end of each line, making it easy to spot trailing spaces or line endings.

```bash
cat file1 file2 > demo.txt
```
Concatenates `file1` and `file2` and writes the combined output into `demo.txt`. If `demo.txt` exists, it is overwritten.

---

### Other File Viewing Commands

```bash
less logfile.log          # Scroll through large file
head file.txt             # Show first 10 lines
tail file.txt             # Show last 10 lines
tail -f /var/log/syslog   # Monitor logs in real-time
```

### Nano Editor

```bash
nano notes.txt
```

| Shortcut | Function  |
|----------|-----------|
| CTRL + O | Save      |
| CTRL + X | Exit      |
| CTRL + K | Cut line  |

### Vim Editor

```bash
vim file.txt
```

| Command | Function       |
|---------|----------------|
| `i`     | Insert mode    |
| `:w`    | Save           |
| `:q`    | Quit           |
| `:wq`   | Save & quit    |

---

## MODULE 5 — File Permissions & Ownership

```bash
ls -l         # View permissions (e.g. -rwxr-xr--)
chmod 755 script.sh       # Change permissions
chown user:user file.txt  # Change owner and group
```

| Symbol | Meaning | Number | Permission |
|--------|---------|--------|------------|
| `r`    | Read    | 7      | rwx        |
| `w`    | Write   | 5      | r-x        |
| `x`    | Execute | 4      | r--        |

---

## MODULE 6 — Users & Group Management

```bash
sudo adduser john                  # Create user
sudo userdel john                  # Delete user
sudo groupadd developers           # Create group
sudo usermod -aG developers john   # Add user to group
```

---

## MODULE 7 — Package Management

**Ubuntu/Debian**
```bash
sudo apt update           # Update repository
sudo apt upgrade          # Upgrade packages
sudo apt install nginx    # Install software
```

**RHEL/CentOS/Rocky Linux**
```bash
sudo yum install nginx
# or
sudo dnf install nginx
```

---

## MODULE 8 — Process & Service Management

```bash
ps aux        # View running processes
top           # Interactive process viewer
htop          # Enhanced process viewer
kill PID      # Terminate process by ID
```

**System Services**
```bash
sudo systemctl start nginx    # Start service
sudo systemctl stop nginx     # Stop service
sudo systemctl enable nginx   # Enable at boot
```

---

## MODULE 9 — Networking Fundamentals

```bash
ip addr               # Check IP address
ping google.com       # Test connectivity
ss -tulpn             # Display open ports
wget URL              # Download files
ssh user@server-ip    # Secure remote access
```

---

## MODULE 10 — Disk & Storage Management

```bash
df -h                     # Check disk usage
du -sh foldername         # Check directory size
mount /dev/sdb1 /mnt/data # Mount drive
```

---

## MODULE 11 — Log Management & Monitoring

```bash
journalctl              # View system logs
cat /var/log/auth.log   # View authentication logs
free -m                 # Memory usage
uptime                  # System uptime
vmstat                  # System resource stats
```

---

## MODULE 12 — Shell Scripting Fundamentals (Beginner Friendly)

### What is a Shell Script?

Imagine you do the same 10 commands every morning on your server — update packages, check disk space, restart a service. Instead of typing all 10 commands one by one, you put them all in a file and run that file once. That file is a **shell script**.

A shell script is simply a **text file containing Linux commands** that run one after another automatically.

> Think of it like a recipe — you write the steps once, and the computer follows them every time.

---

### LESSON 1 — Your First Script

**Step 1 — Create a new script file**
```bash
nano hello.sh
```

**Step 2 — Type this inside the file**
```bash
#!/bin/bash
echo "Hello! My first script is working!"
```

**Step 3 — Save and exit nano**
- Press `CTRL + O` then `Enter` to save
- Press `CTRL + X` to exit

**Step 4 — Make the script executable**
```bash
chmod +x hello.sh
```
This gives the script permission to run. Without this step, Linux won't allow it to execute.

**Step 5 — Run the script**
```bash
./hello.sh
```

**Output:**
```
Hello! My first script is working!
```

**What does `#!/bin/bash` mean?**

This is called a **shebang**. It is always the very first line of every script. It tells Linux: *"Use the Bash program to run this file."* Without it, Linux won't know how to read your script.

---

### LESSON 2 — Variables (Storing Information)

A variable is like a labelled box where you store a value so you can use it later.

**How to create a variable:**
```bash
name="Mike"
```

**How to use a variable (put `$` in front of it):**
```bash
echo $name
```

**Try this script — save it as `greet.sh`:**
```bash
#!/bin/bash

name="Mike"
city="London"

echo "Hello, my name is $name"
echo "I live in $city"
echo "Welcome to Linux scripting, $name!"
```

```bash
chmod +x greet.sh
./greet.sh
```

**Output:**
```
Hello, my name is Mike
I live in London
Welcome to Linux scripting, Mike!
```

> Notice: to CREATE a variable you write `name="Mike"` (no `$`), but to USE it you write `$name` (with `$`).

---

### LESSON 3 — Asking the User for Input

Sometimes you want the script to ask the user a question and use their answer.

**Save this as `ask.sh`:**
```bash
#!/bin/bash

echo "What is your name?"
read name

echo "What is your favourite city?"
read city

echo "Nice to meet you, $name from $city!"
```

```bash
chmod +x ask.sh
./ask.sh
```

**Output:**
```
What is your name?
Mike
What is your favourite city?
London
Nice to meet you, Mike from London!
```

> `read` pauses the script and waits for the user to type something, then stores it in the variable.

---

### LESSON 4 — If / Else (Making Decisions)

Sometimes your script needs to make a decision — *"if this is true, do this, otherwise do that."*

**Simple example — save as `check-age.sh`:**
```bash
#!/bin/bash

echo "How old are you?"
read age

if [ $age -ge 18 ]; then
    echo "You are an adult."
else
    echo "You are a minor."
fi
```

```bash
chmod +x check-age.sh
./check-age.sh
```

**Output (if user types 20):**
```
You are an adult.
```

**Comparison operators explained simply:**

| Operator | Meaning | Example |
|----------|---------|--------|
| `-eq` | equal to | `[ $a -eq 10 ]` |
| `-ne` | not equal to | `[ $a -ne 10 ]` |
| `-gt` | greater than | `[ $a -gt 5 ]` |
| `-lt` | less than | `[ $a -lt 5 ]` |
| `-ge` | greater than or equal | `[ $a -ge 18 ]` |
| `-le` | less than or equal | `[ $a -le 100 ]` |

---

### LESSON 5 — For Loops (Repeating Tasks)

A loop lets you repeat the same action multiple times without writing it over and over.

**Simple counting loop — save as `count.sh`:**
```bash
#!/bin/bash

for i in 1 2 3 4 5; do
    echo "Number: $i"
done
```

```bash
chmod +x count.sh
./count.sh
```

**Output:**
```
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
```

**Loop over a list of names — save as `greet-team.sh`:**
```bash
#!/bin/bash

for name in Alice Bob Charlie Dave; do
    echo "Hello, $name! Welcome to the team."
done
```

**Output:**
```
Hello, Alice! Welcome to the team.
Hello, Bob! Welcome to the team.
Hello, Charlie! Welcome to the team.
Hello, Dave! Welcome to the team.
```

> Loops are very useful in DevOps — for example, running the same command on 10 servers instead of doing it manually 10 times.

---

### LESSON 6 — While Loops (Keep Going Until Done)

A while loop keeps running as long as a condition is true.

**Simple countdown — save as `countdown.sh`:**
```bash
#!/bin/bash

count=5

while [ $count -gt 0 ]; do
    echo "Countdown: $count"
    count=$((count - 1))
done

echo "Blast off!"
```

**Output:**
```
Countdown: 5
Countdown: 4
Countdown: 3
Countdown: 2
Countdown: 1
Blast off!
```

> `$((count - 1))` is how you do basic maths in Bash. The double brackets `(( ))` tell Bash to calculate a number.

---

### LESSON 7 — Functions (Reusable Blocks of Code)

A function is a block of code you give a name to, so you can call it whenever you need it — instead of writing the same code again and again.

**Save as `functions.sh`:**
```bash
#!/bin/bash

# Define the function
say_hello() {
    echo "Hello! Welcome to Linux."
}

# Define another function
show_date() {
    echo "Today's date is: $(date)"
}

# Call the functions
say_hello
show_date
say_hello
```

**Output:**
```
Hello! Welcome to Linux.
Today's date is: Mon Jan 20 10:30:00 UTC 2025
Hello! Welcome to Linux.
```

> You define a function once at the top, then call it by name anywhere below. This keeps your scripts clean and organised.

---

### LESSON 8 — Comments (Explaining Your Code)

Comments are notes you write in your script for yourself or others. Linux ignores them — they are just for humans to read.

```bash
#!/bin/bash

# This script greets the user
# Written by: Mike
# Date: 2025

# Ask for the user's name
echo "Enter your name:"
read name

# Print the greeting
echo "Hello, $name!"
```

> Always add comments to your scripts. When you come back to a script 6 months later, comments remind you what each part does.

---

### BEGINNER PRACTICE LAB — Put It All Together

**Task:** Write a script that:
1. Asks the user for their name
2. Asks how many files they want to create
3. Creates that many files using a loop
4. Confirms when done

**Save as `create-files.sh`:**
```bash
#!/bin/bash

# Ask for user's name
echo "What is your name?"
read username

# Ask how many files to create
echo "How many files do you want to create?"
read count

# Create the files using a loop
for i in $(seq 1 $count); do
    touch "file_${i}.txt"
    echo "Created: file_${i}.txt"
done

# Done message
echo "Done! $count files created for $username."
```

```bash
chmod +x create-files.sh
./create-files.sh
```

**Output (if user types Mike and 3):**
```
What is your name?
Mike
How many files do you want to create?
3
Created: file_1.txt
Created: file_2.txt
Created: file_3.txt
Done! 3 files created for Mike.
```

---

### BEGINNER AUTOMATION SCRIPTS

Now that you know the basics, here are simple real-world automation scripts a beginner can understand and use.

---

**Script 1 — System Info Script**

This script prints useful information about your server in one go.

```bash
nano system-info.sh
```

```bash
#!/bin/bash

# System Information Script
# Shows key details about your Linux server

echo "============================="
echo "   SYSTEM INFORMATION"
echo "============================="

echo "Hostname    : $(hostname)"
echo "Date & Time : $(date)"
echo "Logged in as: $(whoami)"
echo "Uptime      : $(uptime -p)"
echo ""
echo "--- Disk Usage ---"
df -h /
echo ""
echo "--- Memory Usage ---"
free -h
echo "============================="
```

```bash
chmod +x system-info.sh
./system-info.sh
```

> This is a great first automation script — instead of running 6 commands separately, one script shows everything at once.

---

**Script 2 — Simple Backup Script**

This script copies an important folder to a backup location.

```bash
nano simple-backup.sh
```

```bash
#!/bin/bash

# Simple Backup Script
# Copies a folder to a backup location

# What folder do we want to back up?
SOURCE="/home/student/documents"

# Where should the backup go?
BACKUP="/home/student/backups"

# Create the backup folder if it doesn't exist
mkdir -p $BACKUP

# Copy the folder
cp -r $SOURCE $BACKUP

# Tell the user it's done
echo "Backup complete!"
echo "Copied: $SOURCE"
echo "Saved to: $BACKUP"
```

```bash
chmod +x simple-backup.sh
./simple-backup.sh
```

---

**Script 3 — Check If a Service is Running**

This script checks if nginx is running and tells you the result.

```bash
nano check-service.sh
```

```bash
#!/bin/bash

# Check if a service is running

SERVICE="nginx"

echo "Checking if $SERVICE is running..."

if systemctl is-active --quiet $SERVICE; then
    echo "$SERVICE is running. All good!"
else
    echo "$SERVICE is NOT running. Starting it now..."
    sudo systemctl start $SERVICE
    echo "$SERVICE has been started."
fi
```

```bash
chmod +x check-service.sh
./check-service.sh
```

---

**Script 4 — Create Multiple Users**

Instead of creating users one by one, this script creates them all at once.

```bash
nano create-users.sh
```

```bash
#!/bin/bash

# Create Multiple Users Script

# List of users to create
USERS=("alice" "bob" "charlie" "dave")

for USER in "${USERS[@]}"; do
    # Check if user already exists
    if id "$USER" &>/dev/null; then
        echo "User $USER already exists. Skipping."
    else
        sudo useradd -m $USER
        echo "User $USER created successfully."
    fi
done

echo "All done!"
```

```bash
chmod +x create-users.sh
sudo ./create-users.sh
```

---

**Script 5 — Disk Space Warning Script**

This script checks disk usage and warns you if it gets too full.

```bash
nano disk-warning.sh
```

```bash
#!/bin/bash

# Disk Space Warning Script
# Warns if disk usage goes above 80%

# Set the warning level
WARNING_LEVEL=80

# Get current disk usage as a number
USAGE=$(df / | tail -1 | awk '{print $5}' | tr -d '%')

echo "Current disk usage: ${USAGE}%"

if [ $USAGE -ge $WARNING_LEVEL ]; then
    echo "WARNING! Disk is ${USAGE}% full. Please free up space!"
else
    echo "Disk usage is fine. You have plenty of space."
fi
```

```bash
chmod +x disk-warning.sh
./disk-warning.sh
```

---

### Quick Reference — Bash Scripting Cheat Sheet

| Concept | Syntax | Example |
|---------|--------|---------|
| Shebang | `#!/bin/bash` | First line of every script |
| Print text | `echo "text"` | `echo "Hello"` |
| Variable | `name="value"` | `city="London"` |
| Use variable | `$name` | `echo $city` |
| User input | `read varname` | `read username` |
| If / else | `if [ condition ]; then ... fi` | `if [ $age -ge 18 ]` |
| For loop | `for x in list; do ... done` | `for i in 1 2 3` |
| While loop | `while [ condition ]; do ... done` | `while [ $n -gt 0 ]` |
| Function | `name() { ... }` | `greet() { echo "Hi"; }` |
| Comment | `# your note` | `# This backs up files` |
| Make executable | `chmod +x file.sh` | `chmod +x backup.sh` |
| Run script | `./file.sh` | `./backup.sh` |

---

## MODULE 13 — Advanced Shell Scripting for DevOps (Beginner to Intermediate)

### What is This Module About?

In Module 12 you learned the building blocks of scripting. Now we put those blocks together to write scripts that DevOps engineers actually use every day — deploying apps, running pipelines, checking servers, and taking backups.

Each lab below follows the same simple pattern:
1. We explain what the script does in plain English
2. We build it step by step
3. We explain every important line

> Don't worry if some parts look complex at first. Read the explanation table after each script and it will make sense.

---

### Hands-On Lab 1 — Automated Application Deployment Script

**Scenario:** You need to deploy a Node.js application from a Git repository to a Linux server. The script must pull the latest code, install dependencies, run tests, and restart the service.

**Step 1 — Create the script**
```bash
nano deploy.sh
```

**Step 2 — Write the deployment script**
```bash
#!/bin/bash
# ============================================
# deploy.sh — Application Deployment Script
# Usage: ./deploy.sh <environment>
# ============================================

set -e   # Exit immediately if any command fails

# --- Configuration ---
APP_NAME="nodeapp"
APP_DIR="/var/www/$APP_NAME"
GIT_BRANCH="main"
SERVICE_NAME="nodeapp"
LOG_FILE="/var/log/${APP_NAME}-deploy.log"

# --- Logging function ---
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a $LOG_FILE
}

# --- Check environment argument ---
ENV=${1:-staging}
log "Starting deployment of $APP_NAME to $ENV environment"

# --- Pull latest code ---
log "Pulling latest code from $GIT_BRANCH..."
cd $APP_DIR
git fetch origin
git checkout $GIT_BRANCH
git pull origin $GIT_BRANCH

# --- Install dependencies ---
log "Installing dependencies..."
npm install --production

# --- Run tests ---
log "Running tests..."
npm test
if [ $? -ne 0 ]; then
    log "ERROR: Tests failed. Deployment aborted."
    exit 1
fi

# --- Restart service ---
log "Restarting $SERVICE_NAME service..."
sudo systemctl restart $SERVICE_NAME
sudo systemctl status $SERVICE_NAME --no-pager

log "Deployment of $APP_NAME to $ENV completed successfully."
```

**Step 3 — Make it executable and run**
```bash
chmod +x deploy.sh
./deploy.sh staging
```

**What each part does:**
| Part | Purpose |
|------|---------|
| `set -e` | Stops the script on any error — prevents partial deployments |
| `tee -a $LOG_FILE` | Prints to screen AND appends to a log file simultaneously |
| `${1:-staging}` | Uses the first argument passed, defaults to `staging` if none given |
| `git pull origin $GIT_BRANCH` | Fetches the latest code from the repo |
| `npm install --production` | Installs only production dependencies |
| `$?` | Checks if the last command succeeded (0 = success) |
| `systemctl restart` | Restarts the app service to apply new code |

---

### Hands-On Lab 2 — CI/CD Pipeline Script

**What is a CI/CD Pipeline?**

CI/CD stands for **Continuous Integration / Continuous Deployment**. It is the process of automatically building, testing, and deploying your application every time code changes. Think of it like an assembly line in a factory — code goes in one end, a working app comes out the other.

In this lab we simulate that pipeline with a shell script.

**Scenario:** Simulate a basic CI/CD pipeline with Build → Test → Deploy stages, with rollback on failure.

**Step 1 — Create the pipeline script**
```bash
nano pipeline.sh
```

**Step 2 — Write the pipeline**
```bash
#!/bin/bash
# ============================================
# pipeline.sh — CI/CD Pipeline Script
# Stages: Build → Test → Deploy
# ============================================

set -e

APP_NAME="myapp"
BUILD_DIR="./build"
DEPLOY_DIR="/var/www/$APP_NAME"
BACKUP_DIR="/var/backups/$APP_NAME"
TIMESTAMP=$(date '+%Y%m%d_%H%M%S')

log() { echo "[PIPELINE] [$(date '+%H:%M:%S')] $1"; }
pass() { echo "[PASS] $1"; }
fail() { echo "[FAIL] $1"; exit 1; }

# ---- STAGE 1: BUILD ----
build_stage() {
    log "STAGE 1: BUILD"
    mkdir -p $BUILD_DIR
    npm install
    npm run build
    pass "Build stage completed."
}

# ---- STAGE 2: TEST ----
test_stage() {
    log "STAGE 2: TEST"
    npm test || fail "Test stage failed. Pipeline stopped."
    pass "Test stage completed."
}

# ---- STAGE 3: DEPLOY ----
deploy_stage() {
    log "STAGE 3: DEPLOY"

    # Backup current deployment before overwriting
    if [ -d "$DEPLOY_DIR" ]; then
        log "Backing up current deployment to $BACKUP_DIR/$TIMESTAMP"
        mkdir -p $BACKUP_DIR
        cp -r $DEPLOY_DIR $BACKUP_DIR/$TIMESTAMP
    fi

    # Copy new build to deployment directory
    cp -r $BUILD_DIR/* $DEPLOY_DIR/
    sudo systemctl restart $APP_NAME
    pass "Deploy stage completed. App is live."
}

# ---- ROLLBACK (called on failure) ----
rollback() {
    log "ROLLBACK: Restoring last known good deployment..."
    LAST_BACKUP=$(ls -t $BACKUP_DIR | head -1)
    if [ -n "$LAST_BACKUP" ]; then
        cp -r $BACKUP_DIR/$LAST_BACKUP/* $DEPLOY_DIR/
        sudo systemctl restart $APP_NAME
        log "Rollback to $LAST_BACKUP completed."
    else
        log "No backup found. Manual intervention required."
    fi
}

# ---- TRAP: Auto-rollback on any failure ----
trap 'rollback' ERR

# ---- RUN PIPELINE ----
log "Pipeline started for $APP_NAME"
build_stage
test_stage
deploy_stage
log "Pipeline finished successfully."
```

**Step 3 — Run the pipeline**
```bash
chmod +x pipeline.sh
./pipeline.sh
```

**Key concepts explained:**
| Concept | Explanation |
|---------|-------------|
| `TIMESTAMP=$(date '+%Y%m%d_%H%M%S')` | Generates a unique timestamp for each backup folder |
| `trap 'rollback' ERR` | Automatically calls the rollback function if any command fails |
| `ls -t $BACKUP_DIR \| head -1` | Gets the most recent backup by sorting by time |
| Backup before deploy | Ensures you can always revert if the new deployment breaks something |

---

### Hands-On Lab 3 — Server Health Check Script

**Why do we need a health check?**

Before you deploy new code to a server, you want to make sure the server is in good shape. If the disk is full or memory is low, your deployment could fail or crash the app. This script checks everything automatically before you deploy.

**Scenario:** Check server health — CPU, memory, disk, and service status — before a deployment.

```bash
nano health-check.sh
```

```bash
#!/bin/bash
# ============================================
# health-check.sh — Pre-Deployment Health Check
# ============================================

SERVICES=("nginx" "mysql" "nodeapp")
CPU_THRESHOLD=80
DISK_THRESHOLD=85
MEM_THRESHOLD=85
PASS=0
FAIL=0

log()  { echo "[CHECK] $1"; }
ok()   { echo "  [OK]   $1"; ((PASS++)); }
warn() { echo "  [WARN] $1"; ((FAIL++)); }

# --- CPU Check ---
log "Checking CPU usage..."
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'.' -f1)
if [ "$CPU_USAGE" -lt "$CPU_THRESHOLD" ]; then
    ok "CPU usage is ${CPU_USAGE}% (threshold: ${CPU_THRESHOLD}%)"
else
    warn "CPU usage is HIGH: ${CPU_USAGE}% (threshold: ${CPU_THRESHOLD}%)"
fi

# --- Memory Check ---
log "Checking memory usage..."
MEM_USAGE=$(free | grep Mem | awk '{printf "%d", $3/$2 * 100}')
if [ "$MEM_USAGE" -lt "$MEM_THRESHOLD" ]; then
    ok "Memory usage is ${MEM_USAGE}% (threshold: ${MEM_THRESHOLD}%)"
else
    warn "Memory usage is HIGH: ${MEM_USAGE}% (threshold: ${MEM_THRESHOLD}%)"
fi

# --- Disk Check ---
log "Checking disk usage..."
DISK_USAGE=$(df / | tail -1 | awk '{print $5}' | tr -d '%')
if [ "$DISK_USAGE" -lt "$DISK_THRESHOLD" ]; then
    ok "Disk usage is ${DISK_USAGE}% (threshold: ${DISK_THRESHOLD}%)"
else
    warn "Disk usage is HIGH: ${DISK_USAGE}% (threshold: ${DISK_THRESHOLD}%)"
fi

# --- Service Status Check ---
log "Checking services..."
for SERVICE in "${SERVICES[@]}"; do
    if systemctl is-active --quiet $SERVICE; then
        ok "$SERVICE is running"
    else
        warn "$SERVICE is NOT running"
    fi
done

# --- Summary ---
echo ""
echo "=============================="
echo " Health Check Summary"
echo "=============================="
echo " Passed : $PASS"
echo " Failed : $FAIL"
echo "=============================="

if [ "$FAIL" -gt 0 ]; then
    echo "Server is NOT ready for deployment. Fix issues above."
    exit 1
else
    echo "Server is healthy. Safe to deploy."
    exit 0
fi
```

```bash
chmod +x health-check.sh
./health-check.sh
```

**What each check does:**
| Check | Command Used | Why It Matters |
|-------|-------------|----------------|
| CPU | `top -bn1` + `awk` | High CPU before deploy can cause timeouts |
| Memory | `free` + `awk` | Low memory can crash the app after restart |
| Disk | `df /` + `awk` | Full disk will break logging and file writes |
| Services | `systemctl is-active` | Ensures dependencies are up before deploying |

---

### Hands-On Lab 4 — Automated Backup Script

**Why automate backups?**

If you manually back up files, you will eventually forget. Automated backups run on a schedule without you doing anything. This script backs up your app files, keeps the last 7 days of backups, and deletes older ones to save disk space.

**Scenario:** Automatically back up application files daily and delete backups older than 7 days.

```bash
nano backup.sh
```

```bash
#!/bin/bash
# ============================================
# backup.sh — Automated Backup with Retention
# Schedule: Run daily via cron
# ============================================

APP_NAME="myapp"
SOURCE_DIRS=("/var/www/$APP_NAME" "/etc/nginx" "/etc/mysql")
BACKUP_ROOT="/var/backups/$APP_NAME"
RETENTION_DAYS=7
TIMESTAMP=$(date '+%Y-%m-%d_%H-%M-%S')
BACKUP_FILE="$BACKUP_ROOT/${APP_NAME}_backup_${TIMESTAMP}.tar.gz"
LOG_FILE="/var/log/${APP_NAME}-backup.log"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a $LOG_FILE; }

# --- Create backup directory ---
mkdir -p $BACKUP_ROOT

# --- Create compressed backup ---
log "Starting backup of: ${SOURCE_DIRS[*]}"
tar -czf $BACKUP_FILE "${SOURCE_DIRS[@]}" 2>>$LOG_FILE

if [ $? -eq 0 ]; then
    SIZE=$(du -sh $BACKUP_FILE | cut -f1)
    log "Backup created: $BACKUP_FILE (Size: $SIZE)"
else
    log "ERROR: Backup failed!"
    exit 1
fi

# --- Enforce retention policy ---
log "Removing backups older than $RETENTION_DAYS days..."
DELETED=$(find $BACKUP_ROOT -type f -name "*.tar.gz" -mtime +$RETENTION_DAYS -print -delete | wc -l)
log "Deleted $DELETED old backup(s)."

log "Backup process complete."
```

**Add to cron to run daily at 2am:**
```bash
crontab -e
```
```
0 2 * * * /home/devops/backup.sh
```

**Key concepts:**
| Concept | Explanation |
|---------|-------------|
| `tar -czf` | Creates a compressed `.tar.gz` archive |
| `RETENTION_DAYS=7` | Only keep 7 days of backups to save disk space |
| `find -mtime +7 -delete` | Finds and deletes files older than 7 days |
| `tee -a $LOG_FILE` | Logs output to both terminal and file |

---

### Hands-On Lab 5 — Server Setup / Provisioning Script

**What is server provisioning?**

When you get a brand new Linux server, it has nothing on it. Provisioning means installing all the tools, creating the right users, setting up folders, and configuring the firewall. Doing this manually on 10 servers would take hours. This script does it all in one run.

**Scenario:** Set up a fresh Linux server with all tools, users, directories, and firewall rules needed for a DevOps environment.

```bash
nano setup-server.sh
```

```bash
#!/bin/bash
# ============================================
# setup-server.sh — DevOps Server Provisioning
# Run once on a fresh server
# ============================================

set -e

DEVOPS_USER="devops"
APP_DIRS=("/var/www/myapp" "/var/log/myapp" "/var/backups/myapp")
PACKAGES=("git" "curl" "wget" "nginx" "nodejs" "npm" "docker.io" "ufw")

log() { echo "[SETUP] $1"; }

# --- Must run as root ---
if [ "$(id -u)" -ne 0 ]; then
    echo "Please run as root: sudo ./setup-server.sh"
    exit 1
fi

# --- Update system ---
log "Updating system packages..."
apt update -y && apt upgrade -y

# --- Install required packages ---
log "Installing packages: ${PACKAGES[*]}"
apt install -y "${PACKAGES[@]}"

# --- Create devops user ---
if ! id "$DEVOPS_USER" &>/dev/null; then
    log "Creating user: $DEVOPS_USER"
    useradd -m -s /bin/bash $DEVOPS_USER
    usermod -aG sudo,docker $DEVOPS_USER
    log "User $DEVOPS_USER created and added to sudo and docker groups."
else
    log "User $DEVOPS_USER already exists. Skipping."
fi

# --- Create application directories ---
log "Creating application directories..."
for DIR in "${APP_DIRS[@]}"; do
    mkdir -p $DIR
    chown $DEVOPS_USER:$DEVOPS_USER $DIR
    log "Created: $DIR"
done

# --- Configure firewall ---
log "Configuring firewall..."
ufw allow OpenSSH
ufw allow 'Nginx Full'
ufw --force enable
log "Firewall configured."

# --- Enable and start services ---
log "Enabling services..."
systemctl enable nginx docker
systemctl start nginx docker

# --- Final summary ---
echo ""
echo "=============================="
echo " Server Setup Complete"
echo "=============================="
echo " User     : $DEVOPS_USER"
echo " Packages : ${PACKAGES[*]}"
echo " Firewall : Enabled (SSH + HTTP/HTTPS)"
echo "=============================="
```

```bash
chmod +x setup-server.sh
sudo ./setup-server.sh
```

**Key concepts:**
| Concept | Explanation |
|---------|-------------|
| `id -u` check | Ensures the script runs as root — required for system changes |
| `id "$USER" &>/dev/null` | Checks if user exists before creating — makes script idempotent |
| `usermod -aG sudo,docker` | Grants the devops user sudo and Docker access |
| Idempotent design | Script can be run multiple times safely without breaking anything |

---

## MODULE 14 — Task Automation (Cron Jobs)

```bash
crontab -e    # Edit cron jobs
```

**Run script every day at midnight**
```
0 0 * * * /home/user/backup.sh
```

| Field | Meaning |
|-------|---------|
| 0     | Minute  |
| 0     | Hour    |
| *     | Day     |
| *     | Month   |
| *     | Weekday |

---

## MODULE 15 — Linux Security Fundamentals

**File Security:** Permissions, Ownership, Sudo access

**Firewall (Ubuntu)**
```bash
sudo ufw enable     # Enable firewall
sudo ufw status     # Check status
```

**Secure SSH**
- Disable root login
- Use SSH keys
- Change default SSH port

---

## MODULE 16 — Linux Automation Projects

| Level        | Projects                                                        |
|--------------|-----------------------------------------------------------------|
| Beginner     | File organizer, backup script, disk usage monitor, user creation|
| Intermediate | Log analyzer, web server deployment, service monitoring         |
| Advanced     | Server hardening, infrastructure deployment, cron maintenance   |

---

## MODULE 17 — Introduction to DevOps Tools

**Git Basics**
```bash
git init
git add .
git commit -m "Initial commit"
```

**Ansible**
```bash
sudo apt install ansible
```

```yaml
- hosts: servers
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

---

## MODULE 18 — Final Capstone Project

Students will build a complete Linux automation solution covering:
- User management
- Backup automation
- Monitoring system
- Scheduled tasks
- Security hardening
- Service deployment

---

---

## Course Delivery — Weekly Structure

| Week   | Topic                    |
|--------|--------------------------|
| Week 1 | Linux Fundamentals       |
| Week 2 | File Management          |
| Week 3 | Permissions & Users      |
| Week 4 | Networking & Services    |
| Week 5 | Bash Scripting           |
| Week 6 | Advanced Automation      |
| Week 7 | Security & Monitoring    |
| Week 8 | Final Project            |

---

## Certification Preparation

This course prepares students for:
- Linux Essentials
- CompTIA Linux+
- RHCSA
- LFCS

---

## Teaching Approach

Each lesson includes:
1. Theory
2. Command Breakdown
3. Live Demonstration
4. Hands-on Lab
5. Real-world Scenario
6. Homework / Practice Task

---

## Sample Homework

**Homework 1**
- Create 5 users
- Create a group
- Assign permissions
- Create directories
- Write a backup script

---

## Course Outcome

After completing this course, students will be able to:
- Administer Linux systems professionally
- Troubleshoot Linux servers
- Write shell scripts
- Automate repetitive tasks
- Manage enterprise Linux environments
- Start careers in Linux administration and DevOps
