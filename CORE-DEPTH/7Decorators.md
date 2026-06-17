Let's go. Full topic, nothing skipped.

---

## 1.7 Decorators

---

## Decorator Mechanics — What Actually Happens

A decorator is a function that takes a function and returns a function. Nothing more.

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("before")
        result = func(*args, **kwargs)
        print("after")
        return result
    return wrapper

def greet(name):
    print(f"Hello, {name}!")

# Manual decoration
greet = my_decorator(greet)
greet("Mohamed")
# before
# Hello, Mohamed!
# after
```

**`@` syntax is just shorthand for the same thing:**

```python
@my_decorator
def greet(name):
    print(f"Hello, {name}!")

# Exactly equivalent to:
# greet = my_decorator(greet)
```

**The `@` line executes at function definition time — not at call time:**

```python
def my_decorator(func):
    print(f"decorating {func.__name__}")   # runs at definition time
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def greet():     # <-- decorator runs HERE, when Python reads this
    pass
# decorating greet   ← printed immediately

greet()          # wrapper runs here — no print of "decorating"
```

**`*args, **kwargs` in the wrapper — forward everything:**

```python
# Without *args/**kwargs — only works for one specific signature
def decorator(func):
    def wrapper(name):          # only works for functions that take 'name'
        return func(name)
    return wrapper

# With *args/**kwargs — works for any function
def decorator(func):
    def wrapper(*args, **kwargs):   # accepts anything
        return func(*args, **kwargs)  # forwards everything unchanged
    return wrapper
```

---

## `functools.wraps` — Why It's Required

Without `functools.wraps`, your decorator replaces the function's identity.

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def add(a, b):
    """Returns sum of a and b."""
    return a + b

print(add.__name__)    # wrapper  ← WRONG — should be 'add'
print(add.__doc__)     # None     ← WRONG — docstring is gone
print(add)             # <function my_decorator.<locals>.wrapper at 0x...>
```

**This breaks:**
- `help(add)` — shows wrapper's docstring (None), not add's
- Logging — logs "wrapper" not the actual function name
- Debugging — tracebacks show "wrapper" everywhere
- `inspect.signature(add)` — shows wrapper's signature

**Fix — `functools.wraps`:**

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)                    # copies metadata from func to wrapper
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def add(a, b):
    """Returns sum of a and b."""
    return a + b

print(add.__name__)     # add      ← correct
print(add.__doc__)      # Returns sum of a and b.  ← correct
print(add.__wrapped__)  # <function add at 0x...>  ← original function accessible
```

**What `@wraps(func)` copies:**

```python
# These attributes are copied from func to wrapper:
# __name__      — function name
# __qualname__  — qualified name (includes class if method)
# __doc__       — docstring
# __dict__      — attribute dictionary
# __module__    — module where defined
# __annotations__ — type hints
# __wrapped__   — reference to the original function (added by wraps)
```

**`__wrapped__` — unwrapping decorators:**

```python
from functools import wraps

def decorator_a(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

def decorator_b(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@decorator_a
@decorator_b
def my_func():
    pass

print(my_func.__wrapped__)              # inner wrapper (decorator_b's)
print(my_func.__wrapped__.__wrapped__)  # original my_func

import inspect
print(inspect.unwrap(my_func))          # original — unwraps all layers
```

---

## Stacking Decorators — Application Order

Decorators apply **bottom-up** — the one closest to the function applies first.

```python
def decorator_a(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("A: before")
        result = func(*args, **kwargs)
        print("A: after")
        return result
    return wrapper

def decorator_b(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("B: before")
        result = func(*args, **kwargs)
        print("B: after")
        return result
    return wrapper

def decorator_c(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("C: before")
        result = func(*args, **kwargs)
        print("C: after")
        return result
    return wrapper

@decorator_a
@decorator_b
@decorator_c
def greet():
    print("Hello!")

greet()
```

**Output:**
```
A: before
B: before
C: before
Hello!
C: after
B: after
A: after
```

**Mental model — translation:**

```python
@decorator_a
@decorator_b
@decorator_c
def greet():
    pass

# Step 1 — decorator_c applied first (closest to function)
greet = decorator_c(greet)

# Step 2 — decorator_b applied to result of step 1
greet = decorator_b(greet)

# Step 3 — decorator_a applied to result of step 2
greet = decorator_a(greet)

# Final: greet = decorator_a(decorator_b(decorator_c(original_greet)))
```

**Order matters — same decorators, different results:**

```python
from functools import wraps

def bold(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return f"<b>{func(*args, **kwargs)}</b>"
    return wrapper

def italic(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return f"<i>{func(*args, **kwargs)}</i>"
    return wrapper

@bold
@italic
def text():
    return "hello"

print(text())    # <b><i>hello</i></b>  — italic applied first, then bold wraps it

@italic
@bold
def text2():
    return "hello"

print(text2())   # <i><b>hello</b></i>  — bold applied first, then italic wraps it
```

---

## Class-Based Decorators

When a decorator needs to maintain state between calls, or when you want a more structured implementation.

```python
from functools import wraps

class CallCounter:
    """Counts how many times a function has been called."""

    def __init__(self, func):
        wraps(func)(self)           # apply wraps to self instead of a wrapper
        self.func  = func
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"{self.func.__name__} called {self.count} time(s)")
        return self.func(*args, **kwargs)

@CallCounter
def add(a, b):
    return a + b

print(add(1, 2))    # add called 1 time(s) → 3
print(add(3, 4))    # add called 2 time(s) → 7
print(add(5, 6))    # add called 3 time(s) → 11
print(add.count)    # 3  — state on the decorator instance
```

**Class-based decorator with `__init__` and `__call__`:**

```python
from functools import wraps

class Retry:
    def __init__(self, times=3):
        self.times = times

    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            last_error = None
            for attempt in range(1, self.times + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    last_error = e
                    print(f"attempt {attempt}/{self.times} failed: {e}")
            raise last_error
        return wrapper

@Retry(times=3)
def fetch_data():
    import random
    if random.random() < 0.7:
        raise ConnectionError("network error")
    return "data"
```

**When to use class-based vs function-based decorators:**

```python
# Use function-based when:
# - Simple, stateless decoration
# - No configuration needed
# - The common case

# Use class-based when:
# - Need persistent state across calls (call counts, caches)
# - Complex logic that benefits from methods
# - Need to expose attributes on the decorator itself (decorator.count)
```

---

## Parametrized Decorators — The Triple-Nested Pattern

A decorator that accepts arguments needs an extra layer of nesting.

```python
# Without parameters — two layers
def decorator(func):        # layer 1: receives func
    def wrapper(*a, **kw):  # layer 2: the actual wrapper
        return func(*a, **kw)
    return wrapper

# With parameters — three layers
def decorator(param):       # layer 1: receives parameters
    def actual_decorator(func):  # layer 2: receives func
        def wrapper(*a, **kw):   # layer 3: the actual wrapper
            return func(*a, **kw)
        return wrapper
    return actual_decorator
```

**Real example — repeat decorator:**

```python
from functools import wraps

def repeat(times):
    """Call the decorated function `times` times."""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            result = None
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello, {name}!")

greet("Mohamed")
# Hello, Mohamed!
# Hello, Mohamed!
# Hello, Mohamed!

# What happens:
# repeat(3)        → returns decorator
# decorator(greet) → returns wrapper
# greet = wrapper
```

**Making a decorator work WITH and WITHOUT arguments:**

```python
from functools import wraps

def log(func=None, *, level="INFO"):
    """Works as @log or @log(level="DEBUG")"""

    def decorator(f):
        @wraps(f)
        def wrapper(*args, **kwargs):
            print(f"[{level}] calling {f.__name__}")
            return f(*args, **kwargs)
        return wrapper

    if func is not None:
        # Called as @log — func is the decorated function
        return decorator(func)
    else:
        # Called as @log(...) — return the decorator
        return decorator

# Both work:
@log
def func1():
    pass

@log(level="DEBUG")
def func2():
    pass

func1()    # [INFO] calling func1
func2()    # [DEBUG] calling func2
```

---

## `functools.lru_cache` and `functools.cache`

### `lru_cache` — Least Recently Used Cache

Caches the return values of a function based on its arguments. Same arguments → cached result, no recomputation.

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(50))    # 12586269025 — instant, even though naively O(2^50)

# Without cache — this would take forever:
# fibonacci(50) calls fibonacci(49) and fibonacci(48)
# fibonacci(49) calls fibonacci(48) and fibonacci(47)
# fibonacci(48) is computed multiple times — exponential blowup
# With cache — fibonacci(48) computed once, result reused
```

**`cache_info()` — inspect cache performance:**

```python
print(fibonacci.cache_info())
# CacheInfo(hits=48, misses=51, maxsize=128, currsize=51)
# hits=48    — 48 times result was found in cache
# misses=51  — 51 times it had to actually compute
# maxsize=128 — maximum cache size
# currsize=51 — currently cached entries
```

**`cache_clear()` — reset the cache:**

```python
fibonacci.cache_clear()
print(fibonacci.cache_info())
# CacheInfo(hits=0, misses=0, maxsize=128, currsize=0)
```

**`maxsize` — LRU eviction:**

```python
@lru_cache(maxsize=3)   # keep only 3 most recently used results
def square(n):
    print(f"computing {n}²")
    return n * n

square(1)    # computing 1²  — cache: {1:1}
square(2)    # computing 2²  — cache: {1:1, 2:4}
square(3)    # computing 3²  — cache: {1:1, 2:4, 3:9}
square(1)    # cache hit     — cache: {2:4, 3:9, 1:1}  (1 moved to recent)
square(4)    # computing 4²  — cache full, evict LRU (2) → cache: {3:9, 1:1, 4:16}
square(2)    # computing 2²  — 2 was evicted, recompute
```

**`maxsize=None` — unlimited cache (same as `functools.cache`):**

```python
@lru_cache(maxsize=None)
def fib(n):
    if n < 2: return n
    return fib(n-1) + fib(n-2)
```

### `functools.cache` — Simpler Unlimited Cache (Python 3.9+)

```python
from functools import cache

@cache
def fib(n):
    if n < 2: return n
    return fib(n-1) + fib(n-2)

# Equivalent to @lru_cache(maxsize=None) but slightly faster
# because it skips the LRU bookkeeping
```

**Arguments must be hashable:**

```python
@cache
def process(data):
    return sum(data)

process((1, 2, 3))    # OK — tuple is hashable
process([1, 2, 3])    # TypeError: unhashable type: 'list'

# Workaround for list arguments:
@cache
def process(data):
    return sum(data)

my_list = [1, 2, 3]
process(tuple(my_list))    # convert to tuple first
```

---

## `functools.cached_property` — Lazy Instance Attribute

Computes an attribute once on first access and caches it on the instance. Unlike `@property`, it only runs once.

```python
from functools import cached_property
import math

class Circle:
    def __init__(self, radius):
        self.radius = radius

    @cached_property
    def area(self):
        print("computing area...")   # only prints once
        return math.pi * self.radius ** 2

    @cached_property
    def circumference(self):
        print("computing circumference...")
        return 2 * math.pi * self.radius

c = Circle(5)

print(c.area)           # computing area... → 78.539...
print(c.area)           # 78.539...  — no recomputation, cached
print(c.circumference)  # computing circumference... → 31.415...
print(c.circumference)  # 31.415...  — cached

# How it works — stores result in instance __dict__
print(c.__dict__)       # {'radius': 5, 'area': 78.53..., 'circumference': 31.41...}
# On second access, __dict__ lookup happens BEFORE the descriptor → returns cached value
```

**`@property` vs `@cached_property`:**

```python
class Example:
    @property
    def computed(self):
        print("computing...")
        return 42              # runs EVERY time you access it

    @cached_property
    def cached_computed(self):
        print("computing...")
        return 42              # runs ONCE, then cached in __dict__

e = Example()
e.computed           # computing...
e.computed           # computing...  again
e.cached_computed    # computing...
e.cached_computed    # (nothing) — from cache
```

**`cached_property` requires a writable `__dict__` — doesn't work with `__slots__`:**

```python
class Bad:
    __slots__ = ["radius"]

    @cached_property
    def area(self):
        return 3.14 * self.radius ** 2

b = Bad()
b.radius = 5
b.area    # TypeError: cannot store computed value — __slots__ has no __dict__
```

---

## `functools.singledispatch` — Type-Based Overloading

Register different implementations of a function for different argument types.

```python
from functools import singledispatch

@singledispatch
def process(value):
    """Default — called when no specific type matches."""
    raise TypeError(f"Unsupported type: {type(value).__name__}")

@process.register(int)
def _(value):
    return f"integer: {value * 2}"

@process.register(str)
def _(value):
    return f"string: {value.upper()}"

@process.register(list)
def _(value):
    return f"list with {len(value)} items"

@process.register(float)
@process.register(complex)
def _(value):
    return f"number: {abs(value)}"   # same handler for float and complex

print(process(42))           # integer: 84
print(process("hello"))      # string: HELLO
print(process([1, 2, 3]))    # list with 3 items
print(process(3.14))         # number: 3.14
print(process(3+4j))         # number: 5.0
print(process({1: 2}))       # TypeError: Unsupported type: dict
```

**Checking which implementation would be called:**

```python
print(process.dispatch(int))     # <function _ at 0x...>
print(process.dispatch(str))     # <function _ at 0x...>
print(process.dispatch(dict))    # <function process at 0x...>  — falls back to base
```

**`singledispatch` on methods — use `singledispatchmethod`:**

```python
from functools import singledispatchmethod

class Converter:
    @singledispatchmethod
    def convert(self, value):
        raise TypeError(f"Cannot convert {type(value)}")

    @convert.register(str)
    def _(self, value):
        return int(value)

    @convert.register(float)
    def _(self, value):
        return int(value)

c = Converter()
print(c.convert("42"))     # 42
print(c.convert(3.14))     # 3
```

---

## Real-World Patterns

### Timing / Benchmarking Decorator

```python
from functools import wraps
import time

def timer(func=None, *, unit="s"):
    """Measure and print execution time."""
    def decorator(f):
        @wraps(f)
        def wrapper(*args, **kwargs):
            start  = time.perf_counter()
            result = f(*args, **kwargs)
            elapsed = time.perf_counter() - start

            if unit == "ms":
                print(f"{f.__name__}: {elapsed * 1000:.2f}ms")
            else:
                print(f"{f.__name__}: {elapsed:.4f}s")
            return result
        return wrapper

    if func is not None:
        return decorator(func)
    return decorator

@timer
def slow_function():
    time.sleep(0.1)
    return "done"

@timer(unit="ms")
def fast_function():
    return sum(range(100_000))

slow_function()       # slow_function: 0.1002s
fast_function()       # fast_function: 3.21ms
```

---

### Retry with Exponential Backoff

```python
from functools import wraps
import time
import logging

logger = logging.getLogger(__name__)

def retry(
    times=3,
    exceptions=(Exception,),
    delay=1.0,
    backoff=2.0,
    max_delay=60.0,
):
    """
    Retry a function on exception with exponential backoff.

    times:      max number of attempts
    exceptions: tuple of exceptions to catch
    delay:      initial wait in seconds
    backoff:    multiplier applied to delay on each retry
    max_delay:  cap on delay between retries
    """
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            current_delay = delay
            last_exception = None

            for attempt in range(1, times + 1):
                try:
                    return func(*args, **kwargs)
                except exceptions as e:
                    last_exception = e
                    if attempt == times:
                        logger.error(
                            f"{func.__name__} failed after {times} attempts: {e}"
                        )
                        raise

                    wait = min(current_delay, max_delay)
                    logger.warning(
                        f"{func.__name__} attempt {attempt}/{times} failed: {e}. "
                        f"Retrying in {wait:.1f}s..."
                    )
                    time.sleep(wait)
                    current_delay *= backoff

            raise last_exception

        return wrapper
    return decorator

@retry(times=4, exceptions=(ConnectionError, TimeoutError), delay=0.5, backoff=2.0)
def fetch_data(url):
    import random
    if random.random() < 0.7:
        raise ConnectionError(f"failed to connect to {url}")
    return f"data from {url}"

try:
    result = fetch_data("https://api.example.com")
    print(result)
except ConnectionError:
    print("all retries failed")
```

---

### Access Control / Auth Guard

```python
from functools import wraps

def require_auth(func=None, *, roles=None):
    """
    Decorator that checks if the current user is authenticated
    and optionally has the required role.
    """
    def decorator(f):
        @wraps(f)
        def wrapper(*args, **kwargs):
            # In a real app, get current_user from request context, JWT, session, etc.
            current_user = kwargs.get("current_user") or (args[0] if args else None)

            if current_user is None:
                raise PermissionError("authentication required")

            if not getattr(current_user, "is_authenticated", False):
                raise PermissionError("user is not authenticated")

            if roles:
                user_role = getattr(current_user, "role", None)
                if user_role not in roles:
                    raise PermissionError(
                        f"role '{user_role}' not in required roles: {roles}"
                    )

            return f(*args, **kwargs)
        return wrapper

    if func is not None:
        return decorator(func)
    return decorator

# Usage
class User:
    def __init__(self, name, role, is_authenticated=True):
        self.name             = name
        self.role             = role
        self.is_authenticated = is_authenticated

@require_auth
def view_profile(current_user):
    return f"Profile of {current_user.name}"

@require_auth(roles=["admin", "superuser"])
def delete_user(current_user, user_id):
    return f"Deleted user {user_id}"

admin = User("Mohamed", "admin")
guest = User("Guest",   "viewer")
anon  = User("Anon",    None, is_authenticated=False)

print(view_profile(current_user=admin))   # Profile of Mohamed
print(delete_user(current_user=admin, user_id=42))   # Deleted user 42

try:
    delete_user(current_user=guest, user_id=42)
except PermissionError as e:
    print(e)   # role 'viewer' not in required roles: ['admin', 'superuser']

try:
    view_profile(current_user=anon)
except PermissionError as e:
    print(e)   # user is not authenticated
```

---

### Input Validation Decorator

```python
from functools import wraps
import inspect

def validate(**validators):
    """
    Validate function arguments using provided validator functions.

    Usage:
        @validate(
            age=lambda x: x >= 0,
            name=lambda x: isinstance(x, str) and len(x) > 0,
        )
        def create_user(name, age): ...
    """
    def decorator(func):
        sig = inspect.signature(func)

        @wraps(func)
        def wrapper(*args, **kwargs):
            # Bind arguments to parameter names
            bound = sig.bind(*args, **kwargs)
            bound.apply_defaults()

            for param_name, validator in validators.items():
                if param_name in bound.arguments:
                    value = bound.arguments[param_name]
                    if not validator(value):
                        raise ValueError(
                            f"invalid value for '{param_name}': {value!r}"
                        )

            return func(*args, **kwargs)
        return wrapper
    return decorator

def type_check(**expected_types):
    """Validate argument types."""
    def decorator(func):
        sig = inspect.signature(func)

        @wraps(func)
        def wrapper(*args, **kwargs):
            bound = sig.bind(*args, **kwargs)
            bound.apply_defaults()

            for param_name, expected in expected_types.items():
                if param_name in bound.arguments:
                    value = bound.arguments[param_name]
                    if not isinstance(value, expected):
                        raise TypeError(
                            f"'{param_name}' must be {expected.__name__}, "
                            f"got {type(value).__name__}"
                        )
            return func(*args, **kwargs)
        return wrapper
    return decorator

@type_check(name=str, age=int, score=float)
@validate(
    name=lambda x: len(x) > 0,
    age=lambda x: 0 <= x <= 150,
    score=lambda x: 0.0 <= x <= 100.0,
)
def create_student(name, age, score):
    return {"name": name, "age": age, "score": score}

print(create_student("Mohamed", 25, 88.5))
# {'name': 'Mohamed', 'age': 25, 'score': 88.5}

try:
    create_student("", 25, 88.5)
except ValueError as e:
    print(e)    # invalid value for 'name': ''

try:
    create_student("Mohamed", -1, 88.5)
except ValueError as e:
    print(e)    # invalid value for 'age': -1

try:
    create_student("Mohamed", "25", 88.5)
except TypeError as e:
    print(e)    # 'age' must be int, got str
```

---

## Putting It All Together

```python
from functools import wraps, lru_cache, cached_property
import time
import logging

logger = logging.getLogger(__name__)

# ── 1. Composable decorators ──────────────────────────────────────────────────

def log_calls(func=None, *, level="INFO"):
    """Log every call with args and return value."""
    def decorator(f):
        @wraps(f)
        def wrapper(*args, **kwargs):
            logger.log(
                getattr(logging, level),
                f"calling {f.__name__}({args}, {kwargs})"
            )
            result = f(*args, **kwargs)
            logger.log(
                getattr(logging, level),
                f"{f.__name__} returned {result!r}"
            )
            return result
        return wrapper
    if func is not None:
        return decorator(func)
    return decorator

def rate_limit(calls_per_second):
    """Prevent function from being called faster than the rate limit."""
    min_interval = 1.0 / calls_per_second
    last_called  = [0.0]    # list so closure can mutate it

    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            now     = time.perf_counter()
            elapsed = now - last_called[0]
            if elapsed < min_interval:
                time.sleep(min_interval - elapsed)
            last_called[0] = time.perf_counter()
            return func(*args, **kwargs)
        return wrapper
    return decorator

# ── 2. A class using multiple decorators and cached_property ─────────────────

class DataProcessor:
    def __init__(self, data: list[int]):
        self._data = data

    @cached_property
    def sorted_data(self):
        """Computed once, cached forever on the instance."""
        print("sorting...")
        return sorted(self._data)

    @cached_property
    def statistics(self):
        d = self.sorted_data
        n = len(d)
        return {
            "count":  n,
            "min":    d[0],
            "max":    d[-1],
            "mean":   sum(d) / n,
            "median": d[n // 2],
        }

    @lru_cache(maxsize=64)
    def percentile(self, p: float) -> float:
        """p-th percentile — cached by p value."""
        if not 0 <= p <= 100:
            raise ValueError(f"percentile must be 0-100, got {p}")
        d = self.sorted_data
        idx = int(len(d) * p / 100)
        return d[min(idx, len(d) - 1)]

    @log_calls(level="DEBUG")
    @rate_limit(calls_per_second=2)
    def fetch_external(self, key: str) -> str:
        """Rate-limited, logged external call."""
        return f"result for {key}"

# ── 3. Demo ───────────────────────────────────────────────────────────────────

import random
data = [random.randint(1, 100) for _ in range(20)]
proc = DataProcessor(data)

print(proc.statistics)         # sorting...  → dict
print(proc.statistics)         # (no recomputation)
print(proc.percentile(50))     # median
print(proc.percentile(90))     # 90th percentile
print(proc.percentile(50))     # from cache
print(proc.percentile.cache_info())
```

---

## Key Takeaways

- A decorator is `decorated = decorator(original)`. `@decorator` is syntactic sugar for exactly that.
- Always use `@wraps(func)` inside your decorator's wrapper. Without it you lose `__name__`, `__doc__`, `__annotations__`, and break `help()`, logging, and debugging.
- `__wrapped__` — added by `@wraps`, lets `inspect.unwrap()` reach the original function through layers.
- Stacking order: **bottom-up at definition, outside-in at call time**. The decorator closest to `def` wraps first.
- Class-based decorators: `__init__` receives `func`, `__call__` is the wrapper. Use when you need persistent state or exposable attributes.
- Parametrized decorator: three layers — outermost receives config, middle receives `func`, innermost is the wrapper.
- `@lru_cache(maxsize=N)` — caches last N results. `@cache` — unlimited, slightly faster. Both require hashable arguments. `cache_info()` / `cache_clear()` for introspection.
- `@cached_property` — computed once on first access, stored in `__dict__`. Doesn't recompute on repeat access. Incompatible with `__slots__`.
- `@singledispatch` — register type-specific implementations. Falls back to base function for unregistered types. Use `singledispatchmethod` for methods.
- Real patterns: `@timer` wraps the call in `perf_counter`. `@retry` loops with `try/except` and `time.sleep`. `@require_auth` checks identity before calling. `@validate` uses `inspect.signature.bind()` to map args to names.