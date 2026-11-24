# 🕸️ How The Web Works (TryHackMe, Pre Security Path)

> My notes from the **How The Web Works** module of TryHackMe's Pre Security path: DNS, HTTP, how websites are built, and every component involved when you open a web page.

## 📑 Contents
1. [DNS in Detail](#1-dns-in-detail)
2. [HTTP in Detail](#2-http-in-detail)
3. [How Websites Work](#3-how-websites-work)
4. [Putting It All Together](#4-putting-it-all-together)
5. [Key Takeaways for Cybersecurity](#-key-takeaways-for-cybersecurity)

---

## 1. DNS in Detail

**DNS (Domain Name System)** turns human-friendly names like `tryhackme.com` into the IP addresses computers use.

### Domain hierarchy

```
        admin . tryhackme . com
          │         │        │
     Subdomain  Second-level  Top-level
                  domain      domain (TLD)
```

| Part | Description | Examples |
|---|---|---|
| **TLD** (Top-Level Domain) | The rightmost part of a domain. Two kinds: | |
| ↳ **gTLD** (generic) | Originally meant to show the domain's purpose | `.com` (commercial), `.edu`, `.gov`, `.org` |
| ↳ **ccTLD** (country code) | Used for geographical purposes | `.br`, `.uk`, `.fr` |
| **Second-level domain** | The name you register | `tryhackme` in `tryhackme.com` |
| **Subdomain** | Sits to the **left** of the second-level domain | `admin` in `admin.tryhackme.com` |

### DNS record types

| Record | Resolves to | Example use |
|---|---|---|
| **A** | An **IPv4** address | `tryhackme.com → 104.26.10.229` |
| **AAAA** | An **IPv6** address | `tryhackme.com → 2606:4700:20::681a:be5` |
| **CNAME** | **Another domain name** | `store.tryhackme.com → shops.shopify.com` |
| **MX** | The **mail servers** for the domain, each with a **priority** value | Sends email to a backup server if the main one is down |
| **TXT** | **Free text**: any text-based data | Proving domain ownership, email anti-spam settings (SPF, DMARC) |

### What happens when you request a domain
1. Your computer checks its **local cache** for a recent lookup.
2. If it's not there, it asks your **recursive DNS server** (usually provided by your ISP), which also has a cache.
3. If needed, the recursive server asks a **root server**, which points to the right **TLD server** (e.g. for `.com`).
4. The TLD server points to the domain's **authoritative DNS server**.
5. The authoritative server returns the **IP address**, which is cached along the way for next time.

---

## 2. HTTP in Detail

- **HTTP (HyperText Transfer Protocol):** the set of rules for communicating with web servers to transmit web page data.
- **HTTPS:** the secure version. Data is **encrypted**, so others can't read what you send and receive. It also assures you that you're talking to the **real web server** and not an impersonator.

### HTTP methods

| Method | Used for |
|---|---|
| **GET** | Getting information from a web server |
| **POST** | Submitting data and possibly creating new records |
| **PUT** | Submitting data to **update** existing information |
| **DELETE** | Deleting information or records |

### Status code ranges

| Range | Category | Meaning |
|---|---|---|
| **1xx** | Information | First part of the request accepted, keep sending (rarely seen today) |
| **2xx** | Success | The request worked |
| **3xx** | Redirection | Go to another page or website |
| **4xx** | Client error | Something was wrong with **your** request |
| **5xx** | Server error | A problem on the **server** side, usually serious |

### Common status codes

| Code | Name | Meaning |
|---|---|---|
| **200** | OK | Request completed successfully |
| **201** | Created | A resource was created (new user, new blog post) |
| **301** | Moved Permanently | Page moved for good; browsers and search engines should use the new location |
| **302** | Found | Temporary redirect, may change again |
| **400** | Bad Request | Something wrong or missing in the request (e.g. a required parameter) |
| **401** | Unauthorized | You must log in (authenticate) first |
| **403** | Forbidden | No permission, whether logged in or not |
| **404** | Not Found | The page/resource doesn't exist |
| **405** | Method Not Allowed | Wrong method, e.g. GET to `/create-account` when it expects POST |
| **500** | Internal Server Error | The server hit an error it doesn't know how to handle |
| **503** | Service Unavailable | Server overloaded or down for maintenance |

### Headers
Headers are **extra pieces of data** sent with requests and responses, such as which website you want (`Host`), your browser type (`User-Agent`) or cookies.

---

## 3. How Websites Work

When you visit a website, your browser asks a **web server** for information about the page. A web server is just a dedicated computer somewhere in the world that handles those requests. It replies with data the browser uses to build the page you see.

*Notes on HTML, JavaScript and sensitive data exposure in progress.*

---

## 4. Putting It All Together

### Quick summary
To open a website, your computer first needs the server's **IP address**, which it gets from **DNS**. It then talks to the web server using **HTTP**, and the server returns **HTML, JavaScript, CSS, images**, etc. that the browser uses to display the site.

### Load balancers
Used when traffic is too heavy for one server, or when the site needs **high availability**.

Two main jobs:
1. Make sure high-traffic websites can **handle the load**
2. Provide **failover** if a server becomes unresponsive

The load balancer receives your request first and forwards it to one of several servers behind it, chosen by an algorithm:

| Algorithm | How it picks a server |
|---|---|
| **Round robin** | Each server in turn |
| **Weighted** | Checks how many requests each server is handling and picks the **least busy** one |

It also runs periodic **health checks**. If a server doesn't respond, or responds incorrectly, it gets no traffic until it's healthy again.

### CDN (Content Delivery Network)
- Cuts down traffic to busy websites
- Hosts a site's **static files** on thousands of servers around the world
- Sends each user to the **physically nearest** server, making pages load faster

### Databases
- Store and recall information for the website's users; the web server talks to them
- Range from a simple text file to complex clusters of servers built for speed and resilience

### WAF (Web Application Firewall)
Sits **between your request and the web server** to protect it from hacking and denial-of-service attacks.
- Analyses requests for **common attack techniques**
- Checks whether the request comes from a real browser or a **bot**
- Uses **rate limiting**: allows only a certain number of requests per IP per second
- Requests flagged as attacks are **dropped** and never reach the server

### Web servers
- Software that **listens for incoming connections** and uses HTTP to deliver web content
- Serves files from its **root directory**, set in its configuration
- Examples: Apache, Nginx, IIS, Node.js

### Virtual hosts
Let **one web server host many websites** with different domain names.
1. The server reads the requested **hostname** from the HTTP headers.
2. It matches it against its virtual hosts (simple text configuration files).
3. Match → serves that website. No match → serves the **default** website.

- Each virtual host can have its own root directory.
- There's no limit to how many websites one server can host.

### Static vs dynamic content

| | Static | Dynamic |
|---|---|---|
| Changes? | Never; served exactly as stored | Can change with each request |
| Examples | Pictures, CSS, JavaScript files | A blog, search results, a logged-in dashboard |

### 🧭 The full journey of a request

```
 1. You type tryhackme.com in the browser
 2. Check local cache for the IP address
 3. Ask your recursive DNS server
 4. Query a root server → find the authoritative DNS server
 5. Authoritative DNS server returns the IP address
 6. Request passes through a Web Application Firewall (WAF)
 7. Request passes through a load balancer
 8. Connect to the web server on port 80 (HTTP) or 443 (HTTPS)
 9. Web server receives the GET request
10. Web application talks to the database
11. Your browser renders the HTML into a viewable website
```

---

## 🔐 Key Takeaways for Cybersecurity

- **Subdomain enumeration** (finding `admin.`, `dev.`, `test.`…) is a key reconnaissance step, since forgotten subdomains are often weak points.
- **DNS records** (MX, TXT) reveal a lot about a target's infrastructure and email setup.
- **Status codes** guide attackers and testers: `403` means something exists but is protected, `500` can point to a bug worth investigating.
- **HTTP methods** matter: a server accepting `PUT` or `DELETE` where it shouldn't is a vulnerability.
- **Virtual hosts** mean one IP can hide many websites.
- **WAFs and rate limiting** are defences that attackers try to bypass (e.g. brute-force protection).
- **HTTPS** protects data in transit, but not a vulnerable application.

---

*Platform: TryHackMe · Path: Pre Security · Module: How The Web Works · November 2026*
