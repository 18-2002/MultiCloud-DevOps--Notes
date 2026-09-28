## Key Outcomes

Day 2 of the Linux administration module (Batch 45) covered approximately 30–40 Linux commands, GCP VM creation via both GUI and CLI, SSH key pair generation and management, and a range of practical use cases including file operations, process monitoring, and disk usage checks. Students practiced commands hands-on in GCP-provisioned Ubuntu VMs and engaged in extended Q&A around public/private key authentication concepts. The session reinforced real-time interview language and framing techniques alongside technical content. 

---

## Session Context & Agenda

- **Batch:** Batch 45, Day 2 of Linux administration
- **Instructor:** Vikas
- **Week's Focus:** Linux administration activities — commands, OS fundamentals, and GCP machine setup 
- **Command Target:** Instructor set a minimum target of **30 commands** to be covered in the session; by end of session, participants reported running 30–50 commands 
- **Prior Session Recap:** Day 1 covered OS overview, kernel vs. shell distinction, Linux basics, GCP account creation, and Windows vs. Linux comparison — not repeated in detail 

---

## Linux & OS Fundamentals Recap

- **OS Structure:** OS has two major parts — **kernel** (connected to hardware) and **shell** (connected to users/applications); requests flow from user/app → OS/kernel → hardware → output returned 
- **Linux as Kernel:** Linux is technically a kernel — closely connected to hardware, delivering better performance; though commonly called an OS, the more precise term is kernel 
- **Why Linux is Popular — Four Keywords:**
    - **Free and open source** — source code available on GitHub (`torvalds/linux` repo) 
    - **Customizable** — code can be modified per requirement 
    - **Performance** — better than many alternatives due to low-level design 
    - **Secure** — widely used in IT for security-sensitive workloads 
- **Why Linux is Written in C:** C is a low-level language; conversion of C code to machine language is faster, yielding higher performance for OS operations; Linux has ~19,000 contributors with code maintained on GitHub 

---

## GCP VM Creation — GUI and CLI

### GUI Method (Beginner Path)

- Navigate to `console.cloud.google.com` → **Compute Engine** → **Create Instance** 
- **Default Configuration Used:**
    - Region: default
    - Machine type: **2 vCPU, 4 GB RAM** (E2 medium) 
    - OS: Changed from Debian to **Ubuntu 24.04 LTS** (not minimal — full version to ensure all tools/commands are available) 
    - Disk: **10 GB** (sufficient for learning) 
- **Why Ubuntu 24.04 LTS over 26.10:** Interview-ready answer — "my client has approved 24.04 LTS as the stable version"; using client-approved language makes fake/non-production experience sound real-time 
- **Networking:** HTTP and HTTPS traffic allowed; firewall rule `0.0.0.0/0` added for internet visibility (learning purpose only) 
- **Minimal vs. Full OS:** Minimal = fewer tools, some networking/firewall/sudo commands may not work; full version recommended for learning 

### CLI / Automation Method (Experienced Path)

- After configuring via GUI, click **Equivalent Code** to copy the auto-generated CLI command 
- Open **Cloud Shell** (Activate Cloud Shell button) → paste the command → VM created in ~30–35 seconds 
- **Use case for CLI:** Reusability — save the command, modify parameters (e.g., machine name), and recreate VMs without going through GUI each time; also relevant when companies restrict GUI access and require CLI-only operations 
- **VM Output Fields Explained:**
    - Machine name, zone (location), machine type (E2 medium), **internal IP** (for same-VPC communication), **external IP** (for internet communication), status: running 

### Connecting to VM via SSH

- Click **SSH button** in GCP console → browser authenticates via port 22 using public/private key transfer → user is authorized and landed into the VM 
- If a red security pop-up appears when VM is publicly accessible, it can be safely ignored for learning purposes 

---

## SSH Key Pair — Generation, Concepts, and Interview Scenarios

### Key Generation

- **Command:** `ssh-keygen` 
- Run as root user (`sudo -i` first); press Enter three times (accept default location, skip passphrase twice) 
- **Default location:** `~/.ssh/` (hidden folder) 
- **Output files:**
    - `id_rsa` → **private key** (no extension)
    - `id_rsa.pub` → **public key** (`.pub` extension) 

### Key Sharing Workflow

- **Junior Engineer (Day 1 join):** Generate key pair → read public key with `cat id_rsa.pub` → copy output → share via email/message with manager/lead 
- **Manager/Lead Activity:** Receive public key from junior → go to **GCP → Compute Engine → Metadata → SSH Keys → Edit → Add Item** → paste public key → access is granted to that user 
- **Rule:** Never share the private key — private key stays with the individual always; only the public key is shared 

### How Key Pair Authentication Works

- Private key = half the combination; public key = other half
- When connecting: `ssh -i <private_key> username@IP_address` 
- System checks: does the private key match the public key already on the server? If yes → access granted 
- **Analogy used:** Lock (public key) and key (private key) — only the correct key opens the correct lock; or: tala (lock) and chaabi (key) — when combined, they open 

### Interview Questions Covered

- **Can the same key pair be used across companies?**
    - Yes — key pairs are not machine-specific or company-specific; if the key pair combination matches on any server, access is granted; analogy: buying a new house where the same lock-and-key combination happens to fit 
    - Caveat (Mohd's point): Technically, generating on a specific machine ties the username in the key; if the user doesn't exist on the new server, a new user must be created first — but the key pair itself is transferable 
    - Best practice: Generate key pair using a tool (e.g., PuTTY, MobaXterm) locally before joining any company; carry the same key pair to multiple companies/projects 
- **Why use key pairs instead of passwords?**
    - Passwords like `Welcome@123`, `admin123` are commonly reused across organizations — insecure 
    - Even if someone obtains your public key, they cannot access the server without your private key 
    - Key pair provides **passwordless authentication** — critical for automation tools like Ansible managing multiple VMs 
- **Public key on GCP metadata — account-level or VM-level?**
    - Account-level (metadata): one public key grants access to all VMs in that GCP account 

---

## Linux Commands Covered

### Navigation & File System

|       Command       |                         Purpose                          |
|---------------------|----------------------------------------------------------|
| `pwd`               | Print Working Directory — shows current location         |
| `ls`                | List files/directories in current location               |
| `cd <path>`         | Change Directory                                         |
| `mkdir <name>`      | Make Directory — creates empty directory                 |
| `touch <filename>`  | Create empty file                                        |
| `cat <filename>`    | Read/display full file content                           |
| `more <filename>`   | Page-wise file reading (useful for large files)          |
| `vi <filename>`     | Open file in VI editor (Linux equivalent of Notepad)     |
| `cp <src> <dest>`   | Copy file                                                |
| `mv <src> <dest>`   | Move or rename file                                      |
| `rm <filename>`     | Remove/delete file — **cannot be recovered in Linux**    |
| `locate <filename>` | Search for file across the system (like Windows search)  |

### System Information

|    Command    |                                  Purpose                                  |
|---------------|---------------------------------------------------------------------------|
| `hostname`    | Display machine name                                                      |
| `hostname -I` | Display private/internal IP address                                       |
| `uname`       | Show OS type (Linux)                                                      |
| `uname -a`    | Show all system details — OS version, kernel, architecture, machine name  |
| `whoami`      | Show current logged-in user                                               |
| `history`     | Show all previously executed commands                                     |

### Process & Performance Monitoring

|         Command          |                                           Purpose                                            |
|--------------------------|----------------------------------------------------------------------------------------------|
| `ps`                     | Show processes running under current (root) user                                             |
| `ps -aef`                | Show **all** processes with full details (A=all users, E=every process, F=full format)       |
| `ps -aef \| grep <name>` | Filter processes by name — pipe output of `ps` into `grep` for search                        |
| `top`                    | Real-time task manager — shows CPU, memory, tasks, uptime, users (press `q` to exit)         |
| `free -h`                | Show memory usage in human-readable format; `-m` for MB, `-g` for GB                         |
| `df -h`                  | Show disk usage per partition in human-readable format — used for disk full troubleshooting  |

### File Reading & Filtering

|      Command      |                                      Purpose                                       |
|-------------------|------------------------------------------------------------------------------------|
| `head -10 <file>` | Show first 10 lines of a file                                                      |
| `tail -10 <file>` | Show last 10 lines of a file                                                       |
| `cat` vs `more`   | `cat` = full file in one shot; `more` = paginated (37% at a time for large files)  |

**Production use case for** **`tail`****:** When troubleshooting production issues, engineers read the last 10–100 lines of log files to find where code is breaking — not the entire log 

### Networking & External Tools

|      Command       |                                      Purpose                                       |
|--------------------|------------------------------------------------------------------------------------|
| `curl <URL>`       | Hit a URL from Linux terminal; tool for transferring data from a server using URL  |
| `curl ifconfig.me` | Get the external/public IP of the machine                                          |
| `man <command>`    | Open manual page for any command (e.g., `man curl`); press `q` to exit             |

### Key & Security Commands

|            Command             |              Purpose               |
|--------------------------------|------------------------------------|
| `ssh-keygen`                   | Generate public/private key pair   |
| `ssh -i <private_key> user@IP` | Connect to a VM using private key  |

### Pipe and Grep

- **Pipe (****`|`****):** Takes output of first command as input to second command 
- **Grep:** Search/filter tool — e.g., `ps -aef | grep java` returns only Java-related processes 
- **Interview framing:** Demonstrates intermediate-level Linux knowledge beyond basic commands 

---

## VI Editor Basics

- `vi <filename>` opens the editor; if file doesn't exist, opens a blank page 
- Paste content → `Escape` → `:wq` → saves and exits 
- Linux does **not require file extensions** — all files are treated as text by default 
- `cat` and `vi` can both open/read the same file with identical output for small files 

---

## Disk & Resource Troubleshooting Scenario

- **Scenario (interview question):** Application becomes slow or stops due to server disk storage fault
- **Step 1:** Run `df -h` to check which partition is full 
- **Step 2:** Identify full partition → delete/clean up unnecessary files
- **Analogy:** Like checking phone/laptop storage — identify what's useless vs. useful and remove it 

---

## Interview Preparation Guidance

- **Use client-approval language:** Instead of saying "this is the stable version," say "my client has approved this version" — makes training experience sound like real-time work experience 
- **Four keywords for Linux popularity:** Free, open source, performance, secure — memorize and use in interviews 
- **`ps -aef`** **vs** **`systemctl`****:** Both can reveal running services; `systemctl` is the standard for service management, but `ps -aef | grep <service>` is a valid alternative when `systemctl` utility is unavailable (e.g., minimal container images) 
- **Key pair interview trap:** If asked "can the same key pair work in another company?" — answer is yes, as long as the combination matches on the target server; use the lock-and-key analogy 
- **Write down interview questions encountered:** Ankur shared that he was asked about searching/stopping services in a recent interview — instructor encouraged noting and sharing such questions with the group 
- **Sharing opportunities:** If any student finds a Linux/cloud job opening in their company, post it in the WhatsApp group for others to benefit 

---

## GCP & Cloud Housekeeping

- **Billing concern (negative balance):** Negative billing = advance payment made; no issue, credits will be consumed over time; 2-month runway is sufficient for the course 
- **Delete VMs after class:** Running VMs incur billing even when not in use — analogy: booking a 5-star hotel room and not staying still generates a bill; delete resources when not needed 
- **External IP check:** Use `curl ifconfig.me` from Linux terminal OR visit `ifconfig.me` in browser; both return the machine's external IP 
- **Internal IP check:** `hostname -I` returns private IP, verifiable against GCP Console → Compute Engine → VM Instances 
- **Alternative for students without GCP access:** AWS Ubuntu VM is an acceptable substitute for practicing Linux commands 
- **GCP account from mobile:** Some commands may not work on mobile terminal, but basic commands will run; use as fallback only 

---

## Bandit Game & Practice

- Students were assigned the **Bandit wargame** (OverTheWire) for hands-on Linux practice
- Progress reported: students reached levels 12–23+; one student completed 100+ attempts to reach level 23 
- **Game rules clarified:** Password is not visible when typed (standard Linux security behavior); password from previous level must be carried forward to unlock next level 
- **Target:** Complete as many levels as possible — more hours invested = higher confidence = better job package 
- Instructor emphasized: game is working correctly; issues are user-side (not playing correctly, not copying passwords properly) 

---

## Student Q&A Highlights

- **Q (Indira): Does** **`touch`** **always create a text file? Do we need extensions?**
Linux does not require file extensions; `touch` always creates a plain text file by default. 
- **Q (Indira): Can we jump to a specific line number in a file?**
Yes — instructor acknowledged this is possible and will be covered; `head` and `tail` numbers are adjustable (e.g., `head -50`, `tail -100`). 
- **Q (Ankur): Redirect operator (****`>`****) for file creation?**
Instructor redirected — this is a redirection operator topic to be covered separately; introducing it now would confuse other students. 
- **Q (Shubham): Do we need to manually copy public key to every server?**
Yes, for the first time it is manual; scripting/automation can handle it subsequently. 
- **Q (Nithish): Why use key pairs specifically in cloud?**
Passwordless authentication is required — especially for automation tools like Ansible connecting to multiple VMs simultaneously; passwords cannot be embedded in scripts securely. 
- **Q (Gopal Dash): Email ID typo during registration**
Instructor corrected the email ID in the system; login credentials updated accordingly. 
- **Q (Neminath): Will practicing 4–5 hours/day on GCP VM affect billing/credit?**
No significant billing impact for the machine size being used; free credits are sufficient. 

---

## Action Items & Follow-Ups

- **All students:** Run all commands shared in the GitHub repo (Day 2 of Linux commands posted by instructor); execute and confirm with "D" in chat 
- **All students:** Pin the Batch 45 WhatsApp community to avoid missing messages 
- **All students:** Respond to the WhatsApp poll on video completion, GCP account status, and LinkedIn posts 
- **All students:** Post LinkedIn updates and tag the instructor within 2–3 days for leaderboard sync (leaderboard updates once per week) 
- **Students with interview questions:** Write down questions encountered in interviews and share in the group/post on LinkedIn 
- **Ashish (7-year exp, Accenture interview on 30th):** Watch AWS Batch 44 recordings — focus on top 10 AWS services (IAM, EC2, VPC, S3, RDS, Lambda); position RDS as SME given prior SQL/Informatica/Teradata background; do practical exercises before interview 
- **Bhalendu (Azure DevOps role):** Watch Batch 44 Azure DevOps recordings covering Azure Repos, Pipelines, Artifacts; treat Azure Boards as equivalent to Jira 
- **Students with LMS access issues:** Contact the support number (not instructor's personal number); LMS access issues related to fees — check payment status 
- **Instructor:** Teach auto-delete scheduling for VMs in a future session 
- **Instructor:** Teach PuTTY/MobaXterm SSH login for GCP in AWS classes 
- **Instructor:** Demonstrate Ansible practical with multiple VMs to show real-world key pair usage 
