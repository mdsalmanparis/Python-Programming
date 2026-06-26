# 0.4 Lists

## Creating a List

A list is an ordered, mutable collection of items. The most used data structure in Python.

```python
# Basic list
numbers = [1, 2, 3, 4, 5]

# Mixed types — Python allows this
mixed = [1, "hello", True, 3.14, None]

# Empty list — two ways, both identical
empty1 = []
empty2 = list()

# List from another iterable
chars = list("Python")
print(chars)      # ['P', 'y', 't', 'h', 'o', 'n']

nums = list(range(5))
print(nums)       # [0, 1, 2, 3, 4]

print(type([]))   # <class 'list'>
```

---

## Indexing

Exactly like strings — zero-based, supports negative indexing.

```python
fruits = ["apple", "banana", "cherry", "date", "elderberry"]
#           0         1          2         3          4        ← positive
#          -5        -4         -3        -2         -1        ← negative

print(fruits[0])     # apple
print(fruits[1])     # banana
print(fruits[-1])    # elderberry  — last item
print(fruits[-2])    # date
```

### IndexError

Going out of range:

```python
fruits = ["apple", "banana", "cherry"]

print(fruits[5])     # IndexError: list index out of range
print(fruits[-4])    # IndexError: list index out of range
```

---

## Slicing

Same syntax as strings: `list[start:stop:step]`. Returns a **new list**.

```python
nums = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

print(nums[1:4])     # [1, 2, 3]       — index 1, 2, 3 (4 excluded)
print(nums[:3])      # [0, 1, 2]       — from beginning
print(nums[7:])      # [7, 8, 9]       — to end
print(nums[::2])     # [0, 2, 4, 6, 8] — every 2nd
print(nums[1::2])    # [1, 3, 5, 7, 9] — odd indexes
print(nums[::-1])    # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]  — reversed
print(nums[2:8:3])   # [2, 5]          — index 2, 5
```

### Slicing Returns a New List

Original untouched:

```python
nums = [1, 2, 3, 4, 5]
chunk = nums[1:4]
chunk[0] = 99

print(chunk)    # [99, 3, 4]   — chunk changed
print(nums)     # [1, 2, 3, 4, 5]  — original unchanged
```

### Slicing Never Raises IndexError

```python
nums = [1, 2, 3]
print(nums[0:100])    # [1, 2, 3]  — no error, goes to end
print(nums[10:20])    # []         — empty list, no error
```

---

## len() — Number of Elements

```python
print(len([1, 2, 3]))        # 3
print(len([]))               # 0
print(len(["a", "b"]))       # 2
print(len([[1, 2], [3, 4]])) # 2  — two elements (both are lists)
```

---

## Mutability — Lists Can Be Changed In-Place

This is the fundamental difference between lists and strings. You can modify a list directly.

```python
fruits = ["apple", "banana", "cherry"]

fruits[0] = "avocado"      # replace first element
print(fruits)    # ['avocado', 'banana', 'cherry']

fruits[-1] = "coconut"     # replace last element
print(fruits)    # ['avocado', 'banana', 'coconut']
```

### Replace a Slice

```python
nums = [1, 2, 3, 4, 5]
nums[1:3] = [20, 30]       # replace index 1 and 2
print(nums)    # [1, 20, 30, 4, 5]

nums[1:3] = [99]           # replace two elements with one
print(nums)    # [1, 99, 4, 5]

nums[1:1] = [10, 11]       # insert without removing anything
print(nums)    # [1, 10, 11, 99, 4, 5]
```

---

## Adding Elements

### .append(item) — Add a Single Item to the End

```python
fruits = ["apple", "banana"]
fruits.append("cherry")
print(fruits)    # ['apple', 'banana', 'cherry']

fruits.append([1, 2])     # appends the LIST as a single item
print(fruits)    # ['apple', 'banana', 'cherry', [1, 2]]
```

**Time complexity: O(1) amortized** — Python pre-allocates extra space so most appends don't need to resize. Occasionally it resizes (copies to bigger array) but on average it's constant time.

---

### .insert(index, item) — Insert at a Specific Position

```python
fruits = ["apple", "banana", "cherry"]

fruits.insert(1, "avocado")     # insert at index 1
print(fruits)    # ['apple', 'avocado', 'banana', 'cherry']

fruits.insert(0, "first")       # insert at beginning
print(fruits)    # ['first', 'apple', 'avocado', 'banana', 'cherry']

fruits.insert(100, "last")      # index beyond end — goes to end, no error
print(fruits)    # ['first', 'apple', 'avocado', 'banana', 'cherry', 'last']
```

**Time complexity: O(n)** — everything after the insertion point shifts one position right. For large lists, inserting at the beginning is slow. If you need fast inserts at both ends, use `collections.deque` (Phase 2).

---

### .extend(iterable) — Add Multiple Items

```python
fruits = ["apple", "banana"]

fruits.extend(["cherry", "date"])
print(fruits)    # ['apple', 'banana', 'cherry', 'date']

fruits.extend("hi")             # strings are iterable — adds each character
print(fruits)    # ['apple', 'banana', 'cherry', 'date', 'h', 'i']

fruits.extend(range(3))
print(fruits)    # ['apple', 'banana', 'cherry', 'date', 'h', 'i', 0, 1, 2]
```

**`+=` is identical to `.extend()`:**

```python
nums = [1, 2, 3]
nums += [4, 5]
print(nums)    # [1, 2, 3, 4, 5]
```

### .append() vs .extend() — The Critical Difference

```python
a = [1, 2, 3]
b = [1, 2, 3]

a.append([4, 5])     # adds [4, 5] as ONE item
print(a)    # [1, 2, 3, [4, 5]]   — length 4

b.extend([4, 5])     # adds 4 and 5 as SEPARATE items
print(b)    # [1, 2, 3, 4, 5]     — length 5
```

This is one of the most common beginner mistakes. If you want to add multiple items flat, use `.extend()`. If you want to add a list as a nested element, use `.append()`.

---

## Removing Elements

### .remove(value) — Remove First Occurrence by Value

```python
fruits = ["apple", "banana", "cherry", "banana"]

fruits.remove("banana")       # removes FIRST occurrence only
print(fruits)    # ['apple', 'cherry', 'banana']

fruits.remove("xyz")          # ValueError: list.remove(x): x not in list
```

**Safe remove — check first:**

```python
if "banana" in fruits:
    fruits.remove("banana")
```

---

### .pop(index) — Remove and Return Item by Index

```python
fruits = ["apple", "banana", "cherry", "date"]

last = fruits.pop()          # no index = removes last item
print(last)      # date
print(fruits)    # ['apple', 'banana', 'cherry']

second = fruits.pop(1)       # remove index 1
print(second)    # banana
print(fruits)    # ['apple', 'cherry']

fruits.pop(10)               # IndexError: pop index out of range
```

**`.pop()` is the only remove method that returns the removed value** — use it when you need to both remove and use the item.

---

### del — Delete by Index or Slice

```python
fruits = ["apple", "banana", "cherry", "date", "elderberry"]

del fruits[1]           # delete index 1
print(fruits)    # ['apple', 'cherry', 'date', 'elderberry']

del fruits[1:3]         # delete slice
print(fruits)    # ['apple', 'elderberry']

del fruits              # deletes the variable entirely — fruits no longer exists
print(fruits)    # NameError: name 'fruits' is not defined
```

---

### .clear() — Empty the List

```python
fruits = ["apple", "banana", "cherry"]
fruits.clear()
print(fruits)    # []
print(len(fruits))   # 0
```

### .clear() vs del vs Reassigning

```python
a = [1, 2, 3]
b = a              # b points to the same list

a.clear()          # empties the list object itself
print(a)    # []
print(b)    # []   — b sees the change because it's the SAME object
```

Compare with reassignment:

```python
a = [1, 2, 3]
b = a
a = []             # a now points to a NEW empty list
print(a)    # []
print(b)    # [1, 2, 3]  — b still points to the original
```

This distinction — clearing an object vs pointing a name at a new object — is fundamental to understanding Python's memory model.

---

## Searching

### in Operator — Membership Test

```python
fruits = ["apple", "banana", "cherry"]

print("banana" in fruits)    # True
print("xyz" in fruits)       # False
print("apple" not in fruits) # False

# Time complexity: O(n) — scans the list linearly
```

### .index(value) — Find Position of First Occurrence

```python
fruits = ["apple", "banana", "cherry", "banana"]

print(fruits.index("banana"))     # 1  — first occurrence
print(fruits.index("cherry"))     # 2
print(fruits.index("xyz"))        # ValueError: 'xyz' is not in list

# Search within a range: .index(value, start, stop)
print(fruits.index("banana", 2))  # 3  — find banana starting from index 2
```

### .count(value) — Count Occurrences

```python
nums = [1, 2, 3, 2, 2, 4, 2]

print(nums.count(2))    # 4
print(nums.count(9))    # 0  — not found, returns 0, no error
```

---

## Sorting

### .sort() — Sorts In-Place, Returns None

```python
nums = [3, 1, 4, 1, 5, 9, 2, 6]
nums.sort()
print(nums)    # [1, 1, 2, 3, 4, 5, 6, 9]

nums.sort(reverse=True)
print(nums)    # [9, 6, 5, 4, 3, 2, 1, 1]
```

**Sorting strings:**

```python
words = ["banana", "apple", "cherry", "date"]
words.sort()
print(words)    # ['apple', 'banana', 'cherry', 'date']  — alphabetical
```

### key= Parameter — Sort by a Custom Rule

```python
words = ["banana", "apple", "cherry", "fig", "date"]
words.sort(key=len)               # sort by length
print(words)    # ['fig', 'date', 'apple', 'banana', 'cherry']

words.sort(key=str.lower)         # case-insensitive sort
```

### The Trap — .sort() Returns None

```python
nums = [3, 1, 2]
result = nums.sort()    # sort happens in-place
print(result)   # None  — .sort() returns nothing
print(nums)     # [1, 2, 3]  — the list itself was sorted
```

---

## sorted() — Returns a New Sorted List

Original unchanged:

```python
nums = [3, 1, 4, 1, 5]
new = sorted(nums)
print(new)     # [1, 1, 3, 4, 5]
print(nums)    # [3, 1, 4, 1, 5]  — unchanged

# sorted() works on ANY iterable, .sort() only on lists
print(sorted("python"))    # ['h', 'n', 'o', 'p', 't', 'y']
print(sorted((3,1,2)))     # [1, 2, 3]  — tuple input, list output

# reverse and key work the same way
print(sorted(nums, reverse=True))         # [5, 4, 3, 1, 1]
print(sorted(words, key=len))
```

### When to Use Which

**Use `.sort()` when you want to sort the list itself and don't need the original:**
```python
nums.sort()
```

**Use `sorted()` when you need to keep the original OR sort something that isn't a list:**
```python
original = [3, 1, 2]
sorted_copy = sorted(original)
```

---

## Reversing

### .reverse() — Reverses In-Place, Returns None

```python
nums = [1, 2, 3, 4, 5]
nums.reverse()
print(nums)    # [5, 4, 3, 2, 1]
```

### [::-1] — Returns a New Reversed List

```python
nums = [1, 2, 3, 4, 5]
reversed_nums = nums[::-1]
print(reversed_nums)   # [5, 4, 3, 2, 1]
print(nums)            # [1, 2, 3, 4, 5]  — unchanged
```

### reversed() — Built-In, Returns an Iterator

```python
nums = [1, 2, 3, 4, 5]
for n in reversed(nums):
    print(n)    # 5, 4, 3, 2, 1

list(reversed(nums))    # [5, 4, 3, 2, 1]  — convert to list if needed
```

---

## Copying — The Most Important Gotcha in Lists

### b = a Does NOT Copy

Both names point to the same list:

```python
a = [1, 2, 3]
b = a              # b is NOT a copy — b IS a

b.append(4)
print(a)    # [1, 2, 3, 4]  — a was "changed" too
print(b)    # [1, 2, 3, 4]

b[0] = 99
print(a)    # [99, 2, 3, 4]  — same object
print(b)    # [99, 2, 3, 4]
```

There is only one list in memory. `a` and `b` are just two different names pointing to it. Modifying through either name changes the same object.

---

## Shallow Copy — .copy() or [:]

Creates a new list object with the same elements.

```python
a = [1, 2, 3]
b = a.copy()      # new list, same values
# or: b = a[:]   # identical result

b.append(4)
print(a)    # [1, 2, 3]      — unchanged
print(b)    # [1, 2, 3, 4]   — only b changed
```

### "Shallow" Means It Copies References, Not Nested Objects

```python
a = [1, 2, [10, 20]]
b = a.copy()           # shallow copy

b[0] = 99
print(a)    # [1, 2, [10, 20]]   — a[0] unchanged

b[2].append(30)        # modifying the nested list
print(a)    # [1, 2, [10, 20, 30]]  — a[2] WAS changed — same nested list object
print(b)    # [99, 2, [10, 20, 30]]
```

The outer list is a copy, but the nested list `[10, 20]` inside is still the same object in memory. Both `a[2]` and `b[2]` point to it.

---

## Deep Copy — When You Need Truly Independent Nested Lists

```python
import copy

a = [1, 2, [10, 20]]
b = copy.deepcopy(a)   # completely independent copy at all levels

b[2].append(30)
print(a)    # [1, 2, [10, 20]]       — completely unchanged
print(b)    # [1, 2, [10, 20, 30]]
```

### Summary — Which to Use

```python
b = a              # NO copy — same object, don't do this when you want a copy
b = a.copy()       # shallow copy — safe for flat lists
b = a[:]           # shallow copy — identical to .copy()
b = list(a)        # shallow copy — another way
b = copy.deepcopy(a)  # deep copy — safe for nested lists
```

---

## Nested Lists

A list that contains other lists. Used to represent grids, matrices, tables.

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

print(matrix[0])        # [1, 2, 3]  — first row
print(matrix[1])        # [4, 5, 6]  — second row
print(matrix[0][1])     # 2  — row 0, column 1
print(matrix[2][2])     # 9  — row 2, column 2
print(matrix[-1][-1])   # 9  — last row, last column
```

### Modifying a Nested List

```python
matrix[0][0] = 99
print(matrix)
# [[99, 2, 3], [4, 5, 6], [7, 8, 9]]
```

### Iterating a Matrix

**Row by row:**
```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

for row in matrix:
    print(row)
```

**Every individual element:**
```python
for row in matrix:
    for item in row:
        print(item, end=" ")    # 1 2 3 4 5 6 7 8 9
```

**With indexes:**
```python
for i in range(len(matrix)):
    for j in range(len(matrix[i])):
        print(f"[{i}][{j}] = {matrix[i][j]}")
```

### Creating a Matrix with a Loop

```python
# 3x3 matrix of zeros
rows, cols = 3, 3
matrix = [[0] * cols for _ in range(rows)]
print(matrix)    # [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
```

### The Wrong Way to Create a Matrix

A very common bug:

```python
# WRONG
matrix = [[0] * 3] * 3
matrix[0][0] = 99
print(matrix)    # [[99, 0, 0], [99, 0, 0], [99, 0, 0]]
                 # all 3 rows are the SAME list object
```

**Correct approach:**
```python
matrix = [[0] * 3 for _ in range(3)]
matrix[0][0] = 99
print(matrix)    # [[99, 0, 0], [0, 0, 0], [0, 0, 0]]
                 # each row is an independent list
```

---

## List Unpacking

Extracting list values directly into named variables.

### Basic Unpacking — Must Match Exactly

```python
coords = [10, 20, 30]
x, y, z = coords
print(x)    # 10
print(y)    # 20
print(z)    # 30
```

Mismatch raises ValueError:

```python
a, b = [1, 2, 3]     # ValueError: too many values to unpack
a, b, c, d = [1, 2]  # ValueError: not enough values to unpack
```

### * — Starred Expression, Captures the Rest

```python
first, *rest = [1, 2, 3, 4, 5]
print(first)    # 1
print(rest)     # [2, 3, 4, 5]

*start, last = [1, 2, 3, 4, 5]
print(start)    # [1, 2, 3, 4]
print(last)     # 5

first, *middle, last = [1, 2, 3, 4, 5]
print(first)     # 1
print(middle)    # [2, 3, 4]
print(last)      # 5
```

Works on a two-element list too:

```python
first, *rest = [1, 2]
print(first)    # 1
print(rest)     # [2]

first, *rest = [1]
print(first)    # 1
print(rest)     # []  — empty list, no error
```

### Unpacking in Loops

```python
pairs = [[1, "one"], [2, "two"], [3, "three"]]

for num, word in pairs:
    print(f"{num} = {word}")
# 1 = one
# 2 = two
# 3 = three
```

Swap using unpacking (from 0.2):

```python
a, b = 5, 10
a, b = b, a
```

### Nested Unpacking

```python
data = [1, [2, 3], 4]
a, (b, c), d = data
print(a, b, c, d)    # 1 2 3 4
```

---

## Putting It All Together

A realistic list processing example:

```python
scores = [88, 45, 92, 67, 45, 78, 92, 55]

# Basic stats
print(f"Count: {len(scores)}")
print(f"Highest: {max(scores)}")
print(f"Lowest: {min(scores)}")
print(f"Total: {sum(scores)}")
print(f"Average: {sum(scores) / len(scores):.1f}")

# How many passed (>= 60)?
passed = [s for s in scores if s >= 60]
print(f"Passed: {len(passed)}")

# Sorted copy — original preserved
ranked = sorted(scores, reverse=True)
print(f"Ranked: {ranked}")

# Remove duplicates (preserving order)
seen = []
unique = []
for s in scores:
    if s not in seen:
        seen.append(s)
        unique.append(s)
print(f"Unique scores: {unique}")

# Top scorer
first, *others = ranked
print(f"Top score: {first}, rest: {others}")
```

---

## Key Takeaways

- `b = a` is **not a copy** — it's two names for the same list. Use `.copy()` or `[:]`.
- `.append()` adds one item. `.extend()` unpacks and adds multiple. Don't confuse them.
- `.sort()` and `.reverse()` mutate in-place and return `None`. `sorted()` and `[::-1]` return new lists.
- `.pop()` is the only removal method that returns the removed value.
- `.remove()` deletes by value (first match). `del` deletes by index. `.pop()` deletes by index and returns it.
- Nested list creation: always use `[[0]*cols for _ in range(rows)]`, never `[[0]*cols]*rows`.
- Shallow copy is fine for flat lists. Use `copy.deepcopy()` for nested lists.
- `first, *rest = list` — starred unpacking captures leftover items as a list.
