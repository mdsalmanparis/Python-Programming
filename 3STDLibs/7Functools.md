Let's go. Full topic, nothing skipped.

---

## 2.7 `functools`

---

## What `functools` Is

`functools` is the standard library module for higher-order functions — tools that operate on or return other functions. Everything in it is about making callables more powerful, composable, or efficient.

```python
from functools import (
    lru_cache, cache, cached_property,
    partial, singledispatch, total_ordering, wraps,
    reduce, cmp_to_key
)
```

---

## `functools.wraps` — Copying Function Metadata in Decorators

Already covered in depth in 1.7, but consolidated here as the `functools` module topic.

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)                    # copies __name__, __doc__, __annotations__, __wrapped__
    def wrapper(*args, **kwargs):
        print("before")
        result = func(*args, **kwargs)
        print("after")
        return result
    return wrapper

@my_decorator
def add(a: int, b: int) -> int:
    """Returns the sum of a and b."""
    return a + b

print(add.__name__)       # add        ← correct (without @wraps: 'wrapper')
print(add.__doc__)        # Returns... ← correct (without @wraps: None)
print(add.__wrapped__)    # <function add> ← reference to original

import inspect
print(inspect.unwrap(add))    # unwrap all layers to reach original
```

**What `@wraps(func)` does internally:**

```python
from functools import update_wrapper

# @wraps(func) is shorthand for:
def my_decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    update_wrapper(wrapper, func)   # copies attributes from func to wrapper
    return wrapper

# update_wrapper copies:
WRAPPER_ASSIGNMENTS = ('__module__', '__name__', '__qualname__',
                       '__annotations__', '__doc__')
WRAPPER_UPDATES = ('__dict__',)   # merges __dict__
# and sets __wrapped__ = func
```

---

## `functools.lru_cache` — Memoization with LRU Eviction

Caches the return value of a function keyed on its arguments. Same arguments → return cached result, skip recomputation.

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n: int) -> int:
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(50))    # 12586269025 — instant
print(fibonacci(100))   # 354224848179261915075 — also instant
```

**Without cache — exponential time complexity:**

```python
def fib_slow(n):
    if n < 2:
        return n
    return fib_slow(n-1) + fib_slow(n-2)

# fib_slow(40) takes seconds — computes 2^40 calls
# fibonacci(40) with cache takes microseconds — computes 41 calls
```

### `cache_info()` — Inspect Cache Performance

```python
from functools import lru_cache

@lru_cache(maxsize=64)
def square(n: int) -> int:
    print(f"  computing {n}²")
    return n * n

square(5)    # computing 5²
square(5)    # cache hit — no recomputation
square(6)    # computing 6²
square(5)    # cache hit

info = square.cache_info()
print(info)
# CacheInfo(hits=2, misses=2, maxsize=64, currsize=2)

print(info.hits)      # 2  — returned from cache
print(info.misses)    # 2  — actually computed
print(info.maxsize)   # 64 — max entries
print(info.currsize)  # 2  — currently cached
```

### `cache_clear()` — Invalidate the Cache

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def get_user(user_id: int) -> dict:
    print(f"fetching user {user_id} from DB")
    return {"id": user_id, "name": "Mohamed"}

get_user(1)    # fetching user 1 from DB
get_user(1)    # cache hit
get_user(2)    # fetching user 2 from DB

print(get_user.cache_info())   # hits=1, misses=2, currsize=2

get_user.cache_clear()
print(get_user.cache_info())   # hits=0, misses=0, currsize=0

get_user(1)    # fetching user 1 from DB — cache was cleared
```

### LRU Eviction — `maxsize`

```python
from functools import lru_cache

@lru_cache(maxsize=3)   # keep only 3 most recently used
def compute(n):
    print(f"  computing {n}")
    return n * n

compute(1)    # computing 1  — cache: {1}
compute(2)    # computing 2  — cache: {1, 2}
compute(3)    # computing 3  — cache: {1, 2, 3}
compute(1)    # cache hit    — cache: {2, 3, 1} (1 is now most recent)
compute(4)    # computing 4  — cache full, evict LRU (2) → cache: {3, 1, 4}
compute(2)    # computing 2  — 2 was evicted, recompute
```

**LRU = Least Recently Used. When cache is full, the entry not accessed for the longest time is evicted.**

### Arguments Must Be Hashable

```python
from functools import lru_cache

@lru_cache
def process(data):
    return sum(data)

process((1, 2, 3))      # OK — tuple is hashable
process(frozenset([1]))  # OK — frozenset is hashable
process([1, 2, 3])      # TypeError: unhashable type: 'list'
process({"a": 1})       # TypeError: unhashable type: 'dict'

# Workaround for unhashable arguments
@lru_cache
def process_list(data: tuple) -> int:   # accept tuple, document the convention
    return sum(data)

my_list = [1, 2, 3]
process_list(tuple(my_list))    # convert before calling
```

### `maxsize=None` — Unlimited Cache

```python
from functools import lru_cache

@lru_cache(maxsize=None)   # grow indefinitely — no eviction
def fib(n):
    if n < 2: return n
    return fib(n-1) + fib(n-2)

# Identical to @cache (Python 3.9+)
```

---

## `functools.cache` — Simpler Unbounded Cache (Python 3.9+)

```python
from functools import cache

@cache
def fib(n: int) -> int:
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)

print(fib(200))    # 280571172992510140037611932413038677189525

# @cache is equivalent to @lru_cache(maxsize=None)
# Slightly faster because it skips the LRU bookkeeping
# Use @cache when you never need eviction (bounded input space)
# Use @lru_cache(maxsize=N) when inputs are unbounded and memory matters
```

**When to use `@cache` vs `@lru_cache`:**

```python
# @cache — use when:
# - Input space is small and bounded (config lookups, small integer ranges)
# - You want maximum speed (skips LRU overhead)
# - Memory is not a concern

@cache
def load_config(env: str) -> dict:
    # env is one of "dev", "test", "prod" — tiny set, never evict
    return read_config_file(env)

# @lru_cache(maxsize=N) — use when:
# - Input space is large or unbounded (user IDs, URLs)
# - You want to limit memory usage
# - Older entries should be evicted when cache fills

@lru_cache(maxsize=1000)
def fetch_user_profile(user_id: int) -> dict:
    return database.get_user(user_id)
```

---

## `functools.cached_property` — Lazy Attribute

Computes a value on first access, stores it on the instance, never recomputes. Like a property that caches its own result.

```python
from functools import cached_property
import math
import statistics

class DataSet:
    def __init__(self, data: list[float]):
        self._data = data

    @cached_property
    def mean(self) -> float:
        print("computing mean...")
        return statistics.mean(self._data)

    @cached_property
    def std_dev(self) -> float:
        print("computing std_dev...")
        return statistics.stdev(self._data)

    @cached_property
    def sorted_data(self) -> list[float]:
        print("sorting...")
        return sorted(self._data)

    @cached_property
    def percentile_90(self) -> float:
        d = self.sorted_data    # uses cached sorted_data if already computed
        idx = int(len(d) * 0.9)
        return d[idx]

ds = DataSet([4, 7, 13, 2, 1, 8, 15, 6, 3, 9])

print(ds.mean)           # computing mean... → 6.8
print(ds.mean)           # 6.8  — no recomputation
print(ds.std_dev)        # computing std_dev... → 4.54...
print(ds.sorted_data)    # sorting... → [1, 2, 3, 4, 6, 7, 8, 9, 13, 15]
print(ds.percentile_90)  # no "sorting..." — sorted_data was cached
```

**How it works — the descriptor protocol:**

```python
from functools import cached_property

class Circle:
    def __init__(self, radius):
        self.radius = radius

    @cached_property
    def area(self):
        return math.pi * self.radius ** 2

c = Circle(5)

# First access — descriptor __get__ is called, computes value, stores in __dict__
print("area" in c.__dict__)    # False — not yet computed
print(c.area)                  # computes: 78.539...
print("area" in c.__dict__)    # True  — now stored directly on instance
print(c.__dict__["area"])      # 78.539... — value in instance dict

# Second access — instance __dict__ takes precedence over descriptor
print(c.area)    # 78.539... — reads from __dict__, descriptor not called
```

**Manually invalidating `cached_property`:**

```python
c = Circle(5)
print(c.area)         # computed and cached

# "Update" the radius — cache is now stale
c.radius = 10

# Invalidate by deleting from __dict__
del c.__dict__["area"]

print(c.area)         # recomputed: 314.159...
```

**`cached_property` requires a writable `__dict__` — incompatible with `__slots__`:**

```python
class Bad:
    __slots__ = ["radius"]

    @cached_property
    def area(self):
        return math.pi * self.radius ** 2

b = Bad()
b.radius = 5
b.area    # TypeError: Cannot use cached_property with __slots__ on 'Bad' object
```

**`@property` vs `@cached_property`:**

```python
class Example:
    data = [1, 2, 3, 4, 5]

    @property
    def computed(self) -> float:
        print("computing...")
        return sum(self.data) / len(self.data)

    @cached_property
    def cached_computed(self) -> float:
        print("computing...")
        return sum(self.data) / len(self.data)

e = Example()

e.computed          # computing... 3.0
e.computed          # computing... 3.0  — runs AGAIN every access
e.computed          # computing... 3.0  — always recomputes

e.cached_computed   # computing... 3.0  — runs ONCE
e.cached_computed   # 3.0               — from cache
e.cached_computed   # 3.0               — from cache
```

---

## `functools.partial()` — Partial Application

Creates a new callable with some arguments pre-filled.

```python
from functools import partial

def power(base: float, exponent: float) -> float:
    return base ** exponent

# Create specialised callables
square = partial(power, exponent=2)
cube   = partial(power, exponent=3)
square_root = partial(power, exponent=0.5)

print(square(5))       # 25.0
print(cube(3))         # 27.0
print(square_root(16)) # 4.0

# Inspect partial
print(square.func)         # <function power>
print(square.args)         # ()   — no frozen positional args
print(square.keywords)     # {'exponent': 2}
```

### Freezing Positional Arguments

```python
from functools import partial

def add(a: int, b: int, c: int) -> int:
    return a + b + c

add10 = partial(add, 10)         # freeze a=10
print(add10(1, 2))               # 13   — 10 + 1 + 2

add10_20 = partial(add, 10, 20)  # freeze a=10, b=20
print(add10_20(5))               # 35   — 10 + 20 + 5

# Freeze from the left — partial fills positional args from left
def log(level: str, message: str, timestamp: str) -> str:
    return f"[{timestamp}] {level}: {message}"

log_error = partial(log, "ERROR")
log_info  = partial(log, "INFO")

print(log_error("something broke", "2024-11-15"))
# [2024-11-15] ERROR: something broke

print(log_info("server started", "2024-11-15"))
# [2024-11-15] INFO: server started
```

### Real-World Use Cases

```python
from functools import partial

# 1. Configuring open() for consistent file access
open_utf8 = partial(open, encoding="utf-8", errors="replace")
open_binary = partial(open, mode="rb")

with open_utf8("config.txt") as f:    # no need to repeat encoding
    config = f.read()

# 2. Configuring sort key
from functools import partial

def sort_key(item: dict, field: str, reverse: bool = False):
    val = item[field]
    return (-val if isinstance(val, (int, float)) and reverse else val)

students = [
    {"name": "Mohamed", "score": 88},
    {"name": "Priya",   "score": 92},
    {"name": "Arjun",   "score": 74},
]

sort_by_score_desc = partial(sort_key, field="score", reverse=True)
print(sorted(students, key=sort_by_score_desc))

# 3. Event handlers — partial for callbacks with pre-bound data
def on_button_click(button_id: int, event_type: str, widget: object) -> None:
    print(f"Button {button_id} received {event_type}")

# Bind button_id to the handler
button_1_handler = partial(on_button_click, 1)
button_2_handler = partial(on_button_click, 2)

# 4. Retry configuration
import time
from functools import partial

def retry(func, times: int, delay: float, *args, **kwargs):
    for attempt in range(times):
        try:
            return func(*args, **kwargs)
        except Exception as e:
            if attempt == times - 1:
                raise
            time.sleep(delay)

# Create retry variants
retry_3_times = partial(retry, times=3, delay=1.0)
retry_5_times = partial(retry, times=5, delay=0.5)

# retry_3_times(fetch_data, url)
# retry_5_times(send_email, recipient, subject)

# 5. map() with extra arguments
def multiply(x: int, factor: int) -> int:
    return x * factor

numbers = [1, 2, 3, 4, 5]
tripled = list(map(partial(multiply, factor=3), numbers))
print(tripled)    # [3, 6, 9, 12, 15]
```

**`partial` vs `lambda` — prefer `partial` when binding arguments to an existing function:**

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

# lambda — works but less informative
square = lambda x: power(x, 2)

# partial — better: preserves function name info, introspectable
square = partial(power, exponent=2)
print(square.func.__name__)   # power  ← you can see the original function
print(square.keywords)        # {'exponent': 2}
```

---

## `functools.singledispatch` — Type-Based Overloading

Register different implementations of a function for different argument types. Dispatches based on the type of the first argument.

```python
from functools import singledispatch

@singledispatch
def process(value):
    """Default implementation — called when no specific type matches."""
    raise TypeError(f"Cannot process type: {type(value).__name__}")

@process.register(int)
def _(value: int) -> str:
    return f"integer: {value * 2}"

@process.register(str)
def _(value: str) -> str:
    return f"string: {value.upper()}"

@process.register(list)
def _(value: list) -> str:
    return f"list with {len(value)} items: {value}"

@process.register(float)
def _(value: float) -> str:
    return f"float: {value:.4f}"

print(process(42))              # integer: 84
print(process("hello"))         # string: HELLO
print(process([1, 2, 3]))       # list with 3 items: [1, 2, 3]
print(process(3.14))            # float: 3.1400
```

### Registering Multiple Types with One Handler

```python
from functools import singledispatch

@singledispatch
def serialize(value) -> str:
    return str(value)

# Same handler for int and float
@serialize.register(int)
@serialize.register(float)
def _(value) -> str:
    return f"{value:g}"   # removes trailing zeros

# Python 3.11+: register with Union
# @serialize.register(int | float)
# def _(value): ...

print(serialize(42))      # 42
print(serialize(3.14))    # 3.14
print(serialize(3.0))     # 3  — trailing zero removed
print(serialize("text"))  # text  — default
```

### Inspecting Registered Implementations

```python
from functools import singledispatch

@singledispatch
def convert(value):
    raise TypeError(f"unsupported: {type(value)}")

@convert.register(str)
def _(value): return int(value)

@convert.register(float)
def _(value): return int(value)

# See all registered types
print(convert.registry)
# {<class 'object'>: <function convert>, <class 'str'>: <function _>, <class 'float'>: <function _>}

# See which implementation would be used
print(convert.dispatch(str))      # <function _> — the str implementation
print(convert.dispatch(int))      # <function convert> — falls back to base
print(convert.dispatch(bool))     # str's? No — dispatches to object (base)
                                  # bool → int → object chain
```

### `singledispatchmethod` — For Instance Methods

```python
from functools import singledispatchmethod

class Processor:
    @singledispatchmethod
    def process(self, value):
        raise TypeError(f"Cannot process {type(value)}")

    @process.register(int)
    def _(self, value: int) -> str:
        return f"int: {value * 2}"

    @process.register(str)
    def _(self, value: str) -> str:
        return f"str: {value.upper()}"

    @process.register(list)
    def _(self, value: list) -> str:
        return f"list[{len(value)}]: {sorted(value)}"

p = Processor()
print(p.process(10))          # int: 20
print(p.process("hello"))     # str: HELLO
print(p.process([3, 1, 2]))   # list[3]: [1, 2, 3]
```

### Real-World Use — JSON Serialization

```python
from functools import singledispatch
from datetime import datetime, date
from decimal import Decimal
from uuid import UUID
from pathlib import Path
from enum import Enum

@singledispatch
def to_json_serializable(obj):
    """Convert Python objects to JSON-serializable types."""
    if hasattr(obj, "to_dict"):
        return obj.to_dict()
    if hasattr(obj, "__dict__"):
        return obj.__dict__
    raise TypeError(f"Not JSON serializable: {type(obj).__name__}")

@to_json_serializable.register(datetime)
def _(obj): return obj.isoformat()

@to_json_serializable.register(date)
def _(obj): return obj.isoformat()

@to_json_serializable.register(Decimal)
def _(obj): return str(obj)

@to_json_serializable.register(UUID)
def _(obj): return str(obj)

@to_json_serializable.register(Path)
def _(obj): return str(obj)

@to_json_serializable.register(set)
@to_json_serializable.register(frozenset)
def _(obj): return sorted(list(obj))

@to_json_serializable.register(Enum)
def _(obj): return obj.value

import json

def app_dumps(obj, **kwargs) -> str:
    return json.dumps(obj, default=to_json_serializable, **kwargs)

from uuid import uuid4
data = {
    "id":       uuid4(),
    "created":  datetime.now(),
    "price":    Decimal("49.99"),
    "tags":     {"python", "backend"},
}
print(app_dumps(data, indent=2))
```

---

## `functools.total_ordering` — Completing Comparison Methods

Implement `__eq__` and ONE of `__lt__`, `__le__`, `__gt__`, `__ge__`, get the other three automatically.

```python
from functools import total_ordering

@total_ordering
class Student:
    def __init__(self, name: str, score: float):
        self.name  = name
        self.score = score

    def __repr__(self) -> str:
        return f"Student({self.name!r}, {self.score})"

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, Student):
            return NotImplemented
        return self.score == other.score

    def __lt__(self, other: "Student") -> bool:
        if not isinstance(other, Student):
            return NotImplemented
        return self.score < other.score

    # @total_ordering generates: __le__, __gt__, __ge__
    # from __eq__ and __lt__

s1 = Student("Mohamed", 88)
s2 = Student("Priya", 92)
s3 = Student("Arjun", 88)

print(s1 < s2)     # True   — 88 < 92
print(s2 > s1)     # True   — generated __gt__
print(s1 <= s3)    # True   — 88 <= 88, generated __le__
print(s2 >= s1)    # True   — generated __ge__
print(s1 == s3)    # True   — 88 == 88

# Works with sorted and min/max
students = [s2, s1, s3]
print(sorted(students))      # [Student('Mohamed', 88), Student('Arjun', 88), Student('Priya', 92)]
print(min(students))         # Student('Mohamed', 88)  — first 88 found
print(max(students))         # Student('Priya', 92)
```

**When `@total_ordering` is NOT the right choice:**

```python
# Use @total_ordering when ordering is simple and consistent
# Don't use it when:
# - Performance is critical (each generated method chains through 2 calls)
# - You need custom logic for each comparison (just define all 6)
# - __hash__ is needed (define it separately — @total_ordering doesn't touch it)

@total_ordering
class Version:
    def __init__(self, major, minor, patch):
        self.major = major
        self.minor = minor
        self.patch = patch

    def __eq__(self, other):
        if not isinstance(other, Version):
            return NotImplemented
        return (self.major, self.minor, self.patch) == (other.major, other.minor, other.patch)

    def __lt__(self, other):
        if not isinstance(other, Version):
            return NotImplemented
        return (self.major, self.minor, self.patch) < (other.major, other.minor, other.patch)

    def __hash__(self):
        return hash((self.major, self.minor, self.patch))

v1 = Version(1, 9, 0)
v2 = Version(1, 10, 0)
v3 = Version(2, 0, 0)

print(v1 < v2)     # True   — 1.9.0 < 1.10.0
print(v2 < v3)     # True   — 1.10.0 < 2.0.0
print(sorted([v3, v1, v2]))   # [1.9.0, 1.10.0, 2.0.0]
versions = {v1, v2, v3}       # works — __hash__ defined
```

---

## `functools.reduce` — Fold Over a Sequence

Apply a function of two arguments cumulatively to reduce a sequence to a single value.

```python
from functools import reduce
import operator

nums = [1, 2, 3, 4, 5]

# reduce(func, sequence, initializer=None)
print(reduce(lambda acc, x: acc + x, nums))          # 15 — sum
print(reduce(lambda acc, x: acc * x, nums))          # 120 — product
print(reduce(lambda acc, x: max(acc, x), nums))      # 5 — maximum
print(reduce(lambda acc, x: acc + [x, x], nums, [])) # [1,1,2,2,3,3,4,4,5,5]

# With operator module — cleaner than lambda
print(reduce(operator.add, nums))    # 15
print(reduce(operator.mul, nums))    # 120

# With initializer — starting value
print(reduce(operator.add, nums, 100))   # 115 — starts from 100
print(reduce(operator.add, [], 0))       # 0   — empty sequence with initializer

# Empty sequence without initializer raises TypeError
try:
    reduce(operator.add, [])   # TypeError: reduce() of empty sequence with no initial value
except TypeError as e:
    print(e)

# Flatten nested list
nested = [[1, 2], [3, 4], [5, 6]]
flat = reduce(operator.add, nested)
print(flat)    # [1, 2, 3, 4, 5, 6]
               # NOTE: list(chain.from_iterable(nested)) is much faster

# Pipeline of functions
def pipeline(*funcs):
    return reduce(lambda f, g: lambda x: g(f(x)), funcs)

double     = lambda x: x * 2
add_one    = lambda x: x + 1
to_string  = lambda x: str(x)

process = pipeline(double, add_one, to_string)
print(process(5))    # "11"  — 5*2=10, 10+1=11, str(11)="11"
```

---

## `functools.cmp_to_key` — Converting Old-Style Comparators

```python
from functools import cmp_to_key

# Old-style comparator: returns -1, 0, or 1
def compare_by_last_char(a: str, b: str) -> int:
    if a[-1] < b[-1]: return -1
    if a[-1] > b[-1]: return 1
    return 0

words = ["banana", "apple", "cherry", "fig"]
print(sorted(words, key=cmp_to_key(compare_by_last_char)))
# ['banana', 'apple', 'fig', 'cherry']  — sorted by last char: a, e, g, y

# Real use case — version sorting
def version_compare(a: str, b: str) -> int:
    a_parts = list(map(int, a.split(".")))
    b_parts = list(map(int, b.split(".")))
    if a_parts < b_parts: return -1
    if a_parts > b_parts: return 1
    return 0

versions = ["1.10.0", "1.9.0", "2.0.0", "1.9.1"]
print(sorted(versions, key=cmp_to_key(version_compare)))
# ['1.9.0', '1.9.1', '1.10.0', '2.0.0']  — numerically correct
```

---

## Putting It All Together

```python
from functools import (
    lru_cache, cache, cached_property,
    partial, singledispatch, total_ordering, wraps, reduce
)
from datetime import datetime, date
from decimal import Decimal
from typing import Any
import operator
import math

# ── 1. total_ordering + cached_property ──────────────────────────────────────

@total_ordering
class Student:
    def __init__(self, name: str, scores: list[float]):
        self.name   = name
        self.scores = scores

    def __repr__(self) -> str:
        return f"Student({self.name!r}, avg={self.average:.1f})"

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, Student): return NotImplemented
        return self.average == other.average

    def __lt__(self, other: "Student") -> bool:
        if not isinstance(other, Student): return NotImplemented
        return self.average < other.average

    def __hash__(self) -> int:
        return hash(self.name)

    @cached_property
    def average(self) -> float:
        return sum(self.scores) / len(self.scores)

    @cached_property
    def grade(self) -> str:
        a = self.average
        if a >= 90: return "A"
        if a >= 80: return "B"
        if a >= 70: return "C"
        if a >= 60: return "D"
        return "F"

    @cached_property
    def std_dev(self) -> float:
        avg = self.average
        variance = sum((s - avg) ** 2 for s in self.scores) / len(self.scores)
        return math.sqrt(variance)

# ── 2. lru_cache for expensive lookups ───────────────────────────────────────

@lru_cache(maxsize=256)
def percentile(scores_tuple: tuple[float, ...], p: float) -> float:
    """p-th percentile of a sorted tuple of scores."""
    if not 0 <= p <= 100:
        raise ValueError(f"percentile must be 0-100, got {p}")
    sorted_scores = sorted(scores_tuple)
    idx = int(len(sorted_scores) * p / 100)
    return sorted_scores[min(idx, len(sorted_scores) - 1)]

# ── 3. singledispatch for serialisation ──────────────────────────────────────

@singledispatch
def to_serializable(obj: Any) -> Any:
    if hasattr(obj, "__dict__"):
        return obj.__dict__
    raise TypeError(f"Not serializable: {type(obj).__name__}")

@to_serializable.register(Student)
def _(s: Student) -> dict:
    return {
        "name":    s.name,
        "average": s.average,
        "grade":   s.grade,
        "std_dev": round(s.std_dev, 2),
    }

@to_serializable.register(datetime)
def _(obj): return obj.isoformat()

@to_serializable.register(Decimal)
def _(obj): return float(obj)

# ── 4. partial for reusable sort configurations ───────────────────────────────

sort_by_average_desc = partial(sorted, key=lambda s: s.average, reverse=True)
sort_by_name         = partial(sorted, key=lambda s: s.name)

# ── 5. wraps in a decorator that uses lru_cache inspection ───────────────────

def cache_stats(func):
    """Decorator that reports cache stats on every call (if cached)."""
    @wraps(func)
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        if hasattr(func, "cache_info"):
            info = func.cache_info()
            hit_rate = info.hits / max(info.hits + info.misses, 1) * 100
            print(f"[cache] {func.__name__}: {hit_rate:.0f}% hit rate "
                  f"({info.hits} hits, {info.misses} misses, {info.currsize} cached)")
        return result
    return wrapper

cached_percentile = cache_stats(percentile)

# ── 6. reduce for aggregation ─────────────────────────────────────────────────

def class_summary(students: list[Student]) -> dict:
    if not students:
        return {}
    scores = [s.average for s in students]
    return {
        "count":    len(students),
        "total":    reduce(operator.add, scores),
        "highest":  reduce(lambda a, b: a if a > b else b, scores),
        "lowest":   reduce(lambda a, b: a if a < b else b, scores),
        "average":  reduce(operator.add, scores) / len(scores),
    }

# ── Demo ──────────────────────────────────────────────────────────────────────

students = [
    Student("Mohamed", [88, 92, 85, 90]),
    Student("Priya",   [95, 88, 92, 97]),
    Student("Arjun",   [74, 68, 80, 72]),
    Student("Sara",    [92, 95, 88, 91]),
    Student("Vikram",  [55, 60, 58, 62]),
]

print("Ranked by average (desc):")
for rank, s in enumerate(sort_by_average_desc(students), 1):
    print(f"  {rank}. {s}")

print("\nSerialized:")
for s in students[:2]:
    print(f"  {to_serializable(s)}")

print("\nClass summary:")
summary = class_summary(students)
for k, v in summary.items():
    print(f"  {k}: {v:.2f}" if isinstance(v, float) else f"  {k}: {v}")

print("\nPercentile calls (with cache stats):")
all_scores = tuple(s.average for s in students)
cached_percentile(all_scores, 50)
cached_percentile(all_scores, 90)
cached_percentile(all_scores, 50)   # cache hit

print(f"\npercentile cache info: {percentile.cache_info()}")
```

---

## Key Takeaways

- **`@wraps(func)`** — always use inside decorators. Copies `__name__`, `__doc__`, `__annotations__`, sets `__wrapped__`. Without it your decorator breaks `help()`, logging, and debugging.
- **`@lru_cache(maxsize=N)`** — LRU eviction when cache fills. Arguments must be hashable. `cache_info()` shows hits/misses/size. `cache_clear()` resets it. Use when input space is large and memory should be bounded.
- **`@cache`** — unbounded, no eviction, slightly faster than `lru_cache(maxsize=None)`. Use when input space is small or fixed.
- **`@cached_property`** — computed once on first access, stored in `__dict__`. Subsequent accesses bypass the descriptor entirely. Incompatible with `__slots__`. Invalidate by `del instance.__dict__["attr"]`.
- **`partial(func, *args, **kwargs)`** — freeze arguments from the left (positional) or by name (keyword). Creates a new callable. Introspectable: `.func`, `.args`, `.keywords`. Prefer over `lambda` when binding args to existing functions.
- **`@singledispatch`** — dispatch on the type of the first argument. Base function = fallback. `@func.register(type)` adds implementations. `func.dispatch(type)` shows which implementation would run. Use `@singledispatchmethod` for instance methods.
- **`@total_ordering`** — define `__eq__` + one of `__lt__`/`__le__`/`__gt__`/`__ge__`, get the other three generated. Always define `__hash__` separately if needed (total_ordering doesn't touch it).
- **`reduce(func, seq, initial)`** — left fold. Always provide `initial` when sequence might be empty (prevents `TypeError`). Prefer `sum()`, `max()`, `min()` or `itertools.accumulate()` for common cases — `reduce` with lambda is often less readable.