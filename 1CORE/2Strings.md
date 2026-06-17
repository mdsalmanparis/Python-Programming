## 0.3 Strings in Depth

---

## String Creation

A string is a sequence of characters. You create one by wrapping text in quotes.

### Single and double quotes — identical

```python
a = 'hello'
b = "hello"
print(a == b)      # True — exact same string
print(type(a))     # <class 'str'>
```

Choose one style and stay consistent. The only time you switch is to avoid escaping:

```python
# Instead of escaping the apostrophe:
bad  = 'it\'s fine'       # works but ugly
good = "it's fine"        # cleaner — use double quotes when string has apostrophe

# Instead of escaping double quotes:
bad  = "she said \"hello\""    # works but ugly
good = 'she said "hello"'      # cleaner — use single quotes when string has double quotes
```

---

### Triple quotes — multi-line strings

```python
message = """This is line one.
This is line two.
This is line three."""

print(message)
# This is line one.
# This is line two.
# This is line three.
```

Triple quotes preserve every newline and space exactly as written. Single-quoted strings cannot span multiple lines without `\n`.

```python
# Single quote multi-line — you must manually add \n
message = "line one\nline two\nline three"

# Triple quote — newlines are literal
message = """line one
line two
line three"""
```

**Triple quotes are also used for docstrings:**

```python
def add(a, b):
    """Returns the sum of a and b."""
    return a + b
```

**Triple single quotes work identically:**

```python
note = '''This also works
across multiple lines'''
```

---

## String Indexing

A string is a sequence — every character has a position number called an **index**.

```python
s = "Python"
#    P  y  t  h  o  n
#    0  1  2  3  4  5   ← positive indexes
#   -6 -5 -4 -3 -2 -1   ← negative indexes
```

**Positive indexing — starts at 0:**

```python
s = "Python"
print(s[0])    # P
print(s[1])    # y
print(s[5])    # n  — last character
```

**Negative indexing — starts at -1 from the end:**

```python
s = "Python"
print(s[-1])   # n  — last character
print(s[-2])   # o  — second from last
print(s[-6])   # P  — same as s[0]
```

Negative indexing is extremely useful — `s[-1]` to get the last character works regardless of how long the string is.

**`IndexError` — going out of range:**

```python
s = "Python"     # length is 6, valid indexes: 0 to 5, or -1 to -6

print(s[6])      # IndexError: string index out of range
print(s[100])    # IndexError: string index out of range
print(s[-7])     # IndexError: string index out of range
```

---

## String Slicing

Slicing extracts a portion of a string. It returns a **new string** — the original is unchanged.

### Syntax: `s[start:stop:step]`

- `start` — index to begin at (inclusive)
- `stop` — index to end at (exclusive — this character is NOT included)
- `step` — how many characters to skip (default is 1)

```python
s = "Python"
#    0 1 2 3 4 5

print(s[1:4])    # yth  — index 1, 2, 3 (4 is excluded)
print(s[0:3])    # Pyt  — index 0, 1, 2
print(s[2:6])    # thon — index 2, 3, 4, 5
```

### Omitting start or stop

```python
s = "Python"

print(s[:3])     # Pyt  — from beginning to index 2
print(s[2:])     # thon — from index 2 to end
print(s[:])      # Python — full copy of the string
```

### Step

```python
s = "Python"

print(s[::2])    # Pto  — every 2nd character: P(0), t(2), o(4)
print(s[1::2])   # yhn  — start at 1, every 2nd: y(1), h(3), n(5)
print(s[::3])    # Ph   — every 3rd: P(0), h(3)
```

### Reverse a string — `s[::-1]`

```python
s = "Python"
print(s[::-1])   # nohtyP — step of -1 goes backwards

word = "racecar"
print(word[::-1])   # racecar — palindrome check trick
print(word == word[::-1])   # True
```

**Step of -1 with start and stop:**

```python
s = "Python"
print(s[4:1:-1])   # oht  — from index 4 down to index 2 (1 is excluded)
```

### Slicing never raises IndexError

```python
s = "Python"
print(s[0:100])    # Python — goes to end of string, no error
print(s[10:20])    # ""     — empty string, no error
```

---

## `len(s)` — Length

Returns the number of characters in the string.

```python
print(len("Python"))      # 6
print(len(""))            # 0  — empty string
print(len("hello world")) # 11 — space counts as a character
print(len("  "))          # 2  — two spaces
```

**Relationship between `len()` and indexing:**

```python
s = "Python"
last_index = len(s) - 1    # 5
print(s[last_index])       # n
print(s[len(s) - 1])       # n — same
print(s[-1])               # n — simpler, use this
```

---

## String Concatenation

Joining strings together with `+`.

```python
first = "Mohamed"
last  = "Salman"
full  = first + " " + last
print(full)   # Mohamed Salman
```

**Concatenation only works between strings:**

```python
name = "Mohamed"
age  = 25

print(name + age)           # TypeError: can only concatenate str (not "int") to str
print(name + str(age))      # Mohamed25 — must convert first
print(name + " " + str(age))  # Mohamed 25
```

### Why concatenation in a loop is inefficient

```python
# BAD — creates a new string object on every iteration
result = ""
for i in range(1000):
    result += str(i)     # 1000 new string objects created
```

Every time you do `result += str(i)`, Python creates a brand new string in memory because strings are immutable. For 1000 iterations that's 1000 throwaway objects.

```python
# GOOD — collect into a list, join once at the end
parts = []
for i in range(1000):
    parts.append(str(i))
result = "".join(parts)   # one single string created
```

You'll use this pattern constantly in real code.

---

## String Repetition

```python
print("ha" * 3)       # hahaha
print("-" * 30)       # ------------------------------ (30 dashes — useful for separators)
print("ab" * 0)       # ""    — zero repetitions = empty string
print("=" * 20)       # ====================

laugh = "ha"
print(laugh * 5)      # hahahahaha
```

---

## f-strings — Formatted String Literals

The modern, preferred way to embed values inside strings. Available from Python 3.6+.

```python
name = "Mohamed"
print(f"Hello, {name}!")    # Hello, Mohamed!
```

### Any expression works inside `{}`

```python
a = 10
b = 3
print(f"{a} + {b} = {a + b}")         # 10 + 3 = 13
print(f"{a} / {b} = {a / b:.2f}")     # 10 / 3 = 3.33  (formatted to 2 decimal places)
print(f"{'hello'.upper()}")            # HELLO
print(f"{2 ** 8}")                     # 256
print(f"{'yes' if a > b else 'no'}")   # yes
```

### Format specifiers — controlling output

```python
pi = 3.14159265

print(f"{pi:.2f}")      # 3.14     — 2 decimal places
print(f"{pi:.4f}")      # 3.1416   — 4 decimal places
print(f"{pi:10.2f}")    # "      3.14"  — width 10, 2 decimals, right-aligned
print(f"{pi:<10.2f}")   # "3.14      "  — left-aligned
print(f"{pi:^10.2f}")   # "   3.14   "  — center-aligned

n = 1000000
print(f"{n:,}")         # 1,000,000   — thousands separator
print(f"{n:_}")         # 1_000_000   — underscore separator

x = 255
print(f"{x:b}")         # 11111111    — binary
print(f"{x:x}")         # ff          — hex lowercase
print(f"{x:X}")         # FF          — hex uppercase
print(f"{x:o}")         # 377         — octal
```

### f-string debugging with `=` (Python 3.8+)

```python
x = 42
y = 10
print(f"{x=}")          # x=42        — prints name AND value
print(f"{x + y=}")      # x + y=52    — expression and result
```

Extremely useful for quick debugging without writing `print("x =", x)`.

---

## String Methods

Methods are functions that belong to a string object. Called with dot notation: `string.method()`.

---

### Case Methods

```python
s = "hello world"

print(s.upper())        # HELLO WORLD  — all uppercase
print(s.lower())        # hello world  — all lowercase
print(s.capitalize())   # Hello world  — only first character uppercase, rest lower
print(s.title())        # Hello World  — first letter of every word uppercase
print(s.swapcase())     # HELLO WORLD → hello world (swaps each character's case)
```

**Important — methods return a new string, they don't change the original:**

```python
s = "hello"
s.upper()         # does nothing useful — return value is thrown away
print(s)          # hello — unchanged

result = s.upper()
print(result)     # HELLO — correct: capture the return value
```

---

### Search Methods

**`.find(sub)` — returns index of first occurrence, `-1` if not found:**

```python
s = "hello world"
print(s.find("world"))    # 6   — starts at index 6
print(s.find("o"))        # 4   — first 'o'
print(s.find("xyz"))      # -1  — not found, no error
print(s.find("o", 5))     # 7   — start searching from index 5
```

**`.index(sub)` — same as find but raises `ValueError` if not found:**

```python
s = "hello world"
print(s.index("world"))   # 6
print(s.index("xyz"))     # ValueError: substring not found
```

Use `.find()` when not finding is a normal case. Use `.index()` when not finding means something went wrong.

**`.count(sub)` — counts non-overlapping occurrences:**

```python
s = "hello world"
print(s.count("l"))       # 3
print(s.count("o"))       # 2
print(s.count("xyz"))     # 0  — not found, returns 0 (no error)
print(s.count("ll"))      # 1
```

**`.startswith(prefix)` and `.endswith(suffix)` — return bool:**

```python
s = "hello world"
print(s.startswith("hello"))    # True
print(s.startswith("world"))    # False
print(s.endswith("world"))      # True
print(s.endswith("hello"))      # False

# Can pass a tuple to check multiple options
filename = "report.pdf"
print(filename.endswith((".pdf", ".docx", ".xlsx")))   # True
```

**`in` operator — membership test:**

```python
s = "hello world"
print("world" in s)     # True
print("xyz" in s)       # False
print("o" in s)         # True
print("" in s)          # True  — empty string is always "in" any string

# Used in conditions
if "error" in log_message:
    print("something went wrong")
```

---

### Clean Methods

**`.strip()` — removes leading and trailing whitespace:**

```python
s = "   hello world   "
print(s.strip())     # "hello world"  — both sides cleaned
print(s.lstrip())    # "hello world   "  — left side only
print(s.rstrip())    # "   hello world"  — right side only
```

**Strip a specific character:**

```python
s = "---hello---"
print(s.strip("-"))    # "hello"   — removes dashes from both ends
print(s.strip("-h"))   # "ello"    — removes any of these characters from ends

url = "https://example.com/"
print(url.strip("/"))   # "https://example.com"
```

**The most common use — cleaning user input:**

```python
user_input = "   Mohamed   "
clean = user_input.strip()
print(clean)    # "Mohamed"
```

---

### Replace

**`.replace(old, new)` — replaces all occurrences:**

```python
s = "hello world"
print(s.replace("world", "Python"))    # hello Python
print(s.replace("l", "L"))            # heLLo worLd  — all occurrences
print(s.replace("l", "L", 1))         # heLlo world  — only first occurrence

# Replace with empty string to delete
s = "h-e-l-l-o"
print(s.replace("-", ""))    # hello
```

---

### Split and Join

**`.split(sep)` — splits a string into a list:**

```python
s = "hello world python"
print(s.split())            # ['hello', 'world', 'python']  — splits on any whitespace
print(s.split(" "))         # ['hello', 'world', 'python']  — splits on space

csv = "name,age,city"
print(csv.split(","))       # ['name', 'age', 'city']

path = "home/user/documents"
print(path.split("/"))      # ['home', 'user', 'documents']
```

**`.split()` with no argument vs `" "` — subtle difference:**

```python
s = "  hello   world  "
print(s.split())       # ['hello', 'world']     — strips and splits on any whitespace
print(s.split(" "))    # ['', '', 'hello', '', '', 'world', '', '']  — literal space splits
```

No-argument `.split()` is almost always what you want.

**`.splitlines()` — split on newlines:**

```python
s = "line1\nline2\nline3"
print(s.splitlines())    # ['line1', 'line2', 'line3']
```

**`sep.join(list)` — joins a list of strings into one string:**

```python
words = ["hello", "world", "python"]
print(" ".join(words))     # hello world python
print("-".join(words))     # hello-world-python
print("".join(words))      # helloworldpython
print(", ".join(words))    # hello, world, python
```

**`join` only works with a list of strings — common mistake:**

```python
numbers = [1, 2, 3]
print(", ".join(numbers))              # TypeError: sequence item 0: expected str instance
print(", ".join(str(n) for n in numbers))   # "1, 2, 3"  — convert first
```

**Split → process → join — a pattern you'll use constantly:**

```python
sentence = "the quick brown fox"
words = sentence.split()
capitalized = [word.capitalize() for word in words]
result = " ".join(capitalized)
print(result)   # The Quick Brown Fox
```

---

### Check Methods — return bool

```python
print("123".isdigit())      # True   — all characters are digits
print("12.3".isdigit())     # False  — dot is not a digit
print("12a".isdigit())      # False

print("hello".isalpha())    # True   — all characters are letters
print("hello2".isalpha())   # False  — contains a digit
print("".isalpha())         # False  — empty string is False

print("hello2".isalnum())   # True   — letters and digits only
print("hello!".isalnum())   # False  — contains punctuation
print("".isalnum())         # False

print("   ".isspace())      # True   — only whitespace
print("  a  ".isspace())    # False

print("hello".islower())    # True
print("HELLO".isupper())    # True
print("Hello".isupper())    # False
print("Title Case".istitle()) # True
```

**Real-world use — validating input:**

```python
user_age = input("Enter your age: ")   # input always returns str

if user_age.isdigit():
    age = int(user_age)
    print(f"You are {age} years old")
else:
    print("That's not a valid age")
```

---

## String Immutability

Strings cannot be changed after creation. Every "modification" creates a brand new string.

```python
s = "hello"
s[0] = "H"      # TypeError: 'str' object does not support item assignment
```

**What actually happens when you "modify" a string:**

```python
s = "hello"
s = s.upper()    # a NEW string "HELLO" is created, s now points to it
                 # the old "hello" object still exists until garbage collected
```

**Why this matters — identity vs value:**

```python
a = "hello"
b = a
b = b.upper()   # b now points to a NEW string "HELLO"

print(a)        # hello  — completely unchanged
print(b)        # HELLO
```

Reassigning `b` never touches `a`. They were just two names pointing to the same object, and now `b` points to a different one.

**Practical implication — always capture the return value:**

```python
name = "  Mohamed  "

# WRONG — does nothing
name.strip()
print(name)       # "  Mohamed  "  — unchanged

# CORRECT — capture the result
name = name.strip()
print(name)       # "Mohamed"
```

---

## `str.format()` — The Older Way

Before f-strings (pre Python 3.6), `.format()` was the standard. You'll see it in older codebases.

**Positional:**

```python
print("Hello, {}!".format("Mohamed"))          # Hello, Mohamed!
print("{} + {} = {}".format(1, 2, 3))          # 1 + 2 = 3
print("{0} and {1} and {0}".format("a", "b"))  # a and b and a  — reuse by index
```

**Named:**

```python
print("Name: {name}, Age: {age}".format(name="Mohamed", age=25))
# Name: Mohamed, Age: 25
```

**Format specifiers work the same as f-strings:**

```python
print("{:.2f}".format(3.14159))    # 3.14
print("{:10}".format("hello"))     # "hello     "  — padded to width 10
print("{:>10}".format("hello"))    # "     hello"  — right-aligned
```

**f-string vs `.format()` vs `%` formatting — which to use:**

```python
name = "Mohamed"
score = 88

# Old style — avoid
print("Hello %s, score: %d" % (name, score))

# .format() — acceptable, common in older code
print("Hello {}, score: {}".format(name, score))

# f-string — use this always in new code
print(f"Hello {name}, score: {score}")
```

---

## Escape Characters

Special characters that can't be typed literally inside a string.

```python
print("line one\nline two")     # newline — goes to next line
print("col1\tcol2\tcol3")       # tab — horizontal space
print("back\\slash")            # \\  — literal backslash
print("she said \"hello\"")     # \"  — double quote inside double-quoted string
print('it\'s fine')             # \'  — single quote inside single-quoted string
print("\a")                     # bell (system beep, rarely used)
print("\r")                     # carriage return (moves cursor to line start)
```

**`\n` in action:**

```python
address = "123 Main Street\nChennai\nTamil Nadu"
print(address)
# 123 Main Street
# Chennai
# Tamil Nadu
```

**`\t` in action:**

```python
print("Name\tAge\tCity")
print("Mohamed\t25\tChennai")
# Name    Age     City
# Mohamed 25      Chennai
```

**`\\` — when you actually need a backslash:**

```python
path = "C:\\Users\\Mohamed\\Documents"
print(path)    # C:\Users\Mohamed\Documents
```

---

## Raw Strings

Prefix `r` before the opening quote — backslashes are treated as literal characters, not escape sequences.

```python
# Without raw string — \n and \t are escape characters
path = "C:\new_folder\text_file"
print(path)
# C:
# ew_folder	ext_file    ← \n became newline, \t became tab

# With raw string — backslashes are literal
path = r"C:\new_folder\text_file"
print(path)
# C:\new_folder\text_file    ← exactly as written
```

**Where raw strings are essential:**

```python
# File paths on Windows
path = r"C:\Users\Mohamed\Documents\report.pdf"

# Regular expressions — backslashes have meaning in regex
import re
pattern = r"\d+\.\d+"    # matches decimal numbers like "3.14"
# Without r: "\\d+\\.\\d+" — ugly and error-prone

# Raw strings with triple quotes
text = r"""
first\line
second\line
"""
print(text)   # backslashes are literal
```

**Raw strings cannot end with a single backslash:**

```python
r"hello\"    # SyntaxError — a raw string can't end with odd number of backslashes
r"hello\\"   # ok — two backslashes at end
```

---

## Putting It All Together

```python
# A realistic string processing example
raw_data = "  Mohamed Salman , 25 , Chennai  "

# Clean whitespace
cleaned = raw_data.strip()
print(cleaned)    # "Mohamed Salman , 25 , Chennai"

# Split into parts
parts = cleaned.split(",")
print(parts)      # ['Mohamed Salman ', ' 25 ', ' Chennai  ']

# Strip each part
parts = [p.strip() for p in parts]
print(parts)      # ['Mohamed Salman', '25', 'Chennai']

# Access individual pieces
name = parts[0]
age  = int(parts[1])
city = parts[2]

# Build output
print(f"Name: {name.title()}")     # Name: Mohamed Salman
print(f"Age: {age}")               # Age: 25
print(f"City: {city.upper()}")     # City: CHENNAI
print(f"Is adult: {age >= 18}")    # Is adult: True
```

---

## Key Takeaways

- Strings are **immutable** — every method returns a new string; always capture the return value.
- Indexing starts at `0`. Negative indexing starts at `-1` from the end.
- Slicing `s[start:stop:step]` — `stop` is **excluded**. Slicing never raises `IndexError`.
- `s[::-1]` reverses any string.
- `.find()` returns `-1` if not found. `.index()` raises `ValueError`. Use `.find()` for safe searching.
- `.split()` with no argument handles multiple spaces correctly. `.split(" ")` does not.
- `"sep".join(list)` is the right way to build strings in a loop — not `+=`.
- f-strings are the modern standard. `.format()` is for reading older code.
- Raw strings `r"..."` are essential for file paths and regex patterns.