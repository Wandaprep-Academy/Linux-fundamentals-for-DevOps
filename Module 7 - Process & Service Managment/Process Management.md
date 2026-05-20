# Module 7: Process & Service Management

## Overview
Every program running on a Linux system is a **process**. As a DevOps engineer, you need to monitor,
control, and troubleshoot processes constantly — whether it's checking why a server is slow, killing
a hung application, managing background jobs, or controlling system services like Nginx, MySQL, or
a custom application daemon. This module covers everything from basic process inspection to full
service lifecycle management with `systemd`.

---

## 7.1 Understanding Processes

### What is a Process?
A process is an instance of a running program. Every process has:

| Attribute | Description |
|-----------|-------------|
| **PID** | Process ID — unique identifier for the process |
| **PPID** | Parent Process ID — the process that spawned it |
| **UID/GID** | The user and group the process runs as |
| **State** | Current state of the process |
| **Priority** | Scheduling priority (nice value) |
| **TTY** | Terminal associated with the process |

### Process States

| State | Symbol | Description |
|-------|--------|-------------|
| Running | `R` | Actively using the CPU or in the run queue |
| Sleeping (interruptible) | `S` | Waiting for an event (I/O, signal) |
| Sleeping (uninterruptible) | `D` | Waiting for I/O — cannot be interrupted |
| Stopped | `T` | Paused by a signal (e.g., Ctrl+Z) |
| Zombie | `Z` | Finished but not yet cleaned up by parent |
| Idle | `I` | Kernel idle thread |

### Process Hierarchy
Every process (except `init`/`systemd`) is spawned by a parent process, forming a tree:

```
systemd (PID 1)
├── sshd (PID 512)
│   └── bash (PID 1024)
│       └── vim (PID 2048)
├── nginx (PID 600)
│   ├── nginx worker (PID 601)
│   └── nginx worker (PID 602)
└── cron (PID 700)
```

---

## 7.2 `ps` — Process Status

Displays a snapshot of currently running processes.

```bash
# Basic usage
ps                                    # Show processes in current terminal session
ps -e                                 # Show all processes on the system
ps -ef                                # Full-format listing of all processes
ps -aux                               # BSD-style: all processes with detailed info
ps -u mike                            # Show processes owned by user 'mike'
ps -p 1234                            # Show info for a specific PID
ps --ppid 1234                        # Show children of a specific process

# Useful combinations
ps -ef | grep nginx                   # Find nginx processes
ps -ef | grep -v grep                 # Exclude grep itself from results
ps -eo pid,ppid,user,%cpu,%mem,cmd    # Custom output columns
ps -eo pid,%cpu,%mem,cmd --sort=-%cpu # Sort by CPU usage (highest first)
ps -eo pid,%cpu,%mem,cmd --sort=-%mem # Sort by memory usage (highest first)
```

**Output of `ps -ef`:**
```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 09:00 ?        00:00:03 /sbin/init
root       512     1  0 09:00 ?        00:00:00 sshd: /usr/sbin/sshd
mike      1024   512  0 09:05 pts/0    00:00:00 bash
mike      2048  1024  0 09:10 pts/0    00:00:01 vim app.conf
```

**Output columns explained:**
| Column | Description |
|--------|-------------|
| `UID` | User who owns the process |
| `PID` | Process ID |
| `PPID` | Parent Process ID |
| `C` | CPU utilization |
| `STIME` | Start time |
| `TTY` | Terminal (`?` = no terminal / background) |
| `TIME` | Total CPU time used |
| `CMD` | Command that started the process |

---

## 7.3 `top` — Real-Time Process Monitor

Displays a live, continuously updating view of system processes and resource usage.

```bash
top                                   # Launch top (updates every 3 seconds)
top -u mike                           # Show only processes for user 'mike'
top -p 1234                           # Monitor a specific PID
top -n 5                              # Run top for 5 iterations then exit
top -b -n 1 > top-output.txt          # Batch mode: save output to file
```

### Top Display Layout

```
top - 09:30:00 up 2 days,  3:15,  2 users,  load average: 0.52, 0.48, 0.45
Tasks: 185 total,   1 running, 184 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.2 us,  1.3 sy,  0.0 ni, 92.8 id,  0.5 wa,  0.0 hi,  0.2 si
MiB Mem :   7850.0 total,   2100.0 free,   3200.0 used,   2550.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   4200.0 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 1234 mike      20   0  512000  45000  12000 S   5.2   0.6   0:30.12 node
  600 www-data  20   0  145000  12000   8000 S   1.3   0.2   0:05.00 nginx
```

**Header lines explained:**
- Line 1: System uptime, logged-in users, load averages (1, 5, 15 min)
- Line 2: Task/process counts by state
- Line 3: CPU usage breakdown (`us`=user, `sy`=system, `id`=idle, `wa`=I/O wait)
- Line 4: Physical memory usage
- Line 5: Swap memory usage

### Interactive Keys Inside `top`

| Key | Action |
|-----|--------|
| `h` | Show help |
| `q` | Quit |
| `k` | Kill a process (enter PID) |
| `r` | Renice a process (change priority) |
| `M` | Sort by memory usage |
| `P` | Sort by CPU usage |
| `T` | Sort by running time |
| `u` | Filter by username |
| `f` | Add/remove display columns |
| `1` | Toggle per-CPU stats |
| `Space` | Refresh immediately |

---

## 7.4 `htop` — Enhanced Interactive Process Viewer

`htop` is an improved, more user-friendly version of `top` with color and mouse support.

```bash
# Install htop
sudo apt install htop -y              # Ubuntu/Debian
sudo dnf install htop -y              # RHEL/CentOS

# Run htop
htop                                  # Launch htop
htop -u mike                          # Show only mike's processes
htop -p 1234,5678                     # Monitor specific PIDs
```

**htop advantages over top:**
- Color-coded CPU, memory, and swap bars
- Mouse support (click to select/kill processes)
- Easier process tree view (`F5`)
- Horizontal and vertical scrolling
- Search processes with `F3`
- Filter processes with `F4`

---

## 7.5 `kill` — Send Signals to Processes

Sends a signal to a process, most commonly used to terminate processes.

### Common Signals

| Signal | Number | Name | Description |
|--------|--------|------|-------------|
| `SIGHUP` | `1` | Hangup | Reload config (graceful restart) |
| `SIGINT` | `2` | Interrupt | Same as Ctrl+C |
| `SIGQUIT` | `3` | Quit | Quit and dump core |
| `SIGKILL` | `9` | Kill | Force kill — cannot be caught or ignored |
| `SIGTERM` | `15` | Terminate | Graceful termination (default) |
| `SIGSTOP` | `19` | Stop | Pause process (cannot be caught) |
| `SIGCONT` | `18` | Continue | Resume a stopped process |
| `SIGUSR1` | `10` | User 1 | Custom signal (app-defined) |
| `SIGUSR2` | `12` | User 2 | Custom signal (app-defined) |

```bash
# Kill by PID
kill 1234                             # Send SIGTERM (graceful) to PID 1234
kill -15 1234                         # Explicitly send SIGTERM
kill -9 1234                          # Force kill (SIGKILL) — use as last resort
kill -1 1234                          # Send SIGHUP (reload config)
kill -SIGTERM 1234                    # Use signal name instead of number

# Kill multiple processes
kill 1234 5678 9012                   # Kill multiple PIDs at once

# List all available signals
kill -l
```

> Always try `kill` (SIGTERM) first to allow the process to clean up gracefully.
> Only use `kill -9` (SIGKILL) if the process doesn't respond to SIGTERM.

---

## 7.6 `killall` and `pkill` — Kill by Name

Kill processes by name instead of PID.

```bash
# killall — kill all processes with exact name
killall nginx                         # Kill all nginx processes (SIGTERM)
killall -9 nginx                      # Force kill all nginx processes
killall -u mike                       # Kill all processes owned by mike
killall -v nginx                      # Verbose: confirm what was killed

# pkill — kill processes by pattern match
pkill nginx                           # Kill processes matching 'nginx'
pkill -9 nginx                        # Force kill matching processes
pkill -u mike                         # Kill all processes owned by mike
pkill -f "python app.py"              # Kill by full command line match
pkill -SIGHUP nginx                   # Send SIGHUP to nginx (reload config)
```

---

## 7.7 `pgrep` — Find Process IDs by Name

Searches for processes by name and returns their PIDs.

```bash
pgrep nginx                           # Get PID(s) of nginx
pgrep -u mike                         # Get PIDs of all processes owned by mike
pgrep -l nginx                        # Show PID and process name
pgrep -a nginx                        # Show PID and full command line
pgrep -f "python app.py"              # Match against full command line
pgrep -c nginx                        # Count matching processes
```

**Example — Check if a service is running:**
```bash
if pgrep nginx > /dev/null; then
    echo "Nginx is running"
else
    echo "Nginx is not running"
fi
```

---

## 7.8 Background and Foreground Jobs

Linux allows you to run processes in the background and manage multiple jobs in a terminal.

```bash
# Run a command in the background
command &                             # Append & to run in background
sleep 100 &                           # Run sleep in background
[1] 1234                              # Shell shows job number and PID

# Job control commands
jobs                                  # List all background jobs
jobs -l                               # List jobs with PIDs

fg                                    # Bring most recent background job to foreground
fg %1                                 # Bring job number 1 to foreground
fg %2                                 # Bring job number 2 to foreground

bg                                    # Resume most recent stopped job in background
bg %1                                 # Resume job number 1 in background

# Suspend a foreground process
Ctrl+Z                                # Pause (stop) the current foreground process
                                      # Then use bg to resume it in background
```

**Example workflow:**
```bash
# Start a long-running task
tar -czvf backup.tar.gz /var/www/ &
[1] 2345

# Check its progress
jobs -l
# [1]+  2345 Running    tar -czvf backup.tar.gz /var/www/ &

# Bring it to foreground to see output
fg %1

# Pause it and send back to background
Ctrl+Z
bg %1
```

---

## 7.9 `nohup` — Run Processes That Survive Logout

`nohup` (no hang up) keeps a process running even after you log out of the terminal.

```bash
nohup command &                       # Run command immune to hangup signals
nohup ./deploy.sh &                   # Run deploy script in background
nohup python3 app.py > app.log 2>&1 & # Redirect output to a log file

# Output goes to nohup.out by default
cat nohup.out                         # View nohup output
```

> Use `nohup` when running long tasks over SSH that must continue if the connection drops.
> For production services, use `systemd` instead.

---

## 7.10 `nice` and `renice` — Process Priority

Controls the CPU scheduling priority of processes. Priority ranges from `-20` (highest) to `19` (lowest).

```bash
# nice — start a process with a specific priority
nice -n 10 ./backup.sh                # Start with low priority (nice value 10)
nice -n -5 ./critical-task.sh         # Start with higher priority (requires root)
nice -n 19 tar -czvf backup.tar.gz /  # Lowest priority (won't impact other processes)

# renice — change priority of a running process
renice -n 10 -p 1234                  # Lower priority of PID 1234
renice -n -5 -p 1234                  # Raise priority (requires root)
renice -n 15 -u mike                  # Lower priority of all mike's processes
renice -n 10 -g developers            # Lower priority of all processes in group

# View process priorities in top
top                                   # NI column shows nice value
ps -eo pid,ni,cmd                     # Show nice values with ps
```

> Use `nice` for resource-intensive background tasks (backups, compression) to prevent
> them from impacting production workloads.

---

## 7.11 `systemd` and `systemctl` — Service Management

`systemd` is the init system and service manager used by most modern Linux distributions
(Ubuntu 16.04+, CentOS 7+, RHEL 7+, Debian 8+). It manages system services (daemons)
and is the first process started at boot (PID 1).

### Basic Service Control

```bash
# Start, stop, restart services
sudo systemctl start nginx            # Start a service
sudo systemctl stop nginx             # Stop a service
sudo systemctl restart nginx          # Stop then start (full restart)
sudo systemctl reload nginx           # Reload config without stopping (graceful)
sudo systemctl try-restart nginx      # Restart only if currently running

# Enable/disable at boot
sudo systemctl enable nginx           # Start nginx automatically at boot
sudo systemctl disable nginx          # Prevent nginx from starting at boot
sudo systemctl enable --now nginx     # Enable AND start immediately
sudo systemctl disable --now nginx    # Disable AND stop immediately

# Check service status
systemctl status nginx                # Detailed status with recent logs
systemctl is-active nginx             # Returns 'active' or 'inactive'
systemctl is-enabled nginx            # Returns 'enabled' or 'disabled'
systemctl is-failed nginx             # Returns 'failed' if service crashed
```

**Output of `systemctl status nginx`:**
```
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2024-01-10 09:00:00 UTC; 2h 30min ago
       Docs: man:nginx(8)
    Process: 600 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
   Main PID: 601 (nginx)
      Tasks: 3 (limit: 4915)
     Memory: 12.5M
        CPU: 250ms
     CGroup: /system.slice/nginx.service
             ├─601 nginx: master process /usr/sbin/nginx
             ├─602 nginx: worker process
             └─603 nginx: worker process

Jan 10 09:00:00 server systemd[1]: Starting nginx...
Jan 10 09:00:00 server systemd[1]: Started nginx.
```

---

### Listing and Querying Services

```bash
systemctl list-units                          # List all active units
systemctl list-units --type=service           # List all active services
systemctl list-units --type=service --all     # List all services (active + inactive)
systemctl list-unit-files                     # List all installed unit files
systemctl list-unit-files --type=service      # List all service unit files
systemctl list-units --failed                 # List all failed services
systemctl list-dependencies nginx             # Show service dependencies
```

---

### System Power Management

```bash
sudo systemctl reboot                 # Reboot the system
sudo systemctl poweroff               # Shut down the system
sudo systemctl halt                   # Halt the system
sudo systemctl suspend                # Suspend the system
sudo systemctl hibernate              # Hibernate the system

# Reload systemd after editing unit files
sudo systemctl daemon-reload          # Reload systemd manager configuration
```

---

## 7.12 Creating a Custom systemd Service

You can create your own service unit file to manage any application with `systemctl`.

### Unit File Structure

```bash
sudo vim /etc/systemd/system/myapp.service
```

```ini
[Unit]
Description=My Custom Application
Documentation=https://myapp.example.com/docs
After=network.target                  # Start after network is available
Requires=postgresql.service           # Requires postgres to be running

[Service]
Type=simple                           # Process type (simple, forking, oneshot, notify)
User=appuser                          # Run as this user
Group=appuser                         # Run as this group
WorkingDirectory=/opt/myapp           # Working directory
ExecStart=/opt/myapp/bin/myapp        # Command to start the service
ExecReload=/bin/kill -HUP $MAINPID    # Command to reload config
ExecStop=/bin/kill -TERM $MAINPID     # Command to stop the service
Restart=on-failure                    # Restart if it crashes
RestartSec=5                          # Wait 5 seconds before restarting
StandardOutput=journal                # Send stdout to systemd journal
StandardError=journal                 # Send stderr to systemd journal
Environment=NODE_ENV=production       # Set environment variables
EnvironmentFile=/opt/myapp/.env       # Load env vars from a file

[Install]
WantedBy=multi-user.target            # Enable for multi-user (normal) boot
```

**Service Types:**
| Type | Description | Use Case |
|------|-------------|----------|
| `simple` | Main process is the service | Most applications |
| `forking` | Process forks and parent exits | Traditional daemons |
| `oneshot` | Process runs once and exits | Scripts, one-time tasks |
| `notify` | Process sends ready notification | Systemd-aware apps |
| `idle` | Starts after all jobs dispatched | Low-priority tasks |

**After creating the unit file:**
```bash
# 1. Reload systemd to recognize the new file
sudo systemctl daemon-reload

# 2. Enable and start the service
sudo systemctl enable --now myapp

# 3. Check status
systemctl status myapp
```

---

## 7.13 `journalctl` — View System Logs

`journalctl` queries the systemd journal — the centralized logging system for all services.

```bash
# Basic log viewing
journalctl                            # Show all journal logs (oldest first)
journalctl -r                         # Show logs in reverse (newest first)
journalctl -n 50                      # Show last 50 log entries
journalctl -f                         # Follow logs in real-time (like tail -f)

# Filter by service/unit
journalctl -u nginx                   # Show logs for nginx service
journalctl -u nginx -f                # Follow nginx logs in real-time
journalctl -u nginx -n 100            # Last 100 lines of nginx logs
journalctl -u nginx --since today     # Nginx logs from today

# Filter by time
journalctl --since "2024-01-10"                    # Logs since a date
journalctl --since "2024-01-10 09:00:00"           # Logs since a specific time
journalctl --until "2024-01-10 18:00:00"           # Logs until a specific time
journalctl --since "1 hour ago"                    # Logs from the last hour
journalctl --since yesterday --until today         # Yesterday's logs

# Filter by priority/severity
journalctl -p err                     # Show only errors
journalctl -p warning                 # Show warnings and above
journalctl -p debug                   # Show all including debug
journalctl -u nginx -p err            # Errors from nginx only

# Filter by user or PID
journalctl _UID=1000                  # Logs from processes run by UID 1000
journalctl _PID=1234                  # Logs from a specific PID

# Output formats
journalctl -u nginx -o json           # Output in JSON format
journalctl -u nginx -o short-precise  # Precise timestamps
journalctl -u nginx -o cat            # Clean output (message only)

# Disk usage
journalctl --disk-usage               # Show how much disk the journal uses
sudo journalctl --vacuum-size=500M    # Keep only 500MB of logs
sudo journalctl --vacuum-time=30d     # Keep only last 30 days of logs
```

**Priority levels (lowest to highest severity):**
| Level | Number | Description |
|-------|--------|-------------|
| `debug` | 7 | Debug messages |
| `info` | 6 | Informational |
| `notice` | 5 | Normal but significant |
| `warning` | 4 | Warning conditions |
| `err` | 3 | Error conditions |
| `crit` | 2 | Critical conditions |
| `alert` | 1 | Action must be taken immediately |
| `emerg` | 0 | System is unusable |

---

## 7.14 `lsof` — List Open Files

Lists all open files and the processes using them. In Linux, everything is a file
(sockets, pipes, devices, regular files).

```bash
lsof                                  # List all open files (very long output)
lsof -u mike                          # Files opened by user mike
lsof -p 1234                          # Files opened by PID 1234
lsof /var/log/nginx/access.log        # Which process has this file open
lsof -i                               # All network connections
lsof -i :80                           # Processes using port 80
lsof -i :443                          # Processes using port 443
lsof -i TCP                           # All TCP connections
lsof -i TCP:22                        # Processes using TCP port 22
lsof -i UDP                           # All UDP connections
lsof +D /var/www/                     # All files open under a directory
lsof -c nginx                         # Files opened by processes named nginx
```

**Example — Find what's using port 80:**
```bash
sudo lsof -i :80
# COMMAND  PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
# nginx    600 www-data   6u  IPv4  12345      0t0  TCP *:http (LISTEN)
```

---

## 7.15 `strace` — Trace System Calls

Traces system calls and signals made by a process — essential for deep debugging.

```bash
strace ls                             # Trace system calls made by ls
strace -p 1234                        # Attach to a running process
strace -e open,read,write ls          # Trace only specific system calls
strace -o trace.log ls                # Save trace output to a file
strace -c ls                          # Summary of system calls (count + time)
strace -f ./app                       # Follow child processes too
```

---

## 7.16 Practical Lab Exercises

### Exercise 1: Process Monitoring and Management
```bash
# 1. View all running processes sorted by CPU usage
ps -eo pid,%cpu,%mem,cmd --sort=-%cpu | head -10

# 2. Find the PID of sshd
pgrep -l sshd

# 3. Start a background sleep process
sleep 300 &

# 4. List background jobs
jobs -l

# 5. Bring it to foreground
fg %1

# 6. Pause it and send to background
# Press Ctrl+Z then:
bg %1

# 7. Kill the background sleep process
pkill sleep
```

### Exercise 2: Service Management with systemctl
```bash
# 1. Install and start Nginx
sudo apt install nginx -y             # Ubuntu
sudo systemctl start nginx
sudo systemctl enable nginx

# 2. Check service status
systemctl status nginx

# 3. View Nginx logs in real-time
journalctl -u nginx -f

# 4. Reload Nginx config (graceful — no downtime)
sudo systemctl reload nginx

# 5. Check if enabled at boot
systemctl is-enabled nginx

# 6. List all failed services
systemctl list-units --failed
```

### Exercise 3: Create a Custom Service
```bash
# 1. Create a simple application script
sudo tee /opt/myapp/app.sh << 'EOF'
#!/bin/bash
while true; do
    echo "$(date): App is running" >> /var/log/myapp.log
    sleep 10
done
EOF
sudo chmod +x /opt/myapp/app.sh

# 2. Create the service unit file
sudo tee /etc/systemd/system/myapp.service << 'EOF'
[Unit]
Description=My Custom App
After=network.target

[Service]
Type=simple
ExecStart=/opt/myapp/app.sh
Restart=on-failure
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
EOF

# 3. Enable and start the service
sudo systemctl daemon-reload
sudo systemctl enable --now myapp

# 4. Verify it's running
systemctl status myapp
journalctl -u myapp -f
```

### Exercise 4: Troubleshooting a Failed Service
```bash
# 1. Check for failed services
systemctl list-units --failed

# 2. Get detailed status of a failed service
systemctl status myapp

# 3. View the last 50 log lines for the service
journalctl -u myapp -n 50

# 4. View logs since the last boot
journalctl -u myapp -b

# 5. Check for errors specifically
journalctl -u myapp -p err

# 6. Attempt to restart and watch logs
sudo systemctl restart myapp
journalctl -u myapp -f
```

---

## 7.17 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `ps -ef` | List all processes (full format) |
| `ps -eo pid,%cpu,%mem,cmd --sort=-%cpu` | Sort processes by CPU |
| `top` | Real-time process monitor |
| `htop` | Enhanced real-time process monitor |
| `kill PID` | Gracefully terminate a process |
| `kill -9 PID` | Force kill a process |
| `killall nginx` | Kill all processes named nginx |
| `pkill -f "pattern"` | Kill processes matching a pattern |
| `pgrep nginx` | Get PID of nginx |
| `jobs` | List background jobs |
| `fg %1` | Bring job 1 to foreground |
| `bg %1` | Resume job 1 in background |
| `nohup cmd &` | Run command immune to logout |
| `nice -n 10 cmd` | Start command with low priority |
| `renice -n 10 -p PID` | Change priority of running process |
| `systemctl start svc` | Start a service |
| `systemctl stop svc` | Stop a service |
| `systemctl restart svc` | Restart a service |
| `systemctl reload svc` | Reload service config |
| `systemctl enable svc` | Enable service at boot |
| `systemctl disable svc` | Disable service at boot |
| `systemctl status svc` | Show service status |
| `systemctl list-units --failed` | List failed services |
| `systemctl daemon-reload` | Reload systemd after unit file changes |
| `journalctl -u svc` | View service logs |
| `journalctl -u svc -f` | Follow service logs live |
| `journalctl -u svc -p err` | Show only errors for a service |
| `journalctl --since "1 hour ago"` | Logs from last hour |
| `lsof -i :80` | Find process using port 80 |
| `lsof -p PID` | Files opened by a process |

---

## Summary

In this module, you learned:
- What a process is, its attributes, states, and how processes form a hierarchy
- How to inspect processes with `ps` using various output formats and sorting options
- How to monitor system resources in real-time with `top` and `htop`
- How to send signals to processes with `kill`, `killall`, and `pkill`
- How to find process IDs with `pgrep`
- How to manage background and foreground jobs with `&`, `jobs`, `fg`, `bg`, and `Ctrl+Z`
- How to keep processes running after logout with `nohup`
- How to control process CPU priority with `nice` and `renice`
- How to manage system services with `systemctl` (start, stop, restart, enable, disable)
- How to create custom `systemd` service unit files for your own applications
- How to query and filter system logs with `journalctl`
- How to inspect open files and network connections with `lsof`
- How to trace system calls for deep debugging with `strace`

---

> **Next Module →** Module 8: Disk & Storage Management (fdisk, lsblk, mount, df, LVM, fstab)
