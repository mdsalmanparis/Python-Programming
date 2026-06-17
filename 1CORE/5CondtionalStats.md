Let's go. Full topic, nothing skipped.

---

## 0.6 Conditional Statements

---

## `if` Statement

The most fundamental control flow tool. Run a block of code only when a condition is `True`.

```python
age = 20

if age >= 18:
    print("You are an adult")
    print("You can vote")

# If condition is False — nothing runs, no error
age = 15

if age >= 18:
    print("You are an adult")   # skipped entirely

print("Program continues")      # this always runs
```

**The colon and indentation are mandatory:**

```python
# SyntaxError — missing colon
if age >= 18
    print("adult")

# IndentationError — missing indent
if age >= 18:
print("adult")

# Correct
if age >= 18:
    print("adult")
```

Python uses indentation to define blocks — not curly braces like most languages. Standard is 4 spaces. Mixing tabs and spaces causes errors.

---

## `else` Clause

Runs when the `if` condition is `False`. Only one `else` per `if`, always at the end.

```python
age = 15

if age >= 18:
    print("adult")
else:
    print("minor")
# minor

# Both branches are mutually exclusive — exactly one will always run
score = 75

if score >= 50:
    print("passed")
else:
    print("failed")
# passed
```

---

## `elif` Clause

Multiple conditions checked in order. The first one that is `True` wins — the rest are skipped entirely.

```python
score = 85

if score >= 90:
    print("A")
elif score >= 80:
    print("B")
elif score >= 70:
    print("C")
elif score >= 60:
    print("D")
else:
    print("F")
# B  — score >= 80 is the first True condition
```

**"First True wins" — order matters:**

```python
score = 95

# WRONG order — gives wrong result
if score >= 60:
    print("D")       # prints D even though score is 95
elif score >= 70:
    print("C")       # never reached
elif score >= 80:
    print("B")       # never reached
elif score >= 90:
    print("A")       # never reached

# CORRECT order — most restrictive first
if score >= 90:
    print("A")       # prints A
elif score >= 80:
    print("B")
elif score >= 70:
    print("C")
elif score >= 60:
    print("D")
```

**`else` is optional:**

```python
role = "admin"

if role == "admin":
    print("full access")
elif role == "editor":
    print("edit access")
elif role == "viewer":
    print("read access")
# no else — if none match, nothing happens
```

**Each condition is only checked if all previous ones were `False`:**

```python
x = 10

if x > 5:
    print("greater than 5")
elif x > 3:          # never evaluated — first branch already matched
    print("greater than 3")
elif x > 1:          # never evaluated
    print("greater than 1")
# greater than 5
```

---

## Truthiness and Falsiness

Every Python value has a boolean truth value. You don't always need `== True` or `== False`.

### Falsy values — these ALL evaluate to `False` in a condition

```python
if False:    print("False")      # skipped
if 0:        print("0")          # skipped
if 0.0:      print("0.0")        # skipped
if "":       print("empty str")  # skipped
if []:       print("empty list") # skipped
if {}:       print("empty dict") # skipped
if set():    print("empty set")  # skipped
if None:     print("None")       # skipped

print("none of those ran")
```

### Truthy values — everything else

```python
if 1:           print("1")           # runs
if -1:          print("-1")          # runs — any non-zero number
if 0.001:       print("0.001")       # runs
if "hello":     print("hello")       # runs
if "0":         print('"0"')         # runs — non-empty string
if "False":     print('"False"')     # runs — non-empty string
if [0]:         print("[0]")         # runs — list with one item
if [False]:     print("[False]")     # runs — list with one item
if {"a": 1}:    print("dict")        # runs
if (0,):        print("tuple")       # runs — single element tuple
```

### Using truthiness directly — the Pythonic way

```python
name = "Mohamed"
items = [1, 2, 3]
user = None

# Un-Pythonic — explicit comparison
if name != "":
    print("has name")

if len(items) > 0:
    print("has items")

if user is not None:
    print("user exists")

# Pythonic — use truthiness directly
if name:
    print("has name")

if items:
    print("has items")

# Exception — for None specifically, use 'is not None', not just truthiness
# because 0, "", [] also evaluate to False but are not None
if user is not None:      # correct — distinguishes None from 0 or ""
    print("user exists")
```

**The `None` check distinction matters in real code:**

```python
def get_count():
    return 0     # valid result — zero items found

count = get_count()

if count:             # WRONG — 0 is falsy, this skips even when valid
    process(count)

if count is not None: # CORRECT — distinguishes "no result" from "zero results"
    process(count)
```

---

## Conditions with Comparison and Logical Operators

```python
age = 25
income = 50000
has_id = True

# and — both must be True
if age >= 18 and has_id:
    print("can enter")

# or — at least one must be True
if age < 13 or age > 65:
    print("special pricing")

# not — flip the condition
if not has_id:
    print("ID required")

# Combining
if age >= 18 and income >= 30000 and has_id:
    print("approved")

# Chained comparison — very Pythonic
if 18 <= age <= 65:
    print("working age")

# Checking multiple values with 'in'
status = "pending"
if status in ("pending", "processing", "on_hold"):
    print("not complete")

day = "Saturday"
if day in {"Saturday", "Sunday"}:    # set for O(1) lookup
    print("weekend")
```

---

## Ternary — One-Line `if`

Assign a value based on a condition in a single line.

**Syntax: `value_if_true if condition else value_if_false`**

```python
age = 20
label = "adult" if age >= 18 else "minor"
print(label)    # adult

score = 45
result = "pass" if score >= 50 else "fail"
print(result)   # fail

# In a print statement
print("even" if 10 % 2 == 0 else "odd")    # even

# In an f-string
name = "Mohamed"
print(f"Hello {'sir' if name else 'stranger'}")   # Hello sir
```

**Ternary can be chained — but use sparingly:**

```python
score = 85
grade = "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "F"
print(grade)    # B
```

This is technically valid but becomes hard to read quickly. If you have more than two branches, use `if/elif/else`.

**When ternary is appropriate — simple, readable assignments:**

```python
# Good use — short and clear
is_even = True if n % 2 == 0 else False
# Even better — just use the expression directly
is_even = n % 2 == 0

# Bad use — too complex for one line
result = process_a(x) if condition_a(x) and some_flag else process_b(x) if condition_b else default_value
# Refactor this into if/elif/else — readability matters
```

---

## Nested `if` — Use Sparingly

An `if` inside another `if`.

```python
age = 25
has_ticket = True
is_vip = False

if age >= 18:
    if has_ticket:
        if is_vip:
            print("VIP lounge access")
        else:
            print("general admission")
    else:
        print("no ticket")
else:
    print("underage")
```

This works but three levels deep is already hard to follow. The deeper you nest, the harder it is to read and test.

### Refactor with `and` — flatten conditions

```python
# Instead of nested ifs
if age >= 18:
    if has_ticket:
        print("general admission")

# Combine with and
if age >= 18 and has_ticket:
    print("general admission")
```

### Refactor with early return — the guard clause pattern

This is the professional way to handle nested conditions. Check failure cases first and return early.

```python
# Deep nesting — hard to follow
def process_order(user, order):
    if user is not None:
        if user.is_active:
            if order is not None:
                if order.total > 0:
                    print("processing order")
                else:
                    print("empty order")
            else:
                print("no order")
        else:
            print("inactive user")
    else:
        print("no user")

# Guard clause pattern — flat and clear
def process_order(user, order):
    if user is None:
        print("no user")
        return

    if not user.is_active:
        print("inactive user")
        return

    if order is None:
        print("no order")
        return

    if order.total <= 0:
        print("empty order")
        return

    print("processing order")    # happy path at the end, no nesting
```

The guard clause pattern is used everywhere in production Python. Check preconditions first, return/raise early, keep the main logic at the outermost level.

---

## `match / case` — Pattern Matching (Python 3.10+)

A cleaner replacement for long `elif` chains, especially when matching against specific values or structures.

### Basic value matching

```python
status_code = 404

match status_code:
    case 200:
        print("OK")
    case 201:
        print("Created")
    case 400:
        print("Bad Request")
    case 404:
        print("Not Found")
    case 500:
        print("Server Error")
    case _:             # default — like else
        print("Unknown status")
# Not Found
```

`case _:` is the wildcard — matches anything not matched above.

### Matching strings

```python
command = "quit"

match command:
    case "quit" | "exit" | "q":    # | means OR inside case
        print("Goodbye")
    case "help" | "h":
        print("Showing help")
    case "start":
        print("Starting")
    case _:
        print(f"Unknown command: {command}")
# Goodbye
```

### Matching with a guard condition

```python
score = 85

match score:
    case s if s >= 90:
        print("A")
    case s if s >= 80:
        print("B")
    case s if s >= 70:
        print("C")
    case _:
        print("F")
# B
```

The `s` captures the matched value, `if s >= 90` is the guard.

### Matching sequences (lists and tuples)

```python
point = (0, 5)

match point:
    case (0, 0):
        print("origin")
    case (0, y):
        print(f"on Y axis at y={y}")    # y captures the value 5
    case (x, 0):
        print(f"on X axis at x={x}")
    case (x, y):
        print(f"at ({x}, {y})")
    case _:
        print("not a point")
# on Y axis at y=5
```

### Matching dictionaries

```python
event = {"type": "click", "button": "left", "x": 100, "y": 200}

match event:
    case {"type": "click", "button": "left"}:
        print("left click")
    case {"type": "click", "button": "right"}:
        print("right click")
    case {"type": "keypress", "key": key}:
        print(f"key pressed: {key}")
    case _:
        print("unknown event")
# left click
```

Dictionary patterns only need to match the specified keys — extra keys in the dict are ignored.

### Matching with class patterns

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(0, 5)

match p:
    case Point(x=0, y=0):
        print("origin")
    case Point(x=0, y=y):
        print(f"Y axis, y={y}")
    case Point(x=x, y=0):
        print(f"X axis, x={x}")
    case Point(x=x, y=y):
        print(f"Point at ({x}, {y})")
# Y axis, y=5
```

### `if/elif` vs `match/case` — when to use which

```python
# if/elif — use for:
# - range checks (score >= 90)
# - complex boolean conditions
# - Python < 3.10 compatibility

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
else:
    grade = "F"

# match/case — use for:
# - matching exact values or structures
# - HTTP status codes, command strings, event types
# - destructuring tuples, lists, dicts

match status:
    case 200: ...
    case 404: ...
    case 500: ...
```

---

## Putting It All Together

```python
def classify_user(user):
    # Guard clauses first
    if user is None:
        return "no user"

    if not isinstance(user, dict):
        return "invalid input"

    age    = user.get("age", 0)
    role   = user.get("role", "guest")
    active = user.get("active", False)

    if not active:
        return "inactive account"

    # Ternary for simple label
    age_label = "adult" if age >= 18 else "minor"

    # match/case for role
    match role:
        case "admin":
            access = "full"
        case "editor":
            access = "read-write"
        case "viewer":
            access = "read-only"
        case _:
            access = "none"

    return f"{age_label} | role: {role} | access: {access}"


print(classify_user(None))
# no user

print(classify_user({"age": 25, "role": "editor", "active": True}))
# adult | role: editor | access: read-write

print(classify_user({"age": 16, "role": "viewer", "active": True}))
# minor | role: viewer | access: read-only

print(classify_user({"age": 30, "role": "guest", "active": False}))
# inactive account
```

---

## Key Takeaways

- `elif` chains check top to bottom — put the most restrictive condition first.
- Falsy values: `False`, `0`, `0.0`, `""`, `[]`, `{}`, `set()`, `None` — memorise this list.
- Don't use `if x is not None` and `if x` interchangeably — `0`, `""`, `[]` are falsy but not `None`.
- Ternary is for simple assignments. More than two branches → use `if/elif/else`.
- Deep nesting is a code smell — flatten with `and`, or use guard clauses with early `return`.
- `match/case` is for matching exact values and structures — cleaner than long `elif` chains.
- `case _:` is the wildcard default in `match` — like `else`.