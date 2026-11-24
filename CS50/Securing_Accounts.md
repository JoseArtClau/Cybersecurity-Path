# 🔑 Lecture 0: Securing Accounts (CS50 Cybersecurity)

> My notes from **Lecture 0** of Harvard's **CS50's Introduction to Cybersecurity**: how accounts get attacked and how to defend them.

## 📑 Contents
1. [Authentication vs Authorization](#1-authentication-vs-authorization)
2. [Password Attacks](#2-password-attacks)
3. [Usability vs Security](#3-usability-vs-security)
4. [Other Attacks on Accounts](#4-other-attacks-on-accounts)
5. [Defenses](#5-defenses)
6. [Key Takeaways](#-key-takeaways)

---

## 1. Authentication vs Authorization

| Concept | Question it answers | Example |
|---|---|---|
| **Authentication** | *Who are you?* Proving your identity | Logging in with a password |
| **Authorization** | *What are you allowed to do?* Whether you should have access to specific functions | A normal user can't open the admin panel |

---

## 2. Password Attacks

### Dictionary attack
The attacker uses a **file containing many common words and passwords** and tries each one, hoping one matches yours.

### Brute-force attack
The attacker uses software to try **every possible combination** until one works.

### Brute-force in Python
These loops generate every possible 4-character combination, which is what a brute-force tool would try one by one:

**4-digit PIN**
```python
from string import digits

for i in digits:
    for j in digits:
        for k in digits:
            for l in digits:
                print(i, j, k, l)
```

**4 letters**
```python
from string import ascii_letters

for i in ascii_letters:
    for j in ascii_letters:
        for k in ascii_letters:
            for l in ascii_letters:
                print(i, j, k, l)
```

**4 characters (letters + digits + symbols)**
```python
from string import ascii_letters, digits, punctuation

characters = ascii_letters + digits + punctuation

for i in characters:
    for j in characters:
        for k in characters:
            for l in characters:
                print(i, j, k, l)
```

### Why length and variety matter

| Password type | Possible characters | Combinations (4 characters) |
|---|---|---|
| Digits only | 10 | 10⁴ = **10,000** |
| Letters (upper + lower) | 52 | 52⁴ = **7,311,616** |
| Letters + digits + symbols | 94 | 94⁴ = **78,074,896** |

Each extra character **multiplies** the total by the number of possible characters, so **length** is one of the strongest defenses. A computer can still try 78 million combinations quickly, so 4 characters is never enough.

---

## 3. Usability vs Security

There's always a **trade-off**: more security usually means less convenience, and the other way round.

**Example: "iPhone is disabled"**
After too many wrong passcodes, the iPhone locks for longer and longer periods. That doesn't make the passcode harder to guess, but it **increases the attacker's cost** (time) and **slows down** brute-force attacks, at the price of occasionally annoying the real owner.

---

## 4. Other Attacks on Accounts

| Attack | How it works |
|---|---|
| **SIM swapping** | The attacker tricks the phone carrier into moving **your number to their SIM card**, so your **SMS codes go to the attacker** |
| **Keylogging** | Malware on your device **records everything you type** (passwords included) and sends it to a server the attacker controls |
| **Credential stuffing** | The attacker takes **usernames and passwords leaked** from one site and tries them on **other sites**, betting that you reuse passwords |
| **Social engineering** | A **social** rather than technical attack: the attacker creates a situation that pushes you into an action that compromises your security |
| **Phishing** | A form of social engineering that tries to **"hook"** you: emails or messages urging you to click a link, make a donation or confirm credentials on a fake site |
| **Machine-in-the-middle (MITM)** | Many machines (routers, services) sit between you and a website. If one is malicious and the connection isn't secure, it can **read or change** your traffic |

---

## 5. Defenses

### Multi-factor authentication (MFA)
Requires **two or more different factors**, so a stolen password alone isn't enough:
- something you **know** (password)
- something you **have** (phone, authenticator app, security key)
- something you **are** (fingerprint, face)

> ⚠️ **SMS codes** are better than nothing but can be stolen through **SIM swapping**. Authenticator apps and security keys are stronger.

### Single Sign-On (SSO)
- Logging into a website **with an account you already have** elsewhere ("Log in with Google")
- **Why:** less friction and better usability, and the site benefits from Google's strong protections (MFA, suspicious-login detection)
- **Your password is never given to the website.** It only receives confirmation that you logged in successfully with Google.
- ⚠️ Trade-off: if that main account is compromised, every site linked to it is at risk.

### Password managers
- Software that **creates, stores and fills in** your passwords, so you can use a **unique, strong password for every site** (defeats credential stuffing)
- Remembers **which URL** each password belongs to, so on a **look-alike phishing site** with a different URL it **won't fill in** your credentials
- ⚠️ Trade-off: protect the **master password** (and turn on MFA for it), because it unlocks everything.

### Passkeys
- A replacement for passwords. For each website, your device generates a **key pair**:
  - a **private key** that **stays on your device** and is never shared
  - a **public key** that is **given to the website**
- To log in, the website sends a challenge, and your device proves it has the private key (usually after a fingerprint, face or PIN), **without ever sending it**.
- Synchronised across your devices (e.g. via iCloud Keychain or Google Password Manager)
- ✅ Nothing secret is stored on the server, and passkeys are tied to the real website, so they **resist phishing and credential stuffing**.

### Which defense stops which attack?

| Attack | Main defenses |
|---|---|
| Dictionary / brute-force | Long passwords, rate limiting/lockouts, MFA |
| Credential stuffing | Unique passwords (password manager), MFA |
| Phishing | Password manager (URL check), passkeys, awareness |
| SIM swapping | Authenticator app or security key instead of SMS |
| Keylogging | Keeping devices clean of malware, MFA, passkeys |
| MITM | Encrypted connections (HTTPS) |

---

## 🔐 Key Takeaways

- **Authentication ≠ authorization:** proving who you are vs what you're allowed to do.
- **Length beats complexity:** each extra character multiplies the work for an attacker.
- **Never reuse passwords:** credential stuffing relies on it.
- **Turn on MFA everywhere**, preferring authenticator apps or security keys over SMS.
- Security always comes with a **usability trade-off**; good defenses raise the attacker's cost.
- **Passkeys** are where account security is heading: no shared secret to steal.

---

*Course: CS50's Introduction to Cybersecurity (Harvard) · Lecture 0 · November 2026*
