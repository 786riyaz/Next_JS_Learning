# Phase 40 – Linux Administration, Shell Scripting and Server Management

**Level:** Advanced  
**Domain:** Linux Administration, DevOps, Server Management, Automation  
**Primary OS:** Ubuntu Server 24.04 LTS / 26.04 LTS concepts  
**Shell:** Bash  
**Technologies:** Linux, SSH, systemd, APT, Cron, UFW, Nginx, Docker, PM2

---

# 1. Learning Objectives

By completing this phase, you should understand how to:

1. Understand Linux fundamentals and architecture.
2. Work with different Linux distributions.
3. Understand the Linux kernel and shell.
4. Navigate the Linux filesystem hierarchy.
5. Use essential Linux commands.
6. Manage files and directories.
7. Configure file permissions and ownership.
8. Create and manage Linux users and groups.
9. Monitor and manage processes.
10. Manage services using systemd.
11. Install and update software using APT.
12. Write Bash shell scripts.
13. Work with variables and environment variables.
14. Use conditions and loops.
15. Create reusable Bash functions.
16. Schedule tasks using Cron.
17. Manage logs and log rotation.
18. Monitor disk, memory and CPU usage.
19. Troubleshoot Linux networking.
20. Configure SSH access.
21. Secure and harden Linux servers.
22. Administer production Linux servers.

---

# 2. Linux Fundamentals and Architecture

## 2.1 What Is Linux?

Linux is an open-source, Unix-like operating system kernel.

The Linux kernel manages hardware and provides essential operating system functionality.

Examples of responsibilities:

- Process management.
- Memory management.
- Device management.
- File system management.
- Networking.
- Security and access control.

Linux-based operating systems are commonly called Linux distributions.

Examples:

- Ubuntu.
- Debian.
- Fedora.
- Red Hat Enterprise Linux.
- Arch Linux.
- Linux Mint.
- Rocky Linux.

Linux is widely used in:

- Cloud servers.
- Web hosting.
- Backend development.
- Containers.
- Kubernetes.
- Embedded systems.
- Networking infrastructure.
- Supercomputers.

## 2.2 What Is an Operating System?

An operating system manages the computer's hardware and provides services to applications.

Consider your computer:

```text
Computer Hardware
       |
       v
Operating System
       |
       v
Applications
```

Hardware includes:

- CPU.
- RAM.
- Storage.
- Network interfaces.
- Graphics devices.

The operating system provides an environment in which applications can run.

## 2.3 Linux Architecture

A simplified Linux architecture:

```text
+----------------------------------+
|          User Applications       |
|                                  |
|  Node.js | Nginx | MySQL | Docker|
+----------------------------------+
|              Shell               |
|                                  |
|          Bash / Zsh              |
+----------------------------------+
|           System Libraries       |
|                                  |
|          glibc / libc            |
+----------------------------------+
|           Linux Kernel           |
|                                  |
| Process | Memory | Network       |
| Filesystem | Device Management   |
+----------------------------------+
|             Hardware             |
|                                  |
| CPU | RAM | Disk | Network       |
+----------------------------------+
```

### Layer 1: Hardware

Physical computing resources.

### Layer 2: Kernel

The kernel manages hardware resources and provides essential operating system services.

### Layer 3: System Libraries

Libraries provide interfaces that applications use to access operating system functionality.

### Layer 4: Shell

A shell allows users to interact with the operating system through commands and scripts.

### Layer 5: Applications

Examples:

- Node.js.
- Nginx.
- MySQL.
- MongoDB.
- Docker.

## 2.4 What Is the Linux Kernel?

The kernel is the core component of the operating system.

Its responsibilities include:

| Responsibility | Description |
|---|---|
| Process Management | Creates, schedules and manages processes |
| Memory Management | Allocates and manages memory |
| Device Management | Communicates with hardware devices |
| File Systems | Manages filesystem operations |
| Networking | Implements network-stack functionality |
| Security | Enforces various access-control mechanisms |

Check your Linux kernel version:

```bash
uname -r
```

Example output:

```text
6.x.x-generic
```

Get detailed system information:

```bash
uname -a
```

## 2.5 What Is a Shell?

A shell is a command interpreter.

It accepts commands and executes programs or shell operations.

Example:

```bash
ls -la
```

The shell interprets this command and executes the appropriate utility.

Popular shells include:

| Shell | Description |
|---|---|
| Bash | Bourne Again Shell |
| Zsh | Z Shell |
| Fish | Friendly Interactive Shell |
| Dash | Lightweight POSIX-oriented shell |
| Sh | Traditional shell interface |

Check your current shell:

```bash
echo "$SHELL"
```

Check the shell process:

```bash
ps -p $$ -o comm=
```

### Shell vs Terminal

These terms are not identical.

**Terminal:** Provides an interface for interacting with a command-line application.

**Shell:** Interprets commands entered into that interface.

Example:

```text
Terminal Application
       |
       v
Bash Shell
       |
       v
Linux Commands
       |
       v
Operating System
```

---

# 3. Linux Distributions

A Linux distribution combines the Linux kernel with system utilities, package management, libraries and other software.

## 3.1 Popular Linux Distributions

| Distribution | Package Manager | Common Usage |
|---|---|---|
| Ubuntu | APT | Servers, development |
| Debian | APT | Stable servers |
| Fedora | DNF | Development, desktop |
| RHEL | DNF | Enterprise servers |
| Rocky Linux | DNF | Enterprise-compatible environments |
| Arch Linux | Pacman | Advanced desktop users |
| Linux Mint | APT | Desktop computing |

## 3.2 Ubuntu Server

Ubuntu Server is commonly used for:

- VPS hosting.
- AWS EC2.
- Web servers.
- Backend applications.
- Docker hosts.
- Database servers.

For your practical Linux administration exercises, Ubuntu Server is a suitable starting point.

## 3.3 Check Linux Distribution Information

```bash
cat /etc/os-release
```

Example:

```text
NAME="Ubuntu"
VERSION="24.04 LTS"
ID=ubuntu
```

Check the kernel:

```bash
uname -r
```

Check CPU architecture:

```bash
uname -m
```

Common outputs:

```text
x86_64
```

or:

```text
aarch64
```

---

# 4. Linux File System Hierarchy

Linux organizes files and directories using a hierarchical filesystem.

Unlike Windows, which commonly uses drive letters such as `C:` and `D:`, Linux uses a single directory tree starting at `/`.

## 4.1 Root Directory

The root directory is:

```text
/
```

It is the top-level directory of the Linux filesystem hierarchy.

Example:

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

## 4.2 Important Linux Directories

| Directory | Purpose |
|---|---|
| `/` | Root directory |
| `/bin` | Essential user command binaries; often linked to `/usr/bin` |
| `/boot` | Bootloader and kernel-related files |
| `/dev` | Device files |
| `/etc` | System configuration |
| `/home` | Regular users' home directories |
| `/root` | Root user's home directory |
| `/lib` | Essential shared libraries; often linked to `/usr/lib` |
| `/media` | Mount points for removable media |
| `/mnt` | Temporary mount points |
| `/opt` | Optional third-party software |
| `/proc` | Virtual filesystem exposing process and kernel information |
| `/run` | Runtime system data |
| `/sbin` | System administration binaries; often linked to `/usr/sbin` |
| `/srv` | Data served by system services |
| `/sys` | Virtual filesystem exposing kernel and device information |
| `/tmp` | Temporary files |
| `/usr` | Most user-space applications, libraries and data |
| `/var` | Variable data such as logs, caches and application state |

## 4.3 Important Directories for Backend Developers

### `/etc`

Contains system configuration.

Examples:

```text
/etc/nginx/
/etc/ssh/
/etc/systemd/
/etc/hosts
```

Nginx configuration:

```bash
sudo nano /etc/nginx/nginx.conf
```

### `/var/log`

Contains system and application logs.

Examples:

```text
/var/log/syslog
/var/log/auth.log
/var/log/nginx/
/var/log/mysql/
```

### `/home`

Contains regular users' home directories.

Example:

```text
/home/ubuntu
```

### `/var/www`

Common location for website files.

Example:

```text
/var/www/my-website
```

### `/opt`

Often used for manually installed third-party applications.

Example:

```text
/opt/my-application
```

## 4.4 Absolute vs Relative Paths

### Absolute Path

An absolute path starts from `/`.

Example:

```bash
/home/ubuntu/project/app.js
```

### Relative Path

A relative path is interpreted from the current working directory.

Example:

```bash
cd /home/ubuntu/project

cat app.js
```

Here, `app.js` is a relative path.

## 4.5 Special Path Symbols

| Symbol | Meaning |
|---|---|
| `/` | Root directory |
| `~` | Current user's home directory |
| `.` | Current directory |
| `..` | Parent directory |
| `-` | Previous working directory in `cd -` |

Examples:

```bash
cd ~
```

```bash
cd ..
```

```bash
cd -
```

Official reference: [Ubuntu Server Documentation](https://ubuntu.com/server/docs/)

---

# 5. Essential Linux Commands

Linux administration requires familiarity with command-line tools.

Let's understand the most frequently used commands.

## 5.1 Navigation Commands

### `pwd`

Displays the current working directory.

```bash
pwd
```

### `ls`

Lists files and directories.

```bash
ls
```

Show hidden files:

```bash
ls -a
```

Detailed listing:

```bash
ls -l
```

Combined:

```bash
ls -lah
```

### `cd`

Changes the current directory.

```bash
cd /var/www
```

Move to the home directory:

```bash
cd ~
```

Move to the parent:

```bash
cd ..
```

## 5.2 File and Directory Commands

### `mkdir`

Creates directories.

```bash
mkdir projects
```

Create nested directories:

```bash
mkdir -p projects/backend/api
```

### `touch`

Creates an empty file if it does not exist, or updates its timestamps.

```bash
touch app.js
```

### `cp`

Copies files.

```bash
cp app.js backup.js
```

Copies directories recursively:

```bash
cp -r project project-backup
```

### `mv`

Moves or renames files.

Rename:

```bash
mv old.js new.js
```

Move:

```bash
mv app.js /home/ubuntu/
```

### `rm`

Deletes files.

```bash
rm app.js
```

Delete a directory recursively:

```bash
rm -r project
```

**Warning:** `rm -rf` can permanently remove large amounts of data without prompting.

Never run commands such as:

```bash
sudo rm -rf /
```

Understand every path before executing destructive commands.

## 5.3 Viewing Files

### `cat`

Displays file contents.

```bash
cat package.json
```

### `less`

Opens a file in a scrollable viewer.

```bash
less /var/log/syslog
```

### `head`

Displays the beginning of a file.

```bash
head -n 20 app.log
```

### `tail`

Displays the end of a file.

```bash
tail -n 20 app.log
```

Follow a log continuously:

```bash
tail -f app.log
```

### `grep`

Searches for text patterns.

```bash
grep "error" app.log
```

Case-insensitive:

```bash
grep -i "error" app.log
```

Recursive search:

```bash
grep -r "database" /var/www/myapp
```

Show line numbers:

```bash
grep -n "error" app.log
```

## 5.4 Finding Files

### `find`

Find files by name:

```bash
find /var/www -name "package.json"
```

Find JavaScript files:

```bash
find . -name "*.js"
```

Find files modified within the last day:

```bash
find . -type f -mtime -1
```

Find files larger than 100 MB:

```bash
find /var -type f -size +100M
```

### `which`

Find the executable resolved through your PATH.

```bash
which node
```

### `command -v`

Check how a command is resolved by the shell.

```bash
command -v node
```

## 5.5 Combining Commands

Linux supports pipes and redirection.

### Pipe `|`

Passes the standard output of one command as standard input to another.

Example:

```bash
ps aux | grep node
```

Flow:

```text
ps aux
   |
   v
Process Information
   |
   v
grep node
   |
   v
Matching Lines
```

### Output Redirection `>`

Writes output to a file, replacing its previous contents.

```bash
echo "Hello Linux" > output.txt
```

### Append `>>`

Appends output to a file.

```bash
echo "New Entry" >> output.txt
```

### Input Redirection `<`

Provides a file as command input.

```bash
sort < names.txt
```

### Standard Error `2>`

Redirects error output.

```bash
ls /invalid-path 2> error.log
```

### Redirect Both Output Streams

```bash
command > output.log 2>&1
```

This redirects standard output and standard error to the same file.

## 5.6 Useful Command Combinations

List Node.js processes:

```bash
ps aux | grep node
```

Find the largest files:

```bash
du -ah /var/www | sort -rh | head -20
```

Count files:

```bash
find . -type f | wc -l
```

Search logs:

```bash
grep -i "error" /var/log/syslog
```

Official reference: [GNU Coreutils Manual](https://www.gnu.org/software/coreutils/manual/coreutils.html)

---

# 6. File Permissions and Ownership

Linux uses a permission system to control who can access files and directories.

This is essential for server security.

## 6.1 Understanding File Permissions

Run:

```bash
ls -l
```

Example:

```text
-rwxr-xr-- 1 ubuntu developers 1024 Nov 25 app.sh
```

Let's understand the permission section:

```text
-rwxr-xr--
```

It contains:

```text
-   rwx   r-x   r--
|    |     |     |
|    |     |     └── Others
|    |     └──────── Group
|    └────────────── Owner
└────────────────── File Type
```

## 6.2 Permission Types

| Permission | Symbol | Numeric Value |
|---|---|---:|
| Read | `r` | 4 |
| Write | `w` | 2 |
| Execute | `x` | 1 |

### Read Permission

For files:

Allows reading file contents.

For directories:

Allows listing directory entries, subject to execute permission for access.

### Write Permission

For files:

Allows modifying file contents.

For directories:

Allows creating, deleting or renaming entries, subject to directory permissions and other security controls.

### Execute Permission

For files:

Allows execution when the file is a valid executable or script with an appropriate interpreter.

For directories:

Allows traversal through the directory.

## 6.3 Numeric Permissions

Permissions can be represented numerically.

Example:

```text
755
```

Calculation:

```text
Owner: 4 + 2 + 1 = 7

Group: 4 + 0 + 1 = 5

Others: 4 + 0 + 1 = 5
```

Therefore:

```text
755 = rwxr-xr-x
```

Other common permissions:

| Numeric | Symbolic | Meaning |
|---:|---|---|
| 777 | `rwxrwxrwx` | Everyone has full permissions |
| 755 | `rwxr-xr-x` | Owner full, others read and execute |
| 750 | `rwxr-x---` | Owner full, group read and execute |
| 700 | `rwx------` | Owner only |
| 644 | `rw-r--r--` | Owner read/write, others read |
| 640 | `rw-r-----` | Owner read/write, group read |
| 600 | `rw-------` | Owner read/write only |
| 400 | `r--------` | Owner read-only |

## 6.4 `chmod`

`chmod` changes file permissions.

Set permissions:

```bash
chmod 755 app.sh
```

Set a configuration file to owner-only read/write:

```bash
chmod 600 .env
```

Add execute permission:

```bash
chmod +x deploy.sh
```

Remove write permission from others:

```bash
chmod o-w app.js
```

## 6.5 `chown`

Changes file ownership.

```bash
sudo chown ubuntu app.js
```

Change owner and group:

```bash
sudo chown ubuntu:developers app.js
```

Recursive ownership:

```bash
sudo chown -R ubuntu:developers /var/www/myapp
```

Use recursive ownership changes carefully.

## 6.6 `chgrp`

Changes the group ownership.

```bash
sudo chgrp developers app.js
```

## 6.7 Linux Permission Example

Suppose your application is located at:

```text
/var/www/myapp
```

You want:

- Application user to read and modify deployment files.
- Group members to read and execute.
- Other users to have no access.

You could configure:

```bash
sudo chown -R ubuntu:developers /var/www/myapp

sudo chmod -R 750 /var/www/myapp
```

However, remember that applying one permission recursively to every file and directory may be inappropriate.

For example, application files and directories have different permission requirements.

A more controlled approach is:

```bash
sudo find /var/www/myapp -type d -exec chmod 750 {} \;

sudo find /var/www/myapp -type f -exec chmod 640 {} \;
```

Add execute permission to required scripts separately.

## 6.8 Special Permissions

Linux also supports:

- SUID.
- SGID.
- Sticky Bit.

### SUID

Allows an executable to run with the effective user ID of the file owner, subject to system and filesystem rules.

### SGID

For executable files, affects group identity during execution.

For directories, newly created entries generally inherit the directory's group.

Example:

```bash
chmod 2775 shared
```

### Sticky Bit

Commonly used on shared directories.

It restricts deletion or renaming of entries to their owners, the directory owner or a privileged process.

Example:

```bash
chmod 1777 /tmp
```

## 6.9 Umask

Umask determines which permission bits are removed from default permissions when files and directories are created.

Check current umask:

```bash
umask
```

Example:

```text
0022
```

A typical calculation:

```text
Files:       666 - 022 = 644

Directories: 777 - 022 = 755
```

Actual permissions can also be affected by the application or program creating the file.

---

# 7. Linux Users and Groups

Linux supports multiple user accounts.

Each user has an identity and permissions.

## 7.1 Root User

The root user is the system's superuser.

It has extensive administrative privileges.

Root UID:

```text
0
```

Check current user:

```bash
whoami
```

Check user ID:

```bash
id
```

## 7.2 What Is `sudo`?

`sudo` allows an authorized user to execute commands with elevated privileges.

Example:

```bash
sudo apt update
```

You generally do not need to log in directly as root for routine administration.

Ubuntu commonly uses `sudo` for administrative tasks. [Ubuntu](https://ubuntu.com/server/docs/security-users/?utm_source=chatgpt.com)

## 7.3 Create a User

```bash
sudo adduser developer
```

This creates a user and typically prompts you to set a password and other account information.

## 7.4 Delete a User

```bash
sudo deluser developer
```

Delete the user and home directory:

```bash
sudo deluser --remove-home developer
```

Check account details before deleting a production account.

## 7.5 Create a Group

```bash
sudo groupadd developers
```

## 7.6 Add a User to a Group

```bash
sudo usermod -aG developers developer
```

Important:

`-aG` appends the user to supplementary groups.

Without `-a`, you risk replacing existing supplementary group memberships.

## 7.7 List Groups

```bash
groups developer
```

Or:

```bash
id developer
```

## 7.8 Important User Files

| File | Purpose |
|---|---|
| `/etc/passwd` | User account information |
| `/etc/shadow` | Protected password hash information and account aging |
| `/etc/group` | Group information |
| `/etc/gshadow` | Protected group information |
| `/etc/sudoers` | Sudo permission configuration |

Never edit `/etc/sudoers` using a normal text editor without validation.

Use:

```bash
sudo visudo
```

## 7.9 SSH User Management

You can restrict which users are permitted to access a server through SSH.

For example, OpenSSH supports directives such as:

```text
AllowUsers ubuntu developer
```

Or group-based restrictions:

```text
AllowGroups sshlogin
```

Validate the SSH configuration before reloading the service.

Official reference: [Ubuntu User Management](https://ubuntu.com/server/docs/how-to/security/user-management/) [Ubuntu](https://ubuntu.com/server/docs/how-to/security/user-management/?utm_source=chatgpt.com)

---

# 8. Process Management

A process is an instance of a running program.

Examples:

- Nginx process.
- Node.js process.
- MySQL process.
- Docker daemon.
- SSH daemon.

## 8.1 What Is a Process?

Suppose you execute:

```bash
node app.js
```

The operating system creates a process for the Node.js program.

The process receives resources such as:

- CPU time.
- Memory.
- File descriptors.
- Process identifiers.

## 8.2 PID – Process ID

Each process has a process identifier.

Example:

```bash
ps aux
```

Output:

```text
USER       PID  %CPU %MEM COMMAND
ubuntu    1200   1.2  2.0 node app.js
```

Here:

```text
1200
```

is the PID.

## 8.3 View Running Processes

```bash
ps aux
```

Search for Node.js:

```bash
ps aux | grep node
```

Display a process tree:

```bash
pstree
```

## 8.4 `top`

Displays real-time system and process information.

```bash
top
```

It shows:

- CPU usage.
- Memory usage.
- Load average.
- Running processes.
- Process resource consumption.

## 8.5 `htop`

An interactive process viewer.

Install:

```bash
sudo apt install htop
```

Run:

```bash
htop
```

## 8.6 Kill a Process

Terminate a process gracefully:

```bash
kill 1200
```

Forcefully terminate:

```bash
kill -9 1200
```

### Important Difference

`kill` sends a signal to a process.

By default, it sends SIGTERM, allowing the process to perform cleanup.

`kill -9` sends SIGKILL, which cannot be caught or handled by the process.

Use SIGKILL only when graceful termination fails and you understand the consequences.

## 8.7 Find a Process by Port

Suppose your application is running on port 3000.

```bash
sudo lsof -i :3000
```

You can then identify the process using that port.

## 8.8 Background Processes

Run a command in the background:

```bash
node app.js &
```

The shell returns control while the process continues running.

However, this is not a robust production process-management strategy.

Use systemd, PM2 or an appropriate container orchestrator for production services.

## 8.9 Process Signals

| Signal | Number | Meaning |
|---|---:|---|
| SIGHUP | 1 | Hangup |
| SIGINT | 2 | Interrupt |
| SIGKILL | 9 | Force termination |
| SIGTERM | 15 | Termination request |
| SIGSTOP | 19 | Stop process |

Signal numbers can vary across platforms; these are common Linux values.

---

# 9. System Services and systemd

## 9.1 What Is a Service?

A service is a background program that provides functionality to the system or other applications.

Examples:

- Nginx.
- MySQL.
- SSH.
- Docker.
- Redis.

Services commonly run without direct user interaction.

## 9.2 What Is systemd?

systemd is a widely used Linux system and service manager.

It manages:

- System startup.
- Services.
- Dependencies.
- Mounts.
- Timers.
- Sockets.
- Other system resources.

## 9.3 `systemctl`

`systemctl` is used to manage systemd units.

Check Nginx status:

```bash
sudo systemctl status nginx
```

Start:

```bash
sudo systemctl start nginx
```

Stop:

```bash
sudo systemctl stop nginx
```

Restart:

```bash
sudo systemctl restart nginx
```

Reload:

```bash
sudo systemctl reload nginx
```

Enable at boot:

```bash
sudo systemctl enable nginx
```

Disable at boot:

```bash
sudo systemctl disable nginx
```

Check whether a service is enabled:

```bash
systemctl is-enabled nginx
```

Check whether it is active:

```bash
systemctl is-active nginx
```

### Restart vs Reload

| Restart | Reload |
|---|---|
| Stops and starts the service | Asks the service to reload configuration |
| May interrupt active connections | May avoid a full service restart |
| Used when restart is necessary | Used when the service supports configuration reload |

## 9.4 Create a Custom systemd Service for Node.js

Suppose your project is located at:

```text
/var/www/myapp
```

Create:

```bash
sudo nano /etc/systemd/system/myapp.service
```

Configuration:

```ini
[Unit]
Description=My Node.js Application
After=network.target

[Service]
Type=simple

User=ubuntu

WorkingDirectory=/var/www/myapp

Environment=NODE_ENV=production

ExecStart=/usr/bin/node /var/www/myapp/server.js

Restart=on-failure

RestartSec=5

[Install]
WantedBy=multi-user.target
```

### Explanation

| Directive | Purpose |
|---|---|
| `Description` | Service description |
| `After` | Startup ordering |
| `Type` | Service process type |
| `User` | User running the application |
| `WorkingDirectory` | Application directory |
| `Environment` | Environment variables |
| `ExecStart` | Command to start the service |
| `Restart` | Restart policy |
| `RestartSec` | Delay before restart |
| `WantedBy` | Installation target |

Reload systemd:

```bash
sudo systemctl daemon-reload
```

Start the service:

```bash
sudo systemctl start myapp
```

Enable at boot:

```bash
sudo systemctl enable myapp
```

Check status:

```bash
sudo systemctl status myapp
```

View logs:

```bash
sudo journalctl -u myapp
```

**Production note:** Do not put sensitive credentials directly in a world-readable service file. Use suitable systemd credential mechanisms or a properly permissioned external secrets solution. Also ensure that the application's shutdown behavior handles SIGTERM gracefully.

---

# 10. Package Management with APT

Package managers install, update and remove software.

Ubuntu and Debian commonly use APT.

## 10.1 What Is APT?

APT stands for Advanced Package Tool.

It manages software packages and their dependencies.

Examples:

- Nginx.
- Git.
- Node.js packages available through configured repositories.
- MySQL server.
- Docker dependencies.
- System utilities.

## 10.2 Update Package Information

```bash
sudo apt update
```

This refreshes the local package index.

It does not upgrade every installed package.

## 10.3 Upgrade Installed Packages

```bash
sudo apt upgrade
```

This upgrades installed packages where possible without removing installed packages to satisfy dependency changes.

## 10.4 Full Upgrade

```bash
sudo apt full-upgrade
```

This may install or remove packages when needed to complete upgrades.

Review the proposed changes before confirming.

## 10.5 Install a Package

```bash
sudo apt install nginx
```

Install multiple packages:

```bash
sudo apt install git curl unzip htop
```

## 10.6 Remove a Package

```bash
sudo apt remove nginx
```

Remove package configuration files as well:

```bash
sudo apt purge nginx
```

Remove unused dependencies:

```bash
sudo apt autoremove
```

## 10.7 Search for Packages

```bash
apt search nginx
```

Show package information:

```bash
apt show nginx
```

## 10.8 Important APT Commands

| Command | Purpose |
|---|---|
| `apt update` | Refresh package index |
| `apt upgrade` | Upgrade installed packages |
| `apt full-upgrade` | Upgrade with dependency changes where needed |
| `apt install` | Install packages |
| `apt remove` | Remove packages |
| `apt purge` | Remove packages and associated package configuration |
| `apt autoremove` | Remove automatically installed unused dependencies |
| `apt search` | Search packages |
| `apt show` | Display package information |

**Best practice:** Update the package index and apply security updates regularly. Use trusted repositories and review repository signing and package provenance.

---

# 11. Bash Shell Scripting

Bash scripting is one of the most useful Linux automation skills.

It allows you to combine Linux commands into reusable scripts.

Instead of running the same commands manually, you can automate them.

Examples:

- Backups.
- Deployment.
- Log cleanup.
- Server monitoring.
- Database maintenance.
- File processing.
- Scheduled reports.

## 11.1 What Is a Shell Script?

A shell script is a file containing commands executed by a shell.

Example:

```bash
#!/bin/bash

echo "Hello Linux"
```

Save it as:

```text
hello.sh
```

## 11.2 What Is a Shebang?

The first line:

```bash
#!/bin/bash
```

is called the shebang.

It specifies the interpreter used when the script is executed directly.

Another common form is:

```bash
#!/usr/bin/env bash
```

This finds Bash using the current environment's PATH.

## 11.3 Execute a Shell Script

Add executable permission:

```bash
chmod +x hello.sh
```

Run:

```bash
./hello.sh
```

You can also execute it explicitly:

```bash
bash hello.sh
```

## 11.4 Variables in Bash

Define a variable:

```bash
name="Linux"
```

Use it:

```bash
echo "$name"
```

Output:

```text
Linux
```

Important:

Do not put spaces around the assignment operator.

Correct:

```bash
name="Linux"
```

Incorrect:

```bash
name = "Linux"
```

## 11.5 Environment Variables

Environment variables are values made available to processes through their execution environment.

Example:

```bash
export APP_ENV=production
```

Read:

```bash
echo "$APP_ENV"
```

Environment variables are inherited by child processes when exported.

Check environment variables:

```bash
printenv
```

Check a specific variable:

```bash
printenv APP_ENV
```

## 11.6 Positional Arguments

Bash scripts can receive arguments.

Create:

```bash
#!/bin/bash

echo "First argument: $1"

echo "Second argument: $2"

echo "All arguments: $@"

echo "Number of arguments: $#"
```

Save as:

```text
args.sh
```

Run:

```bash
./args.sh Hello Linux
```

Output:

```text
First argument: Hello
Second argument: Linux
All arguments: Hello Linux
Number of arguments: 2
```

Important variables:

| Variable | Meaning |
|---|---|
| `$0` | Script name |
| `$1` | First argument |
| `$2` | Second argument |
| `$@` | All positional arguments |
| `$#` | Number of arguments |
| `$?` | Exit status of previous command |
| `$$` | Current shell process ID |
| `$!` | PID of most recent background process |

## 11.7 Command Substitution

You can capture command output in a variable.

Example:

```bash
current_date=$(date)

echo "$current_date"
```

Another example:

```bash
file_count=$(find . -type f | wc -l)

echo "Total files: $file_count"
```

## 11.8 Arithmetic Operations

Bash supports arithmetic expansion.

```bash
a=10

b=20

sum=$((a + b))

echo "$sum"
```

Output:

```text
30
```

Other operators:

```bash
echo $((10 - 5))

echo $((10 * 5))

echo $((10 / 5))

echo $((10 % 3))
```

---

# 12. Conditions in Bash

Conditions allow scripts to make decisions.

## 12.1 If Statement

Syntax:

```bash
if [ condition ]; then
    commands
fi
```

Example:

```bash
#!/bin/bash

age=25

if [ "$age" -ge 18 ]; then
    echo "Adult"
fi
```

## 12.2 If-Else

```bash
#!/bin/bash

number=10

if [ "$number" -gt 0 ]; then
    echo "Positive"
else
    echo "Not positive"
fi
```

## 12.3 If-Elif-Else

```bash
#!/bin/bash

marks=75

if [ "$marks" -ge 90 ]; then
    echo "Grade A"

elif [ "$marks" -ge 70 ]; then
    echo "Grade B"

elif [ "$marks" -ge 50 ]; then
    echo "Grade C"

else
    echo "Fail"
fi
```

## 12.4 Comparison Operators

Numeric comparisons:

| Operator | Meaning |
|---|---|
| `-eq` | Equal |
| `-ne` | Not equal |
| `-gt` | Greater than |
| `-ge` | Greater than or equal |
| `-lt` | Less than |
| `-le` | Less than or equal |

String comparisons:

| Operator | Meaning |
|---|---|
| `=` | Equal |
| `!=` | Not equal |
| `-z` | String is empty |
| `-n` | String is not empty |

File tests:

| Operator | Meaning |
|---|---|
| `-f` | Regular file exists |
| `-d` | Directory exists |
| `-e` | Path exists |
| `-r` | Readable |
| `-w` | Writable |
| `-x` | Executable |

Example:

```bash
#!/bin/bash

file="app.js"

if [ -f "$file" ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

## 12.5 Double Bracket Conditions

Bash also supports:

```bash
[[ condition ]]
```

Example:

```bash
name="Riyaz"

if [[ "$name" == "Riyaz" ]]; then
    echo "Name matched"
fi
```

`[[ ... ]]` is a Bash feature and provides safer handling of many string and pattern conditions than the older `[ ... ]` syntax.

For Bash scripts, it is often a good default.

---

# 13. Loops in Bash

Loops execute commands repeatedly.

Bash supports:

- `for`.
- `while`.
- `until`.

## 13.1 For Loop

Example:

```bash
#!/bin/bash

for i in 1 2 3 4 5
do
    echo "$i"
done
```

Output:

```text
1
2
3
4
5
```

### Loop Through Files

```bash
#!/bin/bash

for file in *.log
do
    echo "$file"
done
```

This iterates through matching `.log` files in the current directory.

If no matching file exists, Bash's default glob behavior may leave the pattern unchanged. Production scripts should account for that possibility.

## 13.2 While Loop

```bash
#!/bin/bash

count=1

while [ "$count" -le 5 ]
do
    echo "$count"

    count=$((count + 1))
done
```

## 13.3 Until Loop

An `until` loop continues while its condition is false.

```bash
#!/bin/bash

count=1

until [ "$count" -gt 5 ]
do
    echo "$count"

    count=$((count + 1))
done
```

## 13.4 Break and Continue

### Break

Terminates the loop.

```bash
for i in 1 2 3 4 5
do
    if [ "$i" -eq 3 ]; then
        break
    fi

    echo "$i"
done
```

### Continue

Skips the current iteration.

```bash
for i in 1 2 3 4 5
do
    if [ "$i" -eq 3 ]; then
        continue
    fi

    echo "$i"
done
```

Official reference: [GNU Bash Reference Manual](https://www.gnu.org/software/bash/manual/bash.html) [GNU](https://www.gnu.org/software/bash/manual/bash.html?utm_source=chatgpt.com)

---

# 14. Functions in Bash

Functions allow you to reuse code.

## 14.1 Create a Function

```bash
#!/bin/bash

greet() {
    echo "Hello Linux"
}

greet
```

Output:

```text
Hello Linux
```

## 14.2 Function Arguments

```bash
#!/bin/bash

greet() {
    echo "Hello $1"
}

greet "Developer"
```

Output:

```text
Hello Developer
```

## 14.3 Return Values

Bash functions return an exit status, not an arbitrary string or object like JavaScript functions.

Example:

```bash
#!/bin/bash

check_file() {
    if [[ -f "$1" ]]; then
        return 0
    else
        return 1
    fi
}

if check_file "app.js"; then
    echo "File exists"
else
    echo "File not found"
fi
```

By convention:

```text
0     = Success
Non-0 = Failure
```

## 14.4 Returning Data Through Output

If you want a function to produce text, use standard output.

```bash
get_date() {
    date +"%Y-%m-%d"
}

today=$(get_date)

echo "$today"
```

---

# 15. Bash Error Handling and Script Best Practices

Production scripts should handle failures carefully.

## 15.1 Exit Status

Every command returns an exit status.

Check:

```bash
echo $?
```

Usually:

```text
0 = Success
Non-zero = Failure
```

## 15.2 `set -euo pipefail`

A common strict-mode pattern:

```bash
#!/usr/bin/env bash

set -euo pipefail
```

Meaning:

| Option | Meaning |
|---|---|
| `-e` | Exit on many unhandled command failures |
| `-u` | Treat unset variables as errors |
| `-o pipefail` | A pipeline fails if a command within it fails |

Important: `set -e` has exceptions and context-dependent behavior. It is not a substitute for explicit error handling.

## 15.3 Error Handling Example

```bash
#!/usr/bin/env bash

set -euo pipefail

if ! command -v node >/dev/null 2>&1; then
    echo "Node.js is not installed" >&2
    exit 1
fi

echo "Node.js is installed"

node --version
```

## 15.4 Logging in Bash

```bash
#!/usr/bin/env bash

LOG_FILE="/tmp/deploy.log"

echo "$(date): Deployment started" >> "$LOG_FILE"

echo "$(date): Deployment completed" >> "$LOG_FILE"
```

## 15.5 Best Practices

1. Quote variable expansions.
2. Validate input arguments.
3. Check command availability.
4. Handle errors explicitly.
5. Use meaningful exit codes.
6. Avoid unnecessary root privileges.
7. Never hardcode passwords.
8. Use absolute paths in production scripts.
9. Log important operations.
10. Test scripts before scheduling them.

---

# 16. Cron Jobs and Scheduled Tasks

Cron is a time-based job scheduler.

It allows commands to execute automatically at specified times.

Examples:

- Database backups.
- Log cleanup.
- Daily reports.
- Temporary file deletion.
- Scheduled application maintenance.

## 16.1 What Is Cron?

Cron is a scheduler that runs commands according to configured time expressions.

A scheduled entry is called a cron job.

## 16.2 Open Crontab

Edit the current user's crontab:

```bash
crontab -e
```

List scheduled jobs:

```bash
crontab -l
```

Remove the current user's crontab:

```bash
crontab -r
```

Be careful: this removes all scheduled entries for that user.

## 16.3 Cron Syntax

A traditional cron entry contains five time fields followed by a command.

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week
│ │ │ └──── Month
│ │ └────── Day of month
│ └──────── Hour
└────────── Minute
```

## 16.4 Examples

Every minute:

```cron
* * * * * command
```

Every hour:

```cron
0 * * * * command
```

Every day at midnight:

```cron
0 0 * * * command
```

Every day at 2:30 AM:

```cron
30 2 * * * command
```

Every Sunday at 3 AM:

```cron
0 3 * * 0 command
```

Every five minutes:

```cron
*/5 * * * * command
```

## 16.5 Schedule a Backup Script

Suppose your backup script is:

```text
/home/ubuntu/backup.sh
```

Make it executable:

```bash
chmod +x /home/ubuntu/backup.sh
```

Schedule it daily at 2 AM:

```cron
0 2 * * * /home/ubuntu/backup.sh >> /home/ubuntu/backup.log 2>&1
```

This redirects both normal output and errors to the log file.

## 16.6 Important Cron Considerations

Cron jobs often run with a limited environment.

They may not have:

- Your interactive shell configuration.
- The same PATH.
- The same environment variables.
- The same working directory.

Therefore:

Prefer absolute command paths and explicitly configure required environment variables.

## 16.7 Cron vs systemd Timers

| Cron | systemd Timers |
|---|---|
| Traditional job scheduler | systemd scheduling mechanism |
| Simple time-based scheduling | Integrates with systemd services |
| Common on Unix-like systems | Common on systemd-based Linux distributions |
| Limited service lifecycle integration | Supports service dependencies and journaling |

For more advanced service scheduling, systemd timers are worth learning.

---

# 17. Log Management and Log Rotation

Logs are records of system and application activity.

They are essential for troubleshooting and security monitoring.

## 17.1 What Are Logs?

Examples:

```text
Application started
User logged in
Database connection established
Payment failed
Nginx returned 502
```

Logs help engineers understand what happened.

## 17.2 Linux Log Directory

Common log directory:

```text
/var/log
```

List log files:

```bash
ls -lah /var/log
```

Common files on Ubuntu include:

| File | Purpose |
|---|---|
| `/var/log/syslog` | General system messages |
| `/var/log/auth.log` | Authentication-related events |
| `/var/log/kern.log` | Kernel messages, where configured |
| `/var/log/dpkg.log` | Package management activity |
| `/var/log/nginx/access.log` | Nginx access log |
| `/var/log/nginx/error.log` | Nginx error log |

The exact files depend on the distribution and logging configuration.

## 17.3 View Logs

```bash
sudo tail -f /var/log/syslog
```

View Nginx access logs:

```bash
sudo tail -f /var/log/nginx/access.log
```

View Nginx error logs:

```bash
sudo tail -f /var/log/nginx/error.log
```

## 17.4 What Is journald?

systemd-journald collects and manages system logs.

Use `journalctl` to query these logs.

View all available journal entries:

```bash
sudo journalctl
```

View recent entries:

```bash
sudo journalctl -n 50
```

Follow logs:

```bash
sudo journalctl -f
```

View logs for Nginx:

```bash
sudo journalctl -u nginx
```

View logs since today:

```bash
sudo journalctl --since today
```

View logs from the previous boot:

```bash
sudo journalctl -b -1
```

## 17.5 What Is Log Rotation?

Applications can generate large log files.

If logs grow indefinitely, they can consume disk space.

Log rotation manages log files by:

- Rotating old files.
- Compressing older logs.
- Keeping a defined number of backups.
- Removing logs beyond retention policies.

## 17.6 What Is logrotate?

`logrotate` is a utility that manages log rotation.

Configuration directory:

```text
/etc/logrotate.d/
```

Example configuration:

```text
/var/www/myapp/logs/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    copytruncate
}
```

Explanation:

| Directive | Meaning |
|---|---|
| `daily` | Rotate daily |
| `rotate 7` | Keep seven rotated files |
| `compress` | Compress rotated files |
| `missingok` | Do not report an error if the file is missing |
| `notifempty` | Do not rotate empty files |
| `copytruncate` | Copy the file and truncate the original |

**Important:** `copytruncate` can lose log entries written during the copy/truncate window. Where possible, prefer an application's supported log-reopen mechanism.

---

# 18. Disk and Memory Management

Linux servers need monitoring to avoid resource exhaustion.

Important resources include:

- Disk capacity.
- RAM.
- Swap.
- CPU.
- Inodes.

## 18.1 Check Disk Usage

```bash
df -h
```

Example:

```text
Filesystem      Size  Used  Avail  Use%
/dev/sda1        50G   20G    30G   40%
```

`df` reports filesystem-level space usage.

## 18.2 Check Directory Size

```bash
du -sh /var/www
```

Show directory sizes:

```bash
du -h --max-depth=1 /var
```

## 18.3 Check Memory Usage

```bash
free -h
```

Example:

```text
              total   used   free   shared
Mem:           8Gi    3Gi    2Gi     ...
Swap:          2Gi    0Gi    2Gi
```

Linux uses available memory for caching, so low-looking `free` memory alone does not necessarily mean the server is under memory pressure.

Pay attention to the `available` value.

## 18.4 What Is Swap?

Swap is disk-backed memory used to support memory management when RAM pressure occurs.

It can help avoid some out-of-memory situations, but it is much slower than RAM.

Check:

```bash
swapon --show
```

## 18.5 Check CPU Usage

```bash
top
```

Or:

```bash
htop
```

## 18.6 Check System Load Average

```bash
uptime
```

Example:

```text
load average: 0.50, 0.75, 0.80
```

Load average represents the average number of tasks that are runnable or in uninterruptible sleep over different time windows.

It should be interpreted relative to CPU capacity and workload characteristics.

## 18.7 Check Inode Usage

```bash
df -i
```

A filesystem can run out of inodes even when it still has free disk capacity.

This can happen when a system creates a very large number of small files.

## 18.8 Find Large Files

```bash
sudo find /var -type f -size +100M -exec ls -lh {} \;
```

## 18.9 Disk and Memory Commands

| Command | Purpose |
|---|---|
| `df -h` | Filesystem capacity |
| `du -sh` | Directory size |
| `free -h` | Memory information |
| `top` | CPU and process monitoring |
| `htop` | Interactive process monitoring |
| `uptime` | System uptime and load average |
| `swapon --show` | Swap configuration |
| `df -i` | Inode usage |
| `lsblk` | Block devices |
| `mount` | Mounted filesystems |

---

# 19. Linux Networking Commands

Linux provides several commands for inspecting network configuration.

## 19.1 Check IP Addresses

```bash
ip addr
```

Or:

```bash
ip a
```

## 19.2 Check Network Interfaces

```bash
ip link
```

## 19.3 Check Routing Table

```bash
ip route
```

## 19.4 Check Listening Ports

```bash
sudo ss -tulnp
```

## 19.5 Check a Particular Port

```bash
sudo ss -tulnp | grep :3000
```

## 19.6 Check DNS

```bash
resolvectl status
```

On systems using systemd-resolved.

Check a hostname:

```bash
resolvectl query example.com
```

## 19.7 Test Connectivity

```bash
ping -c 4 example.com
```

## 19.8 Test an HTTP Endpoint

```bash
curl -I https://example.com
```

## 19.9 Network Troubleshooting Workflow

Suppose your Node.js application is not accessible.

Investigate:

**Step 1: Is the process running?**

```bash
ps aux | grep node
```

**Step 2: Is the port listening?**

```bash
sudo ss -tulnp | grep :3000
```

**Step 3: Can you access it locally?**

```bash
curl http://127.0.0.1:3000
```

**Step 4: Is Nginx running?**

```bash
sudo systemctl status nginx
```

**Step 5: Is the firewall allowing the required traffic?**

```bash
sudo ufw status
```

**Step 6: Is DNS pointing to the correct server?**

```bash
dig example.com
```

**Step 7: Check Nginx logs.**

```bash
sudo tail -f /var/log/nginx/error.log
```

This systematic approach helps isolate the problem.

---

# 20. SSH and Secure Server Access

SSH stands for **Secure Shell**.

It is used to securely access remote machines over a network.

## 20.1 Why Do We Need SSH?

Suppose you have an Ubuntu VPS.

You want to:

- Install packages.
- Deploy applications.
- Configure Nginx.
- Restart services.
- Monitor logs.

You can perform these operations remotely through SSH.

## 20.2 Connect to a Server

```bash
ssh username@SERVER_IP
```

Example:

```bash
ssh ubuntu@203.0.113.10
```

## 20.3 SSH with a Private Key

```bash
ssh -i my-key.pem ubuntu@203.0.113.10
```

On supported Linux environments, protect the private key:

```bash
chmod 600 my-key.pem
```

## 20.4 What Is SSH Key Authentication?

SSH commonly supports public-key authentication.

A key pair contains:

- Public key.
- Private key.

The public key is placed on the server.

The private key remains with the client.

Typical location:

```text
~/.ssh/authorized_keys
```

## 20.5 Generate an SSH Key Pair

```bash
ssh-keygen -t ed25519 -C "developer@example.com"
```

This creates a private key and public key.

Typical files:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Do not share the private key.

## 20.6 Copy a Public Key to the Server

Where available:

```bash
ssh-copy-id ubuntu@203.0.113.10
```

Alternatively, add the public key to the remote user's:

```text
~/.ssh/authorized_keys
```

Ensure the directory and file permissions are appropriately restricted.

## 20.7 SSH Configuration

Common configuration file:

```text
/etc/ssh/sshd_config
```

Examples of security-related settings:

```text
PermitRootLogin no

PubkeyAuthentication yes

PasswordAuthentication no
```

These are illustrative settings, not instructions to apply blindly.

Before disabling password authentication:

1. Verify key-based login works.
2. Keep an existing administrative session open.
3. Validate the SSH configuration.
4. Reload the service.
5. Test a new connection.

Validate configuration:

```bash
sudo sshd -t
```

Reload:

```bash
sudo systemctl reload ssh
```

**Critical:** Do not disable password authentication until you have verified an alternative login method. Otherwise, you may lose access to the server.

Official reference: [Ubuntu OpenSSH Server Documentation](https://ubuntu.com/server/docs/how-to/security/openssh-server/)

---

# 21. Linux Security and Server Hardening

Server hardening means reducing unnecessary security risks by configuring the system securely.

## 21.1 Update the Operating System

```bash
sudo apt update

sudo apt upgrade
```

Apply security updates regularly.

For production servers, define a patching and reboot policy.

## 21.2 Create a Non-Root Application User

Avoid running application processes as root unless necessary.

Example:

```bash
sudo adduser appuser
```

Assign application ownership:

```bash
sudo chown -R appuser:appuser /var/www/myapp
```

Configure the service to run as this user.

## 21.3 Configure Firewall

Install UFW if necessary:

```bash
sudo apt install ufw
```

Before enabling it, allow the required administrative and application traffic.

For a basic web server:

```bash
sudo ufw allow 22/tcp

sudo ufw allow 80/tcp

sudo ufw allow 443/tcp
```

Enable:

```bash
sudo ufw enable
```

Check:

```bash
sudo ufw status verbose
```

Restrict SSH to a known administrative IP where practical:

```bash
sudo ufw allow from ADMIN_IP to any port 22 proto tcp
```

Replace `ADMIN_IP` with the actual trusted address.

Ubuntu's official security guidance recommends using firewalls, SSH and least-privilege access controls. [Ubuntu](https://ubuntu.com/server/docs/explanation/security/security_suggestions/?utm_source=chatgpt.com)

## 21.4 Install Fail2ban

Fail2ban can monitor logs and temporarily block IP addresses associated with repeated suspicious authentication attempts.

Install:

```bash
sudo apt install fail2ban
```

Check status:

```bash
sudo systemctl status fail2ban
```

Check SSH jail:

```bash
sudo fail2ban-client status sshd
```

Configuration should be reviewed for your actual SSH service and logging environment.

## 21.5 Secure Environment Variables

Never store application secrets in public repositories.

Example:

```env
DATABASE_PASSWORD=secret

JWT_SECRET=secret

PAYMENT_API_KEY=secret
```

Protect environment files:

```bash
chmod 600 .env
```

Ensure they are excluded from Git:

```gitignore
.env
.env.*
!.env.example
```

For production, consider using a managed secrets solution.

## 21.6 Secure Application Ports

A common production setup:

```text
Public:
80
443

Restricted:
22

Private:
3000
4000
3306
6379
27017
```

Only expose ports required for the architecture.

## 21.7 Additional Security Practices

- [ ] Use non-root application accounts.
- [ ] Use SSH keys.
- [ ] Restrict SSH access.
- [ ] Enable a firewall.
- [ ] Apply security updates.
- [ ] Remove unnecessary services.
- [ ] Use HTTPS.
- [ ] Protect secrets.
- [ ] Restrict database access.
- [ ] Monitor authentication logs.
- [ ] Configure backups.
- [ ] Monitor disk capacity.
- [ ] Review user accounts regularly.

Official reference: [Ubuntu Server Security Documentation](https://ubuntu.com/server/docs/security/)

---

# 22. Production Server Administration

Now let's combine these concepts into a realistic deployment scenario.

Suppose you have:

- Next.js frontend.
- Express backend.
- MySQL database.
- MongoDB.
- Redis.
- Nginx.
- Ubuntu VPS.

## 22.1 Production Server Architecture

```text
                    INTERNET
                       |
                       v
                    Firewall
                       |
                       v
                     Nginx
                    80 / 443
                       |
             ┌─────────┴─────────┐
             |                   |
             v                   v
         Next.js              Express
         Port 3000            Port 4000
             |                   |
             └─────────┬─────────┘
                       |
             ┌─────────┼─────────┐
             |         |         |
             v         v         v
           MySQL    MongoDB     Redis
          3306      27017      6379
```

In a production environment, databases and caches should generally be isolated from public access.

## 22.2 Recommended Project Deployment Structure

```text
/var/www/
│
├── frontend/
│   ├── .next/
│   ├── public/
│   ├── package.json
│   └── ...
│
└── backend/
    ├── dist/
    ├── package.json
    └── ...
```

A separate deployment directory can be used for each application.

## 22.3 Process Management

You can use:

- systemd.
- PM2.
- Docker.
- Kubernetes.

Example with PM2:

```bash
pm2 start npm --name frontend -- start

pm2 start dist/server.js --name backend
```

Save the process list:

```bash
pm2 save
```

Configure startup integration:

```bash
pm2 startup
```

Follow the command PM2 prints to configure its startup service.

## 22.4 Nginx Reverse Proxy

Nginx receives public requests and forwards them to application processes.

```text
Users
  |
  v
Nginx
  |
  ├── Next.js :3000
  |
  └── Express :4000
```

## 22.5 Monitoring

Monitor:

```bash
htop
```

```bash
df -h
```

```bash
free -h
```

```bash
sudo systemctl status nginx
```

```bash
sudo journalctl -u myapp -f
```

## 22.6 Backup Strategy

A production server should have a backup plan for:

- Databases.
- Uploaded files.
- Application configuration.
- Critical infrastructure configuration.
- Important logs, where required.

Do not treat a backup stored only on the same VPS as a complete disaster-recovery strategy.

Maintain copies in an independent storage location and test restoration.

---

# 23. Practical Automation Project – Linux Server Health Monitor

Let's build a Bash script that monitors basic server resources.

## Project Structure

```text
server-monitor/
│
└── monitor.sh
```

## Step 1: Create the Script

```bash
nano monitor.sh
```

## Step 2: Add the Following Code

```bash
#!/usr/bin/env bash

set -u

LOG_FILE="/var/log/server-monitor.log"

log_message() {
    local message="$1"

    echo "$(date '+%Y-%m-%d %H:%M:%S') - $message" \
        | tee -a "$LOG_FILE"
}

check_disk() {
    local usage

    usage=$(df -P / | awk 'NR==2 {gsub("%", "", $5); print $5}')

    if [[ "$usage" -ge 80 ]]; then
        log_message "WARNING: Disk usage is ${usage}%"
    else
        log_message "Disk usage is ${usage}%"
    fi
}

check_memory() {
    local usage

    usage=$(free | awk '/Mem:/ {
        if ($2 > 0) {
            printf "%.0f", ($3 / $2) * 100
        }
    }')

    if [[ -z "$usage" ]]; then
        log_message "ERROR: Unable to determine memory usage"
        return 1
    fi

    if [[ "$usage" -ge 80 ]]; then
        log_message "WARNING: Memory usage is ${usage}%"
    else
        log_message "Memory usage is ${usage}%"
    fi
}

check_service() {
    local service="$1"

    if systemctl is-active --quiet "$service"; then
        log_message "$service is running"
    else
        log_message "WARNING: $service is not running"
    fi
}

main() {
    log_message "Server monitoring started"

    check_disk

    check_memory

    check_service nginx

    check_service ssh

    log_message "Server monitoring completed"
}

main "$@"
```

## Step 3: Make It Executable

```bash
chmod +x monitor.sh
```

## Step 4: Run It

```bash
sudo ./monitor.sh
```

The script checks:

1. Root filesystem usage.
2. Memory usage.
3. Nginx service status.
4. SSH service status.

It also records results in:

```text
/var/log/server-monitor.log
```

**Note:** The memory calculation above reports used memory as a percentage of total RAM. For capacity planning, also inspect `MemAvailable`, because Linux uses RAM for caches.

## Step 5: Schedule It with Cron

Open:

```bash
sudo crontab -e
```

Run every five minutes:

```cron
*/5 * * * * /path/to/monitor.sh
```

For a production monitoring system, also consider centralized metrics, alerting and log collection rather than relying only on a local script.

---

# 24. Common Linux Administration Mistakes

| Mistake | Correct Approach |
|---|---|
| Running every command with `sudo` | Use least privilege |
| Using `chmod 777` to fix permissions | Configure correct ownership and permissions |
| Running applications as root | Use dedicated application users |
| Killing processes with `kill -9` immediately | Try graceful termination first |
| Using `&` as production process management | Use systemd, PM2 or containers |
| Editing system files without backups | Back up and validate configuration |
| Ignoring logs | Monitor application and system logs |
| Forgetting disk monitoring | Monitor filesystem and inode usage |
| Running scripts without testing | Validate scripts before production use |
| Using relative paths in Cron | Prefer absolute paths |
| Hardcoding secrets in scripts | Use secure secrets management |
| Disabling SSH passwords before testing keys | Verify alternative access first |
| Exposing database ports | Restrict database access |
| Deleting logs manually without rotation | Configure logrotate or centralized logging |
| Updating production without planning | Use a controlled patching and rollback strategy |

---

# 25. Interview Questions and Answers

## Basic Level

### Q1. What is Linux?

Linux is an open-source, Unix-like operating system kernel used by many operating system distributions.

### Q2. What is a Linux distribution?

A Linux distribution combines the Linux kernel with system tools, libraries, package management and applications.

### Q3. What is the Linux kernel?

The kernel manages system resources such as CPU, memory, processes, devices and networking.

### Q4. What is a shell?

A shell is a command interpreter that allows users to execute commands and scripts.

### Q5. What is Bash?

Bash is the Bourne Again Shell, a widely used command-line shell and scripting language.

### Q6. What is the root directory?

The root directory `/` is the top-level directory of the Linux filesystem hierarchy.

### Q7. What is the difference between `/home` and `/root`?

`/home` generally contains regular users' home directories, while `/root` is the root user's home directory.

### Q8. What is the purpose of `/etc`?

It stores system-wide configuration files.

### Q9. What is the purpose of `/var/log`?

It commonly stores system and application logs.

### Q10. What is `sudo`?

`sudo` allows authorized users to execute commands with elevated privileges.

## Intermediate Level

### Q11. What is the difference between `chmod` and `chown`?

`chmod` changes permissions, while `chown` changes ownership.

### Q12. What is the difference between `755` and `644`?

`755` gives the owner read, write and execute permissions, while group and others receive read and execute.

`644` gives the owner read and write permissions, while group and others receive read permission.

### Q13. What is a process?

A process is an instance of a running program.

### Q14. What is PID?

PID stands for Process ID, a unique identifier for a process during its lifetime.

### Q15. What is the difference between `kill` and `kill -9`?

`kill` sends SIGTERM by default, allowing graceful termination.

`kill -9` sends SIGKILL, forcing immediate termination.

### Q16. What is systemd?

systemd is a system and service manager used to initialize and manage Linux services and other system units.

### Q17. What is `systemctl`?

It is a command-line utility used to manage systemd units.

### Q18. What is APT?

APT is a package-management system used by Ubuntu and Debian-based distributions.

### Q19. What is Cron?

Cron is a time-based job scheduler used to run commands automatically.

### Q20. What is logrotate?

Logrotate manages log files by rotating, compressing and removing old logs according to configured policies.

## Advanced Level

### Q21. What is the difference between a process and a service?

A process is a running program instance. A service is a background application or system function, commonly managed by a service manager such as systemd.

### Q22. What is the difference between restart and reload?

Restart stops and starts a service. Reload asks a service to reload configuration without necessarily restarting the entire process.

### Q23. What is the difference between `df` and `du`?

`df` reports filesystem-level capacity and usage.

`du` estimates the disk space used by files and directories.

### Q24. What is the difference between RAM and swap?

RAM is physical main memory. Swap is disk-backed space used by the memory-management system.

### Q25. What is a Bash script?

A Bash script is a file containing commands and programming constructs executed by the Bash shell.

### Q26. What does `set -euo pipefail` do?

It enables several useful error-handling behaviors:

- Exit on many unhandled command failures.
- Treat unset variables as errors.
- Propagate failures through pipelines.

### Q27. What is the difference between Cron and systemd timers?

Cron provides traditional time-based job scheduling. systemd timers integrate scheduled execution with systemd service management.

### Q28. How do you find which process is using port 3000?

Use:

```bash
sudo lsof -i :3000
```

or:

```bash
sudo ss -tulnp | grep :3000
```

### Q29. How do you investigate a service that is not starting?

Check:

```bash
sudo systemctl status service-name
```

Then:

```bash
sudo journalctl -u service-name -n 100
```

Inspect its configuration, permissions, dependencies and application logs.

### Q30. How do you secure a Linux production server?

I would:

- Use least-privilege accounts.
- Configure SSH securely.
- Restrict administrative access.
- Enable a firewall.
- Apply security patches.
- Use HTTPS.
- Restrict service ports.
- Protect secrets.
- Monitor logs.
- Configure backups.
- Monitor resource consumption.

---

# 26. Practical Assignments

## Assignment 1: Linux Fundamentals

- [ ] Install Ubuntu Server in a virtual machine or use a test VPS.
- [ ] Check the distribution version.
- [ ] Check the kernel version.
- [ ] Identify the current shell.
- [ ] Explore the Linux filesystem hierarchy.
- [ ] Practice absolute and relative paths.

## Assignment 2: Essential Commands

- [ ] Practice `pwd`, `ls` and `cd`.
- [ ] Create files using `touch`.
- [ ] Create directories using `mkdir`.
- [ ] Copy files using `cp`.
- [ ] Move files using `mv`.
- [ ] Delete test files using `rm`.
- [ ] Search files using `find`.
- [ ] Search text using `grep`.
- [ ] Use pipes and redirection.

## Assignment 3: Permissions and Users

- [ ] Create a Linux user.
- [ ] Create a group.
- [ ] Add the user to the group.
- [ ] Configure file ownership.
- [ ] Practice `chmod`.
- [ ] Understand `755`, `750`, `644` and `600`.
- [ ] Configure a shared directory.
- [ ] Inspect `/etc/passwd` and `/etc/group`.

## Assignment 4: Process Management

- [ ] Start a Node.js application.
- [ ] Find its PID.
- [ ] Inspect it using `ps`.
- [ ] Monitor it using `top` or `htop`.
- [ ] Identify its listening port.
- [ ] Terminate it gracefully.
- [ ] Understand process signals.

## Assignment 5: systemd

- [ ] Create a custom Node.js service.
- [ ] Configure its working directory.
- [ ] Configure its execution user.
- [ ] Start the service.
- [ ] Enable it at boot.
- [ ] Inspect its status.
- [ ] Read its logs.
- [ ] Test its restart behavior.

## Assignment 6: Bash Scripting

- [ ] Write a Hello World script.
- [ ] Create variables.
- [ ] Use command-line arguments.
- [ ] Write conditional statements.
- [ ] Write loops.
- [ ] Create functions.
- [ ] Handle command failures.
- [ ] Write a file backup script.

## Assignment 7: Cron and Logging

- [ ] Create a scheduled task.
- [ ] Run a backup every day.
- [ ] Redirect output to a log file.
- [ ] Inspect system logs.
- [ ] Inspect systemd journal logs.
- [ ] Configure logrotate.
- [ ] Test log retention.

## Assignment 8: Server Monitoring

- [ ] Monitor CPU usage.
- [ ] Monitor memory usage.
- [ ] Check filesystem usage.
- [ ] Check inode usage.
- [ ] Identify large files.
- [ ] Check listening ports.
- [ ] Monitor Nginx.
- [ ] Monitor Node.js.

## Assignment 9: Linux Security

- [ ] Create a non-root application user.
- [ ] Configure SSH key authentication.
- [ ] Restrict SSH access.
- [ ] Configure UFW.
- [ ] Review application permissions.
- [ ] Protect environment variables.
- [ ] Install security updates.
- [ ] Review authentication logs.

---

# 27. Quick Revision Notes

| Topic | Key Point |
|---|---|
| Linux | Open-source Unix-like kernel |
| Distribution | Linux kernel plus system software |
| Kernel | Core operating system component |
| Shell | Command interpreter |
| Bash | Bourne Again Shell |
| Root `/` | Top-level filesystem directory |
| `/etc` | Configuration files |
| `/home` | Regular users' home directories |
| `/root` | Root user's home |
| `/var/log` | Common log directory |
| `pwd` | Current directory |
| `ls` | List files |
| `cd` | Change directory |
| `mkdir` | Create directory |
| `cp` | Copy |
| `mv` | Move or rename |
| `rm` | Remove |
| `grep` | Search text |
| `find` | Find files |
| `chmod` | Change permissions |
| `chown` | Change ownership |
| `sudo` | Elevated command execution |
| PID | Process ID |
| `ps` | Process information |
| `top` | Process monitoring |
| `kill` | Send process signal |
| systemd | System and service manager |
| `systemctl` | Manage systemd units |
| APT | Package management |
| Bash Script | Automated shell commands |
| Cron | Time-based scheduling |
| `journalctl` | Query systemd journal |
| logrotate | Log file rotation |
| `df` | Filesystem usage |
| `du` | Directory/file disk usage |
| `free` | Memory usage |
| `ss` | Socket information |
| SSH | Secure remote shell |
| UFW | Firewall management utility |

---

# 28. Phase 40 Completion Checklist

You should now be able to:

- [ ] Explain Linux architecture.
- [ ] Understand the Linux kernel.
- [ ] Explain shells and terminals.
- [ ] Identify Linux distributions.
- [ ] Navigate the filesystem hierarchy.
- [ ] Use essential Linux commands.
- [ ] Manage files and directories.
- [ ] Understand file permissions.
- [ ] Configure ownership.
- [ ] Create users and groups.
- [ ] Manage processes.
- [ ] Understand process signals.
- [ ] Manage systemd services.
- [ ] Install packages using APT.
- [ ] Write Bash scripts.
- [ ] Use variables and arguments.
- [ ] Write conditions and loops.
- [ ] Create reusable functions.
- [ ] Handle script errors.
- [ ] Schedule Cron jobs.
- [ ] Inspect system logs.
- [ ] Configure log rotation.
- [ ] Monitor CPU, RAM and disk.
- [ ] Troubleshoot networking.
- [ ] Configure SSH access.
- [ ] Harden Linux servers.
- [ ] Deploy and monitor a Node.js application.

---

# 29. Official Learning Resources

| Resource | Documentation |
|---|---|
| Ubuntu Server | [Ubuntu Server Documentation](https://ubuntu.com/server/docs/) |
| Linux Kernel | [The Linux Kernel Documentation](https://docs.kernel.org/) |
| GNU Bash | [Bash Reference Manual](https://www.gnu.org/software/bash/manual/bash.html) |
| GNU Coreutils | [Coreutils Manual](https://www.gnu.org/software/coreutils/manual/coreutils.html) |
| systemd | [systemd Documentation](https://systemd.io/) |
| APT | [Debian APT Documentation](https://www.debian.org/doc/manuals/apt-guide/) |
| OpenSSH | [OpenSSH Manual](https://www.openssh.com/manual.html) |
| Cron | [Cron Manual](https://man7.org/linux/man-pages/man8/cron.8.html) |
| logrotate | [logrotate Manual](https://man7.org/linux/man-pages/man8/logrotate.8.html) |
| Linux Commands | [Linux man-pages](https://man7.org/linux/man-pages/) |

---

# 30. What Comes Next?

## Phase 41 – Advanced Git, GitHub, Version Control and Team Collaboration

In the next phase, we will study Git and GitHub from a professional software engineering perspective.

Planned topics:

1. Version Control System Fundamentals.
2. Git Architecture.
3. Git Installation and Configuration.
4. Git Repository Fundamentals.
5. Working Directory, Staging Area and Repository.
6. Git Add, Commit and Status.
7. Git Log and History.
8. Branching and Merging.
9. Merge Conflicts.
10. Git Rebase vs Merge.
11. Interactive Rebase.
12. Cherry-Pick.
13. Git Reset vs Revert.
14. Git Stash.
15. Git Tags.
16. Remote Repositories.
17. Git Push, Pull and Fetch.
18. GitHub Repository Management.
19. Pull Requests and Code Reviews.
20. GitHub Issues and Projects.
21. GitHub Actions and CI/CD Integration.
22. Branching Strategies.
23. Git Workflows for Teams.
24. Git Security and Best Practices.
25. Real-World Git Troubleshooting.

**Phase 40 completed.**

When you're ready, say **"OK, Phase 41"** to continue.