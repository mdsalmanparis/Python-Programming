Let's go. Full topic, nothing skipped.

---

## 2.6 `logging`

---

## Why `print()` Is Not Enough

```python
# print() — no level, no timestamp, no location, no routing
print("user logged in")
print("ERROR: database failed")   # looks the same as info

# logging — structured, filterable, routable
import logging
logger = logging.getLogger(__name__)
logger.info("user logged in")
logger.error("database failed")
# 2024-11-15 14:30:45,123 ERROR myapp.db — database failed
```

**What logging gives you that print doesn't:**
- Severity levels — filter out debug noise in production
- Timestamps — when did this happen
- Source — which module/line produced it
- Routing — send errors to a file, debug to console, criticals to Slack
- On/off at runtime — change log level without code changes

---

## Log Levels

Five built-in levels, in increasing severity:

```python
import logging

logging.basicConfig(level=logging.DEBUG)

logging.debug("detailed internal state — for developers only")    # 10
logging.info("normal operation — server started, user logged in") # 20
logging.warning("unexpected but recoverable — disk 80% full")     # 30
logging.error("failure — request failed, DB query failed")        # 40
logging.critical("system-level failure — DB down, OOM")           # 50
```

**Level filtering — only messages AT OR ABOVE the set level are processed:**

```python
import logging

logging.basicConfig(level=logging.WARNING)

logging.debug("this is ignored")    # below WARNING — not shown
logging.info("this is ignored")     # below WARNING — not shown
logging.warning("this appears")     # at WARNING — shown
logging.error("this appears")       # above WARNING — shown
logging.critical("this appears")    # above WARNING — shown
```

**Level values — useful for comparison:**

```python
logging.DEBUG     # 10
logging.INFO      # 20
logging.WARNING   # 30
logging.ERROR     # 40
logging.CRITICAL  # 50
logging.NOTSET    # 0  — inherits from parent logger
```

**Choosing the right level:**

```python
# DEBUG — internal state, loop iterations, variable values
logger.debug(f"Processing item {i}/{total}: {item!r}")
logger.debug(f"Cache miss for key: {cache_key}")

# INFO — significant normal events
logger.info(f"Server started on port {port}")
logger.info(f"User {user_id} logged in")
logger.info(f"Job {job_id} completed in {elapsed:.2f}s")

# WARNING — unexpected but handled, something to watch
logger.warning(f"Retry {attempt}/3 for {url}")
logger.warning(f"Deprecated config key '{key}' — use '{new_key}'")
logger.warning(f"Response time {ms}ms exceeds threshold {threshold}ms")

# ERROR — operation failed, request failed, expected to be investigated
logger.error(f"Failed to send email to {recipient}: {e}")
logger.error(f"DB query failed: {query}")

# CRITICAL — system-level, needs immediate attention
logger.critical("Database connection pool exhausted")
logger.critical(f"Disk space critically low: {free_gb:.1f}GB remaining")
```

---

## `logging.getLogger(__name__)` — Logger Hierarchy

The most important line in any Python module:

```python
import logging
logger = logging.getLogger(__name__)
```

**Why `__name__`?**

```python
# In module myapp/database.py:
__name__ == "myapp.database"     # Python sets this automatically
logger = logging.getLogger("myapp.database")  # creates a named logger

# In myapp/server.py:
__name__ == "myapp.server"
logger = logging.getLogger("myapp.server")
```

### Logger Hierarchy — the Dot Notation

Loggers form a tree based on the dot-separated name.

```
root logger ("")
└── myapp
    ├── myapp.server
    ├── myapp.database
    │   ├── myapp.database.connection
    │   └── myapp.database.query
    └── myapp.auth
```

**Hierarchy rules:**
- `logging.getLogger("myapp.database")` is a child of `logging.getLogger("myapp")`
- Child loggers inherit their parent's level if they have no level set
- Log records propagate UP the hierarchy to parent handlers

```python
import logging

# Root logger
root = logging.getLogger()
root.setLevel(logging.WARNING)

# Child logger — inherits level from parent
child = logging.getLogger("myapp")
print(child.level)           # 0 (NOTSET) — will inherit from root

# Effective level accounts for inheritance
print(child.getEffectiveLevel())   # 30 (WARNING) — inherited from root

# Setting a level on child overrides inheritance
child.setLevel(logging.DEBUG)
print(child.getEffectiveLevel())   # 10 (DEBUG) — now uses own level
```

### Root Logger vs Named Loggers

```python
import logging

# Root logger — the global catch-all
root_logger = logging.getLogger()         # no name = root
root_logger = logging.getLogger("")       # same thing

# Named loggers — always use these in your code
logger = logging.getLogger(__name__)      # best practice
logger = logging.getLogger("myapp.db")   # explicit name

# Module-level loggers — one per module
# database.py:
logger = logging.getLogger("myapp.database")

# auth.py:
logger = logging.getLogger("myapp.auth")

# Both can be controlled from the parent:
myapp_logger = logging.getLogger("myapp")
myapp_logger.setLevel(logging.DEBUG)    # affects myapp.database AND myapp.auth
```

---

## `basicConfig()` — Quick Setup

For scripts and simple applications. Call ONCE before any logging.

```python
import logging

# Minimal — WARNING to stderr
logging.basicConfig()

# With level
logging.basicConfig(level=logging.DEBUG)

# With format and level
logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s %(levelname)s %(name)s — %(message)s",
)

# To a file
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s — %(message)s",
    filename="app.log",
    filemode="a",       # "a" = append (default), "w" = overwrite
    encoding="utf-8",
)

# `basicConfig()` only works on the ROOT logger
# It does nothing if the root logger already has handlers
# Call it ONCE at the start of your program (in __main__)
```

**`basicConfig()` is only for simple scripts — use `dictConfig()` for real apps (shown later).**

---

## Format Strings — LogRecord Attributes

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s %(levelname)-8s %(name)s:%(lineno)d — %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S",
)
```

**All available format attributes:**

```python
%(asctime)s      # 2024-11-15 14:30:45,123  — human-readable timestamp
%(created)f      # 1731676245.123456         — Unix timestamp
%(filename)s     # database.py               — source filename
%(funcName)s     # connect                   — function name
%(levelname)s    # ERROR                     — level name
%(levelno)d      # 40                        — level number
%(lineno)d       # 42                        — line number in source
%(message)s      # the actual message
%(module)s       # database                  — module name (filename without .py)
%(name)s         # myapp.database            — logger name
%(pathname)s     # /home/user/myapp/database.py — full path
%(process)d      # 12345                     — process ID
%(processName)s  # MainProcess               — process name
%(thread)d       # 139825...                 — thread ID
%(threadName)s   # MainThread                — thread name

# Width/padding formatting — standard Python format mini-language
%(levelname)-8s   # left-aligned, width 8
%(lineno)4d       # right-aligned, width 4
```

---

## Handlers — Where Logs Go

A handler sends log records to a destination.

### `StreamHandler` — Console Output

```python
import logging
import sys

logger = logging.getLogger("myapp")
logger.setLevel(logging.DEBUG)

# StreamHandler — writes to a stream (stderr by default)
console_handler = logging.StreamHandler()              # stderr
console_handler = logging.StreamHandler(sys.stdout)   # stdout

# Set level on handler — handler only processes records at or above this level
console_handler.setLevel(logging.DEBUG)

# Set formatter
formatter = logging.Formatter(
    fmt="%(asctime)s %(levelname)-8s %(name)s — %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S"
)
console_handler.setFormatter(formatter)

# Add handler to logger
logger.addHandler(console_handler)

logger.debug("debug message")
logger.info("info message")
logger.error("error message")
```

### `FileHandler` — Write to File

```python
import logging

file_handler = logging.FileHandler(
    filename="app.log",
    mode="a",           # append
    encoding="utf-8",
)
file_handler.setLevel(logging.INFO)
file_handler.setFormatter(logging.Formatter(
    "%(asctime)s %(levelname)s %(name)s — %(message)s"
))

logger.addHandler(file_handler)
```

### `RotatingFileHandler` — Size-Based Rotation

```python
from logging.handlers import RotatingFileHandler

# Rotate when file reaches maxBytes, keep backupCount old files
rotating_handler = RotatingFileHandler(
    filename="app.log",
    maxBytes=10 * 1024 * 1024,   # 10 MB
    backupCount=5,                # keep app.log.1 through app.log.5
    encoding="utf-8",
)
rotating_handler.setLevel(logging.DEBUG)
rotating_handler.setFormatter(logging.Formatter(
    "%(asctime)s %(levelname)-8s %(name)s:%(lineno)d — %(message)s"
))

logger.addHandler(rotating_handler)
# Files: app.log, app.log.1, app.log.2, app.log.3, app.log.4, app.log.5
# When app.log fills up, it becomes app.log.1, old .5 is deleted
```

### `TimedRotatingFileHandler` — Time-Based Rotation

```python
from logging.handlers import TimedRotatingFileHandler

# Rotate daily, keep 30 days of logs
timed_handler = TimedRotatingFileHandler(
    filename="app.log",
    when="midnight",      # "S"=seconds, "M"=minutes, "H"=hours, "D"=days, "midnight"
    interval=1,           # every 1 day
    backupCount=30,       # keep 30 rotated files
    encoding="utf-8",
    utc=True,             # use UTC for rotation time
)
# Files: app.log, app.log.2024-11-15, app.log.2024-11-14, ...
```

### `NullHandler` — For Libraries

```python
import logging

# In a library — add NullHandler, let the APPLICATION configure logging
# Never add StreamHandler or FileHandler in a library
logging.getLogger("mylibrary").addHandler(logging.NullHandler())

# This means: if the application doesn't configure logging,
# our library's log messages go nowhere (silence, no errors)
# If the application does configure logging, our messages flow through
```

### Multiple Handlers — Routing by Level

```python
import logging
import sys
from logging.handlers import RotatingFileHandler

def setup_logging(log_level=logging.DEBUG, log_file="app.log"):
    logger = logging.getLogger("myapp")
    logger.setLevel(logging.DEBUG)   # capture everything, handlers filter

    fmt = logging.Formatter(
        "%(asctime)s %(levelname)-8s %(name)s:%(lineno)d — %(message)s",
        datefmt="%Y-%m-%d %H:%M:%S"
    )

    # Console — INFO and above
    console = logging.StreamHandler(sys.stdout)
    console.setLevel(logging.INFO)
    console.setFormatter(fmt)
    logger.addHandler(console)

    # File — DEBUG and above (everything)
    file_h = RotatingFileHandler(log_file, maxBytes=10_000_000, backupCount=3)
    file_h.setLevel(logging.DEBUG)
    file_h.setFormatter(fmt)
    logger.addHandler(file_h)

    # Error file — ERROR and above only
    error_h = RotatingFileHandler("errors.log", maxBytes=5_000_000, backupCount=5)
    error_h.setLevel(logging.ERROR)
    error_h.setFormatter(fmt)
    logger.addHandler(error_h)

    return logger
```

---

## `logging.config.dictConfig()` — Production Configuration

The right way to configure logging in real applications.

```python
import logging
import logging.config

LOGGING_CONFIG = {
    "version": 1,                      # required, must be 1
    "disable_existing_loggers": False,  # keep loggers created before this call

    "formatters": {
        "standard": {
            "format": "%(asctime)s %(levelname)-8s %(name)s:%(lineno)d — %(message)s",
            "datefmt": "%Y-%m-%d %H:%M:%S",
        },
        "detailed": {
            "format": (
                "%(asctime)s %(levelname)-8s %(name)s:%(lineno)d "
                "[%(process)d:%(thread)d] — %(message)s"
            ),
        },
        "json": {                       # for log aggregators like Datadog, Loki
            "()": "pythonjsonlogger.jsonlogger.JsonFormatter",  # third-party
            "format": "%(asctime)s %(levelname)s %(name)s %(message)s",
        },
    },

    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "level": "INFO",
            "formatter": "standard",
            "stream": "ext://sys.stdout",
        },
        "file": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "DEBUG",
            "formatter": "detailed",
            "filename": "app.log",
            "maxBytes": 10_485_760,   # 10 MB
            "backupCount": 5,
            "encoding": "utf-8",
        },
        "error_file": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "ERROR",
            "formatter": "detailed",
            "filename": "errors.log",
            "maxBytes": 10_485_760,
            "backupCount": 10,
            "encoding": "utf-8",
        },
        "null": {
            "class": "logging.NullHandler",
        },
    },

    "loggers": {
        "myapp": {
            "level": "DEBUG",
            "handlers": ["console", "file", "error_file"],
            "propagate": False,       # don't send to root logger
        },
        "myapp.database": {
            "level": "DEBUG",
            "handlers": [],           # inherits from myapp
            "propagate": True,        # yes, propagate to myapp
        },
        "uvicorn": {
            "level": "INFO",
            "handlers": ["console"],
            "propagate": False,
        },
        "sqlalchemy.engine": {
            "level": "WARNING",       # suppress SQL echo in production
            "handlers": ["file"],
            "propagate": False,
        },
    },

    "root": {
        "level": "WARNING",           # catch-all for unrecognized loggers
        "handlers": ["console"],
    },
}

logging.config.dictConfig(LOGGING_CONFIG)
logger = logging.getLogger("myapp")
logger.info("Logging configured")
```

### Loading Config from YAML

```python
import logging.config
import yaml

with open("logging.yaml", "r") as f:
    config = yaml.safe_load(f)

logging.config.dictConfig(config)
```

---

## Logger Propagation — Preventing Duplicate Log Entries

```python
import logging

# Problem: duplicate log entries
parent = logging.getLogger("myapp")
parent.setLevel(logging.DEBUG)
parent.addHandler(logging.StreamHandler())

child = logging.getLogger("myapp.database")
child.addHandler(logging.StreamHandler())   # child also has a handler

# By default propagate=True — message goes to BOTH child's handler AND parent's handler
child.info("query executed")
# Prints twice:
# INFO myapp.database — query executed   ← from child's handler
# INFO myapp.database — query executed   ← propagated to parent's handler

# Fix 1 — disable propagation on child
child.propagate = False    # message handled only by child's own handlers

# Fix 2 — don't add handler to child, let it propagate to parent only
child_no_handler = logging.getLogger("myapp.auth")
# child_no_handler has no handlers, propagate=True (default)
# message propagates to parent's handlers only — correct
child_no_handler.info("user authenticated")
# Prints once — handled by parent's handler
```

**The pattern:**
- Root or top-level app logger has handlers
- All child loggers have `propagate=True` (default) and NO handlers
- They propagate records up to the logger that has handlers

```python
# Correct pattern
logging.config.dictConfig({
    "version": 1,
    "handlers": {
        "console": {"class": "logging.StreamHandler", "level": "DEBUG"}
    },
    "loggers": {
        "myapp": {
            "level": "DEBUG",
            "handlers": ["console"],
            "propagate": False,    # stop here, don't go to root
        },
        # myapp.database, myapp.auth, etc. have NO handlers listed
        # they propagate to myapp automatically
    },
    "root": {"level": "WARNING", "handlers": ["console"]},
})
```

---

## Library vs Application Logging

### Library — Only `NullHandler`

```python
# mylibrary/__init__.py or any library module

import logging

# THIS IS ALL A LIBRARY SHOULD DO
logging.getLogger(__name__).addHandler(logging.NullHandler())

# Then in the library's modules:
logger = logging.getLogger(__name__)
logger.debug("library doing something")
logger.warning("library encountered an issue")

# The application that uses this library decides
# where those messages go (or whether they appear at all)
# The library NEVER adds StreamHandler, FileHandler, basicConfig, etc.
```

**Why:**
```python
# If a library adds StreamHandler:
# - ALL programs that import it get log output they didn't ask for
# - They can't easily suppress it
# - Multiple libraries adding handlers = chaos

# If a library uses only NullHandler:
# - Silence by default — no output unless app configures it
# - App can route library's logs anywhere or nowhere
# - App can set level: logging.getLogger("requests").setLevel(logging.WARNING)
```

### Application — Full Configuration

```python
# myapp/__main__.py or main entry point

import logging
import logging.config

# Application sets up all logging configuration
logging.config.dictConfig(LOGGING_CONFIG)

# Then import and use the app — all loggers (including libraries) flow through here
from myapp.server import Server
server = Server()
server.run()
```

---

## `logger.exception()` and `exc_info=True` — Logging Exceptions with Traceback

```python
import logging

logger = logging.getLogger(__name__)

# Method 1 — logger.exception() — always logs at ERROR level with full traceback
try:
    result = 10 / 0
except ZeroDivisionError:
    logger.exception("division failed")
    # Output:
    # ERROR myapp — division failed
    # Traceback (most recent call last):
    #   File "...", line 2, in ...
    #     result = 10 / 0
    # ZeroDivisionError: division by zero

# Method 2 — exc_info=True — adds traceback to any level
try:
    result = int("bad")
except ValueError:
    logger.error("conversion failed", exc_info=True)    # same as exception()
    logger.warning("conversion issue", exc_info=True)   # warning with traceback
    logger.debug("debug: conversion failed", exc_info=True)

# Method 3 — exc_info=(exc_type, exc_val, exc_tb)
import sys
try:
    risky()
except Exception:
    logger.error("failed", exc_info=sys.exc_info())

# logger.exception() vs logger.error(..., exc_info=True)
# — they are functionally identical
# — logger.exception() is shorthand, always ERROR level
# — exc_info=True is explicit and works at any level

# WRONG — logging exception OUTSIDE the except block
try:
    result = 1 / 0
except ZeroDivisionError:
    pass

logger.exception("this is wrong")  # no active exception — logs NoneType: None
```

---

## `extra` — Adding Context to Log Records

```python
import logging

logger = logging.getLogger(__name__)

# Add extra fields to a single log call
logger.info("user action", extra={
    "user_id":    "usr_123",
    "request_id": "req_abc",
    "ip":         "192.168.1.1",
})

# Format must include the extra fields to show them
formatter = logging.Formatter(
    "%(asctime)s %(levelname)s [%(request_id)s] %(name)s — %(message)s"
)
```

### `LoggerAdapter` — Persistent Extra Context

```python
import logging

logger = logging.getLogger(__name__)

class RequestLogger:
    """Logger that automatically adds request context."""

    def __init__(self, request_id: str, user_id: str | None = None):
        self._logger = logging.LoggerAdapter(
            logger,
            extra={"request_id": request_id, "user_id": user_id or "anonymous"}
        )

    def info(self, msg, *args, **kwargs):
        self._logger.info(msg, *args, **kwargs)

    def error(self, msg, *args, **kwargs):
        self._logger.error(msg, *args, **kwargs)

    def exception(self, msg, *args, **kwargs):
        self._logger.exception(msg, *args, **kwargs)

# Usage — all messages from this logger include request_id
def handle_request(request_id: str):
    log = RequestLogger(request_id)
    log.info("request received")
    log.info("processing")
    log.info("request complete")
```

### `contextvars` — Per-Request Context in Async Code

```python
import logging
import contextvars

# Store request ID in context variable (thread and coroutine safe)
request_id_var: contextvars.ContextVar[str] = contextvars.ContextVar(
    "request_id", default="no-request"
)

class ContextFilter(logging.Filter):
    """Add request_id from contextvar to every log record."""

    def filter(self, record: logging.LogRecord) -> bool:
        record.request_id = request_id_var.get()
        return True

# Add filter to handler
handler = logging.StreamHandler()
handler.addFilter(ContextFilter())
handler.setFormatter(logging.Formatter(
    "%(asctime)s %(levelname)s [%(request_id)s] %(name)s — %(message)s"
))

# In FastAPI middleware:
# async def middleware(request, call_next):
#     token = request_id_var.set(str(uuid4()))
#     try:
#         return await call_next(request)
#     finally:
#         request_id_var.reset(token)
```

---

## Putting It All Together

```python
import logging
import logging.config
import sys
from logging.handlers import RotatingFileHandler
from pathlib import Path

# ── 1. Configuration ──────────────────────────────────────────────────────────

LOG_DIR = Path("logs")
LOG_DIR.mkdir(exist_ok=True)

LOGGING_CONFIG = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "standard": {
            "format": "%(asctime)s %(levelname)-8s %(name)s:%(lineno)d — %(message)s",
            "datefmt": "%Y-%m-%d %H:%M:%S",
        },
        "minimal": {
            "format": "%(levelname)-8s — %(message)s",
        },
    },
    "filters": {},
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "level": "INFO",
            "formatter": "standard",
            "stream": "ext://sys.stdout",
        },
        "app_file": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "DEBUG",
            "formatter": "standard",
            "filename": str(LOG_DIR / "app.log"),
            "maxBytes": 10_485_760,
            "backupCount": 5,
            "encoding": "utf-8",
        },
        "error_file": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "ERROR",
            "formatter": "standard",
            "filename": str(LOG_DIR / "errors.log"),
            "maxBytes": 5_242_880,
            "backupCount": 10,
            "encoding": "utf-8",
        },
        "null": {
            "class": "logging.NullHandler",
        },
    },
    "loggers": {
        "myapp": {
            "level": "DEBUG",
            "handlers": ["console", "app_file", "error_file"],
            "propagate": False,
        },
        # Silence noisy third-party loggers
        "urllib3": {"level": "WARNING", "handlers": ["null"], "propagate": False},
        "httpx":   {"level": "WARNING", "handlers": ["null"], "propagate": False},
    },
    "root": {
        "level": "WARNING",
        "handlers": ["console"],
    },
}

logging.config.dictConfig(LOGGING_CONFIG)

# ── 2. Module-level loggers ───────────────────────────────────────────────────

logger = logging.getLogger("myapp.demo")

# ── 3. Usage patterns ─────────────────────────────────────────────────────────

def connect_database(host: str, port: int) -> bool:
    logger.debug(f"Attempting connection to {host}:{port}")
    try:
        # simulate connection
        if port == 0:
            raise ConnectionError(f"refused by {host}:{port}")
        logger.info(f"Connected to database at {host}:{port}")
        return True
    except ConnectionError:
        logger.exception(f"Failed to connect to {host}:{port}")
        return False

def process_records(records: list) -> dict:
    logger.info(f"Processing {len(records)} records")
    results = {"success": 0, "failed": 0, "skipped": 0}

    for i, record in enumerate(records):
        logger.debug(f"Processing record {i+1}/{len(records)}: {record!r}")
        try:
            if record.get("skip"):
                logger.warning(f"Skipping record {record.get('id')}: flagged for skip")
                results["skipped"] += 1
                continue

            # simulate processing
            if record.get("bad"):
                raise ValueError(f"Invalid data in record {record.get('id')}")

            results["success"] += 1
        except ValueError:
            logger.exception(f"Failed to process record {record.get('id')}")
            results["failed"] += 1

    logger.info(
        f"Completed: {results['success']} ok, "
        f"{results['failed']} failed, "
        f"{results['skipped']} skipped"
    )
    return results

# ── 4. Demo ───────────────────────────────────────────────────────────────────

if __name__ == "__main__":
    logger.info("Application starting")

    connect_database("localhost", 5432)    # success
    connect_database("localhost", 0)       # failure with traceback in logs

    records = [
        {"id": 1, "data": "good"},
        {"id": 2, "skip": True},
        {"id": 3, "bad": True},
        {"id": 4, "data": "also good"},
    ]
    summary = process_records(records)
    logger.info(f"Final summary: {summary}")
    logger.info("Application shutting down")
```

---

## Key Takeaways

- Use `logger = logging.getLogger(__name__)` in every module. The `__name__` creates a named logger that fits into the hierarchy automatically.
- Loggers form a dot-separated hierarchy. Child loggers inherit parent's level if `NOTSET`. Records propagate UP to parent handlers by default.
- `basicConfig()` only works once and only affects the root logger. Use it for scripts. Use `dictConfig()` for applications.
- Logger level vs handler level: the logger filters first, then each handler filters. Set logger to `DEBUG` and control verbosity per handler.
- `propagate=True` (default) sends records up the hierarchy. Set `propagate=False` on your top-level app logger to stop records reaching the root logger and avoid duplicates.
- **Libraries**: only `logging.getLogger(__name__).addHandler(logging.NullHandler())`. Never `basicConfig`, never `StreamHandler`. Let the application control output.
- **Applications**: configure once at startup with `dictConfig()`. All loggers across all modules and libraries are controlled from one place.
- `logger.exception("msg")` = `logger.error("msg", exc_info=True)` — both log at ERROR with the current exception's full traceback. Only call inside an `except` block.
- `RotatingFileHandler(maxBytes=10MB, backupCount=5)` — size-based rotation. `TimedRotatingFileHandler(when="midnight", backupCount=30)` — time-based.
- Use `extra={}` to attach context to individual log calls. Use `LoggerAdapter` for context that applies to all calls from a component. Use `contextvars` + `Filter` for per-request context in async apps.