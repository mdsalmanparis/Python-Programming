# 0.12 Modules & Imports

## What Is a Module

A module is any `.py` file. That's it. Every Python file you write is already a module. Python's entire standard library is a collection of modules — `math.py`, `os.py`, `random.py` and hundreds more — that ship with Python.

```
your_project/
    main.py          ← a module
    utils.py         ← a module
    database.py      ← a module
```

Modules let you split code across files instead of writing everything in one giant script. You write a function once in one file and import it anywhere.

---

## import module — The Basic Form

Imports the entire module. Access everything through the module name.

```python
import math

print(math.sqrt(16))      # 4.0
print(math.pi)            # 3.141592653589793
print(math.floor(3.7))    # 3
print(math.ceil(3.2))     # 4

# The module name is required every time — that's the point
# math.sqrt, math.pi, math.floor — always prefixed
```

### Why the Prefix Matters — No Name Collisions

```python
import math
import statistics

# Both have a mean concept but different implementations
# Without prefix you'd have no idea which is which
print(math.floor(3.7))          # 3
print(statistics.mean([1,2,3])) # 2
```

### Multiple Imports

```python
import math
import random
import os
import sys

# Convention — one import per line, grouped:
# 1. standard library
# 2. third-party (numpy, requests, etc.)
# 3. your own modules
# with a blank line between each group
```

---

## from module import name — Import Specific Names

Imports only what you need. Use the name directly without the prefix.

```python
from math import sqrt

print(sqrt(16))    # 4.0 — no math. prefix needed
print(pi)          # NameError — pi wasn't imported

# Import multiple names
from math import sqrt, pi, floor, ceil

print(sqrt(25))    # 5.0
print(pi)          # 3.141592653589793
print(floor(3.9))  # 3
print(ceil(3.1))   # 4
```

### When This Is the Right Choice

```python
# Deeply nested module — prefix would be verbose
from os.path import join, exists, dirname

# Instead of:
import os
os.path.join("home", "user")         # verbose

# You write:
join("home", "user")                 # clean
```

### When This Causes Problems — Name Shadowing

```python
from math import floor

def floor(x):             # you accidentally redefine floor
    return int(x)

floor(3.9)   # calls YOUR function, not math.floor
             # the import was silently overwritten
```

With `import math`, you'd always call `math.floor()` — no ambiguity. `from module import name` is convenient but you take on the responsibility of not reusing that name.

---

## from module import * — Import Everything

Imports all public names from the module into your current namespace.

```python
from math import *

print(sqrt(16))    # 4.0
print(pi)          # 3.141592653589793
print(floor(3.7))  # 3
print(e)           # 2.718...
```

### Why to Avoid It in Real Code

```python
from math import *
from statistics import *

# Both modules have functions — which 'mean' is this?
# Did statistics overwrite something from math?
# You have no idea without reading both modules' source
mean([1, 2, 3])      # ambiguous

# A new version of a library adds a function with the same name as yours
# Your function silently gets overwritten — hard to debug
```

### The Only Acceptable Use — Interactive Python Shell for Quick Exploration

```python
# In REPL only — acceptable
>>> from math import *
>>> sqrt(16)
4.0
```

Never in `.py` files that other people will read or maintain.

---

## import module as alias — Renaming on Import

Give the module a shorter or more convenient name.

```python
import math as m

print(m.sqrt(16))    # 4.0
print(m.pi)          # 3.141592653589793
```

### Standard Aliases Everyone Uses

These are conventions, not rules, but follow them:

```python
import numpy as np           # universal — everyone uses np
import pandas as pd          # universal — everyone uses pd
import matplotlib.pyplot as plt   # universal
import seaborn as sns        # standard
import tensorflow as tf      # standard

# Using non-standard aliases confuses everyone
import numpy as numpster     # don't do this
```

### from module import name as alias — Rename a Specific Import

```python
from math import sqrt as square_root

print(square_root(16))    # 4.0

# Useful for avoiding name conflicts
from mymodule import format as format_output   # 'format' is a built-in
```

---

## Creating Your Own Module

Any `.py` file is a module. You import it by its filename (without `.py`).

**File structure:**

```
project/
    main.py
    mathutils.py
    stringutils.py
```

**mathutils.py:**

```python
# mathutils.py

PI = 3.14159

def circle_area(radius):
    return PI * radius ** 2

def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

**main.py — importing your own module:**

```python
import mathutils

print(mathutils.circle_area(5))    # 78.53975
print(mathutils.is_prime(17))      # True
print(mathutils.PI)                # 3.14159

# Or import specific names
from mathutils import circle_area, is_prime

print(circle_area(5))    # 78.53975
print(is_prime(17))      # True
```

### Python Finds Modules by Looking in sys.path

```python
import sys
print(sys.path)
# ['', '/usr/lib/python311', ...]
# '' = current directory — that's why importing same-folder files works
```

---

## __name__ == "__main__" — The Most Important Module Pattern

When Python runs a file, it sets a special variable `__name__`:
- If the file is **run directly**: `__name__ = "__main__"`
- If the file is **imported**: `__name__ = "the module's filename"`

```python
# mathutils.py

print(f"__name__ is: {__name__}")

def circle_area(radius):
    return 3.14159 * radius ** 2
```

```
# Running directly:   python mathutils.py
__name__ is: __main__

# Importing it:       import mathutils (from main.py)
__name__ is: mathutils
```

### The Problem This Solves — Test Code Running on Import

```python
# mathutils.py WITHOUT the guard

def circle_area(radius):
    return 3.14159 * radius ** 2

# Test code at the bottom
print(circle_area(5))        # 78.53975
print("testing is_prime...")
```

```python
# main.py
import mathutils    # this runs ALL top-level code in mathutils.py
                    # including your print statements — unwanted output!
```

### The Fix — Guard All Runnable Code

```python
# mathutils.py WITH the guard

def circle_area(radius):
    return 3.14159 * radius ** 2

def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

if __name__ == "__main__":
    # This block ONLY runs when you execute: python mathutils.py
    # It does NOT run when someone does: import mathutils
    print(circle_area(5))        # 78.53975
    print(is_prime(17))          # True
    print(is_prime(18))          # False
```

### Every Python File You Write Should Follow This Structure

```python
# mymodule.py

# 1. Imports at the top
import math

# 2. Constants
MAX_SIZE = 100

# 3. Functions and classes
def my_function():
    pass

# 4. Main guard at the bottom
if __name__ == "__main__":
    my_function()
```

This is not optional style — it's the standard that every Python project follows.

---

## Standard Library Modules

### math — Mathematical Functions

```python
import math

# Constants
print(math.pi)       # 3.141592653589793
print(math.e)        # 2.718281828459045
print(math.inf)      # inf
print(math.nan)      # nan
print(math.tau)      # 6.283...  (2 * pi)

# Rounding
print(math.floor(3.9))    # 3   — round down always
print(math.ceil(3.1))     # 4   — round up always
print(math.trunc(3.9))    # 3   — toward zero (same as int())
print(math.trunc(-3.9))   # -3  — toward zero (int(-3.9) = -3 too)

# Powers and roots
print(math.sqrt(25))       # 5.0
print(math.pow(2, 10))     # 1024.0   — always returns float
print(math.log(math.e))    # 1.0      — natural log
print(math.log(100, 10))   # 2.0      — log base 10
print(math.log2(8))        # 3.0
print(math.log10(1000))    # 3.0

# Trigonometry (angles in radians)
print(math.sin(math.pi / 2))    # 1.0
print(math.cos(0))              # 1.0
print(math.degrees(math.pi))    # 180.0  — radians to degrees
print(math.radians(180))        # 3.14159...  — degrees to radians

# Other
print(math.factorial(5))        # 120
print(math.gcd(12, 8))          # 4    — greatest common divisor
print(math.lcm(4, 6))           # 12   — least common multiple (3.9+)
print(math.isfinite(math.inf))  # False
print(math.isnan(math.nan))     # True
print(math.fabs(-3.14))         # 3.14 — absolute value as float

# math.pow vs ** vs abs
print(2 ** 10)          # 1024    — int result
print(math.pow(2, 10))  # 1024.0  — always float
```

---

### random — Random Number Generation

```python
import random

# Random float between 0.0 and 1.0 (exclusive)
print(random.random())          # 0.7234...  — different every run

# Random integer — both endpoints INCLUSIVE
print(random.randint(1, 6))     # 1, 2, 3, 4, 5, or 6  — dice roll
print(random.randint(0, 100))   # 0 to 100 inclusive

# Random float in a range
print(random.uniform(1.0, 5.0)) # e.g. 3.142...

# Random choice from a sequence
colours = ["red", "green", "blue", "yellow"]
print(random.choice(colours))           # one random element

# Random sample — multiple unique items
print(random.sample(colours, 2))        # e.g. ['blue', 'red'] — no repeats
print(random.sample(range(100), 10))    # 10 unique numbers from 0-99

# Shuffle — in-place, modifies the list
deck = list(range(1, 14))
random.shuffle(deck)
print(deck)    # [7, 3, 11, ...] — random order

# Weighted random choice (Python 3.6+)
outcomes   = ["win", "lose", "draw"]
weights    = [1, 3, 1]                  # lose is 3x more likely
print(random.choices(outcomes, weights=weights, k=5))
# e.g. ['lose', 'lose', 'win', 'lose', 'draw']

# Reproducible randomness — seed fixes the sequence
random.seed(42)
print(random.randint(1, 100))    # always 82 with seed 42
print(random.randint(1, 100))    # always 15 with seed 42
random.seed(42)
print(random.randint(1, 100))    # 82 again — same sequence
```

---

### os — Operating System Interface

```python
import os

# Current directory
print(os.getcwd())                   # /home/user/project
os.chdir("/tmp")                     # change working directory
print(os.getcwd())                   # /tmp

# Path operations
print(os.path.exists("file.txt"))    # True or False
print(os.path.isfile("file.txt"))    # True if it's a file
print(os.path.isdir("mydir"))        # True if it's a directory

print(os.path.join("home", "user", "file.txt"))
# home/user/file.txt  (Linux/Mac) or home\user\file.txt (Windows)
# os.path.join handles platform differences automatically

print(os.path.basename("/home/user/file.txt"))   # file.txt
print(os.path.dirname("/home/user/file.txt"))    # /home/user
print(os.path.split("/home/user/file.txt"))      # ('/home/user', 'file.txt')
print(os.path.splitext("report.pdf"))            # ('report', '.pdf')
print(os.path.abspath("file.txt"))               # full absolute path

# Listing directory contents
print(os.listdir("."))               # ['main.py', 'utils.py', 'data']
print(os.listdir("/tmp"))            # files in /tmp

# Creating directories
os.mkdir("new_folder")               # create one directory
os.makedirs("a/b/c", exist_ok=True)  # create nested dirs; no error if exists

# Removing
os.remove("file.txt")               # delete a file
os.rmdir("empty_folder")            # delete empty directory only
# os.removedirs, shutil.rmtree for non-empty dirs

# Environment variables
print(os.environ.get("HOME"))        # /home/user
print(os.environ.get("PATH"))        # all PATH entries
print(os.getenv("DATABASE_URL", "localhost"))  # with default

# Walking directory tree
for dirpath, dirnames, filenames in os.walk("."):
    for filename in filenames:
        full_path = os.path.join(dirpath, filename)
        print(full_path)
```

---

### sys — System-Specific Parameters

```python
import sys

# Command line arguments
# python script.py arg1 arg2 arg3
print(sys.argv)         # ['script.py', 'arg1', 'arg2', 'arg3']
print(sys.argv[0])      # 'script.py'  — always the script name
print(sys.argv[1])      # 'arg1'       — first actual argument

# Practical use
if len(sys.argv) < 2:
    print("Usage: python script.py <filename>")
    sys.exit(1)         # exit with error code

filename = sys.argv[1]

# Exit the program
sys.exit()              # exit with code 0 (success)
sys.exit(0)             # same — success
sys.exit(1)             # exit with code 1 (error — convention)
sys.exit("error msg")   # prints message to stderr, exits with code 1

# Python version
print(sys.version)          # '3.11.0 (main, ...)'
print(sys.version_info)     # sys.version_info(major=3, minor=11, ...)
print(sys.version_info.major)   # 3
print(sys.version_info >= (3, 10))  # True if Python 3.10+

# Module search path
print(sys.path)             # list of directories Python searches for modules

# Standard streams
print("normal output", file=sys.stdout)   # default
print("error output", file=sys.stderr)    # for errors/warnings

# Platform
print(sys.platform)     # 'linux', 'darwin' (Mac), 'win32' (Windows)

# Memory size of an object
print(sys.getsizeof([1, 2, 3]))    # bytes used by the object
```

---

### datetime — Dates and Times

```python
from datetime import datetime, date, time, timedelta

# Current date and time
now = datetime.now()
print(now)                    # 2024-11-15 14:30:45.123456
print(now.year)               # 2024
print(now.month)              # 11
print(now.day)                # 15
print(now.hour)               # 14
print(now.minute)             # 30
print(now.second)             # 45

# Current date only
today = date.today()
print(today)                  # 2024-11-15

# Creating specific dates
birthday  = date(1999, 3, 15)
meeting   = datetime(2024, 12, 25, 10, 30, 0)

# Formatting — strftime (string format time)
print(now.strftime("%Y-%m-%d"))           # 2024-11-15
print(now.strftime("%d/%m/%Y"))           # 15/11/2024
print(now.strftime("%B %d, %Y"))          # November 15, 2024
print(now.strftime("%I:%M %p"))           # 02:30 PM

# Parsing — strptime (string parse time)
date_str = "2024-11-15"
parsed = datetime.strptime(date_str, "%Y-%m-%d")
print(parsed)    # 2024-11-15 00:00:00

# timedelta — arithmetic on dates
one_week   = timedelta(weeks=1)
ten_days   = timedelta(days=10)
two_hours  = timedelta(hours=2)

print(today + one_week)      # 7 days from today
print(today - ten_days)      # 10 days ago

future = datetime(2025, 1, 1)
diff = future - now
print(diff.days)             # days until Jan 1 2025

# Common format codes
# %Y = 4-digit year  %m = month (01-12)  %d = day (01-31)
# %H = 24h hour      %M = minute         %S = second
# %I = 12h hour      %p = AM/PM          %B = full month name
# %A = full weekday  %a = short weekday
```

---

### time — Time Functions

```python
import time

# Current time as a float (seconds since Jan 1 1970 — Unix epoch)
print(time.time())           # 1731676245.123456

# Pause execution
time.sleep(1)                # sleep for 1 second
time.sleep(0.5)              # sleep for 500 milliseconds

# Measuring elapsed time
start = time.time()
# ... do some work ...
for i in range(1_000_000):
    pass
end = time.time()
print(f"took {end - start:.4f} seconds")

# Higher resolution timer for benchmarking
start = time.perf_counter()
for i in range(1_000_000):
    pass
end = time.perf_counter()
print(f"took {end - start:.6f} seconds")
# perf_counter is more precise than time() for benchmarks

# Convert timestamp to readable format
timestamp = time.time()
print(time.ctime(timestamp))         # 'Fri Nov 15 14:30:45 2024'
```

---

## pip install — Third-Party Packages

Python's built-in standard library is powerful, but the real ecosystem is on PyPI (Python Package Index) — hundreds of thousands of packages.

```bash
# Install a package
pip install requests

# Install a specific version
pip install requests==2.31.0

# Install multiple packages
pip install requests flask sqlalchemy

# Upgrade a package
pip install --upgrade requests

# Uninstall
pip uninstall requests

# List installed packages
pip list

# Show info about a package
pip show requests

# Save current environment's packages to a file
pip freeze > requirements.txt

# Install from a requirements file
pip install -r requirements.txt
```

### requirements.txt — The Standard Way to Share Dependencies

```
# requirements.txt
requests==2.31.0
flask==3.0.0
sqlalchemy==2.0.23
pydantic==2.5.0
```

### Essential Packages to Know About Now

```python
import requests      # pip install requests  — HTTP requests
import flask         # pip install flask     — web framework
import sqlalchemy    # pip install sqlalchemy — database ORM
import pydantic      # pip install pydantic  — data validation
import pytest        # pip install pytest    — testing

# Usage preview
import requests
response = requests.get("https://api.github.com")
print(response.status_code)    # 200
print(response.json())         # response as dict
```

### Virtual Environments — Always Use One Per Project

```bash
# Create
python -m venv .venv

# Activate (Linux/Mac)
source .venv/bin/activate

# Activate (Windows)
.venv\Scripts\activate

# Now pip installs only affect this project
pip install requests

# Deactivate
deactivate
```

Why: without a virtual environment, every project shares the same Python installation. If project A needs `requests==2.28` and project B needs `requests==2.31`, they conflict. Virtual environments give each project its own isolated Python.

---

## Putting It All Together

```python
# A realistic script using multiple modules
import os
import sys
import math
import random
from datetime import datetime

def generate_report(data_dir, sample_size=5):
    """
    Generate a summary report from score files in a directory.
    Usage: python script.py ./data 10
    """
    # Check directory exists
    if not os.path.exists(data_dir):
        print(f"error: directory not found: {data_dir}", file=sys.stderr)
        sys.exit(1)

    # Find all .txt files
    files = [f for f in os.listdir(data_dir) if f.endswith(".txt")]

    if not files:
        print("no data files found")
        sys.exit(0)

    print(f"found {len(files)} files in {data_dir}")

    # Simulate scores
    random.seed(42)
    all_scores = [random.randint(40, 100) for _ in range(50)]

    # Stats using math
    total   = sum(all_scores)
    avg     = total / len(all_scores)
    highest = max(all_scores)
    lowest  = min(all_scores)

    # Standard deviation manually using math
    variance = sum((s - avg) ** 2 for s in all_scores) / len(all_scores)
    std_dev  = math.sqrt(variance)

    # Sample
    sample = random.sample(all_scores, min(sample_size, len(all_scores)))

    # Report
    now = datetime.now().strftime("%Y-%m-%d %H:%M")
    print(f"\n=== Score Report — {now} ===")
    print(f"Total students : {len(all_scores)}")
    print(f"Average score  : {round(avg, 2)}")
    print(f"Highest        : {highest}")
    print(f"Lowest         : {lowest}")
    print(f"Std deviation  : {round(std_dev, 2)}")
    print(f"Sample of {sample_size}: {sample}")


if __name__ == "__main__":
    data_dir    = sys.argv[1] if len(sys.argv) > 1 else "."
    sample_size = int(sys.argv[2]) if len(sys.argv) > 2 else 5
    generate_report(data_dir, sample_size)
```

---

## Key Takeaways

- A module is any `.py` file — you've been writing modules from the start.
- `import math` — access with `math.sqrt()`. The prefix prevents name collisions.
- `from math import sqrt` — use directly, but you own the name now — don't reuse it.
- `from module import *` — never in real code. REPL only.
- Standard aliases: `numpy as np`, `pandas as pd`, `matplotlib.pyplot as plt` — follow conventions.
- `if __name__ == "__main__":` — mandatory guard for any runnable code at module level. Without it, your test code runs on every import.
- `sys.argv` — command line arguments. `sys.argv[0]` is always the script name.
- `sys.exit(0)` — success. `sys.exit(1)` — error. Convention used everywhere.
- `os.path.join()` — always use this for file paths, never string concatenation. It handles Windows/Mac/Linux differences.
- Always use virtual environments — one per project, never install globally.
- `pip freeze > requirements.txt` — how you share your project's dependencies.
