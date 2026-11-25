# 🧬 CryptoHack: Base64

> **Category:** General · Encoding
> Decode a hex string into bytes, then re-encode those bytes as Base64 to reveal the flag.

---

## 🧩 The challenge

> Another common encoding scheme is Base64, which allows us to represent binary data as an ASCII string using an alphabet of 64 characters. One character of a Base64 string encodes 6 bits, so 4 characters of Base64 encode three 8-bit bytes.
> Take the hex string below, decode it into bytes, and then encode it into Base64.

```
72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf
```

**Hint given:** after `import base64`, use `base64.b64encode()`. Decode the hex first.

---

## 📜 What is Base64?

**Base64** represents any binary data using only **64 safe, printable characters**:

```
A–Z  (26)  +  a–z  (26)  +  0–9  (10)  +  "+"  "/"   = 64
"="  is used as padding at the end when needed
```

### Why 64?
- 64 = 2⁶, so **each Base64 character holds 6 bits**.
- 3 bytes = 24 bits = **4 Base64 characters** (24 ÷ 6).

```
 3 bytes:   [8 bits][8 bits][8 bits]          = 24 bits
 Base64:    [6 bits][6 bits][6 bits][6 bits]  = 4 characters
```

So Base64 output is about **33% bigger** than the original data. This challenge has **27 bytes → 36 Base64 characters** (27 ÷ 3 × 4).

### Where it's used
- Embedding images and files directly in **HTML/CSS** (`data:image/png;base64,...`)
- Email attachments
- Tokens and keys on the web (e.g. JWTs, HTTP Basic Auth headers)

### Comparing encodings

| Encoding | Characters used | Bits per character | Size vs. original |
|---|---|---|---|
| [Hex](hex.md) | 16 (`0–9`, `a–f`) | 4 | 2× |
| **Base64** | 64 | 6 | ~1.33× |

---

## 💻 My solution

The challenge goes in **two directions**:

```
hex string  ──bytes.fromhex()──►  raw bytes  ──base64.b64encode()──►  Base64
```

```python
import base64

message = "72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf"

result = bytes.fromhex(message)
print(result)

print(base64.b64encode(result))
```

🚩 Flag found!

## 🐛 Mistakes I made along the way

My first attempt didn't work:

```python
result = print(bytes.fromhex(message).decode)   # ❌
print(base64.b64decode(result))                 # ❌
```

| Bug | Problem | Fix |
|---|---|---|
| `result = print(...)` | `print()` only displays a value and **returns `None`**, so `result` was `None` | Save the value first: `result = bytes.fromhex(message)` |
| `.decode` without `()` | Refers to the method without running it | And in this case, **don't decode at all**: these bytes aren't text, so `.decode()` raises a `UnicodeDecodeError` |
| `b64decode` | Wrong direction: the challenge asks to **encode** into Base64 | `base64.b64encode()` |

**Lessons:**
- `print()` is for showing values, not storing them.
- Not every sequence of bytes is valid text. In the [Hex](hex.md) challenge the bytes happened to be readable, but here they're raw binary.
- **Encode vs. decode:** always check which direction the data is going.

---

## 🔐 What I learned

- **Base64** turns any binary data into printable text using 64 characters, 6 bits each.
- **3 bytes ↔ 4 characters**, so the result is about a third bigger.
- Chaining conversions: **hex → bytes → Base64**.
- The difference between **printable text** and **raw bytes**, and when `.decode()` will fail.
- **Base64 is encoding, not encryption.** It's easy to spot (letters, digits, `+`, `/`, often ending in `=`) and anyone can decode it, so data that is "hidden" in Base64 is not protected at all.

---

*Platform: CryptoHack · Challenge: Base64 · November 2026*
