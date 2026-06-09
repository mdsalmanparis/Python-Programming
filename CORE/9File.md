Let's go. Full topic, nothing skipped.

---

## 0.10 File I/O Basics

---

## What Is File I/O

File I/O means reading from and writing to files on disk. Your program's variables live in RAM — they disappear when the program ends. Files are how you persist data between runs.

```
RAM (variables)          Disk (files)
─────────────────        ─────────────────
scores = [88, 92]   ←→   scores.txt
user = {"name":...} ←→   user.json
                         report.csv
```

---

## `open()` — The Gateway to Files

`open()` returns a **file object**. You read or write through that object.

```python
f = open("file.txt", "r")    # open for reading
content = f.read()
f.close()                    # must close manually — risky
```

**The problem — if an error happens before `f.close()`, the file never closes:**

```python
f = open("file.txt", "r")
content = f.read()
process(content)             # if this raises an exception...
f.close()                    # this never runs — file stays open, resource leaked
```

---

## `with` Statement — Always Use This

The `with` statement guarantees the file is closed — no matter what happens, even if an exception is raised inside the block.

```python
with open("file.txt", "r") as f:
    content = f.read()
# file is automatically closed here — guaranteed
```

**Under the hood — `with` calls `f.__enter__()` on entry and `f.__exit__()` on exit. `__exit__` calls `f.close()` even if an exception occurred.**

```python
# This is what with does internally:
f = open("file.txt", "r")
try:
    content = f.read()
finally:
    f.close()    # always closes

# The with statement is just cleaner syntax for this pattern
```

**Multiple files in one `with` statement:**

```python
with open("input.txt", "r") as fin, open("output.txt", "w") as fout:
    data = fin.read()
    fout.write(data.upper())
# both files closed automatically
```

---

## File Modes

```python
open("file.txt", "r")    # read — file must exist, error if not
open("file.txt", "w")    # write — creates file if not exists, OVERWRITES if exists
open("file.txt", "a")    # append — creates if not exists, adds to end if exists
open("file.txt", "x")    # exclusive create — fails if file already exists
open("file.txt", "r+")   # read AND write — file must exist
```

**Binary modes — for images, PDFs, audio, any non-text file:**

```python
open("image.png", "rb")   # read binary
open("image.png", "wb")   # write binary
open("image.png", "ab")   # append binary
```

**Text vs binary — the key difference:**

```python
# Text mode (default) — Python translates \n for the platform
# Windows: \r\n on disk, Python gives you \n in code
# Linux/Mac: \n on disk, Python gives you \n in code

# Binary mode — no translation, raw bytes
# Use for: images, audio, video, PDFs, Excel files, any non-text
```

**The `"w"` overwrite trap:**

```python
# file.txt contains: "important data"

with open("file.txt", "w") as f:
    f.write("new content")
# "important data" is GONE — w always starts from scratch
# Use "a" to add to existing content
# Use "r+" to read and selectively modify
```

---

## Reading — Four Ways

### `f.read()` — Entire File as One String

```python
with open("notes.txt", "r", encoding="utf-8") as f:
    content = f.read()

print(content)         # entire file as one string
print(type(content))   # <class 'str'>
print(len(content))    # number of characters
```

**Read a specific number of characters:**

```python
with open("notes.txt", "r", encoding="utf-8") as f:
    first_10 = f.read(10)    # read first 10 characters only
    next_10  = f.read(10)    # reads next 10 — file position advances
    rest     = f.read()      # reads everything remaining
```

**When to use:** small files where you need the whole content at once — config files, JSON files, small data files.

**When NOT to use:** large files — `f.read()` loads everything into RAM. A 2GB log file would use 2GB of RAM.

---

### `f.readlines()` — List of Lines

```python
with open("students.txt", "r", encoding="utf-8") as f:
    lines = f.readlines()

print(lines)
# ['Mohamed, 88\n', 'Priya, 92\n', 'Arjun, 74\n']
# Note: \n is included in each line
```

**Stripping newlines:**

```python
with open("students.txt", "r", encoding="utf-8") as f:
    lines = f.readlines()

clean_lines = [line.rstrip("\n") for line in lines]
# or
clean_lines = [line.strip() for line in lines]
print(clean_lines)
# ['Mohamed, 88', 'Priya, 92', 'Arjun, 74']
```

**When to use:** when you need all lines as a list to index or slice — `lines[0]`, `lines[-1]`, `lines[2:5]`.

**When NOT to use:** large files — same problem as `f.read()`, loads everything into RAM.

---

### `for line in f:` — Iterate Lazily (Best for Large Files)

```python
with open("large_log.txt", "r", encoding="utf-8") as f:
    for line in f:
        # processes ONE line at a time — only that line is in RAM
        print(line.strip())
```

**This is the most memory-efficient way to read a file.** Python reads one line at a time from disk. A 10GB log file uses almost no RAM.

```python
# Count lines without loading whole file
count = 0
with open("huge_file.txt", "r", encoding="utf-8") as f:
    for line in f:
        count += 1
print(f"total lines: {count}")

# Find lines containing a keyword
with open("server.log", "r", encoding="utf-8") as f:
    errors = [line.strip() for line in f if "ERROR" in line]
print(f"found {len(errors)} errors")

# Process CSV line by line
with open("data.csv", "r", encoding="utf-8") as f:
    header = next(f).strip()           # read first line separately
    for line in f:
        values = line.strip().split(",")
        # process each row
```

**When to use:** always, unless you specifically need random access to lines. Default to this.

---

### `f.readline()` — One Line at a Time

```python
with open("students.txt", "r", encoding="utf-8") as f:
    first  = f.readline()    # 'Mohamed, 88\n'
    second = f.readline()    # 'Priya, 92\n'
    third  = f.readline()    # 'Arjun, 74\n'
    empty  = f.readline()    # ''  — empty string means end of file

print(first.strip())    # Mohamed, 88
```

**End-of-file detection — empty string, not `\n`:**

```python
with open("file.txt", "r", encoding="utf-8") as f:
    while True:
        line = f.readline()
        if not line:         # empty string = end of file
            break
        process(line.strip())

# This is equivalent to:
with open("file.txt", "r", encoding="utf-8") as f:
    for line in f:           # cleaner — use this instead
        process(line.strip())
```

**When to use:** when you need to read specific lines conditionally — read a header, then decide whether to read the rest.

---

### Reading Summary — Which to Use When

```python
# Small file, need full content as one string
content = open("config.json").read()         # rare — use with statement

# All lines as a list (need indexing/slicing)
lines = open("data.txt").readlines()

# Large file, process line by line — DEFAULT CHOICE
for line in open("large.txt"):
    process(line)

# Manual line-by-line control
line = f.readline()
```

| Method | Returns | Loads all into RAM | Use when |
|---|---|---|---|
| `f.read()` | one string | yes | small files, need full content |
| `f.readlines()` | list of strings | yes | need line indexing/slicing |
| `for line in f:` | one line/iter | no | large files, default choice |
| `f.readline()` | one string | no | manual line control |

---

## Writing

### `f.write()` — Write a String

```python
with open("output.txt", "w", encoding="utf-8") as f:
    f.write("Hello, World!")
# output.txt contains: Hello, World!
```

**No automatic newline — you must add `\n` yourself:**

```python
with open("output.txt", "w", encoding="utf-8") as f:
    f.write("line one")
    f.write("line two")
    f.write("line three")
# output.txt contains: line oneline twolinethree  — all on one line

# Correct — add \n explicitly
with open("output.txt", "w", encoding="utf-8") as f:
    f.write("line one\n")
    f.write("line two\n")
    f.write("line three\n")
# output.txt:
# line one
# line two
# line three
```

**`f.write()` returns the number of characters written:**

```python
with open("output.txt", "w", encoding="utf-8") as f:
    n = f.write("hello")
    print(n)    # 5
```

---

### `f.writelines()` — Write a List of Strings

```python
lines = ["line one\n", "line two\n", "line three\n"]

with open("output.txt", "w", encoding="utf-8") as f:
    f.writelines(lines)
# writelines does NOT add \n automatically either
# the \n must be in your strings already
```

---

### Append Mode — Add to Existing File

```python
# First run — creates file
with open("log.txt", "a", encoding="utf-8") as f:
    f.write("2024-11-15: server started\n")

# Second run — adds to end, doesn't overwrite
with open("log.txt", "a", encoding="utf-8") as f:
    f.write("2024-11-15: user logged in\n")

# log.txt now contains:
# 2024-11-15: server started
# 2024-11-15: user logged in
```

---

## `encoding="utf-8"` — Always Specify

```python
# BAD — encoding depends on the platform
# Windows default: cp1252, Linux/Mac default: utf-8
# Code that works on Mac may break on Windows

with open("file.txt", "r") as f:          # don't do this
    content = f.read()

# GOOD — explicit UTF-8 works everywhere
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()
```

**UTF-8 handles all characters — Tamil, Arabic, emoji, anything:**

```python
with open("names.txt", "w", encoding="utf-8") as f:
    f.write("Mohamed\n")
    f.write("முகமது\n")     # Tamil — works with utf-8
    f.write("محمد\n")        # Arabic — works with utf-8

with open("names.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())
# Mohamed
# முகமது
# محمد
```

---

## Checking Existence

```python
import os

# Check if file exists
if os.path.exists("file.txt"):
    print("file exists")

if os.path.isfile("file.txt"):
    print("it's a file")     # distinguishes from directory

if os.path.isdir("myfolder"):
    print("it's a directory")

# Safe read — check before opening
filename = "data.txt"
if os.path.exists(filename):
    with open(filename, "r", encoding="utf-8") as f:
        content = f.read()
else:
    print(f"{filename} not found")

# Or handle with try/except — more Pythonic
try:
    with open(filename, "r", encoding="utf-8") as f:
        content = f.read()
except FileNotFoundError:
    print(f"{filename} not found")
```

**`try/except` vs `os.path.exists()` — which to use:**

```python
# os.path.exists() — check first
# Risk: file could be deleted BETWEEN the check and the open (race condition)
# Fine for scripts and simple programs

# try/except — ask for forgiveness, not permission
# No race condition — handles the error at the point it happens
# Preferred in professional code
```

Phase 2 covers `pathlib` which is the modern replacement for `os.path` — cleaner syntax, same power.

---

## Binary Files

```python
# Reading a binary file — image, PDF, etc.
with open("image.png", "rb") as f:
    data = f.read()        # bytes object, not str
    print(type(data))      # <class 'bytes'>
    print(data[:4])        # b'\x89PNG'  — PNG file signature

# Copying a binary file
with open("source.png", "rb") as fin:
    with open("copy.png", "wb") as fout:
        fout.write(fin.read())

# More memory-efficient — read in chunks
CHUNK = 4096    # 4KB at a time

with open("large_video.mp4", "rb") as fin:
    with open("copy.mp4", "wb") as fout:
        while True:
            chunk = fin.read(CHUNK)
            if not chunk:       # empty bytes = end of file
                break
            fout.write(chunk)
```

---

## Common Pattern: Read → Process → Write

The most common file operation pattern in real code.

```python
# Pattern 1 — transform a file
with open("input.txt", "r", encoding="utf-8") as f:
    content = f.read()

processed = content.upper()

with open("output.txt", "w", encoding="utf-8") as f:
    f.write(processed)
```

```python
# Pattern 2 — filter lines
with open("server.log", "r", encoding="utf-8") as fin:
    with open("errors.log", "w", encoding="utf-8") as fout:
        for line in fin:                     # lazy read
            if "ERROR" in line:
                fout.write(line)             # write matching lines
```

```python
# Pattern 3 — process CSV data
students = []

with open("students.csv", "r", encoding="utf-8") as f:
    next(f)    # skip header line

    for line in f:
        parts = line.strip().split(",")
        students.append({
            "name":  parts[0].strip(),
            "score": int(parts[1].strip()),
            "city":  parts[2].strip()
        })

# Process
passing = [s for s in students if s["score"] >= 60]
top     = max(students, key=lambda s: s["score"])

# Write results
with open("report.txt", "w", encoding="utf-8") as f:
    f.write(f"Total students : {len(students)}\n")
    f.write(f"Passed         : {len(passing)}\n")
    f.write(f"Top scorer     : {top['name']} ({top['score']})\n")
    f.write("\nPassing students:\n")
    for s in sorted(passing, key=lambda s: s["score"], reverse=True):
        f.write(f"  {s['name']:15} {s['score']}\n")
```

---

## Putting It All Together

```python
import os
from datetime import datetime

LOG_FILE = "activity.log"
DATA_FILE = "scores.txt"

def log(message):
    """Append a timestamped message to the log file."""
    timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    with open(LOG_FILE, "a", encoding="utf-8") as f:
        f.write(f"[{timestamp}] {message}\n")


def save_scores(scores):
    """Save a list of (name, score) tuples to file."""
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        for name, score in scores:
            f.write(f"{name},{score}\n")
    log(f"saved {len(scores)} scores to {DATA_FILE}")


def load_scores():
    """Load scores from file. Returns empty list if file not found."""
    if not os.path.exists(DATA_FILE):
        log(f"{DATA_FILE} not found — returning empty list")
        return []

    scores = []
    try:
        with open(DATA_FILE, "r", encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if not line:
                    continue    # skip blank lines
                parts = line.split(",")
                name  = parts[0].strip()
                score = int(parts[1].strip())
                scores.append((name, score))
        log(f"loaded {len(scores)} scores from {DATA_FILE}")
    except (ValueError, IndexError) as e:
        log(f"error parsing {DATA_FILE}: {e}")

    return scores


def generate_report(scores):
    """Write a formatted report to report.txt."""
    report_file = "report.txt"
    with open(report_file, "w", encoding="utf-8") as f:
        f.write("=" * 40 + "\n")
        f.write("SCORE REPORT\n")
        f.write(f"Generated: {datetime.now().strftime('%Y-%m-%d %H:%M')}\n")
        f.write("=" * 40 + "\n\n")

        if not scores:
            f.write("No data.\n")
            return

        total = sum(s for _, s in scores)
        avg   = total / len(scores)

        f.write(f"Students : {len(scores)}\n")
        f.write(f"Average  : {avg:.1f}\n")
        f.write(f"Highest  : {max(s for _, s in scores)}\n")
        f.write(f"Lowest   : {min(s for _, s in scores)}\n\n")

        f.write("Rankings:\n")
        ranked = sorted(scores, key=lambda x: x[1], reverse=True)
        for i, (name, score) in enumerate(ranked, start=1):
            f.write(f"  {i}. {name:15} {score}\n")

    log(f"report written to {report_file}")
    print(f"report saved to {report_file}")


if __name__ == "__main__":
    # Save some data
    data = [
        ("Mohamed", 88),
        ("Priya",   92),
        ("Arjun",   74),
        ("Sara",    65),
        ("Vikram",  55),
    ]
    save_scores(data)

    # Load it back
    loaded = load_scores()
    print(f"loaded {len(loaded)} records")

    # Generate report
    generate_report(loaded)

    # Check log
    with open(LOG_FILE, "r", encoding="utf-8") as f:
        print("\nActivity log:")
        for line in f:
            print(" ", line.strip())
```

---

## Key Takeaways

- Always use `with open(...) as f:` — never open without `with`. It guarantees the file closes even on error.
- `"w"` mode **overwrites** silently — there is no warning, no prompt. The old content is gone. Use `"a"` to append.
- Always specify `encoding="utf-8"` — without it, behaviour differs between Windows and Linux/Mac.
- `f.read()` and `f.readlines()` load the entire file into RAM — don't use on large files.
- `for line in f:` is lazy — reads one line at a time — use this as your default for line-by-line processing.
- `f.write()` does **not** add newlines — you must add `\n` yourself.
- `try/except FileNotFoundError` is more Pythonic than `os.path.exists()` before opening.
- Binary mode `"rb"` / `"wb"` for non-text files — images, PDFs, audio, any non-text format.
- The core pattern is always: open → read → process → open → write. Keep reading and writing in separate `with` blocks.