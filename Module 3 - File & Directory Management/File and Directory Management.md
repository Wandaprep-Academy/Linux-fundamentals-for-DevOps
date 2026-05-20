# Module 3: File & Directory Management

## Overview
Managing files and directories is one of the most frequent tasks a DevOps engineer performs daily —
whether it's creating configuration files, organizing project directories, copying deployment artifacts,
or safely removing old logs. This module covers every essential command you need to create, copy,
move, rename, delete, and archive files and directories on a Linux system.

---

## 3.1 Creating Files and Directories

### `touch` — Create an Empty File or Update Timestamps
Creates a new empty file. If the file already exists, it updates its access and modification timestamps.

```bash
touch file.txt                    # Create a single empty file
touch file1.txt file2.txt         # Create multiple files at once
touch -t 202401010900 file.txt    # Set a specific timestamp (YYYYMMDDhhmm)
touch -a file.txt                 # Update only the access time
touch -m file.txt                 # Update only the modification time
```

> Commonly used in scripts to create placeholder files or trigger file-watcher pipelines.

---

### `mkdir` — Create Directories
Creates one or more new directories.

```bash
mkdir projects                        # Create a single directory
mkdir dir1 dir2 dir3                  # Create multiple directories at once
mkdir -p projects/app/config          # Create nested directories (no error if exists)
mkdir -m 755 secure-dir               # Create directory with specific permissions
mkdir -pv /opt/myapp/logs             # Create with verbose output (-v shows what was created)
```

**Output of `mkdir -pv`:**
```
mkdir: created directory '/opt/myapp'
mkdir: created directory '/opt/myapp/logs'
```

> Always use `-p` in scripts to avoid errors when the directory already exists.

---

## 3.2 Copying Files and Directories

### `cp` — Copy Files and Directories
Copies files or directories from one location to another.

```bash
# Copying files
cp file.txt backup.txt                # Copy file to a new name in same directory
cp file.txt /home/mike/              # Copy file to another directory
cp file1.txt file2.txt /backup/      # Copy multiple files to a directory

# Copying directories
cp -r projects/ /backup/             # Recursively copy a directory and its contents
cp -rp projects/ /backup/            # Copy and preserve permissions, timestamps, ownership

# Useful flags
cp -i file.txt /backup/              # Interactive: prompt before overwriting
cp -u file.txt /backup/              # Copy only if source is newer than destination
cp -v file.txt /backup/              # Verbose: show what is being copied
cp -a projects/ /backup/             # Archive mode: preserves all attributes (same as -rp --links)
```

**Example — Backup a config file before editing:**
```bash
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
```

> Always back up config files with `cp` before making changes — a critical DevOps habit.

---

## 3.3 Moving and Renaming Files

### `mv` — Move or Rename Files and Directories
Moves files/directories to a new location or renames them.

```bash
# Renaming
mv old-name.txt new-name.txt          # Rename a file
mv old-dir/ new-dir/                  # Rename a directory

# Moving
mv file.txt /home/mike/documents/     # Move file to another directory
mv dir1/ /opt/                        # Move a directory to another location
mv file1.txt file2.txt /backup/       # Move multiple files to a directory

# Useful flags
mv -i file.txt /backup/               # Interactive: prompt before overwriting
mv -u file.txt /backup/               # Move only if source is newer
mv -v file.txt /backup/               # Verbose: show what is being moved
```

**Example — Rotate a log file:**
```bash
mv /var/log/app.log /var/log/app.log.$(date +%F)
touch /var/log/app.log
```

> `mv` does not duplicate data — it just updates the file's path reference, making it very fast even for large files on the same filesystem.

---

## 3.4 Deleting Files and Directories

### `rm` — Remove Files and Directories
Deletes files and directories permanently. There is no recycle bin in Linux.

```bash
# Removing files
rm file.txt                           # Delete a single file
rm file1.txt file2.txt                # Delete multiple files
rm *.log                              # Delete all .log files in current directory

# Removing directories
rm -r projects/                       # Recursively delete a directory and its contents
rm -rf /tmp/old-build/                # Force delete without prompts (use with caution!)

# Safe deletion flags
rm -i file.txt                        # Interactive: ask before each deletion
rm -I *.log                           # Ask once before deleting more than 3 files
rm -v file.txt                        # Verbose: show what is being deleted
```

> ⚠️ **WARNING**: `rm -rf` is irreversible. Never run `rm -rf /` or `rm -rf /*` — it will wipe the entire system.
> Always double-check the path before running `rm -rf`.

**Safe practice — preview before deleting:**
```bash
# First, list what will be deleted
ls /tmp/old-build/

# Then delete
rm -rf /tmp/old-build/
```

---

### `rmdir` — Remove Empty Directories
Removes only empty directories. Safer than `rm -r`.

```bash
rmdir empty-dir/                      # Remove a single empty directory
rmdir dir1/ dir2/                     # Remove multiple empty directories
rmdir -p a/b/c/                       # Remove nested empty directories
```

> Use `rmdir` when you want to ensure you only delete empty directories — it will fail if the directory has contents.

---

## 3.5 Viewing and Reading Files

### `cat` — Display File Contents
Displays the entire content of a file to the terminal.

```bash
cat file.txt                          # Display file content
cat -n file.txt                       # Show content with line numbers
cat -A file.txt                       # Show hidden characters (tabs, line endings)
cat file1.txt file2.txt               # Display multiple files in sequence
cat file1.txt file2.txt > merged.txt  # Merge two files into one
cat >> file.txt                       # Append typed input to a file (Ctrl+D to stop)
```

---

### `less` — Scroll Through File Content
Opens a file for interactive, page-by-page reading. Best for large files.

```bash
less /var/log/syslog                  # Open a large log file
less +G /var/log/syslog               # Open and jump to the end
less +F /var/log/syslog               # Follow mode (like tail -f)
```

**Navigation keys inside `less`:**
| Key | Action |
|-----|--------|
| `Space` / `f` | Scroll forward one page |
| `b` | Scroll backward one page |
| `G` | Jump to end of file |
| `g` | Jump to beginning |
| `/keyword` | Search forward |
| `?keyword` | Search backward |
| `n` | Next match |
| `N` | Previous match |
| `q` | Quit |

---

### `head` and `tail` — View Start or End of a File

```bash
head file.txt                         # Show first 10 lines (default)
head -n 20 file.txt                   # Show first 20 lines

tail file.txt                         # Show last 10 lines (default)
tail -n 20 file.txt                   # Show last 20 lines
tail -f /var/log/nginx/access.log     # Live-follow a log file (real-time updates)
tail -f -n 50 /var/log/syslog         # Follow last 50 lines in real-time
```

> `tail -f` is the go-to command for monitoring application logs during deployments.

---

## 3.6 Writing to Files (Redirection)

Linux uses redirection operators to write command output to files.

```bash
# Overwrite (creates file if it doesn't exist)
echo "Hello, World!" > file.txt       # Write text to a file (overwrites)
ls -la > directory-list.txt           # Save command output to a file

# Append (adds to existing content)
echo "New line" >> file.txt           # Append text to a file
date >> file.txt                      # Append current date to a file

# Write multiple lines using heredoc
cat > config.txt << EOF
server=localhost
port=8080
debug=true
EOF
```

**Redirection operators summary:**
| Operator | Action |
|----------|--------|
| `>` | Redirect output, overwrite file |
| `>>` | Redirect output, append to file |
| `<` | Read input from a file |
| `2>` | Redirect error output to a file |
| `2>&1` | Redirect errors to same place as output |
| `&>` | Redirect both output and errors to a file |

---

## 3.7 Searching Inside Files

### `grep` — Search for Patterns in Files
Searches for a specific string or pattern inside files.

```bash
grep "error" /var/log/syslog              # Find lines containing "error"
grep -i "error" /var/log/syslog           # Case-insensitive search
grep -n "error" /var/log/syslog           # Show line numbers
grep -r "password" /etc/                  # Recursively search in a directory
grep -v "debug" app.log                   # Show lines that do NOT match
grep -c "error" /var/log/syslog           # Count matching lines
grep -l "nginx" /etc/                     # List files that contain the match
grep -A 3 "error" app.log                 # Show 3 lines after each match
grep -B 3 "error" app.log                 # Show 3 lines before each match
grep -E "error|warning|critical" app.log  # Search for multiple patterns (extended regex)
```

**Example — Find failed SSH login attempts:**
```bash
grep "Failed password" /var/log/auth.log
```

---

## 3.8 Archiving and Compression

### `tar` — Archive Files and Directories
Bundles multiple files and directories into a single archive file.

```bash
# Creating archives
tar -cvf archive.tar file1 file2 dir1/       # Create a .tar archive
tar -czvf archive.tar.gz dir1/               # Create a gzip-compressed archive
tar -cjvf archive.tar.bz2 dir1/             # Create a bzip2-compressed archive
tar -cJvf archive.tar.xz dir1/              # Create an xz-compressed archive

# Extracting archives
tar -xvf archive.tar                         # Extract a .tar archive
tar -xzvf archive.tar.gz                     # Extract a .tar.gz archive
tar -xjvf archive.tar.bz2                    # Extract a .tar.bz2 archive
tar -xvf archive.tar -C /opt/               # Extract to a specific directory

# Viewing archive contents (without extracting)
tar -tvf archive.tar                         # List contents of an archive
tar -tzvf archive.tar.gz                     # List contents of a .tar.gz archive
```

**Flag breakdown:**
| Flag | Meaning |
|------|---------|
| `-c` | Create a new archive |
| `-x` | Extract from archive |
| `-t` | List archive contents |
| `-v` | Verbose (show progress) |
| `-f` | Specify archive filename |
| `-z` | Use gzip compression |
| `-j` | Use bzip2 compression |
| `-J` | Use xz compression |
| `-C` | Extract to specific directory |

---

### `gzip` / `gunzip` — Compress and Decompress Files

```bash
gzip file.txt                         # Compress file (creates file.txt.gz, removes original)
gzip -k file.txt                      # Compress and keep the original file
gzip -d file.txt.gz                   # Decompress (same as gunzip)
gunzip file.txt.gz                    # Decompress a .gz file
gzip -l file.txt.gz                   # Show compression ratio
gzip -9 file.txt                      # Maximum compression level
```

---

### `zip` / `unzip` — Create and Extract ZIP Archives

```bash
zip archive.zip file1.txt file2.txt   # Create a zip archive
zip -r archive.zip dir1/              # Zip a directory recursively
zip -e archive.zip file.txt           # Create encrypted zip (prompts for password)

unzip archive.zip                     # Extract zip archive
unzip archive.zip -d /opt/            # Extract to a specific directory
unzip -l archive.zip                  # List contents without extracting
```

---

## 3.9 File Comparison

### `diff` — Compare Two Files Line by Line
Shows the differences between two files.

```bash
diff file1.txt file2.txt              # Compare two files
diff -u file1.txt file2.txt           # Unified format (easier to read)
diff -i file1.txt file2.txt           # Ignore case differences
diff -r dir1/ dir2/                   # Compare two directories recursively
```

**Output symbols:**
| Symbol | Meaning |
|--------|---------|
| `<` | Line only in file1 |
| `>` | Line only in file2 |
| `---` | Separator between differences |

**Example — Compare config before and after changes:**
```bash
diff /etc/nginx/nginx.conf.bak /etc/nginx/nginx.conf
```

---

### `cmp` — Compare Two Files Byte by Byte

```bash
cmp file1.txt file2.txt               # Returns nothing if identical, shows first difference if not
cmp -l file1.txt file2.txt            # List all differing bytes
```

---

## 3.10 Linking Files

### Hard Links vs Symbolic Links

| Feature | Hard Link | Symbolic Link |
|---------|-----------|---------------|
| Points to | Inode (actual data) | File path |
| Works across filesystems | No | Yes |
| Works for directories | No | Yes |
| Survives original deletion | Yes | No (becomes broken) |
| Created with | `ln file link` | `ln -s file link` |

```bash
# Hard link
ln original.txt hardlink.txt          # Both point to the same inode/data

# Symbolic link (symlink)
ln -s /opt/app/bin/app /usr/local/bin/app    # Create a symlink
ln -s /var/www/html /home/mike/webroot       # Symlink to a directory

# View symlinks
ls -la /usr/local/bin/app
# lrwxrwxrwx 1 root root 16 Jan 10 app -> /opt/app/bin/app

# Remove a symlink
unlink /usr/local/bin/app
rm /usr/local/bin/app                 # Also works
```

---

## 3.11 Practical Lab Exercises

### Exercise 1: File and Directory Operations
```bash
# 1. Create a project structure
mkdir -p ~/devops-project/{scripts,configs,logs,backups}

# 2. Create placeholder files
touch ~/devops-project/scripts/deploy.sh
touch ~/devops-project/configs/app.conf
touch ~/devops-project/logs/app.log

# 3. Verify the structure
tree ~/devops-project

# 4. Copy the config file as a backup
cp ~/devops-project/configs/app.conf ~/devops-project/backups/app.conf.bak

# 5. Rename the deploy script
mv ~/devops-project/scripts/deploy.sh ~/devops-project/scripts/deploy-v1.sh
```

### Exercise 2: Writing and Reading Files
```bash
# 1. Write server config to a file
cat > ~/devops-project/configs/app.conf << EOF
server=localhost
port=8080
env=production
debug=false
EOF

# 2. View the file
cat ~/devops-project/configs/app.conf

# 3. Append a new setting
echo "log_level=info" >> ~/devops-project/configs/app.conf

# 4. Verify the appended content
tail -n 3 ~/devops-project/configs/app.conf
```

### Exercise 3: Archiving and Cleanup
```bash
# 1. Archive the entire project directory
tar -czvf ~/devops-project-backup.tar.gz ~/devops-project/

# 2. Verify the archive contents
tar -tzvf ~/devops-project-backup.tar.gz

# 3. Extract to a new location
tar -xzvf ~/devops-project-backup.tar.gz -C /tmp/

# 4. Find and delete all empty log files
find ~/devops-project/logs -empty -type f -delete
```

---

## 3.12 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `touch file` | Create an empty file |
| `mkdir -p dir/sub` | Create nested directories |
| `cp -r src/ dest/` | Copy directory recursively |
| `cp -a src/ dest/` | Copy preserving all attributes |
| `mv file /path/` | Move or rename a file |
| `rm -rf dir/` | Force delete directory (use carefully) |
| `rmdir dir/` | Remove empty directory |
| `cat file` | Display file content |
| `less file` | Scroll through file |
| `head -n 10 file` | Show first 10 lines |
| `tail -f file` | Follow file in real-time |
| `echo "text" > file` | Write text to file (overwrite) |
| `echo "text" >> file` | Append text to file |
| `grep -r "pattern" dir/` | Search pattern in directory |
| `tar -czvf out.tar.gz dir/` | Create compressed archive |
| `tar -xzvf archive.tar.gz` | Extract compressed archive |
| `zip -r out.zip dir/` | Create zip archive |
| `unzip archive.zip` | Extract zip archive |
| `diff file1 file2` | Compare two files |
| `ln -s target link` | Create symbolic link |

---

## Summary

In this module, you learned:
- How to create files and directories with `touch` and `mkdir`
- How to copy files and directories with `cp` and its important flags
- How to move and rename files with `mv`
- How to safely delete files and directories with `rm` and `rmdir`
- How to view and read file contents with `cat`, `less`, `head`, and `tail`
- How to write to files using redirection operators (`>`, `>>`, heredoc)
- How to search inside files with `grep`
- How to archive and compress files with `tar`, `gzip`, and `zip`
- How to compare files with `diff` and `cmp`
- The difference between hard links and symbolic links

---

> **Next Module →** Module 4: File Permissions & Ownership (chmod, chown, chgrp, umask, ACLs)
