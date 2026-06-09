Let's go. Full topic, nothing skipped.

---

## 0.11 Error Handling Basics

---

## What Is an Exception

Normal code runs line by line. When something goes wrong at runtime, Python **raises an exception** — it creates an exception object and stops execution unless you handle it.

```python
print("line 1")
print(10 / 0)       # ZeroDivisionError raised here
print("line 3")     # never runs
```

Output:
```
line 1
Traceback (most recent call last):
  File "script.py", line 2, in <module>
    print(10 / 0)
ZeroDivisionError: division by zero
```

**Reading a traceback — top to bottom:**

```
Traceback (most recent call last):     ← always says this
  File "script.py", line 2, in <module>  ← where it happened
    print(10 / 0)                          ← the exact line
ZeroDivisionError: division by zero    ← exception type: message
```

The last line is the most important — exception type and message. The lines above show the call stack — which function called which, leading to the error.

**Exceptions are objects — they have a type and a message:**

```python
try:
    10 / 0
except ZeroDivisionError as e:
    print(type(e))     # <class 'ZeroDivisionError'>
    print(e)           # division by zero
    print(repr(e))     # ZeroDivisionError('division by zero')
```

---

## Common Exceptions — Know These Cold

```python
# ValueError — right type, wrong value
int("hello")                    # ValueError: invalid literal for int()
int("3.14")                     # ValueError: invalid literal for int()
[1,2,3].index(99)               # ValueError: 99 is not in list
"hello".encode("invalid")       # ValueError

# TypeError — wrong type entirely
"hello" + 5                     # TypeError: can only concatenate str (not "int") to str
len(42)                         # TypeError: object of type 'int' has no len()
1 + "2"                         # TypeError

# IndexError — index out of range
lst = [1, 2, 3]
lst[5]                          # IndexError: list index out of range
lst[-4]                         # IndexError
"hi"[10]                        # IndexError

# KeyError — dict key doesn't exist
d = {"a": 1}
d["b"]                          # KeyError: 'b'

# FileNotFoundError — file doesn't exist
open("missing.txt")             # FileNotFoundError: [Errno 2] No such file

# ZeroDivisionError — dividing by zero
10 / 0                          # ZeroDivisionError: division by zero
10 // 0                         # ZeroDivisionError: integer division by zero
10 % 0                          # ZeroDivisionError: integer modulo by zero

# AttributeError — object doesn't have that attribute or method
"hello".push("!")               # AttributeError: 'str' has no attribute 'push'
None.upper()                    # AttributeError: 'NoneType' has no attribute 'upper'
x = 5
x.append(1)                     # AttributeError: 'int' has no attribute 'append'

# NameError — variable doesn't exist
print(undefined_var)            # NameError: name 'undefined_var' is not defined
```

**Exception hierarchy — they all inherit from `BaseException`:**

```
BaseException
├── SystemExit              ← raised by sys.exit()
├── KeyboardInterrupt       ← Ctrl+C
└── Exception               ← almost everything you'll handle
    ├── ValueError
    ├── TypeError
    ├── IndexError
    ├── KeyError
    ├── FileNotFoundError   ← subclass of OSError
    ├── ZeroDivisionError
    ├── AttributeError
    ├── NameError
    ├── RuntimeError
    ├── StopIteration
    └── ... many more
```

This matters because catching a parent catches all its children too.

---

## `try / except` — Basic Structure

```python
try:
    # code that might raise an exception
    result = 10 / 0
except ZeroDivisionError:
    # runs only if ZeroDivisionError was raised in the try block
    print("cannot divide by zero")

print("program continues")    # always runs — exception was handled
```

**Without handling — program crashes:**
```python
result = 10 / 0        # program dies here
print("never runs")
```

**With handling — program continues:**
```python
try:
    result = 10 / 0
except ZeroDivisionError:
    result = 0
print(result)          # 0  — program survived
```

**The `try` block exits immediately when an exception is raised:**

```python
try:
    print("step 1")
    x = int("bad")     # raises ValueError here
    print("step 2")    # never runs
    print("step 3")    # never runs
except ValueError:
    print("caught it")

# step 1
# caught it
```

---

## Catching Specific Exceptions

**Always catch the most specific exception you can:**

```python
# BAD — bare except catches everything including KeyboardInterrupt, SystemExit
try:
    x = int(input("number: "))
except:
    print("something went wrong")    # what went wrong? no idea

# BAD — Exception is too broad when you know what to expect
try:
    x = int(input("number: "))
except Exception:
    print("something went wrong")

# GOOD — catch exactly what you expect
try:
    x = int(input("number: "))
except ValueError:
    print("that's not a valid number")
```

**Why bare `except:` is dangerous:**

```python
import time

try:
    time.sleep(10)    # user presses Ctrl+C after 2 seconds
except:
    pass              # silently swallows KeyboardInterrupt
                      # user can never stop this program with Ctrl+C
```

**Catching multiple specific exceptions:**

```python
# Option 1 — tuple of exceptions (same handler)
try:
    value = data[key]
    result = int(value)
except (KeyError, ValueError):
    print("missing key or bad value")

# Option 2 — separate handlers (different responses)
try:
    value = data[key]
    result = int(value)
except KeyError:
    print(f"key not found: {key}")
except ValueError:
    print(f"can't convert to int: {value}")
```

**Catching parent catches all children:**

```python
try:
    open("missing.txt")
except OSError:            # FileNotFoundError is a subclass of OSError
    print("OS error")      # this catches FileNotFoundError too

# More specific is better when you can be
try:
    open("missing.txt")
except FileNotFoundError:
    print("file not found")
except PermissionError:
    print("no permission to read file")
```

---

## `except Exception as e` — Capturing the Exception Object

```python
try:
    result = int("not a number")
except ValueError as e:
    print(e)           # invalid literal for int() with base 10: 'not a number'
    print(type(e))     # <class 'ValueError'>
    print(repr(e))     # ValueError("invalid literal for int() with base 10: 'not a number'")
```

**`str(e)` gives the message. `repr(e)` gives type + message.**

```python
try:
    d = {"a": 1}
    print(d["missing_key"])
except KeyError as e:
    print(e)            # 'missing_key'   — note: KeyError wraps key in quotes
    print(str(e))       # 'missing_key'
    print(e.args)       # ('missing_key',)  — tuple of arguments passed to exception
    print(e.args[0])    # missing_key      — the actual key
```

**Using `e` to make decisions:**

```python
def parse_config(data):
    try:
        host = data["host"]
        port = int(data["port"])
        return host, port
    except KeyError as e:
        print(f"missing required field: {e}")
        return None, None
    except ValueError as e:
        print(f"invalid port value: {e}")
        return None, None
```

**Logging the full traceback with `as e`:**

```python
import traceback

try:
    risky_operation()
except Exception as e:
    print(f"Error: {e}")
    traceback.print_exc()    # prints full traceback to stderr
    # or capture as string:
    tb = traceback.format_exc()
```

---

## `else` Clause — Runs Only If No Exception

```python
try:
    result = int("42")
except ValueError:
    print("conversion failed")
else:
    print(f"conversion succeeded: {result}")    # only runs if no exception
```

**The key distinction — `else` vs putting code at the end of `try`:**

```python
# BAD — putting success code inside try catches its exceptions too
try:
    result = int(user_input)
    send_to_database(result)    # if THIS raises an exception, it's caught by ValueError
except ValueError:
    print("bad input")

# GOOD — else keeps success code separate
try:
    result = int(user_input)
except ValueError:
    print("bad input")
else:
    send_to_database(result)    # only runs on success, NOT caught by ValueError above
```

**A realistic example:**

```python
def load_user(user_id):
    try:
        with open(f"users/{user_id}.json") as f:
            data = f.read()
    except FileNotFoundError:
        print(f"user {user_id} not found")
        return None
    else:
        # only runs if file was opened successfully
        import json
        return json.loads(data)
```

---

## `finally` Clause — Always Runs

`finally` runs whether an exception was raised or not, whether it was handled or not, even if there's a `return` in the `try` block.

```python
try:
    result = 10 / 2
except ZeroDivisionError:
    print("division error")
finally:
    print("this always runs")

# this always runs
# result = 5.0
```

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("division error")
finally:
    print("this always runs")

# division error
# this always runs
```

**`finally` runs even when exception is NOT caught:**

```python
try:
    result = int("bad")    # ValueError — no except for ValueError
finally:
    print("finally runs anyway")

# finally runs anyway
# Traceback: ValueError  ← exception still propagates after finally
```

**`finally` runs even with `return`:**

```python
def test():
    try:
        return "from try"
    finally:
        print("finally ran")    # runs BEFORE the return happens
        # if you put a return here it overrides the try's return

result = test()
# finally ran
print(result)    # from try
```

**Main use — cleanup that must happen no matter what:**

```python
# Without context manager — manual cleanup
f = open("file.txt")
try:
    data = f.read()
    process(data)
except Exception as e:
    print(f"error: {e}")
finally:
    f.close()    # ALWAYS closes the file — even if process() raised an exception

# With context manager — cleaner, same guarantee
with open("file.txt") as f:    # __exit__ is the finally under the hood
    data = f.read()
    process(data)
```

**Full `try / except / else / finally` together:**

```python
def divide(a, b):
    try:
        result = a / b              # might raise ZeroDivisionError
    except ZeroDivisionError as e:
        print(f"error: {e}")
        result = None
    else:
        print("division successful")    # only if no exception
    finally:
        print("divide() finished")      # always

    return result

print(divide(10, 2))
# division successful
# divide() finished
# 5.0

print(divide(10, 0))
# error: division by zero
# divide() finished
# None
```

**Execution order:**
```
try block runs
    → exception raised?
        YES → matching except block runs → finally runs
        NO  → else block runs            → finally runs
```

---

## `raise` — Manually Raising an Exception

You can raise exceptions yourself to signal that something is wrong.

```python
raise ValueError("must be positive")
# ValueError: must be positive
```

**Raising with a message:**

```python
def set_age(age):
    if age < 0:
        raise ValueError(f"age cannot be negative, got {age}")
    if age > 150:
        raise ValueError(f"age {age} is unrealistically large")
    return age

set_age(-5)    # ValueError: age cannot be negative, got -5
set_age(200)   # ValueError: age 200 is unrealistically large
```

**Raising different exception types:**

```python
def get_user(user_id, users):
    if not isinstance(user_id, int):
        raise TypeError(f"user_id must be int, got {type(user_id).__name__}")
    if user_id not in users:
        raise KeyError(f"no user with id {user_id}")
    return users[user_id]
```

**Re-raising — catch, do something, then let it propagate:**

```python
def load_config(path):
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError as e:
        print(f"config file missing: {path}")
        raise    # re-raises the same exception — preserves original traceback

# bare raise re-raises current exception
# raise e  — also works but resets the traceback to this line
```

**`raise` inside `except` — exception chaining:**

```python
try:
    value = int("bad")
except ValueError as e:
    raise RuntimeError("config parsing failed") from e
    # RuntimeError: config parsing failed
    # The above exception was the direct cause of the following exception:
    # ValueError: invalid literal...
```

`from e` links the two exceptions. The original cause is preserved in the traceback. This is the right way to wrap a low-level exception in a higher-level one.

**`raise` with no argument — only valid inside `except`:**

```python
try:
    int("bad")
except ValueError:
    print("logging the error...")
    raise    # re-raises the ValueError that was caught
```

---

## Putting It All Together

```python
def process_student_data(raw_input):
    """
    Parse and validate a student record from a raw string.
    Format: "Name, Score, City"
    """
    # Step 1 — parse
    try:
        parts = raw_input.strip().split(",")
        if len(parts) != 3:
            raise ValueError(f"expected 3 fields, got {len(parts)}")
        name  = parts[0].strip()
        score = int(parts[1].strip())
        city  = parts[2].strip()
    except ValueError as e:
        print(f"parse error: {e}")
        return None

    # Step 2 — validate
    try:
        if not name:
            raise ValueError("name cannot be empty")
        if not 0 <= score <= 100:
            raise ValueError(f"score must be 0-100, got {score}")
        if not city:
            raise ValueError("city cannot be empty")
    except ValueError as e:
        print(f"validation error: {e}")
        return None
    else:
        print(f"record valid: {name}, {score}, {city}")
    finally:
        print(f"finished processing: {raw_input!r}")

    return {"name": name, "score": score, "city": city}


# Test cases
print(process_student_data("  Mohamed, 88, Chennai  "))
# record valid: Mohamed, 88, Chennai
# finished processing: '  Mohamed, 88, Chennai  '
# {'name': 'Mohamed', 'score': 88, 'city': 'Chennai'}

print(process_student_data("Priya, not_a_number, Mumbai"))
# parse error: invalid literal for int() with base 10: 'not_a_number'
# finished processing: 'Priya, not_a_number, Mumbai'
# None

print(process_student_data("Arjun, 150, Delhi"))
# validation error: score must be 0-100, got 150
# finished processing: 'Arjun, 150, Delhi'
# None

print(process_student_data("only two fields"))
# parse error: expected 3 fields, got 1
# finished processing: 'only two fields'
# None
```

---

## Key Takeaways

- Exceptions are objects with a type and a message — the last line of a traceback tells you both.
- Always catch **specific** exceptions. Bare `except:` is dangerous — it swallows `KeyboardInterrupt` and `SystemExit`.
- `except ValueError as e:` — `e` gives you the message, `e.args` gives the raw arguments, `repr(e)` gives type + message.
- `else` runs only when **no exception** occurred — keeps success code outside the `try` block so it doesn't accidentally get caught.
- `finally` runs **always** — with or without exception, with or without `return`. Use it for cleanup.
- `raise ValueError("message")` — raise exceptions yourself to signal bad input or violated rules.
- Bare `raise` inside `except` re-raises the current exception with the original traceback preserved.
- `raise NewError("msg") from original_error` — chains exceptions; preserves root cause in traceback.
- Never use exceptions for normal control flow — they're for genuinely exceptional conditions.