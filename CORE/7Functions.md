Let's go. Full topic, nothing skipped.

---

## 0.8 Functions — Basics

---

## Defining and Calling a Function

A function is a named, reusable block of code. Define it once, call it anywhere.

```python
# Defining
def greet():
    print("Hello, World!")

# Calling
greet()     # Hello, World!
greet()     # Hello, World!  — call it as many times as you want
```

**The `def` keyword, function name, parentheses, and colon are all required:**

```python
# SyntaxError — missing colon
def greet()
    print("hello")

# SyntaxError — missing parentheses
def greet:
    print("hello")

# IndentationError — body not indented
def greet():
print("hello")

# Correct
def greet():
    print("hello")
```

**Function naming follows the same rules as variables — `snake_case` by convention:**

```python
def calculate_total():   ...   # good
def get_user_name():     ...   # good
def validateInput():     ...   # works but not Pythonic — camelCase
def Calculate_Total():   ...   # works but not Pythonic — PascalCase is for classes
```

**Functions are objects — you can store them in variables:**

```python
def greet():
    print("hello")

say_hi = greet       # no parentheses — not calling it, just referencing it
say_hi()             # hello — calling through the new name
print(type(greet))   # <class 'function'>
```

---

## Parameters vs Arguments

These two words are often used interchangeably but they mean different things.

```python
# 'name' and 'age' are PARAMETERS — placeholders in the definition
def introduce(name, age):
    print(f"I am {name}, {age} years old")

# "Mohamed" and 25 are ARGUMENTS — actual values passed at call time
introduce("Mohamed", 25)
# I am Mohamed, 25 years old
```

**Parameters are local variable names. Arguments are the values bound to them.**

```python
def add(a, b):          # a and b are parameters
    return a + b

result = add(10, 20)    # 10 and 20 are arguments
# Inside the function: a = 10, b = 20
```

---

## `return` Statement

Sends a value back to the caller. Exits the function immediately.

```python
def add(a, b):
    return a + b

result = add(10, 20)
print(result)    # 30
```

**`return` exits immediately — code after it does not run:**

```python
def check(n):
    if n > 0:
        return "positive"    # exits here if n > 0
    if n < 0:
        return "negative"    # exits here if n < 0
    return "zero"            # only reaches here if n == 0

print(check(5))     # positive
print(check(-3))    # negative
print(check(0))     # zero
```

**Function returns `None` if there is no `return` statement:**

```python
def greet(name):
    print(f"Hello {name}")    # no return

result = greet("Mohamed")
print(result)           # None
print(result is None)   # True
```

**`return` with no value also returns `None`:**

```python
def do_something(x):
    if x < 0:
        return          # bare return — exits and returns None
    print(x * 2)

result = do_something(-5)
print(result)    # None  — bare return triggered

do_something(3)  # 6
```

**The return value must be captured to be used:**

```python
def square(n):
    return n * n

square(5)              # return value is thrown away — useless call
print(square(5))       # 25 — print uses the return value
result = square(5)     # 25 — stored for later use
```

---

## Multiple Return Values

`return x, y` packs values into a tuple. Unpack at the call site.

```python
def get_dimensions():
    width = 1920
    height = 1080
    return width, height      # returns (1920, 1080) — a tuple

# Unpack directly
w, h = get_dimensions()
print(w)    # 1920
print(h)    # 1080

# Or capture the tuple
dims = get_dimensions()
print(dims)       # (1920, 1080)
print(dims[0])    # 1920
print(type(dims)) # <class 'tuple'>
```

**Multiple return values with different types:**

```python
def analyze_scores(scores):
    total   = sum(scores)
    average = total / len(scores)
    highest = max(scores)
    lowest  = min(scores)
    return total, average, highest, lowest

scores = [88, 45, 92, 67, 78]
total, avg, high, low = analyze_scores(scores)

print(f"Total:   {total}")    # 370
print(f"Average: {avg}")      # 74.0
print(f"Highest: {high}")     # 92
print(f"Lowest:  {low}")      # 45
```

**Using `_` to discard values you don't need:**

```python
total, _, highest, _ = analyze_scores(scores)
# only keeping total and highest, ignoring average and lowest
```

---

## Default Parameter Values

Provide a fallback value for a parameter — callers can omit it.

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Mohamed")              # Hello, Mohamed!   — uses default
greet("Mohamed", "Hi")        # Hi, Mohamed!      — overrides default
greet("Mohamed", "Namaste")   # Namaste, Mohamed! — overrides default
```

**Defaults must come after non-defaults — otherwise `SyntaxError`:**

```python
# SyntaxError — non-default after default
def greet(greeting="Hello", name):
    print(f"{greeting}, {name}")

# Correct — non-defaults first, defaults after
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}")
```

**Multiple defaults:**

```python
def create_user(name, role="viewer", active=True, score=0):
    print(f"{name} | {role} | active={active} | score={score}")

create_user("Mohamed")
# Mohamed | viewer | active=True | score=0

create_user("Priya", "admin")
# Priya | admin | active=True | score=0

create_user("Arjun", "editor", False)
# Arjun | editor | active=False | score=0

create_user("Sara", score=95)
# Sara | viewer | active=True | score=95
```

---

### The Mutable Default Argument Trap

**Never use a mutable object (list, dict, set) as a default value.**

```python
# WRONG — the list is created ONCE when the function is defined
# and SHARED across all calls
def add_item(item, items=[]):
    items.append(item)
    return items

print(add_item("apple"))    # ['apple']
print(add_item("banana"))   # ['apple', 'banana']  ← WRONG — previous call's data is still there
print(add_item("cherry"))   # ['apple', 'banana', 'cherry']  ← keeps accumulating
```

The default list `[]` is created once at function definition time and reused on every call that doesn't pass `items`. It's the same list object every time.

```python
# CORRECT — use None as default, create fresh object inside
def add_item(item, items=None):
    if items is None:
        items = []          # new list created on every call
    items.append(item)
    return items

print(add_item("apple"))    # ['apple']
print(add_item("banana"))   # ['banana']  — fresh list each time
print(add_item("cherry"))   # ['cherry']

# Passing an existing list still works
my_list = ["existing"]
print(add_item("new", my_list))   # ['existing', 'new']
```

This is one of the most famous Python gotchas. The fix is always the same: `None` as default, create inside.

---

## Keyword Arguments

Pass arguments by name — order no longer matters.

```python
def introduce(name, age, city):
    print(f"{name}, {age}, from {city}")

# Positional — order matters
introduce("Mohamed", 25, "Chennai")

# Keyword — order doesn't matter
introduce(age=25, city="Chennai", name="Mohamed")
introduce(name="Mohamed", city="Chennai", age=25)

# Mix — positional first, keyword after
introduce("Mohamed", city="Chennai", age=25)
```

**Positional arguments must always come before keyword arguments:**

```python
introduce(age=25, "Mohamed", "Chennai")
# SyntaxError: positional argument follows keyword argument
```

**Keyword arguments make calls self-documenting:**

```python
# Hard to read — what do these values mean?
create_account("Mohamed", True, False, 30, "admin")

# Clear — every argument is labelled
create_account(
    name="Mohamed",
    is_active=True,
    is_verified=False,
    age=30,
    role="admin"
)
```

---

## Docstrings

A string literal as the very first statement in a function body. Documents what the function does.

```python
def add(a, b):
    """Returns the sum of a and b."""
    return a + b
```

**Accessed via `__doc__` or `help()`:**

```python
print(add.__doc__)    # Returns the sum of a and b.
help(add)             # displays formatted documentation
```

**Multi-line docstring — the standard format:**

```python
def calculate_discount(price, discount_percent, max_discount=None):
    """
    Calculate the discounted price.

    Args:
        price: Original price as a float.
        discount_percent: Discount percentage (0-100).
        max_discount: Optional cap on discount amount.

    Returns:
        Final price after discount as a float.

    Raises:
        ValueError: If discount_percent is not between 0 and 100.
    """
    if not 0 <= discount_percent <= 100:
        raise ValueError("discount_percent must be between 0 and 100")

    discount = price * (discount_percent / 100)

    if max_discount is not None:
        discount = min(discount, max_discount)

    return price - discount
```

**Docstring conventions:**

```python
# One-liner — for simple, obvious functions
def double(n):
    """Return n multiplied by 2."""
    return n * 2

# Multi-line — for anything with parameters, return values, or exceptions
# First line: short summary
# Blank line
# Args, Returns, Raises sections
```

Docstrings are not comments — they're accessible at runtime. IDEs display them on hover. They're how Python's `help()` system works.

---

## Scope

Where a variable can be seen and used. Python has LEGB scope rules: **L**ocal → **E**nclosing → **G**lobal → **B**uilt-in.

### Local scope — variables inside a function

```python
def greet():
    message = "hello"    # local variable
    print(message)       # works fine

greet()                  # hello
print(message)           # NameError: name 'message' is not defined
                         # message only exists inside greet()
```

**Each function call gets its own local scope:**

```python
def show(x):
    result = x * 2
    print(result)

show(5)     # result = 10
show(10)    # result = 20
# These are separate 'result' variables — no connection between calls
```

### Global scope — variables defined at module level

```python
name = "Mohamed"     # global variable

def greet():
    print(name)      # reading global from inside function — allowed

greet()              # Mohamed
print(name)          # Mohamed
```

**Reading a global works. Modifying it does not — without `global`:**

```python
count = 0

def increment():
    count += 1        # UnboundLocalError: local variable 'count' referenced before assignment
    # Python sees the assignment and treats 'count' as local
    # but it has no local value yet

increment()
```

When Python sees an assignment to a name inside a function, it marks that name as local for the entire function. So `count += 1` tries to read a local `count` that hasn't been set yet.

---

## `global` Keyword

Declares that a name inside a function refers to the global variable.

```python
count = 0

def increment():
    global count       # "I mean the global count, not a local one"
    count += 1

increment()
increment()
increment()
print(count)    # 3
```

**`global` must be declared before using the variable:**

```python
total = 100

def add_to_total(n):
    global total
    total += n
    print(f"total is now {total}")

add_to_total(50)   # total is now 150
add_to_total(25)   # total is now 175
print(total)       # 175
```

### Use `global` sparingly — here's why

```python
# BAD — global state makes code hard to follow and test
score = 0

def add_points(n):
    global score
    score += n

def reset():
    global score
    score = 0

# You have to track global state across all functions
# Any function anywhere can change score
# Hard to test in isolation
```

```python
# GOOD — pass and return values instead
def add_points(score, n):
    return score + n

def reset():
    return 0

score = 0
score = add_points(score, 10)
score = add_points(score, 5)
score = reset()
# score is explicit and traceable
```

The only times `global` is acceptable are simple scripts, counters in small programs, or module-level configuration. In any real project, pass values as arguments and return results.

---

## `pass` Statement

A placeholder that does nothing. Used when Python requires a statement syntactically but you have nothing to put there yet.

```python
# Empty function — SyntaxError without pass
def todo():
    # SyntaxError if this is the only line
    pass

# Empty class
class MyClass:
    pass

# Empty if branch
def process(x):
    if x > 0:
        pass          # TODO: handle positive case
    else:
        print("negative")
```

**`pass` vs `...` (Ellipsis) — both work as placeholders:**

```python
def not_implemented_yet():
    pass

def also_not_implemented():
    ...    # Ellipsis — often used in type stubs and abstract definitions
```

**`pass` in exception handling — silently ignore:**

```python
try:
    risky_operation()
except SomeError:
    pass    # deliberately ignoring this error
            # use sparingly — hiding errors is dangerous
            # better: use contextlib.suppress() in real code
```

---

## Putting It All Together

```python
# A realistic function combining everything above
def calculate_final_grade(name, scores, passing_score=60, weights=None):
    """
    Calculate a student's final grade from a list of scores.

    Args:
        name: Student's name (str).
        scores: List of numeric scores (list of int/float).
        passing_score: Minimum score to pass. Default is 60.
        weights: Optional list of weights for each score. Default is equal weight.

    Returns:
        A dict with name, average, grade letter, and pass/fail status.
    """
    if not scores:
        return {"name": name, "error": "no scores provided"}

    if weights is None:
        weights = [1] * len(scores)    # equal weight — mutable default done right

    if len(weights) != len(scores):
        return {"name": name, "error": "scores and weights must have same length"}

    # Weighted average
    total_weight = sum(weights)
    weighted_sum = sum(s * w for s, w in zip(scores, weights))
    average = weighted_sum / total_weight

    # Grade
    if average >= 90:
        grade = "A"
    elif average >= 80:
        grade = "B"
    elif average >= 70:
        grade = "C"
    elif average >= 60:
        grade = "D"
    else:
        grade = "F"

    return {
        "name":    name,
        "average": round(average, 2),
        "grade":   grade,
        "passed":  average >= passing_score
    }


# Calls
result = calculate_final_grade("Mohamed", [88, 92, 75, 84])
print(result)
# {'name': 'Mohamed', 'average': 84.75, 'grade': 'B', 'passed': True}

result = calculate_final_grade(
    name="Priya",
    scores=[95, 88, 91],
    weights=[1, 2, 3]    # keyword args, custom weights
)
print(result)
# {'name': 'Priya', 'average': 91.17, 'grade': 'A', 'passed': True}

result = calculate_final_grade("Arjun", [45, 50, 38])
print(result)
# {'name': 'Arjun', 'average': 44.33, 'grade': 'F', 'passed': False}

result = calculate_final_grade("Sara", [])
print(result)
# {'name': 'Sara', 'error': 'no scores provided'}
```

---

## Key Takeaways

- **Parameter** = name in the definition. **Argument** = value at the call site.
- A function with no `return` returns `None`. Always capture return values when you need them.
- `return x, y` returns a tuple. Unpack with `a, b = func()`.
- Default parameters are evaluated **once at definition time** — never use mutable defaults (`[]`, `{}`). Use `None` and create inside.
- Keyword arguments make calls readable and order-independent. Positional must come before keyword.
- Docstrings are runtime-accessible documentation — write them for anything non-trivial.
- Variables inside a function are **local**. They don't exist outside.
- You can **read** globals from inside a function. You cannot **modify** them without `global`.
- Avoid `global` — pass arguments and return results instead.
- `pass` is a syntactic placeholder — use it for empty bodies you'll fill in later.