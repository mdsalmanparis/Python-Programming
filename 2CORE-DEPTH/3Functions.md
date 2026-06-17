Let's go. Full topic, nothing skipped.

---

## 1.3 Functions — `*args`, `**kwargs`, Closures, First-Class

---

## Positional, Keyword, Default Arguments — Ordering Rules

Already covered in 0.8 at the basics level. Here we go deeper on the rules and edge cases.

```python
def create_user(name, age, city="Chennai", active=True):
    return {"name": name, "age": age, "city": city, "active": active}

# Positional — order matters, must match parameter order
create_user("Mohamed", 25)
create_user("Mohamed", 25, "Mumbai")
create_user("Mohamed", 25, "Mumbai", False)

# Keyword — order doesn't matter
create_user(age=25, name="Mohamed")
create_user(name="Mohamed", age=25, active=False)

# Mix — positional must come before keyword
create_user("Mohamed", 25, active=False)           # OK
create_user("Mohamed", age=25, city="Mumbai")      # OK
create_user(name="Mohamed", 25)                    # SyntaxError
```

**Default argument evaluation — happens once at definition time:**

```python
import datetime

# BAD — datetime.now() evaluated ONCE when function is defined
def log(msg, timestamp=datetime.datetime.now()):
    print(f"{timestamp}: {msg}")

log("first")     # 2024-11-15 10:00:00
# wait 5 seconds
log("second")    # 2024-11-15 10:00:00  ← same timestamp, not current

# GOOD — evaluate inside
def log(msg, timestamp=None):
    if timestamp is None:
        timestamp = datetime.datetime.now()
    print(f"{timestamp}: {msg}")
```

---

## `/` and `*` in Signatures — Positional-Only and Keyword-Only

Python 3.8+ allows you to enforce exactly how arguments must be passed.

### `/` — Positional-Only (everything before `/`)

```python
def greet(name, greeting, /):
    return f"{greeting}, {name}"

greet("Mohamed", "Hello")           # OK — positional
greet("Mohamed", greeting="Hello")  # TypeError: greeting may not be passed as keyword
greet(name="Mohamed", greeting="Hello")  # TypeError
```

**Why positional-only exists:**

```python
# 1. Matches mathematical functions — pow(x, y) not pow(base=x, exp=y)
def pow(x, y, /):
    return x ** y

# 2. Parameter names are implementation details you might rename later
# If users pass by keyword, renaming the param breaks their code
# positional-only prevents that coupling

# 3. Built-in functions use this — len(), print(), etc.
# len(obj=my_list)  ← TypeError in CPython
```

### `*` — Keyword-Only (everything after `*`)

```python
def create_user(name, *, role="viewer", active=True):
    #                  ^ bare * means: everything after must be keyword
    return {"name": name, "role": role, "active": active}

create_user("Mohamed")                           # OK
create_user("Mohamed", role="admin")             # OK
create_user("Mohamed", "admin")                  # TypeError: too many positional args
create_user("Mohamed", "admin", True)            # TypeError
```

**Real-world use — preventing ambiguous calls:**

```python
# Without keyword-only — easy to pass args in wrong order silently
def send_email(to, subject, body, html, priority):
    pass

send_email("a@b.com", "Hi", "Hello", True, 1)   # which is html and which is priority?

# With keyword-only — forces clarity
def send_email(to, subject, body, *, html=False, priority=1):
    pass

send_email("a@b.com", "Hi", "Hello", html=True, priority=2)   # clear
send_email("a@b.com", "Hi", "Hello", True, 2)                 # TypeError — good
```

### Combining `/` and `*`

```python
def complex_func(pos_only, /, normal, *, kw_only):
    print(pos_only, normal, kw_only)

# pos_only — positional only
# normal   — either positional or keyword
# kw_only  — keyword only

complex_func(1, 2, kw_only=3)          # OK
complex_func(1, normal=2, kw_only=3)   # OK
complex_func(pos_only=1, normal=2, kw_only=3)  # TypeError — pos_only can't be keyword
complex_func(1, 2, 3)                  # TypeError — kw_only must be keyword
```

---

## `*args` — Variable Positional Arguments

Collects all extra positional arguments into a **tuple**.

```python
def add(*args):
    print(args)          # it's a tuple
    print(type(args))    # <class 'tuple'>
    return sum(args)

add(1, 2)           # args = (1, 2)
add(1, 2, 3, 4, 5)  # args = (1, 2, 3, 4, 5)
add()               # args = ()  — empty tuple, no error

print(add(1, 2, 3))    # 6
```

**`*args` after regular parameters:**

```python
def greet(greeting, *names):
    for name in names:
        print(f"{greeting}, {name}!")

greet("Hello", "Mohamed", "Priya", "Arjun")
# Hello, Mohamed!
# Hello, Priya!
# Hello, Arjun!

greet("Hi")    # names = () — no extra args, loop doesn't run
```

### Unpacking with `*` at Call Site

The `*` in a function call unpacks an iterable into positional arguments.

```python
def add(a, b, c):
    return a + b + c

nums = [1, 2, 3]
print(add(*nums))       # 6  — same as add(1, 2, 3)

t = (10, 20, 30)
print(add(*t))          # 60

print(add(*range(3)))   # 3  — 0+1+2
```

**Unpacking in other contexts:**

```python
first, *rest = [1, 2, 3, 4, 5]    # already know this

# Merge lists
a = [1, 2, 3]
b = [4, 5, 6]
merged = [*a, *b]
print(merged)    # [1, 2, 3, 4, 5, 6]

merged = [*a, 0, *b]
print(merged)    # [1, 2, 3, 0, 4, 5, 6]
```

---

## `**kwargs` — Variable Keyword Arguments

Collects all extra keyword arguments into a **dict**.

```python
def show(**kwargs):
    print(kwargs)          # it's a dict
    print(type(kwargs))    # <class 'dict'>

show(name="Mohamed", age=25, city="Chennai")
# {'name': 'Mohamed', 'age': 25, 'city': 'Chennai'}

show()    # {}  — empty dict, no error
```

**`**kwargs` after regular and `*args`:**

```python
def everything(a, b, *args, **kwargs):
    print(f"a={a}, b={b}")
    print(f"args={args}")
    print(f"kwargs={kwargs}")

everything(1, 2, 3, 4, x=10, y=20)
# a=1, b=2
# args=(3, 4)
# kwargs={'x': 10, 'y': 20}
```

**Full parameter order — mandatory:**

```python
def f(positional, default=None, *args, keyword_only, **kwargs):
    pass

# Order must be:
# 1. positional parameters
# 2. parameters with defaults
# 3. *args
# 4. keyword-only parameters (after *)
# 5. **kwargs
```

### Unpacking `**` at Call Site

Unpacks a dict into keyword arguments.

```python
def create_user(name, age, city):
    return f"{name}, {age}, {city}"

data = {"name": "Mohamed", "age": 25, "city": "Chennai"}
print(create_user(**data))    # Mohamed, 25, Chennai
# same as: create_user(name="Mohamed", age=25, city="Chennai")

# Merge dicts
a = {"x": 1, "y": 2}
b = {"z": 3, "w": 4}
merged = {**a, **b}
print(merged)    # {'x': 1, 'y': 2, 'z': 3, 'w': 4}

# Override a value
config = {"host": "localhost", "port": 5432, "db": "mydb"}
test_config = {**config, "port": 9999}   # overrides port
print(test_config)    # {'host': 'localhost', 'port': 9999, 'db': 'mydb'}
```

### Forwarding Pattern — Passing `*args` and `**kwargs` Through

The most important real-world use — passing arguments through without knowing what they are.

```python
def log_call(func, *args, **kwargs):
    print(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
    result = func(*args, **kwargs)
    print(f"Result: {result}")
    return result

def add(a, b):
    return a + b

log_call(add, 1, 2)
# Calling add with args=(1, 2), kwargs={}
# Result: 3

log_call(add, a=1, b=2)
# Calling add with args=(), kwargs={'a': 1, 'b': 2}
# Result: 3
```

**Decorator pattern — the key use of forwarding:**

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):       # accept any args
        print("before")
        result = func(*args, **kwargs)  # forward all args to original
        print("after")
        return result
    return wrapper

@my_decorator
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Mohamed")            # works
greet("Mohamed", "Hi")      # works
greet("Mohamed", greeting="Namaste")  # works
# wrapper accepts all of these and forwards them unchanged
```

---

## First-Class Functions

In Python, functions are objects. They can be:
- Assigned to variables
- Passed as arguments
- Returned from functions
- Stored in data structures

```python
def greet(name):
    return f"Hello, {name}"

# Assign to a variable
say_hello = greet
print(say_hello("Mohamed"))    # Hello, Mohamed
print(type(greet))             # <class 'function'>

# Store in a list
def double(n): return n * 2
def triple(n): return n * 3
def square(n): return n ** 2

operations = [double, triple, square]   # list of functions
for op in operations:
    print(op(5))    # 10, 15, 25

# Store in a dict — dispatch table pattern
def add(a, b): return a + b
def sub(a, b): return a - b
def mul(a, b): return a * b

dispatch = {
    "+": add,
    "-": sub,
    "*": mul,
}

print(dispatch["+"](10, 3))    # 13
print(dispatch["*"](10, 3))    # 30

# Use in real code — replace if/elif chains
operator = input("operator: ")
result = dispatch.get(operator, lambda a, b: None)(10, 3)
```

**Passing functions as arguments — callbacks:**

```python
def apply(func, values):
    return [func(v) for v in values]

print(apply(str.upper, ["hello", "world"]))    # ['HELLO', 'WORLD']
print(apply(abs, [-1, -2, 3, -4]))            # [1, 2, 3, 4]
print(apply(lambda x: x**2, range(5)))        # [0, 1, 4, 9, 16]
```

---

## Higher-Order Functions — Functions That Return Functions

A function that takes a function as argument OR returns a function.

```python
# Returns a function
def make_multiplier(n):
    def multiplier(x):
        return x * n
    return multiplier

double = make_multiplier(2)
triple = make_multiplier(3)

print(double(5))    # 10
print(triple(5))    # 15
print(double(triple(4)))    # 24

# The returned function remembers n — that's a closure (next section)
```

**Higher-order functions in the standard library:**

```python
from functools import reduce

nums = [1, 2, 3, 4, 5]

# map — applies function to each element
print(list(map(lambda x: x**2, nums)))    # [1, 4, 9, 16, 25]

# filter — keeps elements where function returns True
print(list(filter(lambda x: x % 2 == 0, nums)))    # [2, 4]

# sorted with key — key is a function
print(sorted(["banana","fig","apple"], key=len))    # ['fig', 'apple', 'banana']

# reduce — folds list into single value
print(reduce(lambda acc, x: acc + x, nums))    # 15  (sum)
print(reduce(lambda acc, x: acc * x, nums))    # 120 (product)
```

---

## Closures — Captured Variables and `nonlocal`

A closure is a function that **remembers variables from its enclosing scope** even after that scope has finished executing.

```python
def make_counter():
    count = 0               # this variable lives in make_counter's scope

    def increment():
        nonlocal count      # tell Python: use the enclosing scope's count
        count += 1
        return count

    return increment        # return the inner function

counter = make_counter()    # make_counter() finishes, but count survives

print(counter())    # 1
print(counter())    # 2
print(counter())    # 3

# Each call to make_counter() creates a NEW independent closure
counter2 = make_counter()
print(counter2())   # 1  — independent count
print(counter())    # 4  — counter1 continues from where it left off
```

**What makes it a closure — the `__closure__` attribute:**

```python
def make_adder(n):
    def adder(x):
        return x + n    # n is captured from enclosing scope
    return adder

add5 = make_adder(5)
add10 = make_adder(10)

print(add5(3))     # 8
print(add10(3))    # 13

# Inspect the closure
print(add5.__closure__)           # (<cell at 0x...>,)
print(add5.__closure__[0].cell_contents)   # 5  — the captured value
```

**Reading without `nonlocal` — fine. Modifying without `nonlocal` — error:**

```python
def outer():
    x = 10

    def inner():
        print(x)    # reading x — fine, no nonlocal needed
    inner()

def outer2():
    x = 10

    def inner():
        x += 1      # UnboundLocalError — Python sees assignment, treats x as local
                    # but x has no local value yet

    inner()

def outer3():
    x = 10

    def inner():
        nonlocal x  # "use the x from the enclosing scope"
        x += 1
        print(x)

    inner()    # 11
    inner()    # 12
    print(x)   # 12
```

**Closures with multiple enclosing variables:**

```python
def make_validator(min_val, max_val):
    def validate(value):
        if not (min_val <= value <= max_val):
            raise ValueError(f"{value} must be between {min_val} and {max_val}")
        return value
    return validate

validate_score = make_validator(0, 100)
validate_age   = make_validator(0, 150)

print(validate_score(88))    # 88
validate_score(150)          # ValueError: 150 must be between 0 and 100
print(validate_age(25))      # 25
```

---

## Late Binding Gotcha — Loop Variable Capture

One of Python's most famous closures bug. The closure captures the **variable**, not its value at the time of creation.

```python
# You want: [0, 1, 4, 9, 16]
funcs = []
for i in range(5):
    funcs.append(lambda x: x * i)   # captures the variable i, not its current value

print(funcs[0](1))    # 4  — expected 0
print(funcs[1](1))    # 4  — expected 1
print(funcs[2](1))    # 4  — expected 4 ✓ (coincidence)
print(funcs[3](1))    # 4  — expected 9
print(funcs[4](1))    # 4  — expected 16
```

**Why — the loop variable `i` is ONE variable that changes. All lambdas share that same variable. By the time any lambda is called, the loop is done and `i = 4`.**

```python
# Verify — i is 4 after the loop
for i in range(5):
    pass
print(i)    # 4
```

**Fix 1 — default argument captures value at definition time:**

```python
funcs = []
for i in range(5):
    funcs.append(lambda x, i=i: x * i)   # i=i captures current value as default

print(funcs[0](1))    # 0  ✓
print(funcs[1](1))    # 1  ✓
print(funcs[2](1))    # 4  ✓
print(funcs[3](1))    # 9  ✓
print(funcs[4](1))    # 16 ✓
```

**Fix 2 — factory function creates a new scope each iteration:**

```python
def make_func(i):
    return lambda x: x * i    # i is now local to make_func, not the loop

funcs = [make_func(i) for i in range(5)]

print(funcs[0](1))    # 0  ✓
print(funcs[1](1))    # 1  ✓
```

**Fix 3 — `functools.partial` (covered next section):**

```python
from functools import partial

def multiply(x, i):
    return x * i

funcs = [partial(multiply, i=i) for i in range(5)]
```

**Late binding applies to ALL closures, not just lambdas:**

```python
def make_funcs():
    funcs = []
    for i in range(3):
        def f():
            return i    # same late binding issue
        funcs.append(f)
    return funcs

results = [f() for f in make_funcs()]
print(results)    # [2, 2, 2]  — all see i=2, the final value
```

---

## `lambda` — Anonymous Functions

A lambda is a function defined in a single expression. No `def`, no `return`, no statements.

```python
# def version
def square(n):
    return n ** 2

# lambda version — identical behaviour
square = lambda n: n ** 2

print(square(5))    # 25
print(type(square)) # <class 'function'>
```

**Syntax: `lambda parameters: expression`**

```python
# One parameter
double = lambda x: x * 2

# Multiple parameters
add = lambda a, b: a + b
print(add(3, 4))    # 7

# Default parameter
greet = lambda name, greeting="Hello": f"{greeting}, {name}"
print(greet("Mohamed"))          # Hello, Mohamed
print(greet("Mohamed", "Hi"))    # Hi, Mohamed

# No parameters
get_pi = lambda: 3.14159
print(get_pi())    # 3.14159
```

**Where lambda shines — inline use as an argument:**

```python
words = ["banana", "fig", "cherry", "apple"]

# Cleaner than defining a named function just for this
print(sorted(words, key=lambda w: w[-1]))    # sort by last character
print(sorted(words, key=lambda w: len(w)))   # sort by length

students = [{"name": "Mohamed", "score": 88}, {"name": "Priya", "score": 92}]
top = max(students, key=lambda s: s["score"])
print(top)    # {'name': 'Priya', 'score': 92}
```

**Lambda limitations — one expression only:**

```python
# CANNOT use statements
f = lambda x: if x > 0: return x    # SyntaxError
f = lambda x: x = x + 1             # SyntaxError — assignment is a statement
f = lambda x: print(x); return x    # SyntaxError — multiple statements

# CANNOT use type annotations
f = lambda x: int: x * 2            # SyntaxError

# CAN use ternary (it's an expression)
f = lambda x: x if x > 0 else -x    # absolute value — valid
```

**When NOT to use lambda:**

```python
# BAD — assigning lambda to a name (just use def)
square = lambda n: n ** 2     # pointless — no advantage over def

# GOOD — use def
def square(n):
    return n ** 2

# BAD — complex logic in lambda, hard to read
process = lambda x: x**2 if x > 0 else (-x)**2 if x < -10 else 0

# GOOD — use def with a name that explains intent
def process_value(x):
    if x > 0:
        return x ** 2
    elif x < -10:
        return (-x) ** 2
    return 0
```

**Rule:** use lambda when it's used inline and immediately. If you're assigning it to a name or it has any complexity, use `def`.

---

## `functools.partial()` — Freezing Arguments

Creates a new callable with some arguments pre-filled.

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

# Create specialised versions with one argument frozen
square = partial(power, exponent=2)
cube   = partial(power, exponent=3)

print(square(5))    # 25
print(cube(3))      # 27
print(square(10))   # 100
```

**Freezing positional arguments:**

```python
from functools import partial

def add(a, b, c):
    return a + b + c

add5 = partial(add, 5)       # freezes a=5
print(add5(1, 2))            # 8  — 5 + 1 + 2

add5_10 = partial(add, 5, 10)    # freezes a=5, b=10
print(add5_10(3))                # 18  — 5 + 10 + 3
```

**Real-world use — configuring functions for specific contexts:**

```python
from functools import partial
import os

# Generic file opener
open_utf8 = partial(open, encoding="utf-8", mode="r")
open_binary = partial(open, mode="rb")

with open_utf8("file.txt") as f:    # no need to repeat encoding
    content = f.read()

# Logging with fixed level
import logging
logger = logging.getLogger(__name__)
log_error = partial(logger.log, logging.ERROR)
log_info  = partial(logger.log, logging.INFO)

log_error("something went wrong")    # cleaner than logger.log(logging.ERROR, "...")
```

**`partial` vs lambda — both work, partial is more explicit:**

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

# lambda
square_lambda = lambda x: power(x, 2)

# partial
square_partial = partial(power, exponent=2)

# Both produce the same result
print(square_lambda(5))    # 25
print(square_partial(5))   # 25

# partial preserves the original function's name and docstring
print(square_partial.func)      # <function power>
print(square_partial.keywords)  # {'exponent': 2}
print(square_partial.args)      # ()
```

---

## Recursion — Base Case, Stack Depth, Iterative Refactor

A function that calls itself. Every recursive solution has two parts:
1. **Base case** — when to stop
2. **Recursive case** — how to reduce the problem

```python
def factorial(n):
    if n == 0:          # base case — stop here
        return 1
    return n * factorial(n - 1)   # recursive case

print(factorial(5))    # 120  — 5 * 4 * 3 * 2 * 1 * 1
print(factorial(0))    # 1
```

**The call stack for `factorial(4)`:**

```
factorial(4)
  → 4 * factorial(3)
        → 3 * factorial(2)
              → 2 * factorial(1)
                    → 1 * factorial(0)
                          → 1         (base case)
                    → 1 * 1 = 1
              → 2 * 1 = 2
        → 3 * 2 = 6
  → 4 * 6 = 24
```

**More recursion examples:**

```python
# Fibonacci
def fib(n):
    if n <= 1:          # base cases: fib(0)=0, fib(1)=1
        return n
    return fib(n-1) + fib(n-2)

print(fib(10))    # 55

# Sum of a list
def list_sum(lst):
    if not lst:           # base case — empty list
        return 0
    return lst[0] + list_sum(lst[1:])

print(list_sum([1, 2, 3, 4, 5]))    # 15

# Flatten nested list
def flatten(lst):
    result = []
    for item in lst:
        if isinstance(item, list):
            result.extend(flatten(item))   # recurse into nested list
        else:
            result.append(item)
    return result

print(flatten([1, [2, [3, 4]], [5, 6]]))    # [1, 2, 3, 4, 5, 6]
```

### `sys.setrecursionlimit()` — Stack Depth

Python's default recursion limit is 1000 calls deep.

```python
import sys

print(sys.getrecursionlimit())    # 1000

def count_down(n):
    if n == 0:
        return
    count_down(n - 1)

count_down(999)     # fine
count_down(5000)    # RecursionError: maximum recursion depth exceeded

# Increase limit — use carefully
sys.setrecursionlimit(10000)
count_down(5000)    # now works
```

**Why the limit exists — the call stack is finite memory. Deep recursion = stack overflow.**

### Converting Recursion to Iteration

Always possible. Often more memory-efficient and faster.

```python
# Recursive factorial — O(n) stack frames
def factorial_recursive(n):
    if n == 0:
        return 1
    return n * factorial_recursive(n - 1)

# Iterative factorial — O(1) stack frames
def factorial_iterative(n):
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

# Recursive fibonacci — O(2^n) time, very slow
def fib_recursive(n):
    if n <= 1:
        return n
    return fib_recursive(n-1) + fib_recursive(n-2)

# Iterative fibonacci — O(n) time
def fib_iterative(n):
    if n <= 1:
        return n
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b

print(fib_recursive(35))    # slow
print(fib_iterative(35))    # instant
```

**Memoization — cache recursive results:**

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    if n <= 1:
        return n
    return fib(n-1) + fib(n-2)

print(fib(100))    # instant — results cached
print(fib.cache_info())    # CacheInfo(hits=98, misses=101, ...)
```

---

## Function Annotations — Runtime vs Static Checking

Type hints added to function signatures. They document intent but Python does NOT enforce them at runtime.

```python
def add(a: int, b: int) -> int:
    return a + b

# Works — Python ignores the hints at runtime
print(add(1, 2))          # 3
print(add("hello", " world"))   # "hello world" — no error despite int hints
```

**Accessing annotations at runtime:**

```python
def greet(name: str, times: int = 1) -> str:
    return (f"Hello, {name}! " * times).strip()

print(greet.__annotations__)
# {'name': <class 'str'>, 'times': <class 'int'>, 'return': <class 'str'>}
```

**Common annotation types:**

```python
from typing import Optional, Union, List, Dict, Tuple, Any, Callable

def process(
    name: str,
    age: int,
    score: float,
    active: bool,
    tags: list[str],           # Python 3.9+ — lowercase built-ins
    metadata: dict[str, Any],
    nickname: Optional[str],   # str or None
    value: Union[int, float],  # int or float
    value2: int | float,       # Python 3.10+ — same as Union[int, float]
) -> tuple[str, int]:
    return name, age
```

**Callable annotations:**

```python
from typing import Callable

def apply(func: Callable[[int], int], value: int) -> int:
    return func(value)

# Callable[[arg_types], return_type]
def transform(
    data: list[int],
    key: Callable[[int], bool],       # function: int -> bool
    mapper: Callable[[int], str],     # function: int -> str
) -> list[str]:
    return [mapper(x) for x in data if key(x)]
```

**`-> None` for functions that return nothing:**

```python
def log(message: str) -> None:
    print(message)
```

**Runtime vs static — the key distinction:**

```python
# Python DOES NOT enforce annotations at runtime
def add(a: int, b: int) -> int:
    return a + b

add("hello", "world")    # "helloworld" — no error

# Static type checkers (mypy, pyright) DO check
# mypy script.py would report:
# error: Argument 1 to "add" has incompatible type "str"; expected "int"

# Use annotations for:
# 1. Documentation — readers know what types are expected
# 2. IDE autocomplete — IDEs use hints to suggest methods
# 3. mypy / pyright — static analysis catches bugs before runtime
# 4. Pydantic, FastAPI — they DO read annotations at runtime for validation
```

---

## Putting It All Together

```python
from functools import partial, lru_cache, reduce
from typing import Callable, TypeVar

T = TypeVar("T")

# 1. Factory function returning closure
def make_pipeline(*funcs: Callable) -> Callable:
    """Return a function that applies funcs in sequence."""
    def pipeline(value):
        return reduce(lambda v, f: f(v), funcs, value)
    return pipeline

# 2. Partial to pre-configure functions
def multiply(x: int, factor: int) -> int:
    return x * factor

double = partial(multiply, factor=2)
triple = partial(multiply, factor=3)

# 3. Higher-order function with *args/**kwargs forwarding
def retry(func: Callable, times: int = 3, *args, **kwargs):
    for attempt in range(1, times + 1):
        try:
            return func(*args, **kwargs)
        except Exception as e:
            print(f"Attempt {attempt} failed: {e}")
            if attempt == times:
                raise

# 4. Closure with nonlocal for stateful transforms
def make_accumulating_pipeline(*funcs: Callable) -> Callable:
    history: list = []

    def pipeline(value):
        nonlocal history
        result = value
        for f in funcs:
            result = f(result)
            history.append(result)
        return result

    def get_history():
        return history[:]

    pipeline.history = get_history
    return pipeline

# 5. Memoized recursion
@lru_cache(maxsize=256)
def fib(n: int) -> int:
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)

# 6. Late binding fix in comprehension
multipliers = [partial(multiply, factor=i) for i in range(1, 4)]

# Demo
process = make_pipeline(
    double,
    triple,
    lambda x: x + 1,
)
print(process(5))     # 5 * 2 = 10 * 3 = 30 + 1 = 31

acc = make_accumulating_pipeline(double, triple)
print(acc(5))         # 30
print(acc.history())  # [10, 30]

print([m(5) for m in multipliers])   # [5, 10, 15]

print(fib(50))    # 12586269025 — instant due to cache
```

---

## Key Takeaways

- Parameter order: positional → defaults → `*args` → keyword-only → `**kwargs`.
- `/` makes everything before it positional-only. `*` makes everything after it keyword-only.
- `*args` → tuple of extra positional args. `**kwargs` → dict of extra keyword args.
- `*` and `**` at the **call site** unpack iterables and dicts into arguments.
- Functions are first-class objects — assign, store, pass, return them like any value.
- Closures capture **variables** not values — they see the current state of the variable.
- `nonlocal` is required to **modify** an enclosing scope variable. Reading works without it.
- Late binding — lambdas in loops capture the loop variable, not its value. Fix: default arg `i=i` or factory function.
- `lambda` is for inline, single-expression use only. If you need a name or complexity, use `def`.
- `functools.partial()` freezes arguments — creates a specialised version of a function.
- Every recursive function must have a base case. Python's default stack limit is 1000. Use `@lru_cache` for expensive recursion.
- Annotations document intent — Python does NOT enforce them at runtime. Mypy and FastAPI do.