Let's go. Full topic, nothing skipped.

---

## 2.8 `contextlib`

---

## Why `contextlib` Exists

Context managers handle the setup/teardown pattern — acquire a resource, use it, release it regardless of what happens. Writing a full class with `__enter__` and `__exit__` for every context manager is verbose. `contextlib` gives you cleaner tools to build and use context managers.

```python
# Full class — verbose for simple cases
class Timer:
    def __enter__(self):
        import time
        self._start = time.perf_counter()
        return self

    def __exit__(self, *exc):
        import time
        self.elapsed = time.perf_counter() - self._start
        return False

# contextlib — same result, half the code
from contextlib import contextmanager
import time

@contextmanager
def timer():
    start = time.perf_counter()
    yield
    print(f"elapsed: {time.perf_counter() - start:.4f}s")
```

---

## `@contextmanager` — Generator-Based Context Managers

The most important tool in `contextlib`. Turns a generator function into a context manager.

### The Mechanics

```python
from contextlib import contextmanager

@contextmanager
def managed(name):
    print(f"setup: {name}")    # __enter__ code
    yield "the resource"       # yield value becomes the 'as' target
    print(f"teardown: {name}") # __exit__ code

with managed("database") as resource:
    print(f"using: {resource}")

# setup: database
# using: the resource
# teardown: database
```

**The three zones:**

```python
from contextlib import contextmanager

@contextmanager
def my_context():
    # ── ZONE 1: __enter__ ──────────────────────────────────────
    # Everything before yield runs when the with block starts
    print("acquiring resource")
    resource = connect()

    # ── ZONE 2: yield ─────────────────────────────────────────
    yield resource    # what 'as' receives. Use bare 'yield' if nothing to expose

    # ── ZONE 3: __exit__ ──────────────────────────────────────
    # Everything after yield runs when the with block exits
    # (whether normally or via exception)
    print("releasing resource")
    resource.close()
```

### Exception Handling Inside `@contextmanager`

If an exception occurs in the `with` block, it is **re-raised at the `yield` statement**. You must handle it to provide cleanup.

```python
from contextlib import contextmanager

@contextmanager
def safe_context():
    resource = acquire()
    try:
        yield resource              # exception from with block re-raised HERE
    except ValueError as e:
        print(f"caught ValueError: {e}")
        # exception handled — with block continues after the with statement
    except Exception as e:
        print(f"unexpected error: {e}")
        raise                       # re-raise — propagate to caller
    finally:
        resource.close()            # always runs — the true cleanup

# Pattern 1 — re-raise after cleanup (most common)
@contextmanager
def db_transaction(conn):
    try:
        yield conn
        conn.commit()              # only reaches here if no exception
    except Exception:
        conn.rollback()            # on any exception: rollback
        raise                      # then re-raise — don't swallow the error
    finally:
        conn.close()               # always close

# Pattern 2 — suppress specific exception
@contextmanager
def suppress_network_errors():
    try:
        yield
    except (ConnectionError, TimeoutError) as e:
        print(f"Network error suppressed: {e}")
        # no raise — exception is suppressed, execution continues after with block
```

### Real-World Examples

**Database transaction:**

```python
from contextlib import contextmanager
import sqlite3

@contextmanager
def transaction(db_path: str):
    conn = sqlite3.connect(db_path)
    cursor = conn.cursor()
    try:
        yield cursor
        conn.commit()
    except Exception:
        conn.rollback()
        raise
    finally:
        conn.close()

with transaction("mydb.sqlite") as cur:
    cur.execute("INSERT INTO users VALUES (?, ?)", (1, "Mohamed"))
    cur.execute("UPDATE accounts SET balance = balance - 100 WHERE id = 1")
# commits if both succeed, rolls back if either fails
```

**Timer with captured result:**

```python
from contextlib import contextmanager
import time

@contextmanager
def timer(label: str = ""):
    start = time.perf_counter()
    stats = {"elapsed": None}
    try:
        yield stats                # caller can access stats inside the with block
    finally:
        stats["elapsed"] = time.perf_counter() - start
        if label:
            print(f"[{label}] {stats['elapsed']:.4f}s")

with timer("sorting") as t:
    data = sorted(range(1_000_000), reverse=True)

print(f"captured: {t['elapsed']:.4f}s")
# [sorting] 0.1234s
# captured: 0.1234s
```

**Temporary directory:**

```python
from contextlib import contextmanager
from pathlib import Path
import shutil
import tempfile

@contextmanager
def temp_directory(prefix: str = "tmp_"):
    tmpdir = Path(tempfile.mkdtemp(prefix=prefix))
    try:
        yield tmpdir
    finally:
        shutil.rmtree(tmpdir, ignore_errors=True)

with temp_directory("processing_") as tmp:
    (tmp / "data.csv").write_text("name,score\nMohamed,88")
    (tmp / "output").mkdir()
    # ... do work ...
# tmpdir deleted automatically — even if exception occurred
```

**Changing working directory temporarily:**

```python
from contextlib import contextmanager
import os

@contextmanager
def working_directory(path: str):
    original = os.getcwd()
    os.chdir(path)
    try:
        yield Path(path)
    finally:
        os.chdir(original)    # always restore

with working_directory("/tmp"):
    print(os.getcwd())    # /tmp
    # run commands, build files, etc.
print(os.getcwd())        # original directory restored
```

**Redirect stdout temporarily:**

```python
from contextlib import contextmanager
import sys
from io import StringIO

@contextmanager
def capture_output():
    captured = StringIO()
    original = sys.stdout
    sys.stdout = captured
    try:
        yield captured
    finally:
        sys.stdout = original

with capture_output() as out:
    print("this goes to the buffer")
    print("not to the terminal")

print("captured:", repr(out.getvalue()))
# captured: 'this goes to the buffer\nnot to the terminal\n'
```

### Multiple `yield` — NOT Allowed

```python
from contextlib import contextmanager

@contextmanager
def bad_context():
    yield 1
    yield 2    # RuntimeError: generator didn't stop after first yield

# contextmanager requires exactly ONE yield
```

---

## `contextlib.suppress(*exceptions)` — Silently Ignore Exceptions

```python
from contextlib import suppress

# Without suppress — verbose
try:
    os.remove("temp.txt")
except FileNotFoundError:
    pass

# With suppress — clear intent, less boilerplate
import os
with suppress(FileNotFoundError):
    os.remove("temp.txt")    # if file doesn't exist, silently do nothing
```

### Multiple Exception Types

```python
from contextlib import suppress

with suppress(FileNotFoundError, PermissionError):
    os.remove("protected.txt")

with suppress(KeyError, IndexError):
    value = data["missing_key"][0]

with suppress(ValueError, TypeError):
    result = int(user_input)
```

### What `suppress` Does Internally

```python
from contextlib import contextmanager

# suppress is implemented roughly like this:
@contextmanager
def suppress(*exceptions):
    try:
        yield
    except exceptions:
        pass    # silently ignore
```

### When to Use `suppress` vs `try/except`

```python
# Use suppress when:
# - Exception is completely ignorable — no logging, no alternative action
# - Simple one-liners

with suppress(FileNotFoundError):
    cache_file.unlink()

# Use try/except when:
# - You need to log the exception
# - You need to take a different action on failure
# - You need the exception object
# - Multiple different responses per exception type

try:
    cache_file.unlink()
except FileNotFoundError:
    logger.debug(f"Cache file {cache_file} not found — skipping cleanup")
except PermissionError as e:
    logger.error(f"Cannot delete cache file: {e}")
    raise
```

### `suppress` with Context — The Tricky Part

```python
from contextlib import suppress

# suppress only suppresses exceptions FROM THE WITH BLOCK
# Code after the with block always continues
result = "default"
with suppress(KeyError):
    result = data["missing"]    # KeyError raised
    result = "this line skipped"  # never reached

print(result)    # "default" — the assignment before KeyError is the last successful one

# This is subtly different from:
try:
    result = data["missing"]
    result = "this line skipped"
except KeyError:
    pass
# Same behaviour — result stays "default"
```

---

## `contextlib.nullcontext()` — No-Op Context Manager

A context manager that does absolutely nothing. Its value is enabling conditional `with` statements without code duplication.

```python
from contextlib import nullcontext

# The problem it solves
def process(data, lock=None):
    # Without nullcontext — duplicated code
    if lock is not None:
        with lock:
            do_the_work(data)
    else:
        do_the_work(data)    # same code, twice

# With nullcontext — clean
def process(data, lock=None):
    ctx = lock if lock is not None else nullcontext()
    with ctx:
        do_the_work(data)    # one code path, works with or without lock
```

### `nullcontext` with a Return Value

```python
from contextlib import nullcontext

# nullcontext(enter_result=value) — the 'as' target
with nullcontext(42) as val:
    print(val)    # 42

with nullcontext("no-op") as val:
    print(val)    # no-op
```

### Real-World Use Cases

**Optional tracing/profiling:**

```python
from contextlib import nullcontext
import cProfile

def run(profile: bool = False):
    ctx = cProfile.Profile() if profile else nullcontext()
    with ctx:
        heavy_computation()

    if profile and isinstance(ctx, cProfile.Profile):
        ctx.print_stats()
```

**Optional file writing:**

```python
from contextlib import nullcontext
from pathlib import Path

def export(data: list, output_path: str | None = None):
    # If output_path given, write to file. Otherwise, just process.
    ctx = open(output_path, "w", encoding="utf-8") if output_path else nullcontext()

    with ctx as f:
        for item in data:
            result = process(item)
            if f is not None:
                f.write(f"{result}\n")
```

**Optional transaction:**

```python
from contextlib import nullcontext

def save_records(records: list, use_transaction: bool = True):
    ctx = db.transaction() if use_transaction else nullcontext()

    with ctx:
        for record in records:
            db.insert(record)
```

**Testing — optionally mock something:**

```python
from contextlib import nullcontext
from unittest.mock import patch

def test_with_option(mock_network: bool = True):
    ctx = patch("requests.get", return_value=fake_response) if mock_network \
          else nullcontext()

    with ctx:
        result = call_api()
        assert result["status"] == "ok"
```

---

## `contextlib.asynccontextmanager` — Async Context Managers

The async equivalent of `@contextmanager`. Everything before `yield` = `__aenter__`, everything after = `__aexit__`.

```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def async_managed(name: str):
    print(f"async setup: {name}")
    await setup_coroutine()
    try:
        yield "async resource"
    finally:
        await teardown_coroutine()
        print(f"async teardown: {name}")

async def main():
    async with async_managed("connection") as resource:
        print(f"using: {resource}")
        await do_work(resource)

import asyncio
asyncio.run(main())
```

### FastAPI Lifespan Events

This is the primary real-world use in FastAPI — replacing the old `@app.on_event("startup")` decorator.

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI

@asynccontextmanager
async def lifespan(app: FastAPI):
    # ── STARTUP ─────────────────────────────────────────────────────
    print("Starting up...")
    await database.connect()
    await cache.connect()
    redis_pool = await create_redis_pool()
    app.state.redis = redis_pool
    print("Ready")

    yield   # application is running here

    # ── SHUTDOWN ─────────────────────────────────────────────────────
    print("Shutting down...")
    await database.disconnect()
    await cache.disconnect()
    await redis_pool.close()
    print("Shutdown complete")

app = FastAPI(lifespan=lifespan)

@app.get("/health")
async def health():
    return {"status": "ok"}
```

### Async Database Session

```python
from contextlib import asynccontextmanager
from sqlalchemy.ext.asyncio import AsyncSession, async_sessionmaker

@asynccontextmanager
async def get_session(session_factory: async_sessionmaker):
    async with session_factory() as session:
        async with session.begin():
            try:
                yield session
                await session.commit()
            except Exception:
                await session.rollback()
                raise

# Usage
async def create_user(name: str, email: str):
    async with get_session(SessionLocal) as session:
        user = User(name=name, email=email)
        session.add(user)
        # commits on exit, rollbacks on exception
```

### Async HTTP Client Session

```python
from contextlib import asynccontextmanager
import httpx

@asynccontextmanager
async def http_client(base_url: str, **kwargs):
    async with httpx.AsyncClient(base_url=base_url, **kwargs) as client:
        yield client

async def fetch_users():
    async with http_client("https://api.example.com", timeout=10.0) as client:
        response = await client.get("/users")
        response.raise_for_status()
        return response.json()
```

### Async Timer

```python
from contextlib import asynccontextmanager
import asyncio
import time

@asynccontextmanager
async def async_timer(label: str = ""):
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start
        print(f"[{label or 'timer'}] {elapsed:.4f}s")

async def main():
    async with async_timer("fetch"):
        await asyncio.sleep(0.1)
        data = await fetch_data()
```

---

## Other `contextlib` Utilities

### `contextlib.ExitStack` — Dynamic Context Managers

When you don't know at compile time how many context managers you need.

```python
from contextlib import ExitStack
from pathlib import Path

filenames = ["a.txt", "b.txt", "c.txt"]

# Open a dynamic number of files — all closed on exit
with ExitStack() as stack:
    files = [
        stack.enter_context(open(f, encoding="utf-8"))
        for f in filenames
    ]
    for f in files:
        print(f.read())
# all files closed in LIFO order

# Conditional context managers
def process(data, use_lock: bool, use_transaction: bool):
    with ExitStack() as stack:
        if use_lock:
            stack.enter_context(threading.Lock())
        if use_transaction:
            stack.enter_context(db.transaction())
        do_work(data)

# Register cleanup callbacks
with ExitStack() as stack:
    tmp_file = stack.callback(cleanup_temp_file, "temp.bin")
    conn = stack.enter_context(database.connect())
    # If anything fails: cleanup_temp_file is called, conn is closed
```

### `contextlib.AsyncExitStack` — Async Version

```python
from contextlib import AsyncExitStack
import httpx

async def fetch_multiple(urls: list[str]):
    async with AsyncExitStack() as stack:
        clients = [
            await stack.enter_async_context(
                httpx.AsyncClient(base_url=url)
            )
            for url in urls
        ]
        # all clients available here
        responses = await asyncio.gather(*[
            client.get("/") for client in clients
        ])
        return responses
    # all clients closed
```

### `contextlib.closing()` — Ensure `.close()` Is Called

```python
from contextlib import closing
import urllib.request

# For objects that have .close() but aren't context managers
with closing(urllib.request.urlopen("https://example.com")) as response:
    data = response.read()
# response.close() called automatically

# Equivalent to:
response = urllib.request.urlopen("https://example.com")
try:
    data = response.read()
finally:
    response.close()
```

### `contextlib.redirect_stdout` and `redirect_stderr`

```python
from contextlib import redirect_stdout, redirect_stderr
from io import StringIO

# Capture stdout from code you can't modify
buf = StringIO()
with redirect_stdout(buf):
    print("captured")
    help(len)   # captures help() output

output = buf.getvalue()
print(f"captured {len(output)} characters")

# Suppress stderr
with redirect_stderr(StringIO()):
    noisy_function()   # stderr output silenced
```

---

## Putting It All Together

```python
from contextlib import (
    contextmanager, asynccontextmanager,
    suppress, nullcontext, ExitStack
)
import asyncio
import time
import os
import sqlite3
from pathlib import Path
import shutil
import tempfile

# ── 1. Synchronous context managers ──────────────────────────────────────────

@contextmanager
def timer(label: str = "", min_warn_ms: float = 0):
    """Time a block. Warn if it exceeds min_warn_ms."""
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed_ms = (time.perf_counter() - start) * 1000
        msg = f"[{label}] {elapsed_ms:.2f}ms"
        if min_warn_ms and elapsed_ms > min_warn_ms:
            print(f"⚠️  SLOW: {msg}")
        else:
            print(msg)

@contextmanager
def temp_workspace(prefix: str = "work_"):
    """Temporary directory — cleaned up on exit."""
    path = Path(tempfile.mkdtemp(prefix=prefix))
    try:
        yield path
    finally:
        with suppress(PermissionError, FileNotFoundError):
            shutil.rmtree(path)

@contextmanager
def sqlite_transaction(db_path: str):
    """SQLite connection with auto commit/rollback."""
    conn = sqlite3.connect(db_path)
    conn.row_factory = sqlite3.Row
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback()
        raise
    finally:
        conn.close()

# ── 2. Async context managers ─────────────────────────────────────────────────

@asynccontextmanager
async def async_timer(label: str = ""):
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start
        print(f"[async:{label}] {elapsed:.4f}s")

@asynccontextmanager
async def managed_resources(*resource_names: str):
    """Simulate acquiring multiple async resources."""
    acquired = []
    try:
        for name in resource_names:
            await asyncio.sleep(0.01)   # simulate async acquisition
            acquired.append(name)
            print(f"acquired: {name}")
        yield acquired
    finally:
        for name in reversed(acquired):   # LIFO release
            await asyncio.sleep(0.005)
            print(f"released: {name}")

# ── 3. Putting it together ────────────────────────────────────────────────────

def sync_demo():
    print("=== Sync Demo ===")

    # Timer
    with timer("list sort", min_warn_ms=50):
        data = sorted(range(100_000), reverse=True)

    # Temp workspace
    with temp_workspace("demo_") as tmp:
        print(f"Working in: {tmp}")
        (tmp / "data.txt").write_text("hello from temp")
        print((tmp / "data.txt").read_text())

    with suppress(FileNotFoundError):
        print(tmp)   # directory is gone
        print(tmp.exists())   # False

    # Optional context manager
    use_lock = False
    ctx = __import__("threading").Lock() if use_lock else nullcontext()
    with ctx:
        result = sum(range(10_000))

    print(f"Result: {result}")

    # ExitStack with dynamic contexts
    labels = ["A", "B", "C"]
    with ExitStack() as stack:
        timers = [stack.enter_context(timer(f"block_{l}")) for l in labels]
        for i, _ in enumerate(timers):
            time.sleep(0.01 * (i + 1))

async def async_demo():
    print("\n=== Async Demo ===")

    async with async_timer("full_demo"):
        async with managed_resources("db", "cache", "queue") as resources:
            print(f"Got resources: {resources}")
            await asyncio.sleep(0.05)
            print("Work done")

if __name__ == "__main__":
    sync_demo()
    asyncio.run(async_demo())
```

---

## Key Takeaways

- **`@contextmanager`** — one `yield` divides setup (before) from teardown (after). Everything after `yield` runs in a `finally`-like context — always executes even if the `with` block raises. Use `try/finally` around the `yield` to guarantee cleanup.
- **Exception handling in `@contextmanager`**: exceptions from the `with` block are re-raised at the `yield`. Catch and re-raise to clean up without suppressing. Catch and don't re-raise to suppress. Always put cleanup in `finally`.
- **`suppress(*exceptions)`** — single-purpose: silently ignore specific exceptions. Use when absence is expected and there's nothing useful to do about it. Don't use when you need logging, fallback values, or different handling per exception type.
- **`nullcontext(enter_result=None)`** — returns a do-nothing context manager. Its entire value is enabling `ctx = real_ctx if condition else nullcontext()` to unify code paths that may or may not need a context manager.
- **`@asynccontextmanager`** — identical to `@contextmanager` but for `async with`. `yield` must be inside an `async def`. This is the correct way to write FastAPI `lifespan` handlers — startup before `yield`, shutdown after.
- **`ExitStack`** — dynamically enter multiple context managers and ensure all are exited. Use when the number or type of context managers is determined at runtime. `stack.enter_context()` for sync, `stack.enter_async_context()` for async in `AsyncExitStack`.
- **`closing(obj)`** — wraps objects that have `.close()` but aren't context managers. Rarely needed with modern libraries but useful for legacy code.
- **`redirect_stdout/stderr`** — capture or suppress output from code you can't modify. Useful in tests.