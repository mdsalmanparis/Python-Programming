# Python Syllabus — Product Company Track
### Mohamed Salman Paris M | Date: 6th June 2026
> **One rule above everything:** You do not switch languages. You do not start a new course.
> If something feels hard, you bring it to your mentor first. Difficulty is not a signal to quit — it is a signal to ask.

---

## How This Syllabus Works

This roadmap has two tracks that run in parallel once you hit the gateway:

```
Phase 0  ──►  Phase 1  ──►  ⬛ DSA GATEWAY  ──►  DSA (Side Track)
                                                 +
                                              Phase 2–6 (Main Track)
```

- **Phase 0 + Phase 1** — complete these fully before anything else
- **DSA Gateway** — a checklist you must pass before DSA begins
- **After the gateway** — DSA runs as a daily side track (1 hr/day) while you continue Phase 2–6 as your main track
- **Phase 7 (DSA)** — listed separately at the end as a structured parallel curriculum

---

## Tag Reference

| Tag | Meaning |
|---|---|
| `[CORE]` | Mandatory — complete before DSA gateway |
| `[DSA-NEEDED]` | Specifically needed to begin DSA problems |
| `[HIRE]` | Tested directly in interviews |
| `[JOB]` | Day-one on the job, lower interview weight |
| `[CAREER+]` | Post-hire — do not study now |

---
---

# ⬛ PHASE 0 — Absolute Beginner Foundation
### Complete 100% · ~3 weeks · `[CORE]`

> Your true starting point. Nothing here is optional. Do not skip, do not skim.

---

### 0.1 Setup & First Program

- Installing Python 3.11+, verifying with `python --version`
- VS Code setup — Python extension, select interpreter, run with F5 or terminal
- REPL — experimenting line by line with `python` in terminal
- First script — `print("Hello, World!")`, save as `.py`, run with `python filename.py`
- Comments — `#` single-line, `"""..."""` multi-line docstrings
- `print()` — multiple arguments, `sep=`, `end=`, f-strings preview
- `input()` — reading user input, always returns a string
- Indentation — 4 spaces standard, what `IndentationError` means

---

### 0.2 Variables & Basic Data Types

- Variables — naming rules, snake_case, multiple assignment `a, b = 1, 2`, swap `a, b = b, a`
- `int`, `float`, `str`, `bool`, `None` — what each one is
- Floating point imprecision — `0.1 + 0.2 != 0.3` and why
- `type()` and `isinstance()` — checking and validating types
- Type conversion — `int()`, `float()`, `str()`, `bool()`
- Arithmetic operators — `+`, `-`, `*`, `/`, `//` floor div, `%` modulo, `**` power
- Operator precedence — PEMDAS, using parentheses to be explicit
- Comparison operators — `==`, `!=`, `<`, `>`, `<=`, `>=` — always return bool
- Logical operators — `and`, `or`, `not`, short-circuit evaluation
- Augmented assignment — `+=`, `-=`, `*=`, `//=`

---

### 0.3 Strings in Depth

- String creation — single, double, triple quotes for multi-line
- Indexing — `s[0]` first, `s[-1]` last, `IndexError` if out of range
- Slicing — `s[start:stop:step]`, reverse with `s[::-1]`
- `len(s)` — number of characters
- f-strings — `f"Hello, {name}!"`, expressions inside `{}`
- String concatenation inefficiency in loops — use `join()` instead
- String repetition — `"ha" * 3` → `"hahaha"`
- Must-know methods:
  - Case: `.upper()`, `.lower()`, `.capitalize()`, `.title()`
  - Search: `.find()`, `.index()`, `.count()`, `.startswith()`, `.endswith()`, `in` operator
  - Clean: `.strip()`, `.lstrip()`, `.rstrip()`
  - Replace: `.replace(old, new)`
  - Split/join: `.split(sep)`, `sep.join(list)`
  - Check: `.isdigit()`, `.isalpha()`, `.isalnum()`, `.isspace()`
- String immutability — cannot change a character in-place, must create a new string
- `str.format()` — older alternative, `"Hello, {}".format(name)`
- Escape characters — `\n`, `\t`, `\\`, raw strings `r"..."`

---

### 0.4 Lists

- Creating — `[1, 2, 3]`, mixed types, empty list `[]`
- Indexing and slicing — same rules as strings
- Mutability — lists can be changed in-place unlike strings
- `len()` — number of elements
- Adding elements — `.append()` O(1), `.insert()` O(n), `.extend()`
- Removing elements — `.remove()`, `.pop()`, `del`, `.clear()`
- Searching — `value in my_list`, `.index()`, `.count()`
- Sorting — `.sort()` in-place vs `sorted()` returns new list, `key=`, `reverse=`
- Reversing — `.reverse()` in-place, `[::-1]` new list
- Shallow copy — `.copy()` or `[:]`, why `b = a` does NOT copy
- Nested lists — matrix access with `matrix[row][col]`
- Unpacking — `a, b, c = [1, 2, 3]`, `first, *rest = [1, 2, 3, 4]`

---

### 0.5 Tuples, Sets & Dictionaries

**Tuples**
- Creating — `(1, 2, 3)`, single-element needs comma `(1,)`
- Immutability — `TypeError` if you try to change
- When to use — fixed collections, returning multiple values, dict keys
- Unpacking — `x, y = (10, 20)`

**Sets**
- Creating — `{1, 2, 3}`, empty set must be `set()` not `{}`
- No duplicates, no indexing, unordered
- O(1) membership test — faster than list's O(n)
- Adding/removing — `.add()`, `.remove()` (KeyError), `.discard()` (safe)
- Set operations — union `|`, intersection `&`, difference `-`, symmetric difference `^`
- `frozenset` — immutable set, can be used as dict key

**Dictionaries**
- Creating — `{"name": "Alice", "age": 25}`, `{}` empty, `dict(name="Alice")`
- Accessing — `d["key"]` raises KeyError, safe: `d.get("key", default)`
- Adding/updating — `d["city"] = "Chennai"`, overwrites if key exists
- Deleting — `del d["age"]`, `.pop("age")`, `.pop("age", None)` safe
- Checking keys — `"name" in d` is O(1)
- Iterating — `for key in d:`, `for key, value in d.items():`, `for value in d.values():`
- `.keys()`, `.values()`, `.items()` — view objects
- `.update(other_dict)` — merging
- Dict ordering — insertion order preserved since Python 3.7
- Nested dicts — `d["address"]["city"]`, common in JSON data

---

### 0.6 Conditional Statements

- `if` / `elif` / `else` — first `True` wins
- Truthiness and falsiness — `0`, `0.0`, `""`, `[]`, `{}`, `set()`, `None` are all falsy
- Ternary expression — `value = "yes" if condition else "no"`
- Nested if — use sparingly, deep nesting is a refactor signal
- `match/case` (Python 3.10+) — know it exists, lower priority for now

---

### 0.7 Loops

**for loops**
- Iterating lists, strings, dicts
- `range()` — `range(5)`, `range(1,6)`, `range(0,10,2)`
- `enumerate()` — `for i, val in enumerate(lst):` index + value together
- `zip()` — `for a, b in zip(list1, list2):` pairs from two lists

**while loops**
- `while condition:` — runs until condition is False
- Infinite loop + break — `while True: ... if done: break`
- `break` — exit immediately
- `continue` — skip current iteration
- `else` on loops — runs only if loop completed without `break`

---

### 0.8 Functions — Basics

- `def`, `return`, function returns `None` if no return statement
- Parameters vs arguments, default values, keyword arguments
- Multiple return values — returns a tuple, unpack with `a, b = func()`
- Docstrings — first line of the function body
- Scope — local vs global, `global` keyword (use sparingly)
- `pass` statement — empty function/class placeholder

---

### 0.9 Built-in Functions You Must Know

- `len`, `range`, `type`, `isinstance` — use constantly
- `int()`, `float()`, `str()`, `bool()`, `list()`, `tuple()`, `set()`, `dict()` — type conversion
- `min()`, `max()` — on any iterable, `key=` parameter
- `sum()`, `abs()`, `round()`
- `sorted()` — returns new sorted list, `key=`, `reverse=`
- `reversed()` — returns iterator, wrap in `list()` to see it
- `enumerate()`, `zip()` — use naturally in every loop
- `map()`, `filter()` — know them, prefer comprehensions in interviews
- `any()`, `all()` — short-circuit boolean checks on iterables
- `help()`, `dir()` — inline documentation

---

### 0.10 File I/O Basics

- Always use `with open("file.txt") as f:` — auto-closes even on error
- Modes — `"r"` read, `"w"` write (overwrites), `"a"` append, `"rb"`/`"wb"` binary
- `f.read()`, `f.readlines()`, iterate with `for line in f:` (best for large files)
- `f.write()` — no automatic newline, add `\n` yourself
- Always specify `encoding="utf-8"`

---

### 0.11 Error Handling Basics

- Common exceptions — `ValueError`, `TypeError`, `IndexError`, `KeyError`, `FileNotFoundError`, `ZeroDivisionError`, `AttributeError`, `NameError`
- `try` / `except` / `else` / `finally` — execution order
- Always catch specific exceptions — never bare `except:`
- `except Exception as e:` — capture and print the error message
- `raise` — manually raising an exception with a message

---

### 0.12 Modules & Imports

- `import module`, `from module import name`, `import as alias`
- Avoid `from module import *` in real code
- Creating your own module — any `.py` file is a module
- `if __name__ == "__main__":` — runs only when executed directly
- Standard library to know now — `math`, `random`, `os`, `sys`, `datetime`, `time`
  - `math.sqrt()`, `math.floor()`, `math.ceil()`, `math.pi`
  - `random.randint()`, `random.choice()`, `random.shuffle()`
  - `os.path.exists()`, `os.listdir()`, `os.makedirs()`
  - `sys.argv`, `sys.exit()`
- `pip install` — installing third-party packages

---
---

# ⬛ PHASE 1 — Python Core Depth
### ~4 weeks · `[CORE]` + `[HIRE]`

> The filter layer every backend interview starts with. No gaps, no hand-waving.

---

### 1.1 Data Types, Mutability & Memory `[CORE]` `[HIRE]`

- `id()`, `is` vs `==` — identity vs equality, when two variables point to the same object
- Mutability rules — mutable: `list`, `dict`, `set` | immutable: `int`, `str`, `tuple`, `frozenset`
- Mutable default argument trap — `def f(x=[]):` is a bug, always use `None` as sentinel
- Shallow copy vs deep copy — `copy.copy()`, `copy.deepcopy()`, when each is needed
- `del` keyword — removes the name binding, not necessarily the object

---

### 1.2 Control Flow, Comprehensions & Generator Expressions `[CORE]` `[HIRE]`

- List comprehensions — `[expr for x in iterable if condition]`, nested comprehensions
- Dict comprehensions — `{k: v for k, v in items}`, inverting a dict
- Set comprehensions — `{expr for x in iterable}`
- Generator expressions — `(expr for x in iterable)`, lazy evaluation, memory-efficient, single-use
- Walrus operator `:=` — assignment inside comprehensions and while loops
- `sorted()` vs `.sort()` — `key=`, `reverse=`, stability guarantee, `functools.cmp_to_key()`
- `zip(strict=True)` — Python 3.10+, raises ValueError on unequal lengths

---

### 1.3 Functions — `*args`, `**kwargs`, Closures, First-Class `[CORE]` `[HIRE]`

- Positional, keyword, default arguments — ordering rules
- `/` and `*` in signatures — positional-only and keyword-only parameters
- `*args` and `**kwargs` — packing, unpacking, forwarding patterns
- First-class functions — passing as arguments, storing in data structures
- Higher-order functions — functions that return functions
- Closures — captured variables, `nonlocal` keyword, cell objects
- Late binding gotcha — loop variable capture in lambdas and the fix
- `lambda` — use cases, limitations (single expression), when NOT to use
- `functools.partial()` — freezing arguments to create specialized callables
- Recursion — base case, call stack depth, `sys.setrecursionlimit()`, iterative refactor
- Function annotations — `def f(x: int) -> str:` — runtime vs static checking

---

### 1.4 OOP — Classes, Dunder Methods, Inheritance, MRO `[CORE]` `[HIRE]`

- Class definition — `class`, `__init__`, instance vs class vs static attributes
- `self` — what it is, how Python passes it automatically
- `@classmethod` vs `@staticmethod` vs instance method — when to use each
- `@property`, `@setter`, `@deleter` — computed attributes, validation on assignment
- Core dunder methods:
  - Representation — `__str__`, `__repr__`, `__format__`
  - Comparison — `__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`
  - Hashing — `__hash__`, why it's linked to `__eq__`, making objects dict keys/set members
  - Arithmetic — `__add__`, `__radd__`, `__iadd__`, `__mul__`
  - Container — `__len__`, `__getitem__`, `__setitem__`, `__delitem__`, `__contains__`, `__iter__`
  - Context manager — `__enter__`, `__exit__`
  - Callable — `__call__`
- Inheritance — single and multiple inheritance, `super()`
- MRO — C3 linearization, `ClassName.__mro__`, diamond problem
- Abstract base classes — `abc.ABC`, `@abstractmethod` — why they're used
- `dataclasses` — `@dataclass`, `field()`, `__post_init__`, `frozen=True`

---

### 1.5 Exceptions & Context Managers `[CORE]` `[HIRE]`

- Exception hierarchy — `BaseException` → `Exception` → specific types, `SystemExit`, `KeyboardInterrupt`
- `try` / `except` / `else` / `finally` — execution order, what `else` means
- Catching multiple exceptions — `except (TypeError, ValueError):`
- Exception chaining — `raise X from Y`, `__cause__` vs `__context__`, suppress with `raise X from None`
- `raise` vs `raise e` — re-raising, losing vs preserving traceback
- Custom exception classes — hierarchy design, adding context attributes
- Context managers — `__enter__`, `__exit__` protocol, the `with` statement
- `contextlib.contextmanager` — generator-based context managers with `@contextmanager`
- `contextlib.suppress()` — cleaner than `try/except: pass`

---

### 1.6 Iterators, `itertools` & Generators `[CORE]` `[HIRE]`

- Iterator protocol — `__iter__()` returns self, `__next__()` raises `StopIteration`
- `iter()` and `next()` — built-in wrappers, `next(it, default)` sentinel
- Writing a custom iterator class — stateful traversal example
- Generator functions — `yield`, `yield from`, return value in generators
- `itertools` — must-know set:
  - `chain()`, `chain.from_iterable()`
  - `islice()`, `takewhile()`, `dropwhile()`
  - `product()`, `permutations()`, `combinations()`
  - `groupby()` — requires sorted input
  - `zip_longest()`, `accumulate()`, `pairwise()` (3.10+)

---

### 1.7 Decorators `[CORE]` `[HIRE]`

- Decorator mechanics — a function that takes a function and returns a function
- `functools.wraps` — why it's required, what it preserves (`__name__`, `__doc__`, `__wrapped__`)
- Stacking decorators — application order (bottom-up), mental model
- Class-based decorators — `__init__` accepts the function, `__call__` wraps it
- Parametrized decorators — the triple-nested pattern
- `functools.lru_cache` and `functools.cache` — memoization, `cache_info()`, `cache_clear()`
- `functools.cached_property` — lazy attribute computation cached on the instance
- `functools.singledispatch` — type-based overloading
- Real-world patterns to implement yourself:
  - Timing/benchmarking decorator
  - Retry with exponential backoff
  - Access control/auth guard
  - Input validation decorator

---

### 1.8 Type Hints `[CORE]` `[HIRE]`

- Basic annotations — variables, function signatures, return types
- `Optional`, `Union`, `Any`, `ClassVar`, `Final`
- `Union` vs `X | Y` (3.10+) — modern union syntax
- `Optional[X]` = `X | None`
- `TypeVar` — generic functions and classes, bound TypeVars
- Generic classes — `class Stack(Generic[T]):`, `list[int]`, `dict[str, int]` (3.9+)
- `Protocol` — structural subtyping, duck typing with type checker, `runtime_checkable`
- `TypedDict` — typed dictionaries, `total=False` for optional keys
- `NamedTuple` — typed alternative to `collections.namedtuple`
- Running mypy — `mypy src/`, fixing common errors, inline `# type: ignore`

---
---

# 🚦 DSA GATEWAY CHECKLIST
### You cannot start DSA until every item here is solid

> This is not about being perfect. It is about being unblocked.
> Go through each item. If you can explain it and write it from scratch — tick it.
> If not — go back and fix it first.

---

**From Phase 0:**
- [ ] You can write a Python function from scratch with parameters, return values, and a docstring
- [ ] You understand list indexing, slicing, and the difference between `.sort()` and `sorted()`
- [ ] You can use `dict`, `set`, and `list` naturally — adding, removing, iterating
- [ ] You understand `for` loops, `while` loops, `break`, `continue`
- [ ] You can write a working `try/except` block

**From Phase 1:**
- [ ] You understand `is` vs `==` and can explain the mutable default argument trap
- [ ] You can write a list comprehension, dict comprehension, and generator expression
- [ ] You understand `*args` and `**kwargs` — can write a function that uses both
- [ ] You can write a class with `__init__`, a class method, a static method, and `__repr__`
- [ ] You understand MRO and can explain what happens in a diamond inheritance
- [ ] You can write a custom decorator using `functools.wraps`
- [ ] You can write a generator function using `yield`

**DSA-specific Python tools — must know cold:**
- [ ] `collections.defaultdict` — can explain when and why to use it
- [ ] `collections.Counter` — can use `most_common()` and counter arithmetic
- [ ] `collections.deque` — can explain O(1) append/pop from both ends
- [ ] `collections.OrderedDict` — know how `move_to_end()` works
- [ ] `heapq.heappush()` and `heapq.heappop()` — can implement a min-heap manually
- [ ] Max-heap trick with negation — can explain why Python only has min-heap
- [ ] `heapq.nlargest()` and `heapq.nsmallest()`
- [ ] `bisect.bisect_left()` and `bisect.bisect_right()` — can use for sorted array search

**Recursion readiness:**
- [ ] You can write a recursive function with a base case and explain the call stack
- [ ] You know what `sys.setrecursionlimit()` does and when you'd need it
- [ ] You can convert a simple recursive function to iterative

---

> **Once all boxes are checked — DSA begins as your side track (1 hr/day).**
> Main track continues with Phase 2 through 6 in parallel.

---
---

# ⬛ PHASE 2 — Standard Library Mastery
### ~3 weeks · `[HIRE]` + `[JOB]`

---

### 2.1 `collections` — Counter, defaultdict, deque, namedtuple `[HIRE]`

- `Counter` — counting hashables, arithmetic (`+`, `-`, `&`, `|`), `most_common(n)`, `update()` vs `subtract()`
- `defaultdict` — factory functions (`list`, `int`, `set`, `lambda`), difference from `dict.setdefault()`
- `deque` — O(1) append/pop from both ends, `maxlen` circular buffer, `rotate()`
- `namedtuple` — `_fields`, `_asdict()`, `_replace()`, `_make()`, when to prefer `dataclass`
- `OrderedDict` — `move_to_end()`, equality semantics differ from dict

---

### 2.2 `pathlib`, `os`, `shutil` `[JOB]`

- `pathlib.Path` — construction, `/` operator for joining, `.name`, `.parent`
- `Path.exists()`, `.is_file()`, `.is_dir()`
- `Path.read_text()`, `.write_text()`, `.read_bytes()`, `.write_bytes()`
- `Path.glob()`, `.rglob()` — recursive file discovery
- `Path.iterdir()` — listing directory contents
- `Path.mkdir(parents=True, exist_ok=True)`
- `Path.unlink()` — deleting a file
- `os.environ`, `os.getenv()` — environment variables
- `os.getcwd()`, `os.chdir()`
- `os.walk()` — recursive directory traversal
- `shutil.copy()`, `.copytree()`, `.rmtree()`, `.move()`
- `tempfile.TemporaryDirectory()`

---

### 2.3 `json`, `csv`, `python-dotenv` `[HIRE]`

- `json.loads()` / `json.dumps()` — `indent`, `sort_keys`, `ensure_ascii=False`, `default` for custom types
- `json.load()` / `json.dump()` — file-based, encoding considerations
- Custom JSON serializer — `json.JSONEncoder` subclass, `default()` method
- `csv.reader()` / `csv.writer()` — delimiter, quotechar
- `csv.DictReader()` / `csv.DictWriter()` — header row handling
- `python-dotenv` — `load_dotenv()`, `dotenv_values()`, `.env` file patterns
- `os.environ` vs `dotenv` — precedence, override behaviour

---

### 2.4 `datetime` `[HIRE]`

- `datetime.date`, `.time`, `.datetime`, `.timedelta` — construction, arithmetic
- `datetime.now()` vs `datetime.utcnow()` vs `datetime.now(tz=timezone.utc)` — why naive datetimes are dangerous
- `timezone.utc`, `timezone(timedelta(hours=N))`
- `strftime()` / `strptime()` — format codes, ISO 8601 patterns
- `isoformat()` and `fromisoformat()`

---

### 2.5 `re` — Regex `[HIRE]`

- Always use raw strings `r"..."` for regex patterns
- Character classes — `.`, `\d`, `\w`, `\s`, `\b`, `[...]`, `[^...]`
- Quantifiers — `*`, `+`, `?`, `{n}`, `{n,m}`, greedy vs lazy (`*?`, `+?`)
- Anchors — `^`, `$`, `\A`, `\Z`, `\b`
- `re.match()` vs `re.search()` vs `re.fullmatch()` — critical difference
- `re.findall()` vs `re.finditer()` — list vs iterator of Match objects
- `re.sub()`, `re.subn()` — substitution with string or callable
- `re.split()` — splitting on pattern
- Groups — `(...)`, named groups `(?P<name>...)`, non-capturing `(?:...)`
- `match.group()`, `.groups()`, `.groupdict()`, `.span()`
- Flags — `re.IGNORECASE`, `re.MULTILINE`, `re.DOTALL`
- `re.compile()` — precompiling for reuse

---

### 2.6 `logging` `[HIRE]`

- Log levels — `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`
- `logging.getLogger(__name__)` — logger hierarchy, root logger vs named loggers
- `basicConfig()` — quick setup, `level`, `format`, `filename`
- Handlers — `StreamHandler`, `FileHandler`, `RotatingFileHandler`, `NullHandler`
- Formatters — `%(asctime)s %(levelname)s %(name)s — %(message)s`
- `logging.config.dictConfig()` — production config as a dict
- Logger propagation — `logger.propagate = False`, preventing duplicate entries
- Library vs application logging — library best practice is only `NullHandler`
- `logger.exception()`, `exc_info=True` — logging exceptions with traceback

---

### 2.7 `functools` `[HIRE]`

- `functools.lru_cache(maxsize=...)` — memoization, `cache_info()`, `cache_clear()`
- `functools.cache` (3.9+) — unbounded LRU cache
- `functools.cached_property` — lazy attribute, descriptor protocol
- `functools.partial()` — partial application, use in callbacks and configuration
- `functools.singledispatch` — type-based overloading
- `functools.total_ordering` — implement `__eq__` and one comparison, get the rest free
- `functools.wraps` — copying function metadata in decorators

---

### 2.8 `contextlib` `[HIRE]`

- `contextlib.contextmanager` — `@contextmanager`, `yield` as the `with` block
- `contextlib.suppress(*exceptions)` — silently ignore specified exceptions
- `contextlib.nullcontext()` — no-op context manager for conditional `with` statements
- `contextlib.asynccontextmanager` — async version, used in FastAPI lifespan events

---
---

# ⬛ PHASE 3 — Concurrency & Async
### ~3 weeks · `[HIRE]` (very high weight)

> FastAPI is async-first. This phase is non-negotiable.

---

### 3.1 Threading — GIL, Locks, Race Conditions `[HIRE]`

- GIL — what it is, what it protects, why CPython has it
- I/O-bound vs CPU-bound — why threads help for I/O but not CPU
- `threading.Thread` — `target`, `args`, `kwargs`, `daemon`, `.start()`, `.join()`
- `threading.Lock` — `.acquire()`, `.release()`, always use `with lock:`
- Race conditions — shared counter example, why they're hard to reproduce
- `threading.RLock` — reentrant lock, when you need it
- `threading.Event` — signalling between threads, `.set()`, `.clear()`, `.wait()`
- `queue.Queue` — thread-safe FIFO, `.put()`, `.get()`, `.task_done()`, `.join()`
- `queue.PriorityQueue`, `queue.LifoQueue`
- Thread pool pattern — producer-consumer with Queue

---

### 3.2 Multiprocessing `[HIRE]`

- `multiprocessing.Process` — `target`, `args`, `.start()`, `.join()`, `.exitcode`
- `if __name__ == "__main__":` — why it's required on Windows/macOS
- `multiprocessing.Pool` — `pool.map()`, `pool.starmap()`, `pool.apply_async()`
- `concurrent.futures.ProcessPoolExecutor` — high-level process pool, `submit()`, `map()`
- `concurrent.futures.ThreadPoolExecutor` — thread pool with same API
- `concurrent.futures.Future` — `.result()`, `.done()`, `.cancel()`
- `concurrent.futures.as_completed()` — processing results as they finish

---

### 3.3 `asyncio` — Event Loop, Coroutines, Tasks `[HIRE]`

- `async def` and `await` — coroutine syntax, what `await` actually does
- Event loop — `asyncio.run()`, what it is
- `asyncio.Task` — wrapping coroutines for concurrent execution, `asyncio.create_task()`
- `asyncio.gather(*coros, return_exceptions=False)` — running concurrently, collecting results
- `asyncio.wait(tasks, return_when=...)` — `FIRST_COMPLETED`, `FIRST_EXCEPTION`, `ALL_COMPLETED`
- `asyncio.shield(coro)` — protecting a coroutine from cancellation
- `asyncio.timeout(seconds)` (3.11+) — `async with asyncio.timeout(5):`
- `asyncio.sleep(seconds)` — yielding control, `sleep(0)` to yield to event loop
- `asyncio.Event`, `asyncio.Lock`, `asyncio.Semaphore`, `asyncio.Condition`
- `asyncio.Queue` — async producer-consumer
- `asyncio.TaskGroup` (3.11+) — structured concurrency, automatic cancellation on failure
- `asyncio.to_thread()` — running blocking code without blocking the event loop
- Cancellation — `task.cancel()`, catching `asyncio.CancelledError`, cleanup

---

### 3.4 Async Patterns `[HIRE]`

- Async producer-consumer — multiple producers and consumers via `asyncio.Queue`
- Bounded concurrency with `asyncio.Semaphore` — limiting concurrent HTTP calls or DB queries
- Async worker pool — Queue + N worker tasks, graceful shutdown with sentinel values
- `asyncio.Event` for signalling — coordinating startup/shutdown between tasks
- Async context managers — `async with`, `__aenter__`, `__aexit__`
- Async iterators — `async for`, `__aiter__`, `__anext__`, async generators
- Running sync code safely — `asyncio.to_thread()` vs `loop.run_in_executor()`

---

### 3.5 `httpx` Async Client `[HIRE]`

- `httpx.AsyncClient` — `async with httpx.AsyncClient() as client:`, why client reuse matters
- Making requests — `.get()`, `.post()`, `.put()`, `.delete()`, `.patch()`
- Request options — `params`, `headers`, `json`, `data`, `timeout`, `follow_redirects`
- `httpx.Timeout` — connect vs read vs write vs pool timeout
- Response handling — `.status_code`, `.json()`, `.text`, `.content`, `.raise_for_status()`
- `httpx.HTTPStatusError` — catching 4xx/5xx
- Connection pooling — `limits=httpx.Limits(...)`
- Mocking httpx in tests — `pytest-httpx` or `respx`
- `httpx.Client` (sync) — same API without async

---
---

# ⬛ PHASE 4 — Testing & Code Quality
### ~2 weeks · `[HIRE]` (very high weight)

---

### 4.1 `pytest` — Fixtures, Parametrize, Markers `[HIRE]`

- Test discovery — `test_*.py`, `Test*`, `test_*`, running with `pytest`
- Assertions — plain `assert` with pytest rewriting
- `@pytest.fixture` — scope (`function`, `class`, `module`, `session`), `yield` for teardown
- `conftest.py` — shared fixtures, test configuration, visibility rules
- `@pytest.mark.parametrize` — parameter matrix, IDs
- `@pytest.mark.skip`, `@pytest.mark.skipif` — conditional skipping
- `@pytest.mark.xfail` — expected failures, `strict=True`
- Custom markers — registering in `pytest.ini`, running with `-m "marker_name"`
- `tmp_path` fixture — temporary directories in tests
- `capsys`, `capfd` — capturing stdout/stderr
- `monkeypatch` — patching attributes, env vars, dict items, `sys.path`
- `pytest.raises(ExceptionType)` — asserting exceptions, `.match`, `.value`
- `pytest.warns()` — asserting warnings
- `pytest.ini` / `pyproject.toml [tool.pytest.ini_options]` — configuration

---

### 4.2 Mocking — `unittest.mock` `[HIRE]`

- `unittest.mock.Mock` — creating mock objects, `assert_called_once_with()`
- `unittest.mock.MagicMock` — magic method support
- `unittest.mock.patch` — as decorator, as context manager, target string format
- `patch.object(obj, "attr")` — patching attributes on specific objects
- `patch.dict()` — temporarily modifying dicts, useful for `os.environ`
- `side_effect` — raising exceptions, returning different values on successive calls
- `return_value` — setting what the mock returns
- `spec=` and `spec_set=` — auto-spec from a real object
- `call_args_list` — inspecting all calls
- `AsyncMock` (3.8+) — mocking async functions
- Mocking patterns you must practise:
  - Mocking `datetime.now()`
  - Mocking database sessions
  - Mocking external API calls
  - Mocking file system operations

---

### 4.3 `pytest-asyncio` & `AsyncClient` for FastAPI Testing `[HIRE]`

- `pytest-asyncio` — `@pytest.mark.asyncio`, `asyncio_mode = "auto"` in config
- `async def test_*()` — writing async test functions
- Async fixtures — `@pytest.fixture` with `async def`, scoping async fixtures
- `httpx.AsyncClient` with `transport=ASGITransport` — testing FastAPI without a running server
- Testing async generators and background tasks

---

### 4.4 Coverage & Linting `[HIRE]`

- `pytest-cov` — `--cov=src --cov-report=html`, branch coverage vs line coverage
- Reading coverage reports — identifying untested branches
- `ruff` — `ruff check .`, `ruff format .`, replaces flake8 + isort + Black
- `ruff` config — `pyproject.toml [tool.ruff]`, `select`, `ignore`, `line-length`
- `mypy` — `mypy src/`, `--strict` flag, common error codes
- `mypy` config — `pyproject.toml [tool.mypy]`
- `pre-commit` — `.pre-commit-config.yaml`, `pre-commit install`, hooks on `git commit`
- Standard hook set — ruff, ruff-format, mypy, trailing-whitespace, end-of-file-fixer

---
---

# ⬛ PHASE 5 — Data & Database Layer
### ~3 weeks · `[HIRE]`

---

### 5.1 SQLAlchemy ORM `[HIRE]`

- `DeclarativeBase` (SQLAlchemy 2.0) — `class Base(DeclarativeBase): pass`
- Mapped columns — `Mapped[int]`, `mapped_column()`, `Optional[str]`
- `relationship()` — one-to-many, many-to-many, one-to-one, `back_populates`
- `ForeignKey` — `ondelete="CASCADE"`, `onupdate="CASCADE"`
- Association table — many-to-many via `Table()`, `secondary=`
- Session management — `Session`, `sessionmaker`
- `AsyncSession` + `async_session` — async SQLAlchemy with `asyncpg`
- CRUD — `session.add()`, `session.delete()`, `session.get()`, `session.execute(select(...))`
- `select()` — `where()`, `order_by()`, `limit()`, `offset()`, `join()`, `outerjoin()`
- Loading strategies:
  - Lazy loading (default) — N+1 query problem (know this cold)
  - Eager loading — `selectinload()`, `joinedload()`, `subqueryload()`
  - `lazy="raise"` — detecting accidental lazy loads in tests
- `expire_on_commit` — why objects become expired after commit, `.refresh()`

---

### 5.2 Alembic `[HIRE]`

- `alembic init alembic` — project structure, `alembic.ini`, `env.py`
- Configuring `env.py` — pointing at `Base.metadata` and database URL
- `alembic revision --autogenerate -m "description"` — generating migrations
- Reviewing generated migrations — never trust autogenerate blindly
- `alembic upgrade head` — running all pending migrations
- `alembic downgrade -1` — rolling back one migration
- `alembic current`, `alembic history` — inspecting migration state
- `alembic stamp head` — marking current DB state for existing DBs

---

### 5.3 Pydantic v2 `[HIRE]`

- `BaseModel` — defining models, required vs optional fields
- `Field()` — `default`, `default_factory`, `alias`, `gt`, `lt`, `min_length`, `max_length`, `pattern`
- `model_config = ConfigDict(...)` — `str_strip_whitespace`, `populate_by_name`, `from_attributes`
- `from_attributes=True` — ORM mode, reading from SQLAlchemy models
- `@field_validator` — `mode="before"` vs `mode="after"`, `@classmethod` requirement
- `@model_validator` — whole-model validation
- `@computed_field` — derived fields
- `model_dump()` — `include`, `exclude`, `by_alias`, `exclude_none`, `exclude_unset`
- `model_validate()` — from dict, `strict=True`
- `model_json_schema()` — generating JSON Schema
- Nested models — composition, `model_rebuild()` for forward references

---

### 5.4 Redis `[HIRE]`

- `redis-py` / `redis.asyncio` — connection, `Redis(host, port, db, decode_responses=True)`, `ConnectionPool`
- Core data types — String, Hash, List, Set — use cases for each
- `SET key value EX seconds` — setting with TTL, `SETEX`, `SET ... EX ... NX`
- `GET`, `DEL`, `EXISTS`, `EXPIRE`, `TTL`, `PERSIST`
- Cache-aside pattern — lazy loading
- Write-through caching
- `HSET`, `HGET`, `HGETALL` — hash operations for structured data
- `LPUSH`, `RPUSH`, `LPOP`, `BRPOP` — list operations, job queue pattern
- `SADD`, `SISMEMBER`, `SMEMBERS` — set operations, deduplication
- `redis.asyncio` client — `await client.set(...)` for FastAPI

---

### 5.5 PostgreSQL `[HIRE]`

- Index types — B-tree (default), Hash, GIN (JSONB/full-text), BRIN
- When to add an index — query patterns, cardinality, write vs read tradeoffs
- Composite indexes — column order matters, covering indexes
- `EXPLAIN` — reading query plan, node types (Seq Scan, Index Scan, Hash Join)
- `EXPLAIN ANALYZE` — actual execution times, rows estimate vs actual
- Transactions — `BEGIN`, `COMMIT`, `ROLLBACK`
- Isolation levels — `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`
- Concurrency anomalies — dirty read, non-repeatable read, phantom read
- JSONB — `@>`, `?`, `#>>` operators, GIN indexing, when appropriate
- `ON CONFLICT` — upsert, `DO NOTHING`, `DO UPDATE SET`

---
---

# ⬛ PHASE 6 — Packaging, Tooling & DevOps Basics
### ~2 weeks · `[HIRE]`

---

### 6.1 `pyproject.toml`, `uv`, Virtual Environments `[HIRE]`

- Virtual environments — `python -m venv .venv`, activation, why they exist
- `pyproject.toml` — `[build-system]`, `[project]`, `[project.optional-dependencies]`
- `uv` — fast installer, `uv pip install`, `uv pip sync`, `uv venv`
- `uv.lock` — deterministic installs, committing lockfiles
- Dependency groups — `dev`, `test`, `uv pip install .[dev]`

---

### 6.2 Docker `[HIRE]`

- Dockerfile instructions — `FROM`, `WORKDIR`, `COPY`, `RUN`, `CMD`, `ENTRYPOINT`, `EXPOSE`, `ENV`, `ARG`
- Layer caching — copy dependencies before source code
- `.dockerignore` — excluding `__pycache__`, `.git`, `.venv`, test files
- `CMD` vs `ENTRYPOINT` — exec form vs shell form, combining them
- `docker build -t name:tag .`, `docker run -p 8000:8000 --env-file .env name:tag`
- `docker-compose.yml` — services, networks, volumes, `depends_on`, `healthcheck`
- Docker Compose for development — mounting source code, hot reload with `uvicorn --reload`
- Postgres + Redis services in Compose — `image:`, `environment:`, `volumes:` for persistence

---

### 6.3 GitHub Actions — CI Pipeline `[HIRE]`

- `.github/workflows/ci.yml` — `on:`, `jobs:`, `steps:`
- Triggers — `push`, `pull_request`, `schedule`, `workflow_dispatch`
- `actions/checkout@v4`, `actions/setup-python@v5`
- Caching dependencies with `actions/cache`
- CI steps — checkout → set up Python → install deps → ruff → mypy → pytest --cov
- Environment secrets — `${{ secrets.DATABASE_URL }}`, never hardcode credentials

---

### 6.4 12-Factor App & Config `[HIRE]`

- Config from environment — never hardcode, `os.environ`, `pydantic-settings`
- `pydantic-settings BaseSettings` — auto-loading from `.env`, type validation of config
- `.env` files — `.env`, `.env.test`, `.env.production`, never commit, `.env.example` as template

---
---

# ⚡ DSA — PARALLEL SIDE TRACK
### Starts after DSA Gateway is passed · 1 hr/day alongside Phase 2–6

> Study order matters. Go through topics in exactly this sequence.
> Every topic: learn the pattern first, then solve problems. Never the reverse.

---

### DSA.1 Arrays & Strings `[HIRE]`

- Python `list` internals — dynamic array, amortised O(1) append
- Two-pointer technique — opposite ends, same direction
- Sliding window — fixed size vs variable size
- Prefix sums — subarray sum queries in O(1)
- Kadane's algorithm — maximum subarray sum
- Boyer-Moore voting — majority element O(n) time O(1) space
- In-place array manipulation — rotate array, move zeros, next permutation
- String operations — reversal, split/join, Counter for anagram checks
- Sliding window on strings — minimum window substring, longest substring with k distinct
- Two-sum / three-sum — hash map for O(n), sorting + two pointer
- Binary search on arrays — `bisect.bisect_left()`, search rotated sorted array

---

### DSA.2 Hash Maps & Hash Sets `[HIRE]`

- Python `dict` internals — hash table, O(1) average, collision handling
- `set` internals — hash table with only keys
- Two-sum with hash map — classic O(n) pattern
- Frequency counting — `collections.Counter`, top-k with `most_common()`
- Grouping / bucketing — `defaultdict(list)`, group anagrams
- Sliding window + hash map — character frequency in window
- Cycle detection with visited set — O(1) lookup with `set()`
- Hash map for memoization — manual vs `functools.lru_cache`
- `frozenset` as dict key — hashing collections

---

### DSA.3 Stacks & Queues `[HIRE]`

- Stack with Python `list` — `append()` / `pop()`
- `collections.deque` — O(1) from both ends, when to prefer over list
- Monotonic stack — next greater element, largest rectangle in histogram
- Stack for matching — valid parentheses patterns
- Implement queue using two stacks
- Implement stack using two queues
- Min stack — O(1) `getMin()` with auxiliary stack
- BFS with queue — `collections.deque`, level-order processing
- `heapq` module — min-heap, `heappush()`, `heappop()`, `heapify()`
- Max-heap with negation trick
- Priority queue patterns — k largest, merge k sorted lists, top k frequent
- `heapq.nlargest()`, `heapq.nsmallest()`

---

### DSA.4 Linked Lists `[HIRE]`

- Implementing singly linked list — `Node` class with `val` and `next`
- Implementing doubly linked list — `prev` and `next` pointers
- Traversal, insertion, deletion — time complexities
- Fast and slow pointer (Floyd's) — cycle detection, cycle start, middle node
- Reversing — iterative (three-pointer), recursive
- Merging two sorted lists — dummy head pattern
- Finding nth node from end — two-pointer with gap
- LRU cache — doubly linked list + dict, O(1) get and put

---

### DSA.5 Binary Search `[HIRE]`

- Find exact value
- Find leftmost / rightmost occurrence
- First true in `[False, False, True, True]` — search space bisection
- `bisect.bisect_left()`, `bisect.bisect_right()`, `bisect.insort()`
- Binary search on answer — minimum capacity to ship, Koko eating bananas
- Built-in sorting — `sorted()`, `.sort()`, Timsort, stability
- Custom sort key — `key=lambda x: (x[1], x[0])`, `functools.cmp_to_key()`

---

### DSA.6 Trees `[HIRE]`

- Binary tree node — `TreeNode(val, left=None, right=None)`
- Tree traversals — recursive and iterative:
  - Inorder (left → root → right) — sorted output for BST
  - Preorder (root → left → right) — tree serialization
  - Postorder (left → right → root) — deletion
  - Level-order (BFS) — `collections.deque`
- Height, depth, diameter of binary tree
- Balanced binary tree check
- BST properties — inorder is sorted
- BST operations — search, insert, delete (three cases)
- Lowest Common Ancestor (LCA)
- Path sum problems
- Serialize and deserialize binary tree
- Trie (Prefix Tree):
  - `TrieNode` with `children: dict` and `is_end: bool`
  - `insert()`, `search()`, `startsWith()` — all O(L)

---

### DSA.7 Recursion & Backtracking `[HIRE]`

- Recursion anatomy — base case, recursive case, call tree
- Backtracking template:
  ```
  def backtrack(state, choices):
      if is_solution(state): record(state); return
      for choice in choices:
          make(choice)
          backtrack(new_state, remaining_choices)
          undo(choice)
  ```
- Subsets / power set — include/exclude pattern
- Permutations — swapping in-place
- Combinations — k of n
- N-Queens — row-by-row, column + diagonal sets
- Word search in grid — DFS + visited set
- Palindrome partitioning
- Pruning — cutting branches early with sorted input + remaining sum check

---

### DSA.8 Graphs `[HIRE]`

- Representations — adjacency list (`defaultdict(list)`), adjacency matrix
- BFS — `collections.deque`, visited set, shortest path in unweighted graph
- DFS — recursive with visited set, iterative with explicit stack
- Cycle detection — undirected (parent tracking), directed (white-gray-black)
- Topological sort — DFS-based, Kahn's algorithm (BFS with in-degree)
- Union-Find (DSU) — `find()` with path compression, `union()` with rank
- Dijkstra's — `heapq` + distance dict, O((V+E) log V)
- Bellman-Ford — negative weights, O(VE)
- Grid as graph — 4-directional, island counting, flood fill, BFS for shortest path
- Bipartite check — two-coloring with BFS/DFS

---

### DSA.9 Dynamic Programming `[HIRE]`

- DP mindset — optimal substructure + overlapping subproblems
- Top-down with `@lru_cache` / `@cache` — cleanest Python DP
- 1D DP patterns:
  - Fibonacci / climbing stairs
  - House robber
  - Coin change (unbounded knapsack)
  - Longest increasing subsequence (LIS) — O(n²) and O(n log n) with `bisect`
- 2D DP patterns:
  - 0/1 knapsack + space optimization to 1D
  - Unique paths / grid DP
  - Edit distance (Levenshtein)
  - Longest common subsequence (LCS)
- String DP — palindromic substrings
- State machine DP — best time to buy/sell stock variants
- Space optimization — rolling array

---

### DSA.10 Bit Manipulation `[HIRE]`

- Bitwise operators — `&`, `|`, `^`, `~`, `<<`, `>>`
- Check if bit i is set — `n & (1 << i)`
- Set bit i — `n | (1 << i)`
- Clear bit i — `n & ~(1 << i)`
- Toggle bit i — `n ^ (1 << i)`
- Check power of 2 — `n & (n-1) == 0`
- Clear lowest set bit — `n & (n-1)`
- XOR properties — `a ^ a = 0`, `a ^ 0 = a`
- Find single number in array where all others appear twice
- Bitmask for subsets — iterating all subsets `0` to `2^n - 1`
- `bin()`, `int('...', 2)` — conversion
- `int.bit_count()` (3.10+) — built-in popcount

---

### DSA.11 Sorting Algorithms (Implement from Scratch) `[HIRE]`

- Merge sort — divide and conquer, stable, O(n log n), used in count inversions
- Quick sort — partition, pivot choice, O(n²) worst case
- Quick select — O(n) average for kth largest/smallest
- Counting sort / radix sort — O(n) for bounded integers
- Cyclic sort — arrays with values in range `[1, n]`, find missing/duplicate

---
---

# ⬛ PHASE 8 — Design Patterns (Awareness Only)
### Read after Phase 1–6. Deep study only after hire. · `[CAREER+]`

---

### 8.1 SOLID Principles — Know the Concepts `[HIRE]` (light)

- **S** — Single Responsibility: one class, one reason to change
- **O** — Open/Closed: extend without modifying, `Protocol`, `ABC`, `@singledispatch`
- **L** — Liskov Substitution: subclasses must behave correctly in place of parents
- **I** — Interface Segregation: small focused `Protocol` classes
- **D** — Dependency Inversion: depend on abstractions, inject via `__init__`

---

### 8.2 Repository, Unit of Work, Service Layer — Know the Pattern `[HIRE]` (light)

- Repository pattern — abstracting DB access behind an interface
- Unit of Work — wrapping a DB transaction, `uow.commit()`, `uow.rollback()`
- Service layer — orchestrating business logic, no DB or HTTP code in services
- FastAPI flow — `router → service → repository → DB`

---

### 8.3+ Everything Else in Phase 8 · `[CAREER+]`

> Dependency injection deep dive, Celery, event-driven patterns, task queues —
> all post-hire. Do not study now.

---
---

# ✗ PHASE 9 — CPython Internals
### Entirely `[CAREER+]` · Do not open until 12+ months on the job

> cProfile, dis module, GIL internals, nogil builds, Cython, ctypes —
> none of this appears in product company interviews at your target band.
> It will be here when you are ready for staff-level work.

---
---

# FastAPI — After Phase 1–6
### One week to learn properly once prerequisites are solid

> With Phases 1–6 complete, every FastAPI concept maps to something you already know.

| FastAPI Concept | Prerequisite Already Done |
|---|---|
| Type hints on routes | Phase 1.8 |
| Request/response models | Phase 5.3 Pydantic v2 |
| Async endpoints | Phase 3.3 asyncio |
| Lifespan events | Phase 2.8 asynccontextmanager |
| DB layer | Phase 5.1 SQLAlchemy + 5.2 Alembic |
| Testing routes | Phase 4.3 pytest-asyncio + AsyncClient |
| Docker + CI | Phase 6.2 + 6.3 |

**What FastAPI adds on top:**
- `APIRouter`, `include_router()`, tags, prefix
- `Depends()` — dependency injection
- `HTTPException`, `status` codes, `JSONResponse`
- Middleware — `BaseHTTPMiddleware`, CORS, trusted hosts
- Background tasks — `BackgroundTasks`
- OpenAPI / Swagger UI — automatic from Pydantic models
- File uploads — `UploadFile`, `File()`
- WebSocket endpoints

---
---

# Quick Reference — Study Priority

| Phase | Topic | Tag | Interview Weight |
|---|---|---|---|
| 0 | Beginner foundation | CORE | Prerequisite |
| 1 | Python core depth | HIRE | Very High |
| DSA Gateway | Python tools for DSA | DSA-NEEDED | Blocker |
| 2 | Standard library | HIRE | High |
| 3 | Concurrency & async | HIRE | Very High |
| 4 | Testing & quality | HIRE | Very High |
| 5 | Data & DB layer | HIRE | High |
| 6 | Packaging & DevOps | HIRE | Medium-High |
| DSA | Data structures & algorithms | HIRE | Very High (coding rounds) |
| 8 (light) | SOLID + Repository pattern | HIRE (light) | Medium |
| 8 (deep) | Design patterns depth | CAREER+ | Post-hire |
| 9 | CPython internals | CAREER+ | Staff level |

---

*Phase 0 + 1 = foundation · DSA Gateway = unlock · Phase 2–6 + DSA parallel = hire-ready · Phase 8–9 = career insurance*
