Let's go. Full topic, nothing skipped. This is the deepest lesson so far.

---

## 1.4 OOP — Classes, Dunder Methods, Inheritance, MRO

---

## Why OOP Exists

Before classes, you'd represent a student like this:

```python
name  = "Mohamed"
score = 88
city  = "Chennai"

def get_grade(score):
    return "A" if score >= 90 else "B" if score >= 80 else "C"
```

This breaks down when you have 100 students — you have 300 variables and no connection between them. OOP bundles **data** (attributes) and **behaviour** (methods) into a single object.

---

## Class Definition

```python
class Student:
    pass    # empty class — valid Python
```

**With `__init__` — the constructor:**

```python
class Student:
    def __init__(self, name, score, city):
        self.name  = name     # instance attribute
        self.score = score
        self.city  = city

# Creating instances
s1 = Student("Mohamed", 88, "Chennai")
s2 = Student("Priya", 92, "Mumbai")

print(s1.name)     # Mohamed
print(s2.score)    # 92
print(type(s1))    # <class '__main__.Student'>
```

**`__init__` is not the constructor — `__new__` is. `__init__` is the initialiser.** Python calls `__new__` to create the object, then `__init__` to set it up. You almost never need to override `__new__`.

---

## `self` — What It Actually Is

`self` is just a name — convention, not a keyword. Python automatically passes the instance as the first argument to every instance method.

```python
class Dog:
    def __init__(self, name):
        self.name = name

    def bark(self):
        print(f"{self.name} says woof!")

d = Dog("Rex")
d.bark()              # Python translates this to:
Dog.bark(d)           # Dog.bark(d) — same thing, self = d
```

**You can name it anything — but don't:**

```python
class Dog:
    def bark(this):      # 'this' works — but everyone uses 'self'
        print(this.name)
```

---

## Instance vs Class vs Static Attributes

```python
class Employee:
    # Class attribute — shared by ALL instances
    company = "Cognizant"
    headcount = 0

    def __init__(self, name, salary):
        # Instance attributes — unique to each instance
        self.name   = name
        self.salary = salary
        Employee.headcount += 1    # modifying class attribute

e1 = Employee("Mohamed", 50000)
e2 = Employee("Priya", 60000)

# Accessing class attribute
print(Employee.company)    # Cognizant
print(e1.company)          # Cognizant  — found on class, not instance
print(e2.company)          # Cognizant

print(Employee.headcount)  # 2
```

**Instance attribute shadows class attribute:**

```python
class Config:
    debug = False

c = Config()
print(c.debug)       # False  — reads class attribute

c.debug = True       # creates an INSTANCE attribute on c only
print(c.debug)       # True   — reads instance attribute (shadows class)
print(Config.debug)  # False  — class attribute unchanged

# Delete instance attribute to reveal class attribute again
del c.debug
print(c.debug)       # False  — back to class attribute
```

**Mutable class attributes — a trap:**

```python
class Team:
    members = []    # shared list — DANGER

    def add(self, name):
        self.members.append(name)

t1 = Team()
t2 = Team()
t1.add("Mohamed")
print(t2.members)    # ['Mohamed']  ← t2 sees it — same list object

# Fix — create in __init__
class Team:
    def __init__(self):
        self.members = []    # each instance gets its own list
```

---

## Instance Method, `@classmethod`, `@staticmethod`

```python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius

    # Instance method — receives self, works on specific instance
    def to_fahrenheit(self):
        return self.celsius * 9/5 + 32

    # Class method — receives cls (the class itself), not an instance
    # Used as alternative constructors
    @classmethod
    def from_fahrenheit(cls, fahrenheit):
        celsius = (fahrenheit - 32) * 5/9
        return cls(celsius)    # creates a new instance

    # Static method — no self, no cls — just a regular function namespaced in the class
    # Used for utility functions that relate to the class but don't need instance or class
    @staticmethod
    def is_valid_celsius(value):
        return -273.15 <= value <= 1_000_000

t1 = Temperature(100)
print(t1.to_fahrenheit())          # 212.0

t2 = Temperature.from_fahrenheit(212)
print(t2.celsius)                  # 100.0

print(Temperature.is_valid_celsius(-300))  # False
print(Temperature.is_valid_celsius(25))    # True
print(t1.is_valid_celsius(25))             # also works — but misleading style
```

**When to use each:**

```python
# Instance method — needs access to instance data (self.something)
def get_area(self): return self.width * self.height

# @classmethod — alternative constructor, factory method, needs the class itself
@classmethod
def from_dict(cls, d): return cls(d["width"], d["height"])

# @staticmethod — utility/helper with logical connection to class but no instance/class needed
@staticmethod
def validate_dimensions(w, h): return w > 0 and h > 0
```

---

## `@property`, `@setter`, `@deleter`

Properties let you define computed attributes that look like regular attributes from the outside.

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius    # _radius is the "private" storage

    @property
    def radius(self):            # getter — called on c.radius
        return self._radius

    @radius.setter
    def radius(self, value):     # setter — called on c.radius = value
        if value < 0:
            raise ValueError(f"radius cannot be negative: {value}")
        self._radius = value

    @radius.deleter
    def radius(self):            # deleter — called on del c.radius
        del self._radius

    @property
    def area(self):              # computed property — no setter
        import math
        return math.pi * self._radius ** 2

c = Circle(5)
print(c.radius)    # 5      — calls getter
print(c.area)      # 78.53  — computed on demand

c.radius = 10      # calls setter
print(c.radius)    # 10

c.radius = -1      # ValueError: radius cannot be negative: -1

del c.radius       # calls deleter
```

**`@property` without a setter creates a read-only attribute:**

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("deposit must be positive")
        self._balance += amount

acc = BankAccount(1000)
print(acc.balance)     # 1000
acc.balance = 2000     # AttributeError: can't set attribute
acc.deposit(500)
print(acc.balance)     # 1500
```

---

## Dunder (Magic) Methods

Dunder = double underscore. These methods define how your objects behave with Python's built-in operations.

---

### Representation — `__str__`, `__repr__`, `__format__`

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        # Goal: unambiguous, for developers, ideally eval()-able
        return f"Point({self.x!r}, {self.y!r})"

    def __str__(self):
        # Goal: readable, for end users
        return f"({self.x}, {self.y})"

    def __format__(self, spec):
        if spec == "polar":
            import math
            r     = math.sqrt(self.x**2 + self.y**2)
            theta = math.atan2(self.y, self.x)
            return f"r={r:.2f}, θ={math.degrees(theta):.1f}°"
        return str(self)

p = Point(3, 4)
print(repr(p))          # Point(3, 4)    — __repr__
print(str(p))           # (3, 4)         — __str__
print(p)                # (3, 4)         — print() calls __str__
print(f"{p}")           # (3, 4)         — f-string calls __str__
print(f"{p!r}")         # Point(3, 4)    — f-string forces __repr__
print(f"{p:polar}")     # r=5.00, θ=53.1° — __format__
```

**`__repr__` is the fallback — define it first:**

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"
    # No __str__ defined — Python uses __repr__ for str() too

p = Point(3, 4)
print(str(p))     # Point(3, 4)  — falls back to __repr__
print(repr(p))    # Point(3, 4)
```

**Without any dunder — useless default:**

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(3, 4)
print(p)    # <__main__.Point object at 0x7f1234>  — useless
```

---

### Comparison — `__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`

```python
class Student:
    def __init__(self, name, score):
        self.name  = name
        self.score = score

    def __eq__(self, other):
        if not isinstance(other, Student):
            return NotImplemented    # not False — NotImplemented lets Python try the other side
        return self.score == other.score and self.name == other.name

    def __lt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.score < other.score

    def __le__(self, other):
        return self == other or self < other

    def __gt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.score > other.score

    def __ge__(self, other):
        return self == other or self > other

    def __ne__(self, other):
        result = self.__eq__(other)
        if result is NotImplemented:
            return result
        return not result

s1 = Student("Mohamed", 88)
s2 = Student("Priya", 92)
s3 = Student("Arjun", 88)

print(s1 == s3)    # False  — same score, different name
print(s1 < s2)     # True   — 88 < 92
print(s2 > s1)     # True
print(sorted([s2, s1, s3]))   # sorted by score
```

**`functools.total_ordering` — define `__eq__` and one comparison, get the rest free:**

```python
from functools import total_ordering

@total_ordering
class Student:
    def __init__(self, name, score):
        self.name  = name
        self.score = score

    def __eq__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.score == other.score

    def __lt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.score < other.score

    # __le__, __gt__, __ge__, __ne__ are automatically generated
```

**Why return `NotImplemented` not `False`:**

```python
# When Python evaluates a == b:
# 1. Try a.__eq__(b)
# 2. If that returns NotImplemented, try b.__eq__(a)
# 3. If both return NotImplemented, fall back to identity comparison

# Returning False from __eq__ when type is wrong prevents Python from
# trying the other side — breaks symmetry
```

---

### Hashing — `__hash__`

```python
# By default, objects ARE hashable (use id as hash)
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(1, 2)
print(hash(p))        # some int based on id
d = {p: "origin"}     # works — objects are hashable by default
s = {p}               # works
```

**Defining `__eq__` automatically sets `__hash__ = None` (unhashable):**

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

p = Point(1, 2)
hash(p)               # TypeError: unhashable type: 'Point'
{p: "origin"}         # TypeError
{p}                   # TypeError
```

**Why — if two objects are equal, they must have the same hash. If you define equality but not hash, Python can't guarantee this.**

**Fix — define `__hash__` using the same fields as `__eq__`:**

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return self.x == other.x and self.y == other.y

    def __hash__(self):
        return hash((self.x, self.y))   # hash of a tuple of the fields

p1 = Point(1, 2)
p2 = Point(1, 2)

print(p1 == p2)        # True
print(hash(p1) == hash(p2))   # True  — equal objects, equal hashes

d = {p1: "point"}
print(d[p2])           # "point"  — p2 looks up p1's value because they're equal

s = {p1, p2}
print(len(s))          # 1  — duplicates removed because p1 == p2
```

**The contract:** `a == b` must imply `hash(a) == hash(b)`. The reverse is NOT required — hash collisions are allowed.

---

### Arithmetic — `__add__`, `__radd__`, `__iadd__`, `__mul__`

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

    def __add__(self, other):
        # Called for: self + other
        if not isinstance(other, Vector):
            return NotImplemented
        return Vector(self.x + other.x, self.y + other.y)

    def __radd__(self, other):
        # Called for: other + self  when other doesn't know how to add Vector
        # e.g. 0 + vector (used in sum([v1, v2, v3]))
        if other == 0:
            return self    # sum() starts with 0 + first_item
        return NotImplemented

    def __iadd__(self, other):
        # Called for: self += other
        if not isinstance(other, Vector):
            return NotImplemented
        self.x += other.x
        self.y += other.y
        return self    # must return self for in-place operations

    def __mul__(self, scalar):
        # Called for: self * scalar
        if not isinstance(scalar, (int, float)):
            return NotImplemented
        return Vector(self.x * scalar, self.y * scalar)

    def __rmul__(self, scalar):
        # Called for: scalar * self
        return self.__mul__(scalar)

    def __neg__(self):
        # Unary negation — called for: -self
        return Vector(-self.x, -self.y)

    def __abs__(self):
        # Called for: abs(self)
        import math
        return math.sqrt(self.x**2 + self.y**2)

v1 = Vector(1, 2)
v2 = Vector(3, 4)

print(v1 + v2)        # Vector(4, 6)
print(v1 * 3)         # Vector(3, 6)
print(3 * v1)         # Vector(3, 6)  — uses __rmul__
print(-v1)            # Vector(-1, -2)
print(abs(v1))        # 2.236...

v1 += v2
print(v1)             # Vector(4, 6)

print(sum([v1, v2]))  # Vector(7, 10)  — uses __radd__ for the initial 0 +
```

**How Python decides which method to call:**

```python
a + b:
  1. Try a.__add__(b)
  2. If NotImplemented, try b.__radd__(a)
  3. If NotImplemented, raise TypeError

a += b:
  1. Try a.__iadd__(b)
  2. If NotImplemented or no __iadd__, try a = a.__add__(b)
```

---

### Container — `__len__`, `__getitem__`, `__setitem__`, `__delitem__`, `__contains__`, `__iter__`

Implement these and your object works like a built-in sequence or mapping.

```python
class Playlist:
    def __init__(self, name):
        self.name   = name
        self._songs = []

    def __repr__(self):
        return f"Playlist({self.name!r}, {len(self)} songs)"

    def __len__(self):
        return len(self._songs)

    def __getitem__(self, index):
        return self._songs[index]    # supports slicing too since list handles it

    def __setitem__(self, index, value):
        self._songs[index] = value

    def __delitem__(self, index):
        del self._songs[index]

    def __contains__(self, song):
        return song in self._songs   # called by 'in' operator

    def __iter__(self):
        return iter(self._songs)     # makes it iterable in for loops

    def append(self, song):
        self._songs.append(song)

p = Playlist("Workout Mix")
p.append("Song A")
p.append("Song B")
p.append("Song C")

print(len(p))             # 3
print(p[0])               # Song A
print(p[-1])              # Song C
print(p[0:2])             # ['Song A', 'Song B']

p[0] = "Song X"
print(p[0])               # Song X

del p[0]
print(len(p))             # 2

print("Song B" in p)      # True
print("Song Z" in p)      # False

for song in p:
    print(song)
# Song B
# Song C

print(list(p))            # ['Song B', 'Song C']
print(sorted(p))          # ['Song B', 'Song C']
```

---

### Context Manager — `__enter__`, `__exit__`

Implement these and your object works with `with`.

```python
class DatabaseConnection:
    def __init__(self, host, port):
        self.host = host
        self.port = port
        self.conn = None

    def __enter__(self):
        print(f"connecting to {self.host}:{self.port}")
        self.conn = f"connection_to_{self.host}"   # simulate connection
        return self.conn    # this becomes the 'as' target

    def __exit__(self, exc_type, exc_val, exc_tb):
        # exc_type, exc_val, exc_tb are exception info if one was raised
        # all three are None if no exception occurred
        print(f"closing connection")
        self.conn = None

        if exc_type is not None:
            print(f"exception occurred: {exc_val}")

        return False   # False = don't suppress the exception
                       # True  = suppress the exception (rarely do this)

with DatabaseConnection("localhost", 5432) as conn:
    print(f"using {conn}")
    # do work here

# connecting to localhost:5432
# using connection_to_localhost
# closing connection

# Exception during with block — __exit__ still called
try:
    with DatabaseConnection("localhost", 5432) as conn:
        raise ValueError("something went wrong")
except ValueError:
    pass
# connecting to localhost:5432
# closing connection
# exception occurred: something went wrong
```

---

### Callable — `__call__`

Makes an instance callable like a function.

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, value):
        return value * self.factor

double = Multiplier(2)
triple = Multiplier(3)

print(double(5))     # 10  — calling the instance like a function
print(triple(5))     # 15
print(callable(double))  # True

# Useful for stateful callables — remember state between calls
class Counter:
    def __init__(self):
        self.count = 0

    def __call__(self):
        self.count += 1
        return self.count

c = Counter()
print(c())    # 1
print(c())    # 2
print(c())    # 3
```

---

## Inheritance — Single, Multiple, `super()`

### Single Inheritance

```python
class Animal:
    def __init__(self, name, sound):
        self.name  = name
        self.sound = sound

    def speak(self):
        return f"{self.name} says {self.sound}"

    def __repr__(self):
        return f"{type(self).__name__}({self.name!r})"

class Dog(Animal):
    def __init__(self, name):
        super().__init__(name, "woof")   # call parent __init__

    def fetch(self, item):
        return f"{self.name} fetches the {item}"

class Cat(Animal):
    def __init__(self, name):
        super().__init__(name, "meow")

    def speak(self):                     # override parent method
        return f"{self.name} says {self.sound}... and ignores you"

d = Dog("Rex")
c = Cat("Whiskers")

print(d.speak())     # Rex says woof
print(c.speak())     # Whiskers says meow... and ignores you
print(d.fetch("ball"))   # Rex fetches the ball

print(isinstance(d, Dog))     # True
print(isinstance(d, Animal))  # True  — Dog IS an Animal
print(issubclass(Dog, Animal))  # True
```

### `super()` — More Than Just Calling Parent

```python
class Base:
    def method(self):
        print("Base.method")

class Child(Base):
    def method(self):
        super().method()    # calls Base.method
        print("Child.method")

class GrandChild(Child):
    def method(self):
        super().method()    # calls Child.method (which then calls Base.method)
        print("GrandChild.method")

GrandChild().method()
# Base.method
# Child.method
# GrandChild.method
```

---

## MRO — Method Resolution Order

When Python looks up a method or attribute, it follows the **MRO** — a linearized list of classes to search in order.

```python
class A:
    def method(self):
        print("A")

class B(A):
    def method(self):
        print("B")

class C(A):
    def method(self):
        print("C")

class D(B, C):
    pass

print(D.__mro__)
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)

D().method()    # B  — B is first in MRO after D
```

### Diamond Problem — Why MRO Matters

```python
#         A
#        / \
#       B   C
#        \ /
#         D

class A:
    def hello(self):
        print("Hello from A")

class B(A):
    def hello(self):
        print("Hello from B")
        super().hello()

class C(A):
    def hello(self):
        print("Hello from C")
        super().hello()

class D(B, C):
    def hello(self):
        print("Hello from D")
        super().hello()

print(D.__mro__)
# D → B → C → A → object
# C3 linearization guarantees A appears only once, after B and C

D().hello()
# Hello from D
# Hello from B
# Hello from C
# Hello from A
```

**`super()` doesn't mean "call parent" — it means "call next in MRO".** This is why all the `super()` calls cooperate in the diamond — each class calls the next one in the MRO chain.

### C3 Linearization — The Algorithm

Python computes MRO using C3 linearization:

```
MRO(D) = D + merge(MRO(B), MRO(C), [B, C])
       = D + merge([B, A, object], [C, A, object], [B, C])
       = D, B + merge([A, object], [C, A, object], [C])
       = D, B, C + merge([A, object], [A, object], [])
       = D, B, C, A + merge([object], [object], [])
       = D, B, C, A, object
```

```python
# Inspect MRO
print([cls.__name__ for cls in D.__mro__])
# ['D', 'B', 'C', 'A', 'object']

# MRO that can't be resolved — TypeError
class X(A, B): pass    # A before B
class Y(B, A): pass    # B before A
# class Z(X, Y): pass  # TypeError: Cannot create a consistent MRO
```

---

## Abstract Base Classes — `abc.ABC`, `@abstractmethod`

Force subclasses to implement specific methods. Can't instantiate a class with unimplemented abstract methods.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        """Return the area of the shape."""
        ...    # or pass

    @abstractmethod
    def perimeter(self) -> float:
        """Return the perimeter of the shape."""
        ...

    def describe(self):    # concrete method — shared implementation
        return f"Area: {self.area():.2f}, Perimeter: {self.perimeter():.2f}"

# Cannot instantiate — has abstract methods
Shape()    # TypeError: Can't instantiate abstract class Shape with abstract methods area, perimeter

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        import math
        return math.pi * self.radius ** 2

    def perimeter(self):
        import math
        return 2 * math.pi * self.radius

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width  = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

    # Missing: can override describe() or use the inherited one

c = Circle(5)
r = Rectangle(4, 6)

print(c.describe())    # Area: 78.54, Perimeter: 31.42
print(r.describe())    # Area: 24.00, Perimeter: 20.00

# Polymorphism — same interface, different implementations
shapes: list[Shape] = [Circle(3), Rectangle(4, 5), Circle(7)]
total_area = sum(s.area() for s in shapes)
print(f"Total area: {total_area:.2f}")
```

**`@abstractmethod` with `@property`:**

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @property
    @abstractmethod
    def sound(self) -> str:
        ...

    def speak(self):
        return f"I say {self.sound}"

class Dog(Animal):
    @property
    def sound(self):
        return "woof"

print(Dog().speak())    # I say woof
```

---

## `dataclasses` — `@dataclass`, `field()`, `__post_init__`, `frozen=True`

`dataclasses` auto-generates `__init__`, `__repr__`, `__eq__` (and optionally more) from class annotations.

### Basic `@dataclass`

```python
from dataclasses import dataclass, field

@dataclass
class Student:
    name:  str
    score: float
    city:  str = "Chennai"    # default value

# Auto-generated __init__, __repr__, __eq__
s1 = Student("Mohamed", 88.0)
s2 = Student("Priya", 92.0, "Mumbai")

print(s1)           # Student(name='Mohamed', score=88.0, city='Chennai')
print(repr(s1))     # Student(name='Mohamed', score=88.0, city='Chennai')
print(s1 == Student("Mohamed", 88.0))   # True  — __eq__ compares all fields
```

### `field()` — Fine-Grained Field Control

```python
from dataclasses import dataclass, field
from typing import ClassVar

@dataclass
class Student:
    name:   str
    score:  float
    tags:   list[str] = field(default_factory=list)    # mutable default — use factory
    _id:    int       = field(default=0, repr=False)   # hidden from repr
    school: str       = field(default="PMU", compare=False)  # excluded from __eq__

    # Class variable — not an instance field
    count: ClassVar[int] = 0

s1 = Student("Mohamed", 88.0)
s2 = Student("Mohamed", 88.0, school="IIT")

print(s1)             # Student(name='Mohamed', score=88.0, tags=[])
print(s1 == s2)       # True  — school excluded from compare
s1.tags.append("A+")
print(s1.tags)        # ['A+']
print(s2.tags)        # []   — separate lists (factory creates new list each time)
```

### `__post_init__` — Validation and Derived Fields

```python
from dataclasses import dataclass, field
import math

@dataclass
class Circle:
    radius: float

    # Computed after __init__ runs
    area:        float = field(init=False)   # not passed to __init__
    circumference: float = field(init=False)

    def __post_init__(self):
        if self.radius < 0:
            raise ValueError(f"radius must be non-negative, got {self.radius}")
        self.area          = math.pi * self.radius ** 2
        self.circumference = 2 * math.pi * self.radius

c = Circle(5)
print(c)
# Circle(radius=5, area=78.53..., circumference=31.41...)

Circle(-1)    # ValueError: radius must be non-negative, got -1
```

### `frozen=True` — Immutable Dataclass

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Point:
    x: float
    y: float

p = Point(3.0, 4.0)
p.x = 10    # FrozenInstanceError: cannot assign to field 'x'

# frozen=True also generates __hash__ — objects are hashable
print(hash(p))
d = {p: "origin"}
s = {p}
```

### `@dataclass` Parameters

```python
@dataclass(
    init=True,       # generate __init__              (default True)
    repr=True,       # generate __repr__              (default True)
    eq=True,         # generate __eq__                (default True)
    order=False,     # generate __lt__, __le__, etc.  (default False)
    frozen=False,    # make immutable                 (default False)
    slots=True,      # use __slots__ for memory savings (Python 3.10+)
)
class MyClass:
    x: int
    y: int
```

**`order=True` — enables sorting:**

```python
from dataclasses import dataclass

@dataclass(order=True)
class Student:
    score: float    # compared first
    name:  str      # compared second (if scores are equal)

students = [Student(88, "Mohamed"), Student(92, "Priya"), Student(88, "Arjun")]
print(sorted(students))
# [Student(score=88, name='Arjun'), Student(score=88, name='Mohamed'), Student(score=92, name='Priya')]
```

---

## Mixin Pattern

Mixins add behaviour through multiple inheritance without being a primary base class.

```python
class SerializeMixin:
    """Adds JSON serialisation to any class."""
    import json

    def to_dict(self):
        return {k: v for k, v in self.__dict__.items() if not k.startswith("_")}

    def to_json(self):
        import json
        return json.dumps(self.to_dict())

    @classmethod
    def from_dict(cls, data):
        return cls(**data)

class LogMixin:
    """Adds logging to any class."""
    def log(self, message):
        print(f"[{type(self).__name__}] {message}")

class Student(SerializeMixin, LogMixin):
    def __init__(self, name, score):
        self.name  = name
        self.score = score

s = Student("Mohamed", 88)
print(s.to_json())            # {"name": "Mohamed", "score": 88}
s.log("created successfully") # [Student] created successfully

s2 = Student.from_dict({"name": "Priya", "score": 92})
print(s2.name)    # Priya
```

---

## Putting It All Together

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from functools import total_ordering

# Abstract base
class Measurable(ABC):
    @property
    @abstractmethod
    def magnitude(self) -> float:
        ...

# Mixin
class ComparableMixin:
    def __eq__(self, other):
        if not isinstance(other, type(self)):
            return NotImplemented
        return self.magnitude == other.magnitude

    def __lt__(self, other):
        if not isinstance(other, type(self)):
            return NotImplemented
        return self.magnitude < other.magnitude

    def __hash__(self):
        return hash(self.magnitude)

# Dataclass with validation
@dataclass
class Vector(Measurable, ComparableMixin):
    x: float
    y: float
    _magnitude: float = field(init=False, repr=False)

    def __post_init__(self):
        import math
        self._magnitude = math.sqrt(self.x**2 + self.y**2)

    @property
    def magnitude(self):
        return self._magnitude

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)

    def __rmul__(self, scalar):
        return self.__mul__(scalar)

    def __neg__(self):
        return Vector(-self.x, -self.y)

    def __repr__(self):
        return f"Vector({self.x}, {self.y}) |{self.magnitude:.2f}|"

    def __iter__(self):          # unpack as tuple: x, y = vec
        yield self.x
        yield self.y

    def __getitem__(self, idx):
        return (self.x, self.y)[idx]

    def dot(self, other):
        return self.x * other.x + self.y * other.y

# Demo
v1 = Vector(3, 4)
v2 = Vector(1, 2)

print(v1)             # Vector(3, 4) |5.00|
print(v1 + v2)        # Vector(4, 6) |7.21|
print(v1 * 2)         # Vector(6, 8) |10.00|
print(2 * v1)         # Vector(6, 8) |10.00|
print(-v1)            # Vector(-3, -4) |5.00|
print(v1 > v2)        # True  — 5.0 > 2.24
print(v1.dot(v2))     # 11.0

x, y = v1             # __iter__
print(x, y)           # 3 4

vectors = [Vector(3,4), Vector(1,2), Vector(2,2)]
print(sorted(vectors))
# [Vector(1, 2) |2.24|, Vector(2, 2) |2.83|, Vector(3, 4) |5.00|]

d = {v1: "unit vector"}   # __hash__ works
print(d[Vector(3, 4)])    # unit vector — equal vectors find the same key
```

---

## Key Takeaways

- `__init__` is the initialiser, not the constructor. `__new__` creates the object.
- `self` is the instance, automatically passed. It's just a convention name.
- **Instance attribute** = per object. **Class attribute** = shared. Mutable class attributes are a trap.
- `@classmethod` → receives `cls`, used for alternative constructors. `@staticmethod` → no self or cls, utility function.
- `@property` = getter. `@name.setter` = setter with validation. No setter = read-only.
- **`__repr__`** = for developers (unambiguous). **`__str__`** = for users (readable). Define `__repr__` first — it's the fallback.
- Defining `__eq__` sets `__hash__ = None`. Always define both together using the same fields.
- Return `NotImplemented` (not `False`) from comparison methods when types don't match — lets Python try the other side.
- `__radd__` is called when the left operand can't handle the operation. Needed for `sum()` to work on custom types.
- `super()` means "next in MRO", not "parent". In diamond inheritance every `super()` cooperates through the chain.
- MRO = C3 linearization — depth-first, left-to-right, no class appears before its subclasses.
- Abstract classes enforce an interface. Subclasses that skip `@abstractmethod` implementations can't be instantiated.
- `@dataclass` auto-generates `__init__`, `__repr__`, `__eq__`. Use `field(default_factory=list)` for mutable defaults. Use `frozen=True` for hashable immutable records.