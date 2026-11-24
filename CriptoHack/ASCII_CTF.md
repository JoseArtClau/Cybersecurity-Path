# 🔢 CryptoHack: ASCII

> **Category:** General · Encoding
> Convert a list of numbers into text to reveal the flag.

---

## 🧩 The challenge

> ASCII is a 7-bit encoding standard which allows the representation of text using the integers 0–127.
> Using the integer array below, convert the numbers to their corresponding ASCII characters to obtain a flag.

```
[99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]
```

**Hint given:** in Python, `chr()` converts an ASCII number to a character, and `ord()` does the opposite.

---

## 📜 What is ASCII?

Computers only store **numbers**. To store text, every character needs an agreed-upon number. **ASCII** (American Standard Code for Information Interchange) is one of those agreements.

- Uses **7 bits** → 2⁷ = **128** possible values (0–127)
- Covers English letters, digits, punctuation and some control characters

| Range | Characters |
|---|---|
| 48–57 | `0`–`9` |
| 65–90 | `A`–`Z` |
| 97–122 | `a`–`z` |
| 123 / 125 | `{` / `}` |
| 95 | `_` |

> ⚠️ **Encoding ≠ encryption.** ASCII has no key and hides nothing. Anyone who knows the standard can read it. It's just a way to *represent* data.

---

## 🛠️ The two functions

```python
print(chr(99))    # number → character:  c
print(ord("A"))   # character → number:  65
```

| Function | Input | Output |
|---|---|---|
| `chr()` | integer | character |
| `ord()` | character | integer |

---

## 💻 My solution

### Step 1: Test the functions
Before solving anything, I tested `chr()` and `ord()` on single values to see how they behave. `chr(99)` gave `c`, which matched the start of CryptoHack's flag format `crypto{...}`, so I knew I was on the right track.

### Step 2: Loop through the list

```python
message = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]

for number in message:
    print(chr(number))
```

This works, but prints **one character per line**:
```
c
r
y
p
...
```

### Step 3: Build the flag in one string
Same idea I used in my [Caesar cipher](registration.md): start with an empty string and add each character with `+=`.

```python
message = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]
flag = ""

for number in message:
    flag += chr(number)

print(flag)
```

### Step 4 (bonus): One-line version
Using `"".join()` to glue every character together:

```python
print("".join(chr(n) for n in message))
```

### Step 5 (bonus): Reverse it
`ord()` turns the flag back into the original numbers, proving the conversion works both ways:

```python
print([ord(c) for c in flag] == message)   # True
```

---

## 🔐 What I learned

- Computers store text as **numbers**, and ASCII is the table that maps them.
- `chr()` and `ord()` convert between the two.
- Building a string step by step with `+=`, and the shorter `"".join()` version.
- **Encoding is not encryption:** it offers zero security, but recognising encodings (ASCII, hex, Base64…) is a basic skill in CTFs and cryptography.

---

*Platform: CryptoHack · Challenge: ASCII · November 2026*
