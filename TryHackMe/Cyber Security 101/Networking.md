# 📡 Networking (TryHackMe, Pre Security Path)

> My notes from the **Networking** module of TryHackMe's Pre Security path: the OSI and TCP/IP models, essential and core protocols, secure protocols, and packet analysis with Wireshark and tcpdump.

## 📑 Contents
1. [Networking Concepts](#1-networking-concepts)
2. [Networking Essentials](#2-networking-essentials)
3. [Networking Core Protocols](#3-networking-core-protocols)
4. [Networking Secure Protocols](#4-networking-secure-protocols)
5. [Wireshark: The Basics](#5-wireshark-the-basics)
6. [Tcpdump: The Basics](#6-tcpdump-the-basics)
7. [Port Cheat Sheet](#-port-cheat-sheet)
8. [Key Takeaways for Cybersecurity](#-key-takeaways-for-cybersecurity)

---

## 1. Networking Concepts

### The OSI model
A **conceptual model** created by the **ISO** (International Organization for Standardization) that describes how communication should happen in a computer network, in **7 layers**.

| # | Layer | Main function | Examples |
|---|---|---|---|
| 7 | **Application** | Provides network services and interfaces directly to applications | HTTP, FTP, DNS, SMTP, POP3, IMAP |
| 6 | **Presentation** | Makes data understandable to the application: **encoding, compression, encryption** | TLS (encryption), JPEG, ASCII |
| 5 | **Session** | **Establishes, maintains and synchronises** sessions between applications; recovery if transmission fails | NFS |
| 4 | **Transport** | **End-to-end** communication between applications on different hosts; data segmentation | TCP, UDP |
| 3 | **Network** | **Logical addressing and routing** between different networks | IP, ICMP, VPN |
| 2 | **Data Link** | Reliable transfer between devices on the **same network segment** | Ethernet, Wi-Fi |
| 1 | **Physical** | The physical transmission medium | Electrical, optical, wireless signals |

> A **network segment** is a group of devices sharing the same medium or channel.
> Layer 2 = same segment. Layer 3 = between different networks.

### TCP/IP model vs OSI

| TCP/IP layer | OSI layers |
|---|---|
| **Application** | 7 Application + 6 Presentation + 5 Session |
| **Transport** | 4 Transport |
| **Internet** | 3 Network |
| **Link** | 2 Data Link (+ 1 Physical) |

### IP addresses
- Every host needs a **unique identifier** so other hosts can reach it. With TCP/IP, that's an **IP address**.
- **Private IP addresses** are used inside local networks and aren't routable on the internet:
  - `10.0.0.0 – 10.255.255.255`
  - `172.16.0.0 – 172.31.255.255`
  - `192.168.0.0 – 192.168.255.255`
- **Public IP addresses** are unique on the internet.
- A **subnet mask** (e.g. `255.255.255.0` or `/24`) tells which part of the IP is the **network** and which part is the **host**.

### Routing
- A **router** forwards packets toward the right network, working at **layer 3** by inspecting the destination IP.
- A packet usually passes through **multiple routers** before reaching its destination.

### UDP vs TCP

**UDP (User Datagram Protocol)**, layer 4:
- **Connectionless**: no connection needed before sending
- **No delivery confirmation**: no way to know a packet arrived
- Uses **port numbers** to reach a specific **process** on the target host (the IP only identifies the host)

**TCP (Transmission Control Protocol)**, layer 4:
- **Connection-oriented**: a connection must be set up first
- Uses several mechanisms (acknowledgements, sequence numbers) to guarantee **reliable delivery**

**Three-way handshake**

```
Client                                  Server
  | --- SYN (client's random ISN) --->    |
  | <-- SYN-ACK (server's random ISN) --  |
  | --- ACK -------------------------->   |
  |       connection established          |
```
*ISN = Initial Sequence Number*

### Encapsulation
Each layer **adds a header** to the data it receives and passes the result to the layer below.

| Step | Layer | Unit of data | What's added |
|---|---|---|---|
| 1 | Application | **Application data** | Formatted by the app's protocol (e.g. HTTP) |
| 2 | Transport | **TCP segment** / **UDP datagram** | TCP or UDP header |
| 3 | Network | **IP packet** | IP header (now routable over the internet) |
| 4 | Data link | **Frame** | Ethernet/Wi-Fi header **and trailer** |

```
[ Frame header | IP header | TCP header | Application data | Frame trailer ]
```

### Telnet
- **Teletype Network**: a protocol for **remote terminal** connections, letting you send text commands to a remote system
- Port **23**, and ⚠️ **everything is sent in cleartext**, including passwords

---

## 2. Networking Essentials

To join a network, a device needs at minimum:
1. an **IP address** + **subnet mask**
2. a **router** (gateway)
3. a **DNS server**

### DHCP: automatic configuration (DORA)

| Step | Message | Who | Purpose |
|---|---|---|---|
| **D**iscover | `DHCPDISCOVER` | Client (broadcast) | "Is there a DHCP server here?" |
| **O**ffer | `DHCPOFFER` | Server | Offers an available IP |
| **R**equest | `DHCPREQUEST` | Client | Accepts the offered IP |
| **A**cknowledge | `DHCPACK` | Server | Confirms the IP is assigned |

At the end, the client has a **leased IP address**, a **gateway** to leave the local network, and a **DNS server** to resolve names.

### ARP: finding MAC addresses
- On Ethernet/Wi-Fi, an IP packet travels inside a **frame**, and the frame needs the target's **MAC address**.
- A host knows the target's IP but must look up its MAC. Devices only need each other's MACs **while communicating**.
- **ARP (Address Resolution Protocol)** does this translation from **layer 3 (IP) → layer 2 (MAC)**:
  - **ARP Request** (broadcast): "Who has 192.168.1.10?"
  - **ARP Reply**: "192.168.1.10 is at aa:bb:cc:dd:ee:ff"
- ARP messages go **directly inside an Ethernet frame**, not inside IP or UDP, so ARP is considered **layer 2**.

### ICMP: troubleshooting networks
**Internet Control Message Protocol**, used for **diagnostics and error reporting**.

| Command | What it does |
|---|---|
| `ping` | Tests whether a target is **alive** and reachable, and measures the **round-trip time (RTT)** |
| `traceroute` (Linux) / `tracert` (Windows) | Discovers the **route** (every router) between you and the target |

**How traceroute works:**
- Every IP packet has a **TTL** (Time-to-Live): the maximum number of routers it can pass through.
- Each router **decrements TTL by 1**. At **0**, the router drops the packet and sends back an **ICMP Time Exceeded** message.
- Traceroute sends packets with TTL = 1, 2, 3… so each router along the path reveals itself.

### Routing protocols

| Protocol | Key idea | Where used |
|---|---|---|
| **OSPF** (Open Shortest Path First) | Routers share link-state info, so each has a **full map** of the network and computes the best paths | Large internal networks |
| **EIGRP** (Enhanced Interior Gateway Routing Protocol) | **Cisco proprietary**; routers share reachable networks and their **cost** | Cisco networks |
| **BGP** (Border Gateway Protocol) | Exchanges routes **between different networks**: the main routing protocol **of the Internet** | Internet / ISPs |
| **RIP** (Routing Information Protocol) | Simple; picks routes with the **fewest hops** | Small networks |

### NAT (Network Address Translation)
Lets **many private IPs share one public IP** to reach the internet. A company with 20 computers needs **1** public IP instead of 20.

---

## 3. Networking Core Protocols

### DNS: remembering addresses
- Maps **domain names → IP addresses**
- **Layer 7** (application)
- Uses **UDP port 53** by default and **TCP port 53** as a fallback

| Record | Maps to |
|---|---|
| **A** | One or more **IPv4** addresses |
| **AAAA** | **IPv6** addresses |
| **CNAME** | Another **domain name** |
| **MX** | The **mail server** for the domain |

### WHOIS
- Registering a domain requires accurate **contact information**, which is stored in a **public WHOIS record**.
- **Privacy services** can hide this information.

### HTTP(S): accessing the web
Relies on **TCP**, on ports **80** (HTTP) and **443** (HTTPS).

| Method | Purpose |
|---|---|
| **GET** | Retrieve data (HTML file, image) |
| **POST** | Submit new data (form, upload) |
| **PUT** | Create a new resource or update/overwrite existing data |
| **DELETE** | Delete a file or resource |

### FTP: transferring files
- Designed specifically for **file transfer**, often faster than HTTP for it
- Control connection on **TCP port 21**; file data goes over a **separate connection**

| Command | Purpose |
|---|---|
| `USER` | Username |
| `PASS` | Password |
| `RETR` | **Download** a file from the server |
| `STOR` | **Upload** a file to the server |

### SMTP: sending email
Defines how a mail client talks to a mail server, and how servers talk to each other. **TCP port 25**.

| Command | Purpose |
|---|---|
| `HELO` / `EHLO` | Start the session |
| `MAIL FROM` | Sender's address |
| `RCPT TO` | Recipient's address |
| `DATA` | Start of the message content |
| `.` (alone on a line) | End of the message |

### POP3: receiving email
Lets a client **download** messages from a mail server. **TCP port 110**.
*Clients send email with SMTP and retrieve it with POP3 (or IMAP).*

| Command | Purpose |
|---|---|
| `USER <username>` | Identify the user |
| `PASS <password>` | Password |
| `STAT` | Number of messages and total size |
| `LIST` | List messages and sizes |
| `RETR <n>` | Retrieve message *n* |
| `DELE <n>` | Mark message *n* for deletion |
| `QUIT` | End session and apply changes |

### IMAP
Also retrieves email (**TCP port 143**), but keeps messages **synchronised on the server**, so they look the same on every device.

---

## 4. Networking Secure Protocols

> ⚠️ Telnet, HTTP, FTP, SMTP, POP3 and IMAP send everything, **passwords included**, in **cleartext**. Secure versions add encryption.

### TLS (Transport Layer Security)
A **cryptographic protocol** that secures communication between client and server over an insecure network. It sits on top of TCP, at the transport layer.

**How a server gets a certificate:**
1. The admin creates a **CSR** (Certificate Signing Request).
2. It's sent to a **CA** (Certificate Authority).
3. The CA verifies it and issues a **signed digital certificate**.
4. Clients check the signature using the **CA certificates installed** on their own system.

> A **self-signed certificate** can't prove the server's authenticity, because no trusted third party confirmed it.

### HTTP vs HTTPS

| HTTP (after DNS resolution) | HTTPS (after DNS resolution) |
|---|---|
| 1. TCP three-way handshake | 1. TCP three-way handshake |
| 2. Send HTTP requests (`GET / HTTP/1.1`) | 2. **Establish a TLS session** |
| | 3. Send HTTP requests (`GET / HTTP/1.1`) |

With TLS, **all packets are encrypted**, and their contents can't be read without the **private key**.

**Key point:** TLS added security **without changing** the layers below (TCP, IP) or above (HTTP). The same idea secures other protocols.

### Secure versions and ports

| Protocol | Port | Secure version | Port |
|---|---|---|---|
| HTTP | 80 | **HTTPS** | 443 |
| SMTP | 25 | **SMTPS** | 465 (and 587 with STARTTLS) |
| POP3 | 110 | **POP3S** | 995 |
| IMAP | 143 | **IMAPS** | 993 |
| Telnet | 23 | **SSH** | 22 |
| FTP | 21 | **SFTP** / **FTPS** | 22 / 990 |

### SSH (Secure Shell)
Benefits of **OpenSSH**:
- **Secure authentication:** passwords, **public keys** and **two-factor** authentication
- **Confidentiality:** end-to-end encryption against eavesdropping; warns about **new server keys** to help prevent **man-in-the-middle** attacks
- **Integrity:** cryptography also prevents tampering with traffic
- **Tunneling:** can route other protocols through SSH, giving a **VPN-like** connection
- **X11 forwarding:** run graphical apps from a remote Unix-like system

### SFTP vs FTPS

| | **SFTP** | **FTPS** |
|---|---|---|
| Stands for | SSH File Transfer Protocol | File Transfer Protocol Secure |
| Secured by | **SSH** | **TLS** |
| Port | **22** | **990** (usually) |
| Setup | Easy: just enable it in the OpenSSH server | Needs a proper **TLS certificate** |

### VPN
Connects different sites securely over the internet, e.g. letting users at a **remote office** access resources at the **main branch**.

---

## 5. Wireshark: The Basics

A graphical **packet analyser** for live traffic and saved captures (**PCAP** files).

**Used for:**
- **Troubleshooting:** network load, failure points, congestion
- **Security:** detecting **rogue hosts**, **abnormal port usage** and **suspicious traffic**
- **Learning:** inspecting protocol details such as response codes and payloads

---

## 6. Tcpdump: The Basics

A **command-line** packet capture tool.

### Capture and files

| Command | Purpose |
|---|---|
| `tcpdump -i INTERFACE` | Capture on a specific interface (e.g. `eth0`) |
| `tcpdump -w FILE` | **Write** captured packets to a file |
| `tcpdump -r FILE` | **Read** packets from a file |
| `tcpdump -c COUNT` | Capture a set number of packets |

### Filters

| Command | Filters by |
|---|---|
| `tcpdump host IP` / `host HOSTNAME` | IP address or hostname |
| `tcpdump src host IP` | Source host |
| `tcpdump dst host IP` | Destination host |
| `tcpdump port PORT` | Port number |
| `tcpdump src port PORT` | Source port |
| `tcpdump dst port PORT` | Destination port |
| `tcpdump PROTOCOL` | Protocol (`ip`, `ip6`, `icmp`…) |

### Display options

| Option | Effect |
|---|---|
| `-n` | Don't resolve IP addresses to names |
| `-nn` | Don't resolve IPs **or** port/protocol numbers |
| `-v`, `-vv`, `-vvv` | More verbose output |
| `-q` | Quick, brief output |
| `-e` | Include **MAC addresses** |
| `-A` | Show packets as **ASCII** |
| `-xx` | Show packets in **hex** |
| `-X` | Show **hex + ASCII** |

**Example:** capture 50 HTTP packets on `eth0`, without name resolution, into a file:
```bash
sudo tcpdump -i eth0 -nn port 80 -c 50 -w http.pcap
```

---

## 🔌 Port Cheat Sheet

| Port | Protocol | Transport | Encrypted? |
|---|---|---|---|
| 21 | FTP | TCP | ❌ |
| 22 | SSH / SFTP | TCP | ✅ |
| 23 | Telnet | TCP | ❌ |
| 25 | SMTP | TCP | ❌ |
| 53 | DNS | UDP / TCP | ❌ |
| 80 | HTTP | TCP | ❌ |
| 110 | POP3 | TCP | ❌ |
| 143 | IMAP | TCP | ❌ |
| 443 | HTTPS | TCP | ✅ |
| 465 / 587 | SMTPS | TCP | ✅ |
| 990 | FTPS | TCP | ✅ |
| 993 | IMAPS | TCP | ✅ |
| 995 | POP3S | TCP | ✅ |

---

## 🔐 Key Takeaways for Cybersecurity

- **Cleartext protocols** (Telnet, FTP, HTTP, POP3, IMAP, SMTP) expose credentials to anyone sniffing the network. Seeing them in a capture is a finding.
- **ARP** has no authentication, which enables **ARP spoofing** (man-in-the-middle on a LAN).
- A rogue **DHCP** server can hand out a malicious gateway or DNS server.
- `ping` and `traceroute` are basic **reconnaissance** tools, and many networks block ICMP for that reason.
- **WHOIS** and **DNS** records are free **OSINT** (open-source intelligence) about a target.
- **Self-signed certificates** and SSH **"new host key"** warnings can signal a man-in-the-middle attack.
- **Wireshark and tcpdump** are core tools for both attackers (sniffing) and defenders (incident response, threat hunting).
- Memorising the **common ports** makes Nmap scan results much faster to read.

---

*Platform: TryHackMe · Path: Pre Security · Module: Networking · November 2026*
