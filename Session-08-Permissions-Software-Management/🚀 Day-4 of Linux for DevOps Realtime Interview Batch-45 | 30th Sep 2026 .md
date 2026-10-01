# Linux Day 4 - Session Summary

A comprehensive, student-friendly reference guide based on the Day 4 Linux & Cloud Administration session.

---

## 1. Linux Directory Structure & Architecture Review

### 1.1 Key System Directories

In Linux, everything starts from the root directory (`/`), which functions similarly to the `C:\` drive in Windows.

- **`/` (Root Directory)**:
  - The top-level directory of the entire Linux filesystem hierarchy.
  - Access using: `cd /`
- **`/etc` (System & Software Configurations)**:
  - Equivalent to configuration storage like `C:\Windows` in Windows.
  - Houses all host configuration files, package settings, user lists, and system parameters.
  - *Example*: `/etc/os-release` contains OS distribution name, version, and architecture details.
    ```bash
    cat /etc/os-release
    ```
- **`/bin` (Binary Executables)**:
  - Contains essential compiled binary executable programs and commands ready to run directly without compilation.
- **`/root` vs `/home`**:
  - **`/root`**: The dedicated home directory for the administrative `root` user.
  - **`/home/<username>`**: The dedicated home directories for non-root, regular users (e.g., `/home/vikas`).
- **`/tmp` (Temporary Storage)**:
  - Dedicated space for temporary files created by users and applications.
  - All users typically have write access to `/tmp`.
  - Automatically cleaned upon reboot or by system maintenance jobs.
- **`/dev` (Device Files)**:
  - Represents physical and virtual hardware devices (disks, terminals, sound cards) as special device nodes.

---

### 1.2 User Elevation & Switching
- **`sudo -i`**: Elevates the session to an interactive shell with the full root environment.
- **`su` (Switch User)**: Used to switch to another user account in the system.

```bash
# Switch to root interactive shell
sudo -i

# Switch to a specific user
su <username>
```

---

### 1.3 32-Bit vs 64-Bit Architecture
- **Legacy 32-bit Architecture (`x86`)**: Outdated and unsupported by modern enterprise operating systems (e.g., Windows 11, modern Linux distributions).
- **64-bit Architecture (`x86_64`)**: The standard for current cloud virtual machines, operating systems, and enterprise software.
- Check system architecture:
  ```bash
  uname -m
  ```

---

## 2. Vi / Vim Text Editor Fundamentals

The `vi` (or `vim`) text editor is the universal, built-in command-line text editor available across all Linux distributions.

### 2.1 The Two Primary Modes
1. **Command Mode (Default)**:
   - When a file is opened, vi starts in Command Mode.
   - Used for cursor navigation, deleting lines, copying/pasting, searching, and running commands.
   - Text cannot be directly typed into the document while in Command Mode.
2. **Insert Mode**:
   - Used to type, edit, and insert content into the file.
   - Enter Insert Mode by pressing `i`.
   - Exit Insert Mode and return to Command Mode by pressing `Esc`.

---

### 2.2 Essential Vi/Vim Commands Cheat Sheet

| Action | Command / Key | Mode | Description |
| :--- | :--- | :--- | :--- |
| **Enter Insert Mode** | `i` | Command Mode | Switches to typing mode at current cursor position |
| **Return to Command Mode** | `Esc` | Insert Mode | Exits text entry mode |
| **Save File** | `:w` | Command Mode | Writes changes to disk without closing the file |
| **Save and Quit** | `:wq` (or `:x`) | Command Mode | Saves all changes and exits vi |
| **Quit without Saving** | `:q!` | Command Mode | Discards all changes and forces vi to quit |
| **Undo Last Action** | `u` | Command Mode | Undoes the previous edit (equivalent to Ctrl + Z) |
| **Delete Current Line** | `dd` | Command Mode | Cuts/deletes the entire line where cursor is located |
| **Delete Multiple Lines** | `3dd` | Command Mode | Deletes 3 lines starting from cursor position |
| **Display Line Numbers** | `:set nu` | Command Mode | Shows line numbers on the left margin |
| **Hide Line Numbers** | `:set nonu` | Command Mode | Hides line numbers |
| **Paste in Terminal** | Right-Click | Any Mode | In tools like MobaXterm/PuTTY, mouse right-click pastes clipboard text |

---

## 3. System Administration: Password Aging Policy

### 3.1 Managing Password Expiration (`chage`)
Security best practices require passwords to expire periodically (e.g., every 60 or 90 days) in enterprise environments.

- **`chage` (Change Age)**: Command-line tool used by system administrators to view and configure user password aging policies.
- **View Policy for a User**:
  ```bash
  chage -l <username>
  ```
- **Configure Policy (Interactive)**:
  ```bash
  chage <username>
  ```
  Parameters configured include:
  - Minimum Password Age (days before password can be changed)
  - Maximum Password Age (days until password must be changed, e.g., 60 days)
  - Password Inactive / Expiration Warning Days

---

## 4. Operating System Patching & Maintenance

### 4.1 What is Patching?
- **Definition**: The process of applying updates to installed packages, software libraries, and the operating system kernel to resolve security vulnerabilities (Common Vulnerabilities and Exposures - CVEs), fix bugs, and enhance system stability.
- **Key Commands (Debian/Ubuntu)**:
  ```bash
  # Step 1: Update repository package indexes
  apt update

  # Step 2: Upgrade packages to patched versions
  apt upgrade -y
  ```

### 4.2 Production Best Practices
- **Environment Staging**: Apply and test patches in lower environments (Dev, QA, Staging) prior to Production deployment.
- **Maintenance Windows**: Execute patching during scheduled maintenance periods (e.g., weekends, off-peak hours).
- **System Reboot**: If kernel or core shared libraries are updated during patching, reboot the server to apply the changes:
  ```bash
  reboot
  ```
- **Interview Insight**:
  - *Question*: "Have you performed patching?"
  - *Answer*: Yes, OS security patching involves scanning for outdated packages, reviewing CVE security advisories, scheduling change requests (CR), applying updates via package managers (`apt`/`yum`), verifying running application services, and rebooting if kernel packages were upgraded.

---

## 5. Web Server Deployment & Service Management (`systemctl`)

### 5.1 Installing and Testing Nginx
Nginx is a lightweight, high-performance web server and reverse proxy.

```bash
# 1. Update package lists
apt update

# 2. Install Nginx web server
apt install nginx -y
```

### 5.2 Accessing the Web Server
- Obtain the VM's external IP address.
- Open a web browser and navigate to: `http://<EXTERNAL_IP>` (using HTTP on port 80).
- By default, the standard "Welcome to nginx!" landing page is rendered.

---

### 5.3 Customizing Static Content (Document Root)
- **Default Document Root**: `/var/www/html/`
- **Main Webpage File**: `/var/www/html/index.html` (or `index.nginx-debian.html`)
- To modify the website content:
  ```bash
  cd /var/www/html
  vi index.html
  ```
- Replacing or editing HTML content in this file immediately updates what users see when accessing the web server IP in their browser.

---

### 5.4 Service Management with `systemctl`
Linux uses `systemd` and its management utility `systemctl` to control background daemon services.

| Command | Action | Description |
| :--- | :--- | :--- |
| `systemctl status nginx` | Check Health | Displays whether the service is `active (running)` or `inactive (dead)`, its PID, and recent logs |
| `systemctl stop nginx` | Stop Service | Shuts down the web server; browser access immediately stops working |
| `systemctl start nginx` | Start Service | Launches the web server; browser access resumes immediately |
| `systemctl restart nginx` | Restart Service | Stops and restarts the service; required when applying configuration changes |
| `systemctl enable nginx` | Enable on Boot | Configures the service to automatically start when the server reboots |
| `systemctl disable nginx` | Disable on Boot | Prevents the service from starting automatically during boot |

### 5.5 Universal DevOps Pattern
The `systemctl` command syntax is universal across Linux distributions and applies to all DevOps tooling:
- **Docker**: `systemctl status docker` \| `systemctl start docker` \| `systemctl restart docker`
- **Jenkins**: `systemctl status jenkins` \| `systemctl restart jenkins`
- **Apache**: `systemctl status apache2` \| `systemctl stop apache2`

---

## 6. Shell Scripting Fundamentals & File Permissions

### 6.1 Creating a First Shell Script
A shell script is an automated file containing a sequence of commands that the shell executes sequentially.

1. Create a script file (e.g., `ankur.sh`):
   ```bash
   vi ankur.sh
   ```
2. Insert system monitoring commands:
   ```bash
   date
   uptime
   whoami
   df -h
   free -h
   ```
3. Save and exit (`:wq`).

---

### 6.2 The "Permission Denied" Error
Attempting to run the script:
```bash
./ankur.sh
# Output: bash: ./ankur.sh: Permission denied
```

- **Root Cause**: In Linux, newly created text files do **not** have executable permissions enabled by default. This is an intentional security design to prevent accidental execution of untrusted files.

---

### 6.3 Linux File Permission Structure (`ls -l`)

Running `ls -l` displays detailed file metadata, such as:
```text
-rw-r--r-- 1 root root 42 Oct 4 10:30 ankur.sh
```

#### Breakdown of the 10-Character Permission String:
- **Position 1 (File Type)**:
  - `-` : Regular file
  - `d` : Directory
  - `l` : Symbolic link
- **Positions 2–4 (Owner / User Permissions)**:
  - Permissions granted to the user who owns the file.
- **Positions 5–7 (Group Permissions)**:
  - Permissions granted to members of the file's assigned group.
- **Positions 8–10 (Others Permissions)**:
  - Permissions granted to everyone else on the system.

---

### 6.4 Permission Modes & Numeric Values

| Permission | Symbol | Binary Value | Numeric Value | Description |
| :--- | :---: | :---: | :---: | :--- |
| **Read** | `r` | `100` | **4** | Allows opening and reading file contents |
| **Write** | `w` | `010` | **2** | Allows modifying, editing, or deleting file contents |
| **Execute** | `x` | `001` | **1** | Allows running the file as a program or script |
| **No Access** | `-` | `000` | **0** | No permissions granted |

#### Common Permission Combinations:
- `7` = `4 + 2 + 1` = `rwx` (Full: Read, Write, Execute)
- `6` = `4 + 2 + 0` = `rw-` (Read and Write)
- `5` = `4 + 0 + 1` = `r-x` (Read and Execute)
- `4` = `4 + 0 + 0` = `r--` (Read-only)

---

### 6.5 Changing Permissions (`chmod`)

#### The Danger of `chmod 777`
- **Why `chmod 777` is strictly forbidden in Production**:
  - `777` (`rwxrwxrwx`) grants full read, write, and execute permissions to the Owner, the Group, and **all unprivileged/external users**.
  - Any user or compromised process can read sensitive data, overwrite code, or delete critical files.

#### Recommended Practice:
Grant only the specific execution permission needed:

```bash
# Method 1: Symbolic Mode (Add execute permission for all)
chmod +x ankur.sh

# Method 2: Numeric Mode (Owner: rwx, Group: r-x, Others: r-x)
chmod 755 ankur.sh

# Method 3: Add execute permission for User/Owner only
chmod u+x ankur.sh
```

- Once execute permission is applied, the filename highlights in green in standard Linux terminal color schemes.

---

### 6.6 Executing the Script
Run the script using any of the following methods:
```bash
# Direct execution (requires execute permission)
./ankur.sh

# Invoking via shell interpreter
bash ankur.sh
sh ankur.sh
```

---

### 6.7 The Shebang (`#!/bin/bash`)
- **Definition**: The first line of a script starting with `#!` followed by the path to the interpreter.
- **Syntax**:
  ```bash
  #!/bin/bash
  ```
- **Purpose**: Informs the operating system kernel which program interpreter should be used to parse and execute the script commands (e.g., `/bin/bash`, `/usr/bin/python3`, `/bin/sh`).

---

## 7. User, Group & Ownership Hierarchy

Linux implements a multi-user security model based on three tiers of ownership:

1. **Owner (User - `u`)**:
   - The individual account that created or owns the file.
   - Typically possesses the highest level of control over the file.
2. **Group (`g`)**:
   - A defined collection of user accounts (e.g., `developers`, `qa`, `devops`).
   - Group permissions enable multiple team members to share access to common project directories and files without individually sharing passwords or root access.
3. **Others (`o`)**:
   - All other accounts on the system that are neither the file owner nor members of the assigned group.

### Ownership Commands:
- **`chmod` (Change Mode)**: Modifies access permissions (`r`, `w`, `x`).
- **`chown` (Change Owner)**: Changes the user and group ownership of a file or directory.
  ```bash
  chown <username>:<groupname> <file_or_dir>
  ```
- **`chgrp` (Change Group)**: Changes only the group ownership of a file or directory.
  ```bash
  chgrp <groupname> <file_or_dir>
  ```

---

## 8. Top 10 Technical Interview Questions & Answers

### Q1: What is the difference between `/etc` and `/bin` directories in the Linux filesystem hierarchy?
**Answer**:
- **`/etc`**: Houses system-wide and application-specific configuration files, startup scripts, and system parameters (e.g., `/etc/os-release`, `/etc/passwd`, `/etc/nginx/`). It contains editable text configurations, not binary programs.
- **`/bin`**: Contains essential compiled binary executables and core command utilities (e.g., `ls`, `cp`, `cat`, `date`) that must be accessible by all users and available in single-user maintenance mode.

---

### Q2: What is the fundamental difference between Vi/Vim's Command Mode and Insert Mode, and how do you toggle between them?
**Answer**:
- **Command Mode**: The default mode when opening a file in Vi/Vim. It is used for cursor navigation, deleting lines (`dd`), undoing changes (`u`), searching, and issuing file control commands (`:wq`, `:q!`). You cannot type text directly in this mode.
- **Insert Mode**: The mode used for typing and editing document text.
- **Switching**:
  - Command Mode to Insert Mode: Press `i`.
  - Insert Mode back to Command Mode: Press `Esc`.

---

### Q3: What is the purpose of the `chage` command in Linux system administration?
**Answer**:
The `chage` (Change Age) command is used by system administrators to view, set, and manage password aging and account expiration policies. It allows setting parameters such as:
- Minimum number of days between password changes.
- Maximum password validity period (e.g., forcing expiration after 60 or 90 days).
- Inactivity grace periods and user warning days prior to expiration.
- Checking policy status: `chage -l <username>`.

---

### Q4: What is OS patching, and what is the difference between `apt update` and `apt upgrade`?
**Answer**:
- **OS Patching**: The process of applying software and operating system updates to resolve known security vulnerabilities (CVEs), fix software bugs, and improve system performance.
- **`apt update`**: Downloads the latest package lists and metadata from configured repositories. It refreshes the local index but does not upgrade or install any software.
- **`apt upgrade`**: Compares installed package versions against the updated package list and upgrades all installed packages to their newest available versions.

---

### Q5: What is the difference between `systemctl restart <service>` and `systemctl enable <service>`?
**Answer**:
- **`systemctl restart <service>`**: Immediately stops and restarts the running service in the current session. Used to reload configuration changes immediately.
- **`systemctl enable <service>`**: Configures the service to launch automatically during system startup by creating the necessary symbolic links in `/etc/systemd/system/`. It does not immediately start the service if it is currently stopped.

---

### Q6: Explain the numeric calculation of Linux file permissions. What does `chmod 755` mean?
**Answer**:
Linux file permissions use an octal (base-8) numeric representation where:
- **Read (`r`)** = 4
- **Write (`w`)** = 2
- **Execute (`x`)** = 1
- **No Permission (`-`)** = 0

Permissions are applied to three distinct entities: **Owner (User)**, **Group**, and **Others**.
In `chmod 755`:
- **7** (`4 + 2 + 1`): Owner has full **Read, Write, and Execute** (`rwx`).
- **5** (`4 + 0 + 1`): Group has **Read and Execute** (`r-x`).
- **5** (`4 + 0 + 1`): Others have **Read and Execute** (`r-x`).

---

### Q7: Why is running `chmod 777` strictly forbidden in enterprise and production environments?
**Answer**:
`chmod 777` (`rwxrwxrwx`) grants full read, write, and execute permissions to everyone—the Owner, the Group, and **all unprivileged external users**. In production:
- Any user or compromised process can modify, overwrite, or delete critical files.
- Unauthorized users can execute malicious code through the file.
- It fails automated security compliance audits (e.g., CIS Benchmarks, SOC2, ISO 27001).

---

### Q8: What is a Shebang line (`#!/bin/bash`), and why is it critical in shell scripts?
**Answer**:
The Shebang is the character sequence `#!` at the very first line of a script followed by the absolute path to an interpreter binary (e.g., `#!/bin/bash` or `#!/usr/bin/env bash`).
It instructs the operating system kernel's program loader which interpreter binary to invoke to parse and execute the commands contained in the script file.

---

### Q9: Even though the `root` user has supreme administrative privileges, why can't `root` execute a script if the `x` bit is not set?
**Answer**:
In Linux, whether a file can be executed as a program or binary is controlled at the filesystem/kernel level by the execute permission bit (`x`) on the file's inode. While `root` has the authority to read, modify, and change permissions on any file, the Linux kernel enforces execution security and will refuse to execute any file whose execute bit is missing, returning `Permission denied`.

---

### Q10: What is the difference between `chown` and `chgrp` commands?
**Answer**:
- **`chown` (Change Owner)**: Changes user ownership and optionally group ownership simultaneously (e.g., `chown user:group filename`).
- **`chgrp` (Change Group)**: Changes only the group ownership of a file or directory (e.g., `chgrp groupname filename`).

---

## 9. Top 10 Scenario-Based Interview Questions & Answers

### Scenario 1: Deploying a Custom Web Landing Page
**Problem**: You installed Nginx on an Ubuntu VM, and the default "Welcome to nginx!" page is visible. Your manager asks you to replace it with a custom HTML dashboard.
**Solution**:
1. Navigate to the Nginx document root:
   ```bash
   cd /var/www/html
   ```
2. Edit the main HTML file:
   ```bash
   vi index.html
   ```
3. Replace the contents with the custom HTML code, save and exit using `:wq`.
4. Refresh the browser at `http://<VM_IP>`. Because static files are served directly from disk, no service restart is required.

---

### Scenario 2: Web Application Inaccessible After Deployment
**Problem**: You installed and started Nginx on a cloud VM, but navigating to `http://<EXTERNAL_IP>` in a browser results in a connection timeout.
**Troubleshooting Steps**:
1. **Verify service status**: Ensure Nginx is actively running:
   ```bash
   systemctl status nginx
   ```
2. **Verify port listening**: Check if Nginx is listening on port 80:
   ```bash
   ss -tuln | grep 80
   ```
3. **Verify cloud firewall**: Check GCP/AWS Firewall and Security Group rules to confirm inbound traffic on TCP port 80 (HTTP) is allowed from `0.0.0.0/0`.
4. **Confirm public IP**: Ensure you are accessing the correct external IP:
   ```bash
   curl ifconfig.me
   ```

---

### Scenario 3: Shell Script Fails with "Permission Denied"
**Problem**: A junior engineer created a monitoring script `healthcheck.sh`, but running `./healthcheck.sh` fails with `bash: ./healthcheck.sh: Permission denied`.
**Solution**:
1. Check file permissions:
   ```bash
   ls -l healthcheck.sh
   ```
2. Notice the missing execute flag (`-rw-r--r--`).
3. Add execute permission safely without using `777`:
   ```bash
   chmod +x healthcheck.sh
   # Or using numeric mode:
   chmod 755 healthcheck.sh
   ```
4. Execute the script:
   ```bash
   ./healthcheck.sh
   ```

---

### Scenario 4: Performing OS Security Patching in Production
**Problem**: The security team reported critical CVE vulnerabilities in installed packages on production Linux servers. How do you execute the patching safely?
**Solution**:
1. **Pre-requisites**: Schedule a change request (CR) within an approved maintenance window. Take a VM snapshot/backup.
2. **Update repositories**:
   ```bash
   apt update
   ```
3. **Upgrade packages**:
   ```bash
   apt upgrade -y
   ```
4. **Reboot verification**: If the Linux kernel, systemd, or glibc were updated, reboot the server:
   ```bash
   reboot
   ```
5. **Post-patch check**: Verify application service health and endpoint responsiveness:
   ```bash
   systemctl status nginx
   ```

---

### Scenario 5: Service Remains Down After Server Reboot
**Problem**: Following a scheduled host reboot, the Nginx web service failed to start automatically, causing downtime until an engineer manually ran `systemctl start nginx`.
**Solution**:
1. Check if the service was configured to start on boot:
   ```bash
   systemctl is-enabled nginx
   ```
   If it outputs `disabled`, the service will not start automatically on reboot.
2. Enable the service to ensure automatic startup upon reboot:
   ```bash
   systemctl enable nginx
   ```
3. Verify that the symlink has been created in `/etc/systemd/system/`.

---

### Scenario 6: Enforcing Enterprise Password Expiration Compliance
**Problem**: An internal security audit reveals that developer accounts on a shared Linux jump host have passwords that never expire. You need to enforce a 60-day expiration with a 7-day warning.
**Solution**:
1. View current password policy for the user:
   ```bash
   chage -l developer1
   ```
2. Configure a 60-day maximum validity and 7-day warning period:
   ```bash
   chage -M 60 -W 7 developer1
   ```
3. Verify the updated configuration:
   ```bash
   chage -l developer1
   ```

---

### Scenario 7: Discarding Accidental Changes in a Critical Configuration File
**Problem**: While inspecting `/etc/nginx/nginx.conf` in Vi/Vim, you accidentally deleted lines and typed random characters. You must exit immediately without saving any of your mistakes.
**Solution**:
1. Press `Esc` to ensure you are in **Command Mode**.
2. Type `:q!` and press `Enter`.
3. This force-quits Vi/Vim and completely discards all unsaved modifications, leaving the original configuration intact.

---

### Scenario 8: Configuring Group Collaboration Permissions
**Problem**: Five developers in the `devteam` group need full read, write, and execute permissions on `/opt/app/scripts/`, while all other users must have read and execute access only.
**Solution**:
1. Set the user owner to `root` and group owner to `devteam`:
   ```bash
   chown -R root:devteam /opt/app/scripts/
   ```
2. Apply permissions using numeric notation (`775`):
   ```bash
   chmod -R 775 /opt/app/scripts/
   ```
   - Owner (`root`): `rwx` (7)
   - Group (`devteam`): `rwx` (7)
   - Others: `r-x` (5)

---

### Scenario 9: Cross-Platform Shell Script Portability in CI/CD
**Problem**: A shell script runs successfully on an Ubuntu CI agent but fails on a different distribution where `bash` is located at `/usr/bin/bash` instead of `/bin/bash`.
**Solution**:
Update the Shebang at line 1 of the script to use the `env` binary, which dynamically searches the system's `PATH` for the bash executable:
```bash
#!/usr/bin/env bash
```
This ensures the script runs portably across Debian, Ubuntu, RHEL, CentOS, Alpine, and macOS runners.

---

### Scenario 10: Applying Service Configuration Changes in Docker / DevOps Services
**Problem**: You updated the Docker daemon configuration file `/etc/docker/daemon.json` to configure a private container registry. How do you apply the changes, and what if the service fails to start?
**Solution**:
1. Restart the Docker service to load the updated configuration:
   ```bash
   systemctl restart docker
   ```
2. Inspect the service status immediately:
   ```bash
   systemctl status docker
   ```
3. If it failed to start, inspect recent error logs using `journalctl`:
   ```bash
   journalctl -u docker.service -e --no-pager
   ```
   Correct any JSON syntax errors in `/etc/docker/daemon.json` and restart again.
