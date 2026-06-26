# 0.7 Loops

## for Loops

A `for` loop iterates over any iterable — list, string, dict, tuple, set, range, or anything Python can step through one item at a time.

### Iterating a List

```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)
# apple
# banana
# cherry
```

**The loop variable (`fruit`) is just a name — it gets reassigned each iteration:**

```python
numbers = [10, 20, 30, 40]

for n in numbers:
    print(n * 2)
# 20
# 40
# 60
# 80

# n still exists after the loop — holds the last value
print(n)    # 40
```

### Modifying a List While Iterating — Never Do This

```python
nums = [1, 2, 3, 4, 5]

# WRONG — skips elements because indexes shift as you remove
for n in nums:
    if n % 2 == 0:
        nums.remove(n)
print(nums)    # [1, 3, 5]  — looks right but only by accident for this case
```

**Correct approach — iterate a copy, modify the original:**

```python
for n in nums[:]:           # nums[:] is a shallow copy
    if n % 2 == 0:
        nums.remove(n)
```

**Best approach — build a new list:**

```python
nums = [n for n in nums if n % 2 != 0]
```

---

### Iterating a String

Strings are sequences — every character is an item.

```python
for char in "hello":
    print(char)
# h
# e
# l
# l
# o
```

Count vowels:

```python
word = "Mohamed"
vowels = 0
for char in word.lower():
    if char in "aeiou":
        vowels += 1
print(vowels)    # 4
```

Build reversed string:

```python
word = "Python"
reversed_word = ""
for char in word:
    reversed_word = char + reversed_word
print(reversed_word)    # nohtyP
# (better: word[::-1] — but good to know the loop version)
```

---

### Iterating a Dict

```python
person = {"name": "Mohamed", "age": 25, "city": "Chennai"}

# Iterate keys — default behaviour
for key in person:
    print(key)
# name
# age
# city
```

Iterate keys explicitly:

```python
for key in person.keys():
    print(key)
```

Iterate values:

```python
for value in person.values():
    print(value)
# Mohamed
# 25
# Chennai
```

### Iterate Key-Value Pairs — Most Common, Use This

```python
for key, value in person.items():
    print(f"{key}: {value}")
# name: Mohamed
# age: 25
# city: Chennai
```

### Never Modify a Dict's Keys While Iterating

Raises `RuntimeError`:

```python
d = {"a": 1, "b": 2, "c": 3}

# WRONG
for key in d:
    if key == "b":
        del d[key]    # RuntimeError: dictionary changed size during iteration
```

**Correct approach — iterate a copy of keys:**

```python
for key in list(d.keys()):
    if key == "b":
        del d[key]
print(d)    # {'a': 1, 'c': 3}
```

---

### range()

Generates a sequence of integers. Does not create a list in memory — it's a lazy iterator.

**range(stop) — 0 to stop-1:**

```python
for i in range(5):
    print(i)
# 0 1 2 3 4
```

**range(start, stop) — start to stop-1:**

```python
for i in range(1, 6):
    print(i)
# 1 2 3 4 5
```

**range(start, stop, step):**

```python
for i in range(0, 10, 2):
    print(i)
# 0 2 4 6 8

for i in range(10, 0, -1):    # count down
    print(i)
# 10 9 8 7 6 5 4 3 2 1

for i in range(0, 11, 3):
    print(i)
# 0 3 6 9
```

### range() is Lazy

Does not create all the numbers in memory:

```python
r = range(1_000_000)
print(r)           # range(0, 1000000)  — not a list
print(r[0])        # 0    — supports indexing
print(r[-1])       # 999999
print(len(r))      # 1000000
print(list(r[:5])) # [0, 1, 2, 3, 4]
```

### Looping with Index Using range(len(...))

```python
fruits = ["apple", "banana", "cherry"]

for i in range(len(fruits)):
    print(f"{i}: {fruits[i]}")
# 0: apple
# 1: banana
# 2: cherry
```

Better — use `enumerate()` instead (covered next).

---

## enumerate() — Index + Value Together

When you need both the index and the value, `enumerate()` is the Pythonic way.

```python
fruits = ["apple", "banana", "cherry"]

for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
# 0: apple
# 1: banana
# 2: cherry
```

Custom start index:

```python
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")
# 1. apple
# 2. banana
# 3. cherry
```

### What enumerate() Actually Returns — Pairs of (index, value)

```python
fruits = ["apple", "banana", "cherry"]
print(list(enumerate(fruits)))
# [(0, 'apple'), (1, 'banana'), (2, 'cherry')]

print(list(enumerate(fruits, start=1)))
# [(1, 'apple'), (2, 'banana'), (3, 'cherry')]
```

### range(len(...)) vs enumerate() — Always Prefer enumerate()

Unpythonic:

```python
for i in range(len(fruits)):
    print(i, fruits[i])
```

Pythonic:

```python
for i, fruit in enumerate(fruits):
    print(i, fruit)
```

### Real-World Use — Finding Index of a Value

```python
scores = [88, 45, 92, 67, 92]

for i, score in enumerate(scores):
    if score == 92:
        print(f"Found 92 at index {i}")
# Found 92 at index 2
# Found 92 at index 4
```

---

## zip() — Pairing Items from Multiple Iterables

Combines two or more iterables element by element.

```python
names  = ["Alice", "Bob", "Charlie"]
scores = [88, 92, 75]

for name, score in zip(names, scores):
    print(f"{name}: {score}")
# Alice: 88
# Bob: 92
# Charlie: 75
```

### What zip() Actually Returns — Pairs as Tuples

```python
names  = ["Alice", "Bob", "Charlie"]
scores = [88, 92, 75]

print(list(zip(names, scores)))
# [('Alice', 88), ('Bob', 92), ('Charlie', 75)]
```

### zip() Stops at the Shortest Iterable

```python
a = [1, 2, 3, 4, 5]
b = ["a", "b", "c"]

for x, y in zip(a, b):
    print(x, y)
# 1 a
# 2 b
# 3 c
# 4 and 5 are ignored — b ran out
```

### zip(strict=True) — Raises Error if Lengths Differ (Python 3.10+)

```python
a = [1, 2, 3]
b = ["a", "b"]

for x, y in zip(a, b, strict=True):
    print(x, y)
# ValueError: zip() has arguments with different lengths
# Use when mismatched lengths mean a bug
```

### zip_longest() — Fill Missing Values with a Default

```python
from itertools import zip_longest

a = [1, 2, 3, 4, 5]
b = ["a", "b", "c"]

for x, y in zip_longest(a, b, fillvalue="-"):
    print(x, y)
# 1 a
# 2 b
# 3 c
# 4 -
# 5 -
```

### Three Iterables with zip()

```python
names  = ["Alice", "Bob", "Charlie"]
scores = [88, 92, 75]
cities = ["Chennai", "Mumbai", "Delhi"]

for name, score, city in zip(names, scores, cities):
    print(f"{name} from {city} scored {score}")
# Alice from Chennai scored 88
# Bob from Mumbai scored 92
# Charlie from Delhi scored 75
```

### Creating a Dict from Two Lists Using zip()

```python
keys   = ["name", "age", "city"]
values = ["Mohamed", 25, "Chennai"]

d = dict(zip(keys, values))
print(d)    # {'name': 'Mohamed', 'age': 25, 'city': 'Chennai'}
```

---

## while Loops

Runs a block of code repeatedly as long as a condition is `True`. Use when you don't know the number of iterations in advance.

### Basic while

```python
count = 0

while count < 5:
    print(count)
    count += 1
# 0 1 2 3 4
```

Condition checked BEFORE each iteration. When count reaches 5, condition is `False` → loop ends.

### Forgetting to Update the Condition — Infinite Loop

```python
count = 0

while count < 5:
    print(count)
    # count never incremented → runs forever
    # Ctrl+C to stop
```

---

## Infinite Loop + break

`while True:` runs forever intentionally. `break` exits the loop immediately.

```python
while True:
    user_input = input("Enter a number (or 'quit'): ")
    if user_input == "quit":
        break
    print(f"You entered: {user_input}")

print("Goodbye")
```

### Menu Loop — Extremely Common Pattern

```python
while True:
    print("\n1. Add item")
    print("2. View items")
    print("3. Quit")

    choice = input("Choose: ")

    if choice == "1":
        print("Adding item...")
    elif choice == "2":
        print("Viewing items...")
    elif choice == "3":
        print("Exiting...")
        break
    else:
        print("Invalid choice, try again")
```

---

## break — Exit the Loop Immediately

Works in both `for` and `while` loops. Exits the innermost loop only.

In a for loop:

```python
for n in range(10):
    if n == 5:
        break
    print(n)
# 0 1 2 3 4
```

### Search and Break — Stop as Soon as Found

```python
names = ["Alice", "Bob", "Mohamed", "Charlie"]
target = "Mohamed"

for name in names:
    if name == target:
        print(f"Found: {name}")
        break
else:
    print("Not found")    # runs only if loop finished without break
# Found: Mohamed
```

### break in Nested Loops — Only Exits the Innermost Loop

```python
for i in range(3):
    for j in range(3):
        if j == 1:
            break          # exits inner loop only
        print(f"({i},{j})")
# (0,0)
# (1,0)
# (2,0)
# j==1 breaks inner loop, outer loop continues
```

---

## continue — Skip to Next Iteration

Skips the rest of the current iteration's body and jumps to the next iteration.

Skip even numbers:

```python
for n in range(10):
    if n % 2 == 0:
        continue
    print(n)
# 1 3 5 7 9
```

Skip empty strings:

```python
words = ["hello", "", "world", "", "python"]
for word in words:
    if not word:
        continue
    print(word.upper())
# HELLO
# WORLD
# PYTHON
```

### continue in a while Loop

```python
count = 0
while count < 10:
    count += 1
    if count % 3 == 0:
        continue          # skip multiples of 3
    print(count)
# 1 2 4 5 7 8 10
```

### break vs continue

```python
# break  — exit the loop entirely
# continue — skip this iteration, keep looping

for n in range(5):
    if n == 3:
        break        # stops at 3: prints 0 1 2
    print(n)

for n in range(5):
    if n == 3:
        continue     # skips 3: prints 0 1 2 4
    print(n)
```

---

## else on Loops

The `else` block runs only if the loop completed **without hitting a `break`**. If `break` was triggered, `else` is skipped.

```python
# for...else
for n in range(5):
    if n == 10:    # never True
        break
else:
    print("loop completed without break")
# loop completed without break
```

Break fires — else skipped:

```python
for n in range(5):
    if n == 3:
        break
else:
    print("this won't print")
# (nothing from else)
```

### The Real Use Case — Search Loops

**Without else — need a flag variable:**

```python
numbers = [1, 3, 5, 7, 9]
target = 6
found = False

for n in numbers:
    if n == target:
        found = True
        break

if found:
    print("found")
else:
    print("not found")
```

**With for...else — cleaner, no flag needed:**

```python
for n in numbers:
    if n == target:
        print("found")
        break
else:
    print("not found")    # runs because break was never hit
# not found
```

### while...else

```python
count = 0
while count < 5:
    count += 1
else:
    print(f"while completed, count = {count}")
# while completed, count = 5
```

The `for...else` / `while...else` pattern is rarely seen in other languages. It's not heavily used in Python either, but it appears in search logic and you will encounter it in real codebases.

---

## Common Loop Patterns

### Counter Loop

```python
# Count how many items meet a condition
scores = [88, 45, 92, 67, 55, 78]
passing = 0

for score in scores:
    if score >= 60:
        passing += 1

print(f"Passed: {passing}")    # 4
```

### Accumulator

```python
# Build up a result across iterations
numbers = [1, 2, 3, 4, 5]
total = 0

for n in numbers:
    total += n

print(total)    # 15
# (better: sum(numbers) — but know the loop version)
```

String accumulator — joining words:

```python
words = ["Python", "is", "great"]
sentence = ""

for word in words:
    sentence += word + " "

print(sentence.strip())    # Python is great
# (better: " ".join(words) — but know the loop version)
```

### Search-and-Break

```python
# Find first item matching a condition
inventory = [
    {"item": "apple",  "qty": 0},
    {"item": "banana", "qty": 5},
    {"item": "cherry", "qty": 3},
]

for product in inventory:
    if product["qty"] > 0:
        print(f"First available: {product['item']}")
        break
else:
    print("All out of stock")
# First available: banana
```

### Nested Loops — Working with Grids

Multiplication table:

```python
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i}x{j}={i*j}", end="  ")
    print()    # newline after each row
# 1x1=1  1x2=2  1x3=3
# 2x1=2  2x2=4  2x3=6
# 3x1=3  3x2=6  3x3=9
```

Flatten a nested list:

```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = []

for row in matrix:
    for item in row:
        flat.append(item)

print(flat)    # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### While with a Retry Pattern

```python
# Retry until success or max attempts
MAX_ATTEMPTS = 3
attempts = 0

while attempts < MAX_ATTEMPTS:
    attempts += 1
    print(f"Attempt {attempts}...")

    success = attempts == 3    # simulating success on 3rd try

    if success:
        print("Connected")
        break
else:
    print(f"Failed after {MAX_ATTEMPTS} attempts")
# Attempt 1...
# Attempt 2...
# Attempt 3...
# Connected
```

---

## Putting It All Together

A realistic example — processing a CSV-like dataset:

```python
data = [
    ("Mohamed", 88, "Chennai"),
    ("Priya",   92, "Mumbai"),
    ("Arjun",   45, "Delhi"),
    ("Sara",    67, "Chennai"),
    ("Vikram",  55, "Mumbai"),
    ("Divya",   91, "Delhi"),
]

headers = ("name", "score", "city")

# Print header
print(" | ".join(f"{h:10}" for h in headers))
print("-" * 36)

# Print rows, skip failing scores (< 60)
passed = []
for name, score, city in data:
    if score < 60:
        continue                     # skip failing students
    passed.append((name, score, city))
    print(f"{name:10} | {score:10} | {city:10}")

print(f"\nTotal passed: {len(passed)}")

# Find top scorer
top_name, top_score, top_city = max(passed, key=lambda x: x[1])
print(f"Top scorer: {top_name} with {top_score}")

# City-wise count using a dict accumulator
city_count = {}
for name, score, city in passed:
    if city not in city_count:
        city_count[city] = 0
    city_count[city] += 1

print("\nPassed by city:")
for city, count in city_count.items():
    print(f"  {city}: {count}")
```

---

## Key Takeaways

- `for` loops work on any iterable — list, string, dict, tuple, set, range, zip, enumerate.
- Never modify a list while iterating it — iterate a copy (`lst[:]`) or build a new list.
- Never modify a dict's keys while iterating — iterate `list(d.keys())` instead.
- Use `enumerate()` instead of `range(len(...))` when you need index + value.
- `zip()` stops at the shortest iterable — use `zip(strict=True)` if mismatched lengths are a bug.
- `break` exits the loop. `continue` skips to the next iteration.
- `else` on a loop runs only when no `break` occurred — useful for search patterns without flag variables.
- `while True` + `break` is the right pattern for menus, retries, and user input loops.
- Counter, accumulator, and search-and-break are the three core loop patterns — recognise them everywhere.
