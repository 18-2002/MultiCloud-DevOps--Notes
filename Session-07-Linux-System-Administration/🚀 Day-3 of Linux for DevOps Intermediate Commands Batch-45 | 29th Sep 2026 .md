# Linux Day 3 - Session Summary

A comprehensive, student-friendly reference guide based on the Day 3 Linux & Cloud Administration session.

---

## 1. Google Cloud Platform (GCP) & Project Access Management

### 1.1 GCP Console & Project Basics
- **Console URL**: `console.cloud.google.com`
- **Project Identifiers**:
  - **Project Name**: Human-readable name assigned to the project.
  - **Project ID**: Globally unique identifier across Google Cloud.
  - **Project Number**: Unique numerical identifier assigned by GCP, used primarily when troubleshooting with GCP support.
- **Resource Billing & Discipline**:
  - Cloud resources (such as Compute Engine VMs) incur charges while provisioned/running.
  - As a best practice during training and lab practice: create resources, perform labs, and stop or delete the VMs afterwards to avoid unintended billing.

### 1.2 Identity and Access Management (IAM)
- **Purpose**: IAM allows controlling project-level permissions for team members in multi-cloud environments (GCP, AWS, Azure).
- **Granting Access via IAM**:
  1. Navigate to **IAM & Admin** > **IAM**.
  2. Click **Grant Access**.
  3. Enter the user's email address under **New principals**.
  4. Assign the appropriate role (e.g., **Basic > Owner** for full project administration or specific restricted roles).
  5. The invited user receives an invitation email and clicks **Accept**.
  6. The invited user can then access the project and connect to VMs via browser-based SSH.

---

## 2. System Administration & Monitoring Commands

### 2.1 Switching to Root & Kernel Information
- **`sudo -i`**: Switches the current shell session to the `root` administrative user environment.
- **`uname -a`**: Prints complete system and kernel details (kernel name, network node hostname, kernel release, architecture, OS).

```bash
# Switch to root user
sudo -i

# Display system and kernel details
uname -a
```

---

### 2.2 System Resource & Process Monitoring

#### `top`
Provides a real-time dynamic view of running system processes, CPU utilization, memory allocation, and load average.
- **Header Information**:
  - **Uptime & Users**: System uptime duration and number of currently logged-in users.
  - **Load Average**: Average system load over 1, 5, and 15 minutes.
  - **Tasks**: Total task count broken down into running, sleeping, stopped, and zombie.
  - **CPU & Memory**: User CPU (`%us`), system CPU (`%sy`), idle CPU (`%id`), total/used/free RAM, and Swap memory.
- **Exit `top`**: Press `q`.

#### `htop`
An interactive, color-coded, user-friendly process viewer and alternative to `top`. Shows visual bar meters for per-core CPU and memory utilization.

```bash
# Run real-time monitoring
top

# Run interactive enhanced monitoring (if installed)
htop
```

---

### 2.3 Key System Concepts: Zombie Processes & Swap Memory

#### Zombie Processes
- **Definition**: A zombie process (defunct process) is a process that has completed its execution (or whose parent terminated/failed to read its exit status), but its process descriptor still remains in the process table.
- **Identification & Resolution**:
  - Identified in the `top` summary line (`zombie` count) or via process list.
  - **Action**: If a defunct or stuck process causes resource issues, locate its Process ID (PID) and terminate it using:
    ```bash
    kill <PID>
    # Force kill if necessary
    kill -9 <PID>
    ```

#### Swap Memory
- **Definition**: Space reserved on a local storage drive (disk partition or swap file) used as virtual memory when physical RAM becomes fully utilized or inactive memory pages need to be temporarily paged out to disk.

---

### 2.4 System Uptime, User Tracking & History

#### `uptime`
Shows how long the system has been running, current time, number of users, and load averages.
- **Real-Time Use Case**: Verifying whether a server rebooted as scheduled during a weekend maintenance window, or checking if an outage was caused by an unexpected machine restart.

```bash
uptime
```

#### `last`
Displays a chronological history of system logins and logouts, including usernames, terminal devices, source IP addresses, and timestamps.
- **Use Case**: Auditing login activity, tracking who accessed the server, and identifying unexpected or external access attempts.

```bash
last
```

#### `who`, `w`, `whoami`
- **`whoami`**: Prints the username of the active effective user.
- **`who`**: Lists currently logged-in users, terminals, login times, and source IPs.
- **`w`**: Provides a detailed summary of currently logged-in users along with what each user is executing, idle time, and system load.

```bash
whoami
who
w
```

---

### 2.5 Disk & Memory Inspection

#### `free`
Displays total, used, and available physical memory (RAM) and swap memory.
- **`free -h`**: Outputs values in human-readable format (MB, GB).

```bash
free -h
```

#### `df` (Disk Filesystem)
Shows storage space usage across all mounted filesystems.
- **`df -h`**: Human-readable disk filesystem utilization (e.g., checking if `/` root partition is reaching capacity).

```bash
df -h
```

#### `du` (Disk Usage)
Estimates and displays file and directory space usage on disk.
- **`df` vs `du`**:
  - `df`: Reports overall filesystem-level storage capacity and free space.
  - `du`: Reports directory and file-level space consumption for specific folders.

```bash
# Check disk usage of the current directory
du -sh .
```

---

### 2.6 Network Verification

#### `ping`
Tests network reachability between the current host and a destination server or IP address over ICMP.
- **Use Case**: Verifying internet connectivity and measuring round-trip packet latency and packet loss.
- **Stop ping**: Press `Ctrl + C`.

```bash
ping google.com
```

*Note*: If the `ping` utility is missing on a minimal or containerized Ubuntu image, install the networking package:
```bash
apt-get update
apt-get install -y iputils-ping
```

#### `wget`
Command-line utility used to download files directly from external URLs/the internet to the server.

```bash
wget <URL>
```

---

## 3. Package Management & File Search

### 3.1 Package Best Practices (`apt`)
- **Golden Rule**: Always update the local package index before installing new packages on Debian/Ubuntu systems:
  ```bash
  apt-get update
  ```
- **Difference between `update` and `upgrade`**:
  - `apt update`: Updates the local database index of available package versions from repositories (does not modify installed software).
  - `apt upgrade`: Upgrades installed packages to their newest available minor or major versions.

---

### 3.2 Finding Files in Linux

#### System Log Directory
- **Path**: `/var/log`
- All key system, service, and application log files reside in this location.

#### `locate`
Searches a pre-built system database for files matching a given pattern.
- Fast file search mechanism.
- If not installed by default on Ubuntu:
  ```bash
  apt-get update
  apt-get install -y plocate
  ```
- Example syntax:
  ```bash
  locate "*.log"
  ```
  *(Quoting the wildcard prevents shell expansion errors before the command executes).*

#### `find`
Performs real-time searches through the live directory tree based on filenames, extensions, or patterns.

```bash
# Search for all .log files in /var/log
find /var/log -name "*.log"

# Search for all .log files in the current directory
find . -name "*.log"
```

---

## 4. Archiving & Compression with `tar`

### 4.1 Concept
- **`tar`** stands for **Tape Archive**.
- Linux equivalent to creating archive/compressed bundles (similar to `.zip` in Windows).
- Used by administrators to bundle multiple files or directories into a single file for backups, deployments, and transfers.

### 4.2 Common Flags
- `-c`: Create a new archive.
- `-x`: Extract files from an existing archive.
- `-v`: Verbose output (lists each file processed in real-time).
- `-f`: Specifies the archive filename to read or write.

### 4.3 Practical Commands

```bash
# 1. Create a directory and sample files
mkdir my_files
touch my_files/a.txt my_files/b.txt my_files/c.txt

# 2. Archive files into a single .tar archive
tar -cvf all_files.tar my_files/

# 3. Extract the contents of the archive
tar -xvf all_files.tar
```

---

## 5. User Access Management (UAM) & Administration

### 5.1 User Management

#### Creating a User
Creates a new system user, prompts for password and user details, and creates a default home directory (`/home/<username>`).

```bash
adduser <username>
# Example:
adduser kishore
```

#### Switching User
Switch from the current user account to another account:

```bash
su - <username>
# Return to previous session
exit
```

#### Changing / Resetting Password
Root users can change or reset passwords for any system user:

```bash
passwd <username>
# Example:
passwd kishore
```

#### Deleting a User
Removes the user account from system account databases:

```bash
userdel <username>
```
*Note*: By default, `userdel` removes the user credentials from `/etc/passwd` and `/etc/shadow`, but retains the user's home directory (`/home/<username>`). Use caution in production environments as deletions cannot be undone.

---

### 5.2 Group Management

#### Creating a Group
Creates a new group identifier on the system:

```bash
addgroup <groupname>
# Example:
addgroup DevOps45
```

#### Adding a User to a Group
Assigns an existing user to an existing group:

```bash
adduser <username> <groupname>
# Example:
adduser om DevOps45
```

#### Verifying Group Membership
Displays group definitions and member lists:

```bash
getent group <groupname>
# Example:
getent group DevOps45
```

---

### 5.3 Temporary / Contract User Expiry (`chage`)
- **`chage`**: Change user password and account aging parameters.
- **Real-Time Use Case**: Setting an automatic account expiration date for contract employees (e.g., 3-month or 6-month contracts) so access is automatically disabled when the contract period ends.

```bash
# View account aging details for a user
chage -l <username>
```

---

### 5.4 Critical Authentication & Identity Configuration Files

All user, group, and credential configurations are stored under the `/etc` directory:

| File Path | Description | Access Permissions |
| :--- | :--- | :--- |
| `/etc/passwd` | Stores user account attributes (Username, Password placeholder `x`, UID, GID, User Info, Home Directory, Default Shell). | Readable by all users. |
| `/etc/shadow` | Stores encrypted password hashes, password change dates, expiration policies. | Restricted strictly to root/admin. |
| `/etc/group` | Stores group definitions, Group IDs (GID), and list of members belonging to each group. | Readable by all users. |
| `/etc/sudoers` | Configures sudo privileges and administrative command authorizations for users and groups. | Managed carefully via root. |

To inspect entries:
```bash
# View user entries
cat /etc/passwd

# View encrypted credentials (as root)
cat /etc/shadow

# View group definitions
cat /etc/group
```

---

### 5.5 Sudoers File & Granting Privileges (`/etc/sudoers`)
- **Purpose**: Defines which users or groups can execute commands with superuser (`root`) privileges using `sudo`.
- **Granting Full Sudo Access**:
  Adding a user entry under root privileges:
  ```text
  <username> ALL=(ALL:ALL) ALL
  ```
  *Example*: `om ALL=(ALL:ALL) ALL` allows user `om` to execute any administrative command via `sudo`.

- **Granting Restricted Service Access**:
  Allowing a user to perform only specific administrative tasks (such as restarting specific services) without requiring a password:
  ```text
  shruti ALL=(ALL) NOPASSWD: /usr/sbin/service, /bin/systemctl
  ```

---

## 6. Review & Real-World Best Practices

1. **Production Safety**:
   - Deleting files or users in Linux CLI is permanent. There is no Recycle Bin mechanism in server environments.
2. **Key Security (SSH)**:
   - **Public Key**: Placed on the target server (e.g., `~/.ssh/authorized_keys`). Can be shared openly.
   - **Private Key**: Kept strictly secret on your local client machine. Never share private keys, commit them to GitHub, or store them in messaging channels.
3. **Troubleshooting Commands**:
   - If a command is reported as "not found", verify syntax, check if running as root/sudo, or install the required package/utility (`apt-get update && apt-get install <package>`).
