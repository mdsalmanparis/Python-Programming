Let's go. Full topic, nothing skipped.

---

## 1.2 Control Flow, Comprehensions & Generator Expressions

---

## List Comprehensions

A concise way to build a list from an iterable. More readable and faster than an equivalent `for` loop.

**Basic syntax: `[expression for item in iterable]`**

```python
# Loop version
squares = []
for n in range(1, 6):
    squares.append(n ** 2)
print(squares)    # [1, 4, 9, 16, 25]

# Comprehension version — same result
squares = [n ** 2 for n in range(1, 6)]
print(squares)    # [1, 4, 9, 16, 25]
```

**With a condition: `[expression for item in iterable if condition]`**

```python
# Only even numbers
evens = [n for n in range(10) if n % 2 == 0]
print(evens)    # [0, 2, 4, 6, 8]

# Only passing scores
scores = [88, 45, 92, 37, 67, 55, 78]
passing = [s for s in scores if s >= 60]
print(passing)    # [88, 92, 67, 78]

# Strings matching a condition
words = ["hello", "world", "python", "hi", "code"]
long_words = [w for w in words if len(w) > 4]
print(long_words)    # ['hello', 'world', 'python']
```

**Expression can be any computation:**

```python
names = ["  Mohamed  ", "  Priya  ", "  Arjun  "]
clean = [name.strip().title() for name in names]
print(clean)    # ['Mohamed', 'Priya', 'Arjun']

# Conditional expression (ternary) in the expression part
nums = [1, -2, 3, -4, 5]
abs_vals = [n if n >= 0 else -n for n in nums]
print(abs_vals)    # [1, 2, 3, 4, 5]

# Label each score
scores = [88, 45, 92, 37, 67]
labels = ["pass" if s >= 60 else "fail" for s in scores]
print(labels)    # ['pass', 'fail', 'pass', 'fail', 'pass']
```

**if/else in expression vs if as filter — critical difference:**

```python
nums = [1, 2, 3, 4, 5, 6]

# if at the END — filters items (only keeps matching)
evens = [n for n in nums if n % 2 == 0]
print(evens)    # [2, 4, 6]   — only even numbers, fewer items

# if/else in the EXPRESSION — transforms items (same count)
labelled = ["even" if n % 2 == 0 else "odd" for n in nums]
print(labelled)    # ['odd', 'even', 'odd', 'even', 'odd', 'even']  — same count

# CANNOT combine both forms like this — SyntaxError:
# ["even" if n % 2 == 0 else "odd" for n in nums if n > 2]  ← this IS valid
# ["even" for n in nums if n % 2 == 0 else "odd"]           ← this is NOT valid
```

---

### Nested Comprehensions

A comprehension inside another comprehension. Used for flattening or building matrices.

**Flattening a 2D list:**

```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Nested loop version
flat = []
for row in matrix:
    for item in row:
        flat.append(item)

# Comprehension version — read left to right, outer loop first
flat = [item for row in matrix for item in row]
print(flat)    # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**The reading order — matches the loop order exactly:**

```python
# for row in matrix    ← outer loop (written first)
# for item in row      ← inner loop (written second)
# item                 ← expression (at front)

[item for row in matrix for item in row]
```

**Building a matrix:**

```python
# 3x3 multiplication table
table = [[i * j for j in range(1, 4)] for i in range(1, 4)]
print(table)
# [[1, 2, 3], [2, 4, 6], [3, 6, 9]]

# Reading this: outer comprehension builds rows
# inner comprehension builds each row
```

**Nested with condition:**

```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Flatten but only keep values > 4
result = [item for row in matrix for item in row if item > 4]
print(result)    # [5, 6, 7, 8, 9]
```

**When to stop using nested comprehensions:**

```python
# Two levels — acceptable
flat = [item for row in matrix for item in row]

# Three levels — stop, use loops
# This is too hard to read:
result = [z for x in a for y in x for z in y]

# Write it as loops instead:
result = []
for x in a:
    for y in x:
        for z in y:
            result.append(z)
```

---

## Dict Comprehensions

Build a dictionary in one expression.

**Basic syntax: `{key_expr: value_expr for item in iterable}`**

```python
# Squares dict
squares = {n: n**2 for n in range(1, 6)}
print(squares)    # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# String lengths
words = ["apple", "banana", "cherry"]
lengths = {word: len(word) for word in words}
print(lengths)    # {'apple': 5, 'banana': 6, 'cherry': 6}
```

**With a condition:**

```python
scores = {"Mohamed": 88, "Priya": 92, "Arjun": 45, "Sara": 67}

# Only passing students
passing = {name: score for name, score in scores.items() if score >= 60}
print(passing)    # {'Mohamed': 88, 'Priya': 92, 'Sara': 67}
```

**Inverting a dict — swap keys and values:**

```python
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
print(inverted)    # {1: 'a', 2: 'b', 3: 'c'}

# Real-world use — build a reverse lookup
country_codes = {"India": "IN", "USA": "US", "UK": "GB"}
code_to_country = {code: country for country, code in country_codes.items()}
print(code_to_country)    # {'IN': 'India', 'US': 'USA', 'GB': 'UK'}
print(code_to_country["IN"])    # India
```

**Warning — inverting only works when values are unique and hashable:**

```python
d = {"a": 1, "b": 1}        # two keys map to same value
inverted = {v: k for k, v in d.items()}
print(inverted)    # {1: 'b'}  — 'a' was overwritten because both map to 1
```

**Transforming keys or values:**

```python
# Normalise keys to lowercase
raw = {"Name": "Mohamed", "AGE": 25, "CITY": "Chennai"}
normalised = {k.lower(): v for k, v in raw.items()}
print(normalised)    # {'name': 'Mohamed', 'age': 25, 'city': 'Chennai'}

# Apply transformation to values
prices = {"apple": 100, "banana": 50, "cherry": 200}
discounted = {item: round(price * 0.9, 2) for item, price in prices.items()}
print(discounted)    # {'apple': 90.0, 'banana': 45.0, 'cherry': 180.0}
```

---

## Set Comprehensions

Build a set — like a list comprehension but with `{}` and automatic deduplication.

**Basic syntax: `{expression for item in iterable}`**

```python
# Unique squares
squares = {n**2 for n in range(-3, 4)}
print(squares)    # {0, 1, 4, 9}  — duplicates removed, unordered

# Unique first characters
words = ["apple", "banana", "avocado", "cherry", "blueberry"]
first_chars = {w[0] for w in words}
print(first_chars)    # {'a', 'b', 'c'}  — unordered

# With condition
nums = range(20)
result = {n % 5 for n in nums}
print(result)    # {0, 1, 2, 3, 4}  — remainders when dividing by 5
```

**Practical use — finding unique values:**

```python
orders = [
    {"product": "laptop",  "city": "Chennai"},
    {"product": "phone",   "city": "Mumbai"},
    {"product": "laptop",  "city": "Delhi"},
    {"product": "tablet",  "city": "Chennai"},
]

# All unique products
products = {o["product"] for o in orders}
print(products)    # {'laptop', 'phone', 'tablet'}

# All unique cities
cities = {o["city"] for o in orders}
print(cities)    # {'Chennai', 'Mumbai', 'Delhi'}
```

---

## Generator Expressions

Syntax looks like a list comprehension but with `()` instead of `[]`. The key difference: **lazy evaluation** — items are computed one at a time, on demand.

```python
# List comprehension — builds entire list in memory immediately
lst = [n**2 for n in range(10)]
print(lst)          # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]  — all in RAM

# Generator expression — computes nothing yet
gen = (n**2 for n in range(10))
print(gen)          # <generator object <genexpr> at 0x...>  — no values yet
```

### Lazy Evaluation — Values Produced On Demand

```python
gen = (n**2 for n in range(5))

print(next(gen))    # 0   — computes first value
print(next(gen))    # 1   — computes second value
print(next(gen))    # 4   — computes third value
print(next(gen))    # 9
print(next(gen))    # 16
print(next(gen))    # StopIteration — exhausted
```

**Iterating with a for loop — most common:**

```python
gen = (n**2 for n in range(5))

for val in gen:
    print(val)
# 0, 1, 4, 9, 16
```

### Single-Use — Generators Are Exhausted After One Pass

```python
gen = (n**2 for n in range(5))

print(list(gen))    # [0, 1, 4, 9, 16]  — consumed
print(list(gen))    # []                 — exhausted, nothing left

# List comprehension can be iterated multiple times
lst = [n**2 for n in range(5)]
print(list(lst))    # [0, 1, 4, 9, 16]
print(list(lst))    # [0, 1, 4, 9, 16]  — still there
```

### Memory Efficiency — The Main Reason to Use Generators

```python
import sys

# List comprehension — stores all 1 million values in RAM
lst = [n**2 for n in range(1_000_000)]
print(sys.getsizeof(lst))    # ~8 MB

# Generator expression — stores almost nothing
gen = (n**2 for n in range(1_000_000))
print(sys.getsizeof(gen))    # ~120 bytes — regardless of size
```

**Generator works with `sum()`, `max()`, `min()`, `any()`, `all()`:**

```python
# No intermediate list created — memory efficient
total   = sum(n**2 for n in range(1_000_000))
maximum = max(n**2 for n in range(1_000_000))
exists  = any(n > 500 for n in range(1_000))
all_pos = all(n > 0 for n in range(1, 100))

# Notice: no extra parentheses needed when generator is sole argument
total = sum(n**2 for n in range(10))       # clean
total = sum((n**2 for n in range(10)))     # also valid, extra parens redundant
```

### When to Use Generator vs List Comprehension

```python
# Use LIST comprehension when:
# 1. You need to iterate multiple times
# 2. You need indexing (gen[0] doesn't work)
# 3. You need len()
# 4. You need to pass it to something that needs a sequence

squares_list = [n**2 for n in range(10)]
print(squares_list[3])      # 9    — indexing works
print(len(squares_list))    # 10   — len works
# iterate again:
for x in squares_list: pass   # works

# Use GENERATOR expression when:
# 1. You only iterate once
# 2. The iterable is large or infinite
# 3. You're passing directly to sum/max/min/any/all
# 4. Memory matters

total = sum(n**2 for n in range(10_000_000))   # never builds a list
first_match = next(x for x in data if x > 100)  # stops at first match
```

**`next()` with a generator — find first match without processing everything:**

```python
nums = [3, 7, 2, 9, 1, 5, 8]

# Find first number > 6
first = next((n for n in nums if n > 6), None)   # None = default if not found
print(first)    # 7  — stops after finding 7, doesn't check 2, 9, 1, 5, 8

# Without generator — processes entire list
matches = [n for n in nums if n > 6]
first = matches[0] if matches else None    # less efficient
```

---

## Walrus Operator `:=` — Assignment Expression (Python 3.8+)

Assigns a value to a variable **and** returns that value in the same expression. Pronounced "walrus" because `:=` looks like walrus eyes.

**Basic syntax: `name := expression`**

```python
# Without walrus — compute twice
import re
data = "User: Mohamed, Age: 25"

if re.search(r"\d+", data):
    match = re.search(r"\d+", data)    # computed again
    print(match.group())

# With walrus — compute once
if match := re.search(r"\d+", data):
    print(match.group())    # 25  — match is assigned AND tested in one step
```

### Walrus in `while` Loops

The most natural use — read-process loops without repeating the read call.

```python
# Without walrus — repetition
line = input("Enter text (or 'quit'): ")
while line != "quit":
    print(f"You said: {line}")
    line = input("Enter text (or 'quit'): ")    # repeated

# With walrus — clean
while (line := input("Enter text (or 'quit'): ")) != "quit":
    print(f"You said: {line}")
```

**Reading a file in chunks:**

```python
CHUNK_SIZE = 4096

with open("large_file.txt", "r") as f:
    # Without walrus
    chunk = f.read(CHUNK_SIZE)
    while chunk:
        process(chunk)
        chunk = f.read(CHUNK_SIZE)    # repeated

    # With walrus
    while chunk := f.read(CHUNK_SIZE):
        process(chunk)
```

### Walrus in Comprehensions — Avoid Recomputation

```python
import math

nums = [1, 4, 9, 16, 25, -1, -4]

# Without walrus — math.sqrt() called twice for filtered items
results = [math.sqrt(n) for n in nums if n >= 0]

# With walrus — compute sqrt once, reuse the value
results = [root for n in nums if (root := math.sqrt(n)) > 1 and n >= 0]
# Careful — order matters: n >= 0 must be checked before math.sqrt(n)

# Better example — expensive computation used in both filter and expression
def expensive(x):
    return x ** 2 + x + 1   # imagine this is slow

nums = range(20)

# Without walrus — expensive() called twice for kept items
result = [expensive(n) for n in nums if expensive(n) > 10]

# With walrus — expensive() called once per item
result = [val for n in nums if (val := expensive(n)) > 10]
print(result)
```

**Walrus leaks the variable into the enclosing scope:**

```python
# In a comprehension, the loop variable is local
[x for x in range(5)]
# print(x)   # NameError in Python 3

# But walrus variable leaks out
[y := x**2 for x in range(5)]
print(y)    # 16  — last value assigned by walrus
# This can be useful or surprising — be aware
```

**When NOT to use walrus — don't sacrifice readability:**

```python
# Unreadable — walrus for the sake of it
result = [y for x in data if (y := f(x)) > 0 and (z := g(y)) < 100]

# Just use a loop
result = []
for x in data:
    y = f(x)
    if y > 0:
        z = g(y)
        if z < 100:
            result.append(y)
```

---

## `sorted()` vs `.sort()`

### The Core Difference

```python
nums = [3, 1, 4, 1, 5, 9, 2, 6]

# sorted() — returns NEW list, original unchanged
new = sorted(nums)
print(new)     # [1, 1, 2, 3, 4, 5, 6, 9]
print(nums)    # [3, 1, 4, 1, 5, 9, 2, 6]  — unchanged

# .sort() — modifies IN-PLACE, returns None
nums.sort()
print(nums)    # [1, 1, 2, 3, 4, 5, 6, 9]  — changed
result = [3,1,2].sort()
print(result)  # None  — the trap
```

**`sorted()` works on any iterable. `.sort()` only on lists:**

```python
sorted("python")          # ['h', 'n', 'o', 'p', 't', 'y']
sorted((3, 1, 2))         # [1, 2, 3]   — input tuple, output list
sorted({3, 1, 2})         # [1, 2, 3]   — input set
sorted({"b":2,"a":1})     # ['a', 'b']  — sorts dict keys

(3,1,2).sort()            # AttributeError — tuples have no .sort()
```

### `key=` Parameter — Custom Sort Logic

`key` takes a function. Python calls it on each element and sorts by the **returned value**, not the element itself.

```python
words = ["banana", "fig", "cherry", "apple", "kiwi"]

# Sort by length
print(sorted(words, key=len))
# ['fig', 'kiwi', 'apple', 'banana', 'cherry']

# Sort by last character
print(sorted(words, key=lambda w: w[-1]))
# ['banana', 'apple', 'fig', 'cherry', 'kiwi']

# Sort alphabetically, case-insensitive
mixed = ["Banana", "apple", "Cherry", "fig"]
print(sorted(mixed))                    # ['Banana', 'Cherry', 'apple', 'fig']  — uppercase first
print(sorted(mixed, key=str.lower))     # ['apple', 'Banana', 'Cherry', 'fig']  — true alpha
```

**Sorting dicts by value — the most common real-world use:**

```python
scores = {"Mohamed": 88, "Priya": 92, "Arjun": 74, "Sara": 67}

# Sort by score ascending
ranked = sorted(scores.items(), key=lambda item: item[1])
print(ranked)
# [('Sara', 67), ('Arjun', 74), ('Mohamed', 88), ('Priya', 92)]

# Sort by score descending
ranked = sorted(scores.items(), key=lambda item: item[1], reverse=True)
print(ranked)
# [('Priya', 92), ('Mohamed', 88), ('Arjun', 74), ('Sara', 67)]

# Get sorted keys only
sorted_names = sorted(scores, key=scores.get, reverse=True)
print(sorted_names)    # ['Priya', 'Mohamed', 'Arjun', 'Sara']
```

**Multi-key sort — tuple as key:**

```python
students = [
    {"name": "Mohamed", "score": 88, "age": 25},
    {"name": "Priya",   "score": 92, "age": 22},
    {"name": "Arjun",   "score": 88, "age": 23},
    {"name": "Sara",    "score": 92, "age": 25},
]

# Sort by score DESC, then by name ASC
ranked = sorted(students, key=lambda s: (-s["score"], s["name"]))
for s in ranked:
    print(f"{s['name']:10} {s['score']}")
# Priya      92
# Sara       92
# Arjun      88
# Mohamed    88
```

**`operator.itemgetter` and `operator.attrgetter` — faster than lambda:**

```python
from operator import itemgetter, attrgetter

# Equivalent to lambda item: item[1]
ranked = sorted(scores.items(), key=itemgetter(1))

# Multiple keys
ranked = sorted(students, key=itemgetter("score", "name"))

# For objects with attributes
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score

students = [Student("Mohamed", 88), Student("Priya", 92)]
sorted_students = sorted(students, key=attrgetter("score"))
```

### Stability Guarantee

Python's sort is **stable** — equal elements keep their original relative order.

```python
data = [
    ("Mohamed", 88),
    ("Priya",   88),   # same score as Mohamed
    ("Arjun",   92),
]

# Sort by score only
sorted_data = sorted(data, key=lambda x: x[1])
print(sorted_data)
# [('Mohamed', 88), ('Priya', 88), ('Arjun', 92)]
# Mohamed comes before Priya — their original order is preserved
```

**Stability enables multi-pass sorting:**

```python
# Sort by name first, then by score
# Because sort is stable, equal scores keep the name-sorted order

data = [("Mohamed", 88), ("Priya", 92), ("Arjun", 88), ("Sara", 92)]

# First sort by name
data = sorted(data, key=lambda x: x[0])
# [('Arjun', 88), ('Mohamed', 88), ('Priya', 92), ('Sara', 92)]

# Then sort by score — equal scores keep name order from previous sort
data = sorted(data, key=lambda x: x[1])
# [('Arjun', 88), ('Mohamed', 88), ('Priya', 92), ('Sara', 92)]
# Arjun before Mohamed (both 88, kept alphabetical order from first sort)
# Priya before Sara (both 92, kept alphabetical order)
```

### `functools.cmp_to_key()` — For Complex Comparison Logic

When you need a comparison function (like `cmp` in old Python 2) instead of a key function.

```python
from functools import cmp_to_key

# Comparison function: returns negative, zero, or positive
# negative → a comes first
# zero     → equal
# positive → b comes first

def compare_scores(a, b):
    if a["score"] != b["score"]:
        return b["score"] - a["score"]    # higher score first
    return len(a["name"]) - len(b["name"])  # shorter name first on tie

students = [
    {"name": "Mohamed", "score": 88},
    {"name": "Priya",   "score": 92},
    {"name": "Arjun",   "score": 88},
]

ranked = sorted(students, key=cmp_to_key(compare_scores))
for s in ranked:
    print(f"{s['name']:10} {s['score']}")
# Priya      92
# Arjun      88   — shorter name than Mohamed
# Mohamed    88
```

**When `cmp_to_key` is actually needed:**

```python
# Version string sorting — "1.10" > "1.9" but "1.10" < "1.9" lexicographically
versions = ["1.10", "1.9", "1.2", "2.0", "1.11"]

# Wrong — lexicographic
print(sorted(versions))    # ['1.10', '1.11', '1.2', '1.9', '2.0']

# Correct — parse version parts
print(sorted(versions, key=lambda v: list(map(int, v.split(".")))))
# ['1.2', '1.9', '1.10', '1.11', '2.0']

# cmp_to_key version — shows the pattern
import functools
def version_cmp(a, b):
    a_parts = list(map(int, a.split(".")))
    b_parts = list(map(int, b.split(".")))
    return (a_parts > b_parts) - (a_parts < b_parts)   # -1, 0, or 1

print(sorted(versions, key=cmp_to_key(version_cmp)))
```

---

## `zip(strict=True)` — Python 3.10+

Standard `zip()` silently stops at the shortest iterable. `strict=True` makes mismatched lengths an error.

```python
names  = ["Alice", "Bob", "Charlie"]
scores = [88, 92, 75]

# Default — silent truncation
short_scores = [88, 92]
for name, score in zip(names, short_scores):
    print(name, score)
# Alice 88
# Bob 92
# Charlie is silently dropped — you'd never know

# strict=True — raises ValueError on mismatch
for name, score in zip(names, short_scores, strict=True):
    print(name, score)
# Alice 88
# Bob 92
# ValueError: zip() has arguments with different lengths
```

**When to use `strict=True`:**

```python
# Use when mismatched lengths means a bug — data integrity matters
keys   = ["name", "age", "city"]
values = ["Mohamed", 25]              # someone forgot city

d = dict(zip(keys, values))
print(d)    # {'name': 'Mohamed', 'age': 25}  — city silently missing

# Safer
d = dict(zip(keys, values, strict=True))
# ValueError: zip() has arguments with different lengths

# Use default zip when stopping at shortest is intentional
for a, b in zip(infinite_stream, finite_list):   # stops when finite_list ends
    process(a, b)
```

**Checking lengths before zip — the alternative:**

```python
def safe_zip(a, b):
    if len(a) != len(b):
        raise ValueError(f"length mismatch: {len(a)} vs {len(b)}")
    return zip(a, b)

# zip(strict=True) does this for you — use it
```

---

## Putting It All Together

```python
from functools import cmp_to_key

# Dataset
students = [
    {"name": "Mohamed", "scores": [88, 92, 85], "city": "Chennai"},
    {"name": "Priya",   "scores": [95, 88, 92], "city": "Mumbai"},
    {"name": "Arjun",   "scores": [74, 68, 80], "city": "Delhi"},
    {"name": "Sara",    "scores": [92, 95, 88], "city": "Chennai"},
    {"name": "Vikram",  "scores": [55, 60, 58], "city": "Mumbai"},
]

# 1. Add average score using dict comprehension
averages = {
    s["name"]: round(sum(s["scores"]) / len(s["scores"]), 1)
    for s in students
}
print("Averages:", averages)

# 2. Passing students — generator expression fed to list
passing_names = list(
    s["name"] for s in students
    if sum(s["scores"]) / len(s["scores"]) >= 70
)
print("Passing:", passing_names)

# 3. Unique cities — set comprehension
cities = {s["city"] for s in students}
print("Cities:", cities)

# 4. All scores flat — nested list comprehension
all_scores = [score for s in students for score in s["scores"]]
print("All scores:", all_scores)

# 5. Memory-efficient total using generator
grand_total = sum(
    score
    for s in students
    for score in s["scores"]
)
print("Grand total:", grand_total)

# 6. Sort by average descending, then name ascending
ranked = sorted(
    students,
    key=lambda s: (-sum(s["scores"]) / len(s["scores"]), s["name"])
)
print("\nRankings:")
for rank, s in enumerate(ranked, start=1):
    avg = averages[s["name"]]
    print(f"  {rank}. {s['name']:10} avg={avg}")

# 7. Walrus — find first student with avg > 90 without building full list
top = next(
    (s["name"] for s in students
     if (avg := sum(s["scores"]) / len(s["scores"])) > 90),
    None
)
print(f"\nFirst student with avg > 90: {top}")

# 8. Zip names with averages strictly
names_list = [s["name"] for s in students]
avg_list   = [averages[n] for n in names_list]

print("\nName → Average:")
for name, avg in zip(names_list, avg_list, strict=True):
    print(f"  {name}: {avg}")
```

---

## Key Takeaways

- List comprehension: `[expr for x in it if cond]` — filter is at the **end**. Ternary in the **expression** is different — it transforms, not filters.
- Nested comprehension: outer loop written first — `[item for row in matrix for item in row]`. Two levels max for readability.
- Dict comprehension: `{k: v for k, v in items.items()}`. Inverting: `{v: k for k, v in d.items()}` — only works when values are unique.
- Set comprehension: `{expr for x in it}` — automatic deduplication, unordered.
- Generator expression: `(expr for x in it)` — lazy, memory-efficient, **single-use**. Default when feeding `sum()`, `max()`, `any()`, `all()`.
- Walrus `:=` — compute once, use in condition and expression. Best in `while` loops and comprehensions with expensive operations.
- `sorted()` — returns new list, works on any iterable. `.sort()` — in-place, lists only, returns `None`.
- `key=` receives a function — Python sorts by the **return value** of that function, not the element.
- Python sort is **stable** — equal elements keep their original relative order.
- `cmp_to_key()` — for when you need a full comparison function (`-1`, `0`, `1`) instead of a key.
- `zip(strict=True)` — raises `ValueError` on length mismatch. Use when unequal lengths means a bug.