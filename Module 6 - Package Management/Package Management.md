# Module 6: Package Management

## Overview
Package management is one of the most critical skills for a DevOps engineer. Every time you provision
a server, set up a CI/CD pipeline, or configure a container, you will install, update, and manage
software packages. Linux distributions use different package managers depending on their family:

| Distribution Family | Package Manager | Package Format |
|--------------------|----------------|----------------|
| Ubuntu / Debian | `apt` / `apt-get` / `dpkg` | `.deb` |
| RHEL / CentOS / AlmaLinux / Fedora | `yum` / `dnf` / `rpm` | `.rpm` |
| Arch Linux | `pacman` | `.pkg.tar.zst` |
| Universal (any distro) | `snap` / `flatpak` | `.snap` / `.flatpak` |

This module covers both `apt` (Debian/Ubuntu) and `yum`/`dnf` (RHEL/CentOS) since these are the
most common in DevOps and cloud environments.

---

## 6.1 How Package Management Works

A package manager handles:
1. **Downloading** software from online repositories
2. **Installing** the package and its dependencies
3. **Updating** installed packages to newer versions
4. **Removing** packages cleanly without leaving broken dependencies
5. **Querying** information about installed or available packages

```
┌─────────────────────────────────────────────────────┐
│                  Package Manager                     │
│                                                      │
│  ┌──────────┐    ┌──────────────┐    ┌───────────┐  │
│  │  Command │───▶│  Dependency  │───▶│Repository │  │
│  │  (apt)   │    │  Resolver    │    │(internet) │  │
│  └──────────┘    └──────────────┘    └───────────┘  │
│        │                                    │        │
│        ▼                                    ▼        │
│  ┌──────────┐                      ┌───────────────┐ │
│  │  Local   │◀─────────────────────│  Download     │ │
│  │  System  │                      │  Packages     │ │
│  └──────────┘                      └───────────────┘ │
└─────────────────────────────────────────────────────┘
```

---

## 6.2 APT — Advanced Package Tool (Ubuntu/Debian)

`apt` is the modern, user-friendly package manager for Debian-based distributions.
It is a higher-level wrapper around `dpkg`.

### Updating Package Index

Before installing anything, always update the local package index to get the latest
available versions from repositories.

```bash
sudo apt update                       # Refresh the package index (does NOT upgrade packages)
sudo apt update && sudo apt upgrade   # Update index then upgrade all packages
```

> `apt update` only refreshes the list of available packages. It does NOT install or upgrade anything.
> Always run `apt update` before installing a package to ensure you get the latest version.

---

### Installing Packages

```bash
sudo apt install nginx                        # Install a single package
sudo apt install nginx curl git vim           # Install multiple packages at once
sudo apt install nginx -y                     # Auto-confirm installation (no prompt)
sudo apt install ./package.deb                # Install a local .deb file
sudo apt install nginx=1.18.0-0ubuntu1        # Install a specific version
sudo apt install --reinstall nginx            # Reinstall a package
sudo apt install -f                           # Fix broken dependencies
```

---

### Upgrading Packages

```bash
sudo apt upgrade                      # Upgrade all upgradable packages (safe)
sudo apt upgrade nginx                # Upgrade a specific package
sudo apt full-upgrade                 # Upgrade + remove obsolete packages if needed
sudo apt dist-upgrade                 # Full system upgrade (handles dependency changes)
```

> Use `apt upgrade` for routine updates. Use `apt full-upgrade` or `apt dist-upgrade`
> when upgrading to a new OS release.

---

### Removing Packages

```bash
sudo apt remove nginx                 # Remove package (keeps config files)
sudo apt purge nginx                  # Remove package AND its config files
sudo apt autoremove                   # Remove unused dependency packages
sudo apt autoremove --purge           # Remove unused packages and their configs
sudo apt clean                        # Clear downloaded package cache
sudo apt autoclean                    # Remove only outdated cached packages
```

> Always run `sudo apt autoremove` after removing packages to clean up orphaned dependencies.

---

### Searching and Querying Packages

```bash
apt search nginx                      # Search for packages by name or description
apt show nginx                        # Show detailed info about a package
apt list                              # List all available packages
apt list --installed                  # List all installed packages
apt list --upgradable                 # List packages with available upgrades
apt depends nginx                     # Show package dependencies
apt rdepends nginx                    # Show reverse dependencies (what depends on nginx)
```

**Output of `apt show nginx`:**
```
Package: nginx
Version: 1.18.0-0ubuntu1
Priority: optional
Section: web
Maintainer: Ubuntu Developers
Installed-Size: 44.0 kB
Depends: nginx-core (<< 1.18.0-0ubuntu1.1~) | nginx-full (<< 1.18.0-0ubuntu1.1~)
Homepage: https://nginx.net
Description: small, powerful, scalable web/proxy server
```

---

### APT Quick Reference

| Command | Description |
|---------|-------------|
| `apt update` | Refresh package index |
| `apt upgrade` | Upgrade all packages |
| `apt install pkg` | Install a package |
| `apt remove pkg` | Remove a package (keep configs) |
| `apt purge pkg` | Remove package and configs |
| `apt autoremove` | Remove unused dependencies |
| `apt search pkg` | Search for a package |
| `apt show pkg` | Show package details |
| `apt list --installed` | List installed packages |
| `apt clean` | Clear package cache |

---

## 6.3 APT-GET — Traditional Package Tool (Ubuntu/Debian)

`apt-get` is the older, script-friendly version of `apt`. It is preferred in shell scripts
because its output is more stable and predictable.

```bash
sudo apt-get update                   # Refresh package index
sudo apt-get install nginx -y         # Install package
sudo apt-get upgrade                  # Upgrade all packages
sudo apt-get remove nginx             # Remove package
sudo apt-get purge nginx              # Remove package and configs
sudo apt-get autoremove               # Remove unused dependencies
sudo apt-get clean                    # Clear package cache
sudo apt-get check                    # Check for broken dependencies
sudo apt-get install -f               # Fix broken dependencies
sudo apt-get dist-upgrade             # Full distribution upgrade
```

> Use `apt` interactively in the terminal and `apt-get` in shell scripts and automation.

---

## 6.4 DPKG — Low-Level Debian Package Manager

`dpkg` is the low-level tool that `apt` uses under the hood. Use it for managing
individual `.deb` files directly.

```bash
# Installing and removing
sudo dpkg -i package.deb              # Install a local .deb package
sudo dpkg -r nginx                    # Remove a package (keep configs)
sudo dpkg -P nginx                    # Purge package and configs

# Querying installed packages
dpkg -l                               # List all installed packages
dpkg -l nginx                         # Check if nginx is installed
dpkg -l | grep nginx                  # Search installed packages
dpkg -s nginx                         # Show status/details of installed package
dpkg -L nginx                         # List all files installed by a package
dpkg -S /usr/sbin/nginx               # Find which package owns a file

# Checking package contents (without installing)
dpkg -c package.deb                   # List contents of a .deb file
dpkg -I package.deb                   # Show info about a .deb file

# Fixing issues
sudo dpkg --configure -a              # Configure all unpacked but unconfigured packages
sudo dpkg --audit                     # Check for partially installed packages
```

**Example — Find which package a file belongs to:**
```bash
dpkg -S /usr/bin/curl
# curl: /usr/bin/curl
```

---

## 6.5 YUM — Yellowdog Updater Modified (RHEL/CentOS 7)

`yum` is the package manager for older RHEL-based distributions (CentOS 7, RHEL 7).

### Installing and Removing

```bash
sudo yum install nginx                # Install a package
sudo yum install nginx -y             # Install without confirmation
sudo yum install nginx httpd git      # Install multiple packages
sudo yum reinstall nginx              # Reinstall a package
sudo yum remove nginx                 # Remove a package
sudo yum erase nginx                  # Remove package and dependencies
sudo yum localinstall package.rpm     # Install a local .rpm file
```

### Updating Packages

```bash
sudo yum update                       # Update all packages
sudo yum update nginx                 # Update a specific package
sudo yum check-update                 # Check for available updates (no install)
sudo yum upgrade                      # Update + remove obsolete packages
```

### Searching and Querying

```bash
yum search nginx                      # Search for a package
yum info nginx                        # Show package details
yum list installed                    # List all installed packages
yum list available                    # List all available packages
yum list updates                      # List packages with updates available
yum deplist nginx                     # Show package dependencies
yum provides /usr/sbin/nginx          # Find which package provides a file
```

### Managing Repositories

```bash
yum repolist                          # List enabled repositories
yum repolist all                      # List all repositories (enabled + disabled)
sudo yum-config-manager --enable epel # Enable a repository
sudo yum-config-manager --disable epel # Disable a repository
sudo yum clean all                    # Clear all cached data
sudo yum makecache                    # Rebuild the package cache
```

---

## 6.6 DNF — Dandified YUM (RHEL/CentOS 8+, Fedora)

`dnf` is the modern replacement for `yum` in RHEL 8+, CentOS 8+, AlmaLinux, and Fedora.
It has the same syntax as `yum` but is faster and handles dependencies better.

```bash
# Installing and removing
sudo dnf install nginx                # Install a package
sudo dnf install nginx -y             # Install without confirmation
sudo dnf reinstall nginx              # Reinstall a package
sudo dnf remove nginx                 # Remove a package
sudo dnf autoremove                   # Remove unused dependencies

# Updating
sudo dnf update                       # Update all packages
sudo dnf update nginx                 # Update a specific package
sudo dnf check-update                 # Check for available updates
sudo dnf upgrade --refresh            # Refresh metadata and upgrade

# Searching and querying
dnf search nginx                      # Search for a package
dnf info nginx                        # Show package details
dnf list installed                    # List installed packages
dnf list available nginx              # Check if a package is available
dnf provides /usr/sbin/nginx          # Find which package provides a file
dnf repoquery --requires nginx        # Show package dependencies

# Repository management
dnf repolist                          # List enabled repositories
dnf repolist all                      # List all repositories
sudo dnf config-manager --enable epel # Enable a repository
sudo dnf config-manager --add-repo URL # Add a new repository
sudo dnf clean all                    # Clear all cached data
sudo dnf makecache                    # Rebuild the package cache

# Groups (install a set of related packages)
dnf group list                        # List available package groups
sudo dnf group install "Development Tools"  # Install a package group
sudo dnf group remove "Development Tools"   # Remove a package group
```

---

## 6.7 RPM — Red Hat Package Manager

`rpm` is the low-level package manager for RHEL-based systems, similar to `dpkg` for Debian.

```bash
# Installing and removing
sudo rpm -ivh package.rpm             # Install a local .rpm file (verbose + progress)
sudo rpm -Uvh package.rpm             # Upgrade or install a package
sudo rpm -e nginx                     # Remove a package

# Querying
rpm -qa                               # List all installed packages
rpm -qa | grep nginx                  # Search installed packages
rpm -qi nginx                         # Show detailed info about installed package
rpm -ql nginx                         # List all files installed by a package
rpm -qf /usr/sbin/nginx               # Find which package owns a file
rpm -qR nginx                         # Show package dependencies
rpm -qc nginx                         # List config files of a package

# Querying a .rpm file (before installing)
rpm -qip package.rpm                  # Show info about a .rpm file
rpm -qlp package.rpm                  # List files in a .rpm file

# Verifying
rpm -V nginx                          # Verify installed package integrity
rpm --checksig package.rpm            # Verify package signature
```

**Example — Find which package owns a file:**
```bash
rpm -qf /etc/nginx/nginx.conf
# nginx-1.20.1-9.el9.x86_64
```

---

## 6.8 Managing Repositories

### APT Repositories (Ubuntu/Debian)

Repository sources are stored in `/etc/apt/sources.list` and `/etc/apt/sources.list.d/`.

```bash
# View current repositories
cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/

# Add a repository manually
sudo add-apt-repository ppa:nginx/stable          # Add a PPA (Ubuntu)
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

# Add a repository GPG key (for security verification)
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg

# Remove a repository
sudo add-apt-repository --remove ppa:nginx/stable

# Update after adding a repo
sudo apt update
```

**Example — Add Docker repository on Ubuntu:**
```bash
# 1. Install prerequisites
sudo apt install ca-certificates curl gnupg -y

# 2. Add Docker's GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 3. Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Update and install
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
```

---

### YUM/DNF Repositories (RHEL/CentOS)

Repository files are stored in `/etc/yum.repos.d/`.

```bash
# View current repositories
ls /etc/yum.repos.d/
cat /etc/yum.repos.d/CentOS-Base.repo

# Add EPEL repository (Extra Packages for Enterprise Linux)
sudo dnf install epel-release -y      # RHEL/CentOS 8+
sudo yum install epel-release -y      # CentOS 7

# Add a custom repository file
sudo tee /etc/yum.repos.d/nginx.repo << EOF
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/\$releasever/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
EOF

# Install from the new repo
sudo dnf install nginx -y
```

---

## 6.9 SNAP — Universal Package Manager

`snap` packages work across different Linux distributions and include all dependencies.

```bash
# Basic snap commands
sudo snap install code --classic      # Install VS Code
sudo snap install docker              # Install Docker
sudo snap install --classic node      # Install Node.js
sudo snap remove code                 # Remove a snap package
sudo snap refresh                     # Update all snap packages
sudo snap refresh code                # Update a specific snap package

# Querying snaps
snap list                             # List installed snaps
snap find nginx                       # Search for a snap package
snap info code                        # Show details about a snap
snap changes                          # Show recent snap changes
```

---

## 6.10 Holding and Pinning Package Versions

Prevent specific packages from being upgraded (useful for stability in production).

### APT (Ubuntu/Debian)

```bash
# Hold a package at its current version
sudo apt-mark hold nginx              # Prevent nginx from being upgraded
sudo apt-mark unhold nginx            # Allow nginx to be upgraded again
apt-mark showhold                     # List all held packages

# Pin a specific version in /etc/apt/preferences
sudo tee /etc/apt/preferences.d/nginx << EOF
Package: nginx
Pin: version 1.18.*
Pin-Priority: 1001
EOF
```

### YUM/DNF (RHEL/CentOS)

```bash
# Exclude a package from updates
sudo dnf update --exclude=nginx       # Skip nginx during this update
sudo yum update --exclude=nginx       # Skip nginx (yum)

# Permanently exclude in config
echo "exclude=nginx" | sudo tee -a /etc/dnf/dnf.conf

# Lock a specific version with versionlock plugin
sudo dnf install python3-dnf-plugin-versionlock
sudo dnf versionlock add nginx        # Lock nginx at current version
sudo dnf versionlock delete nginx     # Remove the lock
sudo dnf versionlock list             # List all locked packages
```

---

## 6.11 Practical Lab Exercises

### Exercise 1: Install and Manage a Web Server (Ubuntu)
```bash
# 1. Update package index
sudo apt update

# 2. Install Nginx
sudo apt install nginx -y

# 3. Verify installation
nginx -v
systemctl status nginx

# 4. Check installed files
dpkg -L nginx | head -20

# 5. Hold nginx at current version
sudo apt-mark hold nginx

# 6. Verify the hold
apt-mark showhold
```

### Exercise 2: Install and Manage a Web Server (RHEL/CentOS)
```bash
# 1. Install EPEL repository
sudo dnf install epel-release -y

# 2. Update package cache
sudo dnf makecache

# 3. Install Nginx
sudo dnf install nginx -y

# 4. Verify installation
nginx -v
rpm -qi nginx

# 5. List all files installed by nginx
rpm -ql nginx

# 6. Lock nginx version
sudo dnf versionlock add nginx
```

### Exercise 3: Add a Third-Party Repository and Install Docker
```bash
# Ubuntu
# 1. Install prerequisites
sudo apt install ca-certificates curl gnupg lsb-release -y

# 2. Add Docker GPG key and repository
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 3. Install Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y

# 4. Verify
docker --version
```

### Exercise 4: System Cleanup
```bash
# Ubuntu
# 1. Remove unused packages
sudo apt autoremove -y

# 2. Clear package cache
sudo apt clean

# 3. Check disk space freed
df -h /

# RHEL/CentOS
# 1. Remove unused packages
sudo dnf autoremove -y

# 2. Clear all cached data
sudo dnf clean all

# 3. Check disk space
df -h /
```

---

## 6.12 Comparing APT vs YUM/DNF Commands

| Task | APT (Ubuntu/Debian) | YUM (CentOS 7) | DNF (RHEL 8+) |
|------|--------------------|--------------------|----------------|
| Update index | `apt update` | `yum check-update` | `dnf check-update` |
| Install package | `apt install pkg` | `yum install pkg` | `dnf install pkg` |
| Remove package | `apt remove pkg` | `yum remove pkg` | `dnf remove pkg` |
| Purge package | `apt purge pkg` | `yum erase pkg` | `dnf remove pkg` |
| Upgrade all | `apt upgrade` | `yum update` | `dnf upgrade` |
| Search package | `apt search pkg` | `yum search pkg` | `dnf search pkg` |
| Show package info | `apt show pkg` | `yum info pkg` | `dnf info pkg` |
| List installed | `apt list --installed` | `yum list installed` | `dnf list installed` |
| Clean cache | `apt clean` | `yum clean all` | `dnf clean all` |
| Fix dependencies | `apt install -f` | `yum-complete-transaction` | `dnf distro-sync` |
| Find file owner | `dpkg -S /path/file` | `rpm -qf /path/file` | `rpm -qf /path/file` |

---

## 6.13 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `apt update` | Refresh package index (Ubuntu) |
| `apt install pkg -y` | Install package without prompt |
| `apt upgrade` | Upgrade all packages |
| `apt remove pkg` | Remove package (keep configs) |
| `apt purge pkg` | Remove package and configs |
| `apt autoremove` | Remove unused dependencies |
| `apt search pkg` | Search for a package |
| `apt show pkg` | Show package details |
| `apt list --installed` | List installed packages |
| `dpkg -l` | List all installed packages |
| `dpkg -L pkg` | List files installed by package |
| `dpkg -S /path/file` | Find package that owns a file |
| `dnf install pkg -y` | Install package (RHEL/CentOS) |
| `dnf update` | Update all packages |
| `dnf remove pkg` | Remove a package |
| `dnf search pkg` | Search for a package |
| `dnf info pkg` | Show package details |
| `dnf list installed` | List installed packages |
| `rpm -qa` | List all installed RPM packages |
| `rpm -qf /path/file` | Find package that owns a file |
| `rpm -ql pkg` | List files installed by package |
| `apt-mark hold pkg` | Prevent package from upgrading |
| `dnf versionlock add pkg` | Lock package version (RHEL) |
| `snap install pkg` | Install a snap package |
| `snap list` | List installed snaps |

---

## Summary

In this module, you learned:
- How package management works and the difference between package managers across distributions
- How to use `apt` and `apt-get` to install, update, remove, and query packages on Ubuntu/Debian
- How to use `dpkg` for low-level `.deb` package management
- How to use `yum` and `dnf` to manage packages on RHEL/CentOS systems
- How to use `rpm` for low-level `.rpm` package management
- How to add, enable, and manage software repositories on both APT and YUM/DNF systems
- How to use `snap` for universal cross-distribution package management
- How to hold and pin package versions to prevent unwanted upgrades in production
- How to clean up package caches and remove unused dependencies

---

> **Next Module →** Module 7: Process & Service Management (ps, top, kill, systemctl, journalctl)
