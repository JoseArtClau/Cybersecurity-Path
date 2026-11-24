# 🖥️ Command Line Fundamentals (Codédex)

> Notes on what I learned completing the beginner **Command Line** course on [Codédex](https://www.codedex.io/), plus my own notes, practice and mistakes.

**Environment:** Windows + Git Bash (UCRT64) — same commands as Linux/macOS terminals.

---

## 🔐 Why the command line matters for cybersecurity

- Most servers, security tools (Nmap, Wireshark/tshark, Metasploit, Burp extensions, etc.) and Linux distros like Kali are driven from the terminal.
- Incident response and log analysis mean navigating unfamiliar filesystems fast.
- Automation: one or two commands can replace many clicks, and scripts can repeat them.
- CTF (Capture The Flag) challenges are mostly solved from a shell.

---

## 📚 What I learned

### Chapter 1 — Navigating the filesystem

| Command | What it does | Example |
|---|---|---|
| `echo` | Prints text | `echo Hi! My name is Jose` |
| `pwd` | Prints the working directory (where I am) | `pwd` → `/c/Users/josea` |
| `ls` | Lists files and folders in the current directory | `ls` |
| `cd <dir>` | Changes into a directory | `cd Desktop` |
| `cd ..` | Goes up one level (to the parent directory) | `cd ..` |
| `cd ../..` | Goes up two levels | `cd ../..` |
| `cd a/b` | Goes down several levels at once | `cd house/kitchen` |
| `cat <file>` | Prints a file's contents | `cat items.txt` |
| `clear` | Clears the screen (does not change directory) | `clear` |

**Shortcuts**
- `↑` / `↓` — browse command history
- `Tab` — autocomplete command, file and folder names (fewer typos!)

**Key concepts**
- Folders are called **directories**. Folders inside them are **subdirectories**.
- The **working directory** is where you are right now. Every relative path starts from there.
- Going into a folder = moving to a **child**. `..` = moving to the **parent**.

**Practice:** explored a `house/` directory (kitchen, bedroom, etc.) and completed a **scavenger hunt** following clues across directories using only `cd`, `ls` and `cat`.

---

### Chapter 2 — Manipulating the filesystem

| Command | What it does | Example |
|---|---|---|
| `mkdir <dir>` | Creates a directory | `mkdir recipe` |
| `mkdir a/b` | Creates a subdirectory inside an existing one | `mkdir recipe/italian` |
| `touch <file>` | Creates an empty file | `touch italian/carbonara.txt` |
| `echo "text" > file` | Writes text to a file (**overwrites**) | `echo "Ingredients:" > carbonara.txt` |
| `echo "text" >> file` | Appends text to a file (keeps existing content) | `echo "- Eggs" >> carbonara.txt` |
| `cat a > b` | Copies a's content into b (**overwrites**) | `cat a.txt > b.txt` |
| `cat a >> b` | Appends a's content to b | `cat a.txt >> b.txt` |
| `mv <src> <dest>` | Moves a file/directory | `mv carbonara.txt ../brazilian/` |
| `mv <old> <new>` | Renames a file/directory | `mv american german` |
| `rm <file>` | Deletes file(s) | `rm old.txt` |
| `rmdir <dir>` | Deletes **empty** directories | `rmdir empty-folder/` |
| `rm -r <dir>` | Deletes a directory **and everything inside** ⚠️ | `rm -r old-folder` |
| `cp a b` | Copies a file (creates or overwrites b) | `cp carbonara.txt favorite1.txt` |
| `cp -r a/ b/` | Copies a whole directory | `cp -r recipe/ recipe-backup/` |
| `ls -R` | Lists everything, including inside subdirectories | `ls -R` |

**Projects**
- 🍝 **Recipe book**: built a `recipe/` directory with cuisine folders (`italian/`, `brazilian/`, `german/`), recipe files written with `echo`, and a `favorites/` folder holding copies.
- 🎵 **Music library**: built a `music-library/` with genre and artist directories and song files containing links, then printed the whole tree with `ls -R`.

## 🐛 Mistakes I made (and what they taught me)

**1. "No such file or directory" when moving a file**
```bash
# I was inside recipe/italian
mv carbonara.txt recipe/brazilian/   # ❌ looks for recipe/italian/recipe/brazilian
mv carbonara.txt brazilian/          # ❌ brazilian isn't inside italian
mv carbonara.txt ../brazilian/       # ✅ go up to recipe, then into brazilian
```
**Lesson:** relative paths always start from the current working directory. Check `pwd` and `ls` first.

**2. Copying a file that wasn't in my current folder**
```bash
# I was inside recipe/favorites
cp carbonara.txt favorite1.txt                  # ❌ the file isn't here
cp ../brazilian/carbonara.txt favorite1.txt     # ✅ give the path to where it actually is
```

**3. Renaming a folder from inside itself**
```bash
# I was inside recipe/american
mv american german   # ❌
cd ..
mv american german   # ✅
```
*Course: Codédex — Command Line (Chapters 1 & 2) · Completed November 2026*
