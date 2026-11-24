# 🛡️ Lecture 2: Securing Systems (CS50 Cybersecurity)

> My notes from **Lecture 2** of Harvard's **CS50's Introduction to Cybersecurity**: how network traffic gets intercepted, how HTTPS, VPNs and firewalls protect it, and the main types of malware.

## 📑 Contents
1. [Wi-Fi Protected Access](#1-wi-fi-protected-access)
2. [HTTP and Its Weaknesses](#2-http-and-its-weaknesses)
3. [HTTPS, TLS and Certificates](#3-https-tls-and-certificates)
4. [SSL Stripping and HSTS](#4-ssl-stripping-and-hsts)
5. [VPN and SSH](#5-vpn-and-ssh)
6. [Ports and Port Scanning](#6-ports-and-port-scanning)
7. [Firewalls and Proxies](#7-firewalls-and-proxies)
8. [Malware](#8-malware)
9. [Defenses Against Malware](#9-defenses-against-malware)
10. [Key Takeaways](#-key-takeaways)

---

## 1. Wi-Fi Protected Access

**WPA** (Wi-Fi Protected Access) encrypts the traffic between your device and the wireless access point, so people nearby can't simply read it out of the air. Current versions are **WPA2** and **WPA3**.

> ⚠️ This only protects the link between you and the router. Beyond the router, your traffic still needs its own protection, like HTTPS.

---

## 2. HTTP and Its Weaknesses

**HTTP** (HyperText Transfer Protocol) is the protocol computers use to communicate on the web. On its own it is **not encrypted**, which leaves you open to **machine-in-the-middle (MITM)** attacks.

**Example:** when you visit a website, you download its HTML. A machine in the middle could **inject extra HTML or code** into that page before it reaches you, such as ads, malicious scripts or fake login forms.

### Packet sniffing
- A **packet** is like a virtual envelope carrying data across the internet.
- **Packet sniffing** means looking inside those envelopes as they pass by.
- If the contents **aren't encrypted**, any machine in the middle can **read** them, **change** them and pass them along.

### Cookies and sessions
- HTTP is stateless, so websites use **cookies** to remember who you are.
- After you log in, the site gives you a cookie containing a **session** identifier.
- On every later request, your browser **presents that cookie** to prove you're the same person who logged in.

### Session hijacking
If an attacker sniffs your **unencrypted** HTTP traffic and finds your **session cookie** inside, they can send requests with **your cookie** and use the website **as you**, without ever knowing your password.

```
You ──[ GET /inbox  Cookie: session=abc123 ]──► 👀 Attacker copies cookie ──► Website
Attacker ──[ GET /inbox  Cookie: session=abc123 ]──► Website thinks it's you
```

---

## 3. HTTPS, TLS and Certificates

### HTTPS
HTTP + encryption. **Everything inside the envelope is encrypted**, so sniffers only see scrambled data, and cookies can't be stolen this way.

### TLS
**TLS** (Transport Layer Security) is the protocol that does the encrypting of HTTP traffic.

### Certificates
- A web server has a **certificate**: its **public key**, **digitally signed** by a trusted third party.
- **Certificate Authorities (CAs)** are companies and organisations whose job is to sign certificates.
- Browser makers ship their browsers with a **list of CAs they trust**.

### How your browser checks a certificate
1. Download the website's certificate (over HTTPS).
2. Calculate a **hash** of the certificate.
3. Use the **CA's public key** on the certificate's **signature**, which should produce a hash.
4. **Compare:** if both hashes match, the certificate really was signed by that CA, and the site is who it claims to be. ✅

*(Same idea as digital signatures from [Lecture 1](lecture-01.md).)*

---

## 4. SSL Stripping and HSTS

### SSL stripping
Tricks you into thinking you have a secure connection when you **don't**, or when your encrypted connection is actually to the **attacker's** server.

**How:**
1. You type `example.com` or `http://example.com` (no `https://`).
2. Your first request goes out over **plain HTTP**, which gives the attacker a **window of opportunity**.
3. A machine in the middle answers with a redirect to **its own site** instead of the real HTTPS one.
4. You're now on the attacker's site, exposed to things like **phishing**.

### HSTS (HTTP Strict Transport Security)
- The **defense** against SSL stripping.
- The server tells browsers: **"always use HTTPS with me"**, whatever the user typed.
- After that, the browser never makes an insecure HTTP request to that site, so the window of opportunity disappears.

---

## 5. VPN and SSH

### VPN (Virtual Private Network)
- **Encrypts all your internet traffic** between you and the VPN server.
- Lets Alice and Bob set up an **encrypted channel**, so machines in the middle don't matter: they only see ciphertext.
- ⚠️ The VPN provider itself can still see your traffic once it leaves the tunnel.

### SSH (Secure Shell)
- An **encrypted channel** for logging into and controlling another computer remotely.

---

## 6. Ports and Port Scanning

### Ports
A **port number** is an agreed-upon number that identifies **which service** a packet is destined for.

| Port | Service |
|---|---|
| **22** | SSH |
| **80** | HTTP |
| **443** | HTTPS |

### Port scanning
Checking a computer's ports to see **which ones are open**, which tells an attacker which services are running and might be vulnerable. Defenders scan their own systems for the same reason. (Tool example: **Nmap**.)

---

## 7. Firewalls and Proxies

### Firewall
Software (or a hardware device) between your network and the outside world. It:
- keeps **out** traffic you don't want coming in
- keeps **in** data you don't want leaving

**How it decides:**

| Method | What it checks |
|---|---|
| **IP address** | Block traffic from or to specific addresses |
| **Port number** | Block specific services (e.g. everything except 80 and 443) |
| **Deep packet inspection** | Looks **inside** packets: can block websites by **domain name**, or see who is emailing whom |

### Proxy
- A server that **sits between two points**, taking data from one side and delivering it to the other
- Can **inspect each packet** and decide whether to let it through (e.g. filtering websites at a company or school)
- Technically, it's a **"friendly" machine in the middle**: the same position an attacker would want

---

## 8. Malware

**Malware** = software written to do **malicious things**.

| Type | How it spreads / works |
|---|---|
| **Virus** | Attaches itself to a **host** (a program or file) and needs a **human action** to spread, like opening an infected file |
| **Worm** | Spreads **on its own**, with no human interaction: it scans for other computers (IP/port scanning), finds **vulnerable listening services** and copies itself through them |
| **Botnet** | Many infected computers ("bots") running hidden software that **listens for commands**. The attacker can control **all of them at once** |

### DoS and DDoS
| Attack | Description |
|---|---|
| **DoS** (Denial of Service) | Overwhelms a server so it **can't serve** legitimate users |
| **DDoS** (Distributed DoS) | The same, but from **many machines at once** (often a botnet). The flood of requests means **legitimate ones can't get through**, and it's hard to block because traffic comes from everywhere |

---

## 9. Defenses Against Malware

### Antivirus
Software on your computer that **scans for viruses, worms and other malware**.

### Automatic updates
- Antivirus software must be **constantly updated** to recognise new threats.
- Your **operating system and apps** need updates too, to fix the vulnerabilities that worms exploit.

### Zero-day attacks
- A **zero-day** is a vulnerability (or the malware exploiting it) that the vendor **doesn't know about yet**. They've had "zero days" to fix it.
- Attackers can release new malware into the wild, and it can take **days or weeks** for vendors to ship patches and antivirus updates. During that window, even fully updated systems are at risk.

---

## 🔐 Key Takeaways

- **Plain HTTP is readable and changeable** by anyone in the middle. Always use **HTTPS**.
- **Session cookies are as valuable as passwords**: stealing one means logging in as you.
- **Certificates + CAs** are how your browser knows a site is real.
- **HSTS** closes the gap that **SSL stripping** exploits.
- **VPNs** protect you from the local network, but you're trusting the VPN provider instead.
- **Port scanning** is step one of attacking, and of defending.
- **Firewalls** filter by IP, port, or packet contents.
- **Viruses need you; worms don't.** Botnets turn infected machines into weapons for **DDoS**.
- **Update everything automatically**, but remember zero-days exist, so defense in depth matters.

---

*Course: CS50's Introduction to Cybersecurity (Harvard) · Lecture 2 · November 2026*
