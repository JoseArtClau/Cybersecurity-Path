# ⚙️ Operating Systems Basics (TryHackMe, Pre Security Path)

> My notes from the **Operating Systems Basics** module of TryHackMe's Pre Security path: what an OS does, Windows essentials, and the security principles an OS enforces.

## 📑 Contents
1. [Operating Systems: Introduction](#1-operating-systems-introduction)
2. [Windows Basics](#2-windows-basics)
3. [Linux CLI Basics](#3-linux-cli-basics)
4. [Windows CLI Basics](#4-windows-cli-basics)
5. [Operating System Security](#5-operating-system-security)
6. [Key Takeaways for Cybersecurity](#-key-takeaways-for-cybersecurity)

---

## 1. Operating Systems: Introduction

An **operating system (OS)** is the core software that coordinates everything on a computer. It sits between the **user**, the **applications** and the **hardware**, and works as the invisible manager that keeps the machine running as one unified system.

```
   User
    ↓
Applications      ← user space
    ↓  system calls
  Kernel          ← kernel space
    ↓
 Hardware
```

### System privilege layers

| Layer | What runs there | Hardware access |
|---|---|---|
| **Kernel space** | The core of the OS, which manages hardware and system resources | **Unrestricted** access to CPU, memory, storage and all hardware |
| **User space** | All standard applications | **None directly.** To open or save a file, play a sound or connect to Wi-Fi, an app makes a **system call** and the kernel does it on its behalf |

### OS responsibilities

| Responsibility | What it does | Everyday example |
|---|---|---|
| **Process management** | Creates, schedules, prioritises and terminates programs; decides how much CPU time each gets | Multitasking feels seamless |
| **Memory management** | Allocates RAM to processes, keeps each one's memory isolated, reclaims it when apps close; uses **virtual memory** when RAM runs low | Many apps open at once without crashing each other |
| **File system management** | Organises files into directories; handles names, paths, permissions and metadata (name, size, type, timestamps) | Creating a folder, saving a photo, setting a file to read-only |
| **User management** | Handles accounts, authentication and permissions: who can access what | Logging in with a password; other users can't see your files |
| **Device management** | Loads drivers and provides a common interface (hardware abstraction layer), so apps can just say "print this" | Plugging in a mouse, printer or external drive and it just works |

### Built-in security at a basic level
- **Authentication:** verifies who you are (passwords, biometrics)
- **Permissions:** controls what each user and app can **read, write or execute**
- **Isolation:** keeps each process in its own protected box (kernel/user space separation)
- **System protection:** guards critical system files and settings against unauthorised changes

---

## 2. Windows Basics

### Account types

| Account | Permissions |
|---|---|
| **Guest** | Temporary access, minimal permissions, can't change system settings |
| **Standard** | Everyday use: run apps, change personal settings, no system-wide changes |
| **Administrator** | Full control: install software, change configuration, manage users |

> 🔐 **Principle of least privilege:** use a Standard account day to day. Malware running under an Administrator account can do far more damage.

### The desktop
- **Desktop:** the main workspace where files, folders and shortcuts live
- **Taskbar:** a control strip giving access to apps, system tools, settings and notifications

### Windows Security: four main sections

| Section | Purpose |
|---|---|
| **Virus & threat protection** | Detects and removes malware with real-time protection and custom scans |
| **Firewall & network protection** | Controls incoming and outgoing traffic to block unauthorised access |
| **App & browser control** | Protects against unsafe apps, files and websites |
| **Device security** | Hardware-based protections that help secure the system |

---

## 3. Linux CLI Basics

*Notes in progress.* See also my [Codédex command line notes](../Codedex/command-line.md), which cover `pwd`, `ls`, `cd`, `cat`, `mkdir`, `touch`, `mv`, `cp`, `rm` and more.

---

## 4. Windows CLI Basics

*Notes in progress.*

---

## 5. Operating System Security

### The CIA triad
Security means protecting three things:

| Principle | Goal | Example of an attack on it |
|---|---|---|
| **C**onfidentiality | Private information is available only to the intended people | Someone reads files they shouldn't |
| **I**ntegrity | Nobody can tamper with data, whether stored or travelling over the network | Someone modifies or corrupts files |
| **A**vailability | The system is usable whenever you need it | Ransomware locks you out of your files |

### Authentication factors

| Factor | Example |
|---|---|
| **Something you know** | Password, PIN |
| **Something you are** | Fingerprint, face |
| **Something you have** | Phone receiving an SMS code, authenticator app, security key |

Combining two or more different factors = **multi-factor authentication (MFA)**.

### Common threats
- **Weak file permissions** hurt:
  - **confidentiality**, because attackers can read files they shouldn't access
  - **integrity**, because attackers can modify files they shouldn't edit
- **Ransomware** is malware that **encrypts** the user's files and demands payment for the key. It's an attack on **availability**.
- **Encryption** makes a file unreadable without the correct key or password. The same technique protects data when used legitimately.


## 🔐 Key Takeaways for Cybersecurity

- **Kernel vs user space** is a core security boundary: malware that reaches the kernel controls everything.
- **Least privilege** (Standard instead of Administrator) limits the damage an attacker can do.
- The **CIA triad** is a framework for classifying almost any attack.
- **MFA** protects accounts even when a password leaks.
- **File permissions** are among the first things to check when hardening or attacking a system.
- Hexadecimal shows up everywhere in security: hashes, memory addresses, packet captures.

---

*Platform: TryHackMe · Path: Pre Security · Module: Operating Systems Basics · November 2026*
