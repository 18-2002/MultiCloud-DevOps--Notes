## Key Outcomes

Day 5 of the Linux training series covered four major topic areas: file permissions and the `chmod` command, file ownership and the `chown` command, package managers (`apt` and `yum`) with a live Jenkins installation demo, and the Linux boot process (`BIOS → Bootloader → Kernel → Init → Login`). Students practiced hands-on on Google Cloud Platform (GCP) virtual machines. The session concluded with a Q&A covering HTTP status codes, troubleshooting website slowness, hardware inspection commands, sticky bits, and SSH connectivity issues.

---

## Session Setup & Environment

- Session is **Day 5 of the Linux module** in the CloudDevOpsHub training program. 
- All students instructed to create a fresh **GCP VM** at `console.cloud.google.com` using:
    - **OS:** Ubuntu 24.04 LTS
    - **Disk:** 10 GB
    - **Network:** HTTP and HTTPS traffic enabled (ports 80/443 allowed) 
- Instructor emphasized that enabling HTTP/HTTPS checkboxes during VM creation directly allows internet access on port 80 — a point raised because some students faced connectivity issues the previous day. 
- Students logged in via **SSH through the browser window** (GCP console → VM instance → "Open in browser window"), which triggers key transfer and authenticates on port 22. 
- Working directory for the session: `/tmp/day5` 

---

## File Permissions

### Checking Permissions

- Command to **check** file permissions: `ls -l` (not `chmod`, which is for changing). 
- `ls -l` provides detailed output including permission bits, owner, group, size, and timestamp. 
- `ls` alone lists files; `ls -l` gives "wider details" and reports `total 0` for an empty directory. 

### Understanding Permission Output

- First character of `ls -l` output indicates file type: 
    - **`-`** = regular file
    - **`d`** = directory (folder)
    - **`l`** = symbolic link; other types include pipe, socket (relevant for networking) 
- Permission string is divided into three groups: **owner**, **group**, **others (public)**. 
- Default directory permissions include extra bits compared to files — interviewers may ask about default permissions. 

### Changing Permissions with `chmod`

- **`chmod`** = "change mode"; used to modify read (`r`=4), write (`w`=2), execute (`x`=1) permissions. 
- **Symbolic method:** `chmod +x <filename>` grants executable permission to **everyone** (owner, group, others). 
    - Use case: L3/DevOps engineers may need to grant execute-only access to L1 staff who need to run scripts but must not modify them. 
- **Numeric method:** permissions calculated by summing values per group. 
    - Example given: owner=write(2), group=write(2), others=read(4) → `chmod 224 <file>` 
    - `chmod 444` = read-only for all; `chmod 222` = write-only for all 
- Live demo: created file `asasi` and folder `123/folder` in `/tmp/day5`, then demonstrated `chmod +x` and observed the output change in `ls -l`. 
- Interactive exercise: student Sandeep specified permissions (owner=write, group=write, others=read), and instructor derived the numeric value live. 

### Sticky Bit (Q&A Addition)

- **Sticky bit** restricts deletion: when set on a directory, only the **file owner** can delete or modify their own files, even if others have write access. 
- Applied with `chmod +t <directory>`; removed with `chmod -t <directory>`. 
- Interview-relevant concept; discussed during Q&A at student Mithun's request. 

---

## File Ownership

### Concept

- **Ownership** = whoever created the file is the owner; files created as `root` have `root` as owner. 
- Ownership is analogous to a property registry — the name on the registry is the owner. 

### Changing Ownership with `chown`

- **`chown`** = "change owner"; syntax: `chown <new_owner>:<group> <filename>` 
- Live demo steps: 
    1. Created a new user: `adduser vinod` (password: `12345`)
    2. Created file `file2` as root in `/tmp/day5`
    3. Ran `chown vinod:root file2` (or `chown vinod <file>`)
    4. Verified with `ls -l` — owner column changed from `root` to `vinod`
- If no output appears after running `chown`, it means the command succeeded. 
- **Key interview point:** to change file ownership → use `chown`; to change permissions → use `chmod`. 
- Ownership change does **not** rename the file; only the owner metadata changes. 
- Students asked to independently: create a user, create a file, and change ownership — then share screenshots. 

---

## SSH Key Management (Recap & Context)

- Public/private key pairs are generated using a key-generation command; two key types are produced simultaneously. 
- **Public key:** can and should be shared; placed on the remote server. 
- **Private key:** never shared; stays on the local machine; functions as a password. 
- Encryption/decryption: public key encrypts, private key decrypts; both must match for SSH authentication. 
- Keys are generated using cryptographic algorithms (e.g., **SHA-256**); algorithm choice affects security level. 
- AWS-specific SSH key behavior noted as slightly different from GCP — to be covered in the AWS module. 
- Interview Q raised: if unable to connect via SSH, possible reasons include SSH service being down, firewall/port 22 blocked, incorrect key, or no network access. 

---

## Package Management

### Overview

- **Package managers** handle installation, removal, update, and search of software packages on Linux. 
- Two primary Linux package managers: 
    - **`yum`** (Yellowdog Updater Modified / "Yellow Dog") — used on **Red Hat family** (CentOS, Fedora, AWS Linux)
    - **`apt`** (Advanced Package Tool) — used on **Debian/Ubuntu family**
- Current training uses Ubuntu → `apt` is the relevant package manager. 
- Package manager comparison across OSes: 
    - **Mac OS:** Homebrew (`brew`)
    - **Windows:** Chocolatey (also MSI installer)
    - **Linux RPM-based:** `yum` / `dnf`

### Using `apt`

- Best practice workflow: 
    1. `sudo apt-get update` — refresh package index (always run first; mention in interviews)
    2. `sudo apt install <package>` — install software
    3. `sudo apt remove <package>` — remove software
    4. Upgrade a specific package with the upgrade command
- Configuration file location: `/etc/apt/apt.config` 
- **Source list** (`/etc/apt/sources.list`): tells `apt` where to download packages from. 

### Jenkins Installation Demo (Live)

- **Problem demonstrated:** running `sudo apt install jenkins` fails because Ubuntu does not know what Jenkins is by default — package not in default sources. 
- **Solution:** manually add Jenkins repository info to the source list. 
    - Steps from Jenkins official documentation:
        1. Install Java dependency first: `sudo apt install openjdk-21-jre` (Java Runtime Environment required before Jenkins) 
        2. Add Jenkins GPG key and repository URL to sources list (three commands copied from Jenkins website) 
        3. Run `sudo apt-get update` to refresh
        4. Run `sudo apt install jenkins` — now succeeds because the source is known 
- **Interview answer:** "If a package is not available directly, manually add the source repository URL to the source list, then update and install." 
- Post-install verification: accessed Jenkins on the browser to confirm it was running. 
- Dependency concept: Jenkins requires Java; Java must be installed first — this is a **dependency**. 

---

## Linux Boot Process

### Process Overview (Interview-Critical)

Ordered sequence from power-on to login screen: 

1. **Power On**
2. **BIOS** (Basic Input/Output System) — detects all hardware (RAM, CPU, storage, peripherals); instructor's laptop BIOS took ~8.4 seconds 
3. **MBR / GPT** — Master Boot Record or GUID Partition Table; hardware configuration loaded
4. **Bootloader (GRUB)** — Grand Unified Bootloader; loads kernel configuration from boot device 
5. **Kernel** — heart of the OS; starts the operating system; reads hardware soft/hard limits 
6. **Init** — initialization of software applications and startup scripts; run-level scripts execute 
7. **Login Screen / User Interface** — user-related services and wallpaper load 

### Run Levels (Raised by Student Shankar)

- Linux has **7 run levels** (0–6): 
    - **`init 0`** — shutdown and power off
    - **`init 1`** — single-user mode (maintenance/admin tasks only)
    - **`init 6`** — reboot
- Running `init 0` on GCP was noted as equivalent to stopping the VM from the cloud console. 

### Inspecting Boot & Hardware

- **`dmesg`** — displays all kernel ring buffer messages from boot; shows every process executed during startup. 
- **`dmesg | wc -l`** — pipes output to word count; instructor's GCP machine showed **585 lines** (585 boot processes); varies per machine depending on installed software. 
- **`lsblk`** — lists block devices (storage blocks/partitions). 
    - Interview distinction: **block** = raw storage unit; **partition** = logical division of a block/disk (e.g., C: and D: drives in Windows) 
- **`cat /proc/cpuinfo`** — CPU hardware information 
- **`free -h`** — memory information 
- **`df -h`** — disk space information 
- **`lshw`** — lists all hardware on the machine 
- Windows equivalent: **Device Manager** or `msinfo32` (System Information) 
- Cloud engineers don't run these commands often in real-time but must know them for interviews. 

---

## HTTP Status Codes & Website Troubleshooting

### Status Code Categories

Four categories, each with a distinct meaning: 

|  Range  |      Meaning      |                       Notes                       |
|---------|-------------------|---------------------------------------------------|
| **1xx** | Informational     | No problem; just information                      |
| **2xx** | Success           | 200 OK, 204 No Content, 206 Partial Content       |
| **3xx** | Redirection       | 301 Permanent, 302 Temporary redirect             |
| **4xx** | Client-side error | 400 Bad Request, 404 Not Found, 403 Forbidden     |
| **5xx** | Server-side error | Service down, gateway timeout, no internet access |

### Key Points

- **302 Redirection** is not an error — it is intentional and beneficial; e.g., clicking a YouTube video embedded on the CloudDevOpsHub website redirects to YouTube, reducing storage and traffic load on the host site. 
- **404 live demo:** instructor changed a GitHub repo from public to private; students received 404 (or 403) when trying to access it — confirming "not found / not authorized." 
- **500 errors** indicate server-side issues: service down, gateway down, longer response times, or no internet. 
- **403** = no write access / not authorized to the project (demonstrated in GCP context). 

### Troubleshooting Website Slowness

- Use **Chrome DevTools** (`Ctrl+Shift+I`) → Network tab → refresh the page to observe all requests. 
- Instructor's site took **1.04 seconds** to load; individual events took up to **681 milliseconds**. 
- Items taking **>300ms** are candidates for optimization — the developer (frontend/backend) is responsible for fixing these. 
- For a broken government/high-traffic site (e.g., IRCTC), many requests show as `fail` — HTML, JavaScript, popup agents all failing — causing overall slowness. 
- DevOps/engineer role: **identify and report** the problem (status codes, slow events); **developer** fixes it. 

---

## Hands-On Practice & GitHub Repository

### Daily Tasks & Interview Prep Structure

- Course GitHub repository at `clouddevopsup.com` → Roadmap → Daily Slavers (daily tasks). 
- Each session file contains keywords, task lists, and **20 interview questions + 20 scenario questions** per day. 
- **Challenge goal:** complete 100 tasks, 40 practicals, 10 projects over the course duration. 
- Students can post LinkedIn updates for each completed task (e.g., "Session 5 completed — GCP account created, screenshot attached"). 
- Repository valid for **1,000 days**; students encouraged not to be fully dependent on it but to self-practice. 

### LinkedIn & Badge System

- Students with **5+ LinkedIn posts** on a module topic qualify for a **module expert badge**. 
- Badge assignment is AI-checked weekly (sync happens once per week); posting and immediately expecting a badge will not work — wait up to one week after posting. 
- Tags must be correct; emojis alone are not sufficient for AI quality checks. 
- Instructor reviewed student Gopal's LinkedIn post live and confirmed it had been submitted. 

### Interview Preparation Advice

- Know **5 commands from each category**: networking, monitoring, day-to-day, troubleshooting, hardware. 
- Rejection in interviews is helpful — it signals what to study next. 
- For experienced candidates: align answers to resume; if the interviewer asks end-to-end access flow, walk through IAM → compute → network → authentication (Active Directory / key-based). 
- Mock interview recordings are not shared publicly due to privacy (breakout room format); students can observe from the main room. 

---

## Action Items & Homework

- **All students:** Run `dmesg | wc -l` on your GCP machine and note the number of boot processes. 
- **All students:** Execute the three Jenkins installation commands from the Jenkins documentation on your own Ubuntu VM. 
- **All students:** Create a user, create a file, change ownership using `chown` — confirm with `ls -l` screenshot. 
- **All students:** Explore the source list file (`/etc/apt/sources.list`) after adding the Jenkins repo to see the entry. 
- **All students:** Read the Day 5 session file in the GitHub repo for keywords and interview questions. 
- **Students seeking LinkedIn badge:** Post 5+ quality posts on the Linux module and wait one week for AI sync. 
- **Tomorrow's session:** Linux troubleshooting — real-time issue scenarios; described as "more important" than today's session. 

---

## Open Questions & Pending Items

- **Increasing SSH session timeout:** Student asked if idle timeout (15 minutes → auto-logout) can be extended; instructor confirmed it disconnects after 15 minutes of inactivity but did not provide a configuration fix during the session. 
- **Root volume resize:** Question about how to increase/decrease root volume size — deferred to the AWS class (EBS/EFS topic). 
- **Removing a package with all dependencies and binaries:** Discussed briefly; `apt remove -r <package>` and navigating to `/bin` for binary removal were suggested but not fully resolved. 
- **GCP VM creation zone selection:** Student Shubham asked about zone selection (US Central A/B/C/D/E) and whether machines in different zones can communicate — noted as requiring scripting/programming knowledge; not fully addressed. 
- **Audio/video sync delay in recordings:** Multiple students reported 1–4 second delay between audio and video in session recordings via the LMS (Graphy platform); instructor suggested raising a support ticket with Graphy and offered to also provide a direct Zoom link as an alternative. 
- **Firewall not showing HTTP enabled:** Student Saeed reported HTTP firewall rule not appearing despite selecting it during VM creation; instructor acknowledged but did not resolve in session. 
- **Billing/403 error on GCP:** Student Zubair encountered a 403 "write access to this project" error and a billing-not-enabled error; instructor identified three issues: project-level permission missing, billing not enabled, and usage API not enabled — advised enabling billing (credit/debit card required).

# Linux Day-5 — 40 Interview Questions & Answers

Based on the Day-5 Linux training session covering permissions, ownership, package management, Jenkins installation, boot process, hardware inspection, HTTP status codes, and troubleshooting. fileciteturn0file0L20-L45

## Part 1 — 20 Interview Questions & Answers

### 1. What is the purpose of `ls -l`?
**Answer:** `ls -l` displays detailed file information such as file type, permissions, owner, group, size, and timestamp.

### 2. What does the first character in `ls -l` output represent?
**Answer:** It represents the file type. `-` means regular file, `d` means directory, and `l` means symbolic link.

### 3. What are the three permission groups in Linux?
**Answer:** The three groups are **owner**, **group**, and **others**.

### 4. What is `chmod`?
**Answer:** `chmod` means change mode. It is used to change read, write, and execute permissions on files and directories.

### 5. What do `r`, `w`, and `x` represent?
**Answer:** `r` means read, `w` means write, and `x` means execute. Their numeric values are 4, 2, and 1 respectively.

### 6. What does `chmod 444 file.txt` do?
**Answer:** It gives read-only permission to the owner, group, and others.

### 7. What does `chmod 222 file.txt` do?
**Answer:** It gives write-only permission to the owner, group, and others.

### 8. What does `chmod +x script.sh` do?
**Answer:** It adds execute permission to the file for owner, group, and others.

### 9. What is the sticky bit?
**Answer:** The sticky bit restricts deletion in a directory so that users can delete or modify their own files even when others have write access to the directory. It can be set using `chmod +t <directory>`.

### 10. What is `chown` used for?
**Answer:** `chown` is used to change file or directory ownership.

### 11. What is the difference between `chmod` and `chown`?
**Answer:** `chmod` changes permissions, while `chown` changes ownership.

### 12. What is the syntax for changing owner and group?
**Answer:** A common syntax is `chown <new_owner>:<group> <filename>`.

### 13. What are Linux package managers?
**Answer:** Package managers install, remove, update, and search software packages. The session covered `apt` for Debian/Ubuntu systems and `yum` for Red Hat-family systems.

### 14. What is the difference between `apt` and `yum`?
**Answer:** `apt` is commonly used on Debian/Ubuntu systems, while `yum` is used with Red Hat-family and RPM-based systems such as CentOS and AWS Linux.

### 15. Why do we run `sudo apt-get update` before installing packages?
**Answer:** It refreshes the package index so the system knows about the latest available packages from configured repositories.

### 16. What is `/etc/apt/sources.list`?
**Answer:** It contains repository information that tells `apt` where it can download packages.

### 17. Why did `sudo apt install jenkins` initially fail in the session?
**Answer:** Jenkins was not available in the default configured Ubuntu package sources. The Jenkins repository had to be added, followed by `apt-get update`.

### 18. Why is Java installed before Jenkins?
**Answer:** Jenkins requires Java to run. Java is therefore a dependency for Jenkins.

### 19. What is the Linux boot process?
**Answer:** The session described the sequence as Power On → BIOS → MBR/GPT → Bootloader/GRUB → Kernel → Init → Login/User Interface.

### 20. What is the purpose of `dmesg`?
**Answer:** `dmesg` displays kernel ring-buffer messages, including messages generated during system boot and hardware/kernel initialization.

---

## Part 2 — 20 Scenario-Based Questions & Answers

### 1. Scenario: A script gives "Permission denied" when you try to execute it. What will you check?
**Answer:** First check the permissions using `ls -l script.sh`. If execute permission is missing, use `chmod +x script.sh` or apply the required numeric permissions.

### 2. Scenario: A developer should only be able to read a file. What permission can you use?
**Answer:** If everyone should have read-only access, `chmod 444 file.txt` can be used. If only a specific owner/group/others combination is required, calculate the permission value accordingly.

### 3. Scenario: You need owner=write, group=write, and others=read. What chmod value will you use?
**Answer:** Write is 2 and read is 4, so the permission is `224`. Use `chmod 224 <file>`.

### 4. Scenario: Multiple users have write access to a shared directory, but users should not delete each other's files. What will you use?
**Answer:** Set the sticky bit on the directory using `chmod +t <directory>`.

### 5. Scenario: A file was created by root but must now belong to user `vinod`. What will you do?
**Answer:** Use `chown`, for example `chown vinod file2`, and verify the result with `ls -l`.

### 6. Scenario: You changed the owner of a file, but its filename did not change. Is that expected?
**Answer:** Yes. `chown` changes ownership metadata; it does not rename the file.

### 7. Scenario: Jenkins is not found when installing it with `apt`. What is your troubleshooting approach?
**Answer:** Check whether the Jenkins repository is configured. Add the Jenkins repository and its GPG key according to the official Jenkins installation instructions, run `sudo apt-get update`, and then install Jenkins.

### 8. Scenario: Jenkins installation fails because Java is missing. What should you do?
**Answer:** Install the required Java runtime first, such as `sudo apt install openjdk-21-jre`, then continue with Jenkins installation.

### 9. Scenario: You added a new repository but `apt` still cannot find the package. What should you check?
**Answer:** Run `sudo apt-get update` after adding the repository and verify that the repository entry is correctly configured.

### 10. Scenario: An Ubuntu server is starting and you want to understand what happened during boot. Which command can help?
**Answer:** Use `dmesg` to inspect kernel and boot-related messages.

### 11. Scenario: You want to count the number of lines in the kernel boot messages. What command can you use?
**Answer:** Use `dmesg | wc -l`. The exact number varies between machines.

### 12. Scenario: You want to inspect storage devices and partitions on a Linux VM. Which command will you use?
**Answer:** Use `lsblk` to list block devices and their partition structure.

### 13. Scenario: You need to check CPU information from Linux. Which command was covered?
**Answer:** Use `cat /proc/cpuinfo`.

### 14. Scenario: A server is running out of memory. Which command can quickly show memory usage?
**Answer:** Use `free -h` for human-readable memory information.

### 15. Scenario: You suspect a disk-space problem on a Linux server. Which command will you use?
**Answer:** Use `df -h` to check filesystem disk usage in a human-readable format.

### 16. Scenario: A website returns HTTP 404. What does it generally indicate?
**Answer:** A 404 indicates that the requested resource was not found. The session demonstrated this with a GitHub repository whose access/state had changed.

### 17. Scenario: A website returns HTTP 403. What does it indicate?
**Answer:** It indicates that access is forbidden or the user is not authorized to access the requested resource.

### 18. Scenario: A website returns HTTP 500. Where would you investigate first?
**Answer:** Investigate the server-side application/service, gateway, or backend because 5xx responses indicate server-side problems.

### 19. Scenario: A website is loading slowly. How can a DevOps engineer identify which requests are slow?
**Answer:** Open Chrome DevTools using `Ctrl+Shift+I`, go to the Network tab, refresh the page, and inspect request timings and failed requests. The session emphasized identifying/reporting the issue while the relevant developer fixes application-level problems.

### 20. Scenario: You cannot connect to a Linux VM through SSH. What common causes should you check?
**Answer:** Check whether the SSH service is running, port 22 is allowed through the firewall/security rules, the correct key is being used, and the VM has network connectivity. fileciteturn0file0L78-L85

---

## Quick Revision — Day-5 Commands

```bash
ls -l
chmod +x script.sh
chmod 444 file.txt
chmod 222 file.txt
chmod 224 file.txt
chmod +t /shared
chmod -t /shared

chown vinod file2
chown vinod:root file2

sudo apt-get update
sudo apt install <package>
sudo apt remove <package>

dmesg
dmesg | wc -l
lsblk
cat /proc/cpuinfo
free -h
df -h
lshw
```

## Day-5 Interview Focus

Focus especially on:

- `chmod` numeric and symbolic permissions
- Owner vs group vs others
- Sticky bit
- `chown` vs `chmod`
- `apt` vs `yum`
- `/etc/apt/sources.list`
- Jenkins repository and Java dependency
- Linux boot sequence
- `dmesg`, `lsblk`, `free -h`, `df -h`, and `lshw`
- HTTP 2xx, 3xx, 4xx, and 5xx status codes
- SSH troubleshooting
- Website slowness troubleshooting

