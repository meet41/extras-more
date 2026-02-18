
# Complete Guide to Threading in Python

## Table of Contents
1. [Introduction to Threading](#introduction-to-threading)
2. [Thread vs Process](#thread-vs-process)
3. [Creating Threads](#creating-threads)
4. [Thread Lifecycle](#thread-lifecycle)
5. [Thread Synchronization](#thread-synchronization)
6. [Thread Communication](#thread-communication)
7. [Thread Pools](#thread-pools)
8. [Daemon Threads](#daemon-threads)
9. [Thread-Local Data](#thread-local-data)
10. [Common Patterns](#common-patterns)
11. [Best Practices](#best-practices)
12. [Common Pitfalls](#common-pitfalls)
13. [Real-World Examples](#real-world-examples)
14. [Performance Considerations](#performance-considerations)
15. [References](#references)

---

## Introduction to Threading

### What is a Thread?

A **thread** is the smallest unit of execution within a process. Multiple threads can exist within a single process, sharing the same memory space but executing independently.

```python
import threading
import os

def show_thread_info():
    """Display information about the current thread"""
    print(f"Thread Name: {threading.current_thread().name}")
    print(f"Thread ID: {threading.get_ident()}")
    print(f"Process ID: {os.getpid()}")
    print(f"Active Threads: {threading.active_count()}")
    print(f"Is Alive: {threading.current_thread().is_alive()}")
    print("-" * 50)

# Main thread
print("=== Main Thread ===")
show_thread_info()

# Create a new thread
def worker():
    print("\n=== Worker Thread ===")
    show_thread_info()

thread = threading.Thread(target=worker, name="WorkerThread")
thread.start()
thread.join()
```

**Output**:
```
=== Main Thread ===
Thread Name: MainThread
Thread ID: 140735268478976
Process ID: 12345
Active Threads: 1
Is Alive: True
--------------------------------------------------

=== Worker Thread ===
Thread Name: WorkerThread
Thread ID: 123145541619712
Process ID: 12345
Active Threads: 2
Is Alive: True
--------------------------------------------------
```

### Key Concepts

- **Concurrency**: Multiple threads make progress in overlapping time periods
- **Shared Memory**: All threads share the same memory space
- **Context Switching**: OS switches between threads
- **GIL (Global Interpreter Lock)**: Python's mechanism that allows only one thread to execute Python bytecode at a time

---

## Thread vs Process

### Comparison

```python
import threading
import multiprocessing
import os
import time

# Thread example
def thread_function(name):
    print(f"Thread {name}: PID={os.getpid()}, ThreadID={threading.get_ident()}")
    time.sleep(1)

# Process example
def process_function(name):
    print(f"Process {name}: PID={os.getpid()}")
    time.sleep(1)

print("=== Threading ===")
print(f"Main Process PID: {os.getpid()}")

threads = []
for i in range(3):
    t = threading.Thread(target=thread_function, args=(i,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print("\n=== Multiprocessing ===")
if __name__ == '__main__':
    processes = []
    for i in range(3):
        p = multiprocessing.Process(target=process_function, args=(i,))
        processes.append(p)
        p.start()
    
    for p in processes:
        p.join()
```

**Output**:
```
=== Threading ===
Main Process PID: 12345
Thread 0: PID=12345, ThreadID=123145368842240
Thread 1: PID=12345, ThreadID=123145374097408
Thread 2: PID=12345, ThreadID=123145379352576

=== Multiprocessing ===
Process 0: PID=12346
Process 1: PID=12347
Process 2: PID=12348
```

### Comparison Table

| Feature | Threading | Multiprocessing |
|---------|-----------|-----------------|
| **Memory** | Shared memory space | Separate memory space |
| **Creation Cost** | Low | High |
| **Communication** | Easy (shared variables) | Harder (IPC required) |
| **GIL Impact** | Limited by GIL | No GIL (separate interpreters) |
| **Use Case** | I/O-bound tasks | CPU-bound tasks |
| **Synchronization** | Locks, Events, etc. | Queue, Pipe, etc. |
| **Debugging** | Easier | More complex |
| **Overhead** | Minimal | Significant |

---

## Creating Threads

### Method 1: Using Thread Class with Function

```python
import threading
import time

def print_numbers(thread_name, count):
    """Print numbers with thread identification"""
    for i in range(count):
        print(f"[{thread_name}] Number: {i}")
        time.sleep(0.5)

# Create and start threads
thread1 = threading.Thread(target=print_numbers, args=("Thread-1", 3))
thread2 = threading.Thread(target=print_numbers, args=("Thread-2", 3))

thread1.start()
thread2.start()

# Wait for both threads to complete
thread1.join()
thread2.join()

print("All threads completed!")
```

**Output**:
```
[Thread-1] Number: 0
[Thread-2] Number: 0
[Thread-1] Number: 1
[Thread-2] Number: 1
[Thread-1] Number: 2
[Thread-2] Number: 2
All threads completed!
```

### Method 2: Subclassing Thread

```python
import threading
import time

class WorkerThread(threading.Thread):
    """Custom thread class"""
    
    def __init__(self, name, count):
        super().__init__()
        self.name = name
        self.count = count
    
    def run(self):
        """This method is called when thread.start() is invoked"""
        print(f"{self.name} starting...")
        for i in range(self.count):
            print(f"[{self.name}] Working on task {i+1}/{self.count}")
            time.sleep(0.5)
        print(f"{self.name} finished!")

# Create threads
thread1 = WorkerThread("Worker-1", 3)
thread2 = WorkerThread("Worker-2", 3)

# Start threads
thread1.start()
thread2.start()

# Wait for completion
thread1.join()
thread2.join()

print("All work completed!")
```

**Output**:
```
Worker-1 starting...
Worker-2 starting...
[Worker-1] Working on task 1/3
[Worker-2] Working on task 1/3
[Worker-1] Working on task 2/3
[Worker-2] Working on task 2/3
[Worker-1] Working on task 3/3
[Worker-2] Working on task 3/3
Worker-1 finished!
Worker-2 finished!
All work completed!
```

### Method 3: Using Lambda

```python
import threading
import time

# Simple lambda function
threads = []
for i in range(3):
    t = threading.Thread(target=lambda n=i: print(f"Thread {n} executing"))
    threads.append(t)
    t.start()

for t in threads:
    t.join()
```

**Output**:
```
Thread 0 executing
Thread 1 executing
Thread 2 executing
```

### Method 4: With Arguments (kwargs)

```python
import threading

def greet(name, age, city):
    """Greet with multiple parameters"""
    print(f"Hello {name}, age {age}, from {city}")

# Using keyword arguments
thread = threading.Thread(
    target=greet,
    kwargs={'name': 'Alice', 'age': 25, 'city': 'NYC'}
)
thread.start()
thread.join()

# Mixed args and kwargs
def display_info(id, name, role='Developer'):
    print(f"ID: {id}, Name: {name}, Role: {role}")

thread = threading.Thread(
    target=display_info,
    args=(101, 'Bob'),
    kwargs={'role': 'Engineer'}
)
thread.start()
thread.join()
```

**Output**:
```
Hello Alice, age 25, from NYC
ID: 101, Name: Bob, Role: Engineer
```

---

## Thread Lifecycle

### States of a Thread

```python
import threading
import time

def worker():
    """Worker function with sleep"""
    print(f"Thread {threading.current_thread().name} is running")
    time.sleep(2)
    print(f"Thread {threading.current_thread().name} is finishing")

# Create thread (NEW state)
thread = threading.Thread(target=worker, name="MyWorker")
print(f"1. Thread created: is_alive() = {thread.is_alive()}")

# Start thread (RUNNABLE/RUNNING state)
thread.start()
print(f"2. Thread started: is_alive() = {thread.is_alive()}")

# Thread is running
time.sleep(0.5)
print(f"3. Thread running: is_alive() = {thread.is_alive()}")

# Wait for thread to complete (TERMINATED state)
thread.join()
print(f"4. Thread finished: is_alive() = {thread.is_alive()}")

# Try to start again (will raise error)
try:
    thread.start()
except RuntimeError as e:
    print(f"5. Cannot restart thread: {e}")
```

**Output**:
```
1. Thread created: is_alive() = False
2. Thread started: is_alive() = True
Thread MyWorker is running
3. Thread running: is_alive() = True
Thread MyWorker is finishing
4. Thread finished: is_alive() = False
5. Cannot restart thread: threads can only be started once
```

### Thread States Diagram

```
NEW → RUNNABLE → RUNNING → BLOCKED → RUNNING → TERMINATED
         ↑          ↓         ↑
         └──────────┴─────────┘
```

### Complete Lifecycle Example

```python
import threading
import time

class LifecycleThread(threading.Thread):
    def __init__(self, name):
        super().__init__(name=name)
        self.states = []
    
    def log_state(self, state):
        """Log thread state with timestamp"""
        self.states.append((time.time(), state))
        print(f"[{self.name}] {state}")
    
    def run(self):
        self.log_state("Started execution")
        
        # Simulate work
        self.log_state("Processing...")
        time.sleep(1)
        
        # Simulate blocking
        self.log_state("Waiting for resource...")
        time.sleep(1)
        
        self.log_state("Finishing...")

# Create and run thread
print("=== Thread Lifecycle ===")
thread = LifecycleThread("LifecycleDemo")

print("State: NEW")
print(f"Is alive: {thread.is_alive()}")

thread.start()
print("\nState: STARTED")
print(f"Is alive: {thread.is_alive()}")

time.sleep(0.5)
print(f"\nState: RUNNING")
print(f"Is alive: {thread.is_alive()}")

thread.join()
print("\nState: TERMINATED")
print(f"Is alive: {thread.is_alive()}")

print("\nState History:")
for timestamp, state in thread.states:
    print(f"  {timestamp:.2f}: {state}")
```

**Output**:
```
=== Thread Lifecycle ===
State: NEW
Is alive: False

State: STARTED
Is alive: True
[LifecycleDemo] Started execution
[LifecycleDemo] Processing...

State: RUNNING
Is alive: True
[LifecycleDemo] Waiting for resource...
[LifecycleDemo] Finishing...

State: TERMINATED
Is alive: False

State History:
  1634567890.12: Started execution
  1634567890.12: Processing...
  1634567891.13: Waiting for resource...
  1634567892.14: Finishing...
```

---

## Thread Synchronization

### 1. Lock (Mutex)

**Purpose**: Ensure only one thread can access a resource at a time.

```python
import threading
import time

# Without Lock - Race Condition
print("=== WITHOUT LOCK (Race Condition) ===")
counter = 0

def increment_without_lock():
    global counter
    for _ in range(100000):
        temp = counter
        temp += 1
        counter = temp

threads = []
for _ in range(5):
    t = threading.Thread(target=increment_without_lock)
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print(f"Final counter (without lock): {counter}")
print(f"Expected: 500000, Got: {counter}")

# With Lock - Thread Safe
print("\n=== WITH LOCK (Thread Safe) ===")
counter = 0
lock = threading.Lock()

def increment_with_lock():
    global counter
    for _ in range(100000):
        with lock:  # or lock.acquire() ... lock.release()
            temp = counter
            temp += 1
            counter = temp

threads = []
for _ in range(5):
    t = threading.Thread(target=increment_with_lock)
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print(f"Final counter (with lock): {counter}")
print(f"Expected: 500000, Got: {counter}")
```

**Output**:
```
=== WITHOUT LOCK (Race Condition) ===
Final counter (without lock): 123456
Expected: 500000, Got: 123456

=== WITH LOCK (Thread Safe) ===
Final counter (with lock): 500000
Expected: 500000, Got: 500000
```

### Lock Methods

```python
import threading
import time

lock = threading.Lock()

# Method 1: with statement (recommended)
def method1():
    with lock:
        print("Method 1: Inside critical section")
        time.sleep(0.1)

# Method 2: acquire/release
def method2():
    lock.acquire()
    try:
        print("Method 2: Inside critical section")
        time.sleep(0.1)
    finally:
        lock.release()

# Method 3: acquire with timeout
def method3():
    if lock.acquire(timeout=1):
        try:
            print("Method 3: Inside critical section")
            time.sleep(0.1)
        finally:
            lock.release()
    else:
        print("Method 3: Could not acquire lock")

# Method 4: non-blocking acquire
def method4():
    if lock.acquire(blocking=False):
        try:
            print("Method 4: Inside critical section")
            time.sleep(0.1)
        finally:
            lock.release()
    else:
        print("Method 4: Lock already held")

# Test all methods
threads = [
    threading.Thread(target=method1),
    threading.Thread(target=method2),
    threading.Thread(target=method3),
    threading.Thread(target=method4),
]

for t in threads:
    t.start()
    time.sleep(0.05)

for t in threads:
    t.join()
```

**Output**:
```
Method 1: Inside critical section
Method 2: Inside critical section
Method 3: Inside critical section
Method 4: Lock already held
```

### 2. RLock (Reentrant Lock)

**Purpose**: Allow the same thread to acquire the lock multiple times.

```python
import threading

# Regular Lock - causes deadlock
print("=== Regular Lock ===")
lock = threading.Lock()

def recursive_function_with_lock(n):
    if n <= 0:
        return
    
    print(f"Acquiring lock at level {n}")
    lock.acquire()
    print(f"Got lock at level {n}")
    
    if n > 1:
        recursive_function_with_lock(n - 1)  # Will deadlock!
    
    lock.release()
    print(f"Released lock at level {n}")

# This will cause deadlock (comment out to continue)
# thread = threading.Thread(target=recursive_function_with_lock, args=(3,))
# thread.start()
# thread.join()

# RLock - works correctly
print("\n=== RLock (Reentrant Lock) ===")
rlock = threading.RLock()

def recursive_function_with_rlock(n):
    if n <= 0:
        return
    
    print(f"Acquiring RLock at level {n}")
    rlock.acquire()
    print(f"Got RLock at level {n}")
    
    if n > 1:
        recursive_function_with_rlock(n - 1)  # Works fine!
    
    rlock.release()
    print(f"Released RLock at level {n}")

thread = threading.Thread(target=recursive_function_with_rlock, args=(3,))
thread.start()
thread.join()
```

**Output**:
```
=== Regular Lock ===

=== RLock (Reentrant Lock) ===
Acquiring RLock at level 3
Got RLock at level 3
Acquiring RLock at level 2
Got RLock at level 2
Acquiring RLock at level 1
Got RLock at level 1
Released RLock at level 1
Released RLock at level 2
Released RLock at level 3
```

### 3. Semaphore

**Purpose**: Limit the number of threads that can access a resource simultaneously.

```python
import threading
import time
import random

# Semaphore with max 3 threads
semaphore = threading.Semaphore(3)

def access_resource(thread_id):
    """Simulate accessing a limited resource"""
    print(f"[Thread-{thread_id}] Waiting for resource...")
    
    with semaphore:
        print(f"[Thread-{thread_id}] ✓ Got access to resource")
        time.sleep(random.uniform(1, 2))  # Use resource
        print(f"[Thread-{thread_id}] ✗ Releasing resource")

# Create 10 threads
threads = []
for i in range(10):
    t = threading.Thread(target=access_resource, args=(i,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print("\nAll threads completed!")
```

**Output**:
```
[Thread-0] Waiting for resource...
[Thread-0] ✓ Got access to resource
[Thread-1] Waiting for resource...
[Thread-1] ✓ Got access to resource
[Thread-2] Waiting for resource...
[Thread-2] ✓ Got access to resource
[Thread-3] Waiting for resource...
[Thread-4] Waiting for resource...
[Thread-5] Waiting for resource...
[Thread-0] ✗ Releasing resource
[Thread-3] ✓ Got access to resource
[Thread-1] ✗ Releasing resource
[Thread-4] ✓ Got access to resource
[Thread-2] ✗ Releasing resource
[Thread-5] ✓ Got access to resource
...
All threads completed!
```

### Real-World Semaphore Example: Database Connection Pool

```python
import threading
import time
import random

class DatabaseConnectionPool:
    """Simulate a database connection pool with limited connections"""
    
    def __init__(self, max_connections=3):
        self.semaphore = threading.Semaphore(max_connections)
        self.connection_count = 0
        self.lock = threading.Lock()
    
    def execute_query(self, query, thread_id):
        """Execute a database query"""
        print(f"[Thread-{thread_id}] Requesting connection for: {query}")
        
        with self.semaphore:
            with self.lock:
                self.connection_count += 1
                active = self.connection_count
            
            print(f"[Thread-{thread_id}] ✓ Got connection (Active: {active})")
            
            # Simulate query execution
            time.sleep(random.uniform(0.5, 1.5))
            
            with self.lock:
                self.connection_count -= 1
            
            print(f"[Thread-{thread_id}] ✗ Released connection")

# Create connection pool
db_pool = DatabaseConnectionPool(max_connections=3)

# Simulate multiple queries
queries = [
    "SELECT * FROM users",
    "INSERT INTO logs VALUES (...)",
    "UPDATE accounts SET balance=...",
    "DELETE FROM temp_data",
    "SELECT COUNT(*) FROM orders",
]

threads = []
for i, query in enumerate(queries):
    t = threading.Thread(target=db_pool.execute_query, args=(query, i))
    threads.append(t)
    t.start()
    time.sleep(0.1)

for t in threads:
    t.join()

print("\nAll queries completed!")
```

**Output**:
```
[Thread-0] Requesting connection for: SELECT * FROM users
[Thread-0] ✓ Got connection (Active: 1)
[Thread-1] Requesting connection for: INSERT INTO logs VALUES (...)
[Thread-1] ✓ Got connection (Active: 2)
[Thread-2] Requesting connection for: UPDATE accounts SET balance=...
[Thread-2] ✓ Got connection (Active: 3)
[Thread-3] Requesting connection for: DELETE FROM temp_data
[Thread-4] Requesting connection for: SELECT COUNT(*) FROM orders
[Thread-0] ✗ Released connection
[Thread-3] ✓ Got connection (Active: 3)
[Thread-1] ✗ Released connection
[Thread-4] ✓ Got connection (Active: 3)
[Thread-2] ✗ Released connection
[Thread-3] ✗ Released connection
[Thread-4] ✗ Released connection

All queries completed!
```

### 4. Event

**Purpose**: Allow threads to wait for a signal/event from another thread.

```python
import threading
import time

# Create event
event = threading.Event()

def waiter(name):
    """Thread that waits for event"""
    print(f"[{name}] Waiting for event...")
    event.wait()  # Block until event is set
    print(f"[{name}] Event received! Continuing...")

def setter():
    """Thread that sets event"""
    print("[Setter] Working on something...")
    time.sleep(3)
    print("[Setter] Setting event!")
    event.set()  # Signal all waiting threads

# Create threads
waiters = [
    threading.Thread(target=waiter, args=("Waiter-1",)),
    threading.Thread(target=waiter, args=("Waiter-2",)),
    threading.Thread(target=waiter, args=("Waiter-3",)),
]
setter_thread = threading.Thread(target=setter)

# Start all threads
for w in waiters:
    w.start()

time.sleep(0.5)  # Let waiters start first
setter_thread.start()

# Wait for completion
for w in waiters:
    w.join()
setter_thread.join()

print("\nAll threads completed!")
```

**Output**:
```
[Waiter-1] Waiting for event...
[Waiter-2] Waiting for event...
[Waiter-3] Waiting for event...
[Setter] Working on something...
[Setter] Setting event!
[Waiter-1] Event received! Continuing...
[Waiter-2] Event received! Continuing...
[Waiter-3] Event received! Continuing...

All threads completed!
```

### Event Methods

```python
import threading
import time

event = threading.Event()

def test_event_methods():
    """Test various event methods"""
    
    # Check if event is set
    print(f"1. Is set: {event.is_set()}")
    
    # Set the event
    event.set()
    print(f"2. After set: {event.is_set()}")
    
    # Clear the event
    event.clear()
    print(f"3. After clear: {event.is_set()}")
    
    # Wait with timeout
    print("4. Waiting with timeout...")
    result = event.wait(timeout=1)
    print(f"   Wait returned: {result} (Fals
