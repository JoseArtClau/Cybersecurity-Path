# 🔣 CryptoHack: Hex

> **Category:** General · Encoding
> Decode a hexadecimal string back into bytes to reveal the flag.

---

## 🧩 The challenge

> When we encrypt something, the resulting ciphertext commonly has bytes which are not printable ASCII characters. To share encrypted data, it's common to encode it into something more user-friendly and portable, such as hexadecimal.
> Decode the hex string below back into bytes to get the flag.

```
63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d
```

**Hint given:** in Python, `bytes.fromhex()` converts hex to bytes, and `.hex()` converts bytes back to hex.

---

## 📜 What is hexadecimal?

**Hexadecimal** is **base 16**. It uses 16 symbols: `0–9` and then `a–f` (a = 10 … f = 15).

| Decimal | 0 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
|---|---|---|---|---|---|---|---|---|
| **Hex** | 0 | 9 | a | b | c | d | e | f |

**Why it's used:** one hex digit = 4 bits, so **two hex digits = exactly one byte** (0–255). That makes hex a compact, readable way to show any byte, including the unprintable ones that encryption produces.

### From text to hex
This builds on the [ASCII challenge](ascii.md):

```
character  →  ASCII number  →  hex
   c       →       99       →  63
```

Decoding by hand: `63` = 6 × 16 + 3 = **99** → `chr(99)` = **`c`**

So the string starts `63 72 79 70 74 6f` → `c r y p t o`.

---

## 💻 My solution

```python
message = "63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d"

print(bytes.fromhex(message))
```

| Direction | Code |
|---|---|
| hex → bytes | `bytes.fromhex(hex_string)` |
| bytes → hex | `my_bytes.hex()` |
| bytes → string | `my_bytes.decode()` |
| string → bytes | `my_string.encode()` |

---

## 🔐 What I learned

- **Hexadecimal** is base 16, and two hex digits represent one byte.
- Hex is the standard way to **share encrypted data**, because raw ciphertext often contains unprintable bytes.
- The difference between **`bytes`** and **`str`** in Python, and how to convert between them with `.decode()` and `.encode()`.
- Like ASCII, **hex is encoding, not encryption**: anyone can decode it. You'll find it everywhere in security: hashes, memory dumps, packet captures (`tcpdump -X`) and keys.

---

*Platform: CryptoHack · Challenge: Hex · November 2026*
