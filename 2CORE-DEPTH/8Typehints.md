Let's go. Full topic, nothing skipped.

---

## 1.8 Type Hints

---

## Why Type Hints Exist

Python is dynamically typed — variables have no declared type. Type hints are **optional annotations** that tell humans and tools what types are expected. Python itself ignores them at runtime.

```python
# Without type hints — what does this accept? What does it return?
def process(data, threshold, label):
    ...

# With type hints — immediately clear
def process(data: list[float], threshold: float, label: str) -> dict[str, float]:
    ...
```

**Three consumers of type hints:**
1. **You and your colleagues** — documentation that never goes stale
2. **Your IDE** — autocomplete, method suggestions, inline error detection
3. **Mypy / Pyright** — static analysis catches bugs before runtime

---

## Basic Annotations

### Variable annotations

```python
# Syntax: name: type = value
age:    int   = 25
name:   str   = "Mohamed"
score:  float = 88.5
active: bool  = True

# Annotation without assignment — declares type but creates no value
x: int          # valid — just declares the type
print(x)        # NameError — x has no value

# Accessing annotations at runtime
print(__annotations__)    # {'age': <class 'int'>, 'name': <class 'str'>, ...}
```

### Function annotations

```python
def add(a: int, b: int) -> int:
    return a + b

def greet(name: str, times: int = 1) -> str:
    return (f"Hello, {name}! " * times).strip()

def log(message: str) -> None:         # -> None: returns nothing
    print(message)

def find(items: list, target: object) -> int | None:   # might return None
    try:
        return items.index(target)
    except ValueError:
        return None

# Inspect annotations
print(add.__annotations__)
# {'a': <class 'int'>, 'b': <class 'int'>, 'return': <class 'int'>}
```

**Python does NOT enforce annotations at runtime:**

```python
def add(a: int, b: int) -> int:
    return a + b

add("hello", " world")    # "hello world" — no error at runtime
# Mypy would flag this: Argument 1 has incompatible type "str"; expected "int"
```

---

## `Optional`, `Union`, `Any`, `ClassVar`, `Final`

### `Optional[X]` — X or None

```python
from typing import Optional

def find_user(user_id: int) -> Optional[str]:   # returns str or None
    if user_id == 1:
        return "Mohamed"
    return None

# Optional[X] is exactly equivalent to Union[X, None]
# In Python 3.10+: X | None is the preferred syntax
```

### `Union[X, Y]` — One of Several Types

```python
from typing import Union

def process(value: Union[int, float, str]) -> str:
    return str(value)

# Python 3.10+ — use | syntax instead
def process(value: int | float | str) -> str:
    return str(value)
```

### `Any` — Opts Out of Type Checking

```python
from typing import Any

def accept_anything(value: Any) -> Any:
    return value    # no type checking on this at all

# Use sparingly — defeats the purpose of type hints
# Legitimate uses:
# - Interop with untyped third-party code
# - Dynamic structures where the type genuinely varies
# - Gradual typing — annotate a little at a time

def legacy_wrapper(data: Any) -> Any:
    return external_library.process(data)   # library has no stubs
```

**`Any` vs `object`:**

```python
from typing import Any

def f(x: Any) -> None:
    x.anything()      # mypy: OK — Any suppresses all checks

def g(x: object) -> None:
    x.anything()      # mypy: ERROR — object has no attribute 'anything'
                      # object is the actual base class, not "skip checking"
```

### `ClassVar` — Class-Level Attribute

```python
from typing import ClassVar

class Config:
    MAX_SIZE: ClassVar[int] = 100    # class variable, not instance variable
    name: str                         # instance variable

    def __init__(self, name: str):
        self.name = name

# Mypy knows MAX_SIZE is not per-instance
c = Config("test")
c.MAX_SIZE = 200    # mypy warning: Cannot assign to a ClassVar
```

### `Final` — Cannot Be Reassigned

```python
from typing import Final

MAX_CONNECTIONS: Final = 10
PI: Final[float] = 3.14159

MAX_CONNECTIONS = 20    # mypy error: Cannot assign to final name "MAX_CONNECTIONS"

# Final on class attributes
class Config:
    DEBUG: Final = False
```

---

## `Union` vs `X | Y` — Modern Union Syntax

```python
from typing import Union

# Old style (works in all Python 3.x)
def f(x: Union[int, str]) -> Union[int, str]:
    return x

# New style (Python 3.10+) — preferred
def f(x: int | str) -> int | str:
    return x

# None is written as None, not type(None)
def g(x: int | None) -> str | None:
    if x is None:
        return None
    return str(x)

# Equivalent old style
def g(x: Optional[int]) -> Optional[str]:
    ...
```

**Using new syntax in older Python with `from __future__`:**

```python
from __future__ import annotations   # must be first line of file

# Now | syntax works even on Python 3.8, 3.9
def process(value: int | str | None) -> list[int] | None:
    ...

# Caveat: annotations are now strings at runtime (lazy evaluation)
# They work for mypy but not for runtime inspection like Pydantic v1
```

---

## `TypeVar` — Generic Functions and Classes

`TypeVar` defines a placeholder type that stays consistent within a function or class.

### Generic Functions

```python
from typing import TypeVar

T = TypeVar("T")

def first(items: list[T]) -> T:
    return items[0]

# TypeVar preserves the relationship:
# if input is list[int], output is int
# if input is list[str], output is str

result = first([1, 2, 3])       # mypy knows result is int
result = first(["a", "b"])      # mypy knows result is str
result = first([1, "mixed"])    # mypy knows result is int | str
```

**Without TypeVar — type information is lost:**

```python
from typing import Any

def first_any(items: list[Any]) -> Any:   # less useful
    return items[0]

result = first_any([1, 2, 3])   # mypy: result is Any — no useful info
result + "hello"                 # no error — Any turns off checking
```

### Bound TypeVar — Restrict to a Type or Its Subclasses

```python
from typing import TypeVar

class Animal:
    def speak(self) -> str:
        return "..."

class Dog(Animal):
    def speak(self) -> str:
        return "woof"

class Cat(Animal):
    def speak(self) -> str:
        return "meow"

# bound=Animal means T can be Animal or any subclass of Animal
AnimalT = TypeVar("AnimalT", bound=Animal)

def make_speak(animal: AnimalT) -> AnimalT:
    print(animal.speak())
    return animal   # returns the SAME type — not just Animal

dog: Dog = make_speak(Dog())   # mypy knows return type is Dog, not just Animal
cat: Cat = make_speak(Cat())   # mypy knows return type is Cat
make_speak("hello")            # mypy error: str is not Animal
```

### Constrained TypeVar — Only Specific Types

```python
from typing import TypeVar

# T can ONLY be int or str — nothing else
IntOrStr = TypeVar("IntOrStr", int, str)

def double(x: IntOrStr) -> IntOrStr:
    return x * 2

double(5)          # OK → 10
double("hi")       # OK → "hihi"
double(3.14)       # mypy error: float is not int or str
```

---

## Generic Classes — `Generic[T]`

```python
from typing import TypeVar, Generic

T = TypeVar("T")

class Stack(Generic[T]):
    def __init__(self) -> None:
        self._items: list[T] = []

    def push(self, item: T) -> None:
        self._items.append(item)

    def pop(self) -> T:
        if not self._items:
            raise IndexError("stack is empty")
        return self._items.pop()

    def peek(self) -> T:
        return self._items[-1]

    def __len__(self) -> int:
        return len(self._items)

    def __bool__(self) -> bool:
        return bool(self._items)

# Usage with type inference
int_stack: Stack[int] = Stack()
int_stack.push(1)
int_stack.push(2)
value = int_stack.pop()    # mypy: value is int

str_stack: Stack[str] = Stack()
str_stack.push("hello")
str_stack.push(42)         # mypy error: int is not str
```

### Built-in Generic Types (Python 3.9+)

```python
# Before 3.9 — had to import from typing
from typing import List, Dict, Tuple, Set, FrozenSet

def f(items: List[int]) -> Dict[str, int]: ...

# Python 3.9+ — use built-in types directly (lowercase)
def f(items: list[int]) -> dict[str, int]: ...

# More examples
def g(
    data:  list[int],
    index: dict[str, list[int]],
    pair:  tuple[str, int],          # exactly (str, int)
    multi: tuple[int, ...],          # tuple of any length, all ints
    tags:  set[str],
    cache: frozenset[int],
) -> None: ...
```

### Multiple TypeVars

```python
from typing import TypeVar, Generic

K = TypeVar("K")
V = TypeVar("V")

class Pair(Generic[K, V]):
    def __init__(self, key: K, value: V) -> None:
        self.key   = key
        self.value = value

    def swap(self) -> "Pair[V, K]":
        return Pair(self.value, self.key)

    def __repr__(self) -> str:
        return f"Pair({self.key!r}, {self.value!r})"

p: Pair[str, int] = Pair("age", 25)
swapped: Pair[int, str] = p.swap()   # mypy knows the swapped types
```

---

## `Protocol` — Structural Subtyping

`Protocol` defines what methods/attributes an object must have — without requiring explicit inheritance. This is **duck typing with type safety**.

```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None: ...

class Circle:
    def draw(self) -> None:
        print("drawing circle")

class Square:
    def draw(self) -> None:
        print("drawing square")

class Triangle:
    def draw(self) -> None:
        print("drawing triangle")

def render(shape: Drawable) -> None:
    shape.draw()

# Circle, Square, Triangle all satisfy Drawable
# WITHOUT inheriting from Drawable — structural subtyping
render(Circle())     # OK
render(Square())     # OK

class NoDrawMethod:
    def paint(self) -> None:
        print("painting")

render(NoDrawMethod())   # mypy error: 'paint' but not 'draw'
```

**Protocol vs ABC:**

```python
from abc import ABC, abstractmethod
from typing import Protocol

# ABC — nominal subtyping (must inherit)
class AbstractShape(ABC):
    @abstractmethod
    def area(self) -> float: ...

class Circle(AbstractShape):     # MUST declare inheritance
    def area(self) -> float:
        return 3.14 * self.r ** 2

# Protocol — structural subtyping (just needs the right methods)
class HasArea(Protocol):
    def area(self) -> float: ...

class Rectangle:                 # NO inheritance needed
    def area(self) -> float:
        return self.w * self.h

def print_area(shape: HasArea) -> None:
    print(shape.area())

print_area(Rectangle())  # works — Rectangle has .area()
print_area(Circle())     # works — Circle has .area()
```

### `runtime_checkable` — Use Protocol with `isinstance()`

```python
from typing import Protocol, runtime_checkable

@runtime_checkable
class Sized(Protocol):
    def __len__(self) -> int: ...

print(isinstance([1, 2, 3], Sized))    # True  — list has __len__
print(isinstance("hello", Sized))      # True  — str has __len__
print(isinstance(42, Sized))           # False — int has no __len__

# Without @runtime_checkable:
isinstance([], Sized)    # TypeError: Protocols with non-method members don't support issubclass
```

### Protocol with Attributes

```python
from typing import Protocol

class HasNameAndScore(Protocol):
    name:  str
    score: float

    def grade(self) -> str: ...

class Student:
    def __init__(self, name: str, score: float):
        self.name  = name
        self.score = score

    def grade(self) -> str:
        return "A" if self.score >= 90 else "B"

def process(entity: HasNameAndScore) -> None:
    print(f"{entity.name}: {entity.grade()}")

process(Student("Mohamed", 88))   # OK — has name, score, grade()
```

---

## `TypedDict` — Typed Dictionaries

For when you use dicts as structured records (common with JSON APIs).

```python
from typing import TypedDict

class Student(TypedDict):
    name:  str
    score: float
    city:  str

# Now mypy knows the shape of this dict
s: Student = {"name": "Mohamed", "score": 88.0, "city": "Chennai"}

s["name"]        # mypy: str
s["score"]       # mypy: float
s["missing"]     # mypy error: key "missing" not in Student

def get_grade(student: Student) -> str:
    return "A" if student["score"] >= 90 else "B"

get_grade({"name": "Priya", "score": 92.0, "city": "Mumbai"})   # OK
get_grade({"name": "Arjun", "score": "high"})   # mypy error: score must be float
```

### `total=False` — Optional Keys

```python
from typing import TypedDict

class StudentRequired(TypedDict):
    name:  str
    score: float

class StudentOptional(TypedDict, total=False):
    city:     str
    nickname: str

# Combine required and optional
class Student(StudentRequired, StudentOptional):
    pass

s1: Student = {"name": "Mohamed", "score": 88.0}                    # OK
s2: Student = {"name": "Priya",   "score": 92.0, "city": "Mumbai"}  # OK
s3: Student = {"score": 88.0}    # mypy error: 'name' is required
```

### Inline syntax (Python 3.8+)

```python
from typing import TypedDict

Student = TypedDict("Student", {"name": str, "score": float, "city": str})

# Or with individual fields — same result as class syntax
Student = TypedDict("Student", name=str, score=float, city=str)
```

---

## `NamedTuple` — Typed Named Tuples

Like `collections.namedtuple` but with type annotations. Fields are positional AND named.

```python
from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float
    z: float = 0.0    # default value

p1 = Point(1.0, 2.0)
p2 = Point(1.0, 2.0, 3.0)
p3 = Point(x=4.0, y=5.0)

print(p1.x)        # 1.0   — named access
print(p1[0])       # 1.0   — positional access (it's a tuple)
print(p1)          # Point(x=1.0, y=2.0, z=0.0)

x, y, z = p2       # unpacking works
print(p1 == Point(1.0, 2.0, 0.0))   # True  — tuple equality

# Immutable
p1.x = 10          # AttributeError: can't set attribute
```

**`NamedTuple` vs `dataclass` vs `TypedDict`:**

```python
# NamedTuple — tuple with names. Immutable. Positional access works.
#              Use for: coordinates, records you'd naturally unpack

# dataclass — regular class. Mutable by default. More features.
#             Use for: objects with methods, mutable records

# TypedDict — dict with typed keys. Not a class, just a dict.
#             Use for: JSON-shaped data, API responses

from typing import NamedTuple
from dataclasses import dataclass

class PointTuple(NamedTuple):
    x: float
    y: float

@dataclass
class PointClass:
    x: float
    y: float

pt = PointTuple(1.0, 2.0)
pc = PointClass(1.0, 2.0)

x, y = pt    # ✓ — it's a tuple, unpacking works
x, y = pc    # ✗ — dataclass doesn't support positional unpacking by default

print(pt[0])    # 1.0 — tuple indexing works
print(pc[0])    # TypeError — dataclass doesn't support indexing
```

---

## `Annotated` — Attaching Metadata to Types

```python
from typing import Annotated

# Annotated[type, metadata...]
# The type is used by mypy. The metadata is used by libraries like Pydantic.

Age   = Annotated[int, "must be >= 0"]
Score = Annotated[float, "between 0 and 100"]

def create_student(name: str, age: Age, score: Score) -> None:
    pass

# Pydantic v2 uses Annotated for validation constraints:
from pydantic import Field
from typing import Annotated

PositiveInt = Annotated[int, Field(gt=0)]
ShortStr    = Annotated[str, Field(max_length=50)]
```

---

## `TYPE_CHECKING` — Avoiding Circular Imports

```python
from __future__ import annotations
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    # These imports only happen during type checking, not at runtime
    # Solves circular import problems
    from mymodule import SomeClass

def process(data: "SomeClass") -> None:    # string annotation — works at runtime too
    pass

# With 'from __future__ import annotations':
def process(data: SomeClass) -> None:      # no quotes needed
    pass
```

---

## Running Mypy

### Installation and Basic Usage

```bash
pip install mypy

# Check a single file
mypy script.py

# Check a directory
mypy src/

# Strict mode — catches the most issues
mypy --strict src/

# Ignore missing stubs for third-party libraries
mypy src/ --ignore-missing-imports
```

### `pyproject.toml` Configuration

```toml
[tool.mypy]
python_version    = "3.11"
strict            = true
warn_return_any   = true
warn_unused_ignores = true
ignore_missing_imports = true

# Per-module overrides
[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false
```

### Common Mypy Errors and Fixes

```python
# ── Error 1: Missing return type ──────────────────────────────────────────────
# error: Function is missing a return type annotation
def add(a, b):           # BAD
    return a + b

def add(a: int, b: int) -> int:    # GOOD
    return a + b

# ── Error 2: Incompatible types ───────────────────────────────────────────────
# error: Argument 1 to "int" has incompatible type "str | None"; expected "str | bytes"
def parse(s: str | None) -> int:
    return int(s)        # s might be None — can't pass None to int()

def parse(s: str | None) -> int:
    if s is None:
        return 0
    return int(s)        # now s is narrowed to str

# ── Error 3: Optional not handled ─────────────────────────────────────────────
# error: Item "None" of "str | None" has no attribute "upper"
def shout(name: str | None) -> str:
    return name.upper()  # name might be None

def shout(name: str | None) -> str:
    if name is None:
        return ""
    return name.upper()  # name narrowed to str

# ── Error 4: Return value not used ────────────────────────────────────────────
# error: "list[int].sort" does not return a value
nums = [3, 1, 2]
sorted_nums = nums.sort()   # .sort() returns None
# Fix:
sorted_nums = sorted(nums)  # sorted() returns a new list

# ── Error 5: Untyped function body ────────────────────────────────────────────
# error: Call to untyped function in typed context
# This means an imported function has no type annotations
# Fix: add # type: ignore or use a stub file

# ── Error 6: dict[str, X] doesn't have key ───────────────────────────────────
# error: TypedDict "Student" has no key "grade"
s: Student = {"name": "Mohamed", "score": 88.0}
print(s["grade"])    # error — "grade" not in Student definition
```

### Type Narrowing — Mypy Understands Control Flow

```python
def process(value: int | str | None) -> str:
    # At this point: value is int | str | None

    if value is None:
        return "nothing"
    # Now mypy knows: value is int | str (narrowed out None)

    if isinstance(value, int):
        return f"integer: {value * 2}"
    # Now mypy knows: value is str (narrowed out int)

    return value.upper()   # str — safe, mypy confirms
```

### `# type: ignore` — Suppress Specific Errors

```python
# Suppress a specific line
result = some_untyped_function()    # type: ignore

# With error code — more precise
result = some_untyped_function()    # type: ignore[no-untyped-call]

# Suppress an entire file — put at top
# mypy: ignore-errors

# AVOID: ignoring everything — defeats the purpose
```

### `cast()` — Tell Mypy the Type When It Can't Infer It

```python
from typing import cast

# When you know the type but mypy can't prove it
data = get_config()    # returns Any

name = cast(str, data["name"])    # tell mypy: trust me, this is a str
# No runtime effect — cast() is a no-op at runtime
```

---

## Putting It All Together

```python
from __future__ import annotations
from typing import TypeVar, Generic, Protocol, TypedDict, NamedTuple
from typing import Optional, ClassVar, Final
from dataclasses import dataclass, field
import json

# ── 1. Protocols — define interfaces ─────────────────────────────────────────

class Serializable(Protocol):
    def to_dict(self) -> dict[str, object]: ...

    @classmethod
    def from_dict(cls, data: dict[str, object]) -> Serializable: ...

class Comparable(Protocol):
    def __lt__(self, other: object) -> bool: ...
    def __eq__(self, other: object) -> bool: ...

# ── 2. TypedDict — JSON-shaped data ──────────────────────────────────────────

class ScoreRecord(TypedDict):
    student_id: int
    score:      float
    subject:    str

class ScoreRecordFull(ScoreRecord, total=False):
    notes:    str
    verified: bool

# ── 3. NamedTuple — immutable coordinate ──────────────────────────────────────

class Coordinate(NamedTuple):
    lat:  float
    lon:  float
    name: str = ""

# ── 4. Generic repository ─────────────────────────────────────────────────────

T  = TypeVar("T")
ID = TypeVar("ID")

class Repository(Generic[T, ID]):
    def __init__(self) -> None:
        self._store: dict[ID, T] = {}

    def save(self, id: ID, item: T) -> None:
        self._store[id] = item

    def get(self, id: ID) -> T | None:
        return self._store.get(id)

    def get_all(self) -> list[T]:
        return list(self._store.values())

    def delete(self, id: ID) -> bool:
        if id in self._store:
            del self._store[id]
            return True
        return False

# ── 5. Domain model using dataclass + Final + ClassVar ────────────────────────

@dataclass
class Student:
    name:   str
    score:  float
    city:   str = "Chennai"

    MAX_SCORE: ClassVar[Final[float]] = 100.0
    _registry: ClassVar[list[Student]] = []

    def __post_init__(self) -> None:
        if not 0 <= self.score <= self.MAX_SCORE:
            raise ValueError(f"score must be 0-{self.MAX_SCORE}, got {self.score}")
        Student._registry.append(self)

    def grade(self) -> str:
        if self.score >= 90: return "A"
        if self.score >= 80: return "B"
        if self.score >= 70: return "C"
        return "F"

    def to_dict(self) -> dict[str, object]:
        return {"name": self.name, "score": self.score, "city": self.city}

    @classmethod
    def from_dict(cls, data: dict[str, object]) -> Student:
        return cls(
            name  = str(data["name"]),
            score = float(data["score"]),     # type: ignore[arg-type]
            city  = str(data.get("city", "Chennai")),
        )

# ── 6. Generic function with bound TypeVar ───────────────────────────────────

ComparableT = TypeVar("ComparableT", bound=Comparable)

def top_n(items: list[ComparableT], n: int) -> list[ComparableT]:
    return sorted(items, reverse=True)[:n]

# ── 7. Demo ──────────────────────────────────────────────────────────────────

repo: Repository[Student, int] = Repository()

students = [
    Student("Mohamed", 88.0),
    Student("Priya",   92.0, "Mumbai"),
    Student("Arjun",   74.0, "Delhi"),
]

for i, s in enumerate(students):
    repo.save(i, s)

found: Student | None = repo.get(0)
if found is not None:
    print(f"{found.name}: {found.grade()}")   # Mohamed: B

all_students: list[Student] = repo.get_all()
print([s.name for s in all_students])

coord = Coordinate(13.0827, 80.2707, "Chennai")
lat, lon, name = coord    # tuple unpacking
print(f"{name}: {lat:.4f}, {lon:.4f}")

record: ScoreRecord = {
    "student_id": 1,
    "score": 92.5,
    "subject": "Python",
}
print(json.dumps(record, indent=2))
```

---

## Key Takeaways

- Python does NOT enforce type hints at runtime — they exist for mypy, IDEs, and documentation.
- `Optional[X]` = `X | None`. In Python 3.10+ use `X | None` directly. `from __future__ import annotations` backports `|` syntax to 3.8+.
- `Any` opts out of type checking — use sparingly. `object` is the actual base class.
- `TypeVar` creates a placeholder that preserves type relationships. `bound=X` restricts to X and its subclasses.
- `Generic[T]` makes a class parameterizable: `Stack[int]`, `Repository[User, int]`.
- `Protocol` = structural subtyping — no inheritance needed, just the right methods. Add `@runtime_checkable` to use with `isinstance()`.
- `TypedDict` = typed shape for dicts. `total=False` makes all keys optional. Combine required and optional with inheritance.
- `NamedTuple` = tuple with named fields and type annotations. Immutable. Supports positional access and unpacking.
- `Annotated[type, metadata]` = attach extra info used by Pydantic, FastAPI, etc.
- `TYPE_CHECKING` block — imports only for mypy, not at runtime. Solves circular imports.
- Mypy type narrowing — `isinstance()`, `is None`, `if x:` all narrow the type in the branch.
- `# type: ignore[error-code]` — suppress specific errors with the error code when you know better than mypy.