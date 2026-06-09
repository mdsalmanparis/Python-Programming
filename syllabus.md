# Python Job Roadmap — Beginner to Advanced (Backend / Product Company)

> **Goal:** Land a Python backend / full-stack role at a product company or startup (₹5–15 LPA).
> **After this roadmap:** Learn FastAPI — every prerequisite will already be solid.
> **Tags:** `[BEGINNER]` = start here · `[HIRE]` = tested in interviews · `[CORE]` = day-1 on the job · `[CAREER+]` = post-hire growth

---

## Phase 0 — Absolute Beginner Foundation `[BEGINNER]`

> Start here if you're new to Python. No prior programming knowledge assumed.
> Goal: be comfortable writing small programs on your own before moving to Phase 1.

---

### 0.1 Setup & First Program

- **Installing Python:** download from python.org; Python 3.11+ recommended; verify with `python --version`
- **VS Code setup:** install Python extension; select interpreter; run files with `F5` or terminal
- **REPL (interactive shell):** `python` in terminal; experimenting line by line; `exit()`
- **Your first script:** `print("Hello, World!")`; saving as `.py`; running with `python filename.py`
- **Comments:** `#` for single-line; `"""..."""` for multi-line docstrings
- **`print()` function:** multiple arguments; `sep=`, `end=` parameters; f-strings preview
- **`input()` function:** reading user input; everything comes in as a string
- **Indentation:** Python uses spaces (4 spaces standard) not braces; `IndentationError`

---

### 0.2 Variables & Basic Data Types

- **Variables:** naming rules (letters, digits, underscore; can't start with digit); case-sensitive; `snake_case` convention
- **Assignment:** `x = 10`; multiple assignment `a, b = 1, 2`; swap `a, b = b, a`
- **`int`:** whole numbers; `10`, `-3`, `0`; no size limit in Python
- **`float`:** decimal numbers; `3.14`, `-0.5`; floating point imprecision (`0.1 + 0.2 != 0.3`)
- **`str`:** text; single `'hello'` or double `"hello"` quotes; both are identical
- **`bool`:** `True` and `False` (capital T and F); `bool` is a subclass of `int`
- **`type()` function:** checking the type of a value
- **`None`:** represents "no value"; not zero, not empty string — it is its own type (`NoneType`)
- **Type conversion:** `int("42")`, `float("3.14")`, `str(100)`, `bool(0)`
- **Arithmetic operators:** `+`, `-`, `*`, `/`, `//` (floor div), `%` (modulo), `**` (power)
- **Operator precedence:** PEMDAS; using parentheses to be explicit
- **Augmented assignment:** `x += 1`, `x -= 1`, `x *= 2`, `x //= 2`
- **Comparison operators:** `==`, `!=`, `<`, `>`, `<=`, `>=` — always return `bool`
- **Logical operators:** `and`, `or`, `not`; short-circuit evaluation

---

### 0.3 Strings in Depth

- **String creation:** single, double, triple quotes (`"""..."""` for multi-line)
- **String indexing:** `s[0]` first character; `s[-1]` last character; `IndexError` if out of range
- **String slicing:** `s[start:stop:step]`; `s[1:4]`; `s[:3]`; `s[2:]`; `s[::-1]` reverse
- **`len(s)`:** number of characters
- **String concatenation:** `"hello" + " " + "world"`; why it's inefficient in a loop
- **String repetition:** `"ha" * 3` → `"hahaha"`
- **f-strings (formatted string literals):** `f"Hello, {name}!"` — the modern way; expressions inside `{}`
- **String methods (must-know):**
  - Case: `.upper()`, `.lower()`, `.capitalize()`, `.title()`
  - Search: `.find()`, `.index()`, `.count()`, `.startswith()`, `.endswith()`, `in` operator
  - Clean: `.strip()`, `.lstrip()`, `.rstrip()`
  - Replace: `.replace(old, new)`
  - Split/join: `.split(sep)`, `sep.join(list)`
  - Check: `.isdigit()`, `.isalpha()`, `.isalnum()`, `.isspace()`
- **String immutability:** you can't change a character in-place; must create a new string
- **`str.format()`:** older alternative to f-strings; `"Hello, {}".format(name)`
- **Escape characters:** `\n` newline, `\t` tab, `\\` backslash, `\'`, `\"`
- **Raw strings:** `r"C:\Users\name"` — backslash is literal

---

### 0.4 Lists

- **Creating a list:** `my_list = [1, 2, 3]`; mixed types allowed `[1, "hello", True]`; empty list `[]`
- **Indexing:** `my_list[0]`, `my_list[-1]`; zero-based
- **Slicing:** `my_list[1:3]`; creates a new list (shallow copy)
- **`len()`:** number of elements
- **Mutability:** lists can be changed in-place; `my_list[0] = 99`
- **Adding elements:**
  - `.append(item)` — add to end; O(1) amortized
  - `.insert(index, item)` — insert at position; O(n)
  - `.extend(iterable)` — add multiple items; `list += [4, 5]`
- **Removing elements:**
  - `.remove(value)` — removes first occurrence; `ValueError` if not found
  - `.pop(index)` — removes and returns item; default is last element
  - `del my_list[index]` — delete by index; also works on slices
  - `.clear()` — empties the list
- **Searching:** `value in my_list`; `.index(value)`; `.count(value)`
- **Sorting:** `.sort()` — in-place; `sorted(my_list)` — returns new list; `reverse=True`
- **Reversing:** `.reverse()` — in-place; `my_list[::-1]` — new list
- **Copying:** `.copy()` or `my_list[:]` for shallow copy; why `b = a` does NOT copy
- **Nested lists:** `matrix = [[1,2,3],[4,5,6]]`; `matrix[0][1]` → `2`
- **List unpacking:** `a, b, c = [1, 2, 3]`; `first, *rest = [1, 2, 3, 4]`

---

### 0.5 Tuples, Sets & Dictionaries

**Tuples**
- **Creating:** `t = (1, 2, 3)` or `t = 1, 2, 3`; single-element `(1,)` — the comma matters
- **Immutability:** cannot change elements after creation; `TypeError` if you try
- **When to use:** fixed collections; returning multiple values from functions; dict keys
- **Unpacking:** `x, y = (10, 20)`; used constantly in Python

**Sets**
- **Creating:** `s = {1, 2, 3}`; empty set must be `set()` not `{}` (that's a dict)
- **No duplicates:** `{1, 1, 2, 2}` → `{1, 2}`; use to deduplicate a list: `list(set(my_list))`
- **No indexing:** sets are unordered; no `s[0]`
- **Membership test:** `x in s` — O(1) unlike list's O(n)
- **Adding/removing:** `.add(item)`, `.remove(item)` (KeyError if missing), `.discard(item)` (safe)
- **Set operations:** union `|`, intersection `&`, difference `-`, symmetric difference `^`
- **`frozenset`:** immutable set; can be used as dict key

**Dictionaries**
- **Creating:** `d = {"name": "Alice", "age": 25}`; `{}` is an empty dict; `dict(name="Alice")`
- **Accessing values:** `d["name"]`; `KeyError` if missing; safe: `d.get("name", default)`
- **Adding/updating:** `d["city"] = "Chennai"`; overwrites if key exists
- **Deleting:** `del d["age"]`; `.pop("age")` returns the value; `.pop("age", None)` safe
- **Checking keys:** `"name" in d` — O(1)
- **Iterating:** `for key in d:`, `for key, value in d.items():`, `for value in d.values():`
- **`.keys()`, `.values()`, `.items()`:** view objects
- **`.update(other_dict)`:** merging dictionaries
- **Dict ordering:** insertion order preserved since Python 3.7+
- **Nested dicts:** `d["address"]["city"]`; common in JSON data

---

### 0.6 Conditional Statements

- **`if` statement:** `if condition:` → indented block runs if condition is `True`
- **`else` clause:** runs when the `if` condition is `False`
- **`elif` clause:** multiple conditions; checked in order; first `True` wins
- **Truthiness and falsiness:**
  - Falsy: `False`, `0`, `0.0`, `""`, `[]`, `{}`, `set()`, `None`
  - Truthy: everything else — `1`, `"hello"`, `[0]`, `{"a": 1}`
- **Ternary (one-line if):** `value = "yes" if condition else "no"`
- **Nested if:** use sparingly; deep nesting → refactor
- **`match/case` (Python 3.10+):** pattern matching; cleaner than long elif chains

---

### 0.7 Loops

**`for` loops**
- **Iterating a list:** `for item in my_list:`
- **Iterating a string:** `for char in "hello":`
- **Iterating a dict:** `for key in d:` / `for key, val in d.items():`
- **`range()`:** `range(5)` → 0–4; `range(1, 6)` → 1–5; `range(0, 10, 2)` → even numbers
- **`enumerate()`:** `for i, val in enumerate(my_list):` — index + value together
- **`zip()`:** `for a, b in zip(list1, list2):` — pairs from two lists

**`while` loops**
- **Basic:** `while condition:` — runs until condition is `False`
- **Infinite loop + break:** `while True: ... if done: break`
- **`break`:** exit the loop immediately
- **`continue`:** skip rest of current iteration, go to next
- **`else` on loops:** runs if loop completed without `break` (rarely used but useful)
- **Common patterns:** counter loop, accumulator, search-and-break

---

### 0.8 Functions — Basics

- **Defining a function:** `def function_name(parameters):`
- **Calling a function:** `function_name(arguments)`
- **Parameters vs arguments:** parameters are in the definition; arguments are what you pass
- **`return` statement:** sending a value back; function returns `None` if no `return`
- **Multiple return values:** `return x, y` returns a tuple; unpack with `a, b = func()`
- **Default parameter values:** `def greet(name, greeting="Hello"):`; defaults must come after non-defaults
- **Keyword arguments:** `greet(greeting="Hi", name="Alice")` — order doesn't matter
- **Docstrings:** `"""Description of what the function does."""` — first line of function body
- **Scope:** variables inside a function are local; can read globals but not modify without `global`
- **`global` keyword:** `global x` inside function to modify a global variable (use sparingly)
- **`pass` statement:** empty function/class body placeholder

---

### 0.9 Built-in Functions You Must Know

- **`print()`, `input()`** — I/O
- **`len()`** — length of any sequence
- **`range()`** — integer sequences for loops
- **`type()`** — check type; `isinstance(x, int)` — preferred in code
- **`int()`, `float()`, `str()`, `bool()`, `list()`, `tuple()`, `set()`, `dict()`** — type conversion
- **`min()`, `max()`** — on any iterable; `key=` parameter
- **`sum()`** — sum of an iterable
- **`abs()`** — absolute value
- **`round(x, ndigits)`** — rounding
- **`sorted(iterable, key=..., reverse=...)`** — returns new sorted list
- **`reversed(iterable)`** — returns iterator; wrap in `list()` to see it
- **`enumerate(iterable, start=0)`**
- **`zip(*iterables)`**
- **`map(func, iterable)`** — apply function to each element
- **`filter(func, iterable)`** — keep elements where function returns `True`
- **`any(iterable)`, `all(iterable)`** — boolean checks
- **`open(filepath, mode)`** — file I/O; see Phase 2
- **`help(obj)`** — inline documentation; `dir(obj)` — list attributes/methods

---

### 0.10 File I/O Basics

- **Opening a file:** `open("file.txt", "r")` — always use `with` statement
- **Modes:** `"r"` read, `"w"` write (overwrites), `"a"` append, `"rb"` / `"wb"` binary
- **Reading:**
  - `f.read()` — entire file as one string
  - `f.readlines()` — list of lines including `\n`
  - `for line in f:` — iterate lines lazily (best for large files)
  - `f.readline()` — one line at a time
- **Writing:** `f.write("text")` — no automatic newline; add `\n` yourself
- **`with` statement:** `with open("file.txt") as f:` — auto-closes file even on error
- **`encoding="utf-8"`:** always specify encoding to avoid platform issues
- **Checking existence:** `import os; os.path.exists("file.txt")` — or use `pathlib` (Phase 2)
- **Common pattern:** read → process → write

---

### 0.11 Error Handling Basics

- **What is an exception:** a runtime error that stops the program; Python raises an exception object
- **Common exceptions:** `ValueError`, `TypeError`, `IndexError`, `KeyError`, `FileNotFoundError`, `ZeroDivisionError`, `AttributeError`, `NameError`
- **`try / except`:** wrap risky code in `try`; handle the error in `except`
- **Catching specific exceptions:** `except ValueError:` — always prefer specific over bare `except:`
- **`except Exception as e:`:** capturing the exception object; `print(e)` for the message
- **`else` clause:** runs only if no exception was raised
- **`finally` clause:** always runs — for cleanup (closing files, releasing resources)
- **`raise`:** manually raising an exception; `raise ValueError("must be positive")`

---

### 0.12 Modules & Imports

- **What is a module:** any `.py` file; Python's standard library is a collection of modules
- **`import module`:** `import math`; access with `math.sqrt(16)`
- **`from module import name`:** `from math import sqrt`; use directly as `sqrt(16)`
- **`from module import *`:** imports everything — avoid in real code
- **`import module as alias`:** `import numpy as np` — standard convention
- **Creating your own module:** any `.py` file is a module; `import myfile`
- **`__name__ == "__main__"`:** code under this runs only when the file is executed directly, not when imported
- **Standard library modules to know now:** `math`, `random`, `os`, `sys`, `datetime`, `time`
  - `math.sqrt()`, `math.floor()`, `math.ceil()`, `math.pi`
  - `random.randint(a, b)`, `random.choice(seq)`, `random.shuffle(lst)`, `random.random()`
  - `os.path.exists()`, `os.listdir()`, `os.makedirs()`
  - `sys.argv`, `sys.exit()`
- **`pip install`:** installing third-party packages; `pip install requests`

---

## Phase 1 — Python Core Depth

> No gaps, no hand-waving. This is the filter layer every backend interview starts with.

---

### 1.1 Data Types, Mutability & Memory Model `[HIRE]` `[CORE]`

- **Primitive types:** `int`, `float`, `complex`, `bool`, `str`, `bytes`, `bytearray`, `NoneType`
- **id(), is vs ==:** identity vs equality; when two variables point to the same object
- **Mutability rules:** what is mutable (`list`, `dict`, `set`, `bytearray`) vs immutable (`int`, `str`, `tuple`, `frozenset`)
- **Integer interning:** CPython interns `-5` to `256`; why `a is b` is unreliable for large ints
- **String interning:** compile-time interning vs `sys.intern()`, when to rely on it
- **Mutable default argument trap:** `def f(x=[]):` — why it's a bug and the `None` sentinel pattern
- **Shallow copy vs deep copy:** `copy.copy()`, `copy.deepcopy()`, slice `[:]`, `dict.copy()`; when each is needed
- **Reference counting & garbage collection:** `sys.getrefcount()`, cyclic GC (`gc` module), `__del__`
- **Memory layout:** stack vs heap in CPython, small object allocator, `__sizeof__()`
- **`sys.getsizeof()`:** measure object sizes; why `list` overhead is larger than you expect
- **`del` keyword:** what it actually does (removes the name binding, not the object)

---

### 1.2 Control Flow, Comprehensions & Generators `[HIRE]` `[CORE]`

- **`if / elif / else`, ternary expression:** `x if condition else y`
- **`for / while` loops:** `break`, `continue`, `else` clause on loops (often unknown)
- **`range()` vs `enumerate()` vs `zip()`:** when to use each; `zip(strict=True)` (Python 3.10+)
- **`match / case` (structural pattern matching, 3.10+):** literal, capture, sequence, mapping, class patterns, guard clauses
- **List comprehensions:** `[expr for x in iterable if condition]`; nested comprehensions
- **Dict comprehensions:** `{k: v for k, v in items}`, inverting a dict
- **Set comprehensions:** `{expr for x in iterable}`
- **Generator expressions:** `(expr for x in iterable)` — lazy evaluation, single-use nature
- **Walrus operator `:=` (3.8+):** assignment expressions inside comprehensions and while loops
- **`any()`, `all()`, `filter()`, `map()`:** short-circuit evaluation; prefer comprehensions for readability
- **`sorted()` vs `.sort()`:** `key=`, `reverse=`; stability guarantee; `functools.cmp_to_key()`

---

### 1.3 Functions — `*args`, `**kwargs`, Closures, First-Class Functions `[HIRE]` `[CORE]`

- **Positional, keyword, default arguments:** ordering rules
- **`/` and `*` in signatures (3.8+):** positional-only and keyword-only parameters
- **`*args` and `**kwargs`:** packing and unpacking; forwarding patterns
- **First-class functions:** passing functions as arguments, storing in data structures
- **Higher-order functions:** functions that return functions; `map`, `filter`, `sorted` with callables
- **Closures:** captured variables, `nonlocal` keyword, cell objects
- **Late binding gotcha:** loop variable capture in lambdas/closures — why it bites and the fix
- **`lambda`:** use cases, limitations (single expression), when NOT to use
- **`functools.partial()`:** freezing arguments to create specialized callables
- **`functools.reduce()`:** fold over a sequence; when it's appropriate
- **Recursion:** base case, call stack depth, `sys.setrecursionlimit()`, tail call vs iterative refactor
- **Function annotations:** `def f(x: int) -> str:` — runtime vs static checking
- **`__annotations__`, `__doc__`, `__name__`, `__module__`:** introspecting functions

---

### 1.4 OOP — Classes, Dunder Methods, Inheritance, MRO `[HIRE]` `[CORE]`

- **Class definition:** `class`, `__init__`, instance vs class vs static attributes
- **`self` convention:** what it is, how Python passes it automatically
- **`@classmethod` vs `@staticmethod` vs instance method:** when to use each
- **`@property`, `@setter`, `@deleter`:** computed attributes, validation on assignment
- **Dunder (magic) methods:**
  - Representation: `__str__`, `__repr__`, `__format__`
  - Comparison: `__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`
  - Hashing: `__hash__` — why it's linked to `__eq__`; making objects dict keys / set members
  - Arithmetic: `__add__`, `__radd__`, `__iadd__`, `__mul__`, etc.
  - Container: `__len__`, `__getitem__`, `__setitem__`, `__delitem__`, `__contains__`, `__iter__`
  - Context manager: `__enter__`, `__exit__`
  - Callable: `__call__`
  - Attribute access: `__getattr__`, `__getattribute__`, `__setattr__`, `__delattr__`
- **`__slots__`:** memory optimization; tradeoffs vs `__dict__`
- **Inheritance:** single, multiple inheritance syntax; `super()`
- **MRO (Method Resolution Order):** C3 linearization algorithm; `ClassName.__mro__`; diamond problem
- **Mixin pattern:** composing behavior through multiple inheritance
- **Abstract base classes:** `abc.ABC`, `@abstractmethod`, `@abstractclassmethod`; why they're used in library design
- **`dataclasses` (3.7+):** `@dataclass`, `field()`, `__post_init__`, `frozen=True`, `slots=True`
- **`__init_subclass__`:** hook for customizing subclass creation without metaclasses

---

### 1.5 Exceptions — Hierarchy, Custom Exceptions, Context Managers `[HIRE]` `[CORE]`

- **Exception hierarchy:** `BaseException` → `Exception` → specific types; `SystemExit`, `KeyboardInterrupt`
- **`try / except / else / finally`:** execution order; what `else` means (runs only if no exception)
- **Catching multiple exceptions:** `except (TypeError, ValueError):`; broad vs specific catches
- **Exception chaining:** `raise X from Y`; `__cause__` vs `__context__`; suppressing with `raise X from None`
- **`raise` vs `raise e`:** re-raising; losing vs preserving traceback
- **Custom exception classes:** hierarchy design; adding context attributes; `__init__` patterns
- **Context managers with `with`:** the protocol (`__enter__`, `__exit__`); `as` target
- **`contextlib.contextmanager`:** generator-based context managers with `@contextmanager`
- **`contextlib.suppress(*exceptions)`:** cleaner than `try/except: pass`
- **`contextlib.ExitStack`:** dynamic composition of context managers
- **`ExceptionGroup` and `except*` (3.11+):** handling multiple concurrent exceptions
- **`warnings` module:** `warn()`, `filterwarnings()`, when to use warnings vs exceptions
- **Traceback module:** `traceback.format_exc()`, `traceback.print_exc()`

---

### 1.6 Iterators, `itertools` & Generators with `send()` `[HIRE]` `[CORE]`

- **Iterator protocol:** `__iter__()` returns `self`; `__next__()` raises `StopIteration`
- **`iter()` and `next()`:** built-in wrappers; `next(it, default)` sentinel
- **Writing a custom iterator class:** stateful traversal example
- **Generator functions:** `yield`, `yield from`, return value in generators (`StopIteration.value`)
- **`send()` method:** sending values into a running generator; coroutine-style generators
- **`throw()` and `close()`:** injecting exceptions into generators
- **`yield from`:** delegation, transparent proxying, return value forwarding
- **`itertools` — must-know:**
  - Infinite: `count()`, `cycle()`, `repeat()`
  - Finite: `chain()`, `chain.from_iterable()`, `islice()`, `takewhile()`, `dropwhile()`, `filterfalse()`
  - Combinatoric: `product()`, `permutations()`, `combinations()`, `combinations_with_replacement()`
  - Grouping: `groupby()` — requires sorted input
  - Zip/accumulate: `zip_longest()`, `accumulate()`, `starmap()`, `pairwise()` (3.10+)
- **`more-itertools`:** awareness of `chunked`, `windowed`, `flatten`, `peekable`

---

### 1.7 Decorators — `functools.wraps`, Stacking, Parametrized `[HIRE]` `[CORE]`

- **Decorator mechanics:** a function that takes a function and returns a function
- **`functools.wraps`:** why it's required; what it preserves (`__name__`, `__doc__`, `__wrapped__`)
- **Stacking decorators:** application order (bottom-up); mental model for chained wrappers
- **Class-based decorators:** `__init__` accepts the function, `__call__` wraps it
- **Parametrized decorators:** the triple-nested pattern; `decorator_factory(args)(func)(call_args)`
- **`functools.lru_cache` and `functools.cache`:** memoization; cache size, `cache_info()`, `cache_clear()`
- **`functools.cached_property`:** lazy attribute computation cached on the instance
- **`functools.singledispatch`:** type-based overloading without if/isinstance chains
- **Built-in decorators deep dive:** `@property`, `@classmethod`, `@staticmethod`, `@abstractmethod`
- **Real-world patterns:**
  - Timing/benchmarking decorator
  - Retry with exponential backoff
  - Access control / auth guard
  - Input validation / type checking
  - Rate limiter decorator

---

### 1.8 Type Hints — Generics, Protocols, TypeVar, Literal `[HIRE]` `[CORE]`

- **Basic annotations:** variables, function signatures, return types
- **`typing` module overview:** `Optional`, `Union`, `Any`, `ClassVar`, `Final`
- **`Union` vs `X | Y` (3.10+):** modern union syntax
- **`Optional[X]` = `X | None`:** common pattern
- **`TypeVar`:** generic functions and classes; bound TypeVars; covariance/contravariance basics
- **Generic classes:** `class Stack(Generic[T]):`; `list[int]`, `dict[str, int]` (3.9+)
- **`Protocol`:** structural subtyping (duck typing + type checker); `runtime_checkable`
- **`Literal`:** narrowing types to specific values; use in function overloads
- **`TypedDict`:** typed dictionaries; `total=False` for optional keys
- **`NamedTuple`:** typed alternative to `collections.namedtuple`
- **`overload`:** multiple signatures for one function
- **`TYPE_CHECKING`:** avoiding circular imports in type annotation files
- **`Annotated`:** attaching metadata to types; used heavily by Pydantic and FastAPI
- **Running mypy:** `mypy --strict`; fixing common errors; inline `# type: ignore`
- **`__future__ annotations`:** postponed evaluation (PEP 563); `from __future__ import annotations`

---

## Phase 2 — Standard Library Mastery

> The tools that separate juniors from mid-level devs. No third-party library knowledge required — just depth in what ships with Python.

---

### 2.1 `collections` — Counter, defaultdict, deque, namedtuple `[HIRE]` `[CORE]`

- **`Counter`:** counting hashables; arithmetic (`+`, `-`, `&`, `|`); `most_common(n)`; `update()` vs `subtract()`
- **`defaultdict`:** factory functions (`list`, `int`, `set`, `lambda`); difference from `dict.setdefault()`
- **`deque`:** O(1) append/pop from both ends; `maxlen` (circular buffer); `rotate()`; thread-safety
- **`namedtuple`:** `_fields`, `_asdict()`, `_replace()`, `_make()`; when to prefer `dataclass`
- **`OrderedDict`:** insertion order (now dict default); `move_to_end()`; equality semantics differ from dict
- **`ChainMap`:** layered lookups; variable scoping emulation; `new_child()`
- **`UserDict`, `UserList`, `UserString`:** subclassing built-ins safely

---

### 2.2 `pathlib`, `os`, `shutil` — Modern File I/O `[HIRE]` `[CORE]`

- **`pathlib.Path`:** construction, `/` operator for joining, `.name`, `.stem`, `.suffix`, `.parent`, `.parts`
- **`Path.exists()`, `.is_file()`, `.is_dir()`**
- **`Path.read_text()`, `.write_text()`, `.read_bytes()`, `.write_bytes()`**
- **`Path.open()`:** context manager; encoding parameter
- **`Path.glob()`, `.rglob()`:** recursive file discovery; pattern syntax
- **`Path.iterdir()`:** listing directory contents
- **`Path.mkdir(parents=True, exist_ok=True)`**
- **`Path.rename()`, `.replace()`, `.unlink()`**
- **`os.environ`, `os.getenv()`:** environment variables
- **`os.getcwd()`, `os.chdir()`**
- **`os.walk()`:** recursive directory traversal (when `rglob` isn't enough)
- **`shutil.copy()`, `.copy2()`, `.copytree()`, `.rmtree()`, `.move()`**
- **`tempfile.TemporaryFile()`, `NamedTemporaryFile()`, `TemporaryDirectory()`**

---

### 2.3 `json`, `csv`, `configparser`, `python-dotenv` `[HIRE]` `[CORE]`

- **`json.loads()` / `json.dumps()`:** `indent`, `sort_keys`, `ensure_ascii=False`, `default` for custom types
- **`json.load()` / `json.dump()`:** file-based; encoding considerations
- **Custom JSON serializer:** `json.JSONEncoder` subclass; `default()` method
- **`csv.reader()` / `csv.writer()`:** delimiter, quotechar, escaping
- **`csv.DictReader()` / `csv.DictWriter()`:** header row handling; `fieldnames`
- **`configparser.ConfigParser`:** `.ini` format; sections, options; `get()`, `getint()`, `getfloat()`, `getboolean()`; fallback values
- **`python-dotenv` (`dotenv`):** `load_dotenv()`, `dotenv_values()`; `.env` file patterns for 12-factor config
- **`os.environ` vs `dotenv`:** precedence; override behaviour

---

### 2.4 `datetime`, `zoneinfo`, `calendar` `[HIRE]` `[CORE]`

- **`datetime.date`, `.time`, `.datetime`, `.timedelta`:** construction, arithmetic
- **`datetime.now()` vs `datetime.utcnow()` vs `datetime.now(tz=timezone.utc)`:** why naive datetimes are dangerous
- **`timezone.utc`, `timezone(timedelta(hours=N))`**
- **`zoneinfo.ZoneInfo` (3.9+):** IANA timezone database; `ZoneInfo("Asia/Kolkata")`; `available_timezones()`
- **`strftime()` / `strptime()`:** format codes; common ISO 8601 patterns
- **`isoformat()` and `fromisoformat()`**
- **`dateutil` library:** `relativedelta`, `parser.parse()`, `rrule` — awareness
- **`calendar` module:** `monthrange()`, `isleap()`; rarely needed but shows stdlib depth

---

### 2.5 `re` — Patterns, Groups, Lookaheads, `re.compile` `[HIRE]` `[CORE]`

- **Raw strings:** always use `r"..."` for regex patterns
- **Character classes:** `.`, `\d`, `\w`, `\s`, `\b`, `[...]`, `[^...]`
- **Quantifiers:** `*`, `+`, `?`, `{n}`, `{n,m}`; greedy vs lazy (`*?`, `+?`)
- **Anchors:** `^`, `$`, `\A`, `\Z`, `\b`
- **`re.match()` vs `re.search()` vs `re.fullmatch()`:** critical difference
- **`re.findall()` vs `re.finditer()`:** list vs iterator of Match objects
- **`re.sub()`, `re.subn()`:** substitution with string or callable replacement
- **`re.split()`:** splitting on pattern; capture groups in split
- **Groups:** `(...)`, named groups `(?P<name>...)`, non-capturing `(?:...)`
- **`match.group()`, `.groups()`, `.groupdict()`, `.span()`, `.start()`, `.end()`**
- **Lookahead / lookbehind:** `(?=...)`, `(?!...)`, `(?<=...)`, `(?<!...)`
- **Flags:** `re.IGNORECASE`, `re.MULTILINE`, `re.DOTALL`, `re.VERBOSE`
- **`re.compile()`:** precompiling for reuse; when it matters for performance
- **`re.VERBOSE` (`re.X`):** writing readable multi-line patterns with comments

---

### 2.6 `logging` — Handlers, Formatters, Log Levels, `structlog` `[HIRE]` `[CORE]`

- **Log levels:** `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`; choosing the right level
- **`logging.getLogger(__name__)`:** logger hierarchy; root logger vs named loggers
- **`basicConfig()`:** quick setup; `level`, `format`, `filename`
- **Handlers:** `StreamHandler`, `FileHandler`, `RotatingFileHandler`, `TimedRotatingFileHandler`, `NullHandler`
- **Formatters:** `%(asctime)s %(levelname)s %(name)s — %(message)s`; custom formats
- **`logging.config.dictConfig()`:** production config as a dict; YAML-based config
- **Logger propagation:** `logger.propagate = False`; preventing duplicate log entries
- **Logging in libraries vs applications:** library best practice (only `NullHandler`)
- **`extra` dict:** adding contextual fields to log records
- **Exception logging:** `logger.exception()`, `exc_info=True`
- **`structlog`:** structured logging (JSON output); `structlog.get_logger()`, processors pipeline; why it matters for production log aggregation (Datadog, Loki, CloudWatch)
- **Correlation IDs / request context:** `contextvars.ContextVar` for per-request log context

---

### 2.7 `functools` — `lru_cache`, `partial`, `reduce`, `singledispatch` `[HIRE]` `[CORE]`

- **`functools.lru_cache(maxsize=...)`:** memoization; hashable arguments requirement; `cache_info()`, `cache_clear()`
- **`functools.cache` (3.9+):** unbounded LRU cache; `@cache` convenience
- **`functools.cached_property`:** lazy attribute; descriptor protocol
- **`functools.partial(func, *args, **kwargs)`:** partial application; use in callbacks and configuration
- **`functools.reduce(func, iterable, initializer)`:** left fold; when readable vs when to use a loop
- **`functools.singledispatch`:** type-based overloading; registering implementations for types
- **`functools.total_ordering`:** implement `__eq__` and one comparison; get the rest free
- **`functools.wraps`:** copying function metadata in decorators (see 1.7)

---

### 2.8 `contextlib` — `suppress`, `nullcontext`, `asynccontextmanager` `[HIRE]` `[CORE]`

- **`contextlib.contextmanager`:** `@contextmanager`; `yield` as the `with` block; exception handling in generator
- **`contextlib.suppress(*exceptions)`:** silently ignore specified exceptions
- **`contextlib.nullcontext(enter_result=None)`:** no-op context manager; useful in conditional `with` statements
- **`contextlib.asynccontextmanager`:** async version; used in FastAPI lifespan events
- **`contextlib.ExitStack`:** dynamically managing multiple context managers; cleanup on failure
- **`contextlib.AsyncExitStack`:** async version; essential in async code
- **`contextlib.redirect_stdout`, `.redirect_stderr`:** capturing output in tests
- **`contextlib.closing(thing)`:** ensure `.close()` is called; for objects without context manager support

---

## Phase 3 — Concurrency & Async

> The skill gap most backend candidates have. FastAPI is async — this phase is non-negotiable.

---

### 3.1 Threading — GIL, Locks, Race Conditions, Thread-Safe Queues `[HIRE]` `[CORE]`

- **GIL (Global Interpreter Lock):** what it is, what it protects, why CPython has it
- **I/O-bound vs CPU-bound:** why threads help for I/O but not CPU; the `time.sleep()` demonstration
- **`threading.Thread`:** `target`, `args`, `kwargs`, `daemon`; `.start()`, `.join()`
- **`threading.Lock`:** `.acquire()`, `.release()`, context manager form; why you always use `with lock:`
- **Race conditions:** demonstration with shared counter; why they're hard to reproduce
- **`threading.RLock`:** reentrant lock; when you need it
- **`threading.Event`:** signalling between threads; `.set()`, `.clear()`, `.wait()`
- **`threading.Semaphore`:** limiting concurrent access; bounded vs unbounded
- **`threading.Barrier`:** synchronizing N threads at a checkpoint
- **`queue.Queue`:** thread-safe FIFO; `.put()`, `.get()`, `.task_done()`, `.join()`; `Queue.Empty` exception
- **`queue.PriorityQueue`, `queue.LifoQueue`**
- **`threading.local()`:** per-thread storage; use for request context in sync web frameworks
- **Thread pool patterns:** producer-consumer; worker pool with `Queue`

---

### 3.2 Multiprocessing — Pool, Process, Shared Memory `[HIRE]` `[CORE]`

- **`multiprocessing.Process`:** `target`, `args`; `.start()`, `.join()`, `.terminate()`, `.exitcode`
- **`if __name__ == "__main__"`:** why it's required on Windows/macOS
- **`multiprocessing.Pool`:** `pool.map()`, `pool.starmap()`, `pool.apply_async()`; `chunksize`
- **`multiprocessing.Queue` vs `queue.Queue`:** cross-process vs cross-thread
- **`multiprocessing.Pipe`:** duplex or simplex; `send()`, `recv()`
- **`multiprocessing.Value`, `multiprocessing.Array`:** shared memory with locking
- **`multiprocessing.shared_memory` (3.8+):** zero-copy shared memory between processes
- **`multiprocessing.Manager`:** proxy objects for sharing complex data structures
- **`concurrent.futures.ProcessPoolExecutor`:** high-level process pool; `submit()`, `map()`; futures
- **`concurrent.futures.ThreadPoolExecutor`:** thread pool with same API
- **`concurrent.futures.Future`:** `.result()`, `.done()`, `.cancel()`, `.add_done_callback()`
- **`concurrent.futures.as_completed()`:** processing results as they finish
- **Inter-process communication patterns:** fan-out, aggregation

---

### 3.3 `asyncio` — Event Loop, Coroutines, Tasks, `gather`, `shield` `[HIRE]` `[CORE]`

- **`async def` and `await`:** coroutine syntax; what `await` actually does (yields to event loop)
- **Event loop:** what it is; `asyncio.run()`; `asyncio.get_event_loop()` (deprecated pattern)
- **`asyncio.Task`:** wrapping coroutines for concurrent execution; `asyncio.create_task()`
- **`asyncio.gather(*coros, return_exceptions=False)`:** running coroutines concurrently; collecting results
- **`asyncio.wait(tasks, return_when=...)`:** finer control; `FIRST_COMPLETED`, `FIRST_EXCEPTION`, `ALL_COMPLETED`
- **`asyncio.shield(coro)`:** protecting a coroutine from cancellation
- **`asyncio.timeout(seconds)` (3.11+):** `async with asyncio.timeout(5):`
- **`asyncio.sleep(seconds)`:** yielding control; `sleep(0)` to yield to event loop
- **`asyncio.Event`, `asyncio.Lock`, `asyncio.Semaphore`, `asyncio.Condition`:** async synchronization primitives
- **`asyncio.Queue`:** async producer-consumer
- **`asyncio.TaskGroup` (3.11+):** structured concurrency; automatic cancellation on failure
- **`asyncio.to_thread()`:** running blocking code in a thread pool without blocking the event loop
- **Cancellation:** `task.cancel()`, catching `asyncio.CancelledError`; cleanup on cancellation
- **`asyncio.current_task()`, `asyncio.all_tasks()`:** introspection

---

### 3.4 Async Patterns — Semaphore, Queue, Producer-Consumer `[HIRE]` `[CORE]`

- **Async producer-consumer:** multiple producers, multiple consumers via `asyncio.Queue`
- **Bounded concurrency with `asyncio.Semaphore`:** limiting concurrent HTTP calls or DB queries
- **Rate limiting pattern:** `asyncio.Semaphore` + `asyncio.sleep()` for token bucket
- **Async worker pool:** `asyncio.Queue` + N worker tasks; graceful shutdown with sentinel values
- **`asyncio.Event` for signalling:** coordinating startup/shutdown between tasks
- **Backpressure:** `asyncio.Queue(maxsize=N)`; blocking producers when queue is full
- **Retry with async backoff:** `asyncio.sleep(2**attempt)` in a loop
- **Async context managers:** `async with`; `__aenter__`, `__aexit__`
- **Async iterators:** `async for`; `__aiter__`, `__anext__`; async generators (`async def` + `yield`)
- **Running sync code safely:** `asyncio.to_thread()` vs `loop.run_in_executor()`

---

### 3.5 `httpx` Async Client, Connection Pooling `[HIRE]` `[CORE]`

- **`httpx.AsyncClient`:** `async with httpx.AsyncClient() as client:`; why client reuse matters
- **Making requests:** `.get()`, `.post()`, `.put()`, `.delete()`, `.patch()`, `.request()`
- **Request options:** `params`, `headers`, `json`, `data`, `timeout`, `follow_redirects`
- **`httpx.Timeout`:** connect timeout vs read timeout vs write timeout vs pool timeout
- **Response handling:** `.status_code`, `.json()`, `.text`, `.content`, `.headers`; `.raise_for_status()`
- **`httpx.HTTPStatusError`:** catching 4xx/5xx; `.response.status_code`
- **Connection pooling:** `limits=httpx.Limits(max_connections=100, max_keepalive_connections=20)`
- **Base URL and common headers:** `httpx.AsyncClient(base_url="...", headers={...})`
- **Async streaming responses:** `.aiter_bytes()`, `.aiter_lines()`; for large payloads
- **Mocking httpx in tests:** `pytest-httpx` or `respx`
- **`httpx.Client` (sync):** same API without async; for scripts and sync code

---

## Phase 4 — Testing & Code Quality

> Non-negotiable at every product company. If you can't test it, you can't ship it.

---

### 4.1 `pytest` — Fixtures, Parametrize, Markers, `conftest.py` `[HIRE]` `[CORE]`

- **Test discovery:** naming conventions (`test_*.py`, `Test*`, `test_*`); running with `pytest`
- **Assertions:** plain `assert` with pytest rewriting; why `assert a == b` gives better output than `assertEqual`
- **`@pytest.fixture`:** scope (`function`, `class`, `module`, `session`); `yield` for teardown
- **`conftest.py`:** shared fixtures; test configuration; plugin hooks; visibility rules (local vs root)
- **`@pytest.mark.parametrize`:** parameter matrix; IDs; indirect parametrize
- **`@pytest.mark.skip`, `@pytest.mark.skipif`:** conditional skipping
- **`@pytest.mark.xfail`:** expected failures; `strict=True`
- **Custom markers:** registering in `pytest.ini`; running with `-m "marker_name"`
- **`tmp_path` fixture:** temporary directories in tests
- **`capsys`, `capfd`:** capturing stdout/stderr
- **`monkeypatch`:** patching attributes, environment variables, dict items, `sys.path`
- **`pytest.raises(ExceptionType)`:** asserting exceptions; `.match` for message; `.value` for the exception object
- **`pytest.warns()`:** asserting warnings
- **`pytest-xdist`:** parallel test execution (`-n auto`)
- **`pytest.ini` / `pyproject.toml [tool.pytest.ini_options]`:** configuration

---

### 4.2 Mocking — `unittest.mock`, `MagicMock`, `patch`, `side_effect` `[HIRE]` `[CORE]`

- **`unittest.mock.Mock`:** creating mock objects; auto-speccing; `assert_called_once_with()`
- **`unittest.mock.MagicMock`:** magic method support; `__len__`, `__iter__`, `__enter__`, `__exit__`
- **`unittest.mock.patch`:** as decorator, as context manager; `target` string format (`"module.ClassName"`)
- **`patch.object(obj, "attr")`:** patching attributes on specific objects
- **`patch.dict()`:** temporarily modifying dictionaries (useful for `os.environ`)
- **`side_effect`:** raising exceptions; returning different values on successive calls (list); callable
- **`return_value`:** setting what the mock returns when called
- **`spec=` and `spec_set=`:** auto-spec from a real object; prevents typos in attribute names
- **`call_args_list`:** inspecting all calls; `call()` objects
- **`reset_mock()`:** clearing call history between tests
- **`AsyncMock` (3.8+):** mocking async functions and methods
- **Mocking patterns:**
  - Mocking `datetime.now()`
  - Mocking database sessions
  - Mocking external API calls
  - Mocking file system operations

---

### 4.3 `pytest-asyncio` & `AsyncClient` for FastAPI Testing `[HIRE]` `[CORE]`

- **`pytest-asyncio`:** `@pytest.mark.asyncio`; `asyncio_mode = "auto"` in config
- **`async def test_*()`:** writing async test functions
- **Async fixtures:** `@pytest.fixture` with `async def`; scoping async fixtures
- **`httpx.AsyncClient` with `transport=ASGITransport`:** testing FastAPI apps without a running server
- **`anyio` backend:** `pytest-anyio` alternative; trio support
- **Testing async generators and background tasks**
- **`asyncio.get_event_loop_policy()`:** event loop management in tests; `event_loop` fixture scope

---

### 4.4 Coverage & Hypothesis Property-Based Testing `[HIRE]`

- **`pytest-cov`:** `--cov=src --cov-report=html`; coverage percentage; branch coverage
- **`.coveragerc` / `pyproject.toml [tool.coverage]`:** exclude patterns; omit files
- **Reading coverage reports:** identifying untested branches; the difference between line and branch coverage
- **`hypothesis`:** `@given(st.integers(), st.text())`; shrinking failing examples
- **`hypothesis.strategies`:** `st.integers()`, `st.text()`, `st.lists()`, `st.dictionaries()`, `st.one_of()`, `st.builds()`
- **`hypothesis.settings`:** max examples, deadline, suppress health checks
- **`@example()`:** explicit examples alongside property tests
- **Property-based patterns:** round-trip properties (serialize → deserialize = identity), commutative operations, invariants

---

### 4.5 Linting — `ruff`, `mypy`, `pre-commit` Hooks `[HIRE]` `[CORE]`

- **`ruff`:** drop-in replacement for flake8 + isort + pyupgrade + many more; `ruff check .`, `ruff format .`
- **`ruff` configuration:** `pyproject.toml [tool.ruff]`; `select`, `ignore`, `line-length`, `target-version`
- **`ruff format`:** Black-compatible formatter; replacing Black in new projects
- **`mypy`:** static type checker; `mypy src/`; `--strict` flag; common error codes
- **`mypy` configuration:** `pyproject.toml [tool.mypy]`; `ignore_missing_imports`, `disallow_untyped_defs`
- **`pre-commit`:** `.pre-commit-config.yaml`; `pre-commit install`; hooks run on `git commit`
- **Standard hook set:** `ruff`, `ruff-format`, `mypy`, `trailing-whitespace`, `end-of-file-fixer`
- **`pre-commit run --all-files`:** CI equivalent

---

## Phase 5 — Data & Database Layer

> Every backend role touches a database. This phase makes you production-ready.

---

### 5.1 SQLAlchemy ORM — Models, Sessions, Relationships, Lazy vs Eager `[HIRE]` `[CORE]`

- **`DeclarativeBase` (SQLAlchemy 2.0):** `class Base(DeclarativeBase): pass`
- **Mapped columns:** `Mapped[int]`, `mapped_column()`, `Optional[str]`, `Column` types
- **`relationship()`:** one-to-many, many-to-many, one-to-one; `back_populates`, `backref`
- **`ForeignKey`:** column-level; `ondelete="CASCADE"`, `onupdate="CASCADE"`
- **Association table:** many-to-many via `Table()`; `secondary=`
- **Session management:** `Session`, `sessionmaker`, `scoped_session`
- **`async_session` + `AsyncSession`:** async SQLAlchemy with `asyncpg` driver
- **CRUD operations:** `session.add()`, `session.delete()`, `session.get()`, `session.execute(select(...))`
- **`select()` statement:** `where()`, `order_by()`, `limit()`, `offset()`, `join()`, `outerjoin()`
- **Loading strategies:**
  - Lazy loading (default): N+1 query problem
  - Eager loading: `selectinload()`, `joinedload()`, `subqueryload()`
  - `lazy="raise"`: detecting accidental lazy loads in tests
- **`expire_on_commit`:** why objects become expired after commit; `.refresh()`
- **Bulk operations:** `session.execute(insert(Model).values([...]))`; `returning()`
- **`with_for_update()`:** row-level locking for concurrent updates

---

### 5.2 Alembic — Autogenerate Migrations, Upgrade/Downgrade `[HIRE]` `[CORE]`

- **`alembic init alembic`:** project structure; `alembic.ini`, `env.py`
- **Configuring `env.py`:** pointing at SQLAlchemy `Base.metadata` and database URL
- **`alembic revision --autogenerate -m "description"`:** generating migrations from model changes
- **Reviewing generated migrations:** never trust autogenerate blindly; check for data migrations
- **`alembic upgrade head`:** running all pending migrations
- **`alembic downgrade -1`:** rolling back one migration
- **`alembic current`, `alembic history`:** inspecting migration state
- **`alembic stamp head`:** marking current DB state without running migrations (for existing DBs)
- **Data migrations:** combining `op.execute()` with schema changes; `batch_alter_table` for SQLite
- **Multi-head branching:** merging migration branches with `alembic merge`
- **Testing migrations:** running `upgrade` + `downgrade` in CI

---

### 5.3 Pydantic v2 — Validators, `model_config`, Computed Fields `[HIRE]` `[CORE]`

- **`BaseModel`:** defining models; field types; required vs optional fields
- **`Field()`:** `default`, `default_factory`, `alias`, `title`, `description`, `gt`, `lt`, `min_length`, `max_length`, `pattern`
- **`model_config = ConfigDict(...)`:** `str_strip_whitespace`, `populate_by_name`, `from_attributes`, `json_schema_extra`
- **`from_attributes=True`:** ORM mode (replacing v1's `orm_mode`); reading from SQLAlchemy models
- **`@field_validator` (v2):** `mode="before"` vs `mode="after"`; `@classmethod` requirement
- **`@model_validator`:** whole-model validation; `mode="before"` vs `mode="after"`
- **`@computed_field`:** derived fields; `repr=False`
- **`model_dump()`:** `include`, `exclude`, `by_alias`, `exclude_none`, `exclude_unset`
- **`model_validate()`:** from dict; `strict=True`
- **`model_json_schema()`:** generating JSON Schema
- **Nested models:** composition; `model_rebuild()` for forward references
- **Discriminated unions:** `Annotated[Union[A, B], Field(discriminator="type")]`
- **Custom types:** `Annotated[str, AfterValidator(...)]`; `PlainValidator`
- **v1 vs v2 migration gotchas:** `__fields__` removed; validator signature changes; `Config` → `model_config`

---

### 5.4 Redis — Caching, TTL, Pub/Sub, Rate Limiting `[HIRE]` `[CORE]`

- **`redis-py` / `redis.asyncio`:** connection; `Redis(host, port, db, decode_responses=True)`; `ConnectionPool`
- **Core data types:** `String`, `Hash`, `List`, `Set`, `Sorted Set` — use cases for each
- **`SET key value EX seconds`:** setting with TTL; `SETEX`, `SET ... EX ... NX`
- **`GET`, `DEL`, `EXISTS`, `EXPIRE`, `TTL`, `PERSIST`**
- **Caching patterns:**
  - Cache-aside (lazy loading)
  - Write-through
  - Cache invalidation strategies; cache stampede prevention
- **`HSET`, `HGET`, `HMGET`, `HGETALL`:** hash operations for structured data
- **`LPUSH`, `RPUSH`, `LPOP`, `BRPOP`:** list operations; job queue pattern
- **`SADD`, `SISMEMBER`, `SMEMBERS`:** set operations; deduplication
- **`ZADD`, `ZRANGEBYSCORE`:** sorted sets for leaderboards, rate limiting windows
- **Pub/Sub:** `PUBLISH`, `SUBSCRIBE`; async subscriber pattern
- **Rate limiting with Redis:** token bucket with `INCR` + `EXPIRE`; sliding window with sorted sets
- **Lua scripting:** atomic multi-step operations via `EVAL`
- **`redis.asyncio` client:** async `await client.set(...)` patterns for FastAPI

---

### 5.5 PostgreSQL — Indexes, `EXPLAIN ANALYZE`, Transactions, JSONB `[HIRE]` `[CORE]`

- **`psycopg2` / `asyncpg`:** connection, cursor, `execute()`, `fetchone()`, `fetchall()`
- **Index types:** B-tree (default), Hash, GIN (for JSONB, full-text), GiST, BRIN
- **When to add an index:** query patterns, cardinality, write vs read tradeoffs
- **Composite indexes:** column order matters; covering indexes
- **`EXPLAIN`:** reading the query plan; node types (Seq Scan, Index Scan, Hash Join, Merge Join)
- **`EXPLAIN ANALYZE`:** actual execution times; rows estimate vs actual; planning vs execution time
- **Identifying slow queries:** `pg_stat_statements`; `auto_explain`; slow query log
- **Transactions:** `BEGIN`, `COMMIT`, `ROLLBACK`; isolation levels (`READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`)
- **Concurrency anomalies:** dirty read, non-repeatable read, phantom read; which isolation level prevents which
- **Row-level locking:** `SELECT ... FOR UPDATE`, `FOR SHARE`, `SKIP LOCKED` (job queue pattern)
- **JSONB:** `@>`, `?`, `#>>` operators; GIN indexing; when JSONB is appropriate vs normalized tables
- **Window functions:** `ROW_NUMBER()`, `RANK()`, `LAG()`, `LEAD()`, `PARTITION BY`
- **CTEs:** `WITH ... AS (...)`; recursive CTEs
- **`ON CONFLICT`:** upsert; `DO NOTHING`, `DO UPDATE SET`
- **Partial indexes:** `CREATE INDEX ... WHERE condition`; significant size/performance win

---

## Phase 6 — Packaging, Tooling & DevOps Basics

> Ship it — not just write it. Product companies expect this from day one.

---

### 6.1 `pyproject.toml`, `uv` / `poetry`, Virtual Environments `[HIRE]` `[CORE]`

- **Virtual environments:** `python -m venv .venv`; activation; why they exist; `.gitignore`
- **`pyproject.toml`:** PEP 517/518; `[build-system]`, `[project]`, `[project.optional-dependencies]`
- **`uv`:** fast installer and resolver; `uv pip install`, `uv pip sync`, `uv venv`; replacing `pip` + `virtualenv`
- **`uv.lock` / `poetry.lock`:** deterministic installs; committing lockfiles
- **`poetry`:** `pyproject.toml`-native; `poetry add`, `poetry install`, `poetry run`, `poetry build`
- **Dependency groups:** `[project.optional-dependencies]` (`dev`, `test`, `docs`); `uv pip install .[dev]`
- **`pip-tools`:** `pip-compile requirements.in` → `requirements.txt`; `pip-sync`
- **`__version__`:** `importlib.metadata.version("package")` for reading version from metadata
- **Namespace packages:** `src/` layout vs flat layout; import behavior differences
- **Building and publishing:** `python -m build`; `twine upload dist/*`; PyPI tokens

---

### 6.2 Docker — Dockerfile, Multi-Stage Builds, Compose `[HIRE]` `[CORE]`

- **`Dockerfile` instructions:** `FROM`, `WORKDIR`, `COPY`, `RUN`, `CMD`, `ENTRYPOINT`, `EXPOSE`, `ENV`, `ARG`, `USER`
- **Layer caching:** order dependencies copy + install before source copy; why it matters for build speed
- **Multi-stage builds:** `FROM python:3.12 AS builder`; copying artifacts between stages; smaller final images
- **`.dockerignore`:** excluding `__pycache__`, `.git`, `.venv`, test files
- **`CMD` vs `ENTRYPOINT`:** exec form vs shell form; combining them
- **Non-root user:** `USER nobody`; security best practice
- **Environment variables:** `ENV` in Dockerfile vs `--env-file` at runtime
- **`docker build -t name:tag .`**; `docker run -p 8000:8000 --env-file .env name:tag`
- **`docker-compose.yml`:** services, networks, volumes; `depends_on`; `healthcheck`
- **Docker Compose for development:** mounting source code; hot reload with `uvicorn --reload`
- **Postgres + Redis services in Compose:** `image:`, `environment:`, `volumes:` for persistence
- **Container health checks:** `HEALTHCHECK CMD curl -f http://localhost:8000/health || exit 1`

---

### 6.3 GitHub Actions — CI Pipeline, Lint + Test + Build `[HIRE]` `[CORE]`

- **`.github/workflows/ci.yml`:** `on:`, `jobs:`, `steps:`
- **Triggers:** `push`, `pull_request`, `schedule`, `workflow_dispatch`
- **`runs-on: ubuntu-latest`**
- **`actions/checkout@v4`, `actions/setup-python@v5`**
- **Caching dependencies:** `actions/cache` with pip/uv cache dir
- **CI steps:** checkout → set up Python → install deps → ruff check → mypy → pytest --cov
- **Environment secrets:** `${{ secrets.DATABASE_URL }}`; never hardcode credentials
- **Matrix strategy:** testing on multiple Python versions
- **`actions/upload-artifact`:** uploading coverage reports or build artifacts
- **Branch protection rules:** requiring CI to pass before merge

---

### 6.4 12-Factor App, Environment Config, Secrets Management `[HIRE]`

- **12-factor principles:** III (config), IV (backing services), IX (disposability), XI (logs)
- **Config from environment:** never hardcode; `os.environ`, `pydantic-settings`
- **`pydantic-settings` `BaseSettings`:** auto-loading from `.env` and environment; type validation of config
- **Secrets vs config:** secrets (DB passwords, API keys) vs non-sensitive config (log level, port)
- **`.env` files:** `.env`, `.env.test`, `.env.production`; never commit; `.env.example` as template
- **Secret stores awareness:** HashiCorp Vault, AWS Secrets Manager, Azure Key Vault — how apps consume them
- **Docker secrets:** bind-mounting secrets files vs environment variables

---

## Phase 7 — Data Structures & Algorithms `[HIRE]`

> The coding round filter. Every product company and startup runs DSA interviews — LC easy/medium is the baseline, LC medium/hard for ₹8 LPA+. Implement everything in Python from scratch at least once.

---

### 7.1 Arrays & Strings `[HIRE]`

- **Python `list` internals:** dynamic array; amortized O(1) append; over-allocation factor
- **Two-pointer technique:** opposite ends (`two sum sorted`, `container with most water`); same direction (`remove duplicates`, `sliding window`)
- **Sliding window:** fixed size vs variable size; `max sum subarray of size k`; `longest substring without repeating characters`
- **Prefix sums:** `prefix[i] = prefix[i-1] + arr[i]`; subarray sum queries in O(1); `subarray sum equals k`
- **Kadane's algorithm:** maximum subarray sum; tracking current and global max
- **Boyer-Moore voting:** majority element in O(n) time O(1) space
- **In-place array manipulation:** rotate array, move zeros, next permutation
- **String operations:** `s[::-1]` reversal; `s.split()`, `''.join()`; `collections.Counter` for anagram checks
- **Sliding window on strings:** `minimum window substring`; `longest substring with at most k distinct`
- **Two-sum / three-sum patterns:** sorting + two pointer; hash map for O(n)
- **Binary search on arrays:** `bisect.bisect_left()`, `bisect.bisect_right()`; search rotated sorted array; finding boundaries

---

### 7.2 Linked Lists `[HIRE]`

- **Implementing singly linked list in Python:** `Node` class with `val` and `next`
- **Implementing doubly linked list:** `prev` and `next` pointers
- **Traversal, insertion, deletion:** at head, tail, arbitrary position — time complexities
- **Fast and slow pointer (Floyd's):** cycle detection; finding cycle start; finding middle node
- **Reversing a linked list:** iterative (three-pointer); recursive
- **Merging two sorted lists:** iterative with dummy head
- **Finding the nth node from end:** two-pointer with n gap
- **Intersection of two linked lists:** length difference method; cycle trick
- **LRU cache implementation:** doubly linked list + `dict`; O(1) get and put — classic interview problem

---

### 7.3 Stacks & Queues `[HIRE]`

- **Stack with Python `list`:** `append()` / `pop()`; when to use `collections.deque` instead
- **`collections.deque`:** O(1) append and pop from both ends; `appendleft()`, `popleft()`
- **Monotonic stack:** next greater element; largest rectangle in histogram; daily temperatures
- **Stack for matching:** valid parentheses; `({[]})` patterns
- **Implement queue using two stacks**
- **Implement stack using two queues**
- **Min stack:** O(1) `getMin()` using auxiliary stack
- **BFS with queue:** `collections.deque`; level-order processing
- **`heapq` module:** min-heap; `heappush()`, `heappop()`, `heapify()`; max-heap with negation trick
- **Priority queue patterns:** k largest elements; merge k sorted lists; top k frequent elements
- **`heapq.nlargest()`, `heapq.nsmallest()`:** convenience wrappers

---

### 7.4 Hash Maps & Hash Sets `[HIRE]`

- **Python `dict` internals:** hash table; O(1) average get/set/delete; collision handling (open addressing in CPython 3.6+)
- **`set` internals:** hash table with only keys; O(1) membership test
- **Two-sum with hash map:** classic O(n) pattern
- **Frequency counting:** `collections.Counter`; top-k with `most_common()`
- **Grouping / bucketing:** `defaultdict(list)`; group anagrams; group by key
- **Sliding window + hash map:** character frequency in window; `minimum window substring`
- **Cycle detection with visited set:** `set()` for O(1) lookup
- **Hash map for memoization:** manually vs `functools.lru_cache`
- **`frozenset` as dict key / set member:** hashing collections

---

### 7.5 Trees `[HIRE]`

- **Binary tree node:** `TreeNode(val, left=None, right=None)`
- **Tree traversals — recursive and iterative:**
  - Inorder (left → root → right): sorted output for BST
  - Preorder (root → left → right): tree serialization
  - Postorder (left → right → root): deletion, expression trees
  - Level-order (BFS): `collections.deque`; layer-by-layer
- **Height, depth, diameter of binary tree**
- **Balanced binary tree check:** height difference ≤ 1 at every node
- **BST properties:** left < root < right; inorder is sorted
- **BST operations:** search, insert, delete (three cases); O(log n) average, O(n) worst
- **Lowest Common Ancestor (LCA):** recursive; path from root; BST shortcut
- **Path sum problems:** root-to-leaf; any path (`maxPathSum`)
- **Serialize and deserialize binary tree:** preorder + null markers
- **N-ary tree traversal**
- **Trie (Prefix Tree):**
  - `TrieNode` with `children: dict` and `is_end: bool`
  - `insert()`, `search()`, `startsWith()` — all O(L) where L = word length
  - Applications: autocomplete, word search, IP routing

---

### 7.6 Graphs `[HIRE]`

- **Representations:** adjacency list (`defaultdict(list)`), adjacency matrix, edge list
- **BFS:** `collections.deque`; visited set; shortest path in unweighted graph; level tracking
- **DFS:** recursive with visited set; iterative with explicit stack
- **Cycle detection:**
  - Undirected: BFS/DFS with parent tracking
  - Directed: DFS with recursion stack (white-gray-black coloring)
- **Topological sort:**
  - DFS-based (reverse postorder)
  - Kahn's algorithm (BFS with in-degree): `from collections import deque`
- **Connected components:** Union-Find or DFS count
- **Union-Find (Disjoint Set Union):**
  - `find()` with path compression
  - `union()` with union by rank
  - Applications: connected components, cycle detection, Kruskal's MST
- **Shortest path algorithms:**
  - Dijkstra's: `heapq` + distance dict; O((V+E) log V)
  - Bellman-Ford: handles negative weights; O(VE)
  - Floyd-Warshall: all-pairs; O(V³); awareness
- **Minimum Spanning Tree:** Kruskal's (sort edges + Union-Find); Prim's (greedy + heap)
- **Grid as graph:** 4-directional vs 8-directional; island counting; flood fill; BFS for shortest path in maze
- **Bipartite check:** two-coloring with BFS/DFS

---

### 7.7 Recursion & Backtracking `[HIRE]`

- **Recursion anatomy:** base case, recursive case, return value; drawing the call tree
- **Tail recursion:** Python doesn't optimize it — know when to convert to iterative
- **Backtracking template:**
  ```
  def backtrack(state, choices):
      if is_solution(state): record(state); return
      for choice in choices:
          make(choice)
          backtrack(new_state, remaining_choices)
          undo(choice)
  ```
- **Subsets / power set:** include/exclude pattern; bitmask approach
- **Permutations:** swapping in-place; `itertools.permutations` for reference
- **Combinations:** `k` of `n`; `itertools.combinations` for reference
- **N-Queens:** row-by-row placement; column + diagonal sets for O(1) conflict check
- **Sudoku solver:** constraint propagation + backtrack
- **Word search in grid:** DFS + visited set; `path.add(cell)` / `path.remove(cell)`
- **Palindrome partitioning:** backtrack + precomputed palindrome check
- **Pruning:** cutting branches early (sorted input + remaining sum check for combination sum)

---

### 7.8 Dynamic Programming `[HIRE]`

- **DP mindset:** optimal substructure + overlapping subproblems; top-down (memoization) vs bottom-up (tabulation)
- **Top-down with `@lru_cache` / `@cache`:** cleanest Python DP; `functools.cache` as unbounded memo
- **1D DP patterns:**
  - Fibonacci / climbing stairs: `dp[i] = dp[i-1] + dp[i-2]`
  - House robber: `dp[i] = max(dp[i-1], dp[i-2] + nums[i])`
  - Coin change: unbounded knapsack; `dp[i] = min(dp[i], dp[i-coin] + 1)`
  - Longest increasing subsequence (LIS): O(n²) DP; O(n log n) patience sort with `bisect`
- **2D DP patterns:**
  - 0/1 knapsack: `dp[i][w] = max(dp[i-1][w], dp[i-1][w-wt] + val)` + space optimization to 1D
  - Unique paths / grid DP: `dp[i][j] = dp[i-1][j] + dp[i][j-1]`
  - Edit distance (Levenshtein): `dp[i][j]` = min ops to convert `s1[:i]` to `s2[:j]`
  - Longest common subsequence (LCS): `dp[i][j]`; backtracking the solution
- **Interval DP:** merge stones; burst balloons; matrix chain multiplication
- **String DP:** palindromic substrings (`dp[i][j]`); palindrome partitioning minimum cuts
- **State machine DP:** best time to buy/sell stock (with cooldown, with k transactions)
- **Space optimization:** rolling array — many 2D DPs reduce to O(n) space

---

### 7.9 Sorting & Searching `[HIRE]`

- **Built-in sorting:** `sorted()`, `.sort()`; Timsort; stability guarantee; `key=` function
- **Custom sort key:** `key=lambda x: (x[1], x[0])`; `functools.cmp_to_key()` for complex comparisons
- **Binary search — all variants:**
  - Find exact value
  - Find leftmost / rightmost occurrence
  - Find first true in `[False, False, True, True]` (search space bisection)
  - `bisect.bisect_left()`, `bisect.bisect_right()`, `bisect.insort()`
- **Binary search on answer:** minimum capacity to ship packages; koko eating bananas; split array largest sum — "is X feasible?" as the predicate
- **Merge sort:** divide and conquer; stable; O(n log n); merge step used in `count inversions`
- **Quick sort:** partition; pivot choice; O(n²) worst case; in-place
- **Quick select:** O(n) average kth largest/smallest; `heapq.nlargest(k, arr)` alternative
- **Counting sort / radix sort:** O(n) for bounded integers; when to use
- **Cyclic sort pattern:** arrays with values in range `[1, n]`; find missing/duplicate numbers

---

### 7.10 Bit Manipulation `[HIRE]`

- **Bitwise operators:** `&`, `|`, `^`, `~`, `<<`, `>>`
- **Common tricks:**
  - Check if bit i is set: `n & (1 << i)`
  - Set bit i: `n | (1 << i)`
  - Clear bit i: `n & ~(1 << i)`
  - Toggle bit i: `n ^ (1 << i)`
  - Check power of 2: `n & (n-1) == 0`
  - Clear lowest set bit: `n & (n-1)` — used in counting set bits
- **XOR properties:** `a ^ a = 0`; `a ^ 0 = a`; commutativity and associativity
  - Find single number in array where all others appear twice
  - Find two non-repeating numbers
- **Bit masks for subsets:** iterating all subsets of n elements with bitmask `0` to `2^n - 1`
- **`bin()`, `int('...', 2)`:** conversion; `bin(n).count('1')` for popcount
- **`int.bit_count()` (3.10+):** built-in popcount
- **Brian Kernighan's algorithm:** count set bits in O(number of set bits)

---

## Phase 8 — Design Patterns & Architecture `[CAREER+]`

> Learn these after landing the job. They make you a senior-level contributor.

---

### 8.1 SOLID Principles in Python with Real Examples

- **S — Single Responsibility:** one class, one reason to change; splitting services from models from validators
- **O — Open/Closed:** extend behavior without modifying existing code; `Protocol`, `ABC`, `@singledispatch`
- **L — Liskov Substitution:** subclasses must behave correctly in place of parents; violating it in Python
- **I — Interface Segregation:** small, focused `Protocol` classes; not forcing implementors to define unused methods
- **D — Dependency Inversion:** depend on abstractions; injecting dependencies via `__init__` vs hardcoding

---

### 8.2 Repository, Unit of Work, Service Layer

- **Repository pattern:** abstracting DB access behind an interface; `UserRepository`, `ProductRepository`
- **Unit of Work pattern:** wrapping a DB transaction; `uow.commit()`, `uow.rollback()`; used with SQLAlchemy session
- **Service layer:** orchestrating business logic; calling repositories; no DB or HTTP code in services
- **Why layering matters:** testability (mock the repo, test the service); swappability of persistence layer
- **FastAPI application:** `router → service → repository → DB`; dependency injection with `Depends()`

---

### 8.3 Dependency Injection — Manual & FastAPI's `Depends()`

- **Manual DI:** constructor injection; passing dependencies at creation time
- **`fastapi.Depends()`:** declaring dependencies in route function signatures
- **Dependency chains:** a dependency that depends on another dependency
- **Scoped dependencies:** `yield` dependencies for per-request DB sessions
- **Overriding dependencies in tests:** `app.dependency_overrides`
- **Class-based dependencies:** `__call__` method; shared state

---

### 8.4 Event-Driven Patterns, Celery, Task Queues

- **Event-driven architecture:** producers, consumers, events; decoupling services
- **`celery`:** `Celery` app; `@app.task`; `task.delay()`, `task.apply_async()`; broker (Redis/RabbitMQ)
- **Celery beat:** periodic tasks; `beat_schedule`
- **Task retries:** `autoretry_for`, `max_retries`, `countdown`
- **Task routing:** dedicated queues for heavy vs light tasks
- **`arq` (async alternative):** Redis-backed async task queue; simpler than Celery for async apps
- **Webhook processing:** receive → acknowledge → enqueue → process async

---

## Phase 9 — Performance & CPython Internals `[CAREER+]`

> Senior / Staff engineer territory. Come back after 6–12 months on the job.

---

### 9.1 Profiling — `cProfile`, `line_profiler`, `memory_profiler`

- **`cProfile`:** `python -m cProfile -s cumulative script.py`; `pstats.Stats`; `snakeviz` visualizer
- **`profile` vs `cProfile`:** C implementation vs pure Python; overhead
- **`timeit`:** microbenchmarking; `timeit.timeit()`, `timeit.repeat()`; `-n` and `-r`
- **`line_profiler`:** `@profile` decorator; `kernprof -l -v script.py`; line-by-line timing
- **`memory_profiler`:** `@profile`; `mprof run`; tracking memory over time
- **`tracemalloc`:** stdlib; `tracemalloc.start()`; `take_snapshot()`; top memory allocations
- **Flame graphs:** `py-spy record -o profile.svg -- python script.py`; reading call stacks

---

### 9.2 CPython Bytecode, `dis` Module, `__slots__`

- **`dis.dis(func)`:** disassembling Python functions; reading bytecode instructions
- **Common opcodes:** `LOAD_FAST`, `STORE_FAST`, `CALL`, `RETURN_VALUE`, `BUILD_LIST`
- **`code` objects:** `func.__code__`; `.co_varnames`, `.co_consts`, `.co_flags`
- **`__slots__`:** replacing `__dict__` with a fixed set of attributes; memory savings; speed improvement
- **`__dict__` overhead:** why plain classes use more memory than `__slots__` classes
- **`compile()` and `eval()`:** dynamic code execution; security risks

---

### 9.3 GIL Deep-Dive & `nogil` Builds (Python 3.13+)

- **GIL internals:** reference count protection; `sys.getswitchinterval()`
- **GIL release points:** C extensions; I/O; `time.sleep()`
- **Python 3.12 `PEP 684`:** per-interpreter GIL; sub-interpreters
- **Python 3.13 free-threaded builds (`--disable-gil`):** opt-in; `sys._is_gil_enabled()`
- **Implications:** thread safety of pure Python code; extension module compatibility
- **When it matters:** CPU-bound Python code that can't use multiprocessing

---

### 9.4 Cython, `ctypes`, `cffi` for Performance-Critical Paths

- **`ctypes`:** calling C shared libraries from Python; `ctypes.CDLL`; data type mapping
- **`cffi`:** higher-level C interop; `FFI.cdef()`; ABI vs API mode
- **`Cython`:** compiling Python to C; `.pyx` files; type annotations for speed; `cdef`, `cpdef`
- **`mypyc`:** compiling typed Python to C extensions; drop-in for mypy-annotated code
- **`numpy` C API awareness:** why NumPy operations are fast; ufuncs; vectorization vs Python loops
- **When to reach for C:** profiler shows the bottleneck; pure Python exhausted; not premature optimization

---

## FastAPI Strength — After This Roadmap

> With Phases 1–6 complete, FastAPI takes roughly a week to learn properly.

### Prerequisites you'll already have
- Type hints → Pydantic just works
- Pydantic v2 → request/response models are natural
- `asyncio` + `async/await` → async endpoints are not foreign
- `asynccontextmanager` → `lifespan` events make sense
- SQLAlchemy + Alembic → DB layer ready
- `pytest` + `AsyncClient` + `dependency_overrides` → testing ready
- Docker + GitHub Actions → shipping ready

### What FastAPI adds on top
- `APIRouter`, `include_router()`, tags, prefix
- `Depends()` — you already know this from 8.3
- `HTTPException`, `status` codes, `JSONResponse`
- Middleware: `BaseHTTPMiddleware`, CORS, trusted hosts
- Background tasks: `BackgroundTasks`
- OpenAPI / Swagger UI — automatic from your Pydantic models
- File uploads: `UploadFile`, `File()`
- WebSocket endpoints
- Lifespan events: `@asynccontextmanager` on startup/shutdown

---

## Quick Reference — Hiring Priority

| Phase | Topics | Tag | Interview Weight |
|-------|--------|-----|-----------------|
| 0 | Beginner foundation | BEGINNER | Prerequisite (not interviewed) |
| 1 | Python core depth | HIRE | Very high |
| 2 | Standard library | HIRE | High |
| 3 | Concurrency & async | HIRE | Very high |
| 4 | Testing & quality | HIRE | Very high |
| 5 | Data & DB layer | HIRE | High |
| 6 | Packaging & DevOps | HIRE | Medium-high |
| 7 | Data structures & algorithms | HIRE | Very high (coding rounds) |
| 8 | Design patterns | CAREER+ | Medium (senior signal) |
| 9 | CPython internals | CAREER+ | Low (staff signal) |

**Phase 0 = foundation · Phases 1–7 = hire-ready · Phases 8–9 = career insurance.**

---

*Total phases: 10 · Topics: 64 · Subtopics: ~450+ · Full beginner → senior Python engineer*