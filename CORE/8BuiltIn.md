Let's go. Full topic, nothing skipped.

---

## 0.9 Built-in Functions You Must Know

---

## `print()` and `input()` — I/O

### `print()`

Outputs to the console. More powerful than most beginners realise.

```python
# Basic
print("hello")             # hello
print(42)                  # 42
print(3.14)                # 3.14
print(True)                # True
print(None)                # None

# Multiple arguments — separated by space by default
print("name:", "Mohamed")          # name: Mohamed
print(1, 2, 3, 4, 5)              # 1 2 3 4 5
print("a", "b", "c")              # a b c

# sep= — custom separator
print("a", "b", "c", sep=", ")    # a, b, c
print("a", "b", "c", sep="")      # abc
print("a", "b", "c", sep="\n")    # a
                                   # b
                                   # c
print(2024, 6, 15, sep="-")       # 2024-6-15

# end= — what to print at the end (default is \n)
print("hello", end=" ")
print("world")             # hello world  — on same line

print("loading", end="")
print("...", end="")
print("done")              # loading...done

# Printing multiple items in a loop on one line
for i in range(5):
    print(i, end=" ")      # 0 1 2 3 4
print()                    # newline at end

# file= — redirect output (used in real code to write to stderr)
import sys
print("error occurred", file=sys.stderr)

# flush= — force immediate output (useful in progress indicators)
print("processing...", end="", flush=True)
```

---

### `input()`

Reads a line of text from the user. **Always returns a string.**

```python
name = input("Enter your name: ")
print(f"Hello, {name}!")

# input always returns str — convert for numbers
age_str = input("Enter your age: ")
age = int(age_str)
print(f"Next year you'll be {age + 1}")

# One-liner conversion
age = int(input("Enter your age: "))

# Safe input with error handling
while True:
    raw = input("Enter a number: ")
    if raw.isdigit():
        number = int(raw)
        break
    print("That's not a valid number, try again")
```

**`input()` always returns `str` — this bites everyone at least once:**

```python
x = input("Enter a number: ")    # user types 5
y = input("Enter a number: ")    # user types 3
print(x + y)                     # 53  — string concatenation, not addition

# Correct
x = int(input("Enter a number: "))
y = int(input("Enter a number: "))
print(x + y)                     # 8
```

---

## `len()` — Length of Any Sequence

```python
print(len("hello"))              # 5
print(len([1, 2, 3, 4]))        # 4
print(len((10, 20, 30)))        # 3
print(len({1, 2, 3}))           # 3
print(len({"a": 1, "b": 2}))    # 2  — counts keys
print(len(range(100)))           # 100
print(len(""))                   # 0
print(len([]))                   # 0

# Common use — last index
s = "Python"
print(s[len(s) - 1])    # n  — last char (use s[-1] in practice)

# Guard against empty
items = []
if len(items) == 0:    # works
    print("empty")

if not items:          # Pythonic — same thing
    print("empty")
```

---

## `range()` — Integer Sequences

Already covered in 0.7 but consolidated here for completeness.

```python
range(5)          # 0, 1, 2, 3, 4
range(1, 6)       # 1, 2, 3, 4, 5
range(0, 10, 2)   # 0, 2, 4, 6, 8
range(10, 0, -1)  # 10, 9, 8, 7, 6, 5, 4, 3, 2, 1
range(0, -10, -2) # 0, -2, -4, -6, -8

# range is lazy — doesn't store all values in memory
r = range(1_000_000)
print(type(r))      # <class 'range'>
print(r[0])         # 0      — supports indexing
print(r[-1])        # 999999
print(999 in r)     # True   — O(1) membership test
print(len(r))       # 1000000

# Convert to list when you need a real list
print(list(range(5)))     # [0, 1, 2, 3, 4]
print(list(range(5, 0, -1)))  # [5, 4, 3, 2, 1]
```

---

## `type()` and `isinstance()`

### `type()` — exact type check

```python
print(type(42))          # <class 'int'>
print(type(3.14))        # <class 'float'>
print(type("hello"))     # <class 'str'>
print(type(True))        # <class 'bool'>
print(type(None))        # <class 'NoneType'>
print(type([1, 2]))      # <class 'list'>
print(type((1, 2)))      # <class 'tuple'>
print(type({1, 2}))      # <class 'set'>
print(type({"a": 1}))    # <class 'dict'>

# Exact comparison
print(type(42) == int)        # True
print(type(True) == bool)     # True
print(type(True) == int)      # False  — exact match only
```

### `isinstance()` — preferred in real code

```python
print(isinstance(42, int))           # True
print(isinstance(3.14, float))       # True
print(isinstance("hi", str))         # True
print(isinstance(True, bool))        # True
print(isinstance(True, int))         # True  — bool IS a subclass of int
print(isinstance(42, (int, float)))  # True  — check against multiple types

# type() vs isinstance() — the key difference
x = True
print(type(x) == int)        # False — exact match, bool is not int
print(isinstance(x, int))    # True  — respects inheritance chain
```

**Why `isinstance()` is preferred:**

```python
# In real code, you often want "is this number-like?"
def double(n):
    if isinstance(n, (int, float)):   # accepts both int and float
        return n * 2
    raise TypeError(f"Expected number, got {type(n).__name__}")

print(double(5))      # 10
print(double(3.14))   # 6.28
double("5")           # TypeError: Expected number, got str
```

---

## Type Conversion Functions

All covered in detail in 0.2, consolidated here with edge cases.

```python
# int()
int("42")           # 42
int(3.99)           # 3     — truncates, does NOT round
int(True)           # 1
int(False)          # 0
int("FF", 16)       # 255   — parse hex string
int("101", 2)       # 5     — parse binary string
int("0xFF", 16)     # 255

# float()
float("3.14")       # 3.14
float(42)           # 42.0
float("inf")        # inf
float("nan")        # nan

# str()
str(42)             # "42"
str(3.14)           # "3.14"
str(True)           # "True"
str(None)           # "None"
str([1, 2, 3])      # "[1, 2, 3]"

# bool()
bool(0)             # False
bool("")            # False
bool([])            # False
bool(None)          # False
bool(1)             # True
bool("0")           # True   — non-empty string
bool([0])           # True   — non-empty list

# list()
list("hello")       # ['h', 'e', 'l', 'l', 'o']
list((1, 2, 3))     # [1, 2, 3]
list({1, 2, 3})     # [1, 2, 3]  — order not guaranteed
list(range(5))      # [0, 1, 2, 3, 4]
list({"a":1,"b":2}) # ['a', 'b']  — dict → list of keys

# tuple()
tuple([1, 2, 3])    # (1, 2, 3)
tuple("hello")      # ('h', 'e', 'l', 'l', 'o')
tuple(range(3))     # (0, 1, 2)

# set()
set([1, 2, 2, 3])   # {1, 2, 3}  — removes duplicates
set("hello")        # {'h', 'e', 'l', 'o'}
set((1, 1, 2))      # {1, 2}

# dict()
dict(name="Mohamed", age=25)             # {'name': 'Mohamed', 'age': 25}
dict([("a", 1), ("b", 2)])              # {'a': 1, 'b': 2}
dict(zip(["a","b"], [1, 2]))            # {'a': 1, 'b': 2}
```

---

## `min()` and `max()`

```python
# On direct arguments
print(min(3, 1, 4, 1, 5))      # 1
print(max(3, 1, 4, 1, 5))      # 5

# On an iterable
nums = [3, 1, 4, 1, 5, 9, 2]
print(min(nums))    # 1
print(max(nums))    # 9

# On strings — alphabetical / lexicographic order
print(min("apple", "banana", "cherry"))    # apple
print(max("apple", "banana", "cherry"))    # cherry

words = ["banana", "apple", "cherry"]
print(min(words))    # apple
print(max(words))    # cherry
```

### `key=` parameter — custom comparison rule

```python
words = ["banana", "fig", "cherry", "apple", "kiwi"]

# Shortest word
print(min(words, key=len))    # fig

# Longest word
print(max(words, key=len))    # banana — or cherry (tie goes to first found)

# Alphabetically by last character
print(min(words, key=lambda w: w[-1]))    # banana (ends with 'a')
print(max(words, key=lambda w: w[-1]))    # kiwi   (ends with 'i'... actually 'y')

# With dicts — most common real-world use
students = [
    {"name": "Mohamed", "score": 88},
    {"name": "Priya",   "score": 92},
    {"name": "Arjun",   "score": 74},
]

top    = max(students, key=lambda s: s["score"])
bottom = min(students, key=lambda s: s["score"])

print(top["name"])      # Priya
print(bottom["name"])   # Arjun

# Case-insensitive string comparison
names = ["banana", "Apple", "cherry"]
print(min(names, key=str.lower))    # Apple  — treats as "apple" for comparison
```

**`min()` / `max()` on empty iterable raises `ValueError`:**

```python
min([])    # ValueError: min() arg is an empty sequence

# Safe version with default
print(min([], default=0))    # 0
print(max([], default=-1))   # -1
```

---

## `sum()`

```python
print(sum([1, 2, 3, 4, 5]))         # 15
print(sum((10, 20, 30)))             # 60
print(sum(range(1, 101)))            # 5050  — sum of 1 to 100
print(sum([1.5, 2.5, 3.0]))         # 7.0
print(sum([]))                       # 0     — empty iterable returns 0

# start= — initial value added to the sum
print(sum([1, 2, 3], 100))          # 106   — starts from 100
print(sum([1, 2, 3], start=100))    # 106

# sum() only works on numbers
sum(["a", "b", "c"])    # TypeError — use "".join() for strings
```

**Common patterns:**

```python
scores = [88, 92, 75, 84, 91]

total   = sum(scores)
average = sum(scores) / len(scores)
print(average)    # 86.0

# Sum with a condition — sum only passing scores
passing = sum(s for s in scores if s >= 80)
print(passing)    # 88 + 92 + 84 + 91 = 355

# Sum a field from a list of dicts
orders = [{"amount": 250}, {"amount": 180}, {"amount": 320}]
total = sum(o["amount"] for o in orders)
print(total)    # 750
```

---

## `abs()` — Absolute Value

```python
print(abs(5))       # 5
print(abs(-5))      # 5
print(abs(-3.14))   # 3.14
print(abs(0))       # 0

# Complex numbers — returns magnitude
print(abs(3 + 4j))  # 5.0  — sqrt(3² + 4²)
```

**Common uses:**

```python
# Distance between two points
def distance(a, b):
    return abs(a - b)

print(distance(10, 3))    # 7
print(distance(3, 10))    # 7  — same, order doesn't matter

# Comparing floats (from 0.2)
result = 0.1 + 0.2
print(abs(result - 0.3) < 1e-9)    # True — "close enough"

# Finding closest value
target = 50
values = [30, 45, 55, 80, 12]
closest = min(values, key=lambda x: abs(x - target))
print(closest)    # 45
```

---

## `round()`

```python
print(round(3.14159))        # 3      — rounds to nearest int
print(round(3.14159, 2))     # 3.14   — 2 decimal places
print(round(3.14159, 4))     # 3.1416
print(round(3.5))            # 4
print(round(2.5))            # 2      — banker's rounding (rounds to even)
print(round(4.5))            # 4      — banker's rounding (rounds to even)
print(round(-3.14159, 2))    # -3.14
print(round(1234, -2))       # 1200   — negative ndigits rounds to left of decimal
print(round(1678, -2))       # 1700
```

**Banker's rounding — Python rounds to even on .5:**

```python
print(round(0.5))    # 0  — rounds to even (0 is even)
print(round(1.5))    # 2  — rounds to even (2 is even)
print(round(2.5))    # 2  — rounds to even (2 is even)
print(round(3.5))    # 4  — rounds to even (4 is even)
```

This is the IEEE 754 standard. It's not a bug. For always-round-up behaviour use `math.ceil()` or `decimal.Decimal`.

**`round()` for display vs for calculation:**

```python
price = 19.99
tax   = 0.18

total = price * (1 + tax)
print(total)              # 23.5882  — raw float
print(round(total, 2))    # 23.59    — for display

# For money calculations use the decimal module in real code
from decimal import Decimal
price = Decimal("19.99")
tax   = Decimal("0.18")
print(price * (1 + tax))  # 23.5882  — exact decimal arithmetic
```

---

## `sorted()` and `reversed()`

### `sorted()` — returns a new sorted list

```python
nums = [3, 1, 4, 1, 5, 9, 2]

print(sorted(nums))                      # [1, 1, 2, 3, 4, 5, 9]
print(sorted(nums, reverse=True))        # [9, 5, 4, 3, 2, 1, 1]
print(nums)                              # [3, 1, 4, 1, 5, 9, 2]  — unchanged

# Works on any iterable — not just lists
print(sorted("python"))                  # ['h', 'n', 'o', 'p', 't', 'y']
print(sorted((3, 1, 2)))                 # [1, 2, 3]  — input tuple, output list
print(sorted({3, 1, 2}))                 # [1, 2, 3]

# key= parameter
words = ["banana", "fig", "cherry", "apple"]
print(sorted(words, key=len))            # ['fig', 'apple', 'banana', 'cherry']
print(sorted(words, key=str.lower))      # ['apple', 'banana', 'cherry', 'fig']

# Sort dicts by value
scores = {"Mohamed": 88, "Priya": 92, "Arjun": 74}
print(sorted(scores, key=scores.get))              # ['Arjun', 'Mohamed', 'Priya']
print(sorted(scores, key=scores.get, reverse=True))# ['Priya', 'Mohamed', 'Arjun']

# Stable sort — equal elements keep original order
data = [("b", 2), ("a", 2), ("c", 1)]
print(sorted(data, key=lambda x: x[1]))
# [('c', 1), ('b', 2), ('a', 2)]  — b before a preserved
```

### `reversed()` — returns an iterator

```python
nums = [1, 2, 3, 4, 5]

rev = reversed(nums)
print(rev)              # <list_reverseiterator object>
print(list(rev))        # [5, 4, 3, 2, 1]

# Use directly in a for loop — no need to convert
for n in reversed(nums):
    print(n, end=" ")   # 5 4 3 2 1

# Works on sequences (list, tuple, str, range)
print(list(reversed("hello")))       # ['o', 'l', 'l', 'e', 'h']
print(list(reversed((1, 2, 3))))     # [3, 2, 1]
print(list(reversed(range(5))))      # [4, 3, 2, 1, 0]
```

**`reversed()` vs `[::-1]`:**

```python
nums = [1, 2, 3, 4, 5]

list(reversed(nums))   # iterator — memory efficient, no copy
nums[::-1]             # creates a new list immediately

# For large sequences, reversed() is more memory efficient
# For small sequences, [::-1] is fine and more readable
```

---

## `enumerate()` and `zip()`

Already covered deeply in 0.7 — quick summary here.

### `enumerate()`

```python
fruits = ["apple", "banana", "cherry"]

list(enumerate(fruits))           # [(0, 'apple'), (1, 'banana'), (2, 'cherry')]
list(enumerate(fruits, start=1))  # [(1, 'apple'), (2, 'banana'), (3, 'cherry')]

for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")
# 1. apple
# 2. banana
# 3. cherry
```

### `zip()`

```python
names  = ["Alice", "Bob", "Charlie"]
scores = [88, 92, 75]
cities = ["Chennai", "Mumbai", "Delhi"]

list(zip(names, scores))    # [('Alice', 88), ('Bob', 92), ('Charlie', 75)]

for name, score, city in zip(names, scores, cities):
    print(f"{name} | {score} | {city}")

# Unzip — transpose rows and columns
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]
numbers, letters = zip(*pairs)
print(numbers)    # (1, 2, 3)
print(letters)    # ('a', 'b', 'c')
```

---

## `map()` — Apply Function to Every Element

Returns a lazy iterator. Apply a function to each item in an iterable.

```python
nums = [1, 2, 3, 4, 5]

# map(function, iterable)
doubled = map(lambda x: x * 2, nums)
print(doubled)          # <map object>  — lazy
print(list(doubled))    # [2, 4, 6, 8, 10]

# With a named function
def square(n):
    return n * n

print(list(map(square, nums)))    # [1, 4, 9, 16, 25]

# With built-in functions
strings = ["1", "2", "3", "4"]
numbers = list(map(int, strings))   # convert each string to int
print(numbers)    # [1, 2, 3, 4]

names = ["alice", "bob", "charlie"]
print(list(map(str.title, names)))  # ['Alice', 'Bob', 'Charlie']

# map with two iterables
a = [1, 2, 3]
b = [10, 20, 30]
print(list(map(lambda x, y: x + y, a, b)))    # [11, 22, 33]
```

**`map()` vs list comprehension — which to use:**

```python
nums = [1, 2, 3, 4, 5]

# map() — functional style
list(map(lambda x: x * 2, nums))

# List comprehension — more Pythonic and readable
[x * 2 for x in nums]

# Prefer list comprehensions for most cases
# map() shines when you already have a named function
list(map(int, ["1", "2", "3"]))         # clean
[int(x) for x in ["1", "2", "3"]]      # also fine
```

---

## `filter()` — Keep Elements Where Function Returns True

Returns a lazy iterator. Keeps only items where the function returns `True`.

```python
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# filter(function, iterable)
evens = filter(lambda x: x % 2 == 0, nums)
print(list(evens))    # [2, 4, 6, 8, 10]

# With a named function
def is_positive(n):
    return n > 0

values = [-3, -1, 0, 2, 5, -2, 8]
print(list(filter(is_positive, values)))    # [2, 5, 8]

# Filter strings
words = ["hello", "", "world", "", "python", ""]
non_empty = list(filter(None, words))       # None as function = filter falsy values
print(non_empty)    # ['hello', 'world', 'python']

# filter(None, iterable) — removes all falsy values
mixed = [0, 1, "", "hello", None, [], [1, 2], False, True]
print(list(filter(None, mixed)))
# [1, 'hello', [1, 2], True]
```

**`filter()` vs list comprehension:**

```python
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# filter()
list(filter(lambda x: x % 2 == 0, nums))

# List comprehension — more readable
[x for x in nums if x % 2 == 0]

# Prefer comprehensions — filter() with lambda is almost always less readable
# filter() is useful with existing named functions
list(filter(str.isupper, ["Hello", "WORLD", "python"]))    # ['WORLD']
```

---

## `any()` and `all()` — Boolean Checks

### `any()` — True if at least one element is truthy

```python
print(any([False, False, True, False]))    # True
print(any([False, False, False]))          # False
print(any([]))                             # False  — empty is False

# Practical use
scores = [45, 38, 52, 67, 29]
print(any(s >= 60 for s in scores))    # True  — at least one passes

names = ["alice", "Bob", "charlie"]
print(any(n[0].isupper() for n in names))    # True  — at least one starts uppercase
```

### `all()` — True if every element is truthy

```python
print(all([True, True, True]))             # True
print(all([True, False, True]))            # False
print(all([]))                             # True   — vacuously true

# Practical use
scores = [65, 72, 88, 91]
print(all(s >= 60 for s in scores))    # True  — everyone passed

passwords = ["abc123", "secure!", "pass"]
print(all(len(p) >= 8 for p in passwords))    # False  — "pass" is too short
```

### Short-circuit behaviour

```python
# any() stops at first True
def check(x):
    print(f"checking {x}")
    return x > 3

print(any(check(x) for x in [1, 2, 5, 6]))
# checking 1
# checking 2
# checking 5   ← stops here, 5 > 3 is True
# True

# all() stops at first False
print(all(check(x) for x in [5, 6, 2, 8]))
# checking 5
# checking 6
# checking 2   ← stops here, 2 > 3 is False
# False
```

**`any()` / `all()` with generators — memory efficient:**

```python
# No intermediate list created
result = any(x > 100 for x in range(1_000_000))    # stops as soon as found
result = all(x >= 0 for x in range(1_000_000))     # checks all (all are >= 0)
```

---

## `open()` — File I/O

Detailed coverage in Phase 2 (topic 2.2 `pathlib`, `os`, `shutil`). The essentials here:

```python
# Always use with statement — auto-closes the file
with open("file.txt", "r") as f:
    content = f.read()

with open("file.txt", "w") as f:
    f.write("hello world")

with open("file.txt", "a") as f:
    f.append("new line\n")    # append mode

# Modes
# "r"  — read (default)
# "w"  — write (creates or overwrites)
# "a"  — append
# "rb" — read binary
# "wb" — write binary

# Always specify encoding for text files
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()
```

Full coverage — all read/write methods, `pathlib`, directory operations — comes in Phase 2.

---

## `help()` and `dir()`

### `help()` — inline documentation

```python
help(print)       # full documentation for print
help(len)         # documentation for len
help(str)         # all str methods with descriptions
help(list)        # all list methods with descriptions
help([])          # same as help(list)
help(str.split)   # documentation for a specific method
```

### `dir()` — list all attributes and methods of an object

```python
print(dir("hello"))     # all string methods and attributes
print(dir([]))          # all list methods
print(dir({}))          # all dict methods

# Filter out dunder methods to see only public ones
public = [x for x in dir("hello") if not x.startswith("_")]
print(public)
# ['capitalize', 'casefold', 'center', 'count', 'encode', 'endswith',
#  'expandtabs', 'find', 'format', ...]

# On a module
import math
print(dir(math))    # all functions and constants in math module
```

**`help()` and `dir()` together — the discovery workflow:**

```python
# You know something exists but don't remember the exact method
print(dir("hello"))              # find the method name

# Then get details
help(str.center)                 # how does .center() work?

# Output: center(self, width, fillchar=' ', /)
#   Return a centered string of length width.
```

---

## Putting It All Together

```python
# Realistic example combining many built-ins
raw_data = [
    "  Mohamed, 88, Chennai  ",
    "  Priya, 92, Mumbai     ",
    "  Arjun, 45, Delhi      ",
    "  Sara, 67, Chennai     ",
    "  Vikram, 55, Mumbai    ",
    "  Divya, 91, Delhi      ",
]

# Parse into list of dicts
students = []
for line in raw_data:
    parts = [p.strip() for p in line.strip().split(",")]
    students.append({
        "name":  parts[0],
        "score": int(parts[1]),
        "city":  parts[2]
    })

scores = [s["score"] for s in students]

# Stats using built-ins
print(f"Count:   {len(students)}")
print(f"Total:   {sum(scores)}")
print(f"Average: {round(sum(scores) / len(scores), 1)}")
print(f"Highest: {max(scores)}")
print(f"Lowest:  {min(scores)}")

# Checks
print(f"All passed:     {all(s >= 50 for s in scores)}")
print(f"Any distinction:{any(s >= 90 for s in scores)}")

# Top student
top = max(students, key=lambda s: s["score"])
print(f"Top: {top['name']} ({top['score']})")

# Sorted by score descending
ranked = sorted(students, key=lambda s: s["score"], reverse=True)
print("\nRankings:")
for rank, student in enumerate(ranked, start=1):
    name  = student["name"]
    score = student["score"]
    city  = student["city"]
    print(f"  {rank}. {name:10} {score:3}  {city}")

# Filter passing students
passing = list(filter(lambda s: s["score"] >= 60, students))
print(f"\nPassed: {len(passing)}/{len(students)}")

# Convert scores to grades using map
def to_grade(score):
    return "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "D" if score >= 60 else "F"

grades = list(map(to_grade, scores))
print(f"Grades: {grades}")
```

---

## Key Takeaways

- `input()` always returns `str` — always convert before arithmetic.
- `isinstance(x, int)` is preferred over `type(x) == int` — it respects inheritance.
- `min()` / `max()` on empty iterable raises `ValueError` — use `default=` parameter.
- `round(2.5)` is `2`, not `3` — Python uses banker's rounding (round to even).
- `map()` and `filter()` return lazy iterators — wrap in `list()` to see results.
- Prefer list comprehensions over `map(lambda...)` and `filter(lambda...)` — more readable.
- `any([])` is `False`. `all([])` is `True` — know these edge cases.
- `any()` and `all()` short-circuit — they stop as soon as the result is known.
- `reversed()` returns an iterator. `[::-1]` returns a new list — `reversed()` is more memory efficient.
- `dir(obj)` shows you what methods exist. `help(obj)` shows you how to use them.