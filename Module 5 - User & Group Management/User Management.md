# Module 5: User & Group Management

## Overview
Linux is a multi-user operating system, meaning many users can work on the same system at the same time.
As a DevOps engineer, you will regularly create and manage user accounts for developers, service accounts
for applications, and groups for team-based access control. Understanding user and group management is
essential for maintaining system security, enforcing the principle of least privilege, and managing
access to shared resources across servers and cloud infrastructure.

---

## 5.1 Understanding Users in Linux

Every process and file in Linux is associated with a user. Linux has three types of users:

| Type | UID Range | Description | Examples |
|------|-----------|-------------|---------|
| Root User | `0` | Superuser with full system access | `root` |
| System Users | `1–999` | Created by the OS for running services | `www-data`, `nginx`, `mysql` |
| Regular Users | `1000+` | Human users created by administrators | `mike`, `sarah`, `john` |

### Key User-Related Files

| File | Purpose |
|------|---------|
| `/etc/passwd` | Stores user account information |
| `/etc/shadow` | Stores encrypted user passwords |
| `/etc/group` | Stores group information |
| `/etc/gshadow` | Stores encrypted group passwords |
| `/etc/sudoers` | Defines sudo privileges |
| `/etc/skel/` | Template files copied to new user home directories |

---

## 5.2 Understanding `/etc/passwd`

Each line in `/etc/passwd` represents one user account with 7 colon-separated fields.

```bash
cat /etc/passwd
```

**Output format:**
```
mike:x:1000:1000:Mike Johnson,,,:/home/mike:/bin/bash
│    │ │    │    │               │           └── Login shell
│    │ │    │    │               └────────────── Home directory
│    │ │    │    └────────────────────────────── GECOS (full name/info)
│    │ │    └─────────────────────────────────── Primary GID
│    │ └──────────────────────────────────────── UID
│    └────────────────────────────────────────── Password (x = stored in /etc/shadow)
└─────────────────────────────────────────────── Username
```

---

## 5.3 Understanding `/etc/shadow`

Stores the encrypted passwords and password policy for each user.

```bash
sudo cat /etc/shadow
```

**Output format:**
```
mike:$6$xyz...hashed...password:19732:0:99999:7:::
│    │                          │     │ │     └── Inactive days after expiry
│    │                          │     │ └──────── Max password age (days)
│    │                          │     └────────── Min password age (days)
│    │                          └──────────────── Last password change (days since epoch)
│    └─────────────────────────────────────────── Encrypted password
└──────────────────────────────────────────────── Username
```

---

## 5.4 `useradd` — Create a New User

Creates a new user account on the system.

```bash
# Basic user creation
useradd john                          # Create user with default settings
useradd -m john                       # Create user with a home directory

# Full user creation with all options
useradd -m -s /bin/bash -c "John Doe" -u 1500 john
#        │   │            │            │      └── Username
#        │   │            │            └───────── Custom UID
#        │   │            └────────────────────── Comment/Full name (GECOS)
#        │   └─────────────────────────────────── Login shell
#        └─────────────────────────────────────── Create home directory

# Common flags
useradd -m -s /bin/bash john          # Create user with home dir and bash shell
useradd -m -G sudo,developers john    # Add user to supplementary groups at creation
useradd -r nginx                      # Create a system account (no home, no login)
useradd -e 2024-12-31 contractor      # Set account expiry date
useradd -d /opt/appuser appuser       # Set custom home directory path
useradd -u 1500 john                  # Assign a specific UID
```

> After creating a user with `useradd`, always set a password with `passwd username`.

---

## 5.5 `passwd` — Set or Change User Password

Sets or updates the password for a user account.

```bash
passwd                                # Change your own password
passwd john                           # Change another user's password (root only)
passwd -l john                        # Lock a user account (disable login)
passwd -u john                        # Unlock a user account
passwd -e john                        # Expire password (force change on next login)
passwd -d john                        # Delete password (passwordless login — avoid!)
passwd -n 7 john                      # Minimum 7 days before password can be changed
passwd -x 90 john                     # Password expires after 90 days
passwd -w 14 john                     # Warn user 14 days before password expires
passwd --status john                  # Show password status for a user
```

**Output of `passwd --status john`:**
```
john P 2024-01-10 7 90 14 -1
#    │ │          │ │  │  └── Inactive period
#    │ │          │ │  └───── Warning period (days)
#    │ │          │ └──────── Max password age
#    │ │          └────────── Last change date
#    │ └───────────────────── P=Password set, L=Locked, NP=No password
#    └───────────────────────  Username
```

---

## 5.6 `usermod` — Modify an Existing User

Modifies the properties of an existing user account.

```bash
# Change username
usermod -l newname oldname            # Rename user from oldname to newname

# Change home directory
usermod -d /new/home/path john        # Change home directory path
usermod -d /new/home/path -m john     # Change and move existing home directory contents

# Change login shell
usermod -s /bin/bash john             # Set bash as login shell
usermod -s /sbin/nologin john         # Disable interactive login (service accounts)
usermod -s /bin/false john            # Disable login (alternative)

# Manage group memberships
usermod -aG sudo john                 # Add john to sudo group (keep existing groups)
usermod -aG docker,developers john    # Add to multiple groups
usermod -g developers john            # Change primary group
usermod -G developers john            # Set supplementary groups (REPLACES existing!)

# Account control
usermod -L john                       # Lock user account
usermod -U john                       # Unlock user account
usermod -e 2024-12-31 john            # Set account expiry date
usermod -e "" john                    # Remove account expiry date
usermod -u 1600 john                  # Change UID
usermod -c "John Doe - DevOps" john   # Update comment/full name
```

> ⚠️ Always use `-aG` (append + group) when adding a user to groups. Using `-G` alone **replaces** all existing supplementary groups.

---

## 5.7 `userdel` — Delete a User

Removes a user account from the system.

```bash
userdel john                          # Delete user (keeps home directory)
userdel -r john                       # Delete user AND their home directory and mail spool
userdel -f john                       # Force delete even if user is currently logged in
```

**Safe deletion workflow:**
```bash
# 1. Check if user is logged in
who | grep john
w john

# 2. Check user's processes
ps -u john

# 3. Kill user's processes if needed
pkill -u john

# 4. Backup home directory before deletion
tar -czvf /backup/john-home-$(date +%F).tar.gz /home/john/

# 5. Delete the user and home directory
userdel -r john
```

---

## 5.8 `id` — Display User Identity

Shows the UID, GID, and group memberships of a user.

```bash
id                                    # Show current user's identity
id john                               # Show identity of a specific user
id -u john                            # Show only UID
id -g john                            # Show only primary GID
id -G john                            # Show all GIDs
id -Gn john                           # Show all group names
```

**Output:**
```
uid=1000(mike) gid=1000(mike) groups=1000(mike),27(sudo),998(docker),1001(developers)
```

---

## 5.9 `whoami` and `who` — Current User Info

```bash
whoami                                # Print the current logged-in username
who                                   # Show all logged-in users
who -a                                # Show all details (login time, PID, etc.)
w                                     # Show logged-in users and what they are doing
last                                  # Show login history
last john                             # Show login history for a specific user
lastlog                               # Show last login for all users
```

**Output of `w`:**
```
USER     TTY      FROM             LOGIN@   IDLE JCPU   PCPU WHAT
mike     pts/0    192.168.1.10     09:00    0.00s 0.05s  0.01s bash
sarah    pts/1    192.168.1.15     09:15    2:00  0.03s  0.00s vim app.conf
```

---

## 5.10 Group Management

### `groupadd` — Create a New Group

```bash
groupadd developers                   # Create a new group
groupadd -g 1500 devops               # Create group with a specific GID
groupadd -r docker                    # Create a system group
```

---

### `groupmod` — Modify a Group

```bash
groupmod -n newname oldname           # Rename a group
groupmod -g 1600 developers           # Change group GID
```

---

### `groupdel` — Delete a Group

```bash
groupdel developers                   # Delete a group
```

> You cannot delete a group that is the primary group of any user. Remove or reassign those users first.

---

### `groups` — Show Group Memberships

```bash
groups                                # Show groups of current user
groups john                           # Show groups of a specific user
```

**Output:**
```
john : john sudo docker developers
```

---

### `/etc/group` File Structure

```bash
cat /etc/group
```

**Output format:**
```
developers:x:1001:mike,sarah,john
│          │ │    └── Group members (comma-separated)
│          │ └──────── GID
│          └────────── Password (x = stored in /etc/gshadow)
└───────────────────── Group name
```

---

## 5.11 `sudo` — Execute Commands as Another User

`sudo` (superuser do) allows permitted users to run commands with elevated (root) privileges
without logging in as root.

```bash
sudo command                          # Run a command as root
sudo -u john command                  # Run a command as another user
sudo -i                               # Open an interactive root shell
sudo su                               # Switch to root user
sudo su - john                        # Switch to another user's environment
sudo !!                               # Re-run the last command with sudo
sudo -l                               # List commands the current user can run with sudo
sudo -l -U john                       # List sudo privileges for a specific user
```

---

### Configuring `sudo` with `/etc/sudoers`

Always edit the sudoers file using `visudo` — it validates syntax before saving.

```bash
sudo visudo                           # Safely edit the sudoers file
```

**Sudoers file syntax:**
```bash
# Format: user  host=(run_as_user)  commands

# Give a user full sudo access
john    ALL=(ALL:ALL) ALL

# Give a user sudo without password prompt
john    ALL=(ALL) NOPASSWD: ALL

# Allow a user to run only specific commands
john    ALL=(ALL) /usr/bin/systemctl, /usr/bin/apt

# Give a group sudo access
%sudo   ALL=(ALL:ALL) ALL
%developers ALL=(ALL) /usr/bin/systemctl restart nginx

# Allow a user to run a command as a specific user
john    ALL=(www-data) /usr/bin/php
```

---

### Adding Users to the sudo Group

```bash
# Ubuntu/Debian — add to sudo group
usermod -aG sudo john

# RHEL/CentOS/AlmaLinux — add to wheel group
usermod -aG wheel john

# Verify
sudo -l -U john
```

---

## 5.12 Switching Users

### `su` — Switch User

```bash
su john                               # Switch to user john (keeps current environment)
su - john                             # Switch to john with their full environment (login shell)
su -                                  # Switch to root with full environment
su -c "command" john                  # Run a single command as john
```

> The `-` flag in `su -` loads the target user's environment variables, PATH, and shell config.
> Always use `su -` instead of `su` to avoid environment conflicts.

---

## 5.13 Password Policies with `chage`

`chage` manages password aging and expiry policies for user accounts.

```bash
chage -l john                         # List password aging info for john
chage -d 0 john                       # Force password change on next login
chage -M 90 john                      # Set maximum password age to 90 days
chage -m 7 john                       # Set minimum password age to 7 days
chage -W 14 john                      # Warn 14 days before password expires
chage -I 30 john                      # Lock account 30 days after password expires
chage -E 2024-12-31 john              # Set account expiry date
chage -E -1 john                      # Remove account expiry date
```

**Output of `chage -l john`:**
```
Last password change                  : Jan 10, 2024
Password expires                      : Apr 09, 2024
Password inactive                     : never
Account expires                       : never
Minimum number of days between change : 7
Maximum number of days between change : 90
Number of days of warning before expiry: 14
```

---

## 5.14 Monitoring User Activity

```bash
# Currently logged-in users
who                                   # Basic list of logged-in users
w                                     # Detailed list with activity
users                                 # Just the usernames

# Login history
last                                  # Full login/logout history
last -n 20                            # Last 20 login events
last john                             # Login history for john
lastb                                 # Failed login attempts (bad logins)
lastlog                               # Last login time for all users

# User processes
ps -u john                            # All processes owned by john
top -u john                           # Monitor john's processes in real-time

# Audit user commands (if auditd is installed)
ausearch -ua john                     # Search audit logs for john's activity
```

---

## 5.15 Service Account Best Practices

Service accounts are system users created for running applications (e.g., nginx, mysql, jenkins).

```bash
# Create a service account (no home directory, no login shell)
useradd -r -s /sbin/nologin -c "Nginx Service Account" nginx

# Create with a specific home directory for app data
useradd -r -s /sbin/nologin -d /opt/myapp -m appuser

# Verify the account cannot log in
su - nginx
# This account is currently not available.

# Assign application files to the service account
chown -R appuser:appuser /opt/myapp/
chmod -R 750 /opt/myapp/
```

**Service account checklist:**
- Use `-r` flag (system account, UID < 1000)
- Set shell to `/sbin/nologin` or `/bin/false`
- Only give access to directories the service needs
- Never give service accounts sudo privileges

---

## 5.16 Practical Lab Exercises

### Exercise 1: Create and Configure a New User
```bash
# 1. Create a new user with home directory and bash shell
sudo useradd -m -s /bin/bash -c "DevOps Engineer" devuser

# 2. Set a password
sudo passwd devuser

# 3. Add to developers and docker groups
sudo usermod -aG developers,docker devuser

# 4. Verify the user's identity and groups
id devuser

# 5. Set password policy (90-day expiry, 14-day warning)
sudo chage -M 90 -W 14 devuser

# 6. Verify password policy
sudo chage -l devuser
```

### Exercise 2: Group-Based Access Control
```bash
# 1. Create a project group
sudo groupadd project-alpha

# 2. Create two users and add them to the group
sudo useradd -m -s /bin/bash -G project-alpha alice
sudo useradd -m -s /bin/bash -G project-alpha bob
sudo passwd alice
sudo passwd bob

# 3. Create a shared project directory
sudo mkdir /opt/project-alpha

# 4. Assign group ownership and set SGID
sudo chown root:project-alpha /opt/project-alpha
sudo chmod 2775 /opt/project-alpha

# 5. Verify both users can create files in the shared directory
su - alice -c "touch /opt/project-alpha/alice-file.txt"
su - bob -c "touch /opt/project-alpha/bob-file.txt"

# 6. Verify files inherit the group
ls -l /opt/project-alpha/
```

### Exercise 3: Sudo Access Management
```bash
# 1. Create a junior admin user
sudo useradd -m -s /bin/bash junioradmin
sudo passwd junioradmin

# 2. Allow junioradmin to restart services only (no full sudo)
sudo visudo
# Add this line:
# junioradmin ALL=(ALL) /usr/bin/systemctl restart *, /usr/bin/systemctl status *

# 3. Test the restricted sudo access
su - junioradmin
sudo systemctl status nginx          # Should work
sudo apt install vim                 # Should be denied

# 4. Verify sudo permissions
sudo -l -U junioradmin
```

### Exercise 4: Account Cleanup
```bash
# 1. Lock an account (user leaving temporarily)
sudo passwd -l olduser
sudo usermod -L olduser

# 2. Verify account is locked
sudo passwd --status olduser

# 3. Backup home directory
sudo tar -czvf /backup/olduser-$(date +%F).tar.gz /home/olduser/

# 4. Delete the user and home directory
sudo userdel -r olduser

# 5. Verify deletion
id olduser                           # Should return: no such user
ls /home/                            # Home directory should be gone
```

---

## 5.17 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `useradd -m -s /bin/bash user` | Create user with home dir and bash shell |
| `passwd user` | Set or change user password |
| `usermod -aG group user` | Add user to a group (keep existing groups) |
| `usermod -s /sbin/nologin user` | Disable user login |
| `usermod -L user` | Lock user account |
| `usermod -U user` | Unlock user account |
| `userdel -r user` | Delete user and home directory |
| `id user` | Show user UID, GID, and groups |
| `whoami` | Show current username |
| `who` | Show logged-in users |
| `w` | Show users and their activity |
| `last` | Show login history |
| `lastb` | Show failed login attempts |
| `groupadd group` | Create a new group |
| `groupdel group` | Delete a group |
| `groupmod -n new old` | Rename a group |
| `groups user` | Show user's group memberships |
| `sudo visudo` | Safely edit sudoers file |
| `sudo -l` | List current user's sudo permissions |
| `su - user` | Switch to another user with their environment |
| `chage -l user` | Show password aging info |
| `chage -M 90 user` | Set 90-day password expiry |
| `chage -d 0 user` | Force password change on next login |

---

## Summary

In this module, you learned:
- The three types of Linux users (root, system, regular) and their UID ranges
- The structure of `/etc/passwd`, `/etc/shadow`, and `/etc/group` files
- How to create users with `useradd` and set passwords with `passwd`
- How to modify user accounts with `usermod` including group membership management
- How to safely delete users with `userdel`
- How to create and manage groups with `groupadd`, `groupmod`, and `groupdel`
- How to grant and manage elevated privileges with `sudo` and `/etc/sudoers`
- How to switch between users with `su`
- How to enforce password policies with `chage`
- How to monitor user activity with `who`, `w`, `last`, and `lastlog`
- Best practices for creating service accounts for applications

---

> **Next Module →** Module 6: Package Management (apt, yum/dnf, rpm, snap, managing repositories)
