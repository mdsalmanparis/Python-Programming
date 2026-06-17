## 2.3 `json`, `csv`, `python-dotenv`

---

## Why These Three Together

Every backend service deals with these constantly:
- **JSON** — API requests and responses, config files, data exchange
- **CSV** — data imports/exports, reports, spreadsheet interchange
- **dotenv** — environment configuration for local dev, secrets management

---

## `json` — The Full Picture

### `json.loads()` and `json.dumps()` — String-Based

```python
import json

# loads() — JSON string → Python object
json_str = '{"name": "Mohamed", "score": 88, "active": true, "tags": ["python", "backend"]}'
data = json.loads(json_str)

print(data)            # {'name': 'Mohamed', 'score': 88, 'active': True, 'tags': ['python', 'backend']}
print(type(data))      # <class 'dict'>
print(data["name"])    # Mohamed
print(data["active"])  # True  — JSON true → Python True

# dumps() — Python object → JSON string
data = {"name": "Mohamed", "score": 88, "active": True}
json_str = json.dumps(data)
print(json_str)    # {"name": "Mohamed", "score": 88, "active": true}
```

### JSON ↔ Python Type Mapping

```python
# JSON          Python
# object        dict
# array         list
# string        str
# number (int)  int
# number (real) float
# true          True
# false         False
# null          None

json.loads('{"a": 1, "b": [1,2,3], "c": null, "d": true, "e": 3.14}')
# {'a': 1, 'b': [1, 2, 3], 'c': None, 'd': True, 'e': 3.14}
```

### `indent` — Pretty Printing

```python
import json

data = {
    "name": "Mohamed",
    "scores": [88, 92, 75],
    "address": {"city": "Chennai", "state": "Tamil Nadu"}
}

# Compact — default
print(json.dumps(data))
# {"name": "Mohamed", "scores": [88, 92, 75], "address": {"city": "Chennai", "state": "Tamil Nadu"}}

# Pretty — with indent
print(json.dumps(data, indent=2))
# {
#   "name": "Mohamed",
#   "scores": [
#     88,
#     92,
#     75
#   ],
#   "address": {
#     "city": "Chennai",
#     "state": "Tamil Nadu"
#   }
# }

print(json.dumps(data, indent=4))    # 4 spaces — common choice
```

### `sort_keys` — Deterministic Output

```python
import json

data = {"z": 3, "a": 1, "m": 2}

print(json.dumps(data))
# {"z": 3, "a": 1, "m": 2}  — insertion order

print(json.dumps(data, sort_keys=True))
# {"a": 1, "m": 2, "z": 3}  — alphabetical

# Why sort_keys matters:
# - Reproducible output for hashing or comparison
# - Consistent git diffs for config files
# - Predictable test assertions
```

### `ensure_ascii=False` — Unicode Support

```python
import json

data = {"name": "முகமது", "city": "சென்னை"}   # Tamil characters

# Default — all non-ASCII escaped as \uXXXX
print(json.dumps(data))
# {"name": "\u0bae\u0bc1\u0b95\u0bae\u0ba4\u0bc1", "city": "\u0b9a\u0bc6\u0ba9\u0bcd\u0ba9\u0bc8"}

# ensure_ascii=False — keeps Unicode as-is
print(json.dumps(data, ensure_ascii=False))
# {"name": "முகமது", "city": "சென்னை"}

# When writing to file — always use ensure_ascii=False + utf-8 encoding
with open("output.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)
```

### `default` — Serializing Custom Types

Python's `json` module can only serialize: `dict`, `list`, `tuple`, `str`, `int`, `float`, `bool`, `None`.

Everything else raises `TypeError`.

```python
import json
from datetime import datetime, date
from decimal import Decimal

data = {
    "name": "Mohamed",
    "created_at": datetime.now(),   # TypeError: not serializable
}

json.dumps(data)    # TypeError: Object of type datetime is not JSON serializable
```

**Fix 1 — `default` parameter:**

```python
import json
from datetime import datetime, date
from decimal import Decimal
from uuid import UUID
from pathlib import Path

def json_default(obj):
    """Convert non-serializable objects to JSON-compatible types."""
    if isinstance(obj, (datetime, date)):
        return obj.isoformat()
    if isinstance(obj, Decimal):
        return str(obj)           # or float(obj) — str avoids precision loss
    if isinstance(obj, UUID):
        return str(obj)
    if isinstance(obj, Path):
        return str(obj)
    if isinstance(obj, set):
        return list(obj)
    if isinstance(obj, bytes):
        return obj.decode("utf-8")
    raise TypeError(f"Object of type {type(obj).__name__} is not JSON serializable")

data = {
    "name": "Mohamed",
    "created_at": datetime.now(),
    "price": Decimal("99.99"),
    "tags": {"python", "backend"},
}

print(json.dumps(data, default=json_default, indent=2, ensure_ascii=False))
# {
#   "name": "Mohamed",
#   "created_at": "2024-11-15T14:30:45.123456",
#   "price": "99.99",
#   "tags": ["python", "backend"]
# }
```

---

## Custom JSON Encoder — `json.JSONEncoder` Subclass

For reusable, composable serialization logic.

```python
import json
from datetime import datetime, date
from decimal import Decimal
from uuid import UUID
from pathlib import Path
from enum import Enum

class AppJSONEncoder(json.JSONEncoder):
    """Custom encoder for app-wide JSON serialization."""

    def default(self, obj):
        # default() is called for objects the base encoder can't handle
        if isinstance(obj, datetime):
            return {"__type__": "datetime", "value": obj.isoformat()}
        if isinstance(obj, date):
            return obj.isoformat()
        if isinstance(obj, Decimal):
            return str(obj)
        if isinstance(obj, UUID):
            return str(obj)
        if isinstance(obj, Path):
            return str(obj)
        if isinstance(obj, set | frozenset):
            return sorted(list(obj))    # sorted for deterministic output
        if isinstance(obj, Enum):
            return obj.value
        if hasattr(obj, "to_dict"):
            return obj.to_dict()        # support any class with to_dict()
        if hasattr(obj, "__dict__"):
            return obj.__dict__         # fallback — serialize instance attributes
        return super().default(obj)     # raises TypeError for truly unknown types

# Usage
from uuid import uuid4
from enum import Enum

class Status(Enum):
    ACTIVE   = "active"
    INACTIVE = "inactive"

class User:
    def __init__(self, name, score):
        self.name  = name
        self.score = score

data = {
    "id":         uuid4(),
    "created_at": datetime.now(),
    "price":      Decimal("49.99"),
    "status":     Status.ACTIVE,
    "user":       User("Mohamed", 88),
    "tags":       {"python", "backend"},
}

result = json.dumps(data, cls=AppJSONEncoder, indent=2, ensure_ascii=False)
print(result)
```

### Custom Decoder — `object_hook`

```python
import json
from datetime import datetime

def json_object_hook(d):
    """Convert special dicts back to Python objects during decode."""
    if "__type__" in d and d["__type__"] == "datetime":
        return datetime.fromisoformat(d["value"])
    return d

# Round-trip: encode then decode
data = {"created_at": datetime(2024, 11, 15, 14, 30)}
encoded = json.dumps(data, cls=AppJSONEncoder)
decoded = json.loads(encoded, object_hook=json_object_hook)

print(decoded["created_at"])          # 2024-11-15 14:30:00
print(type(decoded["created_at"]))    # <class 'datetime.datetime'>
```

---

## `json.load()` and `json.dump()` — File-Based

```python
import json
from pathlib import Path

config_path = Path("config.json")

# dump() — Python → JSON file
config = {
    "database": {"host": "localhost", "port": 5432, "name": "mydb"},
    "debug": False,
    "allowed_hosts": ["localhost", "127.0.0.1"],
}

with open(config_path, "w", encoding="utf-8") as f:
    json.dump(config, f, indent=2, ensure_ascii=False)

# load() — JSON file → Python
with open(config_path, "r", encoding="utf-8") as f:
    loaded_config = json.load(f)

print(loaded_config["database"]["host"])    # localhost

# pathlib shortcut — for small files
config = json.loads(config_path.read_text(encoding="utf-8"))
config_path.write_text(json.dumps(config, indent=2), encoding="utf-8")
```

**Common pattern — config file with defaults:**

```python
import json
from pathlib import Path

DEFAULT_CONFIG = {
    "debug": False,
    "port": 8000,
    "log_level": "INFO",
    "database": {"host": "localhost", "port": 5432},
}

def load_config(path: str = "config.json") -> dict:
    config_path = Path(path)
    if not config_path.exists():
        return DEFAULT_CONFIG.copy()

    with open(config_path, encoding="utf-8") as f:
        user_config = json.load(f)

    # Merge user config over defaults
    config = DEFAULT_CONFIG.copy()
    config.update(user_config)
    return config

def save_config(config: dict, path: str = "config.json") -> None:
    with open(path, "w", encoding="utf-8") as f:
        json.dump(config, f, indent=2, ensure_ascii=False)
```

---

## `csv` — Reading and Writing CSV Files

### `csv.reader()` — Read as Lists

```python
import csv

# Sample file: students.csv
# name,score,city
# Mohamed,88,Chennai
# Priya,92,Mumbai
# Arjun,74,Delhi

with open("students.csv", "r", encoding="utf-8", newline="") as f:
    reader = csv.reader(f)

    # Read header
    header = next(reader)
    print(header)    # ['name', 'score', 'city']

    # Read rows
    for row in reader:
        print(row)    # ['Mohamed', '88', 'Chennai']
                      # ['Priya', '92', 'Mumbai']
                      # NOTE: all values are strings — convert explicitly
        name  = row[0]
        score = int(row[1])    # must convert
        city  = row[2]
```

**Always pass `newline=""` when opening CSV files:**

```python
# WHY: csv module handles newlines internally
# Without newline="" — extra blank rows on Windows (\r\n treated as two newlines)
# Always:
with open("data.csv", "r", encoding="utf-8", newline="") as f:
    reader = csv.reader(f)
```

### Delimiter and Quotechar Options

```python
import csv

# Tab-separated values (TSV)
with open("data.tsv", "r", encoding="utf-8", newline="") as f:
    reader = csv.reader(f, delimiter="\t")

# Pipe-delimited
with open("data.txt", "r", encoding="utf-8", newline="") as f:
    reader = csv.reader(f, delimiter="|")

# Custom quoting character
with open("data.csv", "r", encoding="utf-8", newline="") as f:
    reader = csv.reader(f, delimiter=",", quotechar="'")

# Fields containing the delimiter are automatically handled
# "Mohamed","Chennai, Tamil Nadu",88  → ['Mohamed', 'Chennai, Tamil Nadu', '88']

# Quoting options
csv.QUOTE_ALL       # quote every field
csv.QUOTE_MINIMAL   # only quote when necessary (default)
csv.QUOTE_NONNUMERIC # quote all non-numeric fields
csv.QUOTE_NONE      # never quote
```

### `csv.writer()` — Write Rows

```python
import csv

rows = [
    ["name", "score", "city"],
    ["Mohamed", 88, "Chennai"],
    ["Priya", 92, "Mumbai"],
    ["Arjun", 74, "Delhi"],
]

with open("output.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.writer(f)
    writer.writerows(rows)      # write all at once

# Or write one by one
with open("output.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["name", "score", "city"])   # header
    for name, score, city in [("Mohamed", 88, "Chennai"), ("Priya", 92, "Mumbai")]:
        writer.writerow([name, score, city])

# Custom delimiter
with open("output.tsv", "w", encoding="utf-8", newline="") as f:
    writer = csv.writer(f, delimiter="\t")
    writer.writerows(rows)
```

---

### `csv.DictReader()` — Read as Dicts

Each row becomes a dict with header as keys. Much more readable than index-based access.

```python
import csv

with open("students.csv", "r", encoding="utf-8", newline="") as f:
    reader = csv.DictReader(f)

    print(reader.fieldnames)    # None until first row read (or after iteration starts)

    for row in reader:
        print(dict(row))   # {'name': 'Mohamed', 'score': '88', 'city': 'Chennai'}
        print(row["name"])    # Mohamed
        print(int(row["score"]))  # 88  — still strings, still must convert
```

**Custom fieldnames — when CSV has no header row:**

```python
import csv

with open("no_header.csv", "r", encoding="utf-8", newline="") as f:
    reader = csv.DictReader(f, fieldnames=["name", "score", "city"])
    for row in reader:
        print(row)
```

**Reading all rows into a list:**

```python
import csv

with open("students.csv", "r", encoding="utf-8", newline="") as f:
    reader = csv.DictReader(f)
    students = list(reader)    # list of dicts

# Process after file is closed
for s in students:
    print(f"{s['name']}: {s['score']}")
```

---

### `csv.DictWriter()` — Write Dicts

```python
import csv

students = [
    {"name": "Mohamed", "score": 88, "city": "Chennai"},
    {"name": "Priya",   "score": 92, "city": "Mumbai"},
    {"name": "Arjun",   "score": 74, "city": "Delhi"},
]

fieldnames = ["name", "score", "city"]

with open("students.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=fieldnames)

    writer.writeheader()         # write the header row
    writer.writerows(students)   # write all rows

# Result:
# name,score,city
# Mohamed,88,Chennai
# Priya,92,Mumbai
# Arjun,74,Delhi
```

**`extrasaction` — handling extra fields:**

```python
import csv

data = [
    {"name": "Mohamed", "score": 88, "city": "Chennai", "extra_field": "ignored"},
]

# Default: extrasaction='raise' — ValueError if dict has keys not in fieldnames
# extrasaction='ignore' — silently ignores extra fields
with open("output.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(
        f,
        fieldnames=["name", "score", "city"],
        extrasaction="ignore"    # ignore the extra_field
    )
    writer.writeheader()
    writer.writerows(data)
```

**`restval` — fill missing fields:**

```python
import csv

data = [
    {"name": "Mohamed", "score": 88},        # missing 'city'
    {"name": "Priya",   "score": 92, "city": "Mumbai"},
]

with open("output.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(
        f,
        fieldnames=["name", "score", "city"],
        restval=""    # fill missing fields with empty string
    )
    writer.writeheader()
    writer.writerows(data)

# name,score,city
# Mohamed,88,        ← city filled with ""
# Priya,92,Mumbai
```

---

## `python-dotenv` — Environment Configuration

### Why `.env` Files Exist

Hardcoding config in source code is bad:
- Secrets in git history
- Different values needed for dev/test/prod
- Changing config requires redeploying code

`.env` files solve this — config lives outside code, never committed to git.

```bash
pip install python-dotenv
```

### `.env` File Format

```bash
# .env file — never commit this to git
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
SECRET_KEY=super-secret-key-change-in-production
DEBUG=true
PORT=8000
ALLOWED_HOSTS=localhost,127.0.0.1
MAX_CONNECTIONS=10

# Comments start with #
# No spaces around = sign (by convention)
# Values don't need quotes (but can have them)
QUOTED="this works too"
SINGLE='also works'

# Multi-line values with quotes
PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA...
-----END RSA PRIVATE KEY-----"
```

### `load_dotenv()` — Load into `os.environ`

```python
from dotenv import load_dotenv
import os

# Load .env file — variables become available in os.environ
load_dotenv()

database_url = os.getenv("DATABASE_URL")
secret_key   = os.getenv("SECRET_KEY")
debug        = os.getenv("DEBUG", "false").lower() == "true"
port         = int(os.getenv("PORT", "8000"))

print(database_url)    # postgresql://user:password@localhost:5432/mydb
print(debug)           # True
print(port)            # 8000
```

**`load_dotenv()` parameters:**

```python
from dotenv import load_dotenv
from pathlib import Path

# Explicit path — when .env is not in current directory
load_dotenv(dotenv_path=Path("/etc/myapp/.env"))
load_dotenv(dotenv_path=".env.local")

# override — whether to overwrite existing env vars
load_dotenv(override=False)   # default — existing env vars take precedence
load_dotenv(override=True)    # .env values overwrite existing env vars

# verbose — print which file was loaded
load_dotenv(verbose=True)

# encoding — for non-ASCII values
load_dotenv(encoding="utf-8")
```

### `dotenv_values()` — Load as Dict (Without Modifying `os.environ`)

```python
from dotenv import dotenv_values

# Returns a dict — doesn't touch os.environ
config = dotenv_values(".env")

print(config["DATABASE_URL"])    # postgresql://...
print(config["PORT"])            # "8000"  — all values are strings

# Useful for:
# - Reading .env without side effects
# - Comparing two .env files
# - Loading multiple .env files and merging
```

**Merging multiple `.env` files:**

```python
from dotenv import dotenv_values

# Layered config — later values override earlier
config = {
    **dotenv_values(".env"),           # base defaults
    **dotenv_values(".env.local"),     # local overrides
    **dotenv_values(".env.test"),      # test-specific (if running tests)
}

print(config.get("DATABASE_URL"))
```

---

## `os.environ` vs `dotenv` — Precedence and Override

### Default Behaviour — Environment Variables Win

```python
import os
from dotenv import load_dotenv

# In terminal: export DATABASE_URL=prod_database
# In .env file: DATABASE_URL=dev_database

load_dotenv()   # override=False (default)

print(os.getenv("DATABASE_URL"))
# prod_database  — real env var wins over .env file

# This is intentional — lets CI/CD systems override .env settings
# by setting real environment variables
```

### `override=True` — `.env` Wins

```python
import os
from dotenv import load_dotenv

os.environ["PORT"] = "9000"    # already set

load_dotenv(override=True)     # .env has PORT=8000

print(os.getenv("PORT"))       # 8000  — .env overwrote it
```

### The Precedence Stack (most to least priority)

```
1. Real environment variables (set by OS, CI/CD, Docker)
2. .env.local (if load_dotenv(".env.local") called explicitly)
3. .env file (loaded by load_dotenv())
4. os.getenv("KEY", "default") defaults
```

### Real-World Config Pattern

```python
import os
from pathlib import Path
from dotenv import load_dotenv

# Load .env only if not already set (respect real env vars)
load_dotenv()

class Config:
    """Application configuration loaded from environment."""

    # Required — raise immediately if missing, not at use time
    DATABASE_URL: str = os.environ["DATABASE_URL"]
    SECRET_KEY:   str = os.environ["SECRET_KEY"]

    # Optional with defaults
    DEBUG:          bool = os.getenv("DEBUG", "false").lower() == "true"
    PORT:           int  = int(os.getenv("PORT", "8000"))
    LOG_LEVEL:      str  = os.getenv("LOG_LEVEL", "INFO").upper()
    MAX_CONN:       int  = int(os.getenv("MAX_CONNECTIONS", "10"))

    # List from comma-separated string
    ALLOWED_HOSTS: list[str] = os.getenv("ALLOWED_HOSTS", "localhost").split(",")

    @classmethod
    def is_production(cls) -> bool:
        return os.getenv("ENVIRONMENT", "development") == "production"

    @classmethod
    def is_testing(cls) -> bool:
        return os.getenv("TESTING", "false").lower() == "true"

try:
    config = Config()
    print(f"Running on port {config.PORT}")
    print(f"Debug: {config.DEBUG}")
    print(f"Allowed hosts: {config.ALLOWED_HOSTS}")
except KeyError as e:
    print(f"Missing required environment variable: {e}")
    raise SystemExit(1)
```

---

## Putting It All Together

```python
import json
import csv
import os
from pathlib import Path
from decimal import Decimal
from datetime import datetime
from dotenv import load_dotenv

# Load environment
load_dotenv()

OUTPUT_DIR = Path(os.getenv("OUTPUT_DIR", "output"))
DATA_FILE  = Path(os.getenv("DATA_FILE",  "students.csv"))

# Custom JSON encoder
class AppEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        if isinstance(obj, Decimal):
            return str(obj)
        if isinstance(obj, Path):
            return str(obj)
        if isinstance(obj, set):
            return sorted(list(obj))
        return super().default(obj)

def read_students(path: Path) -> list[dict]:
    """Read CSV, return list of typed dicts."""
    students = []
    with open(path, "r", encoding="utf-8", newline="") as f:
        reader = csv.DictReader(f)
        for row in reader:
            students.append({
                "name":       row["name"].strip(),
                "score":      float(row["score"]),
                "city":       row["city"].strip(),
                "loaded_at":  datetime.now(),
            })
    return students

def compute_summary(students: list[dict]) -> dict:
    """Compute statistics."""
    scores = [s["score"] for s in students]
    return {
        "count":    len(students),
        "average":  Decimal(str(sum(scores) / len(scores))).quantize(Decimal("0.01")),
        "highest":  max(scores),
        "lowest":   min(scores),
        "cities":   {s["city"] for s in students},
        "passing":  sum(1 for s in students if s["score"] >= 60),
        "generated_at": datetime.now(),
    }

def write_report_json(summary: dict, path: Path) -> None:
    """Write JSON report."""
    path.parent.mkdir(parents=True, exist_ok=True)
    with open(path, "w", encoding="utf-8") as f:
        json.dump(summary, f, cls=AppEncoder, indent=2, ensure_ascii=False)

def write_report_csv(students: list[dict], path: Path) -> None:
    """Write processed CSV."""
    path.parent.mkdir(parents=True, exist_ok=True)

    fieldnames = ["name", "score", "city", "grade", "status"]

    def grade(score: float) -> str:
        if score >= 90: return "A"
        if score >= 80: return "B"
        if score >= 70: return "C"
        if score >= 60: return "D"
        return "F"

    with open(path, "w", encoding="utf-8", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=fieldnames, extrasaction="ignore")
        writer.writeheader()

        for s in sorted(students, key=lambda x: x["score"], reverse=True):
            writer.writerow({
                "name":   s["name"],
                "score":  s["score"],
                "city":   s["city"],
                "grade":  grade(s["score"]),
                "status": "pass" if s["score"] >= 60 else "fail",
            })

# Create sample data
sample_csv = Path("students_sample.csv")
sample_data = [
    ["name", "score", "city"],
    ["Mohamed", "88",  "Chennai"],
    ["Priya",   "92",  "Mumbai"],
    ["Arjun",   "74",  "Delhi"],
    ["Sara",    "65",  "Chennai"],
    ["Vikram",  "45",  "Mumbai"],
]
with open(sample_csv, "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows(sample_data)

# Run pipeline
students = read_students(sample_csv)
summary  = compute_summary(students)

OUTPUT_DIR.mkdir(parents=True, exist_ok=True)
write_report_json(summary,   OUTPUT_DIR / "summary.json")
write_report_csv(students,   OUTPUT_DIR / "processed.csv")

# Verify JSON output
json_content = json.loads((OUTPUT_DIR / "summary.json").read_text(encoding="utf-8"))
print("Summary JSON:")
print(json.dumps(json_content, indent=2))

# Verify CSV output
print("\nProcessed CSV:")
with open(OUTPUT_DIR / "processed.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        print(dict(row))
```

---

## Key Takeaways

**JSON**
- `json.loads()` / `json.dumps()` for strings. `json.load()` / `json.dump()` for files.
- Always `encoding="utf-8"` and `ensure_ascii=False` when writing JSON files.
- `indent=2` for human-readable. `sort_keys=True` for deterministic output and clean git diffs.
- `default=` function handles unknown types in `dumps()`. For reusability, subclass `JSONEncoder` and override `default()`.
- `object_hook=` in `loads()` / `load()` converts dicts back to custom objects during decode.
- All JSON values are strings after `loads()` — `datetime`, `Decimal`, `UUID` need explicit conversion back.

**CSV**
- Always open with `newline=""` — the csv module handles line endings internally.
- Always specify `encoding="utf-8"` — platform default can cause issues.
- `csv.reader` / `csv.writer` — index-based, list of lists.
- `csv.DictReader` / `csv.DictWriter` — name-based, list of dicts. Preferred in real code.
- All values are strings when reading — convert explicitly (`int(row["score"])`).
- `DictWriter` needs `fieldnames` list. `writeheader()` writes the header. `extrasaction="ignore"` handles extra keys.

**dotenv**
- `.env` files are never committed to git. Add `.env` to `.gitignore`. Commit `.env.example` with dummy values.
- `load_dotenv()` loads `.env` into `os.environ`. Use `os.getenv()` after.
- `dotenv_values()` returns a dict without touching `os.environ` — use for merging or testing.
- Default precedence: real env vars > `.env` file. Use `override=True` to flip this.
- Required vars: use `os.environ["KEY"]` — raises `KeyError` immediately if missing. Optional vars: use `os.getenv("KEY", "default")`.