Let's go. Full topic, nothing skipped.

---

## 2.4 `datetime`

---

## Why `datetime` Is Tricky

`datetime` looks simple — just dates and times. But naive vs aware datetimes, timezone handling, and DST transitions cause real production bugs in billing systems, scheduling, and APIs. Getting this right from the start matters.

---

## The Four Core Types

```python
from datetime import date, time, datetime, timedelta, timezone
```

| Type | Represents | Example |
|---|---|---|
| `date` | Calendar date only | `2024-11-15` |
| `time` | Time of day only | `14:30:45` |
| `datetime` | Date + time combined | `2024-11-15 14:30:45` |
| `timedelta` | Duration / difference | `3 days, 2 hours` |

---

## `date` — Calendar Date

```python
from datetime import date

# Construction
d = date(2024, 11, 15)       # year, month, day
print(d)                      # 2024-11-15
print(type(d))                # <class 'datetime.date'>

# Today
today = date.today()
print(today)                  # current local date

# Attributes
print(d.year)                 # 2024
print(d.month)                # 11
print(d.day)                  # 15
print(d.weekday())            # 0=Mon, 6=Sun → 4 = Friday
print(d.isoweekday())         # 1=Mon, 7=Sun → 5 = Friday
print(d.isocalendar())        # datetime.IsoCalendarDate(year=2024, week=46, weekday=5)

# Arithmetic — date + timedelta = date
from datetime import timedelta
tomorrow    = today + timedelta(days=1)
last_week   = today - timedelta(weeks=1)
in_30_days  = today + timedelta(days=30)

# date - date = timedelta
delta = date(2024, 12, 31) - date(2024, 1, 1)
print(delta.days)    # 365

# Min/Max
print(date.min)     # 0001-01-01
print(date.max)     # 9999-12-31
```

---

## `time` — Time of Day

```python
from datetime import time

# Construction — all optional, default 0
t = time(14, 30, 45)               # hour, minute, second
t = time(14, 30, 45, 123456)       # + microseconds
t = time(14, 30, 45, 0, timezone.utc)  # + tzinfo

print(t)                            # 14:30:45

# Attributes
print(t.hour)         # 14
print(t.minute)       # 30
print(t.second)       # 45
print(t.microsecond)  # 0
print(t.tzinfo)       # None (naive) or timezone object

# time does NOT support arithmetic — use datetime for that
# t + timedelta(hours=2)   # TypeError: unsupported operand type
```

---

## `datetime` — Date + Time Combined

The type you'll use most in backend code.

```python
from datetime import datetime, timezone, timedelta

# Construction
dt = datetime(2024, 11, 15, 14, 30, 45)
dt = datetime(2024, 11, 15, 14, 30, 45, 123456)    # + microseconds
dt = datetime(2024, 11, 15)                          # time defaults to midnight

print(dt)       # 2024-11-15 14:30:45

# From date and time components
from datetime import date, time
d = date(2024, 11, 15)
t = time(14, 30, 45)
dt = datetime.combine(d, t)
print(dt)       # 2024-11-15 14:30:45

# Split back
print(dt.date())    # 2024-11-15  → date object
print(dt.time())    # 14:30:45    → time object

# Replace specific fields
dt2 = dt.replace(hour=0, minute=0, second=0, microsecond=0)
print(dt2)    # 2024-11-15 00:00:00  — start of day

# Attributes
print(dt.year, dt.month, dt.day)             # 2024 11 15
print(dt.hour, dt.minute, dt.second)         # 14 30 45
print(dt.microsecond)                        # 0
print(dt.weekday())                          # 4 (Friday)
```

---

## Naive vs Aware — The Most Important Distinction

**Naive datetime** — has no timezone info. Python doesn't know if it's UTC, IST, or EST.
**Aware datetime** — has timezone info attached. Unambiguous point in time.

```python
from datetime import datetime, timezone

# Naive — no timezone
naive = datetime(2024, 11, 15, 14, 30)
print(naive.tzinfo)    # None

# Aware — has timezone
aware = datetime(2024, 11, 15, 14, 30, tzinfo=timezone.utc)
print(aware.tzinfo)    # UTC

# You CANNOT mix naive and aware
naive + timedelta(hours=1)    # OK
aware + timedelta(hours=1)    # OK
aware - naive                 # TypeError: can't subtract offset-naive and offset-aware datetimes
aware == naive                # TypeError
```

---

## `datetime.now()` vs `datetime.utcnow()` vs `datetime.now(tz=timezone.utc)`

This is where most bugs happen. Know all three cold.

### `datetime.now()` — Local Time, Naive

```python
from datetime import datetime

now = datetime.now()
print(now)           # 2024-11-15 19:00:00.123456  (your local time)
print(now.tzinfo)    # None  — NAIVE — dangerous
```

**Problem:** You store this in the database. Later you read it. What timezone is it? Nobody knows. When your server moves from one timezone to another, all stored times are wrong.

### `datetime.utcnow()` — UTC Time, Naive — DEPRECATED

```python
from datetime import datetime

utc_now = datetime.utcnow()
print(utc_now)           # 2024-11-15 14:00:00.123456  (UTC)
print(utc_now.tzinfo)    # None  — STILL NAIVE — still dangerous
```

**Problem:** It LOOKS like UTC but has no timezone info. You can't tell it apart from a naive local time. Python 3.12 deprecated this — use `datetime.now(timezone.utc)` instead.

### `datetime.now(tz=timezone.utc)` — UTC Time, Aware — USE THIS

```python
from datetime import datetime, timezone

utc_now = datetime.now(tz=timezone.utc)
print(utc_now)           # 2024-11-15 14:00:00.123456+00:00
print(utc_now.tzinfo)    # UTC  — AWARE — safe

# Shorthand also works
utc_now = datetime.now(timezone.utc)
```

**This is the correct way.** Always use aware datetimes in production code.

### Why Naive Datetimes Are Dangerous — Concrete Example

```python
from datetime import datetime, timezone

# Bug scenario: billing system
# User signs up, we store naive datetime
signup = datetime.now()                     # naive: 2024-11-15 19:00:00

# We want to send renewal reminder 30 days before expiry
# But the server is in UTC, user is in IST (UTC+5:30)
# naive.now() on UTC server = different time than user's local time

# Correct approach
signup = datetime.now(timezone.utc)         # aware: 2024-11-15 14:00:00+00:00
expiry = signup + timedelta(days=365)
reminder = expiry - timedelta(days=30)

# Convert to user's timezone for display only
ist = timezone(timedelta(hours=5, minutes=30))
print(reminder.astimezone(ist))    # shows correct local time to user
```

**The rule: always store UTC aware datetimes. Convert to local timezone only for display.**

---

## `timezone.utc` and `timezone(timedelta(hours=N))`

```python
from datetime import timezone, timedelta, datetime

# Built-in UTC timezone
tz_utc = timezone.utc
print(tz_utc)    # UTC

# Fixed offset timezones
tz_ist  = timezone(timedelta(hours=5, minutes=30))   # IST — India Standard Time UTC+5:30
tz_est  = timezone(timedelta(hours=-5))              # EST — Eastern Standard Time UTC-5
tz_jst  = timezone(timedelta(hours=9))               # JST — Japan Standard Time UTC+9
tz_cet  = timezone(timedelta(hours=1))               # CET — Central European UTC+1

print(tz_ist)    # UTC+05:30

# Create aware datetime in a specific timezone
now_ist = datetime.now(tz=tz_ist)
print(now_ist)    # 2024-11-15 19:30:00+05:30

# Convert between timezones using astimezone()
now_utc = datetime.now(timezone.utc)
now_ist = now_utc.astimezone(tz_ist)
now_jst = now_utc.astimezone(tz_jst)

print(now_utc)    # 2024-11-15 14:00:00+00:00
print(now_ist)    # 2024-11-15 19:30:00+05:30  — same moment, different representation
print(now_jst)    # 2024-11-15 23:00:00+09:00  — same moment

# They are equal — same point in time
print(now_utc == now_ist)    # True
print(now_utc == now_jst)    # True
```

### `zoneinfo` — IANA Timezone Database (Python 3.9+)

For real apps with DST (Daylight Saving Time), fixed offsets like `timezone(timedelta(hours=-5))` are wrong — EST is UTC-5 in winter but UTC-4 in summer.

```python
from zoneinfo import ZoneInfo
from datetime import datetime

# IANA timezone names handle DST automatically
tz_new_york = ZoneInfo("America/New_York")
tz_london   = ZoneInfo("Europe/London")
tz_ist      = ZoneInfo("Asia/Kolkata")        # India doesn't have DST
tz_tokyo    = ZoneInfo("Asia/Tokyo")

now = datetime.now(ZoneInfo("America/New_York"))
print(now)    # handles EST/EDT automatically based on date

# List available timezones
from zoneinfo import available_timezones
print(len(available_timezones()))    # ~600+ timezones

# Convert
now_utc = datetime.now(ZoneInfo("UTC"))
now_ny  = now_utc.astimezone(ZoneInfo("America/New_York"))
now_ist = now_utc.astimezone(ZoneInfo("Asia/Kolkata"))

print(now_utc)    # 2024-11-15 14:00:00+00:00
print(now_ny)     # 2024-11-15 09:00:00-05:00  (EST in November)
print(now_ist)    # 2024-11-15 19:30:00+05:30
```

---

## `timedelta` — Durations and Arithmetic

```python
from datetime import timedelta

# Construction — all optional, default 0
td = timedelta(days=5)
td = timedelta(weeks=2)
td = timedelta(hours=3, minutes=30)
td = timedelta(days=1, hours=6, minutes=30, seconds=45, microseconds=500000)

# Internally stored as (days, seconds, microseconds)
td = timedelta(hours=25)
print(td.days)         # 1
print(td.seconds)      # 3600   (1 hour remaining after 1 day)
print(td.total_seconds())   # 90000.0  — total in seconds

# Arithmetic
td1 = timedelta(days=5)
td2 = timedelta(days=3)

print(td1 + td2)    # 8 days, 0:00:00
print(td1 - td2)    # 2 days, 0:00:00
print(td1 * 2)      # 10 days, 0:00:00
print(td1 / 2)      # 2 days, 12:00:00
print(td1 / td2)    # 1.6666...  — ratio

# Negative timedelta
td = timedelta(days=-3)
print(td)    # -3 days, 0:00:00
print(abs(td))   # 3 days, 0:00:00
```

### datetime Arithmetic

```python
from datetime import datetime, timedelta, timezone

now = datetime.now(timezone.utc)

# Add/subtract durations
one_hour_later  = now + timedelta(hours=1)
yesterday       = now - timedelta(days=1)
next_week       = now + timedelta(weeks=1)
in_90_days      = now + timedelta(days=90)

# Subtract two datetimes → timedelta
dt1 = datetime(2024, 11, 15, tzinfo=timezone.utc)
dt2 = datetime(2024, 1,  1,  tzinfo=timezone.utc)
delta = dt1 - dt2
print(delta.days)              # 319
print(delta.total_seconds())   # 27561600.0

# Age calculation
birthday = datetime(1999, 3, 15, tzinfo=timezone.utc)
age_delta = datetime.now(timezone.utc) - birthday
age_years = age_delta.days // 365
print(f"Age: {age_years} years")

# Business logic
def is_expired(created_at: datetime, ttl_days: int = 30) -> bool:
    return datetime.now(timezone.utc) > created_at + timedelta(days=ttl_days)

def time_until(target: datetime) -> timedelta:
    delta = target - datetime.now(timezone.utc)
    return max(delta, timedelta(0))    # return 0 if already past
```

---

## `strftime()` — Format datetime as String

`strftime` = "string format time". Converts a datetime to a formatted string.

```python
from datetime import datetime, timezone

dt = datetime(2024, 11, 15, 14, 30, 45, tzinfo=timezone.utc)

# Common format codes
print(dt.strftime("%Y-%m-%d"))           # 2024-11-15  (ISO date)
print(dt.strftime("%d/%m/%Y"))           # 15/11/2024  (European)
print(dt.strftime("%m/%d/%Y"))           # 11/15/2024  (American)
print(dt.strftime("%Y-%m-%d %H:%M:%S")) # 2024-11-15 14:30:45
print(dt.strftime("%B %d, %Y"))          # November 15, 2024
print(dt.strftime("%b %d, %Y"))          # Nov 15, 2024
print(dt.strftime("%A, %B %d"))          # Friday, November 15
print(dt.strftime("%I:%M %p"))           # 02:30 PM  (12-hour)
print(dt.strftime("%H:%M:%S"))           # 14:30:45  (24-hour)
print(dt.strftime("%d %b %Y %H:%M UTC")) # 15 Nov 2024 14:30 UTC
```

### All Format Codes

```python
# Date
%Y   # 4-digit year:         2024
%y   # 2-digit year:         24
%m   # month (zero-padded):  11
%d   # day (zero-padded):    15
%j   # day of year:          320

# Month names
%B   # full month name:      November
%b   # abbreviated month:    Nov

# Weekday
%A   # full weekday:         Friday
%a   # abbreviated weekday:  Fri
%w   # weekday as number:    5 (0=Sunday)
%u   # weekday (1=Monday):   5

# Time
%H   # hour 24h (00-23):     14
%I   # hour 12h (01-12):     02
%M   # minute (00-59):       30
%S   # second (00-59):       45
%f   # microseconds:         000000
%p   # AM or PM:             PM

# Timezone
%Z   # timezone name:        UTC
%z   # UTC offset:           +0000

# Combined
%c   # locale datetime:      Fri Nov 15 14:30:45 2024
%x   # locale date:          11/15/24
%X   # locale time:          14:30:45

# Special
%%   # literal %:            %
```

---

## `strptime()` — Parse String to `datetime`

`strptime` = "string parse time". Inverse of `strftime`.

```python
from datetime import datetime

# strptime(string, format) — format must match string exactly
dt = datetime.strptime("2024-11-15", "%Y-%m-%d")
print(dt)    # 2024-11-15 00:00:00  — time defaults to midnight

dt = datetime.strptime("2024-11-15 14:30:45", "%Y-%m-%d %H:%M:%S")
print(dt)    # 2024-11-15 14:30:45

dt = datetime.strptime("15/11/2024", "%d/%m/%Y")
print(dt)    # 2024-11-15 00:00:00

dt = datetime.strptime("November 15, 2024", "%B %d, %Y")
print(dt)    # 2024-11-15 00:00:00

dt = datetime.strptime("15 Nov 2024 14:30 UTC", "%d %b %Y %H:%M UTC")
print(dt)    # 2024-11-15 14:30:00  — note: timezone NOT parsed as tzinfo

# strptime with timezone offset
dt = datetime.strptime("2024-11-15T14:30:45+05:30", "%Y-%m-%dT%H:%M:%S%z")
print(dt)           # 2024-11-15 14:30:45+05:30
print(dt.tzinfo)    # UTC+05:30  — now it's aware
```

**`strptime` returns naive datetime unless format includes `%z`:**

```python
# Parse and make aware
dt_naive = datetime.strptime("2024-11-15 14:30:45", "%Y-%m-%d %H:%M:%S")
dt_aware = dt_naive.replace(tzinfo=timezone.utc)   # assume it was UTC

# Or parse with timezone in string
dt_aware = datetime.strptime("2024-11-15 14:30:45+0000", "%Y-%m-%d %H:%M:%S%z")
```

**`strptime` raises `ValueError` if format doesn't match:**

```python
from datetime import datetime

try:
    dt = datetime.strptime("15-11-2024", "%Y-%m-%d")   # format mismatch
except ValueError as e:
    print(e)    # time data '15-11-2024' does not match format '%Y-%m-%d'
```

---

## `isoformat()` and `fromisoformat()`

ISO 8601 is the international standard for date/time strings. Use it for APIs, databases, and logs.

### `isoformat()` — Convert to ISO 8601 String

```python
from datetime import datetime, date, time, timezone, timedelta

# date
d = date(2024, 11, 15)
print(d.isoformat())        # 2024-11-15

# time
t = time(14, 30, 45)
print(t.isoformat())        # 14:30:45

t = time(14, 30, 45, 123456)
print(t.isoformat())        # 14:30:45.123456

# datetime — naive
dt = datetime(2024, 11, 15, 14, 30, 45)
print(dt.isoformat())       # 2024-11-15T14:30:45
print(dt.isoformat(" "))    # 2024-11-15 14:30:45  — custom separator

# datetime — aware
dt = datetime(2024, 11, 15, 14, 30, 45, tzinfo=timezone.utc)
print(dt.isoformat())       # 2024-11-15T14:30:45+00:00

dt = datetime(2024, 11, 15, 14, 30, 45,
              tzinfo=timezone(timedelta(hours=5, minutes=30)))
print(dt.isoformat())       # 2024-11-15T14:30:45+05:30

# timespec — control seconds precision
dt = datetime(2024, 11, 15, 14, 30, 45, 123456, tzinfo=timezone.utc)
print(dt.isoformat(timespec="seconds"))       # 2024-11-15T14:30:45+00:00
print(dt.isoformat(timespec="milliseconds"))  # 2024-11-15T14:30:45.123+00:00
print(dt.isoformat(timespec="microseconds"))  # 2024-11-15T14:30:45.123456+00:00
print(dt.isoformat(timespec="minutes"))       # 2024-11-15T14:30+00:00
```

### `fromisoformat()` — Parse ISO 8601 String

```python
from datetime import datetime, date, time

# date
d = date.fromisoformat("2024-11-15")
print(d)    # 2024-11-15
print(type(d))    # <class 'datetime.date'>

# time
t = time.fromisoformat("14:30:45")
print(t)    # 14:30:45

t = time.fromisoformat("14:30:45.123456")
print(t)    # 14:30:45.123456

# datetime — naive
dt = datetime.fromisoformat("2024-11-15T14:30:45")
print(dt)    # 2024-11-15 14:30:45
print(dt.tzinfo)    # None — naive

# datetime — with space separator (Python 3.7+)
dt = datetime.fromisoformat("2024-11-15 14:30:45")
print(dt)    # 2024-11-15 14:30:45

# datetime — aware (Python 3.7+)
dt = datetime.fromisoformat("2024-11-15T14:30:45+00:00")
print(dt)           # 2024-11-15 14:30:45+00:00
print(dt.tzinfo)    # UTC+00:00 — aware

dt = datetime.fromisoformat("2024-11-15T14:30:45+05:30")
print(dt)    # 2024-11-15 14:30:45+05:30

# Python 3.11+ — full ISO 8601 support including Z suffix
dt = datetime.fromisoformat("2024-11-15T14:30:45Z")   # Z = UTC (3.11+)
print(dt)    # 2024-11-15 14:30:45+00:00
```

**`fromisoformat` vs `strptime` — when to use which:**

```python
# fromisoformat — for ISO 8601 strings (APIs, databases, logs)
# Fast, no format string needed
dt = datetime.fromisoformat("2024-11-15T14:30:45+00:00")

# strptime — for arbitrary human formats (user input, legacy systems)
# Needs explicit format string
dt = datetime.strptime("15 Nov 2024", "%d %b %Y")
```

---

## Unix Timestamps

```python
from datetime import datetime, timezone
import time

# Current Unix timestamp (seconds since 1970-01-01 00:00:00 UTC)
ts = time.time()
print(ts)    # 1731676245.123456

# datetime → Unix timestamp
dt = datetime.now(timezone.utc)
ts = dt.timestamp()
print(ts)    # 1731676245.123456

# Unix timestamp → datetime (always specify UTC to avoid naive datetime)
dt = datetime.fromtimestamp(1731676245, tz=timezone.utc)
print(dt)    # 2024-11-15 14:30:45+00:00

# fromtimestamp without tz — returns LOCAL time (naive) — avoid
dt_naive = datetime.fromtimestamp(1731676245)    # naive local time — risky
```

---

## Practical Patterns

### Start and End of Day

```python
from datetime import datetime, time, timezone, timedelta

def start_of_day(dt: datetime) -> datetime:
    """Midnight at the start of the given date in UTC."""
    return dt.replace(hour=0, minute=0, second=0, microsecond=0)

def end_of_day(dt: datetime) -> datetime:
    """Last microsecond of the given date in UTC."""
    return dt.replace(hour=23, minute=59, second=59, microsecond=999999)

today = datetime.now(timezone.utc)
print(start_of_day(today))    # 2024-11-15 00:00:00+00:00
print(end_of_day(today))      # 2024-11-15 23:59:59.999999+00:00
```

### Checking if a Datetime Is in the Past or Future

```python
from datetime import datetime, timezone

def is_expired(expires_at: datetime) -> bool:
    return datetime.now(timezone.utc) >= expires_at

def time_remaining(deadline: datetime) -> timedelta | None:
    delta = deadline - datetime.now(timezone.utc)
    return delta if delta.total_seconds() > 0 else None
```

### Parsing Multiple Date Formats

```python
from datetime import datetime

FORMATS = [
    "%Y-%m-%d",
    "%Y-%m-%dT%H:%M:%S",
    "%Y-%m-%dT%H:%M:%S%z",
    "%d/%m/%Y",
    "%d-%m-%Y",
    "%B %d, %Y",
]

def parse_date_flexible(s: str) -> datetime | None:
    for fmt in FORMATS:
        try:
            return datetime.strptime(s, fmt)
        except ValueError:
            continue
    # Try ISO format as fallback
    try:
        return datetime.fromisoformat(s)
    except ValueError:
        return None

print(parse_date_flexible("2024-11-15"))
print(parse_date_flexible("15/11/2024"))
print(parse_date_flexible("November 15, 2024"))
print(parse_date_flexible("2024-11-15T14:30:45+05:30"))
```

---

## Putting It All Together

```python
from datetime import datetime, date, timedelta, timezone
from zoneinfo import ZoneInfo
import json

class Event:
    """A scheduled event with timezone-aware timestamps."""

    def __init__(
        self,
        title:      str,
        start_utc:  datetime,
        duration:   timedelta,
    ):
        if start_utc.tzinfo is None:
            raise ValueError("start_utc must be timezone-aware")

        self.title      = title
        self.start_utc  = start_utc.astimezone(timezone.utc)
        self.duration   = duration

    @property
    def end_utc(self) -> datetime:
        return self.start_utc + self.duration

    @property
    def is_past(self) -> bool:
        return self.end_utc < datetime.now(timezone.utc)

    @property
    def is_upcoming(self) -> bool:
        return self.start_utc > datetime.now(timezone.utc)

    @property
    def is_ongoing(self) -> bool:
        now = datetime.now(timezone.utc)
        return self.start_utc <= now <= self.end_utc

    def time_until_start(self) -> timedelta | None:
        delta = self.start_utc - datetime.now(timezone.utc)
        return delta if delta.total_seconds() > 0 else None

    def local_start(self, tz_name: str) -> datetime:
        """Start time in the given IANA timezone."""
        return self.start_utc.astimezone(ZoneInfo(tz_name))

    def to_dict(self) -> dict:
        return {
            "title":     self.title,
            "start_utc": self.start_utc.isoformat(),
            "end_utc":   self.end_utc.isoformat(),
            "duration_minutes": int(self.duration.total_seconds() / 60),
        }

    @classmethod
    def from_dict(cls, data: dict) -> "Event":
        return cls(
            title     = data["title"],
            start_utc = datetime.fromisoformat(data["start_utc"]),
            duration  = timedelta(minutes=data["duration_minutes"]),
        )

    def __repr__(self) -> str:
        return (
            f"Event({self.title!r}, "
            f"start={self.start_utc.strftime('%Y-%m-%d %H:%M UTC')})"
        )


# Demo
event = Event(
    title     = "Python Backend Workshop",
    start_utc = datetime(2025, 3, 15, 9, 0, tzinfo=timezone.utc),
    duration  = timedelta(hours=3),
)

print(f"Event: {event.title}")
print(f"Start UTC: {event.start_utc.isoformat()}")
print(f"End UTC:   {event.end_utc.isoformat()}")
print(f"Duration:  {event.duration}")
print(f"Is past:     {event.is_past}")
print(f"Is upcoming: {event.is_upcoming}")

print(f"\nStart in IST: {event.local_start('Asia/Kolkata').strftime('%Y-%m-%d %I:%M %p %Z')}")
print(f"Start in NY:  {event.local_start('America/New_York').strftime('%Y-%m-%d %I:%M %p %Z')}")
print(f"Start in JP:  {event.local_start('Asia/Tokyo').strftime('%Y-%m-%d %I:%M %p %Z')}")

# Serialise
data = event.to_dict()
print(f"\nJSON: {json.dumps(data, indent=2)}")

# Deserialise
restored = Event.from_dict(data)
print(f"\nRestored: {restored}")
print(f"Equal: {event.start_utc == restored.start_utc}")

# Date arithmetic
today = date.today()
print(f"\nToday:     {today.isoformat()}")
print(f"In 30 days: {(today + timedelta(days=30)).isoformat()}")
print(f"Last month: {today.replace(day=1) - timedelta(days=1)}")
```

---

## Key Takeaways

- **`datetime.now(timezone.utc)`** is the correct way to get current UTC time — aware, unambiguous. Never use `datetime.now()` (naive local time) or `datetime.utcnow()` (deprecated, still naive) in production.
- **Naive vs aware**: naive datetime has no `tzinfo`. Aware has `tzinfo`. You cannot compare or subtract a naive and aware datetime — `TypeError` immediately.
- **Store UTC, display local**: always store as UTC aware datetime. Convert to user's local timezone only for display using `astimezone()`.
- **`timezone.utc`** for UTC. **`timezone(timedelta(hours=N))`** for fixed offsets. **`ZoneInfo("Region/City")`** for DST-aware timezones (recommended for real apps).
- **`isoformat()`** → ISO 8601 string with timezone info. **`fromisoformat()`** → parse ISO 8601 back. The `Z` suffix for UTC requires Python 3.11+.
- **`strftime()`** → format datetime to any string. **`strptime()`** → parse any string format to datetime. `strptime` returns naive unless format includes `%z`.
- **`timedelta`** stores days + seconds + microseconds internally. Use `.total_seconds()` to get the full duration as a float.
- **`date` is for calendar dates only** — no time component. `timedelta` arithmetic on `date` returns `date`. Arithmetic on `datetime` returns `datetime`.
- `datetime.fromtimestamp(ts, tz=timezone.utc)` for Unix timestamps — always pass `tz` to get aware datetime.