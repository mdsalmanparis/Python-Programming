Let's go. Full topic, nothing skipped.

---

## 1.5 Exceptions & Context Managers

---

## Exception Hierarchy

Every exception in Python is an object that inherits from `BaseException`.

```
BaseException
├── SystemExit                 ← raised by sys.exit()
├── KeyboardInterrupt          ← Ctrl+C
├── GeneratorExit              ← raised when generator is closed
└── Exception                  ← everything you normally handle
    ├── ArithmeticError
    │   ├── ZeroDivisionError
    │   ├── OverflowError
    │   └── FloatingPointError
    ├── LookupError
    │   ├── IndexError
    │   └── KeyError
    ├── AttributeError
    ├── NameError
    │   └── UnboundLocalError
    ├── TypeError
    ├── ValueError
    │   └── UnicodeError
    ├── OSError  (= IOError = EnvironmentError)
    │   ├── FileNotFoundError
    │   ├── PermissionError
    │   ├── FileExistsError
    │   ├── TimeoutError
    │   └── ConnectionError
    │       ├── ConnectionRefusedError
    │       └── ConnectionResetError
    ├── RuntimeError
    │   └── RecursionError
    ├── StopIteration
    ├── NotImplementedError
    ├── MemoryError
    └── ImportError
        └── ModuleNotFoundError
```

**Why the hierarchy matters — catching a parent catches all its children:**

```python
try:
    d = {}
    d["missing"]          # KeyError
except LookupError:       # catches KeyError AND IndexError
    print("lookup failed")

try:
    open("missing.txt")   # FileNotFoundError
except OSError:           # catches any OS-level error
    print("OS error")

# Catching Exception catches almost everything — but NOT SystemExit or KeyboardInterrupt
try:
    something_risky()
except Exception as e:
    print(f"caught: {e}")
    # KeyboardInterrupt (Ctrl+C) will NOT be caught here — correct behaviour
```

**`SystemExit` and `KeyboardInterrupt` are NOT subclasses of `Exception`:**

```python
# This is why bare except: is dangerous
try:
    time.sleep(100)
except Exception:
    pass    # user presses Ctrl+C — KeyboardInterrupt NOT caught here, propagates up correctly

try:
    time.sleep(100)
except:          # bare except catches EVERYTHING including KeyboardInterrupt
    pass         # user can NEVER stop this program — very bad
```

---

## `try` / `except` / `else` / `finally` — Full Execution Order

```python
def demonstrate(value):
    try:
        print("  try: running")
        result = 10 / value         # might raise ZeroDivisionError
        print(f"  try: result = {result}")
    except ZeroDivisionError as e:
        print(f"  except: caught {e}")
    else:
        print(f"  else: no exception, result was {result}")
    finally:
        print("  finally: always runs")
    print("  after: continues here if exception was handled")

print("=== value = 2 ===")
demonstrate(2)
# try: running
# try: result = 5.0
# else: no exception, result was 5.0
# finally: always runs
# after: continues here if exception was handled

print("=== value = 0 ===")
demonstrate(0)
# try: running
# except: caught division by zero
# finally: always runs
# after: continues here if exception was handled
```

**`finally` runs even when exception is NOT caught:**

```python
def demonstrate_uncaught():
    try:
        print("try")
        raise ValueError("oops")
    except TypeError:               # wrong exception type — not caught
        print("except: not reached")
    finally:
        print("finally: still runs")  # runs before exception propagates
    print("after: NOT reached")       # skipped — exception propagated

try:
    demonstrate_uncaught()
except ValueError:
    print("outer caught it")
# try
# finally: still runs
# outer caught it
```

**`finally` runs even with `return` inside `try`:**

```python
def read_file(path):
    f = open(path)
    try:
        return f.read()       # return is triggered here
    finally:
        f.close()             # but finally runs BEFORE the return completes
        print("file closed")  # this prints

result = read_file("file.txt")
# file closed
# result = contents of file
```

**`finally` with `return` overrides `try`'s `return`:**

```python
def test():
    try:
        return "from try"
    finally:
        return "from finally"   # this wins — overrides try's return

print(test())    # from finally
# Almost never do this — confusing
```

**What `else` is actually for — keeping success code outside `try`:**

```python
# BAD — success code inside try accidentally gets caught
def parse_and_save(text):
    try:
        value = int(text)
        save_to_database(value)    # if THIS raises, it's caught by ValueError
    except ValueError:
        print("bad input")

# GOOD — else separates parsing error from save error
def parse_and_save(text):
    try:
        value = int(text)          # only this is guarded
    except ValueError:
        print("bad input")
        return
    else:
        save_to_database(value)    # only runs if no exception — NOT caught by ValueError
```

---

## Catching Multiple Exceptions

```python
# Tuple — one handler for multiple types
try:
    value = data[key]
    result = int(value)
except (KeyError, ValueError) as e:
    print(f"error: {e}")

# Separate handlers — different responses per type
try:
    value = data[key]
    result = int(value)
except KeyError:
    print(f"key not found: {key}")
    result = 0
except ValueError:
    print(f"cannot convert to int: {value!r}")
    result = 0
except Exception as e:
    print(f"unexpected error: {type(e).__name__}: {e}")
    raise   # re-raise unexpected ones
```

**Order matters — more specific first:**

```python
# WRONG — OSError catches FileNotFoundError before we get to it
try:
    open("missing.txt")
except OSError:
    print("OS error")          # always runs for file errors
except FileNotFoundError:      # never reached — FileNotFoundError IS an OSError
    print("file not found")

# CORRECT — specific first
try:
    open("missing.txt")
except FileNotFoundError:
    print("file not found")    # reached for missing files
except PermissionError:
    print("no permission")
except OSError:
    print("other OS error")    # catch-all for remaining OS errors
```

---

## Exception Chaining — `raise X from Y`

Python tracks how exceptions relate to each other.

### Implicit chaining — `__context__`

When an exception is raised inside an `except` block, Python automatically links them.

```python
try:
    int("bad")
except ValueError:
    raise RuntimeError("config failed")

# Traceback:
# ValueError: invalid literal for int()...
#
# During handling of the above exception, another exception occurred:
#
# RuntimeError: config failed
```

The `ValueError` is stored in `RuntimeError.__context__`. This is **implicit chaining**.

### Explicit chaining — `raise X from Y` and `__cause__`

```python
try:
    int("bad")
except ValueError as e:
    raise RuntimeError("config failed") from e

# Traceback:
# ValueError: invalid literal for int()...
#
# The above exception was the direct cause of the following exception:
#
# RuntimeError: config failed
```

`raise X from Y` sets `X.__cause__ = Y`. The message changes from "During handling" to "direct cause". Use this when you intentionally wrap a low-level exception in a higher-level one.

```python
# Real-world pattern — wrap library exceptions in your own
class ConfigError(Exception):
    pass

def load_config(path):
    try:
        with open(path) as f:
            import json
            return json.load(f)
    except FileNotFoundError as e:
        raise ConfigError(f"config file not found: {path}") from e
    except json.JSONDecodeError as e:
        raise ConfigError(f"invalid JSON in config: {path}") from e
```

### Suppress chaining — `raise X from None`

```python
try:
    int("bad")
except ValueError:
    raise RuntimeError("config failed") from None

# Traceback shows ONLY:
# RuntimeError: config failed
# (the ValueError is hidden)
```

Use `from None` when the original exception is an implementation detail you don't want to expose to callers.

### Inspecting the chain

```python
try:
    try:
        int("bad")
    except ValueError as e:
        raise RuntimeError("wrapped") from e
except RuntimeError as e:
    print(e.__cause__)      # invalid literal for int()...  — from 'raise X from Y'
    print(e.__context__)    # invalid literal for int()...  — original exception
    print(e.__suppress_context__)  # True if 'from None' was used
```

---

## `raise` vs `raise e` — Preserving the Traceback

```python
def process():
    try:
        int("bad")
    except ValueError as e:
        # Option 1: bare raise — re-raises exact same exception with original traceback
        raise

        # Option 2: raise e — re-raises but traceback starts HERE
        raise e      # loses original location — worse

        # Option 3: raise new — new exception, optionally chained
        raise RuntimeError("failed") from e
```

**The difference in traceback:**

```python
def deep_function():
    int("bad")           # line 2 — original error location

def process():
    try:
        deep_function()
    except ValueError as e:
        raise            # traceback shows line 2 in deep_function — correct

def process_bad():
    try:
        deep_function()
    except ValueError as e:
        raise e          # traceback shows THIS line — original location lost
```

**Rule: always use bare `raise` when re-raising the same exception.**

---

## Custom Exception Classes

### Basic custom exception

```python
class AppError(Exception):
    """Base exception for this application."""
    pass

class ValidationError(AppError):
    """Input validation failed."""
    pass

class DatabaseError(AppError):
    """Database operation failed."""
    pass

class NotFoundError(AppError):
    """Resource not found."""
    pass

# Usage
raise ValidationError("score must be between 0 and 100")
raise NotFoundError("user 42 not found")

# Callers can catch specific or broad
try:
    process()
except ValidationError:
    return 400
except NotFoundError:
    return 404
except AppError:
    return 500   # any other app error
```

### Adding context attributes

```python
class ValidationError(Exception):
    def __init__(self, message, field=None, value=None, code=None):
        super().__init__(message)
        self.field = field
        self.value = value
        self.code  = code or "invalid"

    def __str__(self):
        parts = [super().__str__()]
        if self.field:
            parts.append(f"field={self.field!r}")
        if self.value is not None:
            parts.append(f"value={self.value!r}")
        return " | ".join(parts)

# Usage
raise ValidationError("must be positive", field="score", value=-5, code="range_error")
# ValidationError: must be positive | field='score' | value=-5

try:
    raise ValidationError("too short", field="name", value="A")
except ValidationError as e:
    print(e.field)   # name
    print(e.value)   # A
    print(e.code)    # invalid
```

### Designing an exception hierarchy

```python
# Top-level base — all app exceptions inherit from this
class AppError(Exception):
    """Base class for all application exceptions."""

    def __init__(self, message, code=None, details=None):
        super().__init__(message)
        self.code    = code
        self.details = details or {}

    def to_dict(self):
        return {
            "error":   type(self).__name__,
            "message": str(self),
            "code":    self.code,
            "details": self.details,
        }

# Domain-specific subclasses
class ValidationError(AppError):
    """Input validation failed — HTTP 400."""

class AuthError(AppError):
    """Authentication/authorisation failed — HTTP 401/403."""

class NotFoundError(AppError):
    """Resource not found — HTTP 404."""

class ConflictError(AppError):
    """Resource conflict — HTTP 409."""

class ServiceError(AppError):
    """External service failure — HTTP 502."""

# Fine-grained subclasses
class FieldValidationError(ValidationError):
    """Specific field failed validation."""
    def __init__(self, field, message, value=None):
        super().__init__(f"{field}: {message}", code="field_error",
                         details={"field": field, "value": value})
        self.field = field

class TokenExpiredError(AuthError):
    """JWT token has expired."""
    def __init__(self):
        super().__init__("token has expired", code="token_expired")

# Usage in a service layer
def get_user(user_id: int):
    if not isinstance(user_id, int):
        raise FieldValidationError("user_id", "must be an integer", value=user_id)
    user = db.find(user_id)
    if user is None:
        raise NotFoundError(f"user {user_id} not found", code="user_not_found")
    return user

# Handler in API layer
try:
    user = get_user(request.user_id)
except FieldValidationError as e:
    return Response(e.to_dict(), status=400)
except NotFoundError as e:
    return Response(e.to_dict(), status=404)
except AuthError as e:
    return Response(e.to_dict(), status=401)
except AppError as e:
    return Response(e.to_dict(), status=500)
```

---

## Context Managers — `__enter__` and `__exit__`

Already introduced in 0.10. Here we go deep.

### The Protocol

```python
class ManagedResource:
    def __enter__(self):
        # Set up — acquire resource
        print("acquiring resource")
        return self    # this becomes the 'as' target

    def __exit__(self, exc_type, exc_val, exc_tb):
        # Tear down — release resource
        # exc_type: exception class or None
        # exc_val:  exception instance or None
        # exc_tb:   traceback object or None
        print("releasing resource")

        if exc_type is not None:
            print(f"exception occurred: {exc_type.__name__}: {exc_val}")

        return False   # False = propagate exception (normal)
                       # True  = suppress exception (rarely correct)

with ManagedResource() as r:
    print(f"using {r}")

# acquiring resource
# using <__main__.ManagedResource object>
# releasing resource
```

### Suppressing exceptions — return `True`

```python
class SuppressZeroDivision:
    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is ZeroDivisionError:
            print(f"suppressed: {exc_val}")
            return True     # suppress — execution continues after the with block
        return False        # anything else propagates

with SuppressZeroDivision():
    x = 10 / 0     # would normally crash
    print("this is skipped — exception jumped to __exit__")

print("execution continues here")
# suppressed: division by zero
# execution continues here
```

### A realistic context manager

```python
import time

class Timer:
    def __init__(self, name=""):
        self.name    = name
        self.elapsed = None

    def __enter__(self):
        self._start = time.perf_counter()
        return self    # 'as' target

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.elapsed = time.perf_counter() - self._start
        label = f"[{self.name}] " if self.name else ""
        print(f"{label}elapsed: {self.elapsed:.4f}s")
        return False   # don't suppress exceptions

with Timer("sorting") as t:
    data = sorted(range(1_000_000), reverse=True)

print(f"captured: {t.elapsed:.4f}s")
# [sorting] elapsed: 0.1234s
# captured: 0.1234s
```

---

## `contextlib.contextmanager` — Generator-Based Context Managers

Instead of writing a full class, use a generator function. Everything before `yield` = `__enter__`. Everything after `yield` = `__exit__`.

```python
from contextlib import contextmanager

@contextmanager
def managed_resource(name):
    print(f"acquiring {name}")     # __enter__ code
    resource = f"resource:{name}"
    try:
        yield resource             # yield value becomes 'as' target
    finally:
        print(f"releasing {name}") # __exit__ code — always runs

with managed_resource("database") as r:
    print(f"using {r}")

# acquiring database
# using resource:database
# releasing database
```

**Exception handling in `@contextmanager`:**

```python
from contextlib import contextmanager

@contextmanager
def safe_open(path, mode="r"):
    f = None
    try:
        f = open(path, mode, encoding="utf-8")
        yield f                         # __enter__ returns file
    except FileNotFoundError:
        print(f"file not found: {path}")
        yield None                      # yield None if file missing
    finally:
        if f:
            f.close()                   # always close if opened

with safe_open("data.txt") as f:
    if f:
        print(f.read())
```

**Re-raising exceptions in `@contextmanager`:**

```python
from contextlib import contextmanager

@contextmanager
def transaction(conn):
    try:
        yield conn
        conn.commit()              # only if no exception
    except Exception:
        conn.rollback()            # on any exception, roll back
        raise                      # re-raise — don't suppress

with transaction(db_conn) as conn:
    conn.execute("INSERT ...")
    conn.execute("UPDATE ...")
# if either fails: rollback, then exception propagates
# if both succeed: commit
```

**Timer as `@contextmanager`:**

```python
from contextlib import contextmanager
import time

@contextmanager
def timer(name=""):
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start
        label   = f"[{name}] " if name else ""
        print(f"{label}{elapsed:.4f}s")

with timer("data processing"):
    process_data()
# [data processing] 0.2341s
```

---

## `contextlib.suppress()` — Cleaner Exception Ignoring

```python
from contextlib import suppress

# Old way — verbose
try:
    os.remove("temp.txt")
except FileNotFoundError:
    pass

# New way — intent is clear
with suppress(FileNotFoundError):
    os.remove("temp.txt")

# Suppress multiple exception types
with suppress(FileNotFoundError, PermissionError):
    os.remove("protected.txt")
```

**What `suppress` does internally:**

```python
from contextlib import contextmanager

@contextmanager
def suppress(*exceptions):
    try:
        yield
    except exceptions:
        pass    # silently ignore the specified exceptions
```

**When NOT to use `suppress` — only for truly ignorable errors:**

```python
# GOOD — file might not exist, that's fine
with suppress(FileNotFoundError):
    os.remove("cache.tmp")

# BAD — hiding real bugs
with suppress(Exception):    # too broad — hides programming errors
    process_payment(order)
```

---

## Other `contextlib` Utilities

### `contextlib.nullcontext()` — No-Op Context Manager

```python
from contextlib import nullcontext

def process(data, lock=None):
    # Use lock if provided, otherwise no-op
    ctx = lock if lock is not None else nullcontext()
    with ctx:
        do_work(data)

# Without nullcontext you'd need:
def process(data, lock=None):
    if lock is not None:
        with lock:
            do_work(data)
    else:
        do_work(data)      # duplicated logic
```

### `contextlib.ExitStack()` — Dynamic Context Managers

When you don't know at compile time how many context managers you need.

```python
from contextlib import ExitStack

# Open unknown number of files
filenames = ["a.txt", "b.txt", "c.txt"]

with ExitStack() as stack:
    files = [stack.enter_context(open(f, encoding="utf-8")) for f in filenames]
    # all files are open here
    for f in files:
        print(f.read())
# all files are closed here — ExitStack closes them in LIFO order

# Conditionally entering context managers
with ExitStack() as stack:
    if use_transaction:
        stack.enter_context(transaction(conn))
    if use_lock:
        stack.enter_context(threading.Lock())
    do_work()
```

---

## Putting It All Together

```python
from contextlib import contextmanager, suppress
import time
import logging

logger = logging.getLogger(__name__)

# Custom exception hierarchy
class ServiceError(Exception):
    def __init__(self, message, code=None, retryable=False):
        super().__init__(message)
        self.code      = code
        self.retryable = retryable

class ConnectionError(ServiceError):
    def __init__(self, host):
        super().__init__(f"cannot connect to {host}", code="conn_failed", retryable=True)
        self.host = host

class TimeoutError(ServiceError):
    def __init__(self, seconds):
        super().__init__(f"timed out after {seconds}s", code="timeout", retryable=True)
        self.seconds = seconds

class DataError(ServiceError):
    def __init__(self, message):
        super().__init__(message, code="bad_data", retryable=False)

# Context manager for timed + retried operations
@contextmanager
def timed_operation(name, timeout=5.0):
    start = time.perf_counter()
    try:
        yield
    except TimeoutError:
        raise
    finally:
        elapsed = time.perf_counter() - start
        logger.debug(f"{name} took {elapsed:.3f}s")
        if elapsed > timeout:
            raise TimeoutError(timeout)

# Retry context manager
@contextmanager
def retry(times=3, delay=1.0):
    last_error = None
    for attempt in range(1, times + 1):
        try:
            yield attempt
            return          # success — exit generator
        except ServiceError as e:
            last_error = e
            if not e.retryable or attempt == times:
                raise
            logger.warning(f"attempt {attempt} failed: {e} — retrying in {delay}s")
            time.sleep(delay)
    raise last_error

# Usage combining everything
def fetch_data(host, key):
    try:
        with timed_operation("fetch", timeout=2.0):
            with retry(times=3) as attempt:
                logger.info(f"fetching from {host}, attempt {attempt}")
                result = make_request(host, key)
                return result

    except ConnectionError as e:
        logger.error(f"connection failed: {e}")
        raise ServiceError(f"service unavailable") from e

    except TimeoutError as e:
        logger.error(f"timed out: {e}")
        raise

    except DataError as e:
        logger.error(f"bad data: {e}")
        raise

    finally:
        # Cleanup that always runs — log, metrics, release resources
        with suppress(Exception):    # suppress any cleanup errors
            cleanup_request()
```

---

## Key Takeaways

- `BaseException` → `Exception` → everything you normally catch. `SystemExit` and `KeyboardInterrupt` are NOT subclasses of `Exception` — bare `except:` is the only way to accidentally catch them. Never use bare `except:`.
- Execution order: `try` → `except` (if exception) → `else` (if NO exception) → `finally` (always).
- `else` exists so success code isn't inside `try` — keeps exception handling scope tight.
- `finally` runs even with `return`, even with uncaught exceptions, even with `sys.exit()`.
- `raise X from Y` — explicit chaining, message says "direct cause". `raise X from None` — suppresses the chain entirely.
- Bare `raise` preserves the original traceback. `raise e` resets it to the current line — always prefer bare `raise`.
- Custom exceptions: inherit from a domain base class. Add context attributes in `__init__`. Use `from e` when wrapping low-level exceptions.
- `__exit__` receives `(exc_type, exc_val, exc_tb)`. Return `True` to suppress, `False` to propagate.
- `@contextmanager`: code before `yield` = setup, `yield` = what `as` receives, code after `yield` (in `finally`) = teardown.
- `suppress(*exceptions)` — use for truly ignorable errors only. Never suppress `Exception` broadly.
- `ExitStack` — when the number of context managers is dynamic or conditional.