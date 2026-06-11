# DSA Roadmap — Pattern-First, Python, Product Company Track
### Mohamed Salman · Side Track · 1 hr/day after DSA Gateway is cleared

> **How to use this roadmap:**
> Every topic follows the same 4-step loop — no exceptions.
>
> ```
> 1. Watch NeetCode concept video for the pattern
> 2. Understand the pattern template — write it from scratch
> 3. Solve problems: Easy first, then Medium
> 4. Stuck beyond 25 min → watch NeetCode solution walkthrough
> ```
>
> Do not jump topics. Each one builds on the previous.
> This roadmap follows the **NeetCode 150 order** — the most battle-tested interview sequence available.

---

## Resource Map

| Resource | Use For | Link |
|---|---|---|
| NeetCode YouTube | Concept + problem walkthroughs (Python) | youtube.com/c/neetcode |
| NeetCode 150 | Your primary problem list | neetcode.io/practice |
| Abdul Bari | Deep concept explanation when NeetCode feels fast | Search "Abdul Bari" on YouTube |
| LeetCode | Where you actually solve problems | leetcode.com |
| Back To Back SWE | Hard topic deep dives (specific videos only) | Search on YouTube |

---

## Difficulty Legend

| Tag | Meaning |
|---|---|
| `[E]` | LeetCode Easy |
| `[M]` | LeetCode Medium |
| `[H]` | LeetCode Hard |
| `🔴` | High frequency — appears constantly in Indian product company interviews |
| `🟡` | Medium frequency — good to know solidly |
| `🟢` | Lower frequency — do after the reds and yellows |

---
---

# STAGE 1 — Foundation Patterns
### Topics 1–5 · Build your base before touching anything else

---

## Topic 1 — Arrays & Hashing
### ⏱ Time: ~1 week · 🔴 Very High Frequency

**The pattern:**
Use a hash map or hash set to trade space for time.
Whenever a brute-force O(n²) solution exists, ask: "Can I store something in a dict to avoid the inner loop?"

**Python tools needed:**
- `dict`, `set`, `collections.Counter`, `collections.defaultdict`
- `enumerate()`, `zip()`

**Core patterns inside this topic:**

```
Pattern A — Frequency Count
  → Use Counter or defaultdict to count occurrences
  → Ask: "Do two things have the same frequency profile?"

Pattern B — Existence Check
  → Use a set for O(1) lookup
  → Ask: "Have I seen this value before?"

Pattern C — Grouping
  → Use defaultdict(list) to bucket items by a key
  → Ask: "What property groups these items together?"
```

**Problems to solve (in this order):**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Contains Duplicate | `[E]` 🔴 | Existence Check | ✅ Watch first |
| 2 | Valid Anagram | `[E]` 🔴 | Frequency Count | ✅ Watch first |
| 3 | Two Sum | `[E]` 🔴 | Existence Check + complement | ✅ Watch first |
| 4 | Group Anagrams | `[M]` 🔴 | Grouping | ✅ Watch first |
| 5 | Top K Frequent Elements | `[M]` 🔴 | Frequency + bucket sort | ✅ Watch first |
| 6 | Product of Array Except Self | `[M]` 🔴 | Prefix/suffix arrays | ✅ Watch first |
| 7 | Valid Sudoku | `[M]` 🟡 | Hash set per row/col/box | Try first |
| 8 | Encode and Decode Strings | `[M]` 🟡 | Custom serialization | Try first |
| 9 | Longest Consecutive Sequence | `[M]` 🔴 | Set + sequence detection | ✅ Watch first |

**Template to memorize:**
```python
# Two Sum — hash map pattern
def two_sum(nums, target):
    seen = {}  # val → index
    for i, n in enumerate(nums):
        diff = target - n
        if diff in seen:
            return [seen[diff], i]
        seen[n] = i
```

---

## Topic 2 — Two Pointers
### ⏱ Time: ~4 days · 🔴 Very High Frequency

**The pattern:**
Use two index variables on the same array (or two arrays).
Eliminates the need for nested loops when the input is sorted or when you're comparing from both ends.

**When to recognize it:**
- Input is sorted (or can be sorted)
- You're comparing pairs or need to find a pair with a property
- "Remove duplicates", "palindrome", "sum to target"

**Core patterns inside this topic:**

```
Pattern A — Opposite Ends
  → left = 0, right = len - 1, move toward each other
  → Use when: sum problems, palindrome check, container problems

Pattern B — Same Direction (Fast/Slow)
  → slow and fast pointer both start at 0
  → Use when: removing elements in-place, Dutch flag problem
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Valid Palindrome | `[E]` 🔴 | Opposite ends | ✅ Watch first |
| 2 | Two Sum II (sorted input) | `[M]` 🔴 | Opposite ends | ✅ Watch first |
| 3 | 3Sum | `[M]` 🔴 | Sort + opposite ends | ✅ Watch first |
| 4 | Container With Most Water | `[M]` 🔴 | Opposite ends, greedy move | ✅ Watch first |
| 5 | Trapping Rain Water | `[H]` 🟡 | Opposite ends + max tracking | ✅ Watch first |

**Template to memorize:**
```python
# Opposite ends template
def two_pointer(arr):
    left, right = 0, len(arr) - 1
    while left < right:
        if condition_met(arr[left], arr[right]):
            # found answer
            pass
        elif should_move_left(arr[left], arr[right]):
            left += 1
        else:
            right -= 1
```

---

## Topic 3 — Sliding Window
### ⏱ Time: ~5 days · 🔴 Very High Frequency

**The pattern:**
Maintain a window (subarray or substring) that expands and shrinks.
Use when the problem asks about a contiguous subarray or substring with some condition.

**When to recognize it:**
- "Longest/shortest subarray/substring with property X"
- "Maximum sum of subarray of size k"
- "Minimum window containing all characters"

**Core patterns inside this topic:**

```
Pattern A — Fixed Size Window
  → Window size k is given
  → Slide by adding right element, removing left element

Pattern B — Variable Size Window
  → Expand right freely, shrink left when constraint violated
  → Track window state with a hash map or counter
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Best Time to Buy and Sell Stock | `[E]` 🔴 | Variable window (track min) | ✅ Watch first |
| 2 | Longest Substring Without Repeating Characters | `[M]` 🔴 | Variable window + set | ✅ Watch first |
| 3 | Longest Repeating Character Replacement | `[M]` 🔴 | Variable window + freq map | ✅ Watch first |
| 4 | Permutation in String | `[M]` 🟡 | Fixed window + Counter | ✅ Watch first |
| 5 | Minimum Window Substring | `[H]` 🔴 | Variable window + Counter | ✅ Watch first |
| 6 | Sliding Window Maximum | `[H]` 🟡 | Fixed window + deque | ✅ Watch first |

**Template to memorize:**
```python
# Variable sliding window template
def sliding_window(s):
    left = 0
    window = {}  # or Counter
    result = 0

    for right in range(len(s)):
        # expand window — add s[right]
        window[s[right]] = window.get(s[right], 0) + 1

        # shrink window — while constraint violated
        while window_is_invalid(window):
            window[s[left]] -= 1
            if window[s[left]] == 0:
                del window[s[left]]
            left += 1

        # update result
        result = max(result, right - left + 1)

    return result
```

---

## Topic 4 — Stack
### ⏱ Time: ~5 days · 🔴 Very High Frequency

**The pattern:**
LIFO structure. Use when you need to remember the "most recent" thing,
or when you need to undo/cancel the previous element based on the current one.

**When to recognize it:**
- Matching brackets/parentheses
- "Next greater element"
- Evaluating expressions
- "Valid" sequence problems

**Core patterns inside this topic:**

```
Pattern A — Matching/Validation
  → Push opening elements, pop and compare when closing element found

Pattern B — Monotonic Stack
  → Maintain a stack that is always increasing or always decreasing
  → Use for: Next Greater Element, Largest Rectangle in Histogram
  → Key insight: pop elements that the current element "defeats"
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Valid Parentheses | `[E]` 🔴 | Matching | ✅ Watch first |
| 2 | Min Stack | `[M]` 🔴 | Auxiliary stack | ✅ Watch first |
| 3 | Evaluate Reverse Polish Notation | `[M]` 🟡 | Expression eval | ✅ Watch first |
| 4 | Generate Parentheses | `[M]` 🔴 | Backtracking + stack thinking | ✅ Watch first |
| 5 | Daily Temperatures | `[M]` 🔴 | Monotonic stack | ✅ Watch first |
| 6 | Car Fleet | `[M]` 🟡 | Monotonic stack variant | Try first |
| 7 | Largest Rectangle in Histogram | `[H]` 🟡 | Monotonic stack | ✅ Watch first |

**Template to memorize:**
```python
# Monotonic stack — Next Greater Element
def next_greater(nums):
    result = [-1] * len(nums)
    stack = []  # stores indices

    for i, n in enumerate(nums):
        while stack and nums[stack[-1]] < n:
            idx = stack.pop()
            result[idx] = n
        stack.append(i)

    return result
```

---

## Topic 5 — Binary Search
### ⏱ Time: ~5 days · 🔴 Very High Frequency

**The pattern:**
Eliminate half the search space each iteration.
Use when the input is sorted OR when you can define a monotonic condition (true/false boundary).

**When to recognize it:**
- Input array is sorted
- "Find the minimum/maximum X such that condition Y is true"
- Search for a boundary, not just a value

**Core patterns inside this topic:**

```
Pattern A — Standard Binary Search
  → Find exact value in sorted array

Pattern B — Left/Right Boundary Search
  → Find first true or last false in a boolean condition array
  → Template: bisect_left / bisect_right logic

Pattern C — Binary Search on Answer
  → The "answer" itself is the search space, not the array
  → Ask: "Is X feasible?" as the condition
  → Use for: Koko eating bananas, minimum capacity, split array
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Binary Search | `[E]` 🔴 | Standard | ✅ Watch first |
| 2 | Search a 2D Matrix | `[M]` 🔴 | Standard on flattened 2D | ✅ Watch first |
| 3 | Koko Eating Bananas | `[M]` 🔴 | BS on answer | ✅ Watch first |
| 4 | Find Minimum in Rotated Sorted Array | `[M]` 🔴 | BS on rotated array | ✅ Watch first |
| 5 | Search in Rotated Sorted Array | `[M]` 🔴 | BS on rotated array | ✅ Watch first |
| 6 | Time Based Key-Value Store | `[M]` 🟡 | BS on sorted values | Try first |
| 7 | Median of Two Sorted Arrays | `[H]` 🟢 | BS on partition | ✅ Watch first |

**Template to memorize:**
```python
# Universal binary search template
def binary_search(lo, hi):
    while lo < hi:
        mid = (lo + hi) // 2
        if feasible(mid):
            hi = mid       # move left boundary
        else:
            lo = mid + 1   # move right boundary
    return lo  # first True position
```

---
---

# STAGE 2 — Linear Data Structures
### Topics 6–7 · Pointers and node-based structures

---

## Topic 6 — Linked List
### ⏱ Time: ~1 week · 🔴 High Frequency

**The pattern:**
Chain of nodes where each node points to the next.
All problems reduce to: pointer manipulation, two-pointer technique, or dummy head trick.

**Python setup — always start with this:**
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

**Core patterns inside this topic:**

```
Pattern A — Dummy Head
  → Create a dummy node before the list to simplify edge cases
  → Return dummy.next at the end

Pattern B — Fast and Slow Pointers
  → slow moves 1 step, fast moves 2 steps
  → Use for: find middle, detect cycle, find cycle start

Pattern C — In-Place Reversal
  → Three pointers: prev, curr, next_node
  → Iterative reversal is always preferred over recursive
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Reverse Linked List | `[E]` 🔴 | In-place reversal | ✅ Watch first |
| 2 | Merge Two Sorted Lists | `[E]` 🔴 | Dummy head + merge | ✅ Watch first |
| 3 | Linked List Cycle | `[E]` 🔴 | Fast/slow pointers | ✅ Watch first |
| 4 | Reorder List | `[M]` 🔴 | Find middle + reverse + merge | ✅ Watch first |
| 5 | Remove Nth Node From End | `[M]` 🔴 | Fast/slow with gap n | ✅ Watch first |
| 6 | Copy List with Random Pointer | `[M]` 🟡 | Hash map for node mapping | ✅ Watch first |
| 7 | Add Two Numbers | `[M]` 🟡 | Digit-by-digit traversal | Try first |
| 8 | Find the Duplicate Number | `[M]` 🟡 | Floyd's cycle detection | ✅ Watch first |
| 9 | LRU Cache | `[M]` 🔴 | Doubly linked list + dict | ✅ Watch first |
| 10 | Merge K Sorted Lists | `[H]` 🟡 | Heap + dummy head | ✅ Watch first |
| 11 | Reverse Nodes in K-Group | `[H]` 🟢 | In-place reversal in chunks | ✅ Watch first |

**Template to memorize:**
```python
# In-place reversal
def reverse_list(head):
    prev, curr = None, head
    while curr:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    return prev

# Fast/slow — find middle
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

---
---

# STAGE 3 — Tree Patterns
### Topics 7–8 · The most interview-dense area

---

## Topic 7 — Trees
### ⏱ Time: ~2 weeks · 🔴 Very High Frequency

**The pattern:**
Almost every tree problem is a DFS or BFS traversal with a specific thing to compute at each node.
The recursive DFS template is the foundation of 80% of tree problems.

**Python setup — always start with this:**
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

**Core patterns inside this topic:**

```
Pattern A — Recursive DFS (most common)
  → def dfs(node): base case → recurse left → recurse right → return
  → Ask at each node: "What do I need from my children to answer the question?"

Pattern B — Iterative DFS (using stack)
  → Use when recursion depth is too large
  → stack = [root], pop and process, push children

Pattern C — BFS / Level Order (using deque)
  → Process nodes level by level
  → queue = deque([root]), popleft, add children

Pattern D — BST Properties
  → In-order traversal gives sorted output
  → Use BST property (left < root < right) to search in O(log n)
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Invert Binary Tree | `[E]` 🔴 | Recursive DFS | ✅ Watch first |
| 2 | Maximum Depth of Binary Tree | `[E]` 🔴 | Recursive DFS | ✅ Watch first |
| 3 | Diameter of Binary Tree | `[E]` 🔴 | DFS — return height, track diameter | ✅ Watch first |
| 4 | Balanced Binary Tree | `[E]` 🔴 | DFS — return height or -1 | ✅ Watch first |
| 5 | Same Tree | `[E]` 🟡 | Recursive DFS | Try first |
| 6 | Subtree of Another Tree | `[E]` 🟡 | DFS + same tree check | Try first |
| 7 | Binary Tree Level Order Traversal | `[M]` 🔴 | BFS with deque | ✅ Watch first |
| 8 | Binary Tree Right Side View | `[M]` 🔴 | BFS, take last per level | ✅ Watch first |
| 9 | Count Good Nodes in Binary Tree | `[M]` 🟡 | DFS — pass max seen so far | Try first |
| 10 | Validate Binary Search Tree | `[M]` 🔴 | DFS — pass min/max bounds | ✅ Watch first |
| 11 | Kth Smallest Element in BST | `[M]` 🔴 | In-order traversal | ✅ Watch first |
| 12 | Lowest Common Ancestor of BST | `[M]` 🔴 | BST property — left/right navigation | ✅ Watch first |
| 13 | Binary Tree Level Order Traversal II | `[M]` 🟡 | BFS reversed | Try first |
| 14 | Path Sum II | `[M]` 🟡 | DFS — track running sum | Try first |
| 15 | Construct Tree from Preorder+Inorder | `[M]` 🟡 | DFS — index tracking | ✅ Watch first |
| 16 | Max Path Sum | `[H]` 🔴 | DFS — return single path, track global | ✅ Watch first |
| 17 | Serialize and Deserialize Binary Tree | `[H]` 🟡 | BFS or DFS serialization | ✅ Watch first |

**Template to memorize:**
```python
# Recursive DFS — the base template for 80% of tree problems
def dfs(node):
    if not node:
        return base_value  # 0 for height, True for validity, etc.

    left = dfs(node.left)
    right = dfs(node.right)

    # compute answer using left, right, node.val
    return something

# BFS — level order
from collections import deque
def bfs(root):
    if not root:
        return []
    queue = deque([root])
    result = []
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result
```

---

## Topic 8 — Tries
### ⏱ Time: ~3 days · 🟡 Medium Frequency

**The pattern:**
A tree where each node represents a character.
Used for prefix-based search, autocomplete, word validation.

**Core patterns inside this topic:**

```
Pattern A — Standard Trie
  → children: dict mapping char → TrieNode
  → is_end: bool marking word completion

Pattern B — Trie + DFS
  → Traverse trie while simultaneously traversing a string or grid
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Implement Trie (Prefix Tree) | `[M]` 🔴 | Standard Trie | ✅ Watch first |
| 2 | Design Add and Search Words Data Structure | `[M]` 🟡 | Trie + DFS for wildcards | ✅ Watch first |
| 3 | Word Search II | `[H]` 🟡 | Trie + DFS on grid | ✅ Watch first |

**Template to memorize:**
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def search(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.is_end

    def startsWith(self, prefix):
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True
```

---
---

# STAGE 4 — Recursion-Based Patterns
### Topics 9–10 · The hardest mental shift, but the most rewarding

---

## Topic 9 — Backtracking
### ⏱ Time: ~1 week · 🔴 High Frequency

**The pattern:**
Build a solution incrementally. At each step, make a choice.
If the choice leads to a dead end, undo it (backtrack) and try the next choice.

**The single most important thing:**
Every backtracking problem uses the SAME template. Learn the template cold.
Once you see it, you will recognize every backtracking problem instantly.

**Core patterns inside this topic:**

```
Pattern A — Subsets / Combinations
  → At each index, choose to include or exclude
  → No duplicates in input → straightforward
  → Duplicates in input → sort first, skip duplicates

Pattern B — Permutations
  → Every element must be used exactly once
  → Track which elements are used with a set or boolean array

Pattern C — Grid / String DFS
  → Explore all paths in a 2D grid or string
  → Mark visited, recurse, unmark (backtrack)
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Subsets | `[M]` 🔴 | Include/exclude | ✅ Watch first |
| 2 | Combination Sum | `[M]` 🔴 | Combinations, reuse allowed | ✅ Watch first |
| 3 | Permutations | `[M]` 🔴 | Permutations template | ✅ Watch first |
| 4 | Subsets II (with duplicates) | `[M]` 🟡 | Sort + skip duplicates | ✅ Watch first |
| 5 | Combination Sum II (with duplicates) | `[M]` 🟡 | Sort + skip duplicates | Try first |
| 6 | Word Search | `[M]` 🔴 | Grid DFS + visited marking | ✅ Watch first |
| 7 | Palindrome Partitioning | `[M]` 🟡 | DFS + palindrome check | ✅ Watch first |
| 8 | Letter Combinations of Phone Number | `[M]` 🟡 | Combinations from choices | Try first |
| 9 | N-Queens | `[H]` 🟡 | Constraint-based placement | ✅ Watch first |

**Template to memorize:**
```python
# Universal backtracking template
def backtrack(start, current_path, result):
    if is_solution(current_path):
        result.append(list(current_path))  # copy, not reference
        return

    for choice in get_choices(start):
        if is_valid(choice):
            current_path.append(choice)           # make choice
            backtrack(next_start, current_path, result)  # recurse
            current_path.pop()                    # undo choice (backtrack)
```

---

## Topic 10 — Heap / Priority Queue
### ⏱ Time: ~5 days · 🔴 High Frequency

**The pattern:**
Use when you need repeated access to the minimum or maximum element.
Python only has a min-heap. For max-heap, negate all values.

**When to recognize it:**
- "Top K", "K largest", "K smallest", "K most frequent"
- "Merge K sorted lists/arrays"
- "Find the median of a data stream"
- Any problem where you repeatedly need the smallest/largest remaining

**Core patterns inside this topic:**

```
Pattern A — Top K Elements
  → Maintain a heap of size K
  → Push each element, pop when heap exceeds K
  → Result: the K elements remaining in the heap

Pattern B — K-Way Merge
  → Push the first element of each list into a heap
  → Pop minimum, push the next element from that list

Pattern C — Two Heaps (median finding)
  → Max-heap for lower half, min-heap for upper half
  → Balance sizes to always get the median in O(1)
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Kth Largest Element in an Array | `[M]` 🔴 | Top K | ✅ Watch first |
| 2 | Last Stone Weight | `[E]` 🔴 | Max-heap (negate) | ✅ Watch first |
| 3 | K Closest Points to Origin | `[M]` 🔴 | Top K | ✅ Watch first |
| 4 | Task Scheduler | `[M]` 🟡 | Max-heap + greedy | ✅ Watch first |
| 5 | Design Twitter | `[M]` 🟡 | K-way merge | ✅ Watch first |
| 6 | Find Median from Data Stream | `[H]` 🔴 | Two heaps | ✅ Watch first |
| 7 | Merge K Sorted Lists | `[H]` 🟡 | K-way merge | ✅ Watch first (also in LL topic) |

**Template to memorize:**
```python
import heapq

# Top K largest elements
def top_k_largest(nums, k):
    min_heap = []
    for n in nums:
        heapq.heappush(min_heap, n)
        if len(min_heap) > k:
            heapq.heappop(min_heap)
    return list(min_heap)

# Max-heap using negation
def max_heap_example(nums):
    max_heap = [-n for n in nums]
    heapq.heapify(max_heap)
    return -heapq.heappop(max_heap)  # returns the max
```

---
---

# STAGE 5 — Graph Patterns
### Topics 11–12 · High weight in senior-level Indian product company interviews

---

## Topic 11 — Graphs
### ⏱ Time: ~2 weeks · 🔴 High Frequency

**The pattern:**
Graphs are trees with cycles and multiple entry points.
Every graph problem is either: traversal, shortest path, connectivity, or ordering.

**Python setup:**
```python
from collections import defaultdict, deque

# Build adjacency list from edge list
graph = defaultdict(list)
for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)  # for undirected
```

**Core patterns inside this topic:**

```
Pattern A — DFS / BFS Traversal
  → visited = set() to avoid revisiting
  → BFS for shortest path (unweighted), DFS for connectivity/components

Pattern B — Connected Components
  → Count islands, number of provinces
  → DFS/BFS from each unvisited node, increment counter

Pattern C — Topological Sort
  → Only for Directed Acyclic Graphs (DAGs)
  → Kahn's algorithm: start from nodes with in-degree 0
  → Use for: course schedule, task ordering

Pattern D — Union-Find (DSU)
  → Group nodes into connected sets
  → find() with path compression, union() with rank
  → Use for: redundant connections, number of components

Pattern E — Dijkstra's (weighted shortest path)
  → min-heap + distance array
  → Only works with non-negative weights
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Number of Islands | `[M]` 🔴 | DFS/BFS connected components | ✅ Watch first |
| 2 | Max Area of Island | `[M]` 🔴 | DFS connected components | Try first |
| 3 | Clone Graph | `[M]` 🟡 | DFS + hash map for visited copies | ✅ Watch first |
| 4 | Walls and Gates | `[M]` 🟡 | Multi-source BFS | ✅ Watch first |
| 5 | Rotting Oranges | `[M]` 🔴 | Multi-source BFS | ✅ Watch first |
| 6 | Pacific Atlantic Water Flow | `[M]` 🟡 | Reverse BFS from edges | ✅ Watch first |
| 7 | Surrounded Regions | `[M]` 🟡 | DFS from border | Try first |
| 8 | Course Schedule | `[M]` 🔴 | Topological sort / cycle detection | ✅ Watch first |
| 9 | Course Schedule II | `[M]` 🔴 | Topological sort (Kahn's) | ✅ Watch first |
| 10 | Graph Valid Tree | `[M]` 🟡 | Union-Find or DFS cycle detect | ✅ Watch first |
| 11 | Number of Connected Components | `[M]` 🟡 | Union-Find | ✅ Watch first |
| 12 | Redundant Connection | `[M]` 🟡 | Union-Find | ✅ Watch first |
| 13 | Word Ladder | `[H]` 🟡 | BFS with word transformation | ✅ Watch first |
| 14 | Network Delay Time | `[M]` 🟡 | Dijkstra's | ✅ Watch first |
| 15 | Cheapest Flights Within K Stops | `[M]` 🟡 | Bellman-Ford modified | ✅ Watch first |

**Template to memorize:**
```python
# DFS traversal
def dfs(node, visited, graph):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor, visited, graph)

# BFS shortest path
def bfs(start, graph):
    visited = {start}
    queue = deque([(start, 0)])  # (node, distance)
    while queue:
        node, dist = queue.popleft()
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))

# Kahn's topological sort
def topo_sort(n, edges):
    graph = defaultdict(list)
    in_degree = [0] * n
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    order = []
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    return order if len(order) == n else []  # empty = cycle exists

# Union-Find
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py: return False
        if self.rank[px] < self.rank[py]: px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]: self.rank[px] += 1
        return True
```

---

## Topic 12 — Advanced Graphs
### ⏱ Time: ~4 days · 🟢 Lower Frequency (but good to have)

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Reconstruct Itinerary | `[H]` 🟢 | Eulerian path + DFS | ✅ Watch first |
| 2 | Min Cost to Connect All Points | `[M]` 🟡 | Prim's MST | ✅ Watch first |
| 3 | Swim in Rising Water | `[H]` 🟢 | Dijkstra variant | ✅ Watch first |
| 4 | Alien Dictionary | `[H]` 🟡 | Topological sort from char ordering | ✅ Watch first |

---
---

# STAGE 6 — Dynamic Programming
### Topics 13–14 · The deepest pattern — do not rush this

---

## Topic 13 — 1D Dynamic Programming
### ⏱ Time: ~1.5 weeks · 🔴 Very High Frequency

**The pattern:**
Break a problem into subproblems. Cache the result of each subproblem.
If you have already solved smaller versions of the problem, use those results.

**The mental shift:**
DP is not about memorizing formulas. It is about identifying:
1. What is the subproblem?
2. What is the recurrence (how does the answer for problem N depend on smaller problems)?
3. What are the base cases?

**Start with top-down (memoization) — it is easier to think about:**
```python
from functools import lru_cache

@lru_cache(maxsize=None)
def dp(i):
    if i <= 1: return base_case
    return dp(i-1) + dp(i-2)  # recurrence
```

**Core patterns inside this topic:**

```
Pattern A — Linear DP
  → dp[i] depends only on dp[i-1] or dp[i-2]
  → Fibonacci, climbing stairs, house robber

Pattern B — Unbounded Knapsack
  → Items can be reused
  → dp[i] = min/max over all items that fit

Pattern C — 0/1 Knapsack
  → Each item used at most once
  → Requires 2D or space-optimized 1D DP

Pattern D — LIS (Patience Sort)
  → dp[i] = length of LIS ending at index i
  → O(n log n) version uses bisect
```

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Climbing Stairs | `[E]` 🔴 | Linear DP (Fibonacci) | ✅ Watch first |
| 2 | Min Cost Climbing Stairs | `[E]` 🔴 | Linear DP | Try first |
| 3 | House Robber | `[M]` 🔴 | Linear DP | ✅ Watch first |
| 4 | House Robber II (circular) | `[M]` 🔴 | Run House Robber twice | ✅ Watch first |
| 5 | Longest Palindromic Substring | `[M]` 🔴 | Expand around center | ✅ Watch first |
| 6 | Palindromic Substrings | `[M]` 🟡 | Expand around center | Try first |
| 7 | Decode Ways | `[M]` 🔴 | Linear DP with conditions | ✅ Watch first |
| 8 | Coin Change | `[M]` 🔴 | Unbounded knapsack | ✅ Watch first |
| 9 | Maximum Product Subarray | `[M]` 🔴 | Track max and min | ✅ Watch first |
| 10 | Word Break | `[M]` 🔴 | Linear DP + set lookup | ✅ Watch first |
| 11 | Longest Increasing Subsequence | `[M]` 🔴 | LIS — O(n²) then O(n log n) | ✅ Watch first |
| 12 | Partition Equal Subset Sum | `[M]` 🟡 | 0/1 knapsack | ✅ Watch first |

---

## Topic 14 — 2D Dynamic Programming
### ⏱ Time: ~1 week · 🔴 High Frequency

**The pattern:**
Same as 1D DP but the state has two dimensions.
Usually: dp[i][j] = answer for the first i characters of string 1 and first j characters of string 2.

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Unique Paths | `[M]` 🔴 | Grid DP | ✅ Watch first |
| 2 | Longest Common Subsequence | `[M]` 🔴 | Classic 2D DP | ✅ Watch first |
| 3 | Best Time to Buy/Sell Stock with Cooldown | `[M]` 🟡 | State machine DP | ✅ Watch first |
| 4 | Coin Change II | `[M]` 🟡 | 2D knapsack | ✅ Watch first |
| 5 | Target Sum | `[M]` 🟡 | 2D knapsack variant | ✅ Watch first |
| 6 | Interleaving String | `[M]` 🟡 | Classic 2D DP | ✅ Watch first |
| 7 | Longest Increasing Path in Matrix | `[H]` 🟡 | DFS + memoization | ✅ Watch first |
| 8 | Distinct Subsequences | `[H]` 🟢 | 2D DP | ✅ Watch first |
| 9 | Edit Distance | `[M]` 🔴 | Classic 2D DP | ✅ Watch first |
| 10 | Burst Balloons | `[H]` 🟢 | Interval DP | ✅ Watch first |
| 11 | Regular Expression Matching | `[H]` 🟢 | 2D DP with wildcards | ✅ Watch first |

---
---

# STAGE 7 — Remaining Patterns
### Topics 15–18 · Do these after completing Stages 1–6

---

## Topic 15 — Greedy
### ⏱ Time: ~4 days · 🟡 Medium Frequency

**The pattern:**
Make the locally optimal choice at each step.
No DP table needed — one pass is usually enough.
Greedy works when the locally optimal choice always leads to a globally optimal solution.

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Maximum Subarray | `[M]` 🔴 | Kadane's algorithm | ✅ Watch first |
| 2 | Jump Game | `[M]` 🔴 | Track max reachable index | ✅ Watch first |
| 3 | Jump Game II | `[M]` 🔴 | Greedy interval expansion | ✅ Watch first |
| 4 | Gas Station | `[M]` 🟡 | Reset when tank goes negative | ✅ Watch first |
| 5 | Hand of Straights | `[M]` 🟡 | Greedy with sorted Counter | Try first |
| 6 | Merge Triplets to Form Target | `[M]` 🟢 | Greedy selection | Try first |
| 7 | Partition Labels | `[M]` 🟡 | Last occurrence tracking | ✅ Watch first |
| 8 | Valid Parenthesis String | `[M]` 🟡 | Track min/max open count | ✅ Watch first |

---

## Topic 16 — Intervals
### ⏱ Time: ~3 days · 🔴 High Frequency

**The pattern:**
Sort by start time. Then decide: overlap → merge, or no overlap → keep separate.
Always ask: does interval A end before interval B starts?

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Insert Interval | `[M]` 🔴 | Linear scan + merge | ✅ Watch first |
| 2 | Merge Intervals | `[M]` 🔴 | Sort + merge | ✅ Watch first |
| 3 | Non-overlapping Intervals | `[M]` 🔴 | Greedy — remove minimum | ✅ Watch first |
| 4 | Meeting Rooms | `[E]` 🔴 | Sort + check overlap | ✅ Watch first |
| 5 | Meeting Rooms II | `[M]` 🔴 | Min-heap for end times | ✅ Watch first |
| 6 | Minimum Interval to Include Each Query | `[H]` 🟢 | Offline + heap | ✅ Watch first |

---

## Topic 17 — Bit Manipulation
### ⏱ Time: ~3 days · 🟡 Medium Frequency

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Single Number | `[E]` 🔴 | XOR — a^a=0, a^0=a | ✅ Watch first |
| 2 | Number of 1 Bits | `[E]` 🔴 | n & (n-1) trick | ✅ Watch first |
| 3 | Counting Bits | `[E]` 🟡 | DP with bit trick | ✅ Watch first |
| 4 | Reverse Bits | `[E]` 🟡 | Bit shifting | ✅ Watch first |
| 5 | Missing Number | `[E]` 🔴 | XOR or Gauss sum | Try first |
| 6 | Sum of Two Integers (no + operator) | `[M]` 🟡 | XOR + carry | ✅ Watch first |
| 7 | Reverse Integer | `[M]` 🟢 | Math + overflow check | Try first |

---

## Topic 18 — Math & Geometry
### ⏱ Time: ~3 days · 🟢 Lower Frequency

**Problems to solve:**

| # | Problem | Difficulty | Pattern | NeetCode Video |
|---|---|---|---|---|
| 1 | Rotate Image | `[M]` 🔴 | Transpose + reverse rows | ✅ Watch first |
| 2 | Spiral Matrix | `[M]` 🔴 | Boundary shrinking | ✅ Watch first |
| 3 | Set Matrix Zeroes | `[M]` 🟡 | In-place flag marking | ✅ Watch first |
| 4 | Happy Number | `[E]` 🟡 | Fast/slow pointer on sum | Try first |
| 5 | Plus One | `[E]` 🟢 | Carry propagation | Try first |
| 6 | Pow(x, n) | `[M]` 🟡 | Fast exponentiation | ✅ Watch first |
| 7 | Multiply Strings | `[M]` 🟢 | Grade school multiplication | Try first |
| 8 | Detect Squares | `[M]` 🟢 | Hash map geometry | ✅ Watch first |

---
---

# Problem Count Summary

| Stage | Topics | Problems | Difficulty Mix |
|---|---|---|---|
| Stage 1 — Foundation | Arrays, Two Pointers, Sliding Window, Stack, Binary Search | 42 | Mostly Easy/Medium |
| Stage 2 — Linear DS | Linked List | 11 | Medium/Hard |
| Stage 3 — Trees | Trees, Tries | 20 | Medium/Hard |
| Stage 4 — Recursion | Backtracking, Heap | 16 | Medium/Hard |
| Stage 5 — Graphs | Graphs, Advanced Graphs | 19 | Medium/Hard |
| Stage 6 — DP | 1D DP, 2D DP | 23 | Medium/Hard |
| Stage 7 — Remaining | Greedy, Intervals, Bits, Math | 29 | Easy/Medium/Hard |
| **Total** | **18 topics** | **~160 problems** | |

---

# Weekly Schedule (1 hr/day side track)

| Week | Stage | Topics |
|---|---|---|
| Week 1 | Stage 1 | Arrays & Hashing + Two Pointers |
| Week 2 | Stage 1 | Sliding Window + Stack |
| Week 3 | Stage 1 | Binary Search |
| Week 4 | Stage 2 | Linked List |
| Week 5–6 | Stage 3 | Trees |
| Week 7 | Stage 3 | Tries |
| Week 8 | Stage 4 | Backtracking |
| Week 9 | Stage 4 | Heap / Priority Queue |
| Week 10–11 | Stage 5 | Graphs |
| Week 12 | Stage 5 | Advanced Graphs |
| Week 13–14 | Stage 6 | 1D DP |
| Week 15 | Stage 6 | 2D DP |
| Week 16–17 | Stage 7 | Greedy + Intervals + Bits + Math |
| Week 18+ | All | Mock interviews + revision of weak areas |

---

# The One Rule for Every Problem

```
1. Read problem — identify the pattern before writing any code
2. Try solo for 25 minutes maximum
3. If stuck → NeetCode walkthrough
4. After watching — close the video and re-solve from scratch yourself
5. Two days later — solve it again without any help

If you cannot re-solve it two days later → you do not know it yet.
```

---

*NeetCode 150 · Python · Pattern-first · Product company track*
*Total: ~160 problems · ~18 weeks at 1 hr/day*
