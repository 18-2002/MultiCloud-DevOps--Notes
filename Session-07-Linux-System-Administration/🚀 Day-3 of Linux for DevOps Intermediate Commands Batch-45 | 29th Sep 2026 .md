## Key Outcomes

Day 3 of the Linux training session covered approximately 35–50 intermediate to advanced Linux commands focused on real-time system administration tasks, including process monitoring, file compression, user and group management, and permission handling.  The session also included a live demonstration of Google Cloud Platform (GCP) project creation and IAM-based access management, using a student's (Ravindra's) actual GCP account to illustrate project-level access grants.  Students practiced commands hands-on in their own Linux VMs, with Vikas emphasizing that understanding concepts and practicing commands is more valuable than memorization. 

---

## GCP Project Setup and IAM Access Management

- **Project creation walkthrough:** Students were directed to navigate to `console.cloud.google.com` (bookmarked as an interview-relevant URL) and create a new GCP project from scratch by clicking the project selector → **New Project** → entering a unique project name. 
- **Project ID uniqueness:** Demonstrated live when Ravindra's chosen project name ("Lakshya Project") was already taken; resolved by appending a unique numeric suffix to the project ID. 
- **Project switching:** Covered how to switch between dev, QA, and production projects using the project selector — flagged as a common interview question. 
- **IAM for project-level access:** Vikas demonstrated granting himself **Owner-level** access to Ravindra's GCP project via IAM → Grant Access → entering Vikas's Gmail ID → selecting the **Basic → Owner** role → saving. 
    - Distinction made between **VM-level access** (covered Day 2) and **project-level access** (covered today). 
    - After the access grant, Vikas received an invitation link, accepted it, and was able to SSH directly into Ravindra's running VM without needing to configure keys manually — because Owner access auto-transfers credentials. 
- **Access tiers previewed:** Full access, small access, mid-level access, and viewer access were mentioned as topics to be covered in upcoming classes. 
- **Billing note:** Students were reminded that stopping a VM does not stop billing — only deleting the VM stops charges, analogous to checking out of a hotel rather than leaving your room reserved. 
- **VM deletion best practice:** Students were instructed to delete their VMs at the end of each session and recreate them the next day; automation for this (auto-delete) was promised for the next class. 

---

## System Monitoring Commands

A core block of the session covered commands used daily by system admins, L2/L3 teams, and operations engineers:

- **`uname -a`****:** Returns machine-related information (OS, kernel version, architecture). 
- **`sudo -i`****:** Elevates to root user; demonstrated live after SSHing into Ravindra's VM. 
- **`top`** **/** **`htop`****:** Task Manager equivalent — displays real-time CPU, memory, load average, running/sleeping/stopped processes, and per-user resource consumption. 
    - **Load average** interpreted as CPU/memory load from the current user context; root user shown as the primary resource consumer in the demo. 
    - **Zombie process** defined: a child process whose parent has exited but the child remains unconsumed — must be manually killed in worst cases.  Example given: a Zoom meeting where the host leaves but the child process (the open meeting window) lingers. 
    - `top` and `htop` produce nearly identical output; `htop` is visually cleaner but functionally the same — no meaningful use-case difference. 
- **`uptime`****:** Shows how long the server has been running since last restart — used to verify whether a server was actually restarted as instructed (real-time use case: confirming a junior engineer's claimed restart). 
- **`ps`****:** Lists running processes. 
- **`free -h`** **/** **`free -m`****:** Checks available and used memory (RAM and swap). 
    - **Swap memory** explained: disk space used as virtual RAM when physical RAM is exhausted; equivalent to Windows' page file. 
- **`df -h`****:** Checks disk space usage per filesystem. 
- **`last`****:** Displays login history — used to audit which users logged into the machine, from which IPs, and when. Demonstrated live showing Ravindra had logged in 7 times from different IPs, and Vikas's own entry appeared after gaining access. 
- **`who`** **/** **`w`****:** Shows currently logged-in users; `w` provides additional info including IP and timing. Both produce similar output — `w` has slightly more detail. 
- **`wget`****:** Downloads files/resources from the internet to the Linux machine. 
- **`ping`****:** Tests network connectivity and response time from the machine to an external host (e.g., `ping www.google.com`); checks whether the machine has internet access. 
    - Some students encountered "command not found" errors — resolved by installing the `iputils` package using the APT package manager. 
- **`history`****:** Displays all previously executed commands in the session — shown at end of class to recap the day's commands. 
- **`pwd`****:** Prints present working directory. 

---

## File Search Commands: `locate` and `find`

- **`locate *.log`****:** Searches the system database for all files matching a pattern (e.g., all `.log` files). Requires the **plocate** package to be installed. 
    - **Best practice before installing any software:** Run `sudo apt-get update` first to refresh package lists, then install. This is the correct interview answer — not just running `apt install` directly. 
    - Installation command: `sudo apt-get update` → `sudo apt install plocate`. 
    - **Quoting issue:** `locate *.log` without quotes may fail because the shell interprets `*` before passing to `locate`; wrapping in double quotes (`locate "*.log"`) resolves this. 
- **`find`****:** Alternative to `locate` for searching files; works without a pre-built database. Students were given a task to use `find` to locate all `.log` files and share screenshots. 
- **Key learning from troubleshooting:** When a command fails, don't panic — diagnose whether it's a command error or an environment issue (missing utility/package). Use intelligence and prior experience to resolve. 

---

## File Compression: `tar` Command

- **`tar`** **(Tape Archive):** Linux equivalent of ZIP compression — used to compress multiple files into a single archive and decompress archives. 
- **Why compress:** To bundle multiple files into one, reduce size, and transfer between environments. 
- **Create archive:** `tar -cvf all_files.tar *` inside a directory — **C**reate, **V**erbose (shows files being added), **F**ile (specify archive name). 
    - Verbose flag (`-v`) displays logs on screen during compression; omitting it runs silently. 
- **Extract archive:** `tar -xvf all_files.tar` — **X** means extract. 
- **Live demo:** Created a folder `my_files` with three text files (`a.txt`, `b.txt`, `c.txt`), compressed them into `all_files.tar`, then extracted — confirming the same structure was restored. 
- **Pipeline usage:** Combining `mkdir` and file creation commands via pipe (`|`) was briefly discussed — output of one command becomes input of the next. 

---

## User and Group Management

This section was the most detailed, covering real-time system admin workflows:

### User Creation and Deletion

- **`adduser [username]`****:** Creates a new user with home directory, password, full name, room number, and phone prompts. 
    - Demo: Created user `kishore` with password `12345`, full name `Kishore Jawade`. 
- **Verify user creation:** `su kishore` (switch user) to confirm login works, then `exit` to return to root. 
- **`passwd [username]`****:** Changes a user's password; demonstrated resetting Kishore's password to `admin` when the original was "forgotten." 
- **`userdel [username]`****:** Deletes a user. After deletion, `su kishore` confirms the user no longer exists. 
    - **Home directory note:** Deleting a user via `userdel` does **not** automatically delete the home directory; the directory persists. If a new user is created with the same name, a warning about the existing home directory appears. Hard delete vs. soft delete concept applies. 
    - **Production caution:** Deletions in production are irreversible — access to delete commands should be very limited. 

### Group Management

- **`addgroup [groupname]`****:** Creates a new group. Demo: created group `DevOps45` representing Batch 45. 
- **`getent group`****:** Lists all groups and verifies a group exists. 
- **Adding a user to a group:** `usermod -aG DevOps45 omprakash` — adds user `omprakash` to group `DevOps45` without removing them from existing groups. 
- **`delgroup [groupname]`****:** Deletes a group entirely. 
- **`chage`****:** Sets password expiry and account expiry for a user — critical for contract employees who should only have access for a defined period (e.g., 6 months). 
    - Real-time use case: contract employee gets 6-month access; `chage` automates disabling after expiry. 

### Key System Files

- **`/etc/passwd`****:** Stores all user account information (username, UID, GID, home directory, shell). Every created user appears here as an entry. 
    - Read with: `cat /etc/passwd`
- **`/etc/shadow`****:** Stores encrypted passwords for all users. Encryption is one-way — cannot be reversed or hacked from this file. 
    - Read with: `cat /etc/shadow` (requires root)
- **`/etc/group`****:** Stores group information — readable via `cat /etc/group` or `getent group`. 

### Granting Root-Level Permissions (sudoers)

- **`/etc/sudoers`** **file:** Controls which users can execute root-level commands. Edited using `visudo` (or `vi /etc/sudoers`). 
    - To grant a normal user (`om`) full root-level access: copy the root entry line and replace `root` with `om` — that user gets all root permissions. 
    - To grant a user (`shruti`) only service-level access (e.g., service restart): add a restricted entry specifying only allowed commands. 
    - **Real-world note:** In most companies, you don't need to memorize the exact syntax — your lead/senior will have access and share the file; you read and replicate the pattern. 
    - **Interview answer:** When asked how to give root access to a user — open the sudoers file with `visudo`, add the appropriate entry, save with `:wq`. 

---

## APT Package Manager

- **`apt-get update`****:** Refreshes the local package index from upstream repositories — must be run before installing any software. This is the **best practice** answer for interviews. 
- **`apt install [package]`****:** Installs a package (e.g., `apt install plocate`). Always preceded by `apt-get update`. 
- **`apt-get upgrade`****:** Upgrades installed packages to newer minor versions (e.g., 3.1.1 → 3.1.2). 
- **Update vs. Upgrade distinction:**
    - **Update** = minor version increments within the same major version (e.g., Windows 11.1.003 → 11.1.004). 
    - **Upgrade** = major version jump (e.g., Windows 11 → Windows 12). 
- **Version pinning:** When installing, you can specify a particular version using `apt install package=version` to avoid breaking applications that depend on a specific version. 

---

## Public Key / Private Key Recap (from Day 2 Notes)

- **Public key:** Shareable with anyone; stored on the server/system where you need to be identified; used to encrypt data and verify digital signatures. 
- **Private key:** Must never be shared; stored only on your local/client machine; used to decrypt data and create digital signatures. 
- **Golden rule:** Knowing the public key does not reveal the private key. 
- **SSH flow:** Generate a key pair → public key stored on the remote server → private key stays on your laptop → SSH uses the pair to authenticate without a password. 
- **Security warning:** Never upload private keys to GitHub or share via WhatsApp — constitutes a major security vulnerability. 
- **Algorithm/encryption name** (e.g., RSA, Ed25519) is an interview-relevant detail — know which algorithm was used to generate your key pair. 

---

## Real-Time Interview Q&A Highlights

- **How to create a GCP project from scratch?** Navigate to `console.cloud.google.com` → project selector → New Project → enter unique name → Create. 
- **How to switch between dev/QA/prod projects?** Use the project selector dropdown in GCP console. 
- **How to give project-level access in GCP?** IAM → Grant Access → enter email → assign role → Save. 
- **What is a zombie process?** A child process whose parent has exited but the child has not been cleaned up; it lingers and may need to be manually killed. 
- **What is swap memory?** Disk space allocated to act as virtual RAM when physical RAM is exhausted; equivalent to Windows page file. 
- **Best practice before installing software on Linux?** Always run `apt-get update` first, then install. 
- **How to give a normal user root-level access?** Edit `/etc/sudoers` via `visudo`, copy the root entry, replace `root` with the username. 
- **How to verify server uptime/last restart?** Run `uptime` command. 
- **What does the** **`last`** **command show?** Login history — which users logged in, from which IPs, and at what times. 
- **Linux boot process** was flagged as a topic that came up in a student's interview for a Linux admin role — confirmed as an upcoming class topic. 

---

## Action Items and Next Steps

- **All students:** Delete today's VM after the session; do not leave it running to avoid unnecessary billing. 
- **All students:** Execute all commands from today's GitHub list in their own Linux VMs — copy from the shared list, type manually (not just read), and practice until comfortable. 
- **All students:** Be ready with a running Linux VM **before** class starts from tomorrow onwards — this is a stated thumb rule. 
- **Tomorrow's class topics:**
    - Linux patching (flagged as a first interview question for the next session) 
    - Automated VM deletion in GCP 
    - VI editor tips, tricks, and shortcuts 
    - File ownership (`chmod`, `chown`), SSH key pair management, and pipeline commands 
    - Linux boot process 
- **Prabhat:** Batch 44 portal access issue to be resolved — email confirmed as `prabhat.manan@gmail.com`; screenshot submitted. 
- **Students with APT update version concerns:** Specify version in `apt install` command using `package=version` syntax. 

  

| Command | Description |
|---|---|
| `ssh-keygen` | Generates SSH key pairs for secure authentication. |
| `uname -a` | Displays detailed system and kernel information. |
| `top` | Shows real-time CPU, memory, and running process usage. |
| `htop` | Provides an interactive view of system processes and resources. |
| `uptime` | Displays how long the system has been running and system load. |
| `last` | Shows the history of user login sessions. |
| `df` | Displays disk space usage of mounted filesystems. |
| `df -h` | Displays disk space usage in human-readable format. |
| `pwd` | Shows the current working directory. |
| `du` | Displays disk space used by files and directories. |
| `ping www.google.com` | Tests network connectivity to a remote host. |
| `who` | Shows currently logged-in users. |
| `w` | Displays logged-in users and their current activities. |
| `whoami` | Displays the username of the current user. |
| `ls` | Lists files and directories in the current location. |
| `cd /var/log` | Changes the current directory to `/var/log`. |
| `locate *.log` | Searches for files matching the `.log` pattern using the locate database. |
| `apt-get update` | Updates the local package repository information. |
| `apt install plocate` | Installs the `plocate` package for fast file searching. |
| `locate *.log` | Searches the locate database for `.log` files. |
| `find *.log` | Searches for matching `.log` files from the current path. |
| `locate '*.log'` | Searches the locate database for files ending with `.log`. |
| `mkdir myfiles ; touch myfiles/{a.txt,b.txt,c.txt}` | Creates a directory and three empty text files. |
| `ls myfiles` | Lists the files inside the `myfiles` directory. |
| `tar -cvf allfiles.tar myfiles` | Creates a TAR archive containing the `myfiles` directory. |
| `tar -xvf allfiles.tar` | Extracts files from the TAR archive. |
| `adduser kishore` | Creates a new Linux user named `kishore`. |
| `su kishore` | Switches to the `kishore` user account. |
| `passwd kishore` | Sets or changes the password for `kishore`. |
| `userdel kishore` | Deletes the `kishore` user account. |
| `addgroup devops45` | Creates a new Linux group named `devops45`. |
| `getent group` | Displays groups and their membership information. |
| `adduser om` | Creates a new Linux user named `om`. |
| `usermod -a -G devops45 om` | Adds user `om` to the `devops45` group. |
| `chage` | Manages user password expiry and aging settings. |
| `cd /etc/` | Changes the current directory to `/etc`. |
| `cat passwd` | Displays the contents of the `/etc/passwd` file. |
| `cat shadow` | Displays the contents of the `/etc/shadow` file containing password-related information. |
| `cat group` | Displays Linux group information from `/etc/group`. |
| `vi sudoers` | Opens the sudoers configuration file for editing. |
| `history` | Displays previously executed commands in the shell. |
  

# Linux for DevOps – Day 3
## 20 Interview Questions & Answers + 20 Scenario-Based Questions & Answers

Based on the Day-3 Linux for DevOps session covering system monitoring, file search, tar, users/groups, APT, sudoers, SSH keys, and GCP IAM. fileciteturn0file0L21-L41

---

# Part 1: 20 Linux Interview Questions & Answers

### 1. What is the purpose of `uname -a`?
**Answer:** `uname -a` displays detailed information about the Linux system, including the kernel version, operating system, machine architecture, and related system information.

### 2. What is the difference between `top` and `htop`?
**Answer:** Both monitor processes, CPU, memory, and system load in real time. `htop` provides a more interactive and visually cleaner interface, while `top` is commonly available by default.

### 3. What does the `uptime` command show?
**Answer:** It shows how long the server has been running since its last restart along with system load information.

### 4. What is a zombie process?
**Answer:** A zombie process is a child process whose parent process has exited but whose process entry has not yet been cleaned up.

### 5. What is swap memory in Linux?
**Answer:** Swap is disk space used as virtual memory when available physical RAM becomes insufficient. It is similar to the page file concept in Windows.

### 6. What is the use of `df -h`?
**Answer:** `df -h` displays filesystem disk-space usage in a human-readable format such as GB and MB.

### 7. What is the difference between `who` and `w`?
**Answer:** Both show currently logged-in users. `w` provides additional information such as login time, idle time, and current activity.

### 8. What does the `last` command do?
**Answer:** `last` displays login history, including users who logged in, login times, and the source IP or terminal information where available.

### 9. What is the purpose of `ping`?
**Answer:** `ping` tests network connectivity and response time between the Linux machine and a remote host.

### 10. What is the difference between `locate` and `find`?
**Answer:** `locate` searches a pre-built file database and is generally fast. `find` searches the filesystem directly and does not depend on a pre-built database.

### 11. Why should `apt-get update` normally be run before installing a package?
**Answer:** It refreshes the local package index so the package manager has current information about available packages and versions.

### 12. What is `plocate`?
**Answer:** `plocate` provides fast file searching through a database and can be used with the `locate` command.

### 13. What does `tar -cvf` do?
**Answer:** It creates a TAR archive. `c` means create, `v` means verbose, and `f` specifies the archive filename.

### 14. How do you extract a TAR archive?
**Answer:** Use `tar -xvf archive.tar`. The `x` option means extract.

### 15. How do you create a Linux user?
**Answer:** Use `adduser username`. It creates the user and interactively asks for account details such as password and user information.

### 16. What does `usermod -aG group user` do?
**Answer:** It adds the specified user to an additional group without removing the user from existing supplementary groups.

### 17. What is the purpose of `chage`?
**Answer:** `chage` manages password and account aging settings, such as password expiry and account expiry.

### 18. What is stored in `/etc/passwd`?
**Answer:** `/etc/passwd` contains user account information such as username, UID, GID, home directory, and login shell.

### 19. What is `/etc/shadow` used for?
**Answer:** `/etc/shadow` stores password-related authentication information and requires appropriate privileges to read.

### 20. What is the purpose of `/etc/sudoers`?
**Answer:** `/etc/sudoers` controls which users or groups can execute commands with elevated privileges and which commands they are allowed to run. It should preferably be edited with `visudo`.

---

# Part 2: 20 Scenario-Based Linux Interview Questions & Answers

### 1. Scenario: A server administrator says the server was restarted, but you want to verify it. What will you do?
**Answer:** Run `uptime` and check the system uptime. If the uptime is only a short period, it indicates a recent restart.

### 2. Scenario: CPU usage suddenly becomes very high on a production server. What will you check first?
**Answer:** Run `top` or `htop` to identify processes consuming high CPU and investigate the responsible application or process.

### 3. Scenario: You need to quickly check how much disk space is available on a server.
**Answer:** Run `df -h` to view filesystem usage in a human-readable format.

### 4. Scenario: A server has low available RAM. What Linux command can help you investigate?
**Answer:** Run `free -h` to check used, available, and swap memory. You can also use `top` or `htop` to identify memory-heavy processes.

### 5. Scenario: You need to find all `.log` files on a Linux system, but `locate` is not available.
**Answer:** Use `find` to search the filesystem directly. For example, `find /var/log -name "*.log"`.

### 6. Scenario: `locate "*.log"` is not returning expected results. What could be the issue?
**Answer:** The locate database may not contain the latest files, or the required package may not be installed. First check/install `plocate` and ensure the database is updated according to the system setup.

### 7. Scenario: You need to install `plocate` on an Ubuntu server. What steps will you follow?
**Answer:** First run `sudo apt-get update`, then run `sudo apt install plocate`.

### 8. Scenario: You have three files and need to package them into a single archive for transfer.
**Answer:** Use `tar -cvf all_files.tar <files-or-directory>` to create a TAR archive.

### 9. Scenario: You received `all_files.tar` from another server and need to restore its contents.
**Answer:** Run `tar -xvf all_files.tar`.

### 10. Scenario: A new developer joins the team and needs a Linux account.
**Answer:** Create the account using `adduser username`, then configure the required password and access.

### 11. Scenario: A user forgot their Linux password and an administrator needs to reset it.
**Answer:** Run `passwd username` with appropriate administrative privileges and set a new password.

### 12. Scenario: A developer should be added to the `DevOps45` group without losing their existing group memberships.
**Answer:** Use `usermod -aG DevOps45 username`. The `-a` option appends the group rather than replacing supplementary groups.

### 13. Scenario: You need to verify whether the `DevOps45` group exists.
**Answer:** Run `getent group DevOps45` or inspect the group database with `getent group`.

### 14. Scenario: A contract employee should have access only for a defined period.
**Answer:** Use `chage` to configure password or account expiry according to the organization's access policy.

### 15. Scenario: You deleted a Linux user but their home directory still exists. Is that unexpected?
**Answer:** No. A normal `userdel username` does not necessarily remove the user's home directory. The directory may remain and should be handled according to the organization's retention/deletion policy.

### 16. Scenario: You need to check which users are currently logged into a server.
**Answer:** Use `who` for a basic view or `w` for additional session and activity details.

### 17. Scenario: You need to investigate who logged into a server earlier in the day.
**Answer:** Run `last` to review login history and available source/session information.

### 18. Scenario: A normal user needs controlled administrative access to restart a service.
**Answer:** Configure an appropriate restricted rule in `/etc/sudoers`, preferably using `visudo`, allowing only the required service command rather than unrestricted root access.

### 19. Scenario: You need passwordless SSH authentication between your laptop and a Linux server.
**Answer:** Generate an SSH key pair using `ssh-keygen`, place the public key on the server, and keep the private key securely on the client machine. Never share the private key.

### 20. Scenario: You need to give a teammate access to a GCP project.
**Answer:** Open the GCP project IAM settings, choose **Grant Access**, enter the user's identity, assign the required IAM role, and save. Use the minimum required permissions rather than unnecessary broad access.

---

# Quick Revision Commands

```bash
uname -a
top
htop
uptime
df -h
free -h
last
who
w
whoami
ping www.google.com
pwd
locate "*.log"
find /var/log -name "*.log"
sudo apt-get update
sudo apt install plocate
tar -cvf all_files.tar my_files
tar -xvf all_files.tar
adduser username
passwd username
userdel username
addgroup DevOps45
getent group
usermod -aG DevOps45 username
chage username
cat /etc/passwd
cat /etc/shadow
cat /etc/group
visudo
ssh-keygen
history
```

## Interview Tip

Don't just memorize the commands. Be ready to explain **why you would use the command, what problem it solves, and what you would check next if it fails**. The Day-3 session emphasized hands-on practice and real system-administration troubleshooting. fileciteturn0file0L148-L158

