# Copilot Chat Conversation Export: SQL fundamentals and advanced topics

**User:** @meet41
**Thread URL:** https://github.com/copilot/c/bd5f12f6-402d-4a4c-83cb-9c3e45b0f224

## @meet41

tell me about the following topics in details of Python:
OOPs in Python, file handling with (json,csv,txt), threading, multithreading, GIL in python, sync and async io in python, unit testing, pytest. explain about all in detail with code example, when to use, and also demonstrate with problem statement & its solution for each one of topics

## Copilot

## 1) OOP (Object-Oriented Programming) in Python

### What it is
OOP is a way to structure code around **objects** (data + behavior). Core concepts:
- **Class / Object**
- **Encapsulation** (hide internal state via methods/properties)
- **Inheritance** (reuse/extend behavior)
- **Polymorphism** (same interface, different implementations)
- **Composition** (build objects from other objects; often preferred over inheritance)

### When to use
Use OOP when you have:
- A domain with clear “things” (User, Order, Invoice, Device, etc.)
- State + behavior that belong together
- Need extensibility (plug-in style behaviors) or multiple implementations behind a common interface

Avoid overusing OOP for tiny scripts; a few functions may be clearer.

### Code example (OOP with composition + inheritance + polymorphism)
```python name=oop_example.py
from dataclasses import dataclass
from abc import ABC, abstractmethod

@dataclass
class Money:
    amount: float
    currency: str = "USD"

class PaymentMethod(ABC):
    @abstractmethod
    def pay(self, total: Money) -> str:
        raise NotImplementedError

class CardPayment(PaymentMethod):
    def __init__(self, last4: str):
        self._last4 = last4  # "private" by convention

    def pay(self, total: Money) -> str:
        return f"Charged {total.amount:.2f} {total.currency} to card ****{self._last4}"

class WalletPayment(PaymentMethod):
    def __init__(self, wallet_id: str):
        self.wallet_id = wallet_id

    def pay(self, total: Money) -> str:
        return f"Debited {total.amount:.2f} {total.currency} from wallet {self.wallet_id}"

class CheckoutService:
    # composition: CheckoutService "has a" PaymentMethod
    def __init__(self, payment_method: PaymentMethod):
        self.payment_method = payment_method

    def checkout(self, subtotal: Money, tax_rate: float) -> str:
        total = Money(subtotal.amount * (1 + tax_rate), subtotal.currency)
        return self.payment_method.pay(total)

if __name__ == "__main__":
    service = CheckoutService(CardPayment("1234"))
    print(service.checkout(Money(100), tax_rate=0.05))

    service.payment_method = WalletPayment("WALLET-9")
    print(service.checkout(Money(100), tax_rate=0.05))
```

### Problem statement + solution (OOP)
**Problem:** Build a small “notification system” that can send messages via Email or SMS, and can be extended later (e.g., WhatsApp).
- Use a common interface
- Allow switching implementations without changing business logic

```python name=oop_problem_solution.py
from abc import ABC, abstractmethod

class Notifier(ABC):
    @abstractmethod
    def send(self, to: str, message: str) -> None:
        ...

class EmailNotifier(Notifier):
    def send(self, to: str, message: str) -> None:
        print(f"[EMAIL] to={to} msg={message}")

class SMSNotifier(Notifier):
    def send(self, to: str, message: str) -> None:
        print(f"[SMS] to={to} msg={message}")

class AlertService:
    def __init__(self, notifier: Notifier):
        self.notifier = notifier

    def high_cpu_alert(self, user_contact: str, cpu: int) -> None:
        self.notifier.send(user_contact, f"CPU too high: {cpu}%")

if __name__ == "__main__":
    alert = AlertService(EmailNotifier())
    alert.high_cpu_alert("user@example.com", 95)

    alert.notifier = SMSNotifier()
    alert.high_cpu_alert("+91-99999-99999", 95)
```

---

## 2) File Handling (txt, json, csv)

### General concepts
- Use `with open(...) as f:` to ensure the file is closed properly.
- Text mode: `"r"`, `"w"`, `"a"`
- Use encoding explicitly in most apps: `encoding="utf-8"`

---

### 2.1 TXT files
**When to use:** logs, plain text reports, config-like simple files.

```python name=txt_file_example.py
from pathlib import Path

path = Path("notes.txt")

# write
path.write_text("Line1\nLine2\n", encoding="utf-8")

# append
with path.open("a", encoding="utf-8") as f:
    f.write("Line3\n")

# read
content = path.read_text(encoding="utf-8")
print(content)
```

**Problem:** Read a log file and count lines containing `"ERROR"`.
```python name=txt_problem_solution.py
from pathlib import Path

def count_errors(log_path: str) -> int:
    count = 0
    with open(log_path, "r", encoding="utf-8") as f:
        for line in f:
            if "ERROR" in line:
                count += 1
    return count

if __name__ == "__main__":
    Path("app.log").write_text("INFO ok\nERROR fail\nINFO ok\nERROR again\n", encoding="utf-8")
    print(count_errors("app.log"))  # 2
```

---

### 2.2 JSON files
JSON is for structured data (dict/list).

**When to use:** APIs, configs, data interchange, storing structured objects.

Key functions:
- `json.dump(obj, file)` / `json.load(file)`
- `json.dumps(obj)` / `json.loads(str)`

```python name=json_example.py
import json

data = {
    "name": "meet",
    "skills": ["python", "git"],
    "active": True
}

with open("profile.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=2)

with open("profile.json", "r", encoding="utf-8") as f:
    loaded = json.load(f)

print(loaded["skills"])
```

**Problem:** Maintain a simple “todo list” persisted to `todos.json`.
```python name=json_problem_solution.py
import json
from typing import List, Dict

FILE = "todos.json"

def load_todos() -> List[Dict]:
    try:
        with open(FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except FileNotFoundError:
        return []

def save_todos(todos: List[Dict]) -> None:
    with open(FILE, "w", encoding="utf-8") as f:
        json.dump(todos, f, indent=2)

def add_todo(title: str) -> None:
    todos = load_todos()
    todos.append({"title": title, "done": False})
    save_todos(todos)

def mark_done(index: int) -> None:
    todos = load_todos()
    todos[index]["done"] = True
    save_todos(todos)

if __name__ == "__main__":
    add_todo("Learn GIL")
    add_todo("Practice pytest")
    mark_done(0)
    print(load_todos())
```

---

### 2.3 CSV files
CSV is tabular rows/columns.

**When to use:** spreadsheets, exports/imports, simple tabular data.

Use:
- `csv.writer`, `csv.reader`
- `csv.DictWriter`, `csv.DictReader` (often easier)

```python name=csv_example.py
import csv

rows = [
    {"name": "Alice", "score": 90},
    {"name": "Bob", "score": 82},
]

with open("scores.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "score"])
    writer.writeheader()
    writer.writerows(rows)

with open("scores.csv", "r", newline="", encoding="utf-8") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["name"], int(row["score"]))
```

**Problem:** Read `sales.csv` and compute total revenue.
```python name=csv_problem_solution.py
import csv

def total_revenue(path: str) -> float:
    total = 0.0
    with open(path, "r", newline="", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            total += float(row["price"]) * int(row["qty"])
    return total

if __name__ == "__main__":
    # create sample
    with open("sales.csv", "w", newline="", encoding="utf-8") as f:
        w = csv.DictWriter(f, fieldnames=["item", "price", "qty"])
        w.writeheader()
        w.writerow({"item": "pen", "price": "1.5", "qty": "10"})
        w.writerow({"item": "notebook", "price": "3.0", "qty": "5"})

    print(total_revenue("sales.csv"))  # 1.5*10 + 3*5 = 30.0
```

---

## 3) Threading in Python (and why it matters)

### What it is
A **thread** is a lightweight unit of execution within a process sharing memory.

In Python, threads are great for:
- **I/O-bound** work (network calls, waiting for disk, APIs)
- Background tasks (timers, periodic refresh)

Threads are not great for:
- **CPU-bound** workloads (pure computation) because of the **GIL** (more below)

### Basic threading example
```python name=threading_basic.py
import threading
import time

def worker(name: str) -> None:
    for i in range(3):
        print(f"{name} working {i}")
        time.sleep(0.3)

t = threading.Thread(target=worker, args=("T1",), daemon=True)
t.start()
worker("MAIN")
t.join()
```

### Problem statement + solution (threading)
**Problem:** Download 20 URLs faster by doing concurrent I/O waits (simulate with sleep).

```python name=threading_problem_solution.py
import threading
import time
from queue import Queue

def fetch(url: str) -> str:
    # simulate network latency
    time.sleep(0.2)
    return f"content-of-{url}"

def worker(q: Queue, results: dict, lock: threading.Lock) -> None:
    while True:
        url = q.get()
        if url is None:
            q.task_done()
            break
        data = fetch(url)
        with lock:
            results[url] = data
        q.task_done()

if __name__ == "__main__":
    urls = [f"url{i}" for i in range(20)]
    q = Queue()
    results = {}
    lock = threading.Lock()

    threads = [threading.Thread(target=worker, args=(q, results, lock)) for _ in range(5)]
    for t in threads:
        t.start()

    for u in urls:
        q.put(u)

    # stop signals
    for _ in threads:
        q.put(None)

    q.join()
    for t in threads:
        t.join()

    print(len(results), "downloads")
```

---

## 4) Multithreading vs Multiprocessing (important distinction)

### Multithreading
- Many threads, one process, shared memory
- Best for I/O-bound tasks

### Multiprocessing
- Many processes, separate memory, true parallel CPU usage
- Best for CPU-bound tasks

In Python, people often say “multithreading” to mean “using multiple threads”. You can do it, but CPU speedups are limited by the GIL.

### Quick CPU-bound example (use processes)
```python name=multiprocessing_cpu_bound.py
from concurrent.futures import ProcessPoolExecutor
import os

def cpu_heavy(n: int) -> int:
    s = 0
    for i in range(n):
        s += i*i
    return s

if __name__ == "__main__":
    nums = [3_000_00] * 8
    with ProcessPoolExecutor(max_workers=os.cpu_count()) as ex:
        print(list(ex.map(cpu_heavy, nums)))
```

### Problem statement + solution (multi-thread vs multi-process)
**Problem:** You have:
- 100 API calls (I/O bound)
- 8 heavy computations (CPU bound)

**Solution guideline:**
- Use `ThreadPoolExecutor` for API calls
- Use `ProcessPoolExecutor` for computations

```python name=thread_vs_process_solution.py
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor
import time

def api_call(i: int) -> str:
    time.sleep(0.05)
    return f"api-{i}"

def compute(i: int) -> int:
    s = 0
    for k in range(400_000):
        s += (k ^ i) % 7
    return s

if __name__ == "__main__":
    with ThreadPoolExecutor(max_workers=20) as tp:
        api_results = list(tp.map(api_call, range(100)))

    with ProcessPoolExecutor() as pp:
        cpu_results = list(pp.map(compute, range(8)))

    print(len(api_results), len(cpu_results))
```

---

## 5) GIL (Global Interpreter Lock)

### What it is
In **CPython** (the standard Python implementation), the **GIL** allows only **one thread to execute Python bytecode at a time** in a single process.

### Why it exists
Historically:
- Simplifies memory management (reference counting)
- Makes many C extensions easier to write safely

### Practical consequences
- **CPU-bound** Python threads don’t scale across cores well
- **I/O-bound** threads still help because threads release the GIL while waiting on I/O (and many blocking operations release it)

### When to care
- If your multi-threaded code isn’t faster for CPU-heavy tasks: GIL is likely the reason.
- Use **multiprocessing**, **native extensions** (NumPy), or sometimes **PyPy**, or move heavy work to C/C++/Rust.

### Mini demonstration idea
You can compare:
- 1 thread vs 4 threads doing heavy pure-Python loops → often little improvement
- 1 process vs 4 processes → better CPU scaling

(Exact speedup depends on machine + workload.)

---

## 6) Sync vs Async I/O in Python

### Synchronous I/O (blocking)
Code waits until I/O completes. Simple and fine for:
- scripts
- low concurrency

### Asynchronous I/O (non-blocking, cooperative multitasking)
With `asyncio`, tasks yield control at `await` points.
Great for:
- high concurrency I/O servers
- many network requests with limited threads
Not great for:
- CPU-bound loops inside async functions (they block the event loop)

### Sync example (requests one by one, simulated)
```python name=sync_io_example.py
import time

def fetch(i: int) -> str:
    time.sleep(0.1)
    return f"data-{i}"

def main():
    out = [fetch(i) for i in range(20)]
    print(len(out))

if __name__ == "__main__":
    main()
```

### Async example (concurrent)
```python name=async_io_example.py
import asyncio

async def fetch(i: int) -> str:
    await asyncio.sleep(0.1)
    return f"data-{i}"

async def main():
    results = await asyncio.gather(*(fetch(i) for i in range(20)))
    print(len(results))

if __name__ == "__main__":
    asyncio.run(main())
```

### Problem statement + solution (async I/O)
**Problem:** Fetch 200 URLs concurrently but limit concurrency to 20 to avoid overload.

```python name=async_problem_solution.py
import asyncio

async def fetch(url: str, sem: asyncio.Semaphore) -> str:
    async with sem:
        await asyncio.sleep(0.05)  # simulate network
        return f"content({url})"

async def main():
    urls = [f"url{i}" for i in range(200)]
    sem = asyncio.Semaphore(20)
    results = await asyncio.gather(*(fetch(u, sem) for u in urls))
    print("done:", len(results))

if __name__ == "__main__":
    asyncio.run(main())
```

---

## 7) Unit Testing (built-in `unittest`)

### What it is
Unit tests validate small pieces of logic (functions/classes) in isolation.

### When to use
- Anytime you want regression protection
- Refactoring safety
- CI/CD pipelines

### Example with `unittest`
```python name=calc.py
def add(a: int, b: int) -> int:
    return a + b

def div(a: int, b: int) -> float:
    if b == 0:
        raise ValueError("b must not be zero")
    return a / b
```

```python name=test_calc_unittest.py
import unittest
from calc import add, div

class TestCalc(unittest.TestCase):
    def test_add(self):
        self.assertEqual(add(2, 3), 5)

    def test_div_raises(self):
        with self.assertRaises(ValueError):
            div(1, 0)

if __name__ == "__main__":
    unittest.main()
```

### Problem statement + solution (unit testing)
**Problem:** You wrote a function that parses user input and must reject invalid values.

```python name=parse_age.py
def parse_age(s: str) -> int:
    s = s.strip()
    if not s.isdigit():
        raise ValueError("age must be a non-negative integer")
    age = int(s)
    if age > 150:
        raise ValueError("age too large")
    return age
```

```python name=test_parse_age_unittest.py
import unittest
from parse_age import parse_age

class TestParseAge(unittest.TestCase):
    def test_valid(self):
        self.assertEqual(parse_age(" 42 "), 42)

    def test_invalid(self):
        for bad in ["-1", "abc", "12.3", ""]:
            with self.assertRaises(ValueError):
                parse_age(bad)

    def test_too_large(self):
        with self.assertRaises(ValueError):
            parse_age("200")

if __name__ == "__main__":
    unittest.main()
```

---

## 8) pytest

### What it is
`pytest` is a popular testing framework with:
- simpler tests (plain `assert`)
- powerful fixtures
- parametrization
- rich plugin ecosystem

### When to use
- Most modern Python projects choose pytest for readability and productivity
- Great when you need fixtures, parameterized tests, or scalable test suites

### Basic pytest example
```python name=test_calc_pytest.py
import pytest
from calc import add, div

def test_add():
    assert add(2, 3) == 5

def test_div_raises():
    with pytest.raises(ValueError):
        div(1, 0)
```

### Parametrization example
```python name=test_parse_age_pytest.py
import pytest
from parse_age import parse_age

@pytest.mark.parametrize("s,expected", [
    ("0", 0),
    (" 10 ", 10),
    ("150", 150),
])
def test_valid(s, expected):
    assert parse_age(s) == expected

@pytest.mark.parametrize("bad", ["-1", "abc", "12.3", "", "200"])
def test_invalid(bad):
    with pytest.raises(ValueError):
        parse_age(bad)
```

### Fixtures example
```python name=test_fixture_example.py
import pytest

@pytest.fixture
def user():
    return {"name": "meet", "role": "admin"}

def test_user_role(user):
    assert user["role"] == "admin"
```

### Problem statement + solution (pytest)
**Problem:** Test a function that reads a JSON config file. Avoid touching real files.

Solution: use `tmp_path` fixture.
```python name=config_reader.py
import json
from pathlib import Path

def read_config(path: str) -> dict:
    p = Path(path)
    with p.open("r", encoding="utf-8") as f:
        return json.load(f)
```

```python name=test_config_reader_pytest.py
import json
from config_reader import read_config

def test_read_config(tmp_path):
    p = tmp_path / "config.json"
    p.write_text(json.dumps({"debug": True}), encoding="utf-8")

    cfg = read_config(str(p))
    assert cfg["debug"] is True
```

Run with:
- `pytest -q`

---

# If you want, I can tailor these to your level
Tell me:
1) Your Python level (beginner/intermediate/advanced)  
2) Your target use (backend, data, automation, interviews)  
3) Whether you want real HTTP examples (using `httpx`/`aiohttp`) and real threading examples (downloading files) instead of simulated `sleep()`.

I can then provide one “mini-project” per topic (cleaner and more realistic) with the same concepts.

## @meet41

now can you do similar with SQL?

## Copilot

## 1) SQL Fundamentals (DDL, DML, DQL, Constraints)

### What it is
SQL (Structured Query Language) is used to **define**, **store**, **query**, and **modify** data in relational databases.

**Core categories**
- **DDL** (Data Definition Language): `CREATE`, `ALTER`, `DROP`
- **DML** (Data Manipulation Language): `INSERT`, `UPDATE`, `DELETE`
- **DQL** (Data Query Language): `SELECT`
- **DCL/TCL** (permissions/transactions): `GRANT/REVOKE`, `COMMIT/ROLLBACK`

### When to use
- Any time data fits a relational model (entities + relationships) and you need correctness, constraints, joins, reporting.

### Code example (table + constraints + basic CRUD)
```sql name=sql_basics.sql
-- DDL
CREATE TABLE customers (
  customer_id   BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  email         VARCHAR(255) NOT NULL UNIQUE,
  name          VARCHAR(120) NOT NULL,
  created_at    TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
  order_id      BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  customer_id   BIGINT NOT NULL,
  total_amount  DECIMAL(10,2) NOT NULL CHECK (total_amount >= 0),
  status        VARCHAR(20) NOT NULL DEFAULT 'NEW',
  created_at    TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  CONSTRAINT fk_orders_customer
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- DML
INSERT INTO customers(email, name) VALUES ('a@example.com', 'Alice');
INSERT INTO orders(customer_id, total_amount, status) VALUES (1, 25.50, 'PAID');

-- DQL
SELECT * FROM customers;
SELECT * FROM orders WHERE status = 'PAID';

-- Update/Delete
UPDATE orders SET status='REFUNDED' WHERE order_id=1;
DELETE FROM orders WHERE order_id=1;
```

### Problem statement + solution
**Problem:** You need to ensure:
- customer emails are unique
- orders cannot have negative totals
- orders must reference a valid customer

**Solution:** Use `UNIQUE`, `CHECK`, and `FOREIGN KEY` constraints (as above).

---

## 2) SELECT in Depth (WHERE, ORDER BY, LIMIT, DISTINCT)

### When to use
Daily querying and reporting; retrieving subsets of data.

```sql name=select_in_depth.sql
-- Find paid orders over $100, newest first
SELECT DISTINCT order_id, customer_id, total_amount, created_at
FROM orders
WHERE status = 'PAID' AND total_amount > 100
ORDER BY created_at DESC
FETCH FIRST 10 ROWS ONLY; -- Postgres uses LIMIT 10; SQL Server uses TOP 10
```

**Problem:** “Show top 5 customers by total spend in the last 30 days.”
```sql name=top_customers_problem.sql
SELECT
  c.customer_id,
  c.email,
  SUM(o.total_amount) AS total_spend
FROM customers c
JOIN orders o ON o.customer_id = c.customer_id
WHERE o.status = 'PAID'
  AND o.created_at >= CURRENT_TIMESTAMP - INTERVAL '30 days'
GROUP BY c.customer_id, c.email
ORDER BY total_spend DESC
LIMIT 5;
```

---

## 3) Joins (INNER/LEFT/RIGHT/FULL) + Join Pitfalls

### What it is
Joins combine rows from tables based on relationships.

- **INNER JOIN**: only matches
- **LEFT JOIN**: all left rows + matches (NULL if no match)
- **RIGHT JOIN**: symmetric (less used; can swap tables and use LEFT)
- **FULL JOIN**: all rows from both sides

### When to use
Any time you normalized your data into multiple tables (recommended) and need combined results.

```sql name=joins_examples.sql
-- INNER JOIN: customers with at least one order
SELECT c.customer_id, c.email, o.order_id, o.total_amount
FROM customers c
JOIN orders o ON o.customer_id = c.customer_id;

-- LEFT JOIN: include customers even with no orders
SELECT c.customer_id, c.email, o.order_id
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id;
```

### Common pitfall: filtering in WHERE breaks LEFT JOIN
```sql name=left_join_pitfall.sql
-- WRONG if you want customers with zero PAID orders too:
SELECT c.customer_id, c.email, o.order_id
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.status = 'PAID'; -- turns it into INNER behavior

-- Correct: filter in JOIN condition
SELECT c.customer_id, c.email, o.order_id
FROM customers c
LEFT JOIN orders o
  ON o.customer_id = c.customer_id
 AND o.status = 'PAID';
```

### Problem statement + solution
**Problem:** “List all customers and their most recent paid order (if any).”
```sql name=recent_paid_order_solution.sql
SELECT
  c.customer_id,
  c.email,
  o.order_id,
  o.created_at
FROM customers c
LEFT JOIN LATERAL (
  SELECT order_id, created_at
  FROM orders
  WHERE customer_id = c.customer_id AND status='PAID'
  ORDER BY created_at DESC
  LIMIT 1
) o ON TRUE;
```
Note: `LATERAL` is PostgreSQL. For other DBs you can do a window function approach (below).

---

## 4) Aggregations (GROUP BY, HAVING) + Window Functions

### Aggregation
Use `GROUP BY` to summarize data.

```sql name=group_by_example.sql
-- Total paid revenue per day
SELECT
  DATE(created_at) AS day,
  SUM(total_amount) AS revenue
FROM orders
WHERE status='PAID'
GROUP BY DATE(created_at)
ORDER BY day;
```

### HAVING
Filter groups after aggregation.

```sql name=having_example.sql
-- Days with revenue > 1000
SELECT
  DATE(created_at) AS day,
  SUM(total_amount) AS revenue
FROM orders
WHERE status='PAID'
GROUP BY DATE(created_at)
HAVING SUM(total_amount) > 1000
ORDER BY revenue DESC;
```

### Window functions (very important)
They compute results **without collapsing rows**.

Common ones:
- `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`
- `SUM(...) OVER (PARTITION BY ...)`
- `LAG/LEAD`

```sql name=window_function_example.sql
-- Rank orders per customer by newest first
SELECT
  o.*,
  ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY created_at DESC) AS rn
FROM orders o;
```

### Problem statement + solution (window functions)
**Problem:** “For each customer, get their latest paid order.”
```sql name=latest_paid_order_window_solution.sql
WITH ranked AS (
  SELECT
    o.*,
    ROW_NUMBER() OVER (
      PARTITION BY customer_id
      ORDER BY created_at DESC
    ) AS rn
  FROM orders o
  WHERE status='PAID'
)
SELECT *
FROM ranked
WHERE rn = 1;
```

---

## 5) Subqueries + CTEs (WITH)

### What it is
- **Subquery**: query inside a query
- **CTE**: named subquery via `WITH`, improves readability and enables recursion.

### When to use
- Complex pipelines (multiple steps)
- Reuse intermediate results
- Recursive hierarchies (org chart, categories)

```sql name=cte_example.sql
WITH paid_orders AS (
  SELECT * FROM orders WHERE status='PAID'
),
customer_spend AS (
  SELECT customer_id, SUM(total_amount) AS spend
  FROM paid_orders
  GROUP BY customer_id
)
SELECT c.email, cs.spend
FROM customer_spend cs
JOIN customers c ON c.customer_id = cs.customer_id
ORDER BY cs.spend DESC;
```

### Problem statement + solution
**Problem:** “Find customers whose spend is above the average spend.”
```sql name=above_average_spend_solution.sql
WITH spend AS (
  SELECT customer_id, SUM(total_amount) AS total_spend
  FROM orders
  WHERE status='PAID'
  GROUP BY customer_id
),
avg_spend AS (
  SELECT AVG(total_spend) AS avg_total FROM spend
)
SELECT s.customer_id, s.total_spend
FROM spend s
CROSS JOIN avg_spend a
WHERE s.total_spend > a.avg_total
ORDER BY s.total_spend DESC;
```

---

## 6) Indexing & Query Performance (Practical)

### What an index is
An index is a data structure that speeds up lookups/sorts/joins at the cost of:
- extra storage
- slower inserts/updates (because index must be maintained)

### When to use indexes
Index columns that are often used in:
- `WHERE` filters
- `JOIN` keys
- `ORDER BY`
- `GROUP BY` (sometimes)

Avoid indexing low-cardinality columns (like `status`) unless combined in a composite index.

```sql name=index_examples.sql
-- Index for common access pattern:
-- "get paid orders by customer sorted by time"
CREATE INDEX idx_orders_customer_status_created
ON orders (customer_id, status, created_at DESC);

-- If you often search by email:
CREATE INDEX idx_customers_email ON customers (email);
```

### Problem statement + solution
**Problem:** Query is slow:
```sql
SELECT *
FROM orders
WHERE customer_id = 42 AND status='PAID'
ORDER BY created_at DESC
LIMIT 20;
```

**Solution:** Create a composite index matching filter + sort:
```sql name=performance_solution.sql
CREATE INDEX idx_orders_cust_status_created
ON orders (customer_id, status, created_at DESC);
```

---

## 7) Transactions + Isolation + Locks

### What it is
A transaction groups operations into an all-or-nothing unit (ACID).

- **Atomicity**: all or nothing
- **Consistency**: constraints maintained
- **Isolation**: concurrent transactions don’t corrupt results
- **Durability**: committed data persists

### When to use
- Money transfers, inventory updates, multi-step updates that must remain consistent.

### Example: safe transfer (pseudo domain)
```sql name=transaction_example.sql
BEGIN;

-- subtract from source
UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

-- add to destination
UPDATE accounts
SET balance = balance + 100
WHERE account_id = 2;

COMMIT;
-- if anything fails, ROLLBACK;
```

### Problem statement + solution
**Problem:** Prevent overdraft and race conditions in transfers.

**Solution:** lock rows (DB-specific). In PostgreSQL:
```sql name=transaction_lock_solution.sql
BEGIN;

-- Lock both rows to avoid concurrent updates
SELECT account_id, balance
FROM accounts
WHERE account_id IN (1,2)
FOR UPDATE;

-- Validate balance before update (done in app or with checks)
UPDATE accounts SET balance = balance - 100 WHERE account_id=1;
UPDATE accounts SET balance = balance + 100 WHERE account_id=2;

COMMIT;
```

---

## 8) Normalization (1NF/2NF/3NF) + Modeling

### What it is
Normalization reduces redundancy and anomalies.

- **1NF**: no repeating groups; atomic values
- **2NF**: no partial dependency on composite key
- **3NF**: no transitive dependency (non-key depends only on key)

### When to use
- OLTP systems (apps) where updates must be consistent.
Denormalization is sometimes used for analytics/read-heavy performance.

### Problem statement + solution
**Problem:** A table stores `orders` with repeated customer email/name each row → updates inconsistent.

**Solution:** split into `customers` and `orders` with foreign key.

(Already shown in section 1.)

---

## 9) Views + Stored Procedures (Conceptually)

### Views
Saved query as a virtual table. Useful for:
- security (expose limited columns)
- simplifying repeated joins

```sql name=view_example.sql
CREATE VIEW v_paid_orders AS
SELECT * FROM orders WHERE status='PAID';

SELECT * FROM v_paid_orders;
```

### Stored procedures/functions
DB-side logic (varies by DB). Use when:
- you need consistent logic shared by many apps
- heavy data manipulation close to data
But consider maintainability and versioning.

---

## 10) Testing SQL (practical approaches)

SQL testing usually means:
- **data correctness checks** (expected rows, constraints)
- **migration tests**
- **query regression** (same results after changes)

Options:
- In Python: `pytest` + temporary database (Docker/SQLite/Postgres test DB)
- DBT tests (analytics)
- pgTAP (Postgres)

### Problem statement + solution idea
**Problem:** Ensure “paid revenue per day” query matches expected result for a known dataset.

**Solution:** Insert fixture data in a transaction, run query, assert output equals expected.

(If you tell me your DB—Postgres/MySQL/SQLite/SQL Server—I can provide an exact runnable test setup.)

---

# Important question before I go “fully similar” to your Python answer
SQL is not one single runtime like Python—syntax differs across databases.

Which database should I target for the code examples and problem solutions?
1) PostgreSQL  
2) MySQL  
3) SQLite  
4) SQL Server  
5) Oracle  

If you pick one, I can rewrite the examples to be 100% consistent for that DB and add 1 problem + solution per topic (joins, windows, indexing, transactions, etc.) as a cohesive mini-schema (customers/orders/payments).
