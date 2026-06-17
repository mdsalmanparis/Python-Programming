Let's go. Full topic, nothing skipped.

---

## 2.5 `re` — Regex

---

## Why Regex and When to Use It

Regex (regular expressions) is a pattern language for searching, extracting, and transforming text.

```python
import re

# Without regex — verbose, fragile
def extract_emails_manual(text):
    emails = []
    for word in text.split():
        if "@" in word and "." in word:
            emails.append(word.strip(".,;"))
    return emails

# With regex — concise, handles edge cases
def extract_emails_regex(text):
    return re.findall(r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}", text)
```

**When to use regex:**
- Extracting structured data from unstructured text
- Validating formats (email, phone, IP, date)
- Find-and-replace with patterns
- Parsing log files, config files, legacy formats

**When NOT to use regex:**
- Parsing HTML/XML — use `BeautifulSoup` or `lxml`
- Parsing JSON/CSV — use `json` or `csv` modules
- Simple string operations — `str.startswith()`, `str.replace()`, `str.split()` are faster and clearer

---

## Always Use Raw Strings `r"..."`

```python
import re

# Without raw string — Python processes backslashes first
pattern = "\d+"       # Python sees \d as invalid escape → just "d+"
                      # in Python 3.12+ this is a DeprecationWarning

# With raw string — backslashes passed literally to the regex engine
pattern = r"\d+"      # regex engine sees \d+ (one or more digits)

# The problem is clear with \n
print("\n")           # actual newline character
print(r"\n")          # the two characters \ and n

# In regex context
re.search("\n", "hello\nworld")    # matches newline character — fine
re.search(r"\n", "hello\nworld")   # also matches newline — same result here
re.search("\d", "abc123")          # might work or warn depending on Python version
re.search(r"\d", "abc123")         # always correct — use this

# RULE: always use r"..." for regex patterns — no exceptions
```

---

## Character Classes

### Metacharacters — Special Meanings

```python
import re

text = "Hello World 123\nTest_data\t end"

# . — any character EXCEPT newline
re.findall(r".",  "ab\nc")    # ['a', 'b', 'c']  — \n skipped

# \d — any digit [0-9]
re.findall(r"\d", "abc123def456")    # ['1', '2', '3', '4', '5', '6']

# \D — NOT a digit [^0-9]
re.findall(r"\D", "abc123")    # ['a', 'b', 'c']

# \w — word character [a-zA-Z0-9_]
re.findall(r"\w", "hello world_123!")    # ['h','e','l','l','o','w','o','r','l','d','_','1','2','3']

# \W — NOT a word character
re.findall(r"\W", "hello world!")    # [' ', '!']

# \s — whitespace [ \t\n\r\f\v]
re.findall(r"\s", "hello\t world\n")    # ['\t', ' ', '\n']

# \S — NOT whitespace
re.findall(r"\S", "hi there")    # ['h', 'i', 't', 'h', 'e', 'r', 'e']

# \b — word boundary (zero-width assertion)
re.findall(r"\bcat\b", "cat concatenate cats")    # ['cat']  — only whole word
re.findall(r"cat",     "cat concatenate cats")    # ['cat', 'cat', 'cat']
```

### Character Sets `[...]` and `[^...]`

```python
import re

# [abc] — match a, b, or c
re.findall(r"[aeiou]", "hello world")    # ['e', 'o', 'o']

# [a-z] — lowercase letters
re.findall(r"[a-z]+", "Hello World 123")    # ['ello', 'orld']

# [A-Za-z] — any letter
re.findall(r"[A-Za-z]+", "Hello World 123!")    # ['Hello', 'World']

# [0-9] — same as \d
re.findall(r"[0-9]+", "price: 99.99")    # ['99', '99']

# [a-zA-Z0-9_] — same as \w
re.findall(r"[a-zA-Z0-9_]+", "hello_world 123!")    # ['hello_world', '123']

# [^...] — NOT in the set (^ inside [] = negation)
re.findall(r"[^aeiou\s]+", "hello world")    # ['h', 'll', 'w', 'rld']

# Special characters inside [] — most lose special meaning
re.findall(r"[.+*?]", "3.14 + 2 * x?")    # ['.', '+', '*', '?']
                                             # . + * ? are literal inside []

# - must be first, last, or escaped
re.findall(r"[a-z]", "abc")     # range a to z
re.findall(r"[-az]", "a-z")     # literal -, a, or z
re.findall(r"[az-]", "a-z")     # a, z, or literal -

# ] must be first or escaped
re.findall(r"[]abc]", "a]bc")   # ], a, b, or c
re.findall(r"[abc\]]", "a]bc")  # a, b, c, or ]
```

---

## Quantifiers — Greedy and Lazy

### Basic Quantifiers

```python
import re

text = "colour colour"

# * — zero or more (greedy)
re.findall(r"ab*", "a ab abb abbb")    # ['a', 'ab', 'abb', 'abbb']

# + — one or more (greedy)
re.findall(r"ab+", "a ab abb abbb")    # ['ab', 'abb', 'abbb']  — 'a' excluded

# ? — zero or one (optional)
re.findall(r"colou?r", "colour color")  # ['colour', 'color']  — u is optional

# {n} — exactly n times
re.findall(r"\d{3}", "12 123 1234")     # ['123', '123']  — exactly 3 digits

# {n,m} — between n and m times
re.findall(r"\d{2,4}", "1 12 123 1234 12345")    # ['12', '123', '1234', '1234']
                                                   # greedy: takes as many as possible

# {n,} — n or more
re.findall(r"\d{3,}", "12 123 1234 12345")    # ['123', '1234', '12345']
```

### Greedy vs Lazy

**Greedy** — takes as much as possible (default).
**Lazy** — takes as little as possible (add `?` after quantifier).

```python
import re

html = "<b>bold</b> and <i>italic</i>"

# Greedy — matches from first < to LAST >
re.findall(r"<.+>", html)     # ['<b>bold</b> and <i>italic</i>']  — one big match

# Lazy — matches from first < to NEXT >
re.findall(r"<.+?>", html)    # ['<b>', '</b>', '<i>', '</i>']  — individual tags

# More examples
text = "aXbXc"

re.search(r"a.*c",  text).group()    # 'aXbXc'  — greedy, takes all
re.search(r"a.*?c", text).group()    # 'aXbXc'  — lazy, but still must include c

text2 = "aXcXc"
re.search(r"a.*c",  text2).group()   # 'aXcXc'  — greedy takes to LAST c
re.search(r"a.*?c", text2).group()   # 'aXc'    — lazy stops at FIRST c

# Lazy quantifiers
r"*?"    # zero or more, lazy
r"+?"    # one or more, lazy
r"??"    # zero or one, lazy
r"{n,m}?" # n to m, lazy
```

---

## Anchors — Position Assertions

Anchors match positions, not characters. Zero-width.

```python
import re

# ^ — start of string (or line in MULTILINE mode)
re.findall(r"^\d+", "123 abc\n456 def")    # ['123']  — only start of string

# $ — end of string (or line in MULTILINE mode)
re.findall(r"\d+$", "abc 123\ndef 456")    # ['456']  — only end of string

# \A — start of string ONLY (never affected by MULTILINE)
re.findall(r"\A\d+", "123 abc")    # ['123']

# \Z — end of string ONLY (never affected by MULTILINE)
re.findall(r"\d+\Z", "abc 123")    # ['123']

# \b — word boundary — between \w and \W (or start/end of string)
re.findall(r"\bword\b", "word words sword")    # ['word']
re.findall(r"\bcat\b",  "cat cats concatenate")  # ['cat']

# \B — NOT a word boundary
re.findall(r"\Bcat\B", "concatenate")    # ['cat']  — in the middle
re.findall(r"\Bcat\B", "cat")            # []       — at boundaries

# Practical anchors
# Validate exact format
re.fullmatch(r"\d{4}-\d{2}-\d{2}", "2024-11-15")   # match
re.fullmatch(r"\d{4}-\d{2}-\d{2}", "2024-11-1")    # no match — wrong format
```

---

## `re.match()` vs `re.search()` vs `re.fullmatch()` — Critical Difference

This is the most common source of regex bugs.

```python
import re

text = "Hello World 123"

# match() — matches only at the START of the string
result = re.match(r"\d+", text)
print(result)    # None — string doesn't START with digits

result = re.match(r"Hello", text)
print(result)    # <re.Match object> — string STARTS with "Hello"

result = re.match(r"World", text)
print(result)    # None — "World" is not at the start

# search() — searches ANYWHERE in the string, returns first match
result = re.search(r"\d+", text)
print(result)           # <re.Match object>
print(result.group())   # 123  — found digits anywhere

result = re.search(r"World", text)
print(result.group())   # World

# fullmatch() — the ENTIRE string must match the pattern
result = re.fullmatch(r"\d+", "12345")
print(result)    # <re.Match object> — entire string is digits

result = re.fullmatch(r"\d+", "123abc")
print(result)    # None — not ALL digits

result = re.fullmatch(r"Hello.*\d+", text)
print(result)    # <re.Match object> — entire string matches
```

**The key difference:**

```python
import re

s = "the cat sat on the mat"

# match — anchored to start
re.match(r"cat", s)       # None — 'cat' not at start

# search — finds anywhere
re.search(r"cat", s)      # match at position 4

# fullmatch — entire string must match
re.fullmatch(r"cat", s)   # None — more than just 'cat'
re.fullmatch(r".*cat.*", s)  # match — .* covers the rest

# Common mistake
email = "user@example.com"
re.match(r"\w+@\w+\.\w+", email)      # match — but also matches "user@example.com extra"
re.fullmatch(r"\w+@\w+\.\w+", email)  # safer for validation
```

**Rule of thumb:**
- `search()` — finding something in text
- `match()` — text must start with pattern (log parsing, protocol parsing)
- `fullmatch()` — validating that the entire string matches (form validation)

---

## `re.findall()` vs `re.finditer()`

```python
import re

text = "Price: $99.99, Tax: $8.50, Total: $108.49"

# findall() — returns a LIST of all matches (strings)
matches = re.findall(r"\$[\d.]+", text)
print(matches)    # ['$99.99', '$8.50', '$108.49']

# finditer() — returns an ITERATOR of Match objects
for match in re.finditer(r"\$[\d.]+", text):
    print(match.group())    # the matched string
    print(match.start())    # start position
    print(match.end())      # end position
    print(match.span())     # (start, end) tuple

# findall vs finditer — when to use which
# findall  — you just need the strings, all at once
# finditer — you need positions, or lazy evaluation, or Match object info

# findall with groups — returns list of GROUPS not full matches
text = "John:25, Jane:30, Bob:22"
re.findall(r"(\w+):(\d+)", text)
# [('John', '25'), ('Jane', '30'), ('Bob', '22')]  — list of tuples

# finditer with groups — richer access
for m in re.finditer(r"(\w+):(\d+)", text):
    print(m.group(0))   # full match: John:25
    print(m.group(1))   # first group: John
    print(m.group(2))   # second group: 25
```

---

## Groups — Capturing, Named, Non-Capturing

### Capturing Groups `(...)`

```python
import re

text = "2024-11-15"

# () creates a capturing group
m = re.match(r"(\d{4})-(\d{2})-(\d{2})", text)

print(m.group(0))    # 2024-11-15  — full match (group 0)
print(m.group(1))    # 2024        — first group
print(m.group(2))    # 11          — second group
print(m.group(3))    # 15          — third group
print(m.groups())    # ('2024', '11', '15')  — all groups as tuple
```

### Named Groups `(?P<name>...)`

```python
import re

text = "2024-11-15"

m = re.match(r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})", text)

print(m.group("year"))     # 2024
print(m.group("month"))    # 11
print(m.group("day"))      # 15
print(m.groupdict())       # {'year': '2024', 'month': '11', 'day': '15'}

# Named groups in findall — returns list of groupdicts... actually still tuples
# Use finditer for named groups
for m in re.finditer(r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})", text):
    print(m.groupdict())    # {'year': '2024', 'month': '11', 'day': '15'}
```

### Non-Capturing Groups `(?:...)`

```python
import re

# Without (?:...) — group is captured
re.findall(r"(\d+)px", "10px 20px 30px")    # ['10', '20', '30']  — just the numbers

# With (?:...) — group used for structure but not captured
text = "colour color"
re.findall(r"colou?r", text)            # ['colour', 'color']
re.findall(r"col(?:ou|o)r", text)       # ['colour', 'color']  — (?:ou|o) groups alternation

# Alternation without capturing
re.findall(r"(?:cat|dog|bird)s?", "cats dogs birds fish")
# ['cats', 'dogs', 'birds']

# Grouping for quantifiers without capturing
re.findall(r"(?:ab)+", "ab ababab abababab")
# ['ab', 'ababab', 'abababab']  — repeated 'ab', but the group itself not captured
```

### `match.group()`, `.groups()`, `.groupdict()`, `.span()`

```python
import re

text = "Contact: john@example.com or jane@test.org"

pattern = r"(?P<user>[\w.]+)@(?P<domain>[\w.]+)"
m = re.search(pattern, text)

# group() methods
print(m.group())           # john@example.com  — full match (same as group(0))
print(m.group(0))          # john@example.com  — full match
print(m.group(1))          # john              — first capturing group
print(m.group(2))          # example.com       — second capturing group
print(m.group("user"))     # john              — by name
print(m.group("domain"))   # example.com       — by name

# Multiple groups in one call
print(m.group(1, 2))       # ('john', 'example.com')

# All groups
print(m.groups())          # ('john', 'example.com')
print(m.groupdict())       # {'user': 'john', 'domain': 'example.com'}

# Position information
print(m.start())           # 9   — start of full match
print(m.end())             # 25  — end of full match
print(m.span())            # (9, 25)  — (start, end)

print(m.start(1))          # 9   — start of group 1
print(m.span("user"))      # (9, 13)  — span of named group

# Iteration over all matches with full info
for m in re.finditer(pattern, text):
    print(f"Email: {m.group()} at position {m.span()}")
    print(f"  user={m.group('user')}, domain={m.group('domain')}")
```

---

## `re.sub()` and `re.subn()` — Substitution

### `re.sub()` — Replace Pattern with String or Callable

```python
import re

text = "Hello World 123"

# Basic substitution
result = re.sub(r"\d+", "NUM", text)
print(result)    # Hello World NUM

# Limit replacements with count=
result = re.sub(r"\d", "X", "abc123def456", count=3)
print(result)    # abcXXXdef456  — only first 3 digits replaced

# Using back-references in replacement — \1, \2, or \g<name>
text = "2024-11-15"
result = re.sub(r"(\d{4})-(\d{2})-(\d{2})", r"\3/\2/\1", text)
print(result)    # 15/11/2024  — rearranged date

# Named back-references
result = re.sub(
    r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})",
    r"\g<day>/\g<month>/\g<year>",
    text
)
print(result)    # 15/11/2024
```

### Callable Replacement — `re.sub()` with a Function

```python
import re

# The function receives a Match object and returns the replacement string
def double_number(m):
    return str(int(m.group()) * 2)

text = "I have 3 cats and 5 dogs"
result = re.sub(r"\d+", double_number, text)
print(result)    # I have 6 cats and 10 dogs

# Redact sensitive data
def redact(m):
    s = m.group()
    return s[:2] + "*" * (len(s) - 4) + s[-2:]

text = "Card: 4111111111111111, PIN: 1234"
result = re.sub(r"\d{4,}", redact, text)
print(result)    # Card: 41**********11, PIN: 1234

# Uppercase all matched words
text = "hello world foo bar"
result = re.sub(r"\b\w{4,}\b", lambda m: m.group().upper(), text)
print(result)    # hello WORLD foo BAR  — words 4+ chars uppercased

# HTML entity unescaping
import html
text = "&lt;b&gt;Hello&lt;/b&gt;"
result = re.sub(r"&\w+;", lambda m: html.unescape(m.group()), text)
print(result)    # <b>Hello</b>
```

### `re.subn()` — Sub with Count of Replacements Made

```python
import re

text = "cat bat hat rat"
result, count = re.subn(r"[cbhr]at", "???", text)
print(result)    # ??? ??? ??? ???
print(count)     # 4  — number of substitutions made

# Useful for knowing if any replacements happened
text = "no digits here"
result, count = re.subn(r"\d+", "NUM", text)
if count == 0:
    print("no numbers found")    # no numbers found
```

---

## `re.split()` — Split on Pattern

```python
import re

# Split on one or more whitespace
re.split(r"\s+", "hello   world  foo")    # ['hello', 'world', 'foo']

# Split on multiple delimiters
re.split(r"[,;|]+", "a,b;c|d,,e")    # ['a', 'b', 'c', 'd', 'e']

# Split on punctuation followed by space
re.split(r"[.,!?;]+\s*", "Hello, World! How are you?")
# ['Hello', 'World', 'How are you', '']

# maxsplit — limit number of splits
re.split(r"\s+", "a b c d e", maxsplit=2)    # ['a', 'b', 'c d e']

# Keeping delimiters — use capturing group
re.split(r"(\s+)", "hello   world")    # ['hello', '   ', 'world']  — delimiter included
re.split(r"([,;])", "a,b;c")          # ['a', ',', 'b', ';', 'c']
```

**`str.split()` vs `re.split()`:**

```python
# str.split() — simple, fast, for fixed delimiters
"a,b,c".split(",")          # ['a', 'b', 'c']
"a  b  c".split()           # ['a', 'b', 'c']  — any whitespace

# re.split() — for patterns, multiple delimiters, complex rules
import re
re.split(r"[,;|]+", "a,b;;c|d")    # ['a', 'b', 'c', 'd']
re.split(r"\s*,\s*", "a , b,c")    # ['a', 'b', 'c']  — whitespace-tolerant comma split
```

---

## Flags

```python
import re

text = "Hello WORLD\nSecond Line"

# re.IGNORECASE (re.I) — case-insensitive matching
re.findall(r"hello", text, re.IGNORECASE)    # ['Hello']
re.findall(r"[a-z]+", text, re.I)            # ['Hello', 'WORLD', 'Second', 'Line']

# re.MULTILINE (re.M) — ^ and $ match start/end of each LINE
re.findall(r"^\w+", text)               # ['Hello']  — only start of string
re.findall(r"^\w+", text, re.M)         # ['Hello', 'Second']  — each line's start
re.findall(r"\w+$", text, re.M)         # ['WORLD', 'Line']  — each line's end

# re.DOTALL (re.S) — . matches EVERYTHING including newline
re.findall(r"Hello.+Line", text)           # []  — . doesn't cross newline by default
re.findall(r"Hello.+Line", text, re.S)    # ['Hello WORLD\nSecond Line']

# Combining flags with |
re.findall(r"^\w+", text, re.I | re.M)    # ['Hello', 'Second']

# re.VERBOSE (re.X) — allows whitespace and comments in pattern
pattern = re.compile(r"""
    (\d{4})    # year
    -          # separator
    (\d{2})    # month
    -          # separator
    (\d{2})    # day
""", re.VERBOSE)

m = pattern.match("2024-11-15")
print(m.groups())    # ('2024', '11', '15')

# Inline flags — embed in pattern string
re.findall(r"(?i)hello", "Hello HELLO hello")    # ['Hello', 'HELLO', 'hello']
re.findall(r"(?im)^\w+", text)                   # IGNORECASE + MULTILINE
```

---

## `re.compile()` — Precompiling for Reuse

```python
import re

# Without compile — pattern re-parsed every call
for line in lines:
    re.findall(r"\d{4}-\d{2}-\d{2}", line)   # pattern compiled each time

# With compile — parse once, reuse many times
DATE_RE = re.compile(r"\d{4}-\d{2}-\d{2}")

for line in lines:
    DATE_RE.findall(line)    # uses cached compiled pattern — faster

# compile() returns a Pattern object with same methods
EMAIL_RE = re.compile(r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}")

EMAIL_RE.search("contact us at info@example.com today")
EMAIL_RE.findall("a@b.com and c@d.org")
EMAIL_RE.sub("[REDACTED]", "email me at user@example.com please")
EMAIL_RE.match("user@example.com")
EMAIL_RE.fullmatch("user@example.com")
EMAIL_RE.split("this@is.com weird")    # unusual but works
```

### Compiled Pattern with Flags

```python
import re

WORD_RE = re.compile(r"\b\w{4,}\b", re.IGNORECASE)
DATE_RE = re.compile(
    r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})",
    re.VERBOSE
)

LOG_RE = re.compile(r"""
    ^                               # start of line
    (?P<timestamp>\d{4}-\d{2}-\d{2}\ \d{2}:\d{2}:\d{2})  # timestamp
    \s+                             # whitespace
    \[(?P<level>DEBUG|INFO|WARNING|ERROR|CRITICAL)\]      # log level
    \s+                             # whitespace
    (?P<message>.+)                 # message
    $                               # end of line
""", re.MULTILINE | re.VERBOSE)
```

---

## Lookahead and Lookbehind

```python
import re

# Positive lookahead (?=...) — match if FOLLOWED BY pattern (not consumed)
re.findall(r"\d+(?= dollars)", "100 dollars and 50 euros")
# ['100']  — number followed by ' dollars'

# Negative lookahead (?!...) — match if NOT followed by pattern
re.findall(r"\d+(?! dollars)", "100 dollars and 50 euros")
# ['50']  — number NOT followed by ' dollars'

# Positive lookbehind (?<=...) — match if PRECEDED BY pattern
re.findall(r"(?<=\$)\d+", "Total: $99 and $150")
# ['99', '150']  — digits after dollar sign

# Negative lookbehind (?<!...) — match if NOT preceded by pattern
re.findall(r"(?<!\$)\d+", "price $99 code 42")
# ['42']  — digits NOT after dollar sign

# Lookaheads/lookbehinds are zero-width — they don't consume characters
text = "foobar"
re.sub(r"(?<=foo)bar", "BAZ", text)    # 'fooBaz' — foo is not consumed
```

---

## Common Real-World Patterns

```python
import re

# Email
EMAIL = re.compile(r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}")

# URL
URL = re.compile(
    r"https?://"                    # scheme
    r"(?:[\w-]+\.)*[\w-]+"          # domain
    r"(?:\.[a-zA-Z]{2,})+"          # TLD
    r"(?:/[^\s]*)?"                  # optional path
)

# IPv4
IPV4 = re.compile(
    r"(?:(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.){3}"
    r"(?:25[0-5]|2[0-4]\d|[01]?\d\d?)"
)

# ISO date
ISO_DATE = re.compile(r"\d{4}-(?:0[1-9]|1[0-2])-(?:0[1-9]|[12]\d|3[01])")

# Indian phone number
PHONE_IN = re.compile(r"(?:\+91|0)?[6-9]\d{9}")

# Log line
LOG_LINE = re.compile(
    r"(?P<timestamp>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})"
    r"\s+\[(?P<level>\w+)\]"
    r"\s+(?P<message>.+)"
)

# Validate
def is_valid_email(s):   return bool(EMAIL.fullmatch(s))
def is_valid_ip(s):      return bool(IPV4.fullmatch(s))
def is_valid_date(s):    return bool(ISO_DATE.fullmatch(s))

print(is_valid_email("user@example.com"))    # True
print(is_valid_email("not-an-email"))        # False
print(is_valid_ip("192.168.1.1"))            # True
print(is_valid_ip("999.0.0.1"))             # False
print(is_valid_date("2024-11-15"))           # True
print(is_valid_date("2024-13-01"))           # False
```

---

## Putting It All Together

```python
import re
from datetime import datetime

# Log file parser
LOG_PATTERN = re.compile(r"""
    ^
    (?P<timestamp>\d{4}-\d{2}-\d{2}\ \d{2}:\d{2}:\d{2})
    \s+
    \[(?P<level>DEBUG|INFO|WARNING|ERROR|CRITICAL)\]
    \s+
    (?P<source>[\w.]+)
    \s*:\s*
    (?P<message>.+?)
    (?:\s+\[(?P<request_id>[a-f0-9-]{36})\])?
    $
""", re.MULTILINE | re.VERBOSE)

EMAIL_RE    = re.compile(r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}")
IP_RE       = re.compile(r"\b(?:\d{1,3}\.){3}\d{1,3}\b")
DURATION_RE = re.compile(r"(\d+(?:\.\d+)?)\s*(ms|s|seconds?|milliseconds?)")

sample_log = """
2024-11-15 14:30:01 [INFO] app.server: Started on port 8000
2024-11-15 14:30:15 [INFO] app.auth: User login: admin@example.com from 192.168.1.42
2024-11-15 14:30:22 [WARNING] app.db: Query slow: took 1250 ms [a1b2c3d4-e5f6-7890-abcd-ef1234567890]
2024-11-15 14:31:05 [ERROR] app.api: Failed request from 10.0.0.1 for user@bad.com
2024-11-15 14:31:44 [INFO] app.server: Request handled in 45 ms
2024-11-15 14:32:00 [CRITICAL] app.db: Connection lost to 192.168.1.100
""".strip()

def parse_logs(log_text):
    results = {
        "entries":  [],
        "errors":   [],
        "emails":   set(),
        "ips":      set(),
        "warnings": [],
        "slow_queries": [],
    }

    for match in LOG_PATTERN.finditer(log_text):
        entry = match.groupdict()
        entry["datetime"] = datetime.strptime(
            entry["timestamp"], "%Y-%m-%d %H:%M:%S"
        )

        results["entries"].append(entry)

        # Extract emails from message
        results["emails"].update(EMAIL_RE.findall(entry["message"]))

        # Extract IPs from message
        results["ips"].update(IP_RE.findall(entry["message"]))

        # Collect errors and criticals
        if entry["level"] in ("ERROR", "CRITICAL"):
            results["errors"].append(entry)

        # Detect warnings
        if entry["level"] == "WARNING":
            results["warnings"].append(entry)

        # Detect slow queries
        dur_match = DURATION_RE.search(entry["message"])
        if dur_match:
            value = float(dur_match.group(1))
            unit  = dur_match.group(2)
            ms    = value if "ms" in unit else value * 1000
            if ms > 1000:
                results["slow_queries"].append({
                    "message":     entry["message"],
                    "duration_ms": ms,
                    "timestamp":   entry["timestamp"],
                })

    return results

result = parse_logs(sample_log)

print(f"Total entries: {len(result['entries'])}")
print(f"Errors:        {len(result['errors'])}")
print(f"Emails found:  {result['emails']}")
print(f"IPs found:     {result['ips']}")
print(f"Slow queries:  {result['slow_queries']}")

# Redact sensitive data
def redact_log(log_text):
    log_text = EMAIL_RE.sub("[EMAIL]", log_text)
    log_text = IP_RE.sub("[IP]", log_text)
    return log_text

print("\nRedacted log:")
print(redact_log(sample_log))
```

---

## Key Takeaways

- Always use `r"..."` for regex patterns — no exceptions. Avoids backslash confusion.
- `re.match()` — anchored to start. `re.search()` — anywhere in string. `re.fullmatch()` — entire string must match. Use `fullmatch()` for validation, `search()` for extraction.
- `findall()` returns strings (or tuples of groups). `finditer()` returns Match objects with `.group()`, `.span()`, `.groupdict()`.
- Greedy `*` `+` `?` take as much as possible. Lazy `*?` `+?` `??` take as little as possible. Use lazy when matching HTML tags or any "first occurrence" pattern.
- `(...)` captures. `(?:...)` groups without capturing. `(?P<name>...)` captures by name. Use non-capturing when you only need grouping for alternation or quantifiers.
- `re.sub(pattern, repl, text)` — `repl` can be a string with `\1` back-references or a callable that receives the Match object and returns a string.
- `re.IGNORECASE` — case insensitive. `re.MULTILINE` — `^`/`$` match line starts/ends. `re.DOTALL` — `.` matches newline too. `re.VERBOSE` — readable multi-line patterns with comments.
- `re.compile()` — precompile patterns used multiple times. Saves re-parsing on each call.
- Lookahead `(?=...)` and lookbehind `(?<=...)` are zero-width — they assert position without consuming characters.
- For HTML — use `BeautifulSoup`. For JSON/CSV — use `json`/`csv`. Regex is for text patterns, not structured formats.