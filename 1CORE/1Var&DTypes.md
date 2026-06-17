riables and basic data types · MD
# 0.2 Variables & Basic Data Types
 
---
 
## Variables — Naming Rules
 
A variable is just a name you give to a value so you can use it later.
 
```python
age = 25
name = "Mohamed"
is_hired = True
```
 
**Rules Python enforces — break these and you get a `SyntaxError` or `NameError`:**
 
```python
# VALID names
my_name = "Ali"
age2 = 30
_private = "hidden"
__dunder__ = "special"
NAME = "constant"        # all caps is a convention for constants
 
# INVALID names
2fast = "nope"           # SyntaxError: can't start with a digit
my-name = "nope"         # SyntaxError: hyphen is subtraction, not allowed
class = "nope"           # SyntaxError: 'class' is a reserved keyword
```
 
**Case-sensitive — these are three different variables:**
 
```python
age = 25
Age = 30
AGE = 35
 
print(age)   # 25
print(Age)   # 30
print(AGE)   # 35
```
 
**`snake_case` convention — Python's standard style:**
 
```python
# Python style (PEP 8) — use this always
first_name = "Mohamed"
total_price = 499.99
is_logged_in = False
 
# Not wrong, but not Pythonic
firstName = "Mohamed"    # camelCase — JavaScript style
FirstName = "Mohamed"    # PascalCase — reserved for class names
```
 
---
 
## Assignment
 
**Basic assignment — `=` does not mean "equals", it means "bind this name to this value":**
 
```python
x = 10          # x now points to the integer object 10
x = 20          # x now points to 20; the old 10 is not deleted yet
x = "hello"     # x can point to a completely different type — Python allows this
```
 
**Multiple assignment on one line:**
 
```python
a, b = 1, 2
print(a)   # 1
print(b)   # 2
 
# Right side is fully evaluated first, then assigned left to right
x, y, z = 10, 20, 30
```
 
**Swap without a temp variable — a Python classic:**
 
```python
a = 5
b = 10
 
a, b = b, a     # right side (b, a) = (10, 5) evaluated first, then unpacked
 
print(a)   # 10
print(b)   # 5
```
 
In most languages you need a temp variable for this. Python's tuple unpacking makes it a one-liner.
 
**Same value to multiple names:**
 
```python
x = y = z = 0
print(x, y, z)   # 0 0 0
```
 
---
 
## `int` — Integers
 
Whole numbers, positive or negative, no decimal point.
 
```python
age = 25
temperature = -7
zero = 0
big = 1_000_000      # underscores allowed for readability — same as 1000000
 
print(type(age))     # <class 'int'>
```
 
**No size limit in Python** — unlike C or Java where `int` overflows, Python integers grow as large as memory allows:
 
```python
huge = 999999999999999999999999999999999999999999
print(huge + 1)   # works perfectly
```
 
**Integer arithmetic:**
 
```python
print(10 + 3)    # 13   addition
print(10 - 3)    # 7    subtraction
print(10 * 3)    # 30   multiplication
print(10 / 3)    # 3.333...  division — always returns float
print(10 // 3)   # 3    floor division — result is int, rounded down
print(10 % 3)    # 1    modulo — remainder
print(2 ** 10)   # 1024 exponentiation
```
 
**Important: `/` always gives a `float`, even if the result is whole:**
 
```python
print(10 / 2)      # 5.0   not 5
print(type(10/2))  # <class 'float'>
print(10 // 2)     # 5     this gives int
```
 
---
 
## `float` — Floating Point Numbers
 
Numbers with a decimal point.
 
```python
price = 99.99
pi = 3.14159
negative = -0.5
scientific = 1.5e3     # 1500.0  (scientific notation)
small = 2.5e-4         # 0.00025
 
print(type(price))     # <class 'float'>
```
 
**Floating point imprecision — this is not a Python bug, it's how all computers store decimals in binary:**
 
```python
print(0.1 + 0.2)           # 0.30000000000000004
print(0.1 + 0.2 == 0.3)    # False  ← NEVER compare floats with ==
 
# The right way to compare floats:
result = 0.1 + 0.2
print(abs(result - 0.3) < 1e-9)    # True  — check if "close enough"
 
# Or use round() when displaying:
print(round(0.1 + 0.2, 2))    # 0.3
```
 
This will bite you in billing code, test assertions, and anywhere you compare floats. Remember it now.
 
---
 
## `str` — Strings
 
Text data. Anything between quotes.
 
```python
name = "Mohamed"
city = 'Chennai'
both_fine = "it's fine"      # double quotes let you use apostrophe inside
also_fine = 'say "hello"'    # single quotes let you use double quotes inside
 
print(type(name))   # <class 'str'>
```
 
**Single vs double quotes — identical in Python, choose one and be consistent:**
 
```python
a = "hello"
b = 'hello'
print(a == b)   # True — same string, different quote style
```
 
**f-strings — the modern way to embed values in strings:**
 
```python
name = "Mohamed"
score = 88
print(f"Name: {name}, Score: {score}")   # Name: Mohamed, Score: 88
```
 
Strings have a lot more to them (indexing, slicing, methods) — all of that is covered in full in topic 0.3.
 
---
 
## `bool` — Booleans
 
Only two values: `True` and `False`. Capital T and F — lowercase is a `NameError`.
 
```python
is_active = True
has_error = False
 
print(type(is_active))   # <class 'bool'>
 
# true    # NameError — Python is case-sensitive
# false   # NameError
```
 
**`bool` is a subclass of `int` — this is not obvious but important:**
 
```python
print(True == 1)     # True
print(False == 0)    # True
print(True + True)   # 2
print(True * 5)      # 5
print(False + 10)    # 10
 
# This is why you can use booleans in arithmetic — they ARE integers under the hood
print(isinstance(True, int))    # True
```
 
**Comparison operators always return a bool:**
 
```python
print(10 > 5)     # True
print(10 == 10)   # True
print(10 != 9)    # True
print(3 >= 5)     # False
```
 
---
 
## `type()` — Checking the Type of Any Value
 
```python
print(type(42))          # <class 'int'>
print(type(3.14))        # <class 'float'>
print(type("hello"))     # <class 'str'>
print(type(True))        # <class 'bool'>
print(type(None))        # <class 'NoneType'>
print(type([1, 2, 3]))   # <class 'list'>
```
 
**`type()` vs `isinstance()` — know both:**
 
```python
x = True
 
print(type(x) == bool)       # True
print(isinstance(x, bool))   # True
print(isinstance(x, int))    # True  ← because bool IS a subclass of int
 
# isinstance() is preferred in real code — it respects inheritance
```
 
You'll use `isinstance()` far more than `type()` once you're in Phase 1+.
 
---
 
## `None` — The Absence of a Value
 
`None` is not zero. It's not an empty string. It's not `False`. It is its own thing — the explicit representation of "no value here."
 
```python
result = None
print(result)          # None
print(type(result))    # <class 'NoneType'>
```
 
**Where you'll see `None` constantly:**
 
```python
# 1. Functions that don't return anything
def greet(name):
    print(f"Hello {name}")   # no return statement
 
x = greet("Mohamed")
print(x)    # None   ← the function returned None
 
# 2. Uninitialized variables — placeholder pattern
user = None
# ... later in code ...
user = fetch_user_from_db()
 
# 3. Optional function parameters
def connect(host, port=None):
    if port is None:
        port = 5432   # default
```
 
**Checking for `None` — always use `is`, not `==`:**
 
```python
x = None
 
# Correct
if x is None:
    print("no value")
 
if x is not None:
    print("has value")
 
# Works but not the Pythonic way
if x == None:
    print("no value")
```
 
The reason: `is` checks identity (same object in memory), `==` checks equality. `None` is a singleton — there's only one `None` object in Python. `is None` is the convention the entire Python community uses.
 
---
 
## Type Conversion
 
You'll often need to convert between types, especially when reading user input (which always comes in as a string).
 
```python
# String to number
age_str = "25"
age_int = int(age_str)      # 25
price_str = "99.99"
price_float = float(price_str)  # 99.99
 
# Number to string
x = 42
x_str = str(x)    # "42"
 
# To bool — everything has a truth value
print(bool(0))       # False
print(bool(1))       # True
print(bool(""))      # False
print(bool("hi"))    # True
print(bool(None))    # False
print(bool([]))      # False
print(bool([0]))     # True   — a list with one item (even if it's 0) is truthy
 
# Conversion can fail — always be aware
int("hello")           # ValueError: invalid literal for int()
int("3.14")            # ValueError: use float() first then int()
int(float("3.14"))     # 3  — this works
```
 
---
 
## Putting It Together — A Quick Exercise
 
Read this and predict every output before running it:
 
```python
x = 10
y = 3
 
print(x / y)        # ?
print(x // y)       # ?
print(x % y)        # ?
print(x ** y)       # ?
 
a, b = "Python", 2025
print(type(a))      # ?
print(type(b))      # ?
print(a * 2)        # ?   ← think about this one
 
flag = True
print(flag + 1)     # ?
print(type(flag))   # ?
print(isinstance(flag, int))  # ?
 
nothing = None
print(nothing == 0)       # ?
print(nothing == False)   # ?
print(nothing is None)    # ?
```
 
**Answers:**
 
```
3.3333333333333335
3
1
1000
<class 'str'>
<class 'int'>
PythonPython
2
<class 'bool'>
True
False
False
True
```
 
---
 
## Key Takeaways
 
- `=` is assignment, not equality. `==` is equality.
- `/` always returns `float`. `//` returns `int`.
- Never compare floats with `==`.
- `bool` is literally a subclass of `int`. `True` is `1`, `False` is `0`.
- `None` is its own type. Check for it with `is None`, not `== None`.
- User input is always a `str` — convert it before doing math.

Let's go. Full topic, nothing skipped.

---

## Type Conversion

Python does not automatically convert between types — you do it explicitly. This is called **casting**.

### `int()`

Converts to a whole number.

```python
int("42")        # 42   — string to int
int("0")         # 0
int(3.99)        # 3    — float to int, truncates (NOT rounds)
int(3.01)        # 3    — same, always chops the decimal, never rounds up
int(True)        # 1    — bool to int
int(False)       # 0

# These FAIL — ValueError
int("hello")     # ValueError: invalid literal for int() with base 10: 'hello'
int("3.14")      # ValueError: int() can't parse a float string directly
int(None)        # TypeError: int() argument must be a string or number, not 'NoneType'

# Workaround for float strings
int(float("3.14"))   # 3 — convert to float first, then truncate to int
```

**Critical point — `int()` truncates, it does NOT round:**
```python
int(3.9)    # 3  — not 4
int(-3.9)   # -3 — not -4, truncates toward zero
round(3.9)  # 4  — use round() if you want rounding
```

---

### `float()`

Converts to a decimal number.

```python
float("3.14")    # 3.14  — string to float
float("10")      # 10.0  — integer string to float
float(42)        # 42.0  — int to float
float(True)      # 1.0
float(False)     # 0.0

# Special values Python accepts
float("inf")     # inf   — positive infinity
float("-inf")    # -inf  — negative infinity
float("nan")     # nan   — Not a Number

# These FAIL
float("hello")   # ValueError
float(None)      # TypeError
```

---

### `str()`

Converts anything to its string representation. Almost never fails.

```python
str(100)         # "100"
str(3.14)        # "3.14"
str(True)        # "True"
str(False)       # "False"
str(None)        # "None"   — the string "None", not the value None
str([1,2,3])     # "[1, 2, 3]"
str({"a": 1})    # "{'a': 1}"
```

**Practical use — user input always comes as string, output always needs string:**
```python
age = 25
print("I am " + str(age) + " years old")   # must convert, + doesn't mix str and int

# Better with f-string (no conversion needed):
print(f"I am {age} years old")             # f-strings handle it automatically
```

---

### `bool()`

Every value in Python has a truth value. `bool()` reveals it.

```python
# These are ALL False (falsy values — memorise this list)
bool(0)        # False
bool(0.0)      # False
bool("")       # False  — empty string
bool([])       # False  — empty list
bool({})       # False  — empty dict
bool(set())    # False  — empty set
bool(None)     # False

# Everything else is True (truthy)
bool(1)        # True
bool(-1)       # True   — any non-zero number
bool(0.001)    # True
bool("0")      # True   — non-empty string, even if it contains "0"
bool("False")  # True   — non-empty string
bool([0])      # True   — list with one item, even if that item is 0
bool([False])  # True   — same reason
```

**The trap people fall into:**
```python
user_input = "False"
print(bool(user_input))   # True  — it's a non-empty string!

# If you want to check the actual string content:
print(user_input == "False")   # True  — correct way
```

---

## Arithmetic Operators

```python
print(10 + 3)    # 13    addition
print(10 - 3)    # 7     subtraction
print(10 * 3)    # 30    multiplication
print(10 / 3)    # 3.333...  division — always float
print(10 // 3)   # 3     floor division — always int (if both operands are int)
print(10 % 3)    # 1     modulo — remainder after floor division
print(2 ** 10)   # 1024  exponentiation
```

### `/` vs `//` — the one that trips everyone

```python
10 / 2     # 5.0   — always float, even when result is whole
10 // 2    # 5     — int result
10 / 3     # 3.3333333333333335
10 // 3    # 3     — floor (round DOWN toward negative infinity)
```

### `//` floors toward negative infinity — not toward zero

```python
7 // 2     # 3    — 3.5 floors down to 3
-7 // 2    # -4   — -3.5 floors down to -4  (not -3!)
7 // -2    # -4   — same logic
-7 // -2   # 3    — two negatives, result is positive, floors down
```

This is why you got Q9 right — you knew it floors toward negative infinity.

### `%` modulo — remainder

```python
10 % 3     # 1    — 10 = 3*3 + 1
10 % 2     # 0    — 10 is divisible by 2
10 % 10    # 0
11 % 10    # 1
-7 % 2     # 1    — in Python, result has same sign as the DIVISOR (2)
7 % -2     # -1   — result has same sign as the DIVISOR (-2)
```

**Real-world uses of `%`:**
```python
# Check if number is even or odd
n = 17
print(n % 2 == 0)   # False — odd
print(n % 2 == 1)   # True  — odd

# Every nth iteration in a loop
for i in range(10):
    if i % 3 == 0:
        print(f"{i} is divisible by 3")
```

### `**` exponentiation

```python
2 ** 10    # 1024
3 ** 3     # 27
4 ** 0.5   # 2.0   — square root (power of 0.5)
8 ** (1/3) # 2.0   — cube root
2 ** -1    # 0.5   — negative exponent = fraction
```

---

## Operator Precedence

Python evaluates expressions in this order (PEMDAS — same as maths):

| Priority | Operator | Name |
|---|---|---|
| 1 (highest) | `**` | Exponentiation |
| 2 | `+x`, `-x` | Unary plus/minus |
| 3 | `*`, `/`, `//`, `%` | Multiplication/division |
| 4 (lowest) | `+`, `-` | Addition/subtraction |

```python
print(2 + 3 * 4)       # 14  — multiplication first: 2 + 12
print((2 + 3) * 4)     # 20  — parentheses first: 5 * 4
print(2 ** 3 + 1)      # 9   — exponent first: 8 + 1
print(2 ** (3 + 1))    # 16  — parentheses first: 2 ** 4
print(10 - 2 * 3)      # 4   — multiply first: 10 - 6
print(10 / 2 + 3)      # 8.0 — division first: 5.0 + 3
print(10 / (2 + 3))    # 2.0 — parentheses first: 10 / 5
```

**Same-priority operators go left to right:**
```python
10 - 3 - 2     # 5   — (10 - 3) - 2, not 10 - (3 - 2)
2 ** 3 ** 2    # 512 — EXCEPTION: ** goes RIGHT to left: 2 ** (3**2) = 2**9
```

`**` is the only operator that is right-associative. Everything else is left-to-right.

**The rule in production code: use parentheses whenever there's any doubt.** Explicit is better than relying on someone knowing precedence rules.

```python
# Ambiguous — don't do this
result = a + b * c ** d - e / f

# Clear — do this
result = a + (b * (c ** d)) - (e / f)
```

---

## Augmented Assignment

Shorthand for updating a variable using its current value.

```python
x = 10

x += 1     # same as x = x + 1  →  x is now 11
x -= 1     # same as x = x - 1  →  x is now 10
x *= 2     # same as x = x * 2  →  x is now 20
x /= 4     # same as x = x / 4  →  x is now 5.0  (note: becomes float)
x //= 2    # same as x = x // 2 →  x is now 2.0  (floor div, but already float)
x **= 3    # same as x = x ** 3 →  x is now 8.0
x %= 3     # same as x = x % 3  →  x is now 2.0
```

**Watch the `/=` trap:**
```python
x = 10
x /= 2
print(x)         # 5.0  — not 5, it's a float now
print(type(x))   # <class 'float'>

# If you need int, use //=
y = 10
y //= 2
print(y)         # 5
print(type(y))   # <class 'int'>
```

**Chaining in a loop — the most common use:**
```python
total = 0
for i in range(1, 6):    # 1, 2, 3, 4, 5
    total += i
print(total)    # 15
```

---

## Comparison Operators

Always return `True` or `False` — nothing else.

```python
print(10 == 10)    # True   — equal to
print(10 != 9)     # True   — not equal to
print(10 > 5)      # True   — greater than
print(10 < 5)      # False  — less than
print(10 >= 10)    # True   — greater than or equal
print(10 <= 9)     # False  — less than or equal
```

### `==` vs `=` — the most common beginner bug

```python
x = 10      # assignment — puts 10 into x
x == 10     # comparison — checks if x equals 10, returns True
```

### Comparing across types

```python
print(1 == 1.0)      # True  — int and float compared by value
print(1 == True)     # True  — True is 1
print(0 == False)    # True  — False is 0
print(0 == None)     # False — None is not zero
print("" == False)   # False — different types, not equal by ==
```

### Chained comparisons — Python allows this, most languages don't

```python
x = 5
print(1 < x < 10)       # True  — pythonic range check
print(1 < x and x < 10) # True  — same thing, more verbose

age = 25
print(18 <= age <= 60)   # True  — between 18 and 60 inclusive
```

---

## Logical Operators

Combine boolean expressions. Three operators: `and`, `or`, `not`.

### `and` — both must be True

```python
print(True and True)    # True
print(True and False)   # False
print(False and True)   # False
print(False and False)  # False
```

### `or` — at least one must be True

```python
print(True or True)     # True
print(True or False)    # True
print(False or True)    # True
print(False or False)   # False
```

### `not` — flips the bool

```python
print(not True)         # False
print(not False)        # True
print(not 0)            # True   — 0 is falsy, not flips it
print(not "")           # True   — empty string is falsy
print(not "hello")      # False  — non-empty string is truthy
```

---

### Short-Circuit Evaluation — important

Python is lazy. It stops evaluating as soon as the result is determined.

**`and` — stops at the first `False`:**
```python
# If the left side is False, right side is never evaluated
x = 0
print(x != 0 and 10 / x > 1)   # False — stops at x != 0, never divides by zero
```

**`or` — stops at the first `True`:**
```python
# If the left side is True, right side is never evaluated
x = 0
print(x == 0 or 10 / x > 1)    # True — stops at x == 0, never divides by zero
```

**`and` and `or` return the actual value, not just True/False:**

This surprises most people. Python doesn't return `True`/`False` — it returns the value that determined the outcome.

```python
# and: returns first falsy value, or last value if all truthy
print(1 and 2)        # 2     — 1 is truthy, so evaluates 2, returns 2
print(0 and 2)        # 0     — 0 is falsy, stops, returns 0
print(1 and 2 and 3)  # 3     — all truthy, returns last
print(1 and 0 and 3)  # 0     — stops at 0, returns 0

# or: returns first truthy value, or last value if all falsy
print(1 or 2)         # 1     — 1 is truthy, stops, returns 1
print(0 or 2)         # 2     — 0 is falsy, evaluates 2, returns 2
print(0 or "" or 5)   # 5     — first truthy value
print(0 or "" or [])  # []    — all falsy, returns last
```

**Practical patterns using short-circuit:**
```python
# Default value pattern
name = ""
display_name = name or "Anonymous"    # if name is empty, use "Anonymous"
print(display_name)   # Anonymous

# Safe access pattern
user = None
username = user and user.name   # if user is None, doesn't crash on .name
print(username)   # None

# Checking before using
numbers = [1, 2, 3]
print(numbers and numbers[0])   # 1 — safe, list is non-empty
```

---

### Combining Everything — Operator Precedence Extended

When arithmetic, comparison, and logical operators are mixed, this is the full order:

| Priority | Operators |
|---|---|
| 1 (highest) | `**` |
| 2 | `*`, `/`, `//`, `%` |
| 3 | `+`, `-` |
| 4 | `==`, `!=`, `<`, `>`, `<=`, `>=` |
| 5 | `not` |
| 6 | `and` |
| 7 (lowest) | `or` |

```python
print(2 + 3 > 4 and 10 % 3 == 1)
# Step 1 arithmetic:  5 > 4 and 1 == 1
# Step 2 comparison:  True and True
# Step 3 logical:     True

print(not 5 > 3 or 2 ** 2 == 4)
# Step 1 exponent:    not 5 > 3 or 4 == 4
# Step 2 comparison:  not True or True
# Step 3 not:         False or True
# Step 4 or:          True
```

Again — when in doubt, use parentheses:
```python
print((not (5 > 3)) or ((2 ** 2) == 4))   # same result, crystal clear
```

---

## Key Takeaways

- `int()` truncates floats — it does NOT round. `int(3.9)` is `3`.
- `bool("False")` is `True` — non-empty strings are always truthy.
- `/` always returns float. `/=` turns your variable into a float.
- `**` is right-associative: `2 ** 3 ** 2` = `2 ** 9`, not `8 ** 2`.
- `and` / `or` return the actual value that decided the result, not just `True`/`False`.
- Short-circuit: `and` stops at first falsy, `or` stops at first truthy — the right side may never run.
- When operators mix, use parentheses — don't rely on memorised precedence.