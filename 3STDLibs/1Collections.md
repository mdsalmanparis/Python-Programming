Let's go. Full topic, nothing skipped.

---

## 2.1 `collections` — Counter, defaultdict, deque, namedtuple

---

## Why `collections` Exists

Python's built-in `dict`, `list`, and `tuple` cover most cases. `collections` gives you specialised container types that solve specific patterns cleanly — counting, grouping, fast double-ended queues, structured tuples — without writing the boilerplate yourself.

```python
from collections import Counter, defaultdict, deque, namedtuple, OrderedDict
```

---

## `Counter` — Counting Hashables

`Counter` is a dict subclass where values are counts. It counts occurrences of items automatically.

### Creating a Counter

```python
from collections import Counter

# From an iterable — counts each element
c = Counter("aabbccddaab")
print(c)    # Counter({'a': 4, 'b': 3, 'c': 2, 'd': 2})

c = Counter([1, 2, 2, 3, 3, 3, 4])
print(c)    # Counter({3: 3, 2: 2, 1: 1, 4: 1})

words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
c = Counter(words)
print(c)    # Counter({'apple': 3, 'banana': 2, 'cherry': 1})

# From a dict
c = Counter({"a": 5, "b": 2, "c": 8})
print(c)    # Counter({'c': 8, 'a': 5, 'b': 2})

# From keyword arguments
c = Counter(red=4, blue=2, green=3)
print(c)    # Counter({'red': 4, 'green': 3, 'blue': 2})

# Empty counter
c = Counter()
```

### Accessing Counts

```python
c = Counter("aabbbccc")

print(c["a"])       # 2
print(c["b"])       # 3
print(c["z"])       # 0  — missing keys return 0, not KeyError
                         # this is unlike a regular dict

print(c.total())    # 8  — sum of all counts (Python 3.10+)
```

### `most_common(n)` — Top N Elements

```python
c = Counter("abracadabra")
print(c.most_common())      # all elements, most common first
# [('a', 5), ('b', 2), ('r', 2), ('c', 1), ('d', 1)]

print(c.most_common(3))     # top 3
# [('a', 5), ('b', 2), ('r', 2)]

print(c.most_common(1))     # single most common
# [('a', 5)]

# Least common — reverse the list
print(c.most_common()[:-4:-1])   # last 3 in reverse = least common
# [('d', 1), ('c', 1), ('r', 2)]
```

### `update()` vs `subtract()`

```python
c = Counter("aabb")
print(c)    # Counter({'a': 2, 'b': 2})

# update() — ADDS counts (not replaces like dict.update)
c.update("aac")
print(c)    # Counter({'a': 4, 'b': 2, 'c': 1})

c.update({"a": 10, "d": 5})
print(c)    # Counter({'a': 14, 'b': 2, 'd': 5, 'c': 1})

# subtract() — SUBTRACTS counts (allows negative and zero)
c = Counter(a=5, b=3, c=1)
c.subtract(a=2, b=5)
print(c)    # Counter({'a': 3, 'c': 1, 'b': -2})  — b went negative

c.subtract(Counter("aaa"))
print(c)    # Counter({'c': 1, 'b': -2, 'a': 0})
```

### Arithmetic Operations

```python
c1 = Counter(a=4, b=2, c=0, d=-2)
c2 = Counter(a=1, b=2, c=3, d=4)

# + union — adds counts, keeps only positive results
print(c1 + c2)    # Counter({'a': 5, 'b': 4, 'c': 3})
                  # d: -2+4=2... wait, both negative+positive:
                  # actually + keeps only positive sums

# - difference — subtracts, keeps only positive results
print(c1 - c2)    # Counter({'a': 3})  — only a had positive difference

# & intersection — minimum of each count (only positive)
print(c1 & c2)    # Counter({'b': 2, 'a': 1})

# | union — maximum of each count (only positive)
print(c1 | c2)    # Counter({'d': 4, 'a': 4, 'c': 3, 'b': 2})
```

**Practical arithmetic — comparing two texts:**

```python
text1 = Counter("hello world")
text2 = Counter("world peace")

# Words in text1 but not text2
print(text1 - text2)

# Words in both
print(text1 & text2)

# All words from either
print(text1 | text2)
```

### Elements and Other Methods

```python
c = Counter(a=3, b=2, c=1)

# elements() — expands back to an iterable (sorted by insertion order)
print(list(c.elements()))    # ['a', 'a', 'a', 'b', 'b', 'c']

# Check if a string is an anagram of another
def is_anagram(s1: str, s2: str) -> bool:
    return Counter(s1.lower()) == Counter(s2.lower())

print(is_anagram("listen", "silent"))    # True
print(is_anagram("hello", "world"))      # False

# Count word frequencies
text = "the cat sat on the mat the cat"
word_freq = Counter(text.split())
print(word_freq.most_common(3))
# [('the', 3), ('cat', 2), ('sat', 1)]
```

---

## `defaultdict` — Dict with a Default Factory

When you access a missing key in a `defaultdict`, it automatically creates the key with a default value using the factory function. No `KeyError`.

### Creating a defaultdict

```python
from collections import defaultdict

# Factory: int → default 0
counts = defaultdict(int)
counts["a"] += 1
counts["a"] += 1
counts["b"] += 1
print(counts)    # defaultdict(<class 'int'>, {'a': 2, 'b': 1})
print(counts["z"])   # 0 — auto-created with int() = 0

# Factory: list → default []
groups = defaultdict(list)
groups["fruits"].append("apple")
groups["fruits"].append("banana")
groups["veggies"].append("carrot")
print(groups)
# defaultdict(<class 'list'>, {'fruits': ['apple', 'banana'], 'veggies': ['carrot']})

# Factory: set → default set()
seen = defaultdict(set)
seen["user1"].add("page1")
seen["user1"].add("page2")
seen["user2"].add("page1")
print(seen)
# defaultdict(<class 'set'>, {'user1': {'page1', 'page2'}, 'user2': {'page1'}})

# Factory: lambda → any default value
scores = defaultdict(lambda: 100)   # default score is 100
scores["Mohamed"] = 88
print(scores["Priya"])     # 100  — Priya not set, gets default
print(scores["Mohamed"])   # 88   — Mohamed set explicitly
```

### Factory Functions and What They Create

```python
defaultdict(int)        # default: 0
defaultdict(float)      # default: 0.0
defaultdict(str)        # default: ""
defaultdict(list)       # default: []
defaultdict(set)        # default: set()
defaultdict(dict)       # default: {}
defaultdict(tuple)      # default: ()
defaultdict(bool)       # default: False
defaultdict(lambda: -1) # default: -1
defaultdict(lambda: []) # same as defaultdict(list)
```

### `defaultdict` vs `dict.setdefault()`

```python
# Problem: grouping items without defaultdict
data = [("fruits", "apple"), ("veggies", "carrot"), ("fruits", "banana")]

# Regular dict — verbose
groups = {}
for category, item in data:
    if category not in groups:
        groups[category] = []
    groups[category].append(item)

# dict.setdefault() — better but still uses method
groups = {}
for category, item in data:
    groups.setdefault(category, []).append(item)

# defaultdict — cleanest
from collections import defaultdict
groups = defaultdict(list)
for category, item in data:
    groups[category].append(item)
```

**Key difference — `setdefault` vs `defaultdict`:**

```python
d = {}

# setdefault — sets and returns default only if key missing, READS trigger it
d.setdefault("a", [])    # creates "a": [] if not there, returns the list
# Downside: default value is evaluated EVERY TIME you call it
# d.setdefault("a", expensive_computation())  ← expensive() always runs

# defaultdict — factory called only when missing key is ACCESSED
groups = defaultdict(list)
groups["a"]    # triggers list() — creates [] for "a"
groups["a"]    # key exists now — factory NOT called again

# Another difference — behaviour when checking non-existent key
dd = defaultdict(int)
"missing" in dd         # False — 'in' doesn't trigger factory
dd["missing"]           # NOW triggers factory — creates "missing": 0
"missing" in dd         # True — key now exists
```

### Nested defaultdict

```python
# Nested structure — dict of dict of list
from collections import defaultdict

nested = defaultdict(lambda: defaultdict(list))
nested["users"]["admins"].append("Mohamed")
nested["users"]["viewers"].append("Priya")
nested["config"]["keys"].append("secret")

print(nested["users"]["admins"])    # ['Mohamed']
print(nested["missing"]["key"])     # []  — deeply created automatically
```

### Real-World Use Cases

```python
from collections import defaultdict

# 1. Grouping data (GROUP BY equivalent)
students = [
    {"name": "Mohamed", "city": "Chennai"},
    {"name": "Priya",   "city": "Mumbai"},
    {"name": "Arjun",   "city": "Chennai"},
    {"name": "Sara",    "city": "Delhi"},
]

by_city = defaultdict(list)
for s in students:
    by_city[s["city"]].append(s["name"])

print(dict(by_city))
# {'Chennai': ['Mohamed', 'Arjun'], 'Mumbai': ['Priya'], 'Delhi': ['Sara']}

# 2. Counting with conditions
scores = [88, 45, 92, 37, 88, 67, 92, 45]
count_by_score = defaultdict(int)
for score in scores:
    count_by_score[score] += 1
print(dict(count_by_score))   # {88: 2, 45: 2, 92: 2, 37: 1, 67: 1}

# 3. Graph as adjacency list
edges = [(1, 2), (1, 3), (2, 3), (3, 4)]
graph = defaultdict(list)
for src, dst in edges:
    graph[src].append(dst)
    graph[dst].append(src)   # undirected
print(dict(graph))
# {1: [2, 3], 2: [1, 3], 3: [2, 1, 4], 4: [3]}
```

---

## `deque` — Double-Ended Queue

`deque` (pronounced "deck") supports O(1) append and pop from **both** ends. A regular list is O(n) for operations at the front.

### Creating a deque

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])
print(d)            # deque([1, 2, 3, 4, 5])
print(type(d))      # <class 'collections.deque'>

d = deque()         # empty
d = deque("hello")  # deque(['h', 'e', 'l', 'l', 'o'])
d = deque(range(5)) # deque([0, 1, 2, 3, 4])
```

### O(1) Operations at Both Ends

```python
d = deque([2, 3, 4])

# Right end — same as list
d.append(5)        # deque([2, 3, 4, 5])
d.pop()            # returns 5, deque([2, 3, 4])

# Left end — O(1) unlike list.insert(0, x) which is O(n)
d.appendleft(1)    # deque([1, 2, 3, 4])
d.popleft()        # returns 1, deque([2, 3, 4])

# Bulk operations
d.extend([5, 6])       # from right: deque([2, 3, 4, 5, 6])
d.extendleft([1, 0])   # from left, ONE BY ONE: deque([0, 1, 2, 3, 4, 5, 6])
                        # extendleft reverses the order!
```

**Why list is slow at the front:**

```python
import timeit

# list.insert(0) — O(n) — shifts ALL elements right
lst = list(range(10000))
timeit.timeit(lambda: lst.insert(0, -1), number=10000)   # slow

# deque.appendleft — O(1)
from collections import deque
d = deque(range(10000))
timeit.timeit(lambda: d.appendleft(-1), number=10000)    # fast
```

### `maxlen` — Circular Buffer

When `maxlen` is set, the deque automatically discards items from the opposite end when full.

```python
from collections import deque

# Keep only last 3 items
d = deque(maxlen=3)
d.append(1)    # deque([1], maxlen=3)
d.append(2)    # deque([1, 2], maxlen=3)
d.append(3)    # deque([1, 2, 3], maxlen=3)
d.append(4)    # deque([2, 3, 4], maxlen=3)  — 1 dropped from left
d.append(5)    # deque([3, 4, 5], maxlen=3)  — 2 dropped from left

print(d.maxlen)    # 3

# appendleft drops from the right
d = deque(maxlen=3)
d.appendleft(1)    # deque([1], maxlen=3)
d.appendleft(2)    # deque([2, 1], maxlen=3)
d.appendleft(3)    # deque([3, 2, 1], maxlen=3)
d.appendleft(4)    # deque([4, 3, 2], maxlen=3)  — 1 dropped from right
```

**Real-world use — sliding window, recent history:**

```python
# Keep last N log entries
from collections import deque

log_buffer = deque(maxlen=100)

def log(message):
    log_buffer.append(message)

log("server started")
log("user logged in")
# ... 100 more logs ...
log("new event")    # oldest log automatically dropped

# Last N items from a stream — without knowing stream length
def last_n(iterable, n):
    return deque(iterable, maxlen=n)

print(last_n(range(1000), 5))    # deque([995, 996, 997, 998, 999], maxlen=5)
```

### `rotate()` — Shift Elements

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])

d.rotate(2)     # shift right by 2
print(d)        # deque([4, 5, 1, 2, 3])

d.rotate(-2)    # shift left by 2 (undo previous)
print(d)        # deque([1, 2, 3, 4, 5])

d.rotate(1)     # shift right by 1
print(d)        # deque([5, 1, 2, 3, 4])

# rotate(n) = popleft and appendright, n times
# rotate(-n) = pop and appendleft, n times
```

### Other deque Methods

```python
d = deque([1, 2, 3, 4, 3, 2, 1])

d.count(3)           # 2  — count occurrences
d.index(4)           # 3  — first position of 4
d.remove(3)          # removes first 3 → deque([1, 2, 4, 3, 2, 1])
d.reverse()          # in-place reverse
d.clear()            # empty the deque
d.copy()             # shallow copy (Python 3.5+)

# Indexing and slicing — indexing works, slicing does NOT
d = deque([1, 2, 3, 4, 5])
print(d[0])     # 1  — O(1) for first/last
print(d[-1])    # 5  — O(1)
print(d[2])     # 3  — O(n) for middle — deque is not optimised for random access
# d[1:3]        # TypeError — no slicing
```

### Thread Safety

```python
# deque.append() and deque.popleft() are thread-safe in CPython
# This makes deque ideal as a thread-safe queue

from collections import deque
import threading

queue = deque()

def producer():
    for i in range(10):
        queue.append(i)

def consumer():
    while True:
        try:
            item = queue.popleft()
            process(item)
        except IndexError:
            break   # queue empty
```

---

## `namedtuple` — Structured Tuples

A tuple with named fields. Immutable, memory-efficient (no `__dict__`), supports tuple operations AND dot access.

### Creating a namedtuple

```python
from collections import namedtuple

# namedtuple(TypeName, field_names)
Point   = namedtuple("Point", ["x", "y"])
Student = namedtuple("Student", ["name", "score", "city"])
Color   = namedtuple("Color", "red green blue")   # space-separated string works too

p = Point(3, 4)
s = Student("Mohamed", 88, "Chennai")

print(p)           # Point(x=3, y=4)
print(p.x)         # 3     — named access
print(p[0])        # 3     — positional access (it's a tuple)
print(p.x, p.y)    # 3 4
```

### `_fields` — Field Names

```python
Point = namedtuple("Point", ["x", "y", "z"])
print(Point._fields)    # ('x', 'y', 'z')

# Useful for introspection
Student = namedtuple("Student", ["name", "score", "city"])
print(Student._fields)   # ('name', 'score', 'city')

# Iterate fields and values together
s = Student("Mohamed", 88, "Chennai")
for field, value in zip(s._fields, s):
    print(f"{field}: {value}")
# name: Mohamed
# score: 88
# city: Chennai
```

### `_asdict()` — Convert to OrderedDict

```python
s = Student("Mohamed", 88, "Chennai")
d = s._asdict()
print(d)    # {'name': 'Mohamed', 'score': 88, 'city': 'Chennai'}
print(type(d))   # <class 'dict'>  (Python 3.8+ returns regular dict)

# Useful for serialisation
import json
print(json.dumps(s._asdict()))   # '{"name": "Mohamed", "score": 88, "city": "Chennai"}'
```

### `_replace()` — Create Modified Copy

Since namedtuples are immutable, `_replace` creates a new instance with some fields changed.

```python
s = Student("Mohamed", 88, "Chennai")
s2 = s._replace(score=92, city="Mumbai")

print(s)     # Student(name='Mohamed', score=88, city='Chennai')   — unchanged
print(s2)    # Student(name='Mohamed', score=92, city='Mumbai')    — new instance
```

### `_make()` — Create from Iterable

```python
Student = namedtuple("Student", ["name", "score", "city"])

row = ["Priya", 92, "Mumbai"]     # from CSV row, database result, etc.
s = Student._make(row)
print(s)    # Student(name='Priya', score=92, city='Mumbai')

# From a dict — using _make with values
data = {"name": "Arjun", "score": 74, "city": "Delhi"}
s = Student._make(data.values())
print(s)    # Student(name='Arjun', score=74, city='Delhi')
            # WARNING: dict order matters here — use ** for safety:
s = Student(**data)    # safer — keyword arguments
```

### Default Values (Python 3.6.1+)

```python
Student = namedtuple("Student", ["name", "score", "city"], defaults=["Chennai"])
# defaults apply to the LAST N fields
# here: city defaults to "Chennai"

s1 = Student("Mohamed", 88)             # city defaults to Chennai
s2 = Student("Priya",   92, "Mumbai")   # city overridden

print(s1)    # Student(name='Mohamed', score=88, city='Chennai')
print(s2)    # Student(name='Priya', score=92, city='Mumbai')
```

### When to Prefer `dataclass` Over `namedtuple`

```python
from collections import namedtuple
from dataclasses import dataclass

# Use namedtuple when:
# - Data is immutable and should stay immutable
# - You need tuple behaviour (indexing, unpacking, comparison by value)
# - Memory matters — namedtuple has no __dict__ overhead
# - Interop with code expecting tuples

Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)
x, y = p           # tuple unpacking — works
print(p[0])        # positional indexing — works
print(p == (3, 4)) # True — compares equal to plain tuple

# Use dataclass when:
# - You need mutability
# - You need methods beyond simple data access
# - You want __post_init__ validation
# - You don't need tuple behaviour

@dataclass
class Point:
    x: float
    y: float

    def distance(self) -> float:
        return (self.x**2 + self.y**2) ** 0.5

p = Point(3, 4)
p.x = 10           # mutable — works
p.distance()       # 5.0
x, y = p           # TypeError — dataclass doesn't unpack like tuple
```

---

## `OrderedDict` — Insertion-Order-Preserving Dict

Since Python 3.7, regular dicts preserve insertion order too. `OrderedDict` still has two unique features.

### Creating and Using

```python
from collections import OrderedDict

od = OrderedDict()
od["first"]  = 1
od["second"] = 2
od["third"]  = 3

print(od)
# OrderedDict([('first', 1), ('second', 2), ('third', 3)])

# Works like a regular dict
od["fourth"] = 4
del od["second"]
print(list(od.keys()))    # ['first', 'third', 'fourth']
```

### `move_to_end()` — Reorder Entries

```python
from collections import OrderedDict

od = OrderedDict([("a", 1), ("b", 2), ("c", 3), ("d", 4)])

od.move_to_end("b")         # move "b" to the END
print(list(od.keys()))      # ['a', 'c', 'd', 'b']

od.move_to_end("c", last=False)   # move "c" to the BEGINNING
print(list(od.keys()))      # ['c', 'a', 'd', 'b']
```

**LRU cache implementation using `OrderedDict`:**

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache    = OrderedDict()

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)   # mark as recently used
        return self.cache[key]

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)   # evict least recently used (first item)

lru = LRUCache(3)
lru.put(1, "a")
lru.put(2, "b")
lru.put(3, "c")
lru.get(1)        # access 1 — moves to end, order: 2, 3, 1
lru.put(4, "d")   # evicts 2 (least recently used), order: 3, 1, 4
print(lru.get(2)) # -1  — evicted
print(lru.get(3)) # 'c' — still in cache
```

### Equality Semantics — OrderedDict vs dict

```python
from collections import OrderedDict

# Regular dict — equality ignores order
d1 = {"a": 1, "b": 2}
d2 = {"b": 2, "a": 1}
print(d1 == d2)    # True — same keys and values, order doesn't matter

# OrderedDict — equality CONSIDERS order
od1 = OrderedDict([("a", 1), ("b", 2)])
od2 = OrderedDict([("b", 2), ("a", 1)])
print(od1 == od2)   # False — different insertion order

# OrderedDict vs dict — order doesn't matter when compared cross-type
print(od1 == d1)    # True — OrderedDict vs dict ignores order
```

### `popitem(last=True/False)`

```python
from collections import OrderedDict

od = OrderedDict([("a", 1), ("b", 2), ("c", 3)])

od.popitem(last=True)    # removes and returns last item: ('c', 3)
od.popitem(last=False)   # removes and returns first item: ('a', 1)
print(od)                # OrderedDict([('b', 2)])
```

---

## Other `collections` Types — Brief Coverage

### `ChainMap` — Layered Lookups

```python
from collections import ChainMap

defaults = {"color": "blue", "size": "M", "debug": False}
user_prefs = {"color": "red"}
cli_args   = {"debug": True}

# Searches maps in order — first one wins
config = ChainMap(cli_args, user_prefs, defaults)

print(config["color"])    # red    — from user_prefs (cli_args doesn't have it)
print(config["debug"])    # True   — from cli_args (highest priority)
print(config["size"])     # M      — from defaults (not in others)

# Modify only updates the FIRST map
config["color"] = "green"
print(cli_args)      # {'debug': True, 'color': 'green'}  — wrote to first map
print(user_prefs)    # {'color': 'red'}  — unchanged
print(defaults)      # unchanged

# New child scope
child_config = config.new_child({"size": "L"})
print(child_config["size"])    # L  — child overrides
print(config["size"])          # M  — parent unchanged
```

### `UserDict`, `UserList`, `UserString` — Safe Subclassing

```python
from collections import UserDict, UserList

# Subclass UserDict instead of dict — avoids method resolution issues
class UpperDict(UserDict):
    def __setitem__(self, key, value):
        super().__setitem__(key.upper(), value)   # store keys uppercase

d = UpperDict()
d["name"] = "Mohamed"
d["city"] = "Chennai"
print(d)    # {'NAME': 'Mohamed', 'CITY': 'Chennai'}
print(d["name"])    # KeyError — "name" not in dict (was stored as "NAME")
print(d["NAME"])    # Mohamed

# UserList — easier to subclass than list
class PositiveList(UserList):
    def append(self, item):
        if item <= 0:
            raise ValueError(f"{item} must be positive")
        super().append(item)

pl = PositiveList([1, 2, 3])
pl.append(4)      # OK
pl.append(-1)     # ValueError: -1 must be positive
```

---

## Putting It All Together

```python
from collections import Counter, defaultdict, deque, namedtuple, OrderedDict
from typing import NamedTuple
import json

# Dataset — web server access log entries
LogEntry = namedtuple("LogEntry", ["ip", "method", "path", "status", "size"])

raw_logs = [
    ("192.168.1.1", "GET",  "/home",    200, 1024),
    ("192.168.1.2", "POST", "/login",   200, 512),
    ("192.168.1.1", "GET",  "/about",   200, 2048),
    ("192.168.1.3", "GET",  "/home",    404, 256),
    ("192.168.1.2", "GET",  "/home",    200, 1024),
    ("192.168.1.1", "POST", "/api",     500, 128),
    ("192.168.1.4", "GET",  "/home",    200, 1024),
    ("192.168.1.1", "GET",  "/api",     200, 2048),
    ("192.168.1.3", "POST", "/login",   401, 256),
    ("192.168.1.4", "GET",  "/about",   200, 1536),
]

# Parse into namedtuples
logs = [LogEntry(*row) for row in raw_logs]

# 1. Counter — top IPs, most requested paths, status distribution
ip_counter     = Counter(log.ip for log in logs)
path_counter   = Counter(log.path for log in logs)
status_counter = Counter(log.status for log in logs)

print("Top IPs:")
for ip, count in ip_counter.most_common(3):
    print(f"  {ip}: {count} requests")

print("\nMost popular paths:")
for path, count in path_counter.most_common():
    print(f"  {path}: {count}")

print(f"\nStatus codes: {dict(status_counter)}")

# 2. defaultdict — group by IP and by status
by_ip     = defaultdict(list)
by_status = defaultdict(list)

for log in logs:
    by_ip[log.ip].append(log)
    by_status[log.status].append(log)

# Errors per IP
print("\nErrors per IP:")
for ip, entries in by_ip.items():
    errors = [e for e in entries if e.status >= 400]
    if errors:
        print(f"  {ip}: {len(errors)} errors")

# 3. deque — sliding window for recent errors
recent_errors: deque = deque(maxlen=3)
for log in logs:
    if log.status >= 400:
        recent_errors.append(log)

print(f"\nLast 3 errors:")
for entry in recent_errors:
    print(f"  {entry.ip} → {entry.path} ({entry.status})")

# 4. namedtuple operations — asdict for JSON serialisation
print("\nFirst log as JSON:")
print(json.dumps(logs[0]._asdict()))

# Replace status of a log for reporting
corrected = logs[5]._replace(status=200)
print(f"\nCorrected: {corrected}")

# 5. OrderedDict — track request order by IP (LRU style)
request_order = OrderedDict()
for log in logs:
    if log.ip in request_order:
        request_order.move_to_end(log.ip)
    else:
        request_order[log.ip] = 0
    request_order[log.ip] += 1

print("\nIPs by last activity order (most recent last):")
for ip, count in request_order.items():
    print(f"  {ip}: {count} total requests")
```

---

## Key Takeaways

**Counter**
- Missing keys return `0` not `KeyError` — safe to access without checking.
- `update()` adds counts. `subtract()` subtracts (allows negatives). Both differ from dict's update.
- `+` and `&` and `|` produce only positive counts. `-` keeps only positive differences.
- `most_common(n)` is O(n log k) — uses a heap internally.

**defaultdict**
- Factory is called with zero arguments when a missing key is accessed.
- `in` does NOT trigger the factory. `d[missing_key]` does.
- `setdefault` evaluates the default every call. `defaultdict` factory only runs on miss.
- Converting to regular dict: `dict(my_defaultdict)` — stops auto-creation of keys.

**deque**
- `append`/`pop` on right: O(1). `appendleft`/`popleft` on left: O(1). Random access by index: O(n).
- `maxlen` creates a circular buffer — automatic eviction from the opposite end.
- `extendleft` reverses order — each item is `appendleft`-ed one by one.
- Thread-safe for `append` and `popleft` in CPython — safe as a simple queue.

**namedtuple**
- `_fields` — tuple of field names. `_asdict()` — to dict. `_replace()` — new copy with changes. `_make()` — from iterable.
- Prefer `namedtuple` for immutable records that need tuple semantics (unpacking, indexing, comparison to tuple).
- Prefer `dataclass` when you need mutability, methods, or validation.

**OrderedDict**
- `move_to_end(key, last=True/False)` — reposition a key. Used in LRU cache.
- `popitem(last=True)` removes last. `popitem(last=False)` removes first.
- `OrderedDict == OrderedDict` considers order. `OrderedDict == dict` does not.