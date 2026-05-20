# Module 2: Linux File System & Navigation

## Overview
The Linux file system is the backbone of how data is stored, organized, and accessed on a Linux system.
Unlike Windows (which uses drive letters like `C:\`), Linux organizes everything under a single root directory `/`.
Understanding how to navigate and interact with this structure is a fundamental skill for every DevOps engineer.

---

## 2.1 The Linux File System Hierarchy

Linux follows the **Filesystem Hierarchy Standard (FHS)**, which defines the structure and purpose of directories.

```
/
├── bin/       → Essential user binaries (ls, cp, mv)
├── boot/      → Boot loader files and kernel
├── dev/       → Device files (hard drives, USB, etc.)
├── etc/       → System-wide configuration files
├── home/      → Home directories for users (/home/mike)
├── lib/       → Shared libraries for binaries
├── media/     → Mount points for removable media
├── mnt/       → Temporary mount points
├── opt/       → Optional/third-party software
├── proc/      → Virtual filesystem for process info
├── root/      → Home directory for the root user
├── run/       → Runtime data (PIDs, sockets)
├── srv/       → Data for services (web, FTP)
├── sys/       → Virtual filesystem for kernel/hardware info
├── tmp/       → Temporary files (cleared on reboot)
├── usr/       → User utilities and applications
└── var/       → Variable data (logs, databases, mail)
```

### Key Directories Explained

| Directory | Purpose | DevOps Relevance |
|-----------|---------|-----------------|
| `/etc` | Config files for all services | Nginx, SSH, cron configs live here |
| `/var/log` | System and application logs | Debugging, monitoring |
| `/home` | User home directories | User scripts and personal files |
| `/opt` | Third-party software | Jenkins, custom tools |
| `/tmp` | Temporary files | Build artifacts, temp scripts |
| `/proc` | Live kernel/process data | CPU, memory, process inspection |
| `/dev` | Device files | Disk management, I/O operations |

---

## 2.2 Navigation Commands

### `pwd` — Print Working Directory
Displays the full path of the directory you are currently in.

```bash
pwd
```
**Output:**
```
/home/mike
```
> Use this when you are unsure of your current location in the file system.

---

### `ls` — List Directory Contents
Lists files and directories in the current or specified directory.

```bash
ls                    # List files in current directory
ls -l                 # Long format: shows permissions, owner, size, date
ls -a                 # Show all files including hidden files (starting with .)
ls -la                # Combine long format and show hidden files
ls -lh                # Human-readable file sizes (KB, MB, GB)
ls -lt                # Sort by modification time (newest first)
ls -R                 # Recursively list all subdirectories
ls /etc               # List contents of a specific directory
```

**Example output of `ls -lh`:**
```
drwxr-xr-x  2 mike mike 4.0K Jan 10 09:00 projects
-rw-r--r--  1 mike mike 1.2K Jan 10 08:45 notes.txt
-rwxr-xr-x  1 mike mike 512  Jan 09 14:30 deploy.sh
```

**Breaking down the output columns:**
```
drwxr-xr-x   2   mike   mike   4.0K   Jan 10 09:00   projects
│            │    │      │      │       │              └─ Name
│            │    │      │      │       └─ Last modified
│            │    │      │      └─ Size
│            │    │      └─ Group
│            │    └─ Owner
│            └─ Number of hard links
└─ Permissions (d=directory, -=file, l=symlink)
```

---

### `cd` — Change Directory
Moves you from one directory to another.

```bash
cd /home/mike          # Navigate to an absolute path
cd projects            # Navigate to a relative path (subdirectory)
cd ..                  # Go up one level (parent directory)
cd ../..               # Go up two levels
cd ~                   # Go to your home directory
cd -                   # Go back to the previous directory
cd /                   # Go to the root directory
```

> **Absolute path** starts from root `/` (e.g., `/etc/nginx`)
> **Relative path** starts from your current location (e.g., `nginx/conf`)

---

### `tree` — Display Directory Structure as a Tree
Visually displays the directory structure in a tree format.

```bash
tree                   # Show tree of current directory
tree /etc              # Show tree of a specific directory
tree -L 2              # Limit depth to 2 levels
tree -a                # Include hidden files
tree -d                # Show directories only
tree -h                # Show file sizes in human-readable format
```

**Example output:**
```
/home/mike/projects
├── app
│   ├── index.js
│   └── config.json
├── scripts
│   └── deploy.sh
└── README.md
```

> Install tree if not available: `sudo apt install tree` (Ubuntu/Debian) or `sudo yum install tree` (RHEL/CentOS)

---

## 2.3 File & Directory Information Commands

### `file` — Determine File Type
Identifies the type of a file regardless of its extension.

```bash
file notes.txt         # Identifies as ASCII text
file deploy.sh         # Identifies as shell script
file image.png         # Identifies as PNG image
file /bin/ls           # Identifies as ELF executable (binary)
```

**Output example:**
```
notes.txt:   ASCII text
deploy.sh:   Bourne-Again shell script, ASCII text executable
image.png:   PNG image data, 800 x 600
```

> Useful when a file has no extension or an incorrect one.

---

### `stat` — Display Detailed File Information
Shows detailed metadata about a file or directory.

```bash
stat notes.txt
stat /etc/nginx/nginx.conf
```

**Output example:**
```
  File: notes.txt
  Size: 1234       Blocks: 8       IO Block: 4096   regular file
Device: 802h/2050d Inode: 131073   Links: 1
Access: (0644/-rw-r--r--)  Uid: (1000/mike)   Gid: (1000/mike)
Access: 2024-01-10 09:00:00.000
Modify: 2024-01-09 14:30:00.000
Change: 2024-01-09 14:30:00.000
```

> Shows access time, modification time, inode number, and permissions — critical for debugging and auditing.

---

### `du` — Disk Usage of Files and Directories
Shows how much disk space files and directories are using.

```bash
du notes.txt           # Size of a specific file (in blocks)
du -h notes.txt        # Human-readable size
du -sh /var/log        # Summary of total size of a directory
du -ah /home/mike      # All files with human-readable sizes
du -sh *               # Size of all items in current directory
du --max-depth=1 /var  # Show sizes one level deep
```

**Output example:**
```
4.0K    notes.txt
1.2G    /var/log
```

> Use `du -sh *` to quickly find which directories are consuming the most space.

---

### `df` — Disk Free Space
Shows available and used disk space on mounted file systems.

```bash
df                     # Show disk usage for all filesystems
df -h                  # Human-readable format (GB, MB)
df -hT                 # Include filesystem type
df /home               # Show disk usage for a specific mount point
```

**Output example:**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /
tmpfs           2.0G     0  2.0G   0% /dev/shm
/dev/sdb1       100G   60G   37G  62% /data
```

> Monitor `Use%` — when it reaches 90%+, it's time to clean up or expand storage.

---

## 2.4 Finding Files and Directories

### `find` — Search for Files and Directories
Searches for files and directories based on various criteria.

```bash
# Find by name
find /home -name "notes.txt"           # Find exact filename
find /home -name "*.log"               # Find all .log files
find /home -iname "notes.txt"          # Case-insensitive search

# Find by type
find /etc -type f                      # Find files only
find /etc -type d                      # Find directories only
find /etc -type l                      # Find symbolic links only

# Find by size
find / -size +100M                     # Files larger than 100MB
find / -size -1k                       # Files smaller than 1KB
find /tmp -size +50M -size -200M       # Files between 50MB and 200MB

# Find by time
find /var/log -mtime -7                # Modified in the last 7 days
find /tmp -atime +30                   # Accessed more than 30 days ago
find /home -newer /etc/passwd          # Newer than a reference file

# Find by permissions
find /home -perm 777                   # Files with exact 777 permissions
find /home -perm -u+x                  # Files executable by owner

# Find and execute a command
find /tmp -name "*.tmp" -delete        # Find and delete .tmp files
find /home -name "*.sh" -exec chmod +x {} \;   # Make all .sh files executable
```

> The `find` command is one of the most powerful tools in Linux — master it for log management, security audits, and automation.

---

### `locate` — Quickly Find Files by Name
Uses a pre-built database to find files faster than `find`.

```bash
locate notes.txt           # Find all files named notes.txt
locate "*.conf"            # Find all .conf files
locate -i notes.txt        # Case-insensitive search
locate -n 10 "*.log"       # Limit results to 10
```

> Run `sudo updatedb` to refresh the database before using `locate`.
> `locate` is faster than `find` but may show outdated results if the database is not updated.

---

### `which` — Find the Location of a Command
Shows the full path of a command's executable.

```bash
which python3              # /usr/bin/python3
which nginx                # /usr/sbin/nginx
which bash                 # /bin/bash
```

> Use this to confirm which version of a tool is being used when multiple versions are installed.

---

### `whereis` — Locate Binary, Source, and Manual Files
Finds the binary, source code, and man page for a command.

```bash
whereis nginx              # nginx: /usr/sbin/nginx /etc/nginx /usr/share/man/man8/nginx.8.gz
whereis python3            # python3: /usr/bin/python3 /usr/lib/python3 /usr/share/man/man1/python3.1.gz
```

---

## 2.5 Viewing File Contents

### `cat` — Concatenate and Display File Contents
Displays the full content of a file.

```bash
cat notes.txt              # Display file content
cat -n notes.txt           # Display with line numbers
cat file1.txt file2.txt    # Display multiple files
cat file1.txt file2.txt > combined.txt   # Merge files into one
```

> Best for small files. Avoid using `cat` on large files — use `less` instead.

---

### `less` — View File Content Page by Page
Opens a file for scrollable, interactive viewing.

```bash
less /var/log/syslog       # Open a large log file
less +G /var/log/syslog    # Open and jump to the end
```

**Navigation inside `less`:**
| Key | Action |
|-----|--------|
| `Space` / `f` | Scroll forward one page |
| `b` | Scroll backward one page |
| `G` | Jump to end of file |
| `g` | Jump to beginning |
| `/keyword` | Search forward for keyword |
| `?keyword` | Search backward for keyword |
| `n` | Next search result |
| `q` | Quit |

---

### `head` — View the Beginning of a File
Displays the first lines of a file (default: 10 lines).

```bash
head notes.txt             # Show first 10 lines
head -n 20 notes.txt       # Show first 20 lines
head -n 5 /var/log/syslog  # Show first 5 lines of a log
```

---

### `tail` — View the End of a File
Displays the last lines of a file (default: 10 lines).

```bash
tail notes.txt             # Show last 10 lines
tail -n 20 notes.txt       # Show last 20 lines
tail -f /var/log/syslog    # Follow log file in real-time (live updates)
tail -f /var/log/nginx/access.log   # Monitor Nginx access logs live
```

> `tail -f` is essential for monitoring live logs during deployments and debugging.

---

## 2.6 Path Types: Absolute vs Relative

Understanding path types is critical for writing scripts and navigating efficiently.

### Absolute Path
Always starts from the root `/`. Works from any location.

```bash
cd /home/mike/projects     # Always goes to this exact location
cat /etc/nginx/nginx.conf  # Always reads this exact file
```

### Relative Path
Starts from your current directory. Depends on where you are.

```bash
cd projects                # Only works if 'projects' is in current directory
cat ../config/settings.yml # Goes up one level, then into config/
```

### Special Path Symbols

| Symbol | Meaning | Example |
|--------|---------|---------|
| `/` | Root directory | `cd /` |
| `~` | Current user's home | `cd ~` or `cd ~/projects` |
| `.` | Current directory | `./deploy.sh` |
| `..` | Parent directory | `cd ..` |
| `-` | Previous directory | `cd -` |

---

## 2.7 Symbolic Links (Symlinks)

A symbolic link is a pointer (shortcut) to another file or directory.

```bash
# Create a symbolic link
ln -s /opt/nginx/bin/nginx /usr/local/bin/nginx
#     └─ target (real file)   └─ link name (shortcut)

# Create a symlink for a directory
ln -s /var/www/html /home/mike/webroot

# View symlinks
ls -la /usr/local/bin/nginx
# lrwxrwxrwx 1 root root 20 Jan 10 nginx -> /opt/nginx/bin/nginx

# Remove a symlink
unlink /usr/local/bin/nginx
rm /usr/local/bin/nginx
```

> Symlinks are widely used in DevOps to manage multiple versions of tools (e.g., `python` → `python3.11`).

---

## 2.8 Practical Lab Exercises

### Exercise 1: File System Exploration
```bash
# 1. Check your current location
pwd

# 2. List all files including hidden ones in your home directory
ls -la ~

# 3. Navigate to /etc and list its contents sorted by size
cd /etc && ls -lhS

# 4. Go back to your home directory
cd ~

# 5. Display the directory tree of /etc up to 2 levels deep
tree -L 2 /etc
```

### Exercise 2: Finding Files
```bash
# 1. Find all .conf files in /etc
find /etc -name "*.conf" -type f

# 2. Find files larger than 10MB in /var
find /var -size +10M -type f

# 3. Find all files modified in the last 24 hours
find /home -mtime -1 -type f

# 4. Find and list all empty files in /tmp
find /tmp -empty -type f
```

### Exercise 3: Disk Usage Analysis
```bash
# 1. Check overall disk usage
df -h

# 2. Find the top 5 largest directories in /var
du -sh /var/* | sort -rh | head -5

# 3. Check the size of your home directory
du -sh ~
```

---

## 2.9 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `pwd` | Show current directory |
| `ls -la` | List all files with details |
| `cd /path` | Change to absolute path |
| `cd ..` | Go up one directory |
| `cd ~` | Go to home directory |
| `tree -L 2` | Show directory tree (2 levels) |
| `find / -name file` | Search for a file by name |
| `locate file` | Fast file search using database |
| `which cmd` | Show path of a command |
| `stat file` | Show file metadata |
| `du -sh dir` | Show directory size |
| `df -h` | Show disk space usage |
| `cat file` | Display file content |
| `less file` | Scroll through file content |
| `head -n 10 file` | Show first 10 lines |
| `tail -f file` | Follow file updates live |
| `ln -s target link` | Create a symbolic link |

---

## Summary

In this module, you learned:
- The Linux file system hierarchy and the purpose of key directories
- How to navigate the file system using `pwd`, `ls`, `cd`, and `tree`
- How to inspect files and directories with `stat`, `file`, `du`, and `df`
- How to find files using `find`, `locate`, `which`, and `whereis`
- How to view file contents with `cat`, `less`, `head`, and `tail`
- The difference between absolute and relative paths
- How to create and manage symbolic links

---

> **Next Module →** Module 3: File & Directory Management (create, copy, move, delete, and archive files)
