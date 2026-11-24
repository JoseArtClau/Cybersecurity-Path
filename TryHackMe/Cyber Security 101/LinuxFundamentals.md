# 🐧 Linux Fundamentals (TryHackMe, Pre Security Path)

> My notes from the **Linux Fundamentals** module (Parts 1–3) of TryHackMe's Pre Security path. Many servers and security tools run on Linux, which makes it a critical skill in cybersecurity.

## 📑 Contents
1. [Part 1: First Commands](#part-1-first-commands)
2. [Part 2: SSH, Flags, Files & Permissions](#part-2-ssh-flags-files--permissions)
3. [Part 3: Utilities, Processes & Automation](#part-3-utilities-processes--automation)
4. [Quick Reference](#-quick-reference)
5. [Key Takeaways for Cybersecurity](#-key-takeaways-for-cybersecurity)

---

## Part 1: First Commands

### Basic commands

| Command | What it does | Example |
|---|---|---|
| `whoami` | Shows which user you are logged in as | `whoami` |
| `echo` | Prints the text you give it | `echo "Hello"` |
| `ls` | Lists what's in the current folder | `ls` |
| `cd` | Changes directory | `cd Documents` |
| `cat` | Shows a file's contents | `cat notes.txt` |
| `pwd` | Prints the working directory ("where am I?") | `pwd` |

### Searching

| Command | What it does | Example |
|---|---|---|
| `find` | Searches for files **by name** | `find -name password.txt` |
| `grep` | Searches **inside files** for text | `grep "password123" passwords.txt` |

> 💡 `find` also accepts wildcards: `find -name "*.txt"` finds every `.txt` file.

### Shell operators

| Operator | What it does | Example |
|---|---|---|
| `&` | Runs a command **in the background**, so you can keep working while it runs | `long-task &` |
| `&&` | Runs the second command **only if the first one succeeds** | `mkdir test && cd test` |
| `>` | Sends a command's output to a file, **overwriting** it | `echo hi > file.txt` |
| `>>` | Sends output to a file, **appending** to the end | `echo bye >> file.txt` |

---

## Part 2: SSH, Flags, Files & Permissions

### SSH (Secure Shell)
- A protocol for **logging into and using another computer remotely** over a network
- Everything sent is **encrypted** while it travels, and only readable once it reaches the other machine

```bash
ssh username@10.10.10.10
```

### Flags, `--help` and `man`

| Option | What it does | Example |
|---|---|---|
| `-a` / `--all` | Shows hidden files too (names starting with `.`) | `ls -a` |
| `--help` | Lists a command's options with short descriptions and examples | `ls --help` |
| `man` | Opens the **man**ual page: full documentation for a command | `man ls` |

### Working with files and folders

| Command | What it does | Example |
|---|---|---|
| `touch` | Creates an empty file | `touch recipe` |
| `mkdir` | Creates a folder | `mkdir mydirectory` |
| `cp` | Copies a file/folder: `cp <existing> <new name>` | `cp notes.txt notes-backup.txt` |
| `mv` | Moves **or renames**: `mv <original> <new location/name>` | `mv old.txt new.txt` |
| `rm` | Removes a file | `rm file.txt` |
| `rm -R` | Removes a folder and everything in it ⚠️ | `rm -R mydirectory` |

### Users, groups and switching users
- A file has **one owner**, and can also be assigned a **group** with its own, separate set of permissions.
- `su` switches to another user. You need **the username** and **that user's password**.

```bash
su user2
```

### Common root directories

The file system is like a tree: the **trunk** is `/`, the **branches** are directories like `/etc`, `/var`, `/home`, and the **leaves** are the files and folders inside them.

| Directory | Purpose |
|---|---|
| `/etc` | One of the most important: **system and program configuration files** |
| `/var` | **Variable data** that services read/write often: **log files** (`/var/log`), databases, backups |
| `/root` | **Home folder of the root user** (not to be confused with `/`) |
| `/tmp` | **Temporary** data needed only once or twice. Usually cleared on reboot, and **any user can write here** |
| `/home` | Home folders of normal users |

---

## Part 3: Utilities, Processes & Automation

### Terminal text editors

| Editor | Notes |
|---|---|
| **nano** | Beginner-friendly: `nano filename`. Features use **Ctrl + a letter** (e.g. `Ctrl+O` save, `Ctrl+X` exit) |
| **Vim** | More powerful but has a learning curve (`i` to insert, `Esc` then `:wq` to save and quit) |

### Transferring files

| Tool | What it does | Example |
|---|---|---|
| `wget` | Downloads a file from the web over HTTP, like a browser would | `wget https://example.com/file.txt` |
| `curl` | Also downloads/requests web resources | `curl -O https://example.com/file.txt` |
| `scp` | **Secure copy** between two computers over **SSH** (authentication + encryption) | see below |
| `python3 -m http.server` | Turns the current folder into a quick **web server** (port 8000) so other machines can download from it | `python3 -m http.server` |

**SCP examples** (you need valid credentials on the remote machine):

```bash
# Local → remote
scp important.txt ubuntu@192.168.1.30:/home/ubuntu/transferred.txt

# Remote → local
scp ubuntu@192.168.1.30:/home/ubuntu/documents.txt notes.txt
```

### Processes
- **Processes** are the programs running on the machine, managed by the **kernel**.
- Each has a unique **PID** (Process ID).

| Command | What it shows |
|---|---|
| `ps` | Processes in **your** session: status, session, CPU time, command name |
| `ps aux` | **All** processes, including other users' and system ones |
| `top` | **Real-time**, auto-refreshing process stats |

### Killing processes
`kill <PID>` sends a **signal** to a process:

| Signal | Effect |
|---|---|
| **SIGTERM** | Kill, but let it **clean up** first (default) |
| **SIGKILL** | Kill **immediately**, no cleanup (`kill -9 <PID>`) |
| **SIGSTOP** | **Pause/suspend** the process |

### How processes start
- The OS uses **namespaces** to split up the computer's resources and **isolate** processes: only processes in the same namespace can see each other. This is great for security, and it's the basis of containers.
- At boot, the system's **init** process starts first: **systemd** on Ubuntu, with **PID 1**. It manages the user's processes and sits between the OS and the user. Every other process starts as a child of it.

### Services on boot: `systemctl`

```bash
systemctl start apache2     # start now
systemctl stop apache2      # stop now
systemctl enable apache2    # start automatically on boot
systemctl disable apache2   # don't start on boot
```

### Background vs foreground

| Action | How |
|---|---|
| Start in background | `command &` |
| Pause the current foreground process | `Ctrl + Z` |
| Bring a background process back | `fg` |
| Resume a paused process in the background | `bg` |

### Cron and crontabs
- **cron** is a process started at boot that runs **scheduled tasks** (cron jobs).
- A **crontab** is the special file listing those tasks, one per line. Edit it with `crontab -e`.
- Each line needs **6 values**:

---

## 📋 Quick Reference

```bash
whoami                     # who am I?
pwd                        # where am I?
ls -a                      # list everything, hidden files too
find -name "*.txt"         # find files by name
grep "text" file.txt       # find text inside a file
cmd1 && cmd2               # run cmd2 only if cmd1 succeeds
echo hi >> log.txt         # append to a file
ssh user@IP                # remote login
scp file user@IP:/path     # secure copy to another machine
ps aux | grep apache       # find a specific process
kill -9 <PID>              # force-kill a process
systemctl enable <service> # start service on boot
crontab -e                 # edit scheduled tasks
man <command>              # read the manual
```

---

## 🔐 Key Takeaways for Cybersecurity

- `whoami` is often the **first command after gaining access** to a machine.
- `find` and `grep` are how you hunt for **passwords, keys and flags** in files.
- `/etc` holds sensitive config (e.g. `/etc/passwd`), and `/var/log` holds logs: valuable to both attackers and defenders.
- `/tmp` is writable by everyone, so attackers often use it to drop tools.
- **Cron jobs** are a classic **persistence** and **privilege escalation** vector, especially when a job runs as root and calls a script normal users can edit.
- `scp`, `wget` and `python3 -m http.server` are standard ways to **move tools** between an attacker machine and a target.
- `ps aux` reveals what's running and as which user: key for spotting suspicious processes.

---

*Platform: TryHackMe · Path: Pre Security · Module: Linux Fundamentals (Parts 1–3) · November 2026*
