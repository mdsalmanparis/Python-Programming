Let's go. Full topic, nothing skipped.

---

## 2.2 `pathlib`, `os`, `shutil`

---

## Why `pathlib` Replaced `os.path`

Before `pathlib`, file paths were just strings. You had to use `os.path.join()`, string concatenation, and scattered `os.path.*` calls everywhere.

```python
# Old way — string-based, verbose, error-prone
import os
path = os.path.join("home", "user", "documents", "report.pdf")
parent = os.path.dirname(path)
name = os.path.basename(path)
exists = os.path.exists(path)

# pathlib — object-oriented, readable, all operations in one place
from pathlib import Path
path = Path("home") / "user" / "documents" / "report.pdf"
parent = path.parent
name = path.name
exists = path.exists()
```

`pathlib` is the modern standard. `os.path` is not deprecated but you should default to `pathlib` for new code.

---

## `pathlib.Path` — Construction

```python
from pathlib import Path

# From a string
p = Path("documents/report.pdf")
p = Path("/home/user/documents/report.pdf")   # absolute

# From multiple parts
p = Path("home", "user", "documents")         # joined automatically

# Current directory
p = Path(".")
p = Path.cwd()    # absolute path of current working directory

# Home directory
p = Path.home()   # /home/user on Linux, C:\Users\user on Windows

# Platform-specific subclasses — automatically chosen
# PosixPath on Linux/Mac, WindowsPath on Windows
print(type(Path(".")))    # <class 'posixpath.PosixPath'> or WindowsPath
```

### `/` Operator — Joining Paths

The `/` operator joins path components — cleaner than `os.path.join()`.

```python
from pathlib import Path

base    = Path("/home/user")
docs    = base / "documents"
report  = docs / "report.pdf"

print(report)    # /home/user/documents/report.pdf

# Chain multiple
full = Path("/home") / "user" / "projects" / "myapp" / "src" / "main.py"
print(full)    # /home/user/projects/myapp/src/main.py

# Absolute path on right side resets the left
p = Path("/home/user") / "/etc/config"
print(p)    # /etc/config  — absolute on right discards left

# String on either side works
p = Path("/home") / "user" / Path("docs")
```

### Path Attributes

```python
p = Path("/home/user/documents/report.pdf")

print(p.name)        # report.pdf          — filename with extension
print(p.stem)        # report              — filename without extension
print(p.suffix)      # .pdf                — extension including the dot
print(p.suffixes)    # ['.pdf']            — list of all suffixes
print(p.parent)      # /home/user/documents  — parent directory
print(p.parents)     # iterable of all parents
print(p.parts)       # ('/', 'home', 'user', 'documents', 'report.pdf')
print(p.root)        # /                   — root component
print(p.anchor)      # /                   — drive + root

# Multiple extensions
p2 = Path("archive.tar.gz")
print(p2.suffix)     # .gz
print(p2.suffixes)   # ['.tar', '.gz']
print(p2.stem)       # archive.tar

# parent chain
p = Path("/a/b/c/d.txt")
print(p.parent)           # /a/b/c
print(p.parent.parent)    # /a/b
print(list(p.parents))    # [PosixPath('/a/b/c'), PosixPath('/a/b'), PosixPath('/a'), PosixPath('/')]
```

### Converting and Resolving

```python
from pathlib import Path

p = Path("../documents/report.pdf")

# Convert to string
s = str(p)             # '../documents/report.pdf'

# Absolute path — resolves .., symlinks
abs_p = p.resolve()    # /home/user/documents/report.pdf  (absolute, no ..)

# Is it absolute?
print(p.is_absolute())    # False
print(abs_p.is_absolute()) # True

# with_name — replace filename
p = Path("/home/user/report.pdf")
print(p.with_name("summary.pdf"))    # /home/user/summary.pdf

# with_stem — replace stem only (Python 3.9+)
print(p.with_stem("final_report"))   # /home/user/final_report.pdf

# with_suffix — replace extension
print(p.with_suffix(".txt"))         # /home/user/report.txt
print(p.with_suffix(""))             # /home/user/report  — remove extension
```

---

## `exists()`, `is_file()`, `is_dir()`

```python
from pathlib import Path

p = Path("myfile.txt")

# Existence checks
p.exists()          # True if path exists (file OR directory)
p.is_file()         # True if exists AND is a regular file
p.is_dir()          # True if exists AND is a directory
p.is_symlink()      # True if it's a symbolic link
p.is_absolute()     # True if path is absolute

# Practical usage
config = Path("config.json")
if not config.exists():
    print("config file missing")
    config.write_text("{}")   # create default

data_dir = Path("data")
if not data_dir.is_dir():
    data_dir.mkdir(parents=True)

# Checking before open — but try/except is more Pythonic
try:
    content = Path("data.txt").read_text()
except FileNotFoundError:
    content = ""
```

---

## Reading and Writing — `read_text()`, `write_text()`, `read_bytes()`, `write_bytes()`

These are convenience methods — no need to open/close manually.

### Text Files

```python
from pathlib import Path

p = Path("notes.txt")

# Write — creates if not exists, overwrites if exists
p.write_text("Hello, World!\nLine 2\n", encoding="utf-8")

# Read — returns entire file as a string
content = p.read_text(encoding="utf-8")
print(content)

# Append — read_text/write_text don't support append directly
existing = p.read_text(encoding="utf-8")
p.write_text(existing + "new line\n", encoding="utf-8")

# For append without reading full file first:
with p.open("a", encoding="utf-8") as f:
    f.write("appended line\n")
```

### Binary Files

```python
from pathlib import Path

p = Path("image.png")

# Read bytes
data = p.read_bytes()
print(type(data))    # <class 'bytes'>
print(data[:4])      # b'\x89PNG'  — PNG header

# Write bytes
p.write_bytes(data)   # copies the file content

# Copy a binary file
source = Path("original.png")
dest   = Path("copy.png")
dest.write_bytes(source.read_bytes())
```

### Using `open()` for More Control

```python
from pathlib import Path

p = Path("data.txt")

# Path.open() works exactly like built-in open()
with p.open("r", encoding="utf-8") as f:
    for line in f:            # lazy line iteration
        print(line.strip())

with p.open("w", encoding="utf-8") as f:
    f.write("line 1\n")
    f.write("line 2\n")

# Difference: p.open() vs open(p) — identical behaviour, both work
with open(p, "r", encoding="utf-8") as f:     # also valid
    content = f.read()
```

---

## `glob()` and `rglob()` — File Discovery

```python
from pathlib import Path

base = Path("project")

# glob — match files in current directory only
list(base.glob("*.py"))            # all .py files in project/
list(base.glob("*.txt"))           # all .txt files in project/
list(base.glob("data_*.csv"))      # files matching pattern

# glob with subdirectory
list(base.glob("src/*.py"))        # .py files in project/src/ only

# ** — any level of subdirectories
list(base.glob("**/*.py"))         # ALL .py files in project/ and ALL subdirs
                                   # same as rglob("*.py")

# rglob — recursive glob (adds ** prefix automatically)
list(base.rglob("*.py"))           # ALL .py files recursively
list(base.rglob("*.log"))          # ALL .log files recursively
list(base.rglob("config.*"))       # any file named "config.*" recursively
```

**`glob()` returns a generator — iterate it or wrap in `list()`:**

```python
from pathlib import Path

src = Path("src")

# Count Python files
py_files = list(src.rglob("*.py"))
print(f"Found {len(py_files)} Python files")

# Process each file
for py_file in src.rglob("*.py"):
    print(py_file)                  # full path
    print(py_file.name)             # filename only
    print(py_file.relative_to(src)) # path relative to src

# Filter with condition
large_files = [
    f for f in src.rglob("*")
    if f.is_file() and f.stat().st_size > 1024 * 1024   # > 1MB
]

# Find all test files
test_files = list(Path(".").rglob("test_*.py"))

# Find all non-Python files
non_py = [f for f in Path("src").rglob("*") if f.is_file() and f.suffix != ".py"]
```

---

## `iterdir()` — Listing Directory Contents

```python
from pathlib import Path

p = Path(".")

# iterdir — yields Path objects for everything in directory (non-recursive)
for item in p.iterdir():
    print(item)

# Separate files from directories
files = [x for x in p.iterdir() if x.is_file()]
dirs  = [x for x in p.iterdir() if x.is_dir()]

# Sorted listing
sorted_items = sorted(p.iterdir(), key=lambda x: x.name)

# Filter by extension
py_files = [x for x in p.iterdir() if x.suffix == ".py"]

# iterdir vs glob — when to use which
list(p.iterdir())          # everything in directory (one level, no pattern)
list(p.glob("*.py"))       # pattern match in directory (one level)
list(p.rglob("*.py"))      # pattern match recursively
```

---

## `mkdir()` — Creating Directories

```python
from pathlib import Path

# Create a single directory
Path("output").mkdir()

# parents=True — create all intermediate directories (like mkdir -p)
Path("output/reports/monthly").mkdir(parents=True)

# exist_ok=True — don't raise error if directory already exists
Path("output").mkdir(exist_ok=True)

# Most common usage — both flags
Path("output/data/raw").mkdir(parents=True, exist_ok=True)

# Without flags — errors
Path("output").mkdir()            # FileExistsError if output already exists
Path("a/b/c").mkdir()             # FileNotFoundError if a/b doesn't exist

# Creating a temp directory structure
def setup_project(name: str) -> Path:
    base = Path(name)
    for subdir in ["src", "tests", "data", "output"]:
        (base / subdir).mkdir(parents=True, exist_ok=True)
    return base
```

---

## `rename()`, `replace()`, `unlink()` — Moving and Deleting

```python
from pathlib import Path

# unlink() — delete a file
p = Path("temp.txt")
p.write_text("temporary")
p.unlink()                     # deletes the file
# p.unlink(missing_ok=True)   # Python 3.8+: no error if already gone

# rename() — rename/move (fails if destination exists on some platforms)
Path("old_name.txt").rename("new_name.txt")
Path("file.txt").rename(Path("subdir") / "file.txt")  # move to subdirectory

# replace() — rename/move, overwrites destination if it exists
Path("file.txt").replace("existing_file.txt")   # overwrites safely

# rmdir() — remove empty directory only
Path("empty_dir").rmdir()    # OSError if not empty

# For non-empty directories — use shutil.rmtree() (covered below)
```

---

## `stat()` — File Metadata

```python
from pathlib import Path
import datetime

p = Path("report.pdf")
stat = p.stat()

print(stat.st_size)     # size in bytes
print(stat.st_mtime)    # last modified time (Unix timestamp)
print(stat.st_ctime)    # creation/change time

# Convert to readable datetime
modified = datetime.datetime.fromtimestamp(stat.st_mtime)
print(f"Last modified: {modified:%Y-%m-%d %H:%M:%S}")

# Human-readable size
def human_size(path: Path) -> str:
    size = path.stat().st_size
    for unit in ["B", "KB", "MB", "GB"]:
        if size < 1024:
            return f"{size:.1f} {unit}"
        size /= 1024
    return f"{size:.1f} TB"

print(human_size(Path("data.csv")))
```

---

## `os.environ` and `os.getenv()` — Environment Variables

```python
import os

# os.environ — dict-like object, all current env variables
print(os.environ)                   # all env vars
print(os.environ["HOME"])           # /home/user — KeyError if missing
print(os.environ["PATH"])           # all PATH entries

# os.getenv() — safe access with default
print(os.getenv("HOME"))            # /home/user
print(os.getenv("MISSING_VAR"))     # None — no error
print(os.getenv("MISSING_VAR", "default_value"))  # default_value

# Setting env vars — affects current process and child processes
os.environ["MY_VAR"] = "hello"
print(os.getenv("MY_VAR"))    # hello

# Deleting an env var
del os.environ["MY_VAR"]

# Common pattern — app configuration from environment
DATABASE_URL = os.getenv("DATABASE_URL", "sqlite:///local.db")
DEBUG        = os.getenv("DEBUG", "false").lower() == "true"
PORT         = int(os.getenv("PORT", "8000"))
SECRET_KEY   = os.environ["SECRET_KEY"]   # required — KeyError if missing

# Better with python-dotenv (third-party)
from dotenv import load_dotenv
load_dotenv()   # loads .env file into os.environ
DATABASE_URL = os.getenv("DATABASE_URL")
```

---

## `os.getcwd()` and `os.chdir()`

```python
import os

# Current working directory
print(os.getcwd())    # /home/user/projects/myapp

# Change directory
os.chdir("/tmp")
print(os.getcwd())    # /tmp

os.chdir("/home/user/projects")
print(os.getcwd())    # /home/user/projects

# pathlib equivalent
from pathlib import Path
print(Path.cwd())     # same as os.getcwd() but returns a Path object

# Safe chdir — restore original directory
import os
from contextlib import contextmanager

@contextmanager
def working_directory(path):
    original = os.getcwd()
    os.chdir(path)
    try:
        yield
    finally:
        os.chdir(original)    # always restore

with working_directory("/tmp"):
    print(os.getcwd())    # /tmp
    # do work here

print(os.getcwd())    # /home/user/projects — restored
```

---

## `os.walk()` — Recursive Directory Traversal

`os.walk()` traverses a directory tree, yielding `(dirpath, dirnames, filenames)` for each directory.

```python
import os

for dirpath, dirnames, filenames in os.walk("project"):
    print(f"Directory: {dirpath}")
    print(f"  Subdirs: {dirnames}")
    print(f"  Files:   {filenames}")

# project/
#   src/
#     main.py
#     utils.py
#   tests/
#     test_main.py
#   README.md

# Output:
# Directory: project
#   Subdirs: ['src', 'tests']
#   Files:   ['README.md']
# Directory: project/src
#   Subdirs: []
#   Files:   ['main.py', 'utils.py']
# Directory: project/tests
#   Subdirs: []
#   Files:   ['test_main.py']
```

**Building full paths:**

```python
import os
from pathlib import Path

# All Python files with full paths
for dirpath, dirnames, filenames in os.walk("project"):
    for filename in filenames:
        if filename.endswith(".py"):
            full_path = os.path.join(dirpath, filename)
            print(full_path)

# Using pathlib for the full path
for dirpath, dirnames, filenames in os.walk("project"):
    dir_path = Path(dirpath)
    for filename in filenames:
        full_path = dir_path / filename
        print(full_path)
```

**Skipping directories — modify `dirnames` in-place:**

```python
import os

SKIP_DIRS = {".git", "__pycache__", ".venv", "node_modules"}

for dirpath, dirnames, filenames in os.walk("project"):
    # Modify dirnames IN-PLACE to prevent os.walk from descending into them
    dirnames[:] = [d for d in dirnames if d not in SKIP_DIRS]

    for filename in filenames:
        print(os.path.join(dirpath, filename))
```

**`os.walk` vs `Path.rglob()`:**

```python
# os.walk — more control, can skip directories, get dirnames list
# Path.rglob("*") — simpler, but no easy way to skip entire subtrees

# os.walk is better when you need to control which directories to enter
# rglob is better for simple "find all files matching pattern"
```

---

## `shutil` — High-Level File Operations

`shutil` handles file and directory operations that `pathlib` doesn't (copying trees, removing trees, moving).

```python
import shutil
```

### `shutil.copy()` and `shutil.copy2()`

```python
import shutil

# copy(src, dst) — copies file content + permissions
shutil.copy("source.txt", "destination.txt")
shutil.copy("source.txt", "output/")    # copy into directory

# copy2(src, dst) — copies content + permissions + metadata (timestamps)
shutil.copy2("source.txt", "destination.txt")    # preserves modification time

# copyfile(src, dst) — copies ONLY content, not permissions
shutil.copyfile("source.txt", "destination.txt")
```

### `shutil.copytree()` — Copy Entire Directory Tree

```python
import shutil

# Copy entire directory tree
shutil.copytree("source_dir", "destination_dir")
# destination_dir must NOT exist — it's created

# Python 3.8+ — dirs_exist_ok=True allows copying into existing dir
shutil.copytree("source_dir", "existing_dir", dirs_exist_ok=True)

# Ignore certain files/patterns
shutil.copytree(
    "project",
    "project_backup",
    ignore=shutil.ignore_patterns("*.pyc", "__pycache__", ".git", "*.tmp")
)

# Custom copy function (copy2 preserves metadata)
shutil.copytree("src", "dst", copy_function=shutil.copy2)
```

### `shutil.rmtree()` — Remove Entire Directory Tree

```python
import shutil

# Remove directory and ALL its contents — permanent, no recycle bin
shutil.rmtree("output_dir")

# ignore_errors — suppress all errors (use carefully)
shutil.rmtree("maybe_exists", ignore_errors=True)

# onerror — custom error handler
def handle_error(func, path, exc_info):
    print(f"Error deleting {path}: {exc_info[1]}")

shutil.rmtree("output_dir", onerror=handle_error)

# Safe pattern — check before removing
import shutil
from pathlib import Path

output = Path("output")
if output.exists():
    shutil.rmtree(output)
output.mkdir()
```

### `shutil.move()` — Move Files and Directories

```python
import shutil

# Move a file
shutil.move("file.txt", "subdir/file.txt")
shutil.move("file.txt", "subdir/")     # move into directory, keep name

# Move a directory
shutil.move("old_dir", "new_dir")      # rename if on same filesystem
                                        # copies + deletes if cross-filesystem

# Move multiple files
import shutil
from pathlib import Path

output = Path("processed")
output.mkdir(exist_ok=True)

for f in Path("raw").glob("*.csv"):
    shutil.move(str(f), output / f.name)
```

### `shutil.disk_usage()` — Disk Space

```python
import shutil

usage = shutil.disk_usage("/")
print(f"Total:  {usage.total / 1e9:.1f} GB")
print(f"Used:   {usage.used  / 1e9:.1f} GB")
print(f"Free:   {usage.free  / 1e9:.1f} GB")
print(f"Used %: {usage.used / usage.total * 100:.1f}%")
```

### `shutil.which()` — Find Executable

```python
import shutil

print(shutil.which("python"))    # /usr/bin/python3
print(shutil.which("git"))       # /usr/bin/git
print(shutil.which("missing"))   # None — not found in PATH
```

---

## `tempfile` — Temporary Files and Directories

```python
import tempfile
from pathlib import Path

# TemporaryDirectory — auto-deleted when context manager exits
with tempfile.TemporaryDirectory() as tmpdir:
    tmp = Path(tmpdir)
    print(tmp)             # /tmp/tmpXXXXXX  — unique name
    print(tmp.exists())    # True

    # Create files inside
    (tmp / "data.csv").write_text("name,score\nMohamed,88")
    (tmp / "output").mkdir()

    for f in tmp.iterdir():
        print(f)

# After the with block
print(tmp.exists())    # False — entire directory deleted automatically

# NamedTemporaryFile — for single temporary files
with tempfile.NamedTemporaryFile(suffix=".txt", delete=False) as f:
    f.write(b"temporary content")
    tmp_path = Path(f.name)

# delete=False means file persists after close
print(tmp_path.exists())    # True
tmp_path.unlink()           # manually clean up

# TemporaryFile — truly temporary, no name on filesystem (on most platforms)
with tempfile.TemporaryFile() as f:
    f.write(b"data")
    f.seek(0)
    print(f.read())     # b'data'
# deleted on exit

# Specify directory for temp files
with tempfile.TemporaryDirectory(dir="/custom/tmp", prefix="myapp_") as tmpdir:
    print(tmpdir)    # /custom/tmp/myapp_XXXXXX
```

---

## Putting It All Together

```python
import os
import shutil
import tempfile
from pathlib import Path
from datetime import datetime

def process_data_files(
    source_dir: str,
    output_dir: str,
    pattern: str = "*.csv",
    backup: bool = True,
) -> dict:
    """
    Process all CSV files in source_dir:
    1. Back up the source directory
    2. Process each matching file
    3. Write results to output_dir
    4. Return a summary
    """
    source = Path(source_dir)
    output = Path(output_dir)

    # Validate source
    if not source.is_dir():
        raise FileNotFoundError(f"source directory not found: {source}")

    # Setup output directory
    output.mkdir(parents=True, exist_ok=True)

    summary = {
        "processed": [],
        "skipped":   [],
        "errors":    [],
        "backup":    None,
    }

    # Step 1 — backup
    if backup:
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        backup_path = source.parent / f"{source.name}_backup_{timestamp}"
        shutil.copytree(
            source,
            backup_path,
            ignore=shutil.ignore_patterns("*.tmp", "__pycache__")
        )
        summary["backup"] = str(backup_path)
        print(f"backed up to: {backup_path}")

    # Step 2 — process files
    for file_path in sorted(source.rglob(pattern)):
        if not file_path.is_file():
            continue

        rel_path = file_path.relative_to(source)
        out_path = output / rel_path.with_suffix(".processed.csv")

        # Create output subdirectory if needed
        out_path.parent.mkdir(parents=True, exist_ok=True)

        try:
            content = file_path.read_text(encoding="utf-8")
            lines   = content.strip().splitlines()

            if len(lines) < 2:    # header + at least one row
                summary["skipped"].append(str(rel_path))
                continue

            # Process — uppercase headers, strip whitespace
            header = lines[0].upper()
            rows   = [line.strip() for line in lines[1:] if line.strip()]
            processed = "\n".join([header] + rows)

            out_path.write_text(processed + "\n", encoding="utf-8")
            summary["processed"].append(str(rel_path))

        except Exception as e:
            summary["errors"].append({"file": str(rel_path), "error": str(e)})

    # Step 3 — write summary file
    summary_path = output / "_summary.txt"
    with summary_path.open("w", encoding="utf-8") as f:
        f.write(f"Run at: {datetime.now():%Y-%m-%d %H:%M:%S}\n")
        f.write(f"Source: {source.resolve()}\n")
        f.write(f"Output: {output.resolve()}\n\n")
        f.write(f"Processed: {len(summary['processed'])}\n")
        for p in summary["processed"]:
            f.write(f"  ✓ {p}\n")
        f.write(f"\nSkipped: {len(summary['skipped'])}\n")
        for s in summary["skipped"]:
            f.write(f"  - {s}\n")
        if summary["errors"]:
            f.write(f"\nErrors: {len(summary['errors'])}\n")
            for e in summary["errors"]:
                f.write(f"  ✗ {e['file']}: {e['error']}\n")

    return summary


# Usage with tempfile for testing
with tempfile.TemporaryDirectory() as tmpdir:
    tmp = Path(tmpdir)

    # Create test structure
    (tmp / "raw" / "region1").mkdir(parents=True)
    (tmp / "raw" / "region2").mkdir(parents=True)

    (tmp / "raw" / "region1" / "sales.csv").write_text(
        "name,score,city\nMohamed,88,Chennai\nPriya,92,Mumbai\n"
    )
    (tmp / "raw" / "region2" / "sales.csv").write_text(
        "name,score,city\nArjun,74,Delhi\n"
    )
    (tmp / "raw" / "empty.csv").write_text("header_only\n")

    result = process_data_files(
        source_dir=str(tmp / "raw"),
        output_dir=str(tmp / "output"),
        backup=True,
    )

    print("\nSummary:")
    print(f"  Processed: {result['processed']}")
    print(f"  Skipped:   {result['skipped']}")
    print(f"  Errors:    {result['errors']}")
    print(f"  Backup:    {result['backup']}")

    # Show output structure
    print("\nOutput files:")
    for f in sorted((tmp / "output").rglob("*")):
        if f.is_file():
            print(f"  {f.relative_to(tmp / 'output')}")
```

---

## Key Takeaways

**`pathlib.Path`**
- `/` operator joins paths — cleaner than `os.path.join()`.
- `.name` = filename with extension. `.stem` = without extension. `.suffix` = extension. `.parent` = directory.
- `.resolve()` — absolute path with `..` and symlinks resolved.
- `.with_name()`, `.with_stem()`, `.with_suffix()` — create modified path without changing original.
- `read_text()` / `write_text()` — always pass `encoding="utf-8"`. For line-by-line use `.open()`.

**Discovery**
- `.glob("*.py")` — matches in current directory only. `.rglob("*.py")` = `glob("**/*.py")` = recursive.
- `.iterdir()` — all contents, one level, no pattern.
- Both return generators — wrap in `list()` or iterate directly.

**`os`**
- `os.getenv("KEY", "default")` — always use this over `os.environ["KEY"]` unless the key is required.
- `os.walk()` — yields `(dirpath, dirnames, filenames)`. Modify `dirnames[:]` in-place to skip subtrees.
- `os.getcwd()` — prefer `Path.cwd()` for pathlib workflows.

**`shutil`**
- `copy()` — content + permissions. `copy2()` — content + permissions + timestamps. `copyfile()` — content only.
- `copytree()` — destination must not exist (unless `dirs_exist_ok=True` in Python 3.8+). Use `ignore=shutil.ignore_patterns(...)`.
- `rmtree()` — permanent deletion, no undo. Always check before calling.
- `move()` — rename on same filesystem, copy+delete across filesystems.

**`tempfile`**
- `TemporaryDirectory()` — auto-cleaned on context manager exit. Use for test isolation.
- `NamedTemporaryFile(delete=False)` — persists after close, you manage deletion.