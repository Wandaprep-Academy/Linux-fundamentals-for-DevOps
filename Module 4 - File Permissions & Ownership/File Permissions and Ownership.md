# Module 4: File Permissions & Ownership

## Overview
Linux is a multi-user operating system, meaning multiple users can access the same system simultaneously.
File permissions and ownership are the security mechanism that controls **who can read, write, or execute**
a file or directory. As a DevOps engineer, understanding permissions is critical — misconfigured permissions
are one of the most common causes of application failures, security breaches, and deployment issues.

---

## 4.1 Understanding File Permissions

Every file and directory in Linux has three types of permissions assigned to three categories of users.

### Permission Categories (Who)

| Category | Symbol | Description |
|----------|--------|-------------|
| Owner (User) | `u` | The user who created/owns the file |
| Group | `g` | Users who belong to the file's assigned group |
| Others | `o` | Everyone else on the system |
| All | `a` | Owner + Group + Others combined |

### Permission Types (What)

| Permission | Symbol | Octal | On a File | On a Directory |
|------------|--------|-------|-----------|----------------|
| Read | `r` | `4` | View file contents | List directory contents |
| Write | `w` | `2` | Modify file contents | Create/delete files inside |
| Execute | `x` | `1` | Run as a program/script | Enter (cd into) the directory |
| No permission | `-` | `0` | No access | No access |

---

## 4.2 Reading Permission Strings

When you run `ls -l`, each file shows a 10-character permission string:

```bash
ls -l
```

**Output:**
```
-rwxr-xr--  1  mike  developers  4096  Jan 10  deploy.sh
drwxr-xr-x  2  mike  developers  4096  Jan 10  projects/
lrwxrwxrwx  1  root  root          20  Jan 10  nginx -> /opt/nginx/bin/nginx
```

### Breaking Down the Permission String

```
- r w x r - x r - -
│ │─┤ │─┤ │─┤
│  │   │   └── Others:  r-- (read only)
│  │   └─────── Group:   r-x (read + execute)
│  └─────────── Owner:   rwx (read + write + execute)
└────────────── File type: - (file), d (directory), l (symlink)
```

### File Type Characters

| Character | Meaning |
|-----------|---------|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device |
| `b` | Block device |
| `p` | Named pipe |
| `s` | Socket |

---

## 4.3 Octal (Numeric) Permission Notation

Each permission set (owner, group, others) can be represented as a number from 0–7
by adding the values of the permissions assigned.

### Octal Value Table

| Permission | Value |
|------------|-------|
| `r` (read) | 4 |
| `w` (write) | 2 |
| `x` (execute) | 1 |
| `-` (none) | 0 |

### Common Permission Combinations

| Octal | Symbolic | Meaning |
|-------|----------|---------|
| `7` | `rwx` | Read + Write + Execute |
| `6` | `rw-` | Read + Write |
| `5` | `r-x` | Read + Execute |
| `4` | `r--` | Read only |
| `3` | `-wx` | Write + Execute |
| `2` | `-w-` | Write only |
| `1` | `--x` | Execute only |
| `0` | `---` | No permissions |

### Real-World Octal Examples

| Octal | Symbolic | Use Case |
|-------|----------|----------|
| `755` | `rwxr-xr-x` | Scripts, directories (owner full, others read+execute) |
| `644` | `rw-r--r--` | Config files (owner read+write, others read only) |
| `600` | `rw-------` | Private keys, sensitive files (owner only) |
| `777` | `rwxrwxrwx` | Full access for everyone (avoid in production!) |
| `700` | `rwx------` | Private scripts (owner only) |
| `400` | `r--------` | Read-only sensitive files (e.g., SSH private key) |

---

## 4.4 `chmod` — Change File Permissions

Changes the permissions of a file or directory.

### Symbolic Mode (Using Letters)

```bash
# Syntax: chmod [who][operator][permission] file

# Adding permissions
chmod u+x deploy.sh           # Add execute permission for the owner
chmod g+w file.txt            # Add write permission for the group
chmod o+r file.txt            # Add read permission for others
chmod a+x script.sh           # Add execute for everyone (owner, group, others)

# Removing permissions
chmod u-x deploy.sh           # Remove execute from owner
chmod g-w file.txt            # Remove write from group
chmod o-rwx file.txt          # Remove all permissions from others

# Setting exact permissions (replaces existing)
chmod u=rwx,g=rx,o=r file.sh  # Owner: rwx, Group: r-x, Others: r--
chmod a=r file.txt            # Set read-only for everyone

# Recursive (apply to directory and all contents)
chmod -R 755 /var/www/html/   # Apply 755 to directory and everything inside
```

### Numeric (Octal) Mode

```bash
chmod 755 deploy.sh           # rwxr-xr-x  (standard for scripts)
chmod 644 config.txt          # rw-r--r--  (standard for config files)
chmod 600 ~/.ssh/id_rsa       # rw-------  (SSH private key)
chmod 400 secret.key          # r--------  (read-only sensitive file)
chmod 777 /tmp/shared/        # rwxrwxrwx  (full access — avoid in production)
chmod 700 ~/private/          # rwx------  (private directory)

# Recursive
chmod -R 755 /opt/myapp/      # Apply to all files and subdirectories
chmod -R 644 /etc/nginx/      # Apply to all nginx config files
```

**Example — Fix a common deployment issue:**
```bash
# Script won't run? Add execute permission
chmod +x /opt/myapp/start.sh

# Web server can't read files? Fix permissions
chmod -R 644 /var/www/html/
chmod -R 755 /var/www/html/   # Directories need execute to be entered
```

---

## 4.5 `chown` — Change File Ownership

Changes the owner and/or group of a file or directory.

```bash
# Syntax: chown [owner][:group] file

# Change owner only
chown mike file.txt                   # Change owner to 'mike'
chown mike /opt/myapp/                # Change owner of a directory

# Change owner and group
chown mike:developers file.txt        # Change owner to 'mike', group to 'developers'
chown mike:mike deploy.sh             # Set both owner and group to 'mike'

# Change group only
chown :developers file.txt            # Change only the group (note the colon prefix)

# Recursive
chown -R mike:developers /opt/myapp/  # Apply to directory and all contents
chown -R www-data:www-data /var/www/  # Give web server ownership of web files

# Preserve root ownership but change group
chown -R root:docker /var/run/docker.sock
```

**Example — Fix a web server permission issue:**
```bash
# Nginx runs as www-data user — give it ownership of web files
chown -R www-data:www-data /var/www/html/
chmod -R 755 /var/www/html/
```

---

## 4.6 `chgrp` — Change Group Ownership

Changes only the group ownership of a file or directory.

```bash
chgrp developers file.txt             # Change group to 'developers'
chgrp -R devops /opt/project/         # Recursively change group
chgrp docker /var/run/docker.sock     # Allow docker group to access socket
```

> `chgrp` is equivalent to `chown :groupname file` — use whichever is clearer in your scripts.

---

## 4.7 `umask` — Default Permission Mask

`umask` defines the default permissions that are **subtracted** when new files and directories are created.

```bash
umask                                 # Show current umask value
umask 022                             # Set umask to 022
umask -S                              # Show umask in symbolic format
```

### How umask Works

```
New file base permissions:    666  (rw-rw-rw-)
umask value:                - 022  (----w--w-)
                            ─────
Resulting permissions:        644  (rw-r--r--)

New directory base permissions:  777  (rwxrwxrwx)
umask value:                   - 022  (----w--w-)
                               ─────
Resulting permissions:           755  (rwxr-xr-x)
```

### Common umask Values

| umask | Files Created As | Directories Created As | Use Case |
|-------|-----------------|----------------------|----------|
| `022` | `644` (rw-r--r--) | `755` (rwxr-xr-x) | Default for most systems |
| `027` | `640` (rw-r-----) | `750` (rwxr-x---) | More restrictive (group no write, others no access) |
| `077` | `600` (rw-------) | `700` (rwx------) | Highly secure (owner only) |
| `002` | `664` (rw-rw-r--) | `775` (rwxrwxr-x) | Collaborative environments |

**Set umask permanently** by adding it to `~/.bashrc` or `/etc/profile`:
```bash
echo "umask 027" >> ~/.bashrc
source ~/.bashrc
```

---

## 4.8 Special Permissions

Beyond the standard `rwx`, Linux has three special permission bits.

### SUID — Set User ID (Octal: 4000)
When set on an executable, it runs with the **owner's privileges** instead of the caller's.

```bash
chmod u+s /usr/bin/passwd             # Set SUID
chmod 4755 /usr/bin/passwd            # Set SUID using octal

# Identify SUID files
ls -l /usr/bin/passwd
# -rwsr-xr-x  root  root  passwd      ← 's' in owner execute position = SUID

# Find all SUID files on the system (security audit)
find / -perm -4000 -type f 2>/dev/null
```

> The `passwd` command uses SUID so regular users can change their own password (which requires writing to `/etc/shadow`, owned by root).

---

### SGID — Set Group ID (Octal: 2000)
On an executable: runs with the **group's privileges**.
On a directory: new files created inside **inherit the directory's group**.

```bash
chmod g+s /opt/shared/                # Set SGID on a directory
chmod 2755 /opt/shared/               # Set SGID using octal

# Identify SGID files/directories
ls -l /opt/shared/
# drwxr-sr-x  mike  developers  shared/   ← 's' in group execute position = SGID

# Find all SGID files
find / -perm -2000 -type f 2>/dev/null
```

> SGID on shared directories ensures all files created inside belong to the same group — useful for team collaboration.

---

### Sticky Bit (Octal: 1000)
When set on a directory, **only the file owner** (or root) can delete or rename files inside,
even if others have write permission.

```bash
chmod +t /tmp/                        # Set sticky bit
chmod 1777 /tmp/                      # Set sticky bit using octal

# Identify sticky bit
ls -ld /tmp/
# drwxrwxrwt  root  root  /tmp/       ← 't' in others execute position = sticky bit

# Find all directories with sticky bit
find / -perm -1000 -type d 2>/dev/null
```

> `/tmp` always has the sticky bit set — this prevents users from deleting each other's temporary files.

---

### Special Permissions Summary

| Permission | Octal | Symbol | Effect on File | Effect on Directory |
|------------|-------|--------|----------------|---------------------|
| SUID | `4000` | `s` in owner execute | Runs as file owner | No effect |
| SGID | `2000` | `s` in group execute | Runs as file group | New files inherit group |
| Sticky Bit | `1000` | `t` in others execute | No effect | Only owner can delete files |

---

## 4.9 Access Control Lists (ACLs)

Standard Linux permissions only allow one owner and one group per file.
ACLs extend this by allowing **fine-grained permissions for specific users or groups**.

```bash
# Install ACL tools if needed
sudo apt install acl                  # Ubuntu/Debian
sudo yum install acl                  # RHEL/CentOS

# View ACL of a file
getfacl file.txt

# Set ACL for a specific user
setfacl -m u:john:rw file.txt         # Give user 'john' read+write
setfacl -m u:sarah:r file.txt         # Give user 'sarah' read only

# Set ACL for a specific group
setfacl -m g:developers:rwx /opt/project/   # Give developers full access

# Set default ACL on a directory (inherited by new files)
setfacl -d -m g:developers:rw /opt/project/

# Remove a specific ACL entry
setfacl -x u:john file.txt            # Remove john's ACL entry

# Remove all ACL entries
setfacl -b file.txt                   # Remove all ACLs from file
```

**Output of `getfacl file.txt`:**
```
# file: file.txt
# owner: mike
# group: developers
user::rw-
user:john:rw-
user:sarah:r--
group::r--
mask::rw-
other::r--
```

> ACLs are essential in enterprise environments where multiple teams need different levels of access to the same files.

---

## 4.10 Viewing Permissions and Ownership

```bash
ls -l file.txt                        # View permissions of a file
ls -la /etc/nginx/                    # View all files including hidden
ls -ld /opt/myapp/                    # View directory's own permissions (not contents)
stat file.txt                         # Detailed file info including permissions in octal
```

**Output of `stat file.txt`:**
```
  File: file.txt
  Size: 1234
Access: (0644/-rw-r--r--)  Uid: (1000/mike)   Gid: (1001/developers)
```

---

## 4.11 Practical Lab Exercises

### Exercise 1: Setting Up a Secure Web Directory
```bash
# 1. Create a web project directory
sudo mkdir -p /var/www/myapp/{html,logs,config}

# 2. Create a sample index file
echo "<h1>My App</h1>" | sudo tee /var/www/myapp/html/index.html

# 3. Give the web server (www-data) ownership
sudo chown -R www-data:www-data /var/www/myapp/

# 4. Set correct permissions
sudo chmod -R 755 /var/www/myapp/         # Directories: rwxr-xr-x
sudo chmod -R 644 /var/www/myapp/html/    # HTML files: rw-r--r--
sudo chmod -R 640 /var/www/myapp/config/  # Config files: rw-r-----

# 5. Verify
ls -la /var/www/myapp/
```

### Exercise 2: Securing SSH Keys
```bash
# 1. Generate an SSH key pair
ssh-keygen -t rsa -b 4096 -f ~/.ssh/mykey

# 2. Set correct permissions (required by SSH)
chmod 700 ~/.ssh/                     # Only owner can access .ssh directory
chmod 600 ~/.ssh/mykey                # Private key: owner read+write only
chmod 644 ~/.ssh/mykey.pub            # Public key: everyone can read

# 3. Verify
ls -la ~/.ssh/
```

### Exercise 3: Shared Team Directory with SGID
```bash
# 1. Create a shared directory for a team
sudo mkdir /opt/devops-team

# 2. Create a group and assign it
sudo groupadd devops
sudo chown root:devops /opt/devops-team

# 3. Set SGID so all new files inherit the group
sudo chmod 2775 /opt/devops-team

# 4. Verify
ls -ld /opt/devops-team
# drwxrwsr-x  root  devops  /opt/devops-team

# 5. Add users to the group
sudo usermod -aG devops mike
sudo usermod -aG devops sarah
```

### Exercise 4: Finding Permission Issues
```bash
# Find world-writable files (security risk)
find /var/www -perm -o+w -type f

# Find files with no owner (orphaned files)
find / -nouser -type f 2>/dev/null

# Find all SUID binaries (security audit)
find / -perm -4000 -type f 2>/dev/null

# Fix permissions recursively for a web app
find /var/www/myapp -type f -exec chmod 644 {} \;
find /var/www/myapp -type d -exec chmod 755 {} \;
```

---

## 4.12 Common Permission Issues and Fixes

| Problem | Symptom | Fix |
|---------|---------|-----|
| Script won't run | `Permission denied` | `chmod +x script.sh` |
| Web server can't read files | `403 Forbidden` | `chmod -R 644 /var/www/html/` |
| Can't enter a directory | `Permission denied` on `cd` | `chmod +x directory/` |
| SSH key rejected | `WARNING: UNPROTECTED PRIVATE KEY FILE` | `chmod 600 ~/.ssh/id_rsa` |
| App can't write logs | `Permission denied` on log write | `chown appuser:appuser /var/log/app/` |
| Can't delete file in shared dir | `Permission denied` | Check sticky bit or ownership |

---

## 4.13 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `ls -l` | View file permissions |
| `ls -ld dir/` | View directory's own permissions |
| `chmod 755 file` | Set permissions using octal |
| `chmod u+x file` | Add execute for owner |
| `chmod -R 644 dir/` | Recursively set permissions |
| `chown user file` | Change file owner |
| `chown user:group file` | Change owner and group |
| `chown -R user:group dir/` | Recursively change ownership |
| `chgrp group file` | Change group only |
| `umask` | Show default permission mask |
| `umask 022` | Set default permission mask |
| `chmod u+s file` | Set SUID bit |
| `chmod g+s dir/` | Set SGID bit on directory |
| `chmod +t dir/` | Set sticky bit |
| `getfacl file` | View ACL permissions |
| `setfacl -m u:user:rw file` | Set ACL for a user |
| `setfacl -b file` | Remove all ACLs |
| `stat file` | View permissions in octal |
| `find / -perm -4000` | Find all SUID files |

---

## Summary

In this module, you learned:
- How Linux permission strings work and how to read them
- The three permission types (read, write, execute) and three user categories (owner, group, others)
- How to use octal and symbolic notation to represent permissions
- How to change permissions with `chmod` using both symbolic and numeric modes
- How to change file ownership with `chown` and `chgrp`
- How `umask` controls default permissions for new files and directories
- The three special permissions: SUID, SGID, and Sticky Bit — what they do and when to use them
- How to use ACLs with `getfacl` and `setfacl` for fine-grained access control
- How to diagnose and fix common permission-related issues

---

> **Next Module →** Module 5: User & Group Management (useradd, usermod, groupadd, passwd, sudo)
