# 🏛️ CryptoHack Registration: Solving the Caesar Cipher

> To create an account on [CryptoHack](https://cryptohack.org/), you have to solve a challenge first: decrypt a message written with a **Roman emperor's cipher**, the **Caesar cipher**. Instead of solving it by hand, I wrote a Python script to do it.

---

## 🧩 The challenge

```
Solve this Roman emperor's cipher: IFSP OPJTF SFNBJO GBMM
```

**Solution:** `hero noise remain fall` ✅

---

## 📜 What is the Caesar cipher?

One of the oldest known ciphers, named after **Julius Caesar**, who used it in his private letters. Every letter is **shifted** a fixed number of positions in the alphabet. That number is the **key**.

**Example with key = 1:**

```
Plain:   a b c d e f g h ... x y z
Cipher:  b c d e f g h i ... y z a
```

So `hero` becomes `ifsp`: each letter moved **1 position forward**.

| Plain | h | e | r | o |
|---|---|---|---|---|
| Position | 7 | 4 | 17 | 14 |
| +1 | 8 | 5 | 18 | 15 |
| Cipher | **i** | **f** | **s** | **p** |

It's a **symmetric** cipher: the same key encrypts and decrypts. *(See [CS50 Lecture 1: Securing Data](../CS50/lecture-01.md).)*

---

## 💻 My code

```python
# Caesar cipher
# Plan:
# 1. Keep the message in a variable
# 2. Ask the user for the key
# 3. Make sure the key is between 1 and 26
# 4. Understand how letters turn into numbers
# 5. Shift each letter
# 6. Build and show the new message

alphabet = "abcdefghijklmnopqrstuvwxyz"
mensagem = "ifsp opjtf sfnbjo gbmm"  # the variable that stores the encrypted string
mensagem_secreta = ""

while True:  # keep asking until the key is valid
    entrada = input("Digite a key (1-26): ")  # input() always returns a string
    if entrada.isdigit():          # check it's a number
        key = int(entrada)         # convert the string to an integer
        if 1 <= key <= 26:
            break
    print("invalid key! type a number between 1 - 26")

for letra in mensagem:
    minuscula = letra.lower()

    if minuscula in alphabet:
        posicao = alphabet.index(minuscula)       # letter → number (a=0 ... z=25)
        nova_posicao = (posicao + key) % 26       # shift and wrap around
        nova_letra = alphabet[nova_posicao]       # number → letter

        if letra.isupper():                       # keep the original case
            nova_letra = nova_letra.upper()

        mensagem_secreta += nova_letra
    else:
        mensagem_secreta += letra                 # spaces and symbols stay the same

print(mensagem_secreta)
```

---

## 🔍 How it works, step by step

### 1. Validating the key
- `input()` always returns **text**, so `"25"` is a string, not a number.
- `.isdigit()` checks that the text contains only digits, which avoids a crash if someone types `abc`.
- `int()` converts it into a number, and `1 <= key <= 26` checks the range.
- The `while True` loop keeps asking until the key is valid, and `break` exits it.

### 2. Turning letters into numbers
`alphabet.index(letter)` gives each letter's position:

```
a=0  b=1  c=2  ...  y=24  z=25
```

### 3. Shifting with `% 26` (modulo)
The **modulo** operator gives the remainder of a division. It makes the alphabet **wrap around**, so after `z` comes `a`:

```
z (25) + 1 = 26  →  26 % 26 = 0  →  a
y (24) + 3 = 27  →  27 % 26 = 1  →  b
```

### 4. Keeping everything else
- Uppercase letters stay uppercase (`.isupper()` / `.upper()`).
- Spaces and punctuation aren't in `alphabet`, so they're copied unchanged. That's why the words stay separated.

---

## 🔓 Finding the key

My code **adds** the key to each letter. The message was encrypted by shifting **+1**, so to undo it I need to shift **−1**.

Because the alphabet wraps around, shifting **+25** is the same as shifting **−1** (25 + 1 = 26, a full loop):

| Key entered | Output |
|---|---|
| 1 | `jgtq pqkug tgockp hcnn` ❌ |
| **25** | **`hero noise remain fall`** ✅ |

> 💡 To decrypt a Caesar cipher encrypted with key *k*, use key **26 − k**.

---

## 🔐 What I learned

- How a **substitution cipher** works and how to code it from scratch.
- Converting between **letters and numbers**, and using **modulo** to wrap around.
- **Validating user input** so the program doesn't crash.
- **Why the Caesar cipher is insecure:** only 25 possible keys means a computer breaks it instantly. Modern ciphers like **AES** have so many possible keys that brute force is impossible in practice.

---

*Platform: CryptoHack · Challenge: Registration · November 2026*
