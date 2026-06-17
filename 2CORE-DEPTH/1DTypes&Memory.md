Let's go. Full topic, nothing skipped.

---

## 1.1 Data Types, Mutability & Memory

---

## How Python Stores Variables — The Object Model

Before anything else, you need the right mental model. In Python, **variables are not boxes that hold values. They are names that point to objects.**

```python
x = 42
```

What actually happens:
1. Python creates an integer object `42` somewhere in memory
2. Python binds the name `x` to that object
3. `x` is just a label — the object exists independently

```python
x = 42
y = x       # y points to the SAME object as x

print(x)    # 42
print(y)    # 42
```

There is one object. Two names pointing to it.

```python
# Visualising it
x ──→ [42]  ←── y
```

---

## `id()` — Identity of an Object

`id()` returns the memory address of an object — a unique integer that identifies exactly which object you're referring to.

```python
x = 42
print(id(x))    # e.g. 140234567890 — the memory address
```

**Two names pointing to the same object have the same `id()`:**

```python
x = [1, 2, 3]
y = x             # y points to the same list

print(id(x))      # 140234567890
print(id(y))      # 140234567890  — identical
print(id(x) == id(y))    # True — same object in memory
```

**Two different objects with the same value have different `id()`s:**

```python
a = [1, 2, 3]
b = [1, 2, 3]     # a NEW list, same content

print(id(a))      # 140234567890
print(id(b))      # 140234599999  — different address
print(id(a) == id(b))    # False — different objects
```

---

## `is` vs `==` — Identity vs Equality

This is the most important distinction in this entire topic.

```python
# == checks VALUE equality — do these objects have the same content?
# is checks IDENTITY   — are these the exact same object in memory?
```

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)    # True  — same content
print(a is b)    # False — different objects in memory

print(a == c)    # True  — same content
print(a is c)    # True  — same object (c = a, both point to same list)
```

**Visualised:**

```
a ──→ [1, 2, 3]    ← c also points here
b ──→ [1, 2, 3]    ← different object, same content
```

**`is` in real code — only use it for `None`, `True`, `False`:**

```python
# Correct use of 'is'
x = None
if x is None:         # correct
    print("no value")

if x is not None:     # correct
    print("has value")

# WRONG use of 'is' for values
a = [1, 2, 3]
b = [1, 2, 3]
if a is b:            # almost always False for lists/dicts/custom objects
    print("same")     # this is comparing identity, not content

# CORRECT — use == for content comparison
if a == b:
    print("same content")    # True
```

**Why `is None` and not `== None`:**

```python
# None is a singleton — only one None object exists in Python
x = None
y = None
print(x is y)     # True  — same object, always
print(id(x) == id(y))   # True — same address

# == None works too, but 'is None' is:
# 1. The Python convention (PEP 8 explicitly recommends it)
# 2. Slightly faster (no __eq__ method call)
# 3. Immune to custom __eq__ that might return True for non-None values
```

---

## Integer Interning — A CPython Implementation Detail

CPython pre-creates integer objects for `-5` to `256` and reuses them.

```python
a = 100
b = 100
print(a is b)    # True  — same object (100 is in the cached range)

a = 1000
b = 1000
print(a is b)    # False — outside cached range, new objects created
                 # (may be True in some contexts due to compiler optimisation)
```

**String interning — similar concept:**

```python
a = "hello"
b = "hello"
print(a is b)    # True  — CPython interns short strings

a = "hello world with spaces"
b = "hello world with spaces"
print(a is b)    # False in some cases — longer strings may not be interned
```

**The lesson — never rely on `is` for value comparison:**

```python
# This might work today and break tomorrow or on a different Python version
x = 256
y = 256
print(x is y)    # True  — cached

x = 257
y = 257
print(x is y)    # False — not cached

# Always use == for values. is is for identity only.
```

---

## Mutability Rules

**Mutability = can the object's content be changed after creation?**

### Immutable — cannot be changed after creation

| Type | Example |
|---|---|
| `int` | `42` |
| `float` | `3.14` |
| `bool` | `True` |
| `str` | `"hello"` |
| `tuple` | `(1, 2, 3)` |
| `frozenset` | `frozenset({1, 2})` |
| `bytes` | `b"hello"` |

```python
# str — immutable
s = "hello"
s[0] = "H"        # TypeError: 'str' object does not support item assignment

# tuple — immutable
t = (1, 2, 3)
t[0] = 99         # TypeError: 'tuple' object does not support item assignment

# int — immutable
x = 42
# you can't change 42 — when you do x = 43, you're pointing x at a new object
```

**"Modifying" an immutable creates a new object:**

```python
s = "hello"
print(id(s))    # 140234567890

s = s.upper()   # creates a NEW string "HELLO"
print(id(s))    # 140234599999  — different object
                # "hello" still exists until garbage collected
```

### Mutable — can be changed after creation

| Type | Example |
|---|---|
| `list` | `[1, 2, 3]` |
| `dict` | `{"a": 1}` |
| `set` | `{1, 2, 3}` |
| `bytearray` | `bytearray(b"hello")` |

```python
# list — mutable
lst = [1, 2, 3]
print(id(lst))    # 140234567890

lst[0] = 99       # modifies the SAME object
print(lst)        # [99, 2, 3]
print(id(lst))    # 140234567890  — same address, same object

lst.append(4)
print(id(lst))    # 140234567890  — still the same object

# dict — mutable
d = {"a": 1}
d["b"] = 2        # modifies in-place
print(d)          # {"a": 1, "b": 2}
```

---

## Why Mutability Matters — The Shared Reference Problem

```python
a = [1, 2, 3]
b = a              # b points to the SAME list

b.append(4)        # mutates the object through b
print(a)           # [1, 2, 3, 4]  — a sees the change too
print(b)           # [1, 2, 3, 4]

print(a is b)      # True — they are the same object
```

This is not a bug. It's the expected behaviour when two names point to the same mutable object. The bug is when you didn't intend to share the object.

```python
# With immutables — no sharing problem
x = "hello"
y = x

y = y.upper()     # creates a NEW string — x is untouched
print(x)          # hello   — unchanged
print(y)          # HELLO
print(x is y)     # False — different objects now
```

Immutables are safe to share because you can't modify them — any "change" creates a new object.

---

## Mutable Default Argument Trap

One of the most famous Python bugs. Covered in 0.8 but explained here at a deeper level because now you understand the object model.

```python
def add_item(item, items=[]):    # the [] is created ONCE at function definition time
    items.append(item)
    return items
```

**What happens at definition time:**

```python
# When Python reads this line:
# def add_item(item, items=[]):
# It creates ONE list object []
# and binds it to the default value of 'items'
# That same list object is reused on every call that doesn't pass items
```

```python
print(add_item("apple"))     # ['apple']
print(add_item("banana"))    # ['apple', 'banana']  ← WRONG
print(add_item("cherry"))    # ['apple', 'banana', 'cherry']  ← keeps growing

# Inspect the default object
print(add_item.__defaults__)   # (['apple', 'banana', 'cherry'],)
# The default list has been mutated — it now contains all previous calls' data
```

**Why this happens — the object model:**

```python
# The function object stores its defaults
# add_item.__defaults__ = ([],)   ← at definition time
# Because lists are mutable, the SAME list object accumulates data across calls
```

**The fix — `None` as sentinel:**

```python
def add_item(item, items=None):
    if items is None:
        items = []          # fresh list created on EACH call that doesn't pass items
    items.append(item)
    return items

print(add_item("apple"))     # ['apple']
print(add_item("banana"))    # ['banana']    — fresh list each time
print(add_item("cherry"))    # ['cherry']

# Existing list still works
my_list = ["existing"]
print(add_item("new", my_list))   # ['existing', 'new']
```

**The same bug with dict and set defaults:**

```python
# BAD
def register(name, registry={}):
    registry[name] = True
    return registry

# BAD
def add_tag(tag, tags=set()):
    tags.add(tag)
    return tags

# GOOD — always use None
def register(name, registry=None):
    if registry is None:
        registry = {}
    registry[name] = True
    return registry
```

**Immutable defaults are safe — because they can't be mutated:**

```python
def greet(name, greeting="Hello"):    # str is immutable — safe
    return f"{greeting}, {name}"

def repeat(n, times=3):               # int is immutable — safe
    return n * times

def process(data, flags=()):          # tuple is immutable — safe
    pass
```

---

## Shallow Copy vs Deep Copy

You need to copy objects when you want an independent version — changes to the copy shouldn't affect the original.

### The problem — assignment is not a copy

```python
original = [1, 2, 3]
alias    = original      # NOT a copy — both names point to same object

alias.append(4)
print(original)    # [1, 2, 3, 4]  — original changed too
```

### Shallow copy — new container, same inner objects

A shallow copy creates a new outer container but the elements inside are still the same objects.

```python
import copy

original = [1, 2, 3]
shallow  = copy.copy(original)     # new list object

shallow.append(4)
print(original)    # [1, 2, 3]    — unchanged
print(shallow)     # [1, 2, 3, 4]

print(original is shallow)    # False — different objects
```

**Shallow copy methods — all equivalent for flat lists:**

```python
lst = [1, 2, 3]

a = lst.copy()         # list method
b = lst[:]             # slice syntax
c = list(lst)          # list() constructor
d = copy.copy(lst)     # explicit shallow copy

print(a is lst)    # False — all are new objects
print(a == lst)    # True  — same content
```

**Where shallow copy breaks down — nested mutable objects:**

```python
original = [1, 2, [10, 20, 30]]
shallow  = original.copy()

# Outer list is independent
shallow.append(99)
print(original)    # [1, 2, [10, 20, 30]]    — outer unchanged
print(shallow)     # [1, 2, [10, 20, 30], 99]

# But the nested list is SHARED
shallow[2].append(99)
print(original)    # [1, 2, [10, 20, 30, 99]]  ← CHANGED — shared inner list
print(shallow)     # [1, 2, [10, 20, 30, 99], 99]
```

**Visualised:**

```
original ──→ [1, 2, ──→[10, 20, 30]]
                  ↑
shallow  ──→ [1, 2, ──→same inner list, 99]
```

The outer lists are different. The inner list `[10, 20, 30]` is the same object in both.

### Deep copy — completely independent at all levels

```python
import copy

original = [1, 2, [10, 20, 30]]
deep     = copy.deepcopy(original)

deep[2].append(99)
print(original)    # [1, 2, [10, 20, 30]]      — completely unchanged
print(deep)        # [1, 2, [10, 20, 30, 99]]  — only deep changed
```

**Deep copy recursively copies every object at every level:**

```python
# Deeply nested — deep copy handles all levels
original = {"users": [{"name": "Mohamed", "scores": [88, 92]}]}
deep = copy.deepcopy(original)

deep["users"][0]["scores"].append(100)
print(original["users"][0]["scores"])    # [88, 92]    — unchanged
print(deep["users"][0]["scores"])        # [88, 92, 100]
```

### When to use which

```python
# No copy needed — just reading, not modifying
def print_items(lst):
    for item in lst:
        print(item)

# Shallow copy — flat structure, no nested mutables
scores = [88, 92, 74]
backup = scores.copy()    # ints are immutable — shallow is fine

# Shallow copy — you only want independence of the outer container
matrix_rows = [[1,2,3], [4,5,6]]
copy_of_rows = matrix_rows.copy()
# copy_of_rows is a different list but the inner lists are shared

# Deep copy — nested mutables that must be fully independent
config = {"db": {"host": "localhost", "port": 5432}}
test_config = copy.deepcopy(config)
test_config["db"]["port"] = 9999
print(config["db"]["port"])    # 5432  — original unaffected
```

**Performance note — deep copy is slower and uses more memory:**

```python
# Deep copy walks every nested object — expensive for large structures
# Use shallow copy when possible
# Use deep copy only when you genuinely need full independence
```

---

## `del` Keyword — Removes the Name Binding

`del` removes a **name** from the current namespace. It does not necessarily destroy the object.

```python
x = [1, 2, 3]
del x
print(x)    # NameError: name 'x' is not defined
```

**`del` removes the name — the object survives if other names point to it:**

```python
a = [1, 2, 3]
b = a           # b also points to the same list

del a           # removes the name 'a'
print(a)        # NameError: name 'a' is not defined
print(b)        # [1, 2, 3]  — object still alive, b still points to it
```

**The object is only garbage collected when NO names point to it:**

```python
a = [1, 2, 3]    # reference count: 1
b = a            # reference count: 2
c = a            # reference count: 3

del a            # reference count: 2 — object survives
del b            # reference count: 1 — object survives
del c            # reference count: 0 — object is garbage collected, memory freed
```

### `del` on list elements and slices

```python
lst = [10, 20, 30, 40, 50]

del lst[1]          # remove element at index 1
print(lst)          # [10, 30, 40, 50]

del lst[1:3]        # remove elements at index 1, 2
print(lst)          # [10, 50]

del lst[:]          # remove all elements — same as lst.clear()
print(lst)          # []
print(id(lst))      # same address — the list object still exists, just empty
```

**`del lst[:]` vs `del lst` — important difference:**

```python
a = [1, 2, 3]
b = a

del a[:]           # clears the list — object still exists
print(b)           # []  — b sees the change (same object)

a = [1, 2, 3]
b = a

del a              # removes the NAME 'a' — object still alive via b
print(b)           # [1, 2, 3]  — untouched
```

### `del` on dict keys

```python
d = {"name": "Mohamed", "age": 25, "city": "Chennai"}

del d["age"]
print(d)    # {'name': 'Mohamed', 'city': 'Chennai'}

del d["missing"]    # KeyError: 'missing'
```

### `del` on variables in different scopes

```python
x = 10

def test():
    del x    # UnboundLocalError — x is a global, not a local
              # Python treats x as local because of del, but it's never been assigned locally

# Correct — use global keyword if you need to delete a global
def test():
    global x
    del x
```

---

## Reference Counting — How Python Tracks Objects

CPython uses reference counting as its primary memory management mechanism. Every object has a count of how many names/containers point to it.

```python
import sys

x = [1, 2, 3]
print(sys.getrefcount(x))    # 2  — x and the getrefcount argument itself

y = x
print(sys.getrefcount(x))    # 3  — x, y, getrefcount argument

del y
print(sys.getrefcount(x))    # 2  — back to x and getrefcount argument
```

**When refcount hits 0 — object is immediately freed (in CPython):**

```python
a = [1, 2, 3]    # refcount: 1
b = a            # refcount: 2
c = [a, a]       # refcount: 4 — both slots in c point to a

del a            # refcount: 3
del b            # refcount: 2
del c            # refcount: 0 — freed
```

**Circular references — refcounting alone can't handle these:**

```python
a = []
b = []
a.append(b)    # a references b
b.append(a)    # b references a — circular reference

del a
del b
# Both still have refcount > 0 (from each other)
# CPython's cyclic garbage collector handles this separately
# It runs periodically and collects these cycles
```

---

## Putting It All Together

```python
import copy

# Demonstrate the full memory model
def demonstrate():
    # 1. Identity vs equality
    x = [1, 2, 3]
    y = [1, 2, 3]
    z = x

    print(f"x == y:  {x == y}")     # True  — same content
    print(f"x is y:  {x is y}")     # False — different objects
    print(f"x is z:  {x is z}")     # True  — same object
    print(f"id(x):   {id(x)}")
    print(f"id(y):   {id(y)}")
    print(f"id(z):   {id(z)}")      # same as id(x)

    # 2. Mutation through shared reference
    z.append(99)
    print(f"\nAfter z.append(99):")
    print(f"x: {x}")    # [1, 2, 3, 99]  — x changed through z
    print(f"z: {z}")    # [1, 2, 3, 99]

    # 3. Shallow vs deep copy
    nested = [1, [2, 3], [4, 5]]
    shallow = nested.copy()
    deep    = copy.deepcopy(nested)

    nested[1].append(99)

    print(f"\nAfter mutating nested[1]:")
    print(f"nested:  {nested}")     # [1, [2, 3, 99], [4, 5]]
    print(f"shallow: {shallow}")    # [1, [2, 3, 99], [4, 5]]  ← affected
    print(f"deep:    {deep}")       # [1, [2, 3], [4, 5]]      ← unaffected

    # 4. del — removes name, not object
    a = [10, 20, 30]
    b = a
    del a
    print(f"\nAfter del a:")
    print(f"b: {b}")    # [10, 20, 30]  — object survived via b
    # print(a)          # NameError

    # 5. Mutable default trap
    def broken(item, lst=[]):
        lst.append(item)
        return lst

    def fixed(item, lst=None):
        if lst is None:
            lst = []
        lst.append(item)
        return lst

    print(f"\nBroken default:")
    print(broken("a"))   # ['a']
    print(broken("b"))   # ['a', 'b']  ← accumulated

    print(f"\nFixed default:")
    print(fixed("a"))    # ['a']
    print(fixed("b"))    # ['b']       ← fresh each time

demonstrate()
```

---

## Key Takeaways

- Variables are **names pointing to objects** — not boxes containing values. Understanding this explains almost every Python "gotcha".
- `==` compares **value**. `is` compares **identity** (same object in memory). Use `is` only for `None`, `True`, `False`.
- `id()` returns the memory address — two names with the same `id()` are the same object.
- **Immutable:** `int`, `float`, `bool`, `str`, `tuple`, `frozenset` — cannot be changed, safe to share.
- **Mutable:** `list`, `dict`, `set` — can be changed in-place, shared references see each other's mutations.
- **Mutable default argument** — `def f(x=[])` creates the list once at definition time. Fix: use `None` and create inside.
- **Shallow copy** — new outer container, same inner objects. Safe for flat structures. Fails for nested mutables.
- **Deep copy** — completely independent at all levels. Use when nested mutables must not be shared.
- `del x` removes the **name** `x` — the object survives if other names point to it.
- Reference count hits zero → object is freed. Circular references are handled by CPython's cyclic GC.