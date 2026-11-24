# 🌐 Network Fundamentals (TryHackMe, Pre Security Path)

> My notes from the **Network Fundamentals** module of TryHackMe's Pre Security path: how computers talk to each other, the OSI and TCP/IP models, TCP vs UDP, and the devices that build and protect networks.

## 📑 Contents
1. [What is Networking?](#1-what-is-networking)
2. [Intro to LAN](#2-intro-to-lan)
3. [OSI Model](#3-osi-model)
4. [Packets & Frames](#4-packets--frames)
5. [Extending Your Network](#5-extending-your-network)
6. [Key Takeaways for Cybersecurity](#-key-takeaways-for-cybersecurity)

---

### 1. OSI Model

The OSI model splits network communication into **7 layers**. Each layer has one job and hands its result to the next.

| # | Layer | Job | Examples |
|---|---|---|---|
| 7 | **Application** | Rules for how the user interacts with sent/received data | HTTP, DNS, SMTP |
| 6 | **Presentation** | Translation, standardisation, encryption | TLS/SSL, JPEG, ASCII |
| 5 | **Session** | Opens, maintains and closes connections | Sessions, checkpoints |
| 4 | **Transport** | Delivery between applications | **TCP**, **UDP** |
| 3 | **Network** | Routing between networks using IP addresses | IP, routers |
| 2 | **Data Link** | Delivery inside a network using MAC addresses | Ethernet, switches |
| 1 | **Physical** | The actual signals and cables | Cables, Wi-Fi radio |

> 💡 Mnemonic (7 → 1): **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

### Layer 7: Application
Where the protocols and rules live that decide how the user interacts with data sent or received.

### Layer 6: Presentation
- Where **standardisation** starts
- Acts as a **translator** for data going to and from the application layer, so the receiving computer understands data even if it was sent in a different format
- **Encryption** happens here

### Layer 5: Session
- Once data is formatted by the presentation layer, the session layer **creates and maintains the connection** to the destination computer. An established connection = a **session**.
- **Closes** the connection if it is idle for too long or is lost
- Can use **checkpoints**: if data is lost, only the data after the last checkpoint needs to be re-sent
- Sessions are **unique**: data from one session doesn't mix with another

### Layer 4: Transport
Covered in detail in [TCP vs UDP](#tcp-transmission-control-protocol) below.

---

## 2. Packets & Frames

Packets and frames are **small pieces of data** that together form a larger message.

| | Layer | Contains |
|---|---|---|
| **Packet** | 3 (Network) | IP header + payload |
| **Frame** | 2 (Data Link) | The packet + extra info such as **MAC addresses** |

### Encapsulation
As data moves down the layers, each layer **adds its own header**. This is **encapsulation**. The receiver removes them in reverse order: **decapsulation**.

### TCP/IP model (4 layers)

| TCP/IP layer | Matching OSI layers |
|---|---|
| Application | 7, 6, 5 |
| Transport | 4 |
| Internet | 3 |
| Network Interface | 2, 1 |

### Key IP header fields

| Field | Purpose |
|---|---|
| **Time To Live (TTL)** | Expiry timer so a packet that never reaches its host doesn't clog the network forever |
| **Checksum** | Integrity check: if any data changed, the value won't match |
| **Source address** | IP of the sender, so replies know where to go |
| **Destination address** | IP of the receiver, so the packet knows where to travel |

---

### TCP (Transmission Control Protocol)

TCP is **connection-based**: a connection between client and server must be established **before** any data is sent. That's done with the **three-way handshake**. After that, acknowledgements and retransmissions guarantee the data arrives complete and in order.

| ✅ Advantages | ❌ Disadvantages |
|---|---|
| Guarantees data integrity | Needs a reliable connection: missing data must be re-sent |
| Synchronises both devices so data isn't flooded or out of order | A slow device can bottleneck the other, since the connection stays reserved |
| Many extra processes for reliability | Significantly slower than UDP |

#### TCP header fields

| Field | Meaning |
|---|---|
| **Source port** | Port opened by the sender, chosen randomly from unused ports |
| **Destination port** | Port of the application/service on the remote host |
| **Source IP** | IP of the sending device |
| **Destination IP** | IP of the receiving device |
| **Sequence number** | Starts at a random number and counts the data sent, so it can be put back in order |
| **Acknowledgement number** | The next sequence number the receiver expects, confirming what has arrived so far |
| **Checksum** | Integrity check: a calculation is repeated on arrival, and a different result means corrupted data |
| **Data** | The actual content |
| **Flags** | Tell each device how to handle the packet (SYN, ACK, FIN, RST…) |

#### TCP messages / flags

| Message | Meaning |
|---|---|
| **SYN** | Client's first packet: starts the connection and synchronises the devices |
| **SYN/ACK** | Server acknowledges the synchronisation attempt |
| **ACK** | Either side confirms that packets were received |
| **DATA** | Carries data once the connection is up |
| **FIN** | Cleanly closes the connection when finished |
| **RST** | Abruptly ends all communication. Last resort, signals a problem |

#### Three-way handshake
```
Client                      Server
  | ------- SYN --------->    |
  | <----- SYN/ACK -------    |
  | ------- ACK --------->    |
  |   connection established  |
  | <====== DATA =======>     |
```

#### Closing a connection
When a device has confirmed the other side received all data, it sends a **FIN**. The other side acknowledges it and closes its side too.

---

### UDP (User Datagram Protocol)

- **Stateless:** no handshake and no constant connection needed
- No guarantee of delivery or order, but much **faster** and lighter than TCP
- Good for small pieces of data and for real-time traffic like **video streaming**, voice calls, online games and DNS

Fields involved in a UDP packet (as listed in the room): TTL, source address, destination address, source port, destination port, data.

| | TCP | UDP |
|---|---|---|
| Connection | Handshake first | None |
| Reliability | Guaranteed, ordered | Best effort |
| Speed | Slower | Faster |
| Typical use | Web, email, file transfer | Streaming, gaming, DNS |

---

## 3. Extending Your Network

### Port forwarding
- Without it, services like a web server are only reachable from devices on the **same local network**.
- Port forwarding **opens specific ports** to the outside. A **firewall** then decides whether traffic may pass through those ports.

### Firewall
A device that decides **what traffic may enter and leave** a network. Admins set rules based on source, destination, port, protocol and so on, and the firewall uses **packet inspection** to apply them.

Works at **layers 3 and 4** (network and transport).

| | **Stateful** | **Stateless** |
|---|---|---|
| Decides based on | The **whole connection** | **Each packet** against static rules |
| Resources | Heavy (dynamic decisions) | Light |
| Behaviour | A bad connection can get the **whole host blocked** | Simpler, "dumber" |
| Good for | Detailed inspection | Large volumes of traffic from known hosts |

### VPN (Virtual Private Network)
Lets devices on **separate networks** communicate securely through an encrypted **tunnel** over the Internet. Devices in the tunnel form their own private network.

**Benefits**
- Connects networks in different geographical locations (e.g. company offices)
- Privacy: traffic is encrypted
- Some anonymity: sites see the VPN's IP, not yours (the VPN provider can still see your traffic)

**VPN technologies**

| Technology | Notes |
|---|---|
| **PPP** | Provides authentication and encryption for PPTP using a private key + certificate, which must match to connect. Can't leave a network on its own |
| **PPTP** | Carries PPP data out of the network. Easy to set up and widely supported, but **weak encryption**; considered obsolete today |
| **IPSec** | Encrypts data within the existing IP framework. Harder to set up, but **strong encryption** and wide support |

### Router
- Connects **different networks** and passes data between them
- **Routing** = finding a path for data across networks
- Works at **layer 3**

### Switch
- Connects **many devices** in one network, usually with Ethernet cables

| | Layer 2 switch | Layer 3 switch |
|---|---|---|
| Forwards using | **MAC addresses** (frames) | MAC addresses **and** IP (packets) |
| Can route? | No | Yes, does some router tasks |

### VLAN (Virtual LAN)
Splits devices on the same physical network into **separate virtual networks**. They can share things like the internet connection but are treated separately, and rules control how each group may communicate. That **segmentation** is a security feature.

> Example: guest Wi-Fi, office PCs and servers on different VLANs, so a compromised guest device can't reach the servers.

---

## 🔐 Key Takeaways for Cybersecurity

- The **OSI model** is the shared language for describing where an attack or defence happens ("a layer 7 attack").
- The **three-way handshake** is the basis of port scanning (e.g. Nmap SYN scans) and of attacks like **SYN floods**.
- **TTL** values can hint at a target's operating system.
- **Checksums** detect accidental corruption, not deliberate tampering. That needs cryptography.
- **Port forwarding** exposes services to the internet, so every open port must be justified and protected.
- **Firewalls + VLAN segmentation** limit how far an attacker can move inside a network.
- **PPTP** is weak; prefer modern VPN protocols.

---

*Platform: TryHackMe · Path: Pre Security · Module: Network Fundamentals · November 2026*
