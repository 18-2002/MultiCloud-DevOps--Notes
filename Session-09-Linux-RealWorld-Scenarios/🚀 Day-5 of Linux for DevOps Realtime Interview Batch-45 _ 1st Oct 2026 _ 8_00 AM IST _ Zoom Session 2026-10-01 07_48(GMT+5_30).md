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
