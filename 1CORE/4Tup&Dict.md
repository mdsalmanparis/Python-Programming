Let's go. Full topic, nothing skipped.

---

## 0.5 Tuples, Sets & Dictionaries

---

# TUPLES

---

## Creating a Tuple

A tuple is an ordered, immutable sequence. Like a list that can never be changed.

```python
# With parentheses
t = (1, 2, 3)
print(t)          # (1, 2, 3)
print(type(t))    # <class 'tuple'>

# Without parentheses — the comma is what makes it a tuple, not the parentheses
t = 1, 2, 3
print(t)          # (1, 2, 3)
print(type(t))    # <class 'tuple'>

# Mixed types
t = (1, "hello", True, 3.14, None)

# Empty tuple
empty1 = ()
empty2 = tuple()

# From an iterable
t = tuple([1, 2, 3])     # from list
t = tuple("Python")      # from string → ('P', 'y', 't', 'h', 'o', 'n')
t = tuple(range(5))      # from range → (0, 1, 2, 3, 4)
```

### Single-element tuple — the comma is mandatory

```python
# These are NOT tuples
not_tuple = (1)        # just an int in parentheses
print(type(not_tuple)) # <class 'int'>

also_not = ("hello")   # just a string
print(type(also_not))  # <class 'str'>

# These ARE tuples
is_tuple = (1,)        # the trailing comma makes it a tuple
print(type(is_tuple))  # <class 'tuple'>

also_tuple = 1,        # no parentheses, just the comma
print(type(also_tuple))  # <class 'tuple'>
print(also_tuple)        # (1,)
```

This is one of the most common beginner mistakes. If you mean a single-element tuple, the comma is not optional.

---

## Immutability

Once created, a tuple's elements cannot be changed.

```python
t = (1, 2, 3)

t[0] = 99         # TypeError: 'tuple' object does not support item assignment
t.append(4)       # AttributeError: 'tuple' object has no attribute 'append'
t[0:2] = (9, 9)  # TypeError: 'tuple' object does not support item assignment
```

**Tuples support reading operations — just not writing:**

```python
t = (10, 20, 30, 20, 40)

print(t[0])          # 10       — indexing works
print(t[-1])         # 40       — negative indexing works
print(t[1:3])        # (20, 30) — slicing works, returns a tuple
print(len(t))        # 5        — len() works
print(20 in t)       # True     — membership test works
print(t.count(20))   # 2        — count works
print(t.index(30))   # 2        — index works
```

**Immutability is shallow — if a tuple contains a mutable object, that object can still be changed:**

```python
t = (1, [2, 3], 4)
t[1].append(99)       # modifying the list inside the tuple — allowed
print(t)              # (1, [2, 3, 99], 4)

t[1] = [99]           # TypeError — can't replace the reference
```

The tuple can't change which objects it points to, but if one of those objects is mutable, it can mutate internally.

---

## When to Use Tuples

**1. Fixed collections that should never change:**

```python
RGB_RED   = (255, 0, 0)
RGB_GREEN = (0, 255, 0)
RGB_BLUE  = (0, 0, 255)

WEEKDAYS = ("Mon", "Tue", "Wed", "Thu", "Fri")
```

**2. Returning multiple values from a function:**

```python
def get_dimensions():
    return 1920, 1080    # returns a tuple

width, height = get_dimensions()
print(width)    # 1920
print(height)   # 1080

# Without unpacking
dims = get_dimensions()
print(dims)        # (1920, 1080)
print(dims[0])     # 1920
```

This is used everywhere in Python. `os.path.split()`, `divmod()`, `enumerate()`, `dict.items()` — all return tuples.

**3. Dictionary keys — tuples are hashable, lists are not:**

```python
# Using a tuple as a dict key — works
locations = {}
locations[(40.7128, -74.0060)] = "New York"
locations[(19.0760, 72.8777)]  = "Mumbai"
print(locations[(40.7128, -74.0060)])   # New York

# Using a list as a dict key — fails
locations[[40.7128, -74.0060]] = "New York"   # TypeError: unhashable type: 'list'
```

**4. Slightly faster and more memory-efficient than lists:**

```python
import sys
print(sys.getsizeof([1, 2, 3]))    # 88 bytes
print(sys.getsizeof((1, 2, 3)))    # 64 bytes

import timeit
# tuple creation is faster than list creation
```

Use tuples for data that is logically fixed. Use lists for data that grows or changes.

---

## Tuple Unpacking

```python
# Basic unpacking
point = (10, 20)
x, y = point
print(x)    # 10
print(y)    # 20

# Works without parentheses too
x, y = 10, 20

# Nested tuple unpacking
t = (1, (2, 3), 4)
a, (b, c), d = t
print(a, b, c, d)    # 1 2 3 4

# Swap — the classic
a, b = 5, 10
a, b = b, a
print(a, b)    # 10 5

# Starred unpacking — same as lists
first, *rest = (1, 2, 3, 4, 5)
print(first)    # 1
print(rest)     # [2, 3, 4, 5]   ← note: rest is a list, not a tuple

*start, last = (1, 2, 3, 4, 5)
print(start)    # [1, 2, 3, 4]
print(last)     # 5

# Unpacking in a loop — extremely common
pairs = [(1, "one"), (2, "two"), (3, "three")]
for num, word in pairs:
    print(f"{num} = {word}")
```

**`_` as a throwaway variable:**

```python
# When you only want some values
point_3d = (10, 20, 30)
x, y, _ = point_3d     # _ means "I don't care about this value"
print(x, y)    # 10 20

# Skip multiple values
first, *_, last = (1, 2, 3, 4, 5)
print(first, last)    # 1 5
```

---

---

# SETS

---

## Creating a Set

A set is an unordered collection of unique items.

```python
s = {1, 2, 3}
print(s)          # {1, 2, 3}
print(type(s))    # <class 'set'>

# Mixed types (all must be hashable)
s = {1, "hello", 3.14, True}

# From a list
s = set([1, 2, 3, 2, 1])
print(s)    # {1, 2, 3}  — duplicates removed automatically

# From a string
s = set("hello")
print(s)    # {'h', 'e', 'l', 'o'}  — unique characters, unordered
```

### Empty set — must use `set()`, not `{}`

```python
empty_set  = set()        # correct
empty_dict = {}           # this is a dict, NOT a set

print(type(set()))   # <class 'set'>
print(type({}))      # <class 'dict'>
```

This is a very common mistake. `{}` always creates a dict in Python.

---

## No Duplicates

```python
s = {1, 1, 2, 2, 3, 3, 3}
print(s)    # {1, 2, 3}  — duplicates silently dropped

# Practical use — deduplicate a list
names = ["Alice", "Bob", "Alice", "Charlie", "Bob", "Alice"]
unique = list(set(names))
print(unique)    # ['Alice', 'Bob', 'Charlie']  — order not guaranteed
```

**Preserving order while deduplicating (when order matters):**

```python
names = ["Alice", "Bob", "Alice", "Charlie", "Bob"]
seen = set()
unique_ordered = []
for name in names:
    if name not in seen:
        seen.add(name)
        unique_ordered.append(name)
print(unique_ordered)    # ['Alice', 'Bob', 'Charlie']  — original order preserved
```

---

## No Indexing — Sets Are Unordered

```python
s = {1, 2, 3}

print(s[0])     # TypeError: 'set' object is not subscriptable
print(s[1:3])   # TypeError: 'set' object is not subscriptable
```

You cannot access elements by position. You can only iterate or test membership.

```python
# Iteration works, but order is not guaranteed
s = {3, 1, 4, 1, 5, 9, 2}
for item in s:
    print(item)    # prints in arbitrary order, not insertion order
```

---

## Membership Test — O(1)

This is the main reason to use a set over a list.

```python
# Set lookup — O(1) — constant time regardless of size
large_set = set(range(1_000_000))
print(999999 in large_set)    # True  — instant

# List lookup — O(n) — scans from beginning
large_list = list(range(1_000_000))
print(999999 in large_list)   # True  — slow for large lists
```

**When to choose set over list for lookups:**

```python
# BAD — checking membership in a list repeatedly
valid_users = ["alice", "bob", "charlie", "diana"]
for username in incoming_requests:
    if username in valid_users:    # O(n) every time
        allow()

# GOOD — convert to set first
valid_users = {"alice", "bob", "charlie", "diana"}
for username in incoming_requests:
    if username in valid_users:    # O(1) every time
        allow()
```

---

## Adding and Removing

```python
s = {1, 2, 3}

# Adding
s.add(4)
print(s)    # {1, 2, 3, 4}

s.add(2)    # adding a duplicate — silently does nothing
print(s)    # {1, 2, 3, 4}

# .remove() — raises KeyError if not found
s.remove(3)
print(s)      # {1, 2, 4}
s.remove(99)  # KeyError: 99

# .discard() — safe, no error if not found
s.discard(2)
print(s)      # {1, 4}
s.discard(99) # no error — silently does nothing

# .pop() — removes and returns an ARBITRARY element (not the last one)
s = {10, 20, 30}
item = s.pop()
print(item)   # some element — you can't predict which one
print(s)      # remaining two elements

# .clear() — removes all elements
s.clear()
print(s)    # set()
```

Use `.discard()` when you're not sure if the element exists. Use `.remove()` when it must exist and absence means a bug.

---

## Set Operations

Sets support mathematical set operations — these are extremely powerful.

```python
a = {1, 2, 3, 4, 5}
b = {3, 4, 5, 6, 7}
```

### Union `|` — all elements from both

```python
print(a | b)            # {1, 2, 3, 4, 5, 6, 7}
print(a.union(b))       # same result
```

### Intersection `&` — only elements in both

```python
print(a & b)               # {3, 4, 5}
print(a.intersection(b))   # same result
```

### Difference `-` — in first but not second

```python
print(a - b)              # {1, 2}       — in a but not b
print(b - a)              # {6, 7}       — in b but not a
print(a.difference(b))    # {1, 2}       — same as a - b
```

### Symmetric Difference `^` — in either but not both

```python
print(a ^ b)                          # {1, 2, 6, 7}
print(a.symmetric_difference(b))      # same result
```

### Subset and Superset

```python
small = {1, 2, 3}
large = {1, 2, 3, 4, 5}

print(small.issubset(large))     # True  — all of small is in large
print(large.issuperset(small))   # True  — large contains all of small
print(small <= large)            # True  — subset with operator
print(large >= small)            # True  — superset with operator
print(small < large)             # True  — proper subset (not equal)
```

### Disjoint — no elements in common

```python
a = {1, 2, 3}
b = {4, 5, 6}
print(a.isdisjoint(b))    # True — no overlap
```

### Update in-place

```python
a = {1, 2, 3}
a |= {4, 5}        # a.update({4, 5})
print(a)    # {1, 2, 3, 4, 5}

a &= {2, 3, 4}     # a.intersection_update(...)
print(a)    # {2, 3, 4}
```

---

## `frozenset` — Immutable Set

A set that cannot be modified after creation. Can be used as a dictionary key or stored inside another set.

```python
fs = frozenset([1, 2, 3, 2, 1])
print(fs)           # frozenset({1, 2, 3})
print(type(fs))     # <class 'frozenset'>

fs.add(4)           # AttributeError: 'frozenset' object has no attribute 'add'

# frozenset as a dict key — works because it's hashable
permissions = {}
permissions[frozenset(["read", "write"])] = "editor"
permissions[frozenset(["read"])]          = "viewer"

# All non-mutating set operations work
a = frozenset({1, 2, 3})
b = frozenset({2, 3, 4})
print(a & b)    # frozenset({2, 3})
print(a | b)    # frozenset({1, 2, 3, 4})
```

---

---

# DICTIONARIES

---

## Creating a Dictionary

A dictionary stores key-value pairs. Keys must be unique and hashable. Values can be anything.

```python
# Literal syntax
person = {"name": "Mohamed", "age": 25, "city": "Chennai"}
print(person)         # {'name': 'Mohamed', 'age': 25, 'city': 'Chennai'}
print(type(person))   # <class 'dict'>

# Empty dict — two ways
empty1 = {}
empty2 = dict()

# Using dict() constructor
person = dict(name="Mohamed", age=25, city="Chennai")
print(person)   # {'name': 'Mohamed', 'age': 25, 'city': 'Chennai'}

# From a list of tuples
person = dict([("name", "Mohamed"), ("age", 25)])

# From two lists using zip
keys   = ["name", "age", "city"]
values = ["Mohamed", 25, "Chennai"]
person = dict(zip(keys, values))
print(person)   # {'name': 'Mohamed', 'age': 25, 'city': 'Chennai'}
```

**Valid key types — must be hashable:**

```python
d = {}
d[1]          = "int key"
d["name"]     = "string key"
d[(1, 2)]     = "tuple key"
d[3.14]       = "float key"
d[True]       = "bool key"

d[[1, 2]]     = "list key"   # TypeError: unhashable type: 'list'
d[{"a": 1}]   = "dict key"   # TypeError: unhashable type: 'dict'
```

---

## Accessing Values

### `d[key]` — direct access, raises `KeyError` if missing

```python
person = {"name": "Mohamed", "age": 25}

print(person["name"])    # Mohamed
print(person["age"])     # 25
print(person["city"])    # KeyError: 'city'
```

### `.get(key, default)` — safe access, returns default if missing

```python
person = {"name": "Mohamed", "age": 25}

print(person.get("name"))           # Mohamed
print(person.get("city"))           # None    — no error, default is None
print(person.get("city", "unknown"))  # unknown — custom default
print(person.get("age", 0))         # 25      — key exists, default ignored
```

**When to use which:**

```python
# Use d[key] when the key MUST exist — absence is a bug
user_id = request["user_id"]       # if missing, you want to know immediately

# Use .get() when missing is a normal case
theme = settings.get("theme", "light")   # default to light theme
```

---

## Adding and Updating

```python
person = {"name": "Mohamed", "age": 25}

# Adding a new key
person["city"] = "Chennai"
print(person)   # {'name': 'Mohamed', 'age': 25, 'city': 'Chennai'}

# Updating an existing key — overwrites silently
person["age"] = 26
print(person)   # {'name': 'Mohamed', 'age': 26, 'city': 'Chennai'}

# .setdefault() — sets a value ONLY if the key doesn't exist
person.setdefault("country", "India")    # adds it
person.setdefault("name", "Unknown")     # does NOT overwrite — name already exists
print(person["country"])    # India
print(person["name"])       # Mohamed
```

---

## Deleting

```python
person = {"name": "Mohamed", "age": 25, "city": "Chennai"}

# del — delete by key, no return value
del person["city"]
print(person)   # {'name': 'Mohamed', 'age': 25}
del person["xyz"]   # KeyError: 'xyz'

# .pop(key) — delete and return the value
age = person.pop("age")
print(age)      # 25
print(person)   # {'name': 'Mohamed'}
person.pop("xyz")    # KeyError: 'xyz'

# .pop(key, default) — safe pop, returns default if missing
val = person.pop("xyz", None)
print(val)      # None  — no error

# .popitem() — removes and returns the LAST inserted key-value pair as a tuple
person = {"name": "Mohamed", "age": 25, "city": "Chennai"}
item = person.popitem()
print(item)     # ('city', 'Chennai')  — last inserted
print(person)   # {'name': 'Mohamed', 'age': 25}

# .clear() — removes everything
person.clear()
print(person)   # {}
```

---

## Checking Keys

```python
person = {"name": "Mohamed", "age": 25}

# 'in' checks keys only — O(1)
print("name" in person)      # True
print("city" in person)      # False
print("Mohamed" in person)   # False  — "Mohamed" is a VALUE, not a key

# Check values
print("Mohamed" in person.values())   # True

# 'not in'
print("city" not in person)   # True
```

---

## Iterating

```python
person = {"name": "Mohamed", "age": 25, "city": "Chennai"}

# Iterate keys (default)
for key in person:
    print(key)
# name
# age
# city

# Iterate keys explicitly
for key in person.keys():
    print(key)

# Iterate values
for value in person.values():
    print(value)
# Mohamed
# 25
# Chennai

# Iterate key-value pairs — most common
for key, value in person.items():
    print(f"{key}: {value}")
# name: Mohamed
# age: 25
# city: Chennai

# With enumerate on items
for i, (key, value) in enumerate(person.items()):
    print(f"{i}. {key} = {value}")
# 0. name = Mohamed
# 1. age = 25
# 2. city = Chennai
```

---

## View Objects — `.keys()`, `.values()`, `.items()`

These don't return lists — they return **live views** of the dictionary.

```python
person = {"name": "Mohamed", "age": 25}

keys   = person.keys()
values = person.values()
items  = person.items()

print(keys)    # dict_keys(['name', 'age'])
print(values)  # dict_values(['Mohamed', 25])
print(items)   # dict_items([('name', 25), ('age', 25)])

# Live view — changes to dict are reflected immediately
person["city"] = "Chennai"
print(keys)    # dict_keys(['name', 'age', 'city'])  — updated automatically

# Convert to list if you need a snapshot
keys_list = list(person.keys())
```

---

## `.update()` — Merging Dictionaries

```python
person = {"name": "Mohamed", "age": 25}
extra  = {"city": "Chennai", "age": 26}    # 'age' exists in both

person.update(extra)
print(person)   # {'name': 'Mohamed', 'age': 26, 'city': 'Chennai'}
# existing keys are overwritten with values from the argument
```

**Other ways to merge (Python 3.9+):**

```python
a = {"name": "Mohamed", "age": 25}
b = {"city": "Chennai", "age": 26}

# Merge operator | — returns new dict
merged = a | b
print(merged)   # {'name': 'Mohamed', 'age': 26, 'city': 'Chennai'}

# Update operator |= — in-place
a |= b
print(a)        # {'name': 'Mohamed', 'age': 26, 'city': 'Chennai'}
```

---

## Dict Ordering

Since Python 3.7, dictionaries preserve insertion order — guaranteed by the language spec.

```python
d = {}
d["c"] = 3
d["a"] = 1
d["b"] = 2

for key in d:
    print(key)
# c
# a
# b    — insertion order, not alphabetical
```

---

## Nested Dictionaries

Dictionaries inside dictionaries — extremely common with JSON data from APIs.

```python
user = {
    "name": "Mohamed",
    "age": 25,
    "address": {
        "street": "123 Main St",
        "city": "Chennai",
        "state": "Tamil Nadu"
    },
    "skills": ["Python", "FastAPI", "PostgreSQL"]
}

# Accessing nested values
print(user["address"]["city"])       # Chennai
print(user["address"]["state"])      # Tamil Nadu
print(user["skills"][0])             # Python
print(user["skills"][-1])            # PostgreSQL

# Modifying nested values
user["address"]["city"] = "Bangalore"
user["skills"].append("Docker")

# Safe nested access with .get()
city = user.get("address", {}).get("city", "unknown")
print(city)    # Chennai

# Missing key at top level — without the {} default, second .get() would fail
zipcode = user.get("address", {}).get("zipcode", "N/A")
print(zipcode)    # N/A
```

**Iterating nested dicts:**

```python
config = {
    "database": {"host": "localhost", "port": 5432},
    "redis":    {"host": "localhost", "port": 6379},
    "app":      {"host": "0.0.0.0",  "port": 8000}
}

for service, settings in config.items():
    print(f"\n{service}:")
    for key, value in settings.items():
        print(f"  {key}: {value}")
```

---

## Other Useful Dict Methods

```python
person = {"name": "Mohamed", "age": 25, "city": "Chennai"}

# Number of key-value pairs
print(len(person))    # 3

# Copy — shallow copy
copy = person.copy()

# dict.fromkeys() — create dict from a list of keys with a default value
keys = ["name", "age", "city"]
blank = dict.fromkeys(keys, None)
print(blank)    # {'name': None, 'age': None, 'city': None}

blank = dict.fromkeys(keys, 0)
print(blank)    # {'name': 0, 'age': 0, 'city': 0}
```

---

## Putting It All Together

```python
# A realistic example — processing student records
students = [
    {"name": "Mohamed", "score": 88, "city": "Chennai"},
    {"name": "Priya",   "score": 92, "city": "Mumbai"},
    {"name": "Arjun",   "score": 74, "city": "Chennai"},
    {"name": "Sara",    "score": 92, "city": "Delhi"},
    {"name": "Vikram",  "score": 65, "city": "Chennai"},
]

# Unique cities using a set
cities = {s["city"] for s in students}
print(f"Cities: {cities}")

# Top scorers using dict
top_score = max(s["score"] for s in students)
toppers = [s["name"] for s in students if s["score"] == top_score]
print(f"Top score {top_score}: {toppers}")

# Group by city using defaultdict pattern
from collections import defaultdict
by_city = defaultdict(list)
for s in students:
    by_city[s["city"]].append(s["name"])

for city, names in by_city.items():
    print(f"{city}: {names}")

# Score lookup dict — name → score
score_lookup = {s["name"]: s["score"] for s in students}
print(score_lookup.get("Mohamed", 0))   # 88
print(score_lookup.get("Unknown", 0))   # 0
```

---

## Key Takeaways

**Tuples**
- The comma makes a tuple, not the parentheses. `(1)` is an int. `(1,)` is a tuple.
- Immutability is shallow — nested mutable objects can still be mutated.
- Use tuples for fixed data, dict keys, and returning multiple values from functions.
- `*rest` in unpacking always gives a list, even from a tuple.

**Sets**
- `{}` is a dict. Empty set must be `set()`.
- Sets are unordered — no indexing, no slicing.
- `in` on a set is O(1). On a list it's O(n). Use sets for repeated membership checks.
- `.remove()` raises `KeyError`. `.discard()` is silent. Use `.discard()` when unsure.

**Dictionaries**
- `d[key]` raises `KeyError`. `d.get(key, default)` is safe. Choose based on whether absence is a bug.
- `in` checks keys only — O(1). To check values use `in d.values()`.
- `.keys()`, `.values()`, `.items()` are live views, not lists.
- Safe nested access: `d.get("outer", {}).get("inner", default)`.
- Dict preserves insertion order since Python 3.7 — you can rely on this.