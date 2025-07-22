# Interesting python libraries with samples

[Try this code samples at replit](https://replit.com/@jmarcini/python-libs)

### 1. boltons – Batteries You Didn’t Know Were Missing

You know how the standard library almost has everything, but not quite? Enter boltons.

It’s a collection of pure-Python utilities that cover all the stuff you reach for StackOverflow to figure out — but never bother to extract into your own library.

```python
from boltons.iterutils import chunked

for group in chunked(range(10), 3):
    print(group)
# Output: [0, 1, 2], [3, 4, 5], [6, 7, 8], [9]
```

### 2. tenacity – Retry Until It Works (Or You Rage Quit)

Network request failed? API rate-limited? External dependency throwing a tantrum?

tenacity is the most elegant retrying mechanism I’ve ever seen. Full stop.

```python
from tenacity import retry, stop_after_attempt, wait_fixed

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2))
def fragile_function():
    print("Trying...")
    raise Exception("Oops")

fragile_function()
```

It retries failed functions with customizable backoff logic. And yes, it can handle async functions too.

### 3. diskcache – When You Want Cache but Hate Complexity

Look, I love functools.lru_cache. But sometimes I need my cache to persist across runs, or not disappear the moment my script ends.

diskcache gives you a caching system backed by an actual disk (SQLite under the hood) — no setup required.

```python
import diskcache as dc

cache = dc.Cache('./mycache')

@cache.memoize()
def expensive_calc(x):
    print("Calculating...")
    return x * 42

print(expensive_calc(10))  
print(expensive_calc(10))  
```

No Redis, no Docker, no nonsense. Just local caching that works.

### 4. glom – The Data Access Toolkit You Didn’t Know You Needed

You ever deal with nested JSON from APIs that look like:

```json
{
    "data": {
        "items": [
            {"name": "Alpha"},
            {"name": "Beta"}
        ]
    }
}
```

...and then you have to write five lines of get() just to access "Beta"?

Enter glom.

```python
from glom import glom

data = {
    "data": {
        "items": [{"name": "Alpha"}, {"name": "Beta"}]
    }
}

print(glom(data, 'data.items.1.name'))  
```

Read that again. That’s one line to traverse arbitrarily nested structures.

Why I keep using it: When working with APIs, scraping, or messy configs, this library turns pain into poetry.

### 5. tqdm.contrib.concurrent – Multi-Threaded Progress Bars, Finally

You probably already use tqdm for progress bars. But did you know it comes with an underused concurrent module?

```python
from tqdm.contrib.concurrent import thread_map

def process(item):
    
    return item * 2

results = thread_map(process, range(100), max_workers=10)
```

This runs your function across threads with a built-in progress bar. No need to wire up concurrent.futures manually.

✅ Fast

✅ Thread-safe

✅ Looks cool while running

### 6. anyio – Async Done Right (and Sanely)

Okay, I'll say it: Python's async ecosystem is confusing as hell. asyncio, trio, curio—it's a lot.

**anyio** is a compatibility layer that lets you write async code that works seamlessly across different async frameworks. If you're tired of dealing with event-loop headaches and coroutine nightmares, this one's for you.

Here's a simple example to get you started:

```python
import anyio

async def worker(task_id):
    print(f"Task {task_id} starting")
    await anyio.sleep(1)
    print(f"Task {task_id} done")

async def main():
    async with anyio.create_task_group() as tg:
        for i in range(5):
            tg.start_soon(worker, i)

anyio.run(main)
```

It’s my go-to for structured concurrency and clean task cancellation—without losing my sanity.

🚀 Performance Tip: Pair anyio with httpx and tenacity for a robust, production-ready async stack.

### 7. deepdiff – Detect All the Tiny Differences

Comparing two Python objects should be easy. Until you need to compare dicts with nested lists and subtle changes.

```python
from deepdiff import DeepDiff

d1 = {"a": 1, "b": {"x": 10, "y": 20}}
d2 = {"a": 1, "b": {"x": 15, "y": 20}}

print(DeepDiff(d1, d2))
# {'values_changed': {"root['b']['x']": {'new_value': 15, 'old_value': 10}}}
```

Why I use it: Debugging config changes, testing APIs, or writing unittests where precision matters. It saves hours of manual comparison.

### 8. pyrsistent – Immutable Data Structures That Actually Work

You ever pass a dict to a function and accidentally mutate it? Immutable data structures fix that.

pyrsistent gives you persistent data types: PMap, PVector, etc. — functional, thread-safe, and lightning fast.

```python
from pyrsistent import pmap

original = pmap({'a': 1})
modified = original.set('b', 2)

print(original)  
print(modified)  
```

No weird side effects, no shared state bugs. Just clean, immutable goodness.

It’s inspired by Clojure’s data model and has been used in production systems for years.

### 9. structlog – Logs That Aren’t Garbage

Let’s be honest: most logs are noise. structlog helps you write logs that are structured, parseable, and (finally) useful.

```python
import structlog

log = structlog.get_logger()
log.info("user_logged_in", user_id=42)
```

This outputs logs as dictionaries, so you can pipe them to log aggregators like ELK, Datadog, or just grep them without losing your mind.

Why it matters: Because in 2025, log readability is not optional — it’s critical.

### 10. pyinstrument – Profile First, Optimize Later

Trying to speed up your code? pyinstrument gives you a flamegraph-style profiler with beautiful output and zero setup.

```shell
pyinstrument my_script.py
```

Or use in code:

```python
from pyinstrument import Profiler

profiler = Profiler()
profiler.start()

profiler.stop()
print(profiler.output_text(unicode=True, color=True))
```

It shows you where your time is really going — not just function call counts, but wall-time slices.

Why it’s a game-changer: I found a rogue regex function taking 40% of execution time. In production. This tool paid for itself in 30 seconds.
Thank you for being a part of the community

### 11. typer – Beautiful CLI Apps with Minimal Effort

Building command-line interfaces shouldn't be complicated. With Typer, you turn Python functions into CLI apps effortlessly—powered by type hints and rich automatic documentation.

```python
import typer

def main(name: str, count: int = 1):
    for _ in range(count):
        print(f"Hello, {name}!")

if __name__ == "__main__":
    typer.run(main)
# Usage: python app.py Marcin --count 3
```

---

### 12. pydantic – Data Validation Made Simple

Ever struggled to validate and parse input data cleanly? Pydantic uses Python type annotations to simplify model validation, ensuring data correctness and integrity—no headaches needed.

```python
from pydantic import BaseModel, EmailStr

class User(BaseModel):
    name: str
    email: EmailStr

user = User(name="Marcin", email="marcin@example.com")
print(user.model_dump())
# Output: {'name': 'Marcin', 'email': 'marcin@example.com'}
```

---

### 13. loguru – Next-Level Python Logging

Tired of Python's verbose logging setup? Loguru simplifies logging with intuitive configuration, automatic formatting, and effortless file management. Logging as it should've always been.

```python
from loguru import logger

logger.add("debug.log", level="DEBUG")

logger.info("Hello, Marcin!")
logger.debug("Debugging details here.")
```

---

### 14. rich – Stunning Terminal Formatting and UI

Plain console output getting boring? Rich transforms your terminal apps with beautiful text formatting, progress bars, and advanced visualizations—making your CLI feel alive.

```python
from rich import print
from rich.progress import track
import time

print("[bold magenta]Hello, Marcin![/bold magenta]")

for step in track(range(10), description="Processing..."):
    time.sleep(0.1)
```
