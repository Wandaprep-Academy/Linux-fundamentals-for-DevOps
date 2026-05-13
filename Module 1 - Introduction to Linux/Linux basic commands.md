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

## MODULE 13 — Advanced Shell Scripting for DevOps 

### What is This Module About?

In Module 12 you learned the building blocks of scripting. Now we put those blocks together to write scripts that DevOps engineers actually use every day.

In this module we will build three simple but real scripts:
1. **Deploy to EC2** — SSH into an AWS server and deploy your app
2. **Simple Pipeline** — Build → Test → Deploy in one script
3. **Build Script** — Prepare your app for deployment

> Each script is kept simple on purpose. Once you understand these, you can add more features later.

---

### Hands-On Lab 1 — Deploy to an EC2 Instance

**What does this script do?**

You have an EC2 server on AWS. Every time you want to update your app, you have to SSH in, pull the latest code, and restart the service. This script does all of that for you in one command from your local machine.

**What you need before running this:**
- An EC2 instance running on AWS
- Your `.pem` key file (downloaded when you created the EC2 instance)
- Your app code in a Git repository
- Node.js installed on the EC2 server

**Step 1 — Create the script on your local machine**
```bash
nano deploy-to-ec2.sh
```

**Step 2 — Write the script**
```bash
#!/bin/bash
# ============================================
# deploy-to-ec2.sh — Deploy app to AWS EC2
# Usage: ./deploy-to-ec2.sh
# ============================================

# --- Your EC2 details (change these) ---
EC2_USER="ec2-user"
EC2_IP="your-ec2-public-ip"
KEY_FILE="~/.ssh/my-key.pem"
APP_DIR="/home/ec2-user/myapp"
GIT_REPO="https://github.com/your-username/your-repo.git"

echo "Starting deployment to EC2..."

# SSH into EC2 and run commands remotely
ssh -i "$KEY_FILE" "$EC2_USER@$EC2_IP" << EOF

  echo "Connected to EC2. Starting deployment..."

  # If app folder doesn't exist, clone it. Otherwise pull latest code.
  if [ ! -d "$APP_DIR" ]; then
    echo "First time setup — cloning repo..."
    git clone $GIT_REPO $APP_DIR
  else
    echo "Pulling latest code..."
    cd $APP_DIR
    git pull origin main
  fi

  # Go into the app folder and install dependencies
  cd $APP_DIR
  npm install

  # Restart the app (using pm2 — a simple Node.js process manager)
  pm2 restart myapp || pm2 start app.js --name myapp

  echo "Deployment done! App is running."

EOF

echo "All done! Your app is live on EC2."
```

**Step 3 — Make it executable and run**
```bash
chmod +x deploy-to-ec2.sh
./deploy-to-ec2.sh
```

**What each part does:**
| Part | Purpose |
|------|---------|
| `EC2_IP` | The public IP address of your EC2 instance |
| `KEY_FILE` | Your `.pem` file — needed to SSH into EC2 |
| `ssh -i ... << EOF` | Opens an SSH connection and runs all commands inside it remotely |
| `git clone` / `git pull` | Gets the latest code — clones on first run, pulls on updates |
| `npm install` | Installs app dependencies on the server |
| `pm2 restart` | Restarts the app — pm2 keeps Node.js apps running in the background |

> **Tip:** Install pm2 on your EC2 server once with: `npm install -g pm2`

---

### Hands-On Lab 2 — Simple CI/CD Pipeline Script

**What is a pipeline?**

A pipeline is just a series of steps that run one after another — Build, Test, Deploy. If any step fails, the pipeline stops so you don't deploy broken code.

Think of it like a checklist:
- ✅ Step 1: Build the app
- ✅ Step 2: Run tests
- ✅ Step 3: Deploy if everything passed

**Step 1 — Create the script**
```bash
nano pipeline.sh
```

**Step 2 — Write the pipeline**
```bash
#!/bin/bash
# ============================================
# pipeline.sh — Simple Build → Test → Deploy
# ============================================

APP_NAME="myapp"
DEPLOY_DIR="/var/www/$APP_NAME"

echo "=============================" 
echo " Pipeline started: $APP_NAME"
echo "============================="

# ---- STEP 1: BUILD ----
echo ""
echo "[1/3] Building app..."
npm install
if [ $? -ne 0 ]; then
    echo "Build failed! Stopping pipeline."
    exit 1
fi
echo "Build passed."

# ---- STEP 2: TEST ----
echo ""
echo "[2/3] Running tests..."
npm test
if [ $? -ne 0 ]; then
    echo "Tests failed! Stopping pipeline. Nothing was deployed."
    exit 1
fi
echo "Tests passed."

# ---- STEP 3: DEPLOY ----
echo ""
echo "[3/3] Deploying app..."
mkdir -p "$DEPLOY_DIR"
cp -r ./* "$DEPLOY_DIR/"
sudo systemctl restart "$APP_NAME"
echo "Deploy done. App is live."

echo ""
echo "============================="
echo " Pipeline finished successfully!"
echo "============================="
```

**Step 3 — Run the pipeline**
```bash
chmod +x pipeline.sh
./pipeline.sh
```

**Sample output:**
```
=============================
 Pipeline started: myapp
=============================

[1/3] Building app...
Build passed.

[2/3] Running tests...
Tests passed.

[3/3] Deploying app...
Deploy done. App is live.

=============================
 Pipeline finished successfully!
=============================
```

**Key concepts:**
| Concept | Explanation |
|---------|-------------|
| `[1/3]`, `[2/3]`, `[3/3]` | Shows progress so you know which step is running |
| `if [ $? -ne 0 ]` | Checks if the last command failed — stops the pipeline if it did |
| `exit 1` | Stops the script with an error — nothing after this line runs |
| `cp -r ./* "$DEPLOY_DIR/"` | Copies all app files to the deployment folder |
| `systemctl restart` | Restarts the service so the new code takes effect |

---

### Hands-On Lab 3 — Build Script

**What is a build script?**

Before you deploy an app, you need to "build" it — this means installing dependencies, compiling code, and packaging everything up so it is ready to run on a server.

This script prepares your Node.js app for deployment.

**Step 1 — Create the script**
```bash
nano build.sh
```

**Step 2 — Write the build script**
```bash
#!/bin/bash
# ============================================
# build.sh — Prepare app for deployment
# ============================================

APP_NAME="myapp"
BUILD_DIR="./build"

echo "Starting build for $APP_NAME..."

# Step 1 — Clean old build files
echo "Cleaning old build..."
rm -rf "$BUILD_DIR"
mkdir -p "$BUILD_DIR"

# Step 2 — Install dependencies
echo "Installing dependencies..."
npm install
if [ $? -ne 0 ]; then
    echo "ERROR: Failed to install dependencies."
    exit 1
fi

# Step 3 — Run the build command (compiles/bundles the app)
echo "Building app..."
npm run build
if [ $? -ne 0 ]; then
    echo "ERROR: Build failed."
    exit 1
fi

# Step 4 — Copy built files to build folder
echo "Copying files to $BUILD_DIR..."
cp -r ./dist/* "$BUILD_DIR/"
cp package.json "$BUILD_DIR/"

# Step 5 — Show what was built
echo ""
echo "============================="
echo " Build complete!"
echo " Output folder : $BUILD_DIR"
echo " Files created :"
ls "$BUILD_DIR"
echo "============================="
```

**Step 3 — Run the build script**
```bash
chmod +x build.sh
./build.sh
```

**Sample output:**
```
Starting build for myapp...
Cleaning old build...
Installing dependencies...
Building app...
Copying files to ./build...

=============================
 Build complete!
 Output folder : ./build
 Files created :
 index.js  styles.css  index.html
=============================
```

**What each step does:**
| Step | Command | Why |
|------|---------|-----|
| Clean | `rm -rf ./build` | Removes old files so you always get a fresh build |
| Install | `npm install` | Downloads all packages the app needs to run |
| Build | `npm run build` | Compiles/bundles the app (defined in package.json) |
| Copy | `cp -r ./dist/*` | Moves the compiled output into the build folder |
| List | `ls ./build` | Confirms the files were created successfully |

> **How these three scripts work together:**
> 1. Run `build.sh` first — prepares the app
> 2. Run `pipeline.sh` — tests and deploys it locally
> 3. Run `deploy-to-ec2.sh` — pushes it live to your AWS server

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
