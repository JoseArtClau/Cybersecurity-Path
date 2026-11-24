# 🗄️ Lecture 1: Securing Data (CS50 Cybersecurity)

> My notes from **Lecture 1** of Harvard's **CS50's Introduction to Cybersecurity**: how passwords are stored safely, how cryptography protects data in transit and at rest, and what "deleting" really means.

## 📑 Contents
1. [Hashing Passwords](#1-hashing-passwords)
2. [Cryptography Basics](#2-cryptography-basics)
3. [Secret-Key (Symmetric) Cryptography](#3-secret-key-symmetric-cryptography)
4. [Public-Key (Asymmetric) Cryptography](#4-public-key-asymmetric-cryptography)
5. [Digital Signatures](#5-digital-signatures)
6. [Passkeys (WebAuthn)](#6-passkeys-webauthn)
7. [Encryption in Transit & End-to-End Encryption](#7-encryption-in-transit--end-to-end-encryption)
8. [Deletion & Encryption at Rest](#8-deletion--encryption-at-rest)
9. [Quantum Computing](#9-quantum-computing)
10. [Key Takeaways](#-key-takeaways)

---

## 1. Hashing Passwords

### Hashing
- Websites shouldn't store your password itself. They run it through a **hash function** and store the **hash** in the database.
- At login, they hash what you typed and compare the hashes.
- If the database leaks, attackers get hashes instead of passwords, which **increases the attacker's cost**.

### One-way hash functions
- Take an input of **any length** and produce an output of **fixed length**.
- Designed to be **one-way**: easy to compute, but you can't reliably reverse it to recover the password. Since infinitely many inputs map to a limited number of outputs, more than one input can even share the same hash.

```
"password123"  ──hash──►  ef92b778bafe771e...   (always the same length)
```

### Rainbow tables
- A **precomputed table** of passwords and their hashes. An attacker with stolen hashes just **looks them up** instead of computing them.
- Downside for the attacker: the tables take a **huge amount of storage**.

### Salting
- **Problem:** two employees with the same password have the **same hash** in the database. Crack one and you've cracked both, and rainbow tables work against both.
- **Solution:** add a random value, the **salt**, to each password before hashing. The salt is stored alongside the hash.
- Same password + different salt = **different hash**. This also makes **rainbow tables useless**, since an attacker would need a separate table for every salt.

```
hash("password123" + "x7Q2")  →  a91c...
hash("password123" + "Lm9p")  →  0fd4...   ← same password, different hashes
```

---

## 2. Cryptography Basics

### Codes vs ciphers

| | **Codes** | **Ciphers** |
|---|---|---|
| Works on | Whole words or phrases | Individual **letters or bits** |
| Example | "The eagle has landed" meaning something else | Shifting every letter by 3 |
| Steps | **Encode**: plaintext → codetext · **Decode**: codetext → plaintext | **Encipher/Encrypt**: plaintext → ciphertext · **Decipher/Decrypt**: ciphertext → plaintext |

Ciphers break the problem into **small steps** applied to each letter or bit, which is why computers use them.

### Keys
A **key** is the secret value used to **encrypt or decrypt** a message.

---

## 3. Secret-Key (Symmetric) Cryptography

- **The same key** encrypts and decrypts.
- Sender and receiver must **both have the same key**.
- Security depends entirely on **keeping the key secret**.

```
plaintext ──[ key ]──► ciphertext ──[ same key ]──► plaintext
```

| Algorithm | Notes |
|---|---|
| **Caesar cipher** | Classic example: shift each letter by *n* (the key). Easy to break |
| **AES** | Modern standard, widely used today |
| **Triple DES** | Older; being phased out |

**Problem:** how do two people agree on a secret key without anyone else learning it? → **Key exchange** (below).

---

## 4. Public-Key (Asymmetric) Cryptography

Each person has a **key pair**:
- a **public key**, which can be shared with anyone
- a **private key**, which is kept secret

A message encrypted with your **public key** can only be decrypted with your **private key**.

```
Bob ──encrypts with Alice's PUBLIC key──► ciphertext ──Alice decrypts with her PRIVATE key──► plaintext
```

**Algorithms:** RSA, Diffie-Hellman, MQV

### Key exchange
Using math, two people can **arrive at the same secret key** without ever sending the key itself over the network.
**Example:** Diffie-Hellman. In practice, this is often used to agree on a symmetric key (e.g. for AES), which is faster for large amounts of data.

| | Symmetric | Asymmetric |
|---|---|---|
| Keys | One shared secret key | Public + private key pair |
| Speed | Fast | Slower |
| Main challenge | Sharing the key safely | Managing and trusting public keys |
| Examples | AES, Triple DES | RSA, Diffie-Hellman |

---

## 5. Digital Signatures

Prove **who sent a message** and that **it wasn't changed**.
**Algorithms:** DSA, ECDSA, RSA

### Sign (sender)
1. Run the message through a **hash function** to get a fixed-length hash.
2. Process that hash with your **private key**. The output is the **signature**.
3. Send the message and the signature.

### Verify (recipient)
1. Hash the received message with the **same hash function**.
2. Use the sender's **public key** on the signature, which should give back the sender's hash.
3. **Compare:** if the two hashes match, the signature is valid. The message came from the owner of the private key and wasn't altered.

```
Sender:    message ─hash─► H ─private key─► signature
Recipient: message ─hash─► H'
           signature ─public key─► H
           H == H' ?  ✅ valid  /  ❌ forged or tampered
```

---

## 6. Passkeys (WebAuthn)

Passkeys **replace passwords** using public-key cryptography and digital signatures.

**Registration**
1. Your device creates a **new key pair just for that website**.
2. It sends the **public key** (with your username) to the website.
3. The **private key stays on your device**.

**Login**
1. The website sends a **challenge** (a random piece of data).
2. Your device **signs the challenge** with the private key.
3. The website **verifies the signature** with your public key. If it checks out, you're in.

✅ No password to steal, phish or reuse. The website only stores a public key, which is useless to attackers.
⚠️ Don't lose the device holding the keys. Nowadays cloud services **synchronise and back up** passkeys across devices.

---

## 7. Encryption in Transit & End-to-End Encryption

### Encryption in transit
Data is encrypted while travelling **from point A to point B**, so nobody in between can read it.

But: if **Alice** has a secure connection to **Gmail** and **Bob** also has one, that **doesn't** mean Alice has a secure connection **to Bob**. Gmail itself can still read the messages.

```
Alice ══🔒══► Gmail (can read) ══🔒══► Bob
```

### End-to-end encryption (E2EE)
Only the **sender and the recipient** can read the data. Not even the service in the middle can.

```
Alice ══🔒═══════════════════════🔒══► Bob
            (server only sees ciphertext)
```

Examples: Signal, WhatsApp messages.

---

## 8. Deletion & Encryption at Rest

### What "delete" really does
- Data lives on a hard drive, SSD or USB stick.
- When you delete a file, the computer just **forgets where the file is** and marks that space as available. The 0s and 1s are **still there** until something overwrites them, so deleted files can often be **recovered**.

### Secure deletion
- Overwrites the file's 0s and 1s with **other patterns** (e.g. all zeros or random data).
- ⚠️ This is harder on **SSDs**, which move data around internally, so old copies may remain.

### Full disk encryption (encryption at rest)
- **Everything** on the drive is encrypted, and data is only decrypted after you log in with your password.
- If the device is lost or stolen, the data is unreadable. It also makes deletion simpler: without the key, the remaining data is useless.
- Examples: **BitLocker** (Windows), **FileVault** (macOS).
- ⚠️ **Downside:** forget your password or lose the recovery key, and your data is gone for good.

### Ransomware
Attackers use encryption **against you**: they encrypt all the data on a system they've accessed and **demand payment** for the decryption key.
**Defense:** regular, offline **backups**.

---

## 9. Quantum Computing

- A classical **bit** is either 0 or 1.
- A **qubit** can represent **0 and 1 at the same time** (superposition).
- Powerful enough quantum computers could break today's **public-key algorithms** like RSA and Diffie-Hellman.
- That's why researchers are developing **post-quantum cryptography**.

---

## 🔐 Key Takeaways

- **Never store passwords in plaintext.** Hash them, with a unique **salt** for each.
- **Hashing ≠ encryption:** hashing is one-way, encryption is meant to be reversed with a key.
- **Symmetric** encryption is fast but needs a shared secret. **Asymmetric** encryption solves key sharing, and combining both is how HTTPS works.
- **Digital signatures** provide authenticity and integrity, and they're what makes **passkeys** work.
- **Encryption in transit ≠ end-to-end encryption:** ask who in the middle can read your data.
- **Deleted ≠ gone.** Use full disk encryption and secure deletion.
- Encryption protects data, but attackers can also weaponise it (**ransomware**), so keep backups.

---

*Course: CS50's Introduction to Cybersecurity (Harvard) · Lecture 1 · November 2026*
