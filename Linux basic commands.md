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

## MODULE 12 — Shell Scripting Fundamentals

**First Script**
```bash
#!/bin/bash
echo "Welcome to Linux Automation"
```

```bash
chmod +x script.sh    # Make executable
./script.sh           # Run script
```

**Variables**
```bash
name="John"
echo $name
```

**User Input**
```bash
read username
echo "Welcome $username"
```

**Conditional Statements**
```bash
if [ $age -ge 18 ]; then
    echo "Adult"
else
    echo "Minor"
fi
```

**Loops**
```bash
# For loop
for i in {1..5}; do
    echo $i
done

# While loop
while true; do
    echo "Running"
done
```

**Functions**
```bash
function greet() {
    echo "Hello Student"
}
greet
```

---

## MODULE 13 — Advanced Shell Scripting

**File Check Script**
```bash
if [ -f test.txt ]; then
    echo "File exists"
else
    echo "File not found"
fi
```

**Backup Script**
```bash
#!/bin/bash
tar -czf backup.tar.gz /home/user/documents
```

**Log Cleanup Script**
```bash
find /var/log -type f -mtime +30 -delete
```

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

## BONUS TOPICS

**Docker Basics**
```bash
docker ps
docker images
docker run nginx
```

**Kubernetes Introduction:** Pods, Deployments, Services

**Recommended Tools:** Ubuntu Server, Rocky Linux, VirtualBox, VMware, GitHub, VS Code, PuTTY, WinSCP

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
