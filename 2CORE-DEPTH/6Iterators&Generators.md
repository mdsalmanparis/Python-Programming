Let's go. Full topic, nothing skipped.

---

## 1.6 Iterators, `itertools` & Generators

---

## The Iterator Protocol

An **iterable** is anything you can loop over. An **iterator** is the object that does the actual stepping.

Two separate concepts — often confused:

```python
# Iterable — has __iter__(), returns an iterator
my_list = [1, 2, 3]

# Iterator — has __iter__() AND __next__()
my_iter = iter(my_list)

print(next(my_iter))    # 1
print(next(my_iter))    # 2
print(next(my_iter))    # 3
print(next(my_iter))    # StopIteration
```

**What `for` actually does under the hood:**

```python
for item in [1, 2, 3]:
    print(item)

# Python translates this to:
_iter = iter([1, 2, 3])         # call __iter__() to get iterator
while True:
    try:
        item = next(_iter)      # call __next__() to get next value
        print(item)
    except StopIteration:       # iterator exhausted — exit loop
        break
```

### The Protocol — Two Methods

```python
# An iterator must implement:

class MyIterator:
    def __iter__(self):
        return self     # iterators return themselves

    def __next__(self):
        # return next value, or raise StopIteration when done
        ...
```

**An iterable has `__iter__()` but may not have `__next__()`:**

```python
my_list = [1, 2, 3]

print(hasattr(my_list, "__iter__"))    # True  — it's iterable
print(hasattr(my_list, "__next__"))    # False — it's NOT an iterator

my_iter = iter(my_list)
print(hasattr(my_iter, "__iter__"))    # True
print(hasattr(my_iter, "__next__"))    # True  — NOW it's an iterator
```

**Iterator is single-use. Iterable is multi-use:**

```python
my_list = [1, 2, 3]

# Iterable — can loop multiple times
for x in my_list: pass
for x in my_list: pass    # works — creates a fresh iterator each time

# Iterator — one-shot
my_iter = iter(my_list)
for x in my_iter: pass
for x in my_iter: pass    # nothing — exhausted
print(list(my_iter))      # []
```

---

## `iter()` and `next()` — Built-in Wrappers

```python
# iter(iterable) — calls iterable.__iter__()
lst = [10, 20, 30]
it  = iter(lst)

# next(iterator) — calls iterator.__next__()
print(next(it))    # 10
print(next(it))    # 20
print(next(it))    # 30
print(next(it))    # StopIteration
```

### `next(iterator, default)` — Sentinel Value

Returns `default` instead of raising `StopIteration` when exhausted.

```python
it = iter([1, 2])

print(next(it, None))    # 1
print(next(it, None))    # 2
print(next(it, None))    # None  — no error
print(next(it, None))    # None  — keeps returning default
print(next(it, -1))      # -1    — any default value

# Real-world use — find first match without try/except
data = [3, 7, 2, 9, 1, 5]
first_big = next((x for x in data if x > 6), None)
print(first_big)    # 7  — first element > 6, or None if none found
```

### `iter()` with a sentinel — two-argument form

```python
# iter(callable, sentinel)
# Calls callable() repeatedly until it returns sentinel, then StopIteration

import random

# Roll a die until you get 6
rolls = list(iter(lambda: random.randint(1, 6), 6))
print(rolls)    # e.g. [3, 1, 4, 2] — all rolls before the 6

# Read file in fixed-size chunks
with open("file.txt", "rb") as f:
    for chunk in iter(lambda: f.read(4096), b""):
        process(chunk)      # stops when f.read() returns b"" (EOF)
```

---

## Writing a Custom Iterator Class

```python
class CountUp:
    """Counts from start to stop inclusive."""

    def __init__(self, start, stop):
        self.current = start
        self.stop    = stop

    def __iter__(self):
        return self    # iterator returns itself

    def __next__(self):
        if self.current > self.stop:
            raise StopIteration
        value = self.current
        self.current += 1
        return value

counter = CountUp(1, 5)

print(next(counter))    # 1
print(next(counter))    # 2

for n in counter:       # continues from where we left off
    print(n)
# 3
# 4
# 5

# Exhausted
print(list(CountUp(1, 5)))   # [1, 2, 3, 4, 5]  — fresh iterator
```

**A more realistic example — file chunk iterator:**

```python
class ChunkedFile:
    """Read a file in fixed-size chunks."""

    def __init__(self, path, chunk_size=4096):
        self.path       = path
        self.chunk_size = chunk_size
        self._file      = None

    def __iter__(self):
        self._file = open(self.path, "rb")
        return self

    def __next__(self):
        chunk = self._file.read(self.chunk_size)
        if not chunk:
            self._file.close()
            raise StopIteration
        return chunk

    def __del__(self):
        if self._file and not self._file.closed:
            self._file.close()

for chunk in ChunkedFile("large_file.bin"):
    process(chunk)
```

**Separating iterable from iterator — cleaner design:**

```python
class NumberRange:
    """Iterable — can be iterated multiple times."""

    def __init__(self, start, stop):
        self.start = start
        self.stop  = stop

    def __iter__(self):
        return NumberRangeIterator(self.start, self.stop)  # new iterator each time


class NumberRangeIterator:
    """Iterator — stateful, single-use."""

    def __init__(self, current, stop):
        self.current = current
        self.stop    = stop

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > self.stop:
            raise StopIteration
        value = self.current
        self.current += 1
        return value

r = NumberRange(1, 3)

print(list(r))    # [1, 2, 3]
print(list(r))    # [1, 2, 3]  — works again, fresh iterator each time
```

---

## Generator Functions

A generator function uses `yield` instead of `return`. Calling it returns a **generator object** — an iterator that computes values lazily.

```python
def count_up(start, stop):
    current = start
    while current <= stop:
        yield current       # pause here, return value, resume on next()
        current += 1

gen = count_up(1, 5)
print(type(gen))    # <class 'generator'>

print(next(gen))    # 1
print(next(gen))    # 2

for n in gen:       # continues from 3
    print(n)
# 3
# 4
# 5
```

**What `yield` does — pause and resume:**

```python
def trace_generator():
    print("start")
    yield 1
    print("after first yield")
    yield 2
    print("after second yield")
    yield 3
    print("done")

gen = trace_generator()

print(next(gen))    # start → yields 1
# start
# 1

print(next(gen))    # resumes → after first yield → yields 2
# after first yield
# 2

print(next(gen))    # resumes → after second yield → yields 3
# after second yield
# 3

print(next(gen))    # resumes → done → StopIteration
# done
# StopIteration
```

**Generator vs function — key differences:**

```python
def regular():
    result = []
    for i in range(5):
        result.append(i ** 2)
    return result              # returns all at once

def generator():
    for i in range(5):
        yield i ** 2           # returns one at a time

print(regular())               # [0, 1, 4, 9, 16]  — list in memory
print(list(generator()))       # [0, 1, 4, 9, 16]  — same result

# Memory — generator wins for large data
import sys
print(sys.getsizeof(regular()))                    # ~184 bytes
print(sys.getsizeof(generator()))                  # ~112 bytes (the generator object)
# generator for range(1_000_000) still uses ~112 bytes — doesn't matter how large
```

### `return` Value in Generators

A `return` statement inside a generator raises `StopIteration` with the return value.

```python
def gen_with_return():
    yield 1
    yield 2
    return "finished"   # becomes StopIteration's value

g = gen_with_return()
print(next(g))    # 1
print(next(g))    # 2
try:
    next(g)
except StopIteration as e:
    print(e.value)    # finished

# The return value is used by yield from (shown next)
```

### `yield from` — Delegation

`yield from iterable` yields every item from the iterable, one by one. It's cleaner than a loop.

```python
def chain_simple(*iterables):
    for it in iterables:
        for item in it:       # manual loop
            yield item

def chain_yieldfrom(*iterables):
    for it in iterables:
        yield from it         # delegate to each iterable — cleaner

print(list(chain_simple([1,2], [3,4], [5])))      # [1, 2, 3, 4, 5]
print(list(chain_yieldfrom([1,2], [3,4], [5])))   # [1, 2, 3, 4, 5]
```

**`yield from` with sub-generators — transparent proxying:**

```python
def inner():
    yield "a"
    yield "b"
    return "inner done"    # StopIteration value

def outer():
    result = yield from inner()   # yield from captures the return value
    print(f"inner returned: {result}")
    yield "c"

for val in outer():
    print(val)
# a
# b
# inner returned: inner done
# c
```

**Flattening nested structures with `yield from`:**

```python
def flatten(data):
    for item in data:
        if isinstance(item, (list, tuple)):
            yield from flatten(item)   # recurse into nested
        else:
            yield item

nested = [1, [2, [3, 4]], [5, 6], 7]
print(list(flatten(nested)))    # [1, 2, 3, 4, 5, 6, 7]
```

### Generator Pipelines — Lazy Processing Chains

```python
def read_lines(path):
    with open(path, encoding="utf-8") as f:
        yield from f                        # lazy: one line at a time

def strip_lines(lines):
    for line in lines:
        yield line.strip()

def filter_empty(lines):
    for line in lines:
        if line:                            # skip empty strings
            yield line

def parse_csv_line(lines):
    for line in lines:
        yield line.split(",")

# Build a pipeline — nothing computed until consumed
path     = "data.csv"
pipeline = parse_csv_line(
               filter_empty(
                   strip_lines(
                       read_lines(path)
                   )
               )
           )

for row in pipeline:
    print(row)
# Each line flows through the pipeline one at a time
# Memory usage: O(1) regardless of file size
```

---

## `itertools` — The Full Must-Know Set

```python
import itertools
```

---

### `chain()` — Combine Iterables Sequentially

```python
from itertools import chain

# Flatten one level
a = [1, 2, 3]
b = [4, 5, 6]
c = [7, 8, 9]

print(list(chain(a, b, c)))        # [1, 2, 3, 4, 5, 6, 7, 8, 9]
print(list(chain("abc", "def")))   # ['a', 'b', 'c', 'd', 'e', 'f']

# Combine different types
print(list(chain([1, 2], (3, 4), {5, 6})))   # [1, 2, 3, 4, 5, 6]  (set order varies)
```

### `chain.from_iterable()` — Flatten One Level From a Sequence of Iterables

```python
from itertools import chain

nested = [[1, 2], [3, 4], [5, 6]]

# chain(*nested) — unpacks and chains
print(list(chain(*nested)))                    # [1, 2, 3, 4, 5, 6]

# chain.from_iterable — lazy, no unpacking needed
print(list(chain.from_iterable(nested)))       # [1, 2, 3, 4, 5, 6]

# chain.from_iterable is better for large or lazy outer iterables
def get_chunks():
    yield [1, 2, 3]
    yield [4, 5, 6]
    yield [7, 8, 9]

print(list(chain.from_iterable(get_chunks())))   # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

### `islice()` — Slice Any Iterator

Regular slicing (`it[2:5]`) doesn't work on iterators. `islice` does.

```python
from itertools import islice

def infinite_counter(start=0):
    n = start
    while True:
        yield n
        n += 1

gen = infinite_counter(10)

print(list(islice(gen, 5)))       # [10, 11, 12, 13, 14]  — first 5

# islice(iterable, stop)
# islice(iterable, start, stop)
# islice(iterable, start, stop, step)

print(list(islice(range(20), 2, 10)))       # [2, 3, 4, 5, 6, 7, 8, 9]
print(list(islice(range(20), 0, 20, 3)))    # [0, 3, 6, 9, 12, 15, 18]

# Skip the header line of a generator
lines = iter(["header", "data1", "data2", "data3"])
print(list(islice(lines, 1, None)))    # ['data1', 'data2', 'data3']
```

---

### `takewhile()` and `dropwhile()`

```python
from itertools import takewhile, dropwhile

nums = [2, 4, 6, 7, 8, 10, 12]

# takewhile — yield items while condition is True, stop at first False
print(list(takewhile(lambda x: x % 2 == 0, nums)))
# [2, 4, 6]  — stops at 7 (odd), doesn't check 8, 10, 12

# dropwhile — skip items while condition is True, yield rest
print(list(dropwhile(lambda x: x % 2 == 0, nums)))
# [7, 8, 10, 12]  — drops 2, 4, 6, then yields everything from 7 onwards

# Note: takewhile/dropwhile are based on the FIRST False/True
# once condition changes, they don't re-check
nums2 = [2, 4, 6, 7, 8, 9, 10]
print(list(takewhile(lambda x: x % 2 == 0, nums2)))
# [2, 4, 6]  — stops at 7, doesn't yield 8 and 10 even though even
```

**Real-world use — processing log files with headers:**

```python
from itertools import dropwhile

log_lines = [
    "# Config log v1.0",
    "# Generated 2024-11-15",
    "# End of header",
    "ERROR: disk full",
    "INFO: process started",
    "WARNING: low memory",
]

# Skip comment lines at the top
data_lines = list(dropwhile(lambda line: line.startswith("#"), log_lines))
print(data_lines)
# ['ERROR: disk full', 'INFO: process started', 'WARNING: low memory']
```

---

### `product()` — Cartesian Product

```python
from itertools import product

# All combinations from two iterables
print(list(product([1, 2], ["a", "b"])))
# [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]

# repeat= — product of iterable with itself n times
print(list(product([0, 1], repeat=3)))
# [(0,0,0), (0,0,1), (0,1,0), (0,1,1), (1,0,0), (1,0,1), (1,1,0), (1,1,1)]
# All 3-bit binary numbers

# Equivalent to nested loops
colours = ["red", "blue"]
sizes   = ["S", "M", "L"]
for colour, size in product(colours, sizes):
    print(f"{colour}-{size}")
# red-S, red-M, red-L, blue-S, blue-M, blue-L
```

---

### `permutations()` — All Ordered Arrangements

```python
from itertools import permutations

print(list(permutations([1, 2, 3])))
# [(1,2,3), (1,3,2), (2,1,3), (2,3,1), (3,1,2), (3,2,1)]  — 3! = 6

# r= — length of each permutation
print(list(permutations([1, 2, 3], r=2)))
# [(1,2), (1,3), (2,1), (2,3), (3,1), (3,2)]  — 3*2 = 6

print(list(permutations("ABC", 2)))
# [('A','B'), ('A','C'), ('B','A'), ('B','C'), ('C','A'), ('C','B')]

# Count without generating
from math import perm
print(perm(10, 3))    # 720 — P(10,3)
```

---

### `combinations()` — All Unordered Selections

```python
from itertools import combinations, combinations_with_replacement

# combinations — no repetition, order doesn't matter
print(list(combinations([1, 2, 3, 4], 2)))
# [(1,2), (1,3), (1,4), (2,3), (2,4), (3,4)]  — C(4,2) = 6

print(list(combinations("ABCD", 3)))
# [('A','B','C'), ('A','B','D'), ('A','C','D'), ('B','C','D')]

# combinations_with_replacement — repetition allowed
print(list(combinations_with_replacement([1, 2, 3], 2)))
# [(1,1), (1,2), (1,3), (2,2), (2,3), (3,3)]

# Count without generating
from math import comb
print(comb(52, 5))    # 2598960 — poker hands from a deck of 52
```

**`combinations` vs `permutations` vs `product`:**

```python
# permutations("AB", 2) — order matters, no repetition
# [('A','B'), ('B','A')]

# combinations("AB", 2) — order doesn't matter, no repetition
# [('A','B')]

# product("AB", repeat=2) — order matters, repetition allowed
# [('A','A'), ('A','B'), ('B','A'), ('B','B')]
```

---

### `groupby()` — Group Consecutive Items

```python
from itertools import groupby

# CRITICAL: input must be SORTED by the grouping key first
data = [
    {"name": "Mohamed", "city": "Chennai"},
    {"name": "Sara",    "city": "Chennai"},
    {"name": "Priya",   "city": "Mumbai"},
    {"name": "Vikram",  "city": "Mumbai"},
    {"name": "Arjun",   "city": "Delhi"},
]

# Sort by city first
data.sort(key=lambda x: x["city"])

for city, group in groupby(data, key=lambda x: x["city"]):
    members = [p["name"] for p in group]
    print(f"{city}: {members}")
# Chennai: ['Mohamed', 'Sara']
# Delhi: ['Arjun']
# Mumbai: ['Priya', 'Vikram']
```

**The `group` object is a lazy iterator — consume it immediately:**

```python
from itertools import groupby

nums = [1, 1, 2, 2, 2, 3, 1, 1]   # NOT sorted — groupby works on consecutive

for key, group in groupby(nums):
    print(f"{key}: {list(group)}")
# 1: [1, 1]
# 2: [2, 2, 2]
# 3: [3]
# 1: [1, 1]  ← new group because 1s are non-consecutive

# DON'T do this — group object is exhausted by the time you access it
groups = {k: list(g) for k, g in groupby(nums)}
# iterating the dict key consumed the group before list(g) could run in some cases
# safe pattern: convert each group immediately inside the loop
```

**Run-length encoding using `groupby`:**

```python
from itertools import groupby

def run_length_encode(s):
    return [(len(list(group)), char) for char, group in groupby(s)]

print(run_length_encode("aaabbbccddddee"))
# [(3, 'a'), (3, 'b'), (2, 'c'), (4, 'd'), (2, 'e')]
```

---

### `zip_longest()` — Zip With Fill Value

```python
from itertools import zip_longest

a = [1, 2, 3, 4, 5]
b = ["a", "b", "c"]

# Regular zip — stops at shortest
print(list(zip(a, b)))
# [(1, 'a'), (2, 'b'), (3, 'c')]

# zip_longest — continues to longest, fills with fillvalue
print(list(zip_longest(a, b, fillvalue="-")))
# [(1, 'a'), (2, 'b'), (3, 'c'), (4, '-'), (5, '-')]

# Multiple iterables, different lengths
x = [1, 2]
y = ["a", "b", "c"]
z = [True, False, True, True]

print(list(zip_longest(x, y, z, fillvalue=None)))
# [(1, 'a', True), (2, 'b', False), (None, 'c', True), (None, None, True)]
```

---

### `accumulate()` — Running Totals

```python
from itertools import accumulate
import operator

nums = [1, 2, 3, 4, 5]

# Default — running sum
print(list(accumulate(nums)))
# [1, 3, 6, 10, 15]

# Custom function
print(list(accumulate(nums, operator.mul)))
# [1, 2, 6, 24, 120]  — running product (factorials)

print(list(accumulate(nums, max)))
# [1, 2, 3, 4, 5]  — running maximum

print(list(accumulate(nums, min)))
# [1, 1, 1, 1, 1]  — running minimum

# initial= value (Python 3.8+)
print(list(accumulate(nums, operator.add, initial=100)))
# [100, 101, 103, 106, 110, 115]  — starts from 100

# Real-world: cumulative sales
daily_sales = [120, 250, 180, 300, 90]
cumulative  = list(accumulate(daily_sales))
print(cumulative)    # [120, 370, 550, 850, 940]
```

---

### `pairwise()` — Consecutive Pairs (Python 3.10+)

```python
from itertools import pairwise

nums = [1, 2, 3, 4, 5]
print(list(pairwise(nums)))
# [(1, 2), (2, 3), (3, 4), (4, 5)]

# Compute differences between consecutive values
prices = [100, 105, 98, 112, 108]
changes = [b - a for a, b in pairwise(prices)]
print(changes)    # [5, -7, 14, -4]

# Sliding window of 2
for a, b in pairwise("ABCDE"):
    print(f"{a}-{b}", end=" ")
# A-B B-C C-D D-E

# Before pairwise existed (Python < 3.10):
def manual_pairwise(it):
    it = iter(it)
    a = next(it)
    for b in it:
        yield a, b
        a = b
```

---

### Infinite Iterators — `count()`, `cycle()`, `repeat()`

```python
from itertools import count, cycle, repeat, islice

# count(start, step) — infinite counter
print(list(islice(count(10, 2), 5)))    # [10, 12, 14, 16, 18]

# cycle(iterable) — repeat forever
colours = cycle(["red", "green", "blue"])
print(list(islice(colours, 7)))
# ['red', 'green', 'blue', 'red', 'green', 'blue', 'red']

# repeat(value, n) — repeat n times (or infinitely if n omitted)
print(list(repeat("hi", 3)))    # ['hi', 'hi', 'hi']
print(list(map(pow, range(5), repeat(2))))  # [0, 1, 4, 9, 16]  — squares
```

---

### `filterfalse()` and `starmap()`

```python
from itertools import filterfalse, starmap

# filterfalse — opposite of filter, keeps where function returns False
nums = [1, 2, 3, 4, 5, 6]
odds = list(filterfalse(lambda x: x % 2 == 0, nums))
print(odds)    # [1, 3, 5]

# starmap — map where each element is unpacked as args
pairs = [(2, 3), (4, 2), (3, 3)]
print(list(starmap(pow, pairs)))    # [8, 16, 27]  — 2**3, 4**2, 3**3

# starmap vs map
print(list(map(lambda x: x[0] ** x[1], pairs)))   # same, but uglier
```

---

## Putting It All Together

```python
from itertools import (
    chain, islice, groupby, accumulate,
    pairwise, takewhile, combinations
)
import operator

# Dataset — sales records
records = [
    {"region": "South", "month": "Jan", "sales": 120},
    {"region": "South", "month": "Feb", "sales": 145},
    {"region": "North", "month": "Jan", "sales": 200},
    {"region": "North", "month": "Feb", "sales": 180},
    {"region": "East",  "month": "Jan", "sales":  90},
    {"region": "East",  "month": "Feb", "sales": 110},
]

# 1. Generator pipeline — lazy processing
def get_sales(records):
    yield from records                              # yield each record

def filter_above(records, threshold):
    for r in records:
        if r["sales"] >= threshold:
            yield r

def extract_amounts(records):
    for r in records:
        yield r["sales"]

pipeline = extract_amounts(filter_above(get_sales(records), 100))
print("Sales >= 100:", list(pipeline))
# [120, 145, 200, 180, 110]

# 2. groupby — per region breakdown
sorted_records = sorted(records, key=lambda r: r["region"])
print("\nBy region:")
for region, group in groupby(sorted_records, key=lambda r: r["region"]):
    sales = [r["sales"] for r in group]
    print(f"  {region}: total={sum(sales)}, avg={sum(sales)/len(sales):.0f}")

# 3. accumulate — cumulative sales across all records
all_sales = [r["sales"] for r in records]
cumulative = list(accumulate(all_sales))
print(f"\nCumulative: {cumulative}")
print(f"Grand total: {cumulative[-1]}")

# 4. pairwise — month-over-month change
monthly = [120, 145, 180, 160, 200]
changes = [(b - a, f"+{b-a}" if b > a else str(b-a))
           for a, b in pairwise(monthly)]
print(f"\nMonthly changes: {[c[1] for c in changes]}")

# 5. combinations — all pairs of regions for comparison
regions = ["South", "North", "East"]
print(f"\nRegion pairs: {list(combinations(regions, 2))}")

# 6. Custom iterator for sliding window
def sliding_window(data, n):
    it = iter(data)
    window = list(islice(it, n))
    if len(window) == n:
        yield tuple(window)
    for item in it:
        window.pop(0)
        window.append(item)
        yield tuple(window)

sales_stream = [100, 120, 90, 150, 130, 110]
print(f"\n3-period windows: {list(sliding_window(sales_stream, 3))}")
# [(100,120,90), (120,90,150), (90,150,130), (150,130,110)]

# 7. chain — combine multiple data sources lazily
q1_data = iter([100, 120, 90])
q2_data = iter([150, 130, 110])
q3_data = iter([200, 180, 160])

all_quarters = chain(q1_data, q2_data, q3_data)
print(f"\nAll quarters: {list(all_quarters)}")

# 8. takewhile — early stopping
budget   = 400
spending = [100, 120, 90, 150, 200, 80]
running  = list(accumulate(spending))
within_budget = list(takewhile(lambda x: x <= budget, running))
print(f"\nSpending within ${budget}: {within_budget}")
```

---

## Key Takeaways

- **Iterable** has `__iter__()`. **Iterator** has `__iter__()` AND `__next__()`. Iterators return `self` from `__iter__()`.
- `for` calls `iter()` then repeatedly calls `next()` until `StopIteration`.
- Iterators are **single-use**. Iterables can create fresh iterators each time.
- `next(it, default)` — never raises `StopIteration`, returns default when exhausted.
- `iter(callable, sentinel)` — calls callable until it returns sentinel. Perfect for chunked reads.
- Generator functions use `yield`. Calling them returns a generator object — they don't run until you iterate.
- `yield from iterable` — delegates to a sub-iterable, yields each item, captures the sub-generator's return value.
- Generator pipelines — chain generators together. Memory is O(1) regardless of data size.
- `chain()` — sequential concatenation. `chain.from_iterable()` — lazy version for nested iterables.
- `islice()` — slice any iterator. Essential for infinite generators.
- `takewhile()` / `dropwhile()` — condition-based slicing. Stop/skip based on predicate, NOT filter.
- `groupby()` — **requires sorted input**. Groups only consecutive equal-key items. Consume each group immediately.
- `accumulate()` — running aggregation. Default is sum. Pass `operator.mul` for product, `max` for running max.
- `pairwise()` (3.10+) — consecutive pairs. Use for differences, transitions, sliding window of 2.
- `product()` = nested loops. `permutations()` = ordered, no repeat. `combinations()` = unordered, no repeat.