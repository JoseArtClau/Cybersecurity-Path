# 💻 Computer Fundamentals (TryHackMe, Pre Security Path)

> My notes from the **Computer Fundamentals** module of TryHackMe's Pre Security path: hardware, computer types, client-server basics, virtualisation and cloud computing.

## 📑 Contents
1. [Inside a Computer System](#1-inside-a-computer-system)
2. [Computer Types](#2-computer-types)
3. [Client-Server Basics](#3-client-server-basics)
4. [Virtualisation Basics](#4-virtualisation-basics)
5. [Cloud Computing Fundamentals](#5-cloud-computing-fundamentals)
6. [Key Takeaways for Cybersecurity](#-key-takeaways-for-cybersecurity)

---

## 1. Inside a Computer System

### Core components

| Component | Function | Where it sits |
|---|---|---|
| **CPU** (Central Processing Unit) | Processes every program, click and instruction, and tells the other components what to do | CPU socket on the motherboard |
| **RAM** (Random Access Memory) | Temporarily stores the data in use so it can be reached quickly. **Volatile:** contents are lost when power is off | DIMM slots on the motherboard |
| **PSU** (Power Supply Unit) | Converts wall electricity into the correct voltages for internal components | Mounted at the edge of the chassis |
| **Laptop charger** | Converts wall electricity into the DC power a laptop needs | Outside the computer, plugged into the wall |

### Storage

| Component | Function | Where it sits |
|---|---|---|
| **SSD** (Solid State Drive) | Permanently stores files, apps and the OS. Fast and silent, no moving parts | M.2 slot directly on the motherboard (2.5" SATA SSDs go in a drive bay) |
| **HDD** (Hard Disk Drive) | Permanently stores files on spinning magnetic disks with moving parts | Drive bay inside the chassis |

### Connectivity

| Component | Function | Where it sits |
|---|---|---|
| **Network adapter** | Sends and receives data over a network, connecting the computer to other devices and the internet | Inside the chassis, near the rear |
| **Wi-Fi dongle** | Adds wireless connectivity through USB | USB port on the outside of the chassis |
| **I/O panel** | Provides external ports: USB, HDMI, audio, Ethernet | Rear opening of the chassis |

### Visual output

| Component | Function | Where it sits |
|---|---|---|
| **GPU** (Graphics Card) | Creates what's shown on screen; processes images, video and 3D graphics | PCIe x16 slot on the motherboard |
| **Laptop GPU** | Same job with smaller, lower-power hardware | Integrated into or mounted on the laptop motherboard |

---

## 2. Computer Types

| Type | Screen & keyboard | Main purpose |
|---|---|---|
| **Laptop** | Built in | Portable everyday computing |
| **Desktop** | Yes (external) | Sustained performance in a fixed location |
| **Workstation** | Yes (external) | Precision and reliability for professional tasks |
| **Server** | Usually no | Providing services to many users over a network |

### IoT vs embedded
Both can be small and single-purpose. **The difference is connectivity.**

- **IoT devices** connect to a network to report data or receive commands.
  Examples: thermostat, smart doorbell, fitness tracker.
- **Embedded computers** may not connect to anything. They work inside a machine, often for years, without anyone noticing.
  Examples: coffee maker controller, automatic door sensor, lamp dimmer chip.

> 🔐 Every IoT device on a network is a potential entry point for an attacker, and they are often poorly secured.

### Cooling

| Laptop | Desktop |
|---|---|
| Tiny fan | Large fans |
| Heat pipes | Airflow space |
| Heat sink | Tower cooler |

---

## 3. Client-Server Basics

Early networks such as **ARPANET, CYCLADES, NPL and NSFNET** paved the way for the modern internet.

### Protocol
A protocol defines **how a client and a server communicate**:
- which commands both sides understand
- how a request is structured
- what syntax is used
- which response goes with which type of request
- how to respond to a faulty request

### Port
- A port identifies **a specific service** running on a system.
- A service on a server **listens** on a specific port, and the client must connect to the correct one.
- One server can run many services at once, each on a different port.

### DNS and IP
- **IP address:** the "home address" of a system on a network.
- **DNS (Domain Name System):** works like GPS. It turns a website name (e.g. `tryhackme.com`) into the server's IP address.

### HTTP(S)
**HTTP(S)** (Hypertext Transfer Protocol (Secure)) is the client-server protocol of the web. It is **stateless**: each request is processed independently, and the server remembers nothing about previous requests.

That's why logins use a **session identifier** (stored in a cookie or token) that the browser sends with every request. Without it, you'd have to log in again on every page.

### Anatomy of a request (as seen in browser dev tools)

| Field | Meaning |
|---|---|
| **Scheme** | Protocol used: HTTP or HTTPS |
| **Host** | Name of the host the resource is requested from |
| **Filename** | Which file was requested |
| **Address** | IP address where the site is hosted |
| **Status** | Whether the request succeeded (status code) |

Every response has two parts:
- **Response header:** metadata about the response
- **Response body:** the requested content

> 🔐 Session cookies are a prime target: stealing one can let an attacker act as a logged-in user without knowing the password.

---

## 4. Virtualisation Basics

### The problem: "one server = one application"
Before virtualisation, a company running a website, a database, email and an internal app needed **a separate physical server for each**:

- **High cost:** hardware, electricity, cooling, maintenance and data-center space
- **Low utilisation:** many servers sat at only 5–20% usage
- **Slow deployment:** days or weeks to set up a new server
- **Hard to scale:** more resources often meant buying another server

### The solution: virtualisation
Several systems share one physical server safely.

| Analogy | Real thing |
|---|---|
| 🏢 The building | Physical server |
| 🚪 The apartments | Virtual machines (VMs, "lab machines" on TryHackMe) |
| 👤 The tenants | Operating systems / applications |
| 🧑‍💼 The building manager | **Hypervisor** |

### Hypervisor
The software that creates and manages VMs:
- divides one physical computer into several virtual ones
- gives each VM its share of CPU, memory and storage
- keeps the VMs isolated from each other
- manages their lifecycle: start, stop, pause, clone, delete

| | **Type 1** (bare metal) | **Type 2** (hosted) |
|---|---|---|
| Runs on | Directly on the hardware | Inside an existing OS |
| Strengths | Fast, efficient | Easy to install |
| Best for | Servers, professional environments | Learning, testing, small setups |

| Use case | Type 1 | Type 2 |
|---|:---:|:---:|
| Production server | ✅ | |
| Database server | ✅ | |
| Data center | ✅ | |
| Testing malicious files | | ✅ |
| Software testing | | ✅ |
| Running Kali Linux | | ✅ |

### Virtual machines
- A virtual computer with its own virtual CPU, RAM, storage and network
- Can run any operating system
- Fully isolated: if one VM breaks, the others keep working

**Examples:**
- Running **Kali Linux** without buying a second computer
- Opening a **suspicious file** in an isolated VM to protect the main machine

> ⚠️ When analysing malware in a VM, make sure it can't reach the host: use a different OS for guest and host, or isolate the VM's network from the host.

### Containers (the rooms inside the apartment)
A **container** is a lightweight, isolated environment that runs a single application plus everything it needs.

Instead of a full OS, a container shares the host's **kernel**, the core of the OS that talks to the hardware and manages memory and running programs.

- ✅ Starts almost instantly and uses fewer resources than a VM
- ✅ Packages the app with its dependencies, so it runs the same anywhere
- ✅ Isolated: a misbehaving container doesn't affect the others
- ❌ Must match the host's OS type (e.g. no Windows container on a Linux host)

**Docker** is the most common open-source platform for building, deploying and running containers.

| | Virtual machine | Container |
|---|---|---|
| Includes | Full OS | App + dependencies only |
| Kernel | Its own | Shared with host |
| Startup | Slower | Almost instant |
| Resource use | Heavier | Lighter |

### Benefits of virtualisation
Cost savings · better resource usage · **safe testing for cybersecurity** · faster deployment · flexibility · portability · scalability · centralised management

---

## 5. Cloud Computing Fundamentals

### Main features
Scalability · on-demand self-service · pay only for what you use · security · high availability · global access

### Deployment types

| Type | What it is | Typical users |
|---|---|---|
| **Public** | Services shared by many customers, accessed over the internet | Startups, websites, global apps: affordable, easy to scale, no infrastructure to manage |
| **Private** | A cloud built for one organisation only | Banks, healthcare, government: more control, customisation and compliance |
| **Hybrid** | Public and private working together | E-commerce: sensitive data stays private, while public capacity is used during demand peaks |

### Service models

| Model | You manage | Provider manages | Example |
|---|---|---|---|
| **IaaS** (Infrastructure) | OS + applications | Physical hardware, networking | Renting virtual servers (AWS EC2) |
| **PaaS** (Platform) | Your application | Hardware + OS | Ready-made environment to deploy apps |
| **SaaS** (Software) | Nothing; you just use it | Everything | Gmail, Zoom |

### Major providers
AWS · Microsoft Azure · Google Cloud Platform · Alibaba Cloud · IBM Cloud · Oracle Cloud

### AWS basics
- **EC2:** a virtual computer in the cloud, with CPU and RAM, that you can create, use and resize on demand. Each EC2 instance adds one computer to your environment.
- **Instance type:** how powerful that virtual computer is.
  - Bigger instance = more power + higher cost
  - Smaller instance = less power + lower cost

> 🔐 In IaaS, **you** are responsible for securing the OS and apps. Knowing who manages what (the *shared responsibility model*) is key in cloud security.

---

## 🔐 Key Takeaways for Cybersecurity

- **Ports and services** are what attackers scan for. Knowing what listens where is the start of reconnaissance.
- **DNS, IP and HTTP(S)** are the base of web security and network analysis.
- **Stateless HTTP + cookies** explains why session hijacking works.
- **VMs** let me safely run Kali Linux and analyse suspicious files.
- **IoT devices** greatly expand a network's attack surface.
- **Cloud security** depends on understanding who is responsible for each layer.

---

*Platform: TryHackMe · Path: Pre Security · Module: Computer Fundamentals · November 2026*
