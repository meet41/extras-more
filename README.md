# Python Programming Complete Notes

## What is FastAPI?

FastAPI is a modern web framework for building APIs with Python 3.6+ based on standard Python type hints. It allows for the easy creation of RESTful APIs that are fast and can handle many requests efficiently. Here's a breakdown of its key features:

- **Performance**: FastAPI is one of the fastest frameworks for building APIs in Python, as it is built on Starlette for the web parts and Pydantic for the data parts.
- **Ease of Use**: It's designed to be easy to use and learn, providing a simple yet powerful interface.
- **Automatic Documentation**: FastAPI automatically generates interactive API documentation (using Swagger UI and ReDoc) based on the Python type hints you provide.
- **Asynchronous Support**: FastAPI supports asynchronous programming, allowing for concurrent request handling and making it suitable for applications that require high performance.
- **Validation**: With Pydantic, FastAPI provides automatic data validation, ensuring that the incoming request data matches the expected format.
- **Dependency Injection**: FastAPI simplifies dependency management, making your code cleaner and more maintainable.

In summary, FastAPI allows developers to build APIs more efficiently and with less boilerplate code than other frameworks, making it a great choice for web applications and services.

---

## Object-Oriented Programming (OOP) Concepts

### 1. Core OOP Concepts

OOP is based on the concept of "objects", which can contain data in the form of attributes (variables) and code in the form of methods (functions). The core concepts include:

- **Encapsulation**: Bundling the data with the methods that operate on that data.
- **Inheritance**: Mechanism for creating new classes from existing ones, inheriting attributes and methods.
- **Polymorphism**: Ability to present the same interface for different data types.

### 2. Static Method

A static method belongs to a class rather than an instance of a class. It does not have access to the instance (i.e., `self`) or class (i.e., `cls`) variables.

```python
class MathOperations:
    @staticmethod
    def add(x, y):
        return x + y

# Usage
result = MathOperations.add(5, 10)
print(result)  # Output: 15
```

**When to Use**: Use static methods when you need to perform a utility function that doesn't depend on class or instance state.

### 3. Class and Instance Methods

#### Class Method
A method that is bound to the class and not the instance of the class. It can modify class state that applies across all instances.

```python
class Counter:
    count = 0
    
    @classmethod
    def increment(cls):
        cls.count += 1

# Usage
Counter.increment()
print(Counter.count)  # Output: 1
```

#### Instance Method
The most common type of method; it operates on an instance of the class.

```python
class Person:
    def __init__(self, name):
        self.name = name
        
    def greet(self):
        return f"Hello, {self.name}!"

# Usage
person = Person("Alice")
print(person.greet())  # Output: Hello, Alice!
```

**When to Use**: Use instance methods when you need to access or modify the object's attributes. Use class methods when you need to modify class-level attributes or perform operations that relate to the class itself.

### 4. Decorators

Decorators are a way to modify the behavior of a function or class. A common use case is logging, access control, or instrumentation.

```python
def log_function_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_function_call
def add(x, y):
    return x + y

# Usage
print(add(2, 3))
```

**Output**:
```
Calling add
5
```

**When to Use**: Use decorators to enhance or modify function behavior without changing the function's code.

### 5. Method Resolution Order (MRO)

MRO determines the order in which classes are looked up when searching for a method. This is particularly relevant in multiple inheritance scenarios.

```python
class A:
    pass

class B(A):
    pass

class C(A):
    pass

class D(B, C):
    pass

# Usage
print(D.mro())
```

**Output**:
```
[<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
```

**When to Use**: Understand MRO when dealing with multiple inheritances to avoid ambiguity in method calls.

---

## Banking Application Development

### OOP Concepts for Banking Application

1. **Encapsulation**: Use classes to encapsulate properties like accounts, transactions, and customers.
2. **Inheritance**: Create a base class `Account` and extend it to different account types.
3. **Polymorphism**: Implement methods like `calculate_interest` in different account classes.

### Suggested Functionalities

- **User Management**: Registration, login, and profile management
- **Account Management**: Create, view, update, and delete accounts
- **Transaction Processing**: Deposit, withdraw, and transfer funds
- **Loan Management**: Calculate eligibility and process applications
- **Reporting**: Generate statements and transaction history
- **Secure Access**: Implement authentication mechanisms

### Complete Banking Application Code

```python
import json
import os

class InsufficientBalanceError(Exception):
    """Custom exception for handling insufficient balance errors."""
    pass

class InvalidAccountNumberError(Exception):
    """Custom exception for handling invalid account number errors."""
    pass

class BankAccount:
    def __init__(self, account_number, account_holder):
        self.account_number = account_number
        self.account_holder = account_holder
        self.balance = 0

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive.")
        self.balance += amount

    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientBalanceError("Cannot withdraw more than the balance.")
        self.balance -= amount

    def check_balance(self):
        return self.balance

    def to_dict(self):
        return {
            "account_number": self.account_number,
            "account_holder": self.account_holder,
            "balance": self.balance
        }

def save_accounts(accounts):
    with open('accounts.json', 'w') as f:
        json.dump([account.to_dict() for account in accounts], f, indent=2)

def load_accounts():
    try:
        with open('accounts.json', 'r') as f:
            accounts_data = json.load(f)
            loaded_accounts = []
            for data in accounts_data:
                account = BankAccount(data['account_number'], data['account_holder'])
                account.balance = data['balance']
                loaded_accounts.append(account)
            return loaded_accounts
    except FileNotFoundError:
        return []

def main():
    accounts = load_accounts()
    
    while True:
        action = input("Choose action (create, deposit, withdraw, balance, exit): ").strip().lower()
        
        if action == "create":
            acc_number = input("Enter account number: ")
            holder = input("Enter account holder's name: ")
            accounts.append(BankAccount(acc_number, holder))
            print("Account created successfully.")
        
        elif action == "deposit":
            acc_number = input("Enter account number: ")
            amount = float(input("Enter amount to deposit: "))
            for acc in accounts:
                if acc.account_number == acc_number:
                    try:
                        acc.deposit(amount)
                        print(f"Deposited {amount}. New balance: {acc.check_balance()}.")
                    except ValueError as e:
                        print(e)
                    break
            else:
                print("Invalid account number.")
        
        elif action == "withdraw":
            acc_number = input("Enter account number: ")
            amount = float(input("Enter amount to withdraw: "))
            for acc in accounts:
                if acc.account_number == acc_number:
                    try:
                        acc.withdraw(amount)
                        print(f"Withdrew {amount}. New balance: {acc.check_balance()}.")
                    except InsufficientBalanceError as e:
                        print(e)
                    break
            else:
                print("Invalid account number.")

        elif action == "balance":
            acc_number = input("Enter account number: ")
            for acc in accounts:
                if acc.account_number == acc_number:
                    print(f"Balance: {acc.check_balance()}.")
                    break
            else:
                print("Invalid account number.")
        
        elif action == "exit":
            save_accounts(accounts)
            print("Exiting and saving account data.")
            break

main()
```

---

## Exception Handling & Debugging

### 1. try / except / finally

**Concept**: Use `try` to run potential error-throwing code and `except` to handle exceptions gracefully. `finally` executes code regardless of whether an exception occurred.

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero.")
finally:
    print("Execution finished.")
```

**Output**:
```
Cannot divide by zero.
Execution finished.
```

**When to Use**: Use when you need to handle potential errors gracefully and ensure cleanup code runs.

### 2. Multiple Exceptions

**Concept**: Handle different exceptions in a single block.

```python
try:
    a = int(input("Enter a number: "))
    result = 10 / a
except (ValueError, ZeroDivisionError) as e:
    print(f"Error occurred: {e}")
```

**When to Use**: When multiple exception types should be handled the same way.

### 3. Raising Exceptions

**Concept**: Use `raise` to trigger an exception intentionally.

```python
def check_positive(x):
    if x < 0:
        raise ValueError("Must be positive")

try:
    check_positive(-5)
except ValueError as e:
    print(e)
```

**Output**:
```
Must be positive
```

**When to Use**: To enforce business rules and validation in your code.

### 4. Custom Exceptions

**Concept**: Define your own exceptions for specific scenarios.

```python
class MyError(Exception):
    pass

try:
    raise MyError("A custom error occurred.")
except MyError as e:
    print(e)
```

**Output**:
```
A custom error occurred.
```

**When to Use**: When built-in exceptions don't adequately describe your error condition.

### 5. Common Runtime Errors

- **ZeroDivisionError**: Division by zero
- **IndexError**: Accessing invalid list index
- **KeyError**: Accessing invalid dictionary key
- **TypeError**: Operation on incompatible types
- **ValueError**: Function receives correct type but inappropriate value
- **FileNotFoundError**: File doesn't exist

### 6. Debugging Techniques

```python
# Using print statements
def calculate(x, y):
    print(f"Debug: x={x}, y={y}")
    result = x * y
    print(f"Debug: result={result}")
    return result

# Using assert for debugging
def divide(x, y):
    assert y != 0, "Cannot divide by zero"
    return x / y
```

**When to Use**: During development to trace code execution and identify bugs.

### 7. Logging Basics

```python
import logging

# Configure logging
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

logging.debug("This is a debug message.")
logging.info("This is an info message.")
logging.warning("This is a warning message.")
logging.error("This is an error message.")
logging.critical("This is a critical message.")
```

**Output**:
```
2026-02-15 10:30:45 - DEBUG - This is a debug message.
2026-02-15 10:30:45 - INFO - This is an info message.
2026-02-15 10:30:45 - WARNING - This is a warning message.
2026-02-15 10:30:45 - ERROR - This is an error message.
2026-02-15 10:30:45 - CRITICAL - This is a critical message.
```

**When to Use**: In production code instead of print statements for better control and persistence.

---

## File Handling

### 1. Reading/Writing Text Files

```python
# Writing to a file
with open('my_file.txt', 'w') as f:
    f.write("Hello, World!\n")
    f.write("This is line 2.")

# Reading from a file
with open('my_file.txt', 'r') as f:
    content = f.read()
    print(content)
```

**Output**:
```
Hello, World!
This is line 2.
```

**When to Use**: For simple text data storage and retrieval.

### 2. CSV Handling

```python
import csv

# Writing CSV
with open('data.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['Name', 'Age', 'City'])
    writer.writerow(['Alice', '25', 'NYC'])
    writer.writerow(['Bob', '30', 'LA'])

# Reading CSV
with open('data.csv', 'r') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)
```

**Output**:
```
['Name', 'Age', 'City']
['Alice', '25', 'NYC']
['Bob', '30', 'LA']
```

**When to Use**: For tabular data, spreadsheet exports, data exchange.

### 3. JSON File Processing

```python
import json

# Writing JSON
data = {'name': 'Alice', 'age': 25, 'skills': ['Python', 'Java']}
with open('data.json', 'w') as f:
    json.dump(data, f, indent=2)

# Reading JSON
with open('data.json', 'r') as f:
    loaded_data = json.load(f)
    print(loaded_data)
```

**Output**:
```
{'name': 'Alice', 'age': 25, 'skills': ['Python', 'Java']}
```

**When to Use**: For structured data, API responses, configuration files.

### 4. Context Managers (with)

```python
# Without context manager (not recommended)
f = open('file.txt', 'r')
content = f.read()
f.close()

# With context manager (recommended)
with open('file.txt', 'r') as f:
    content = f.read()
# File automatically closed here
```

**When to Use**: Always use context managers for file operations to ensure proper resource cleanup.

### 5. Log File Parsing

```python
import re

def parse_log_file(filename):
    with open(filename, 'r') as f:
        for line in f:
            if 'ERROR' in line:
                timestamp = re.search(r'\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}', line)
                if timestamp:
                    print(f"Error at {timestamp.group()}: {line.strip()}")

# Usage
# parse_log_file('app.log')
```

**When to Use**: For analyzing application logs, monitoring errors, debugging production issues.

---

## Advanced Python Features

### 1. List / Dict / Set Comprehensions

```python
# List comprehension
squares = [x**2 for x in range(10)]
print(squares)

# Dict comprehension
square_dict = {x: x**2 for x in range(5)}
print(square_dict)

# Set comprehension
unique_squares = {x**2 for x in [-2, -1, 0, 1, 2]}
print(unique_squares)

# With condition
even_squares = [x**2 for x in range(10) if x % 2 == 0]
print(even_squares)
```

**Output**:
```
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
{0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
{0, 1, 4}
[0, 4, 16, 36, 64]
```

**When to Use**: For concise creation of lists, dictionaries, or sets from iterables.

### 2. Iterators & Generators

```python
# Iterator
class CountUp:
    def __init__(self, max):
        self.max = max
        self.n = 0
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.n < self.max:
            result = self.n
            self.n += 1
            return result
        else:
            raise StopIteration

for num in CountUp(5):
    print(num)

# Generator
def count_up(max):
    n = 0
    while n < max:
        yield n
        n += 1

for num in count_up(5):
    print(num)
```

**Output** (for both):
```
0
1
2
3
4
```

**When to Use**: Generators for memory-efficient iteration over large datasets.

### 3. Decorators

```python
def timer_decorator(func):
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end-start:.4f} seconds")
        return result
    return wrapper

@timer_decorator
def slow_function():
    import time
    time.sleep(1)
    return "Done"

slow_function()
```

**Output**:
```
slow_function took 1.0001 seconds
```

**When to Use**: For cross-cutting concerns like logging, timing, authentication, caching.

### 4. Closures

```python
def outer_function(msg):
    def inner_function():
        print(msg)
    return inner_function

my_func = outer_function("Hello")
my_func()

# Practical example: Function factory
def multiplier(n):
    def multiply(x):
        return x * n
    return multiply

times_3 = multiplier(3)
times_5 = multiplier(5)

print(times_3(10))
print(times_5(10))
```

**Output**:
```
Hello
30
50
```

**When to Use**: For function factories, maintaining state without classes.

### 5. Shallow vs Deep Copy

```python
import copy

# Shallow copy
original = [[1, 2, 3], [4, 5, 6]]
shallow = copy.copy(original)
shallow[0][0] = 999

print("Original:", original)
print("Shallow:", shallow)

# Deep copy
original2 = [[1, 2, 3], [4, 5, 6]]
deep = copy.deepcopy(original2)
deep[0][0] = 999

print("Original2:", original2)
print("Deep:", deep)
```

**Output**:
```
Original: [[999, 2, 3], [4, 5, 6]]
Shallow: [[999, 2, 3], [4, 5, 6]]
Original2: [[1, 2, 3], [4, 5, 6]]
Deep: [[999, 2, 3], [4, 5, 6]]
```

**When to Use**: Use deep copy when you need completely independent copies of nested structures.

---

## Memory & Performance Basics

### 1. Mutable vs Immutable

```python
# Immutable (strings, tuples, numbers)
x = "hello"
y = x
x = "world"
print(y)  # Still "hello"

# Mutable (lists, dicts, sets)
list1 = [1, 2, 3]
list2 = list1
list1.append(4)
print(list2)  # [1, 2, 3, 4]
```

**Output**:
```
hello
[1, 2, 3, 4]
```

**When to Use**: Understand mutability to avoid unexpected behavior with references.

### 2. Reference vs Copy

```python
# Reference
a = [1, 2, 3]
b = a  # Reference
b.append(4)
print(a)  # [1, 2, 3, 4]

# Copy
a = [1, 2, 3]
b = a.copy()  # or list(a) or a[:]
b.append(4)
print(a)  # [1, 2, 3]
```

**Output**:
```
[1, 2, 3, 4]
[1, 2, 3]
```

**When to Use**: Use copy when you need independent objects.

### 3. Garbage Collection

```python
import gc
import sys

# Check reference count
x = []
print(sys.getrefcount(x))

y = x
print(sys.getrefcount(x))

# Force garbage collection
gc.collect()
```

**Concept**: Python automatically manages memory through reference counting and garbage collection. Objects are deleted when no references exist.

**When to Use**: Usually automatic, but can manually trigger for memory-intensive applications.

---

## Additional Advanced Topics

### 1. Big-O Notation (Intro)

```python
# O(1) - Constant time
def get_first(lst):
    return lst[0]

# O(n) - Linear time
def find_item(lst, target):
    for item in lst:
        if item == target:
            return True
    return False

# O(n²) - Quadratic time
def bubble_sort(lst):
    for i in range(len(lst)):
        for j in range(len(lst) - 1):
            if lst[j] > lst[j + 1]:
                lst[j], lst[j + 1] = lst[j + 1], lst[j]
```

**When to Use**: Understand algorithm efficiency to optimize performance.

### 2. Threading

```python
import threading
import time

def print_numbers():
    for i in range(5):
        print(f"Number: {i}")
        time.sleep(1)

def print_letters():
    for letter in 'ABCDE':
        print(f"Letter: {letter}")
        time.sleep(1)

t1 = threading.Thread(target=print_numbers)
t2 = threading.Thread(target=print_letters)

t1.start()
t2.start()

t1.join()
t2.join()
```

**Output** (interleaved):
```
Number: 0
Letter: A
Number: 1
Letter: B
...
```

**When to Use**: For I/O-bound tasks (file operations, network requests).

### 3. Multiprocessing

```python
from multiprocessing import Process
import os

def worker(name):
    print(f"Worker {name}, PID: {os.getpid()}")

if __name__ == '__main__':
    processes = []
    for i in range(5):
        p = Process(target=worker, args=(i,))
        processes.append(p)
        p.start()
    
    for p in processes:
        p.join()
```

**When to Use**: For CPU-bound tasks (heavy computations, data processing).

### 4. GIL Concept

**Global Interpreter Lock (GIL)**: A mutex that allows only one thread to execute Python bytecode at a time.

- **Threading**: Limited by GIL for CPU-bound tasks
- **Multiprocessing**: Bypasses GIL by using separate processes
- **AsyncIO**: Single-threaded but efficient for I/O operations

### 5. AsyncIO

```python
import asyncio

async def fetch_data(n):
    print(f"Fetching {n}...")
    await asyncio.sleep(2)
    print(f"Done {n}")
    return n

async def main():
    tasks = [fetch_data(i) for i in range(3)]
    results = await asyncio.gather(*tasks)
    print(results)

asyncio.run(main())
```

**Output**:
```
Fetching 0...
Fetching 1...
Fetching 2...
Done 0
Done 1
Done 2
[0, 1, 2]
```

**When to Use**: For I/O-bound concurrent operations (web scraping, API calls).

### 6. Command Line Arguments

```python
# Using sys.argv
import sys

print(f"Script name: {sys.argv[0]}")
print(f"Arguments: {sys.argv[1:]}")

# Using argparse
import argparse

parser = argparse.ArgumentParser(description='Process some integers.')
parser.add_argument('integers', metavar='N', type=int, nargs='+',
                    help='an integer for the accumulator')
parser.add_argument('--sum', dest='accumulate', action='store_const',
                    const=sum, default=max,
                    help='sum the integers (default: find the max)')

args = parser.parse_args()
print(args.accumulate(args.integers))
```

**When to Use**: For creating CLI tools and scripts.

### 7. Unit Testing

```python
# test_calculator.py
import unittest

def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

class TestCalculator(unittest.TestCase):
    def test_add(self):
        self.assertEqual(add(2, 3), 5)
        self.assertEqual(add(-1, 1), 0)
    
    def test_subtract(self):
        self.assertEqual(subtract(5, 3), 2)
        self.assertEqual(subtract(0, 0), 0)

if __name__ == '__main__':
    unittest.main()
```

**Running tests**:
```bash
python test_calculator.py
```

**Output**:
```
..
----------------------------------------------------------------------
Ran 2 tests in 0.001s

OK
```

**When to Use**: For ensuring code correctness, regression testing, CI/CD.

### 8. Code Style & Formatting

#### PEP8 Basics

```python
# Bad
def myFunction(x,y):
    result=x+y
    return result

# Good
def my_function(x, y):
    """Add two numbers together."""
    result = x + y
    return result
```

#### Naming Conventions

```python
# Variables and functions: snake_case
user_name = "Alice"
def calculate_total():
    pass

# Classes: PascalCase
class BankAccount:
    pass

# Constants: UPPER_SNAKE_CASE
MAX_CONNECTIONS = 100
```

#### Function Size

```python
# Too long - hard to understand
def process_data(data):
    # 100 lines of code...
    pass

# Better - split into smaller functions
def validate_data(data):
    pass

def transform_data(data):
    pass

def process_data(data):
    validate_data(data)
    transformed = transform_data(data)
    return transformed
```

**Best Practices**:
- Functions should be < 20-30 lines
- Each function should do one thing
- Use meaningful names
- Add docstrings
- Follow PEP8 style guide

---

## Summary

This comprehensive guide covers:
- Exception handling and debugging techniques
- File handling (text, CSV, JSON)
- Advanced Python features (comprehensions, generators, decorators)
- Memory and performance concepts
- Concurrency (threading, multiprocessing, asyncIO)
- Testing and code quality

Practice these concepts with real projects to solidify your understanding!

# Advanced Python Concepts - Complete Guide

## Table of Contents
1. [Big-O Notation](#big-o-notation)
2. [Concurrency Basics](#concurrency-basics)
3. [Threading](#threading)
4. [Multiprocessing](#multiprocessing)
5. [When to Use What](#when-to-use-what)
6. [GIL Concept](#gil-concept)
7. [Sync vs Async (AsyncIO)](#sync-vs-async-asyncio)
8. [Command Line Arguments](#command-line-arguments)
9. [Unit Testing](#unit-testing)
10. [Code Style & Formatting](#code-style--formatting)

---

## Big-O Notation

### What is Big-O?

Big-O notation describes the performance or complexity of an algorithm, specifically how the runtime or space requirements grow as the input size increases.

### Common Time Complexities

#### 1. O(1) - Constant Time

**Concept**: Operation takes the same time regardless of input size.

```python
def get_first_element(lst):
    """Access first element - always takes same time"""
    return lst[0] if lst else None

# Examples
numbers = [1, 2, 3, 4, 5]
print(get_first_element(numbers))  # Output: 1

large_list = list(range(1000000))
print(get_first_element(large_list))  # Output: 0 (same speed as above)

# Dictionary lookup is also O(1)
user_data = {"name": "Alice", "age": 25}
print(user_data["name"])  # Output: Alice
```

**When to Use**: When you need fast, predictable access regardless of data size.

**Real-World Examples**:
- Array index access
- Dictionary/hash table lookup
- Push/pop from stack

---

#### 2. O(log n) - Logarithmic Time

**Concept**: Runtime grows logarithmically as input size increases. Typically involves dividing the problem in half repeatedly.

```python
def binary_search(sorted_list, target):
    """Search in sorted list by dividing search space in half"""
    left, right = 0, len(sorted_list) - 1
    steps = 0
    
    while left <= right:
        steps += 1
        mid = (left + right) // 2
        print(f"Step {steps}: Checking index {mid}, value {sorted_list[mid]}")
        
        if sorted_list[mid] == target:
            print(f"Found in {steps} steps!")
            return mid
        elif sorted_list[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1

# Example
numbers = [1, 3, 5, 7, 9, 11, 13, 15, 17, 19]
result = binary_search(numbers, 13)
```

**Output**:
```
Step 1: Checking index 5, value 11
Step 2: Checking index 7, value 15
Step 3: Checking index 6, value 13
Found in 3 steps!
```

**When to Use**: For searching in sorted data, tree operations.

**Real-World Examples**:
- Binary search
- Balanced binary search trees
- Finding items in sorted arrays

---

#### 3. O(n) - Linear Time

**Concept**: Runtime grows proportionally with input size.

```python
def find_max(lst):
    """Find maximum by checking each element once"""
    if not lst:
        return None
    
    max_val = lst[0]
    comparisons = 0
    
    for num in lst:
        comparisons += 1
        if num > max_val:
            max_val = num
    
    print(f"Made {comparisons} comparisons")
    return max_val

# Example
numbers = [3, 7, 2, 9, 1, 5, 8]
result = find_max(numbers)
print(f"Maximum: {result}")
```

**Output**:
```
Made 7 comparisons
Maximum: 9
```

**When to Use**: When you must examine every element at least once.

**Real-World Examples**:
- Finding an item in unsorted list
- Calculating sum/average
- Linear search

---

#### 4. O(n log n) - Linearithmic Time

**Concept**: Combination of linear and logarithmic. Common in efficient sorting algorithms.

```python
def merge_sort(arr):
    """Efficient sorting algorithm - O(n log n)"""
    if len(arr) <= 1:
        return arr
    
    # Divide
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    # Conquer (merge)
    return merge(left, right)

def merge(left, right):
    """Merge two sorted arrays"""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# Example
numbers = [64, 34, 25, 12, 22, 11, 90]
print("Original:", numbers)
sorted_numbers = merge_sort(numbers)
print("Sorted:", sorted_numbers)
```

**Output**:
```
Original: [64, 34, 25, 12, 22, 11, 90]
Sorted: [11, 12, 22, 25, 34, 64, 90]
```

**When to Use**: For efficient sorting of large datasets.

**Real-World Examples**:
- Merge sort
- Quick sort (average case)
- Heap sort

---

#### 5. O(n²) - Quadratic Time

**Concept**: Runtime grows quadratically. Often involves nested loops.

```python
def bubble_sort(arr):
    """Simple but inefficient sorting - O(n²)"""
    n = len(arr)
    comparisons = 0
    
    for i in range(n):
        for j in range(0, n - i - 1):
            comparisons += 1
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    
    print(f"Total comparisons: {comparisons}")
    return arr

# Example
numbers = [64, 34, 25, 12, 22]
print("Original:", numbers)
sorted_numbers = bubble_sort(numbers.copy())
print("Sorted:", sorted_numbers)
```

**Output**:
```
Original: [64, 34, 25, 12, 22]
Total comparisons: 10
Sorted: [12, 22, 25, 34, 64]
```

**When to Use**: Avoid for large datasets. Acceptable for small datasets or when simplicity matters.

**Real-World Examples**:
- Bubble sort
- Selection sort
- Nested loop iterations

---

#### 6. O(2ⁿ) - Exponential Time

**Concept**: Runtime doubles with each additional input element. Very slow.

```python
def fibonacci_recursive(n, call_count=[0]):
    """Inefficient recursive Fibonacci - O(2ⁿ)"""
    call_count[0] += 1
    
    if n <= 1:
        return n
    return fibonacci_recursive(n - 1) + fibonacci_recursive(n - 2)

# Example
for i in range(8):
    call_count = [0]
    result = fibonacci_recursive(i, call_count)
    print(f"fib({i}) = {result}, calls: {call_count[0]}")
```

**Output**:
```
fib(0) = 0, calls: 1
fib(1) = 1, calls: 1
fib(2) = 1, calls: 3
fib(3) = 2, calls: 5
fib(4) = 3, calls: 9
fib(5) = 5, calls: 15
fib(6) = 8, calls: 25
fib(7) = 13, calls: 41
```

**When to Use**: Avoid when possible. Use memoization or dynamic programming instead.

---

### Big-O Comparison Chart

```python
import time

def compare_algorithms(n):
    """Compare different time complexities"""
    print(f"\n{'Algorithm':<20} {'Time Complexity':<15} {'Operations for n={n}'}")
    print("-" * 60)
    
    operations = {
        "Constant O(1)": 1,
        "Logarithmic O(log n)": int(n.bit_length()),
        "Linear O(n)": n,
        "Linearithmic O(n log n)": n * int(n.bit_length()),
        "Quadratic O(n²)": n ** 2,
        "Exponential O(2ⁿ)": 2 ** min(n, 20)  # Capped for display
    }
    
    for algo, ops in operations.items():
        print(f"{algo:<20} {ops:>15,}")

# Examples
compare_algorithms(10)
compare_algorithms(100)
compare_algorithms(1000)
```

**Output**:
```
Algorithm            Time Complexity Operations for n=10
------------------------------------------------------------
Constant O(1)                      1
Logarithmic O(log n)               4
Linear O(n)                       10
Linearithmic O(n log n)           40
Quadratic O(n²)                  100
Exponential O(2ⁿ)              1,024

Algorithm            Time Complexity Operations for n=100
------------------------------------------------------------
Constant O(1)                      1
Logarithmic O(log n)               7
Linear O(n)                      100
Linearithmic O(n log n)          700
Quadratic O(n²)               10,000
Exponential O(2ⁿ)  1,267,650,600,228,229,401,496,703,205,376
```

---

## Concurrency Basics

### What is Concurrency?

**Concurrency** is the ability to run multiple tasks in overlapping time periods (not necessarily simultaneously).

**Parallelism** is running multiple tasks literally at the same time (requires multiple CPU cores).

### Key Concepts

```python
import time

# Sequential execution
def sequential_tasks():
    """Tasks run one after another"""
    print("Sequential Execution:")
    start = time.time()
    
    print("Task 1 starting...")
    time.sleep(2)
    print("Task 1 done")
    
    print("Task 2 starting...")
    time.sleep(2)
    print("Task 2 done")
    
    print(f"Total time: {time.time() - start:.2f} seconds\n")

sequential_tasks()
```

**Output**:
```
Sequential Execution:
Task 1 starting...
Task 1 done
Task 2 starting...
Task 2 done
Total time: 4.00 seconds
```

### Types of Concurrency

1. **Threading**: Multiple threads in one process (shared memory)
2. **Multiprocessing**: Multiple processes (separate memory)
3. **Async/Await**: Cooperative multitasking (single thread)

---

## Threading

### What is Threading?

Threading allows multiple threads to exist within one process, sharing the same memory space. Ideal for I/O-bound tasks.

### Basic Threading Example

```python
import threading
import time

def download_file(file_name, duration):
    """Simulate file download"""
    print(f"[{threading.current_thread().name}] Downloading {file_name}...")
    time.sleep(duration)
    print(f"[{threading.current_thread().name}] {file_name} downloaded!")

# Sequential approach
print("=== Sequential Download ===")
start = time.time()
download_file("file1.pdf", 2)
download_file("file2.pdf", 2)
download_file("file3.pdf", 2)
print(f"Sequential time: {time.time() - start:.2f} seconds\n")

# Threading approach
print("=== Threaded Download ===")
start = time.time()

threads = []
files = [("file1.pdf", 2), ("file2.pdf", 2), ("file3.pdf", 2)]

for file_name, duration in files:
    thread = threading.Thread(target=download_file, args=(file_name, duration))
    threads.append(thread)
    thread.start()

# Wait for all threads to complete
for thread in threads:
    thread.join()

print(f"Threaded time: {time.time() - start:.2f} seconds")
```

**Output**:
```
=== Sequential Download ===
[MainThread] Downloading file1.pdf...
[MainThread] file1.pdf downloaded!
[MainThread] Downloading file2.pdf...
[MainThread] file2.pdf downloaded!
[MainThread] Downloading file3.pdf...
[MainThread] file3.pdf downloaded!
Sequential time: 6.00 seconds

=== Threaded Download ===
[Thread-1] Downloading file1.pdf...
[Thread-2] Downloading file2.pdf...
[Thread-3] Downloading file3.pdf...
[Thread-1] file1.pdf downloaded!
[Thread-2] file2.pdf downloaded!
[Thread-3] file3.pdf downloaded!
Threaded time: 2.01 seconds
```

### Thread with Return Values

```python
import threading
import time

def fetch_data(url, results, index):
    """Fetch data and store in shared list"""
    print(f"Fetching {url}...")
    time.sleep(2)  # Simulate network delay
    data = f"Data from {url}"
    results[index] = data
    print(f"Completed {url}")

# Using shared list for results
results = [None] * 3
threads = []
urls = ["api.example.com/users", "api.example.com/posts", "api.example.com/comments"]

start = time.time()

for i, url in enumerate(urls):
    thread = threading.Thread(target=fetch_data, args=(url, results, i))
    threads.append(thread)
    thread.start()

for thread in threads:
    thread.join()

print("\n=== Results ===")
for i, result in enumerate(results):
    print(f"{i+1}. {result}")

print(f"\nTotal time: {time.time() - start:.2f} seconds")
```

**Output**:
```
Fetching api.example.com/users...
Fetching api.example.com/posts...
Fetching api.example.com/comments...
Completed api.example.com/users
Completed api.example.com/posts
Completed api.example.com/comments

=== Results ===
1. Data from api.example.com/users
2. Data from api.example.com/posts
3. Data from api.example.com/comments

Total time: 2.01 seconds
```

### Thread Synchronization (Lock)

```python
import threading
import time

# Without lock - race condition
class BankAccount:
    def __init__(self, balance):
        self.balance = balance
    
    def withdraw(self, amount):
        print(f"[{threading.current_thread().name}] Attempting to withdraw ${amount}")
        if self.balance >= amount:
            time.sleep(0.1)  # Simulate processing
            self.balance -= amount
            print(f"[{threading.current_thread().name}] Withdrew ${amount}, Balance: ${self.balance}")
        else:
            print(f"[{threading.current_thread().name}] Insufficient funds")

# With lock - thread-safe
class SafeBankAccount:
    def __init__(self, balance):
        self.balance = balance
        self.lock = threading.Lock()
    
    def withdraw(self, amount):
        with self.lock:  # Only one thread can execute this at a time
            print(f"[{threading.current_thread().name}] Attempting to withdraw ${amount}")
            if self.balance >= amount:
                time.sleep(0.1)
                self.balance -= amount
                print(f"[{threading.current_thread().name}] Withdrew ${amount}, Balance: ${self.balance}")
            else:
                print(f"[{threading.current_thread().name}] Insufficient funds")

# Test without lock
print("=== Without Lock (Race Condition) ===")
account1 = BankAccount(100)
threads = []
for i in range(3):
    t = threading.Thread(target=account1.withdraw, args=(50,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print(f"\nFinal balance: ${account1.balance} (Should be 100 or 50, but may be negative!)\n")

# Test with lock
print("=== With Lock (Thread-Safe) ===")
account2 = SafeBankAccount(100)
threads = []
for i in range(3):
    t = threading.Thread(target=account2.withdraw, args=(50,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print(f"\nFinal balance: ${account2.balance} (Correctly handled)")
```

**Output**:
```
=== Without Lock (Race Condition) ===
[Thread-1] Attempting to withdraw $50
[Thread-2] Attempting to withdraw $50
[Thread-3] Attempting to withdraw $50
[Thread-1] Withdrew $50, Balance: $0
[Thread-2] Withdrew $50, Balance: $-50
[Thread-3] Withdrew $50, Balance: $-100

Final balance: $-100 (Should be 100 or 50, but may be negative!)

=== With Lock (Thread-Safe) ===
[Thread-4] Attempting to withdraw $50
[Thread-4] Withdrew $50, Balance: $50
[Thread-5] Attempting to withdraw $50
[Thread-5] Withdrew $50, Balance: $0
[Thread-6] Attempting to withdraw $50
[Thread-6] Insufficient funds

Final balance: $0 (Correctly handled)
```

**When to Use Threading**:
- ✅ I/O-bound tasks (file operations, network requests, database queries)
- ✅ When tasks spend time waiting (downloads, API calls)
- ✅ GUI applications (keep UI responsive)
- ❌ CPU-bound tasks (calculations) - use multiprocessing instead

---

## Multiprocessing

### What is Multiprocessing?

Multiprocessing creates separate processes, each with its own Python interpreter and memory space. Ideal for CPU-bound tasks.

### Basic Multiprocessing Example

```python
import multiprocessing
import time
import os

def cpu_intensive_task(n, name):
    """Simulate CPU-intensive work"""
    print(f"[Process {name}] PID: {os.getpid()} - Starting task")
    
    # Calculate sum of squares
    result = sum(i * i for i in range(n))
    
    print(f"[Process {name}] PID: {os.getpid()} - Completed")
    return result

if __name__ == '__main__':
    # Sequential approach
    print("=== Sequential Processing ===")
    start = time.time()
    results = []
    for i in range(4):
        result = cpu_intensive_task(10000000, f"Seq-{i}")
        results.append(result)
    print(f"Sequential time: {time.time() - start:.2f} seconds\n")
    
    # Multiprocessing approach
    print("=== Multiprocessing ===")
    start = time.time()
    
    with multiprocessing.Pool(processes=4) as pool:
        # Create 4 processes
        results = []
        for i in range(4):
            result = pool.apply_async(cpu_intensive_task, args=(10000000, f"MP-{i}"))
            results.append(result)
        
        # Get results
        final_results = [r.get() for r in results]
    
    print(f"Multiprocessing time: {time.time() - start:.2f} seconds")
```

**Output** (on 4-core machine):
```
=== Sequential Processing ===
[Process Seq-0] PID: 12345 - Starting task
[Process Seq-0] PID: 12345 - Completed
[Process Seq-1] PID: 12345 - Starting task
[Process Seq-1] PID: 12345 - Completed
[Process Seq-2] PID: 12345 - Starting task
[Process Seq-2] PID: 12345 - Completed
[Process Seq-3] PID: 12345 - Starting task
[Process Seq-3] PID: 12345 - Completed
Sequential time: 8.45 seconds

=== Multiprocessing ===
[Process MP-0] PID: 12346 - Starting task
[Process MP-1] PID: 12347 - Starting task
[Process MP-2] PID: 12348 - Starting task
[Process MP-3] PID: 12349 - Starting task
[Process MP-0] PID: 12346 - Completed
[Process MP-1] PID: 12347 - Completed
[Process MP-2] PID: 12348 - Completed
[Process MP-3] PID: 12349 - Completed
Multiprocessing time: 2.34 seconds
```

### Process Pool with Map

```python
import multiprocessing
import time

def square(n):
    """Square a number (CPU-intensive for large numbers)"""
    return n * n

def cube(n):
    """Cube a number"""
    return n * n * n

if __name__ == '__main__':
    numbers = list(range(1, 11))
    
    # Using pool.map
    print("=== Using pool.map ===")
    start = time.time()
    
    with multiprocessing.Pool() as pool:
        squared = pool.map(square, numbers)
        cubed = pool.map(cube, numbers)
    
    print(f"Numbers:  {numbers}")
    print(f"Squared:  {squared}")
    print(f"Cubed:    {cubed}")
    print(f"Time: {time.time() - start:.2f} seconds")
```

**Output**:
```
=== Using pool.map ===
Numbers:  [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
Squared:  [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
Cubed:    [1, 8, 27, 64, 125, 216, 343, 512, 729, 1000]
Time: 0.15 seconds
```

### Sharing Data Between Processes

```python
import multiprocessing
import time

def worker(shared_value, shared_array, lock, process_num):
    """Modify shared data safely"""
    with lock:
        print(f"Process {process_num}: Current value = {shared_value.value}")
        shared_value.value += 1
        shared_array[process_num] = process_num * 10
        time.sleep(0.1)

if __name__ == '__main__':
    # Shared memory
    shared_value = multiprocessing.Value('i', 0)  # 'i' = integer
    shared_array = multiprocessing.Array('i', [0, 0, 0, 0])  # array of 4 integers
    lock = multiprocessing.Lock()
    
    # Create processes
    processes = []
    for i in range(4):
        p = multiprocessing.Process(target=worker, args=(shared_value, shared_array, lock, i))
        processes.append(p)
        p.start()
    
    # Wait for completion
    for p in processes:
        p.join()
    
    print(f"\nFinal shared value: {shared_value.value}")
    print(f"Final shared array: {list(shared_array)}")
```

**Output**:
```
Process 0: Current value = 0
Process 1: Current value = 1
Process 2: Current value = 2
Process 3: Current value = 3

Final shared value: 4
Final shared array: [0, 10, 20, 30]
```

**When to Use Multiprocessing**:
- ✅ CPU-bound tasks (mathematical computations, data processing)
- ✅ When you need true parallelism
- ✅ When tasks are independent
- ❌ I/O-bound tasks (use threading or async instead)
- ❌ When sharing large amounts of data (expensive to serialize)

---

## When to Use What

### Decision Tree

```python
"""
Is your task I/O-bound or CPU-bound?

├─ I/O-Bound (waiting for network, files, database)
│  │
│  ├─ Many concurrent connections? → AsyncIO
│  ├─ Simple concurrent tasks? → Threading
│  └─ Need backward compatibility? → Threading
│
└─ CPU-Bound (calculations, data processing)
   │
   ├─ Independent tasks? → Multiprocessing
   ├─ Need shared state? → Multiprocessing with shared memory
   └─ Small dataset? → May not need concurrency
"""
```

### Comparison Table

| Feature | Threading | Multiprocessing | AsyncIO |
|---------|-----------|----------------|---------|
| **Use Case** | I/O-bound | CPU-bound | I/O-bound (many connections) |
| **Concurrency** | Concurrent | Parallel | Concurrent |
| **Memory** | Shared | Separate | Shared |
| **Overhead** | Low | High | Very Low |
| **GIL Impact** | Limited by GIL | Bypasses GIL | Single-threaded (no GIL issue) |
| **Complexity** | Medium | High | Medium-High |
| **Best For** | File I/O, Network | Heavy computation | Web servers, APIs |

### Practical Example Comparison

```python
import threading
import multiprocessing
import asyncio
import time
import requests

# Simulated tasks
def io_bound_task(n):
    """Simulate I/O operation (network request)"""
    time.sleep(1)  # Simulate waiting
    return f"Result {n}"

def cpu_bound_task(n):
    """Simulate CPU-intensive operation"""
    return sum(i * i for i in range(10000000))

# 1. Threading for I/O-bound
def test_threading():
    print("=== Threading (I/O-bound) ===")
    start = time.time()
    
    threads = []
    for i in range(5):
        t = threading.Thread(target=io_bound_task, args=(i,))
        threads.append(t)
        t.start()
    
    for t in threads:
        t.join()
    
    print(f"Time: {time.time() - start:.2f} seconds\n")

# 2. Multiprocessing for CPU-bound
def test_multiprocessing():
    print("=== Multiprocessing (CPU-bound) ===")
    start = time.time()
    
    with multiprocessing.Pool(processes=4) as pool:
        results = pool.map(cpu_bound_task, range(4))
    
    print(f"Time: {time.time() - start:.2f} seconds\n")

# 3. AsyncIO for I/O-bound
async def async_io_task(n):
    await asyncio.sleep(1)
    return f"Result {n}"

async def test_asyncio():
    print("=== AsyncIO (I/O-bound) ===")
    start = time.time()
    
    tasks = [async_io_task(i) for i in range(5)]
    results = await asyncio.gather(*tasks)
    
    print(f"Time: {time.time() - start:.2f} seconds\n")

if __name__ == '__main__':
    test_threading()
    test_multiprocessing()
    asyncio.run(test_asyncio())
```

**Output**:
```
=== Threading (I/O-bound) ===
Time: 1.01 seconds

=== Multiprocessing (CPU-bound) ===
Time: 1.87 seconds

=== AsyncIO (I/O-bound) ===
Time: 1.00 seconds
```

---

## GIL Concept

### What is the GIL?

**Global Interpreter Lock (GIL)** is a mutex that protects access to Python objects, preventing multiple threads from executing Python bytecode simultaneously.

### Why GIL Exists

- Simplifies memory management
- Makes C extensions easier to write
- Protects reference counting

### GIL Impact Demonstration

```python
import threading
import multiprocessing
import time

def countdown(n):
    """CPU-intensive countdown"""
    while n > 0:
        n -= 1

# Single-threaded baseline
print("=== Single Thread (Baseline) ===")
start = time.time()
countdown(50000000)
print(f"Time: {time.time() - start:.2f} seconds\n")

# Multi-threaded (LIMITED BY GIL)
print("=== Two Threads (Limited by GIL) ===")
start = time.time()

t1 = threading.Thread(target=countdown, args=(25000000,))
t2 = threading.Thread(target=countdown, args=(25000000,))

t1.start()
t2.start()
t1.join()
t2.join()

print(f"Time: {time.time() - start:.2f} seconds")
print("Notice: Not much faster due to GIL!\n")

# Multi-processing (BYPASSES GIL)
if __name__ == '__main__':
    print("=== Two Processes (Bypasses GIL) ===")
    start = time.time()
    
    p1 = multiprocessing.Process(target=countdown, args=(25000000,))
    p2 = multiprocessing.Process(target=countdown, args=(25000000,))
    
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    
    print(f"Time: {time.time() - start:.2f} seconds")
    print("Much faster - true parallelism!")
```

**Output** (on dual-core machine):
```
=== Single Thread (Baseline) ===
Time: 2.50 seconds

=== Two Threads (Limited by GIL) ===
Time: 2.48 seconds
Notice: Not much faster due to GIL!

=== Two Processes (Bypasses GIL) ===
Time: 1.30 seconds
Much faster - true parallelism!
```

### GIL Summary

```python
"""
GIL Impact:

✅ Threading WORKS for:
   - I/O operations (file, network, database)
   - Operations that release GIL (NumPy, C extensions)
   - Sleeping/waiting

❌ Threading DOESN'T WORK for:
   - Pure Python CPU-intensive tasks
   - Tight loops in Python
   - Mathematical computations in Python

✅ Solutions:
   - Use multiprocessing for CPU-bound tasks
   - Use async for I/O-bound tasks
   - Use C extensions that release GIL
   - Consider PyPy or other Python implementations
"""
```

---

## Sync vs Async (AsyncIO)

### What is AsyncIO?

AsyncIO is a library for writing concurrent code using the async/await syntax. It's single-threaded but can handle many I/O operations concurrently.

### Synchronous vs Asynchronous

```python
import time
import asyncio

# Synchronous version
def sync_fetch_data(url):
    """Synchronous - blocks while waiting"""
    print(f"Fetching {url}...")
    time.sleep(2)  # Simulate network delay
    print(f"Done {url}")
    return f"Data from {url}"

print("=== Synchronous ===")
start = time.time()
result1 = sync_fetch_data("api.example.com/users")
result2 = sync_fetch_data("api.example.com/posts")
result3 = sync_fetch_data("api.example.com/comments")
print(f"Total time: {time.time() - start:.2f} seconds\n")

# Asynchronous version
async def async_fetch_data(url):
    """Asynchronous - doesn't block"""
    print(f"Fetching {url}...")
    await asyncio.sleep(2)  # Simulate network delay (non-blocking)
    print(f"Done {url}")
    return f"Data from {url}"

async def main():
    print("=== Asynchronous ===")
    start = time.time()
    
    # Run concurrently
    results = await asyncio.gather(
        async_fetch_data("api.example.com/users"),
        async_fetch_data("api.example.com/posts"),
        async_fetch_data("api.example.com/comments")
    )
    
    print(f"Total time: {time.time() - start:.2f} seconds")
    print(f"Results: {results}")

asyncio.run(main())
```

**Output**:
```
=== Synchronous ===
Fetching api.example.com/users...
Done api.example.com/users
Fetching api.example.com/posts...
Done api.example.com/posts
Fetching api.example.com/comments...
Done api.example.com/comments
Total time: 6.00 seconds

=== Asynchronous ===
Fetching api.example.com/users...
Fetching api.example.com/posts...
Fetching api.example.com/comments...
Done api.example.com/users
Done api.example.com/posts
Done api.example.com/comments
Total time: 2.00 seconds
Results: ['Data from api.example.com/users', 'Data from api.example.com/posts', 'Data from api.example.com/comments']
```

### AsyncIO Patterns

#### 1. Basic Async Function

```python
import asyncio

async def greet(name, delay):
    """Simple async function"""
    await asyncio.sleep(delay)
    return f"Hello, {name}!"

async def main():
    # Sequential
    result1 = await greet("Alice", 1)
    result2 = await greet("Bob", 1)
    print(result1, result2)
    
    # Concurrent
    results = await asyncio.gather(
        greet("Alice", 1),
        greet("Bob", 1)
    )
    print(results)

asyncio.run(main())
```

**Output**:
```
Hello, Alice! Hello, Bob!
['Hello, Alice!', 'Hello, Bob!']
```

#### 2. Async with Error Handling

```python
import asyncio

async def risky_operation(n):
    """Operation that might fail"""
    await asyncio.sleep(1)
    if n == 3:
        raise ValueError(f"Failed on {n}")
    return n * 2

async def main():
    tasks = [risky_operation(i) for i in range(5)]
    
    # Continue even if some fail
    results = await asyncio.gather(*tasks, return_exceptions=True)
    
    for i, result in enumerate(results):
        if isinstance(result, Exception):
            print(f"Task {i} failed: {result}")
        else:
            print(f"Task {i} succeeded: {result}")

asyncio.run(main())
```

**Output**:
```
Task 0 succeeded: 0
Task 1 succeeded: 2
Task 2 succeeded: 4
Task 3 failed: Failed on 3
Task 4 succeeded: 8
```

#### 3. Async with Timeout

```python
import asyncio

async def slow_operation():
    """Operation that takes too long"""
    print("Starting slow operation...")
    await asyncio.sleep(5)
    return "Done"

async def main():
    try:
        # Set timeout to 2 seconds
        result = await asyncio.wait_for(slow_operation(), timeout=2.0)
        print(result)
    except asyncio.TimeoutError:
        print("Operation timed out!")

asyncio.run(main())
```

**Output**:
```
Starting slow operation...
Operation timed out!
```

#### 4. Real-World Example: Web Scraping

```python
import asyncio
import aiohttp  # pip install aiohttp

async def fetch_url(session, url):
    """Fetch URL asynchronously"""
    print(f"Fetching {url}...")
    async with session.get(url) as response:
        data = await response.text()
        print(f"Got {url}: {len(data)} bytes")
        return url, len(data)

async def fetch_all(urls):
    """Fetch multiple URLs concurrently"""
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
        return results

# Example usage
urls = [
    "https://example.com",
    "https://python.org",
    "https://github.com"
]

# asyncio.run(fetch_all(urls))
```

**When to Use AsyncIO**:
- ✅ Web scraping (many HTTP requests)
- ✅ Web servers (handling many connections)
- ✅ Database queries (many concurrent queries)
- ✅ I/O-bound tasks with many operations
- ❌ CPU-bound tasks
- ❌ When libraries don't support async

---

## Command Line Arguments

### 1. Using sys.argv

**Concept**: `sys.argv` is a list containing command-line arguments.

```python
# script.py
import sys

print("Script name:", sys.argv[0])
print("Number of arguments:", len(sys.argv))
print("Arguments:", sys.argv[1:])

# Access specific arguments
if len(sys.argv) > 1:
    name = sys.argv[1]
    print(f"Hello, {name}!")
else:
    print("Hello, World!")

# Example with multiple arguments
if len(sys.argv) == 4:
    operation = sys.argv[1]
    num1 = float(sys.argv[2])
    num2 = float(sys.argv[3])
    
    if operation == "add":
        result = num1 + num2
    elif operation == "multiply":
        result = num1 * num2
    else:
        result = "Unknown operation"
    
    print(f"Result: {result}")
```

**Usage**:
```bash
python script.py Alice
python script.py add 5 3
python script.py multiply 4 7
```

**Output**:
```
Script name: script.py
Number of arguments: 2
Arguments: ['Alice']
Hello, Alice!

Script name: script.py
Number of arguments: 4
Arguments: ['add', '5', '3']
Result: 8.0

Script name: script.py
Number of arguments: 4
Arguments: ['multiply', '4', '7']
Result: 28.0
```

**When to Use sys.argv**:
- ✅ Very simple scripts
- ✅ Quick prototypes
- ✅ When you control all inputs
- ❌ Complex arguments
- ❌ Need help messages
- ❌ Need validation

---

### 2. Using argparse

**Concept**: `argparse` is a powerful module for parsing command-line arguments with automatic help, validation, and type conversion.

#### Basic argparse Example

```python
# calculator.py
import argparse

# Create parser
parser = argparse.ArgumentParser(
    description='Simple calculator',
    epilog='Example: python calculator.py add 5 3'
)

# Add arguments
parser.add_argument('operation', 
                   choices=['add', 'subtract', 'multiply', 'divide'],
                   help='Operation to perform')
parser.add_argument('num1', type=float, help='First number')
parser.add_argument('num2', type=float, help='Second number')
parser.add_argument('--verbose', '-v', action='store_true', 
                   help='Enable verbose output')

# Parse arguments
args = parser.parse_args()

# Perform operation
if args.verbose:
    print(f"Performing {args.operation} on {args.num1} and {args.num2}")

if args.operation == 'add':
    result = args.num1 + args.num2
elif args.operation == 'subtract':
    result = args.num1 - args.num2
elif args.operation == 'multiply':
    result = args.num1 * args.num2
elif args.operation == 'divide':
    if args.num2 == 0:
        print("Error: Division by zero!")
        exit(1)
    result = args.num1 / args.num2

print(f"Result: {result}")
```

**Usage**:
```bash
python calculator.py add 10 5
python calculator.py multiply 3 7 --verbose
python calculator.py --help
```

**Output**:
```
Result: 15.0

Performing multiply on 3.0 and 7.0
Result: 21.0

usage: calculator.py [-h] [--verbose] {add,subtract,multiply,divide} num1 num2

Simple calculator

positional arguments:
  {add,subtract,multiply,divide}
                        Operation to perform
  num1                  First number
  num2                  Second number

optional arguments:
  -h, --help            show this help message and exit
  --verbose, -v         Enable verbose output

Example: python calculator.py add 5 3
```

#### Advanced argparse Example

```python
# file_processor.py
import argparse

parser = argparse.ArgumentParser(description='Process files')

# Positional arguments
parser.add_argument('input_file', help='Input file path')
parser.add_argument('output_file', nargs='?', default='output.txt',
                   help='Output file path (default: output.txt)')

# Optional arguments
parser.add_argument('--format', '-f', choices=['json', 'csv', 'xml'],
                   default='json', help='Output format')
parser.add_argument('--verbose', '-v', action='store_true',
                   help='Verbose output')
parser.add_argument('--count', '-c', type=int, default=10,
                   help='Number of records to process')
parser.add_argument('--config', type=argparse.FileType('r'),
                   help='Configuration file')

# Mutually exclusive group
group = parser.add_mutually_exclusive_group()
group.add_argument('--compress', action='store_true',
                  help='Compress output')
group.add_argument('--encrypt', action='store_true',
                  help='Encrypt output')

# Parse arguments
args = parser.parse_args()

# Display parsed arguments
print("Configuration:")
print(f"  Input file: {args.input_file}")
print(f"  Output file: {args.output_file}")
print(f"  Format: {args.format}")
print(f"  Count: {args.count}")
print(f"  Verbose: {args.verbose}")
print(f"  Compress: {args.compress}")
print(f"  Encrypt: {args.encrypt}")

if args.config:
    print(f"  Config: {args.config.name}")
    args.config.close()
```

**Usage**:
```bash
python file_processor.py data.txt
python file_processor.py data.txt processed.txt --format csv --count 20
python file_processor.py data.txt --compress --verbose
python file_processor.py data.txt --help
```

**Output**:
```
Configuration:
  Input file: data.txt
  Output file: output.txt
  Format: json
  Count: 10
  Verbose: False
  Compress: False
  Encrypt: False

Configuration:
  Input file: data.txt
  Output file: processed.txt
  Format: csv
  Count: 20
  Verbose: False
  Compress: False
  Encrypt: False
```

#### Subcommands Example

```python
# git_simulator.py
import argparse

parser = argparse.ArgumentParser(prog='git-sim', description='Git simulator')
subparsers = parser.add_subparsers(dest='command', help='Command to execute')

# Add subcommand
add_parser = subparsers.add_parser('add', help='Add files')
add_parser.add_argument('files', nargs='+', help='Files to add')

# Commit subcommand
commit_parser = subparsers.add_parser('commit', help='Commit changes')
commit_parser.add_argument('-m', '--message', required=True, help='Commit message')

# Push subcommand
push_parser = subparsers.add_parser('push', help='Push to remote')
push_parser.add_argument('remote', default='origin', nargs='?')
push_parser.add_argument('branch', default='main', nargs='?')

args = parser.parse_args()

if args.command == 'add':
    print(f"Adding files: {', '.join(args.files)}")
elif args.command == 'commit':
    print(f"Committing with message: {args.message}")
elif args.command == 'push':
    print(f"Pushing to {args.remote}/{args.branch}")
else:
    parser.print_help()
```

**Usage**:
```bash
python git_simulator.py add file1.py file2.py
python git_simulator.py commit -m "Initial commit"
python git_simulator.py push origin develop
```

**Output**:
```
Adding files: file1.py, file2.py
Committing with message: Initial commit
Pushing to origin/develop
```

**When to Use argparse**:
- ✅ Production scripts
- ✅ Complex argument parsing
- ✅ Need help documentation
- ✅ Type validation
- ✅ Multiple options/flags
- ✅ Subcommands

---

## Unit Testing

### What is Unit Testing?

Unit testing is the practice of testing individual components (units) of code to ensure they work correctly.

### 1. Using unittest (Built-in)

#### Basic unittest Example

```python
# calculator.py
def add(a, b):
    """Add two numbers"""
    return a + b

def subtract(a, b):
    """Subtract b from a"""
    return a - b

def multiply(a, b):
    """Multiply two numbers"""
    return a * b

def divide(a, b):
    """Divide a by b"""
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

# test_calculator.py
import unittest
from calculator import add, subtract, multiply, divide

class TestCalculator(unittest.TestCase):
    """Test calculator functions"""
    
    def test_add(self):
        """Test addition"""
        self.assertEqual(add(2, 3), 5)
        self.assertEqual(add(-1, 1), 0)
        self.assertEqual(add(0, 0), 0)
    
    def test_subtract(self):
        """Test subtraction"""
        self.assertEqual(subtract(5, 3), 2)
        self.assertEqual(subtract(0, 5), -5)
    
    def test_multiply(self):
        """Test multiplication"""
        self.assertEqual(multiply(3, 4), 12)
        self.assertEqual(multiply(0, 5), 0)
        self.assertEqual(multiply(-2, 3), -6)
    
    def test_divide(self):
        """Test division"""
        self.assertEqual(divide(10, 2), 5)
        self.assertEqual(divide(9, 3), 3)
        self.assertAlmostEqual(divide(7, 3), 2.333333, places=5)
    
    def test_divide_by_zero(self):
        """Test division by zero raises error"""
        with self.assertRaises(ValueError):
            divide(10, 0)

if __name__ == '__main__':
    unittest.main()
```

**Running tests**:
```bash
python test_calculator.py
python -m unittest test_calculator.py
python -m unittest test_calculator.TestCalculator.test_add
```

**Output**:
```
.....
----------------------------------------------------------------------
Ran 5 tests in 0.001s

OK
```

#### setUp and tearDown

```python
import unittest
import tempfile
import os

class TestFileOperations(unittest.TestCase):
    """Test file operations with setup/teardown"""
    
    def setUp(self):
        """Run before each test"""
        self.test_dir = tempfile.mkdtemp()
        self.test_file = os.path.join(self.test_dir, 'test.txt')
        print(f"Setup: Created {self.test_file}")
    
    def tearDown(self):
        """Run after each test"""
        if os.path.exists(self.test_file):
            os.remove(self.test_file)
        os.rmdir(self.test_dir)
        print(f"Teardown: Cleaned up {self.test_file}")
    
    def test_write_file(self):
        """Test writing to file"""
        with open(self.test_file, 'w') as f:
            f.write("Hello, World!")
        
        self.assertTrue(os.path.exists(self.test_file))
    
    def test_read_file(self):
        """Test reading from file"""
        with open(self.test_file, 'w') as f:
            f.write("Test content")
        
        with open(self.test_file, 'r') as f:
            content = f.read()
        
        self.assertEqual(content, "Test content")

if __name__ == '__main__':
    unittest.main(verbosity=2)
```

**Output**:
```
test_read_file (__main__.TestFileOperations) ... Setup: Created /tmp/tmpxyz/test.txt
Teardown: Cleaned up /tmp/tmpxyz/test.txt
ok
test_write_file (__main__.TestFileOperations) ... Setup: Created /tmp/tmpxyz/test.txt
Teardown: Cleaned up /tmp/tmpxyz/test.txt
ok

----------------------------------------------------------------------
Ran 2 tests in 0.002s

OK
```

---

### 2. Using pytest (Popular Third-Party)

#### Basic pytest Example

```python
# calculator.py (same as above)
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

# test_calculator_pytest.py
import pytest
from calculator import add, subtract, divide

def test_add():
    """Test addition"""
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

def test_subtract():
    """Test subtraction"""
    assert subtract(5, 3) == 2
    assert subtract(0, 5) == -5

def test_divide():
    """Test division"""
    assert divide(10, 2) == 5
    assert divide(9, 3) == 3

def test_divide_by_zero():
    """Test division by zero"""
    with pytest.raises(ValueError, match="Cannot divide by zero"):
        divide(10, 0)

# Parametrized tests
@pytest.mark.parametrize("a, b, expected", [
    (2, 3, 5),
    (0, 0, 0),
    (-1, 1, 0),
    (10, -5, 5),
])
def test_add_parametrized(a, b, expected):
    """Test addition with multiple inputs"""
    assert add(a, b) == expected
```

**Running pytest**:
```bash
pip install pytest
pytest test_calculator_pytest.py
pytest test_calculator_pytest.py::test_add
pytest -v  # verbose
pytest -k "add"  # run tests matching "add"
```

**Output**:
```
============================= test session starts ==============================
collected 8 items

test_calculator_pytest.py ........                                       [100%]

============================== 8 passed in 0.03s ===============================
```

#### pytest Fixtures

```python
import pytest
import tempfile
import os

@pytest.fixture
def temp_file():
    """Create temporary file for testing"""
    fd, path = tempfile.mkstemp()
    print(f"\nSetup: Created {path}")
    
    yield path  # Provide fixture value
    
    os.close(fd)
    os.remove(path)
    print(f"\nTeardown: Removed {path}")

def test_write_to_file(temp_file):
    """Test writing to temp file"""
    with open(temp_file, 'w') as f:
        f.write("Hello")
    
    with open(temp_file, 'r') as f:
        content = f.read()
    
    assert content == "Hello"

def test_append_to_file(temp_file):
    """Test appending to temp file"""
    with open(temp_file, 'a') as f:
        f.write("Line 1\n")
        f.write("Line 2\n")
    
    with open(temp_file, 'r') as f:
        lines = f.readlines()
    
    assert len(lines) == 2
```

**Output**:
```
============================= test session starts ==============================
collected 2 items

test_file.py
Setup: Created /tmp/tmpxyz123
.
Teardown: Removed /tmp/tmpxyz123

Setup: Created /tmp/tmpxyz456
.
Teardown: Removed /tmp/tmpxyz456

============================== 2 passed in 0.02s ===============================
```

#### Testing BankAccount (Complete Example)

```python
# bank_account.py
class InsufficientFundsError(Exception):
    pass

class BankAccount:
    def __init__(self, account_number, holder_name, initial_balance=0):
        self.account_number = account_number
        self.holder_name = holder_name
        self.balance = initial_balance
    
    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive")
        self.balance += amount
        return self.balance
    
    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive")
        if amount > self.balance:
            raise InsufficientFundsError(f"Insufficient funds: ${self.balance}")
        self.balance -= amount
        return self.balance
    
    def get_balance(self):
        return self.balance

# test_bank_account.py
import pytest
from bank_account import BankAccount, InsufficientFundsError

class TestBankAccount:
    """Test BankAccount class"""
    
    def test_create_account(self):
        """Test account creation"""
        account = BankAccount("12345", "Alice", 1000)
        assert account.account_number == "12345"
        assert account.holder_name == "Alice"
        assert account.balance == 1000
    
    def test_deposit(self):
        """Test deposit functionality"""
        account = BankAccount("12345", "Alice", 100)
        new_balance = account.deposit(50)
        assert new_balance == 150
        assert account.get_balance() == 150
    
    def test_deposit_invalid_amount(self):
        """Test deposit with invalid amount"""
        account = BankAccount("12345", "Alice", 100)
        with pytest.raises(ValueError, match="must be positive"):
            account.deposit(-50)
    
    def test_withdraw(self):
        """Test withdrawal functionality"""
        account = BankAccount("12345", "Alice", 100)
        new_balance = account.withdraw(30)
        assert new_balance == 70
        assert account.get_balance() == 70
    
    def test_withdraw_insufficient_funds(self):
        """Test withdrawal with insufficient funds"""
        account = BankAccount("12345", "Alice", 50)
        with pytest.raises(InsufficientFundsError):
            account.withdraw(100)
    
    @pytest.mark.parametrize("initial, deposit, withdraw, expected", [
        (100, 50, 30, 120),
        (0, 100, 50, 50),
        (1000, 500, 1000, 500),
    ])
    def test_multiple_transactions(self, initial, deposit, withdraw, expected):
        """Test multiple transactions"""
        account = BankAccount("12345", "Alice", initial)
        account.deposit(deposit)
        account.withdraw(withdraw)
        assert account.get_balance() == expected

if __name__ == '__main__':
    pytest.main([__file__, '-v'])
```

**Running tests**:
```bash
pytest test_bank_account.py -v
```

**Output**:
```
============================= test session starts ==============================
collected 8 items

test_bank_account.py::TestBankAccount::test_create_account PASSED         [ 12%]
test_bank_account.py::TestBankAccount::test_deposit PASSED                [ 25%]
test_bank_account.py::TestBankAccount::test_deposit_invalid_amount PASSED [ 37%]
test_bank_account.py::TestBankAccount::test_withdraw PASSED               [ 50%]
test_bank_account.py::TestBankAccount::test_withdraw_insufficient_funds PASSED [ 62%]
test_bank_account.py::TestBankAccount::test_multiple_transactions[100-50-30-120] PASSED [ 75%]
test_bank_account.py::TestBankAccount::test_multiple_transactions[0-100-50-50] PASSED [ 87%]
test_bank_account.py::TestBankAccount::test_multiple_transactions[1000-500-1000-500] PASSED [100%]

============================== 8 passed in 0.05s ===============================
```

**When to Use Testing**:
- ✅ All production code
- ✅ Complex logic
- ✅ Critical functionality
- ✅ Before refactoring
- ✅ CI/CD pipelines
- ✅ Team projects

---

## Code Style & Formatting

### PEP 8 Basics

**PEP 8** is the official Python style guide. Following it makes code more readable and maintainable.

#### Indentation

```python
# ❌ Bad
def bad_function():
  x=1
  if x>0:
   print(x)

# ✅ Good
def good_function():
    x = 1
    if x > 0:
        print(x)
```

#### Line Length

```python
# ❌ Bad - line too long
very_long_variable_name = some_function_call(parameter1, parameter2, parameter3, parameter4, parameter5, parameter6)

# ✅ Good - break into multiple lines
very_long_variable_name = some_function_call(
    parameter1,
    parameter2,
    parameter3,
    parameter4,
    parameter5,
    parameter6
)
```

#### Whitespace

```python
# ❌ Bad
x=1+2
my_list=[1,2,3,4]
my_dict={'name':'Alice','age':25}

# ✅ Good
x = 1 + 2
my_list = [1, 2, 3, 4]
my_dict = {'name': 'Alice', 'age': 25}
```

#### Imports

```python
# ❌ Bad
import sys, os
from os import *

# ✅ Good
import os
import sys
from os import path, getcwd

# Order: standard library, third-party, local
import os
import sys

import numpy as np
import pandas as pd

from myapp import models
from myapp.utils import helpers
```

---

### Meaningful Variable Names

#### Variables

```python
# ❌ Bad - unclear, too short
x = 5
d = {}
tmp = get_data()

# ✅ Good - descriptive
user_count = 5
user_data = {}
customer_records = get_data()
```

#### Functions

```python
# ❌ Bad
def calc(x, y):
    return x + y

def process():
    pass

# ✅ Good
def calculate_total_price(base_price, tax_rate):
    """Calculate total price including tax."""
    return base_price * (1 + tax_rate)

def validate_email_address():
    """Validate user email address format."""
    pass
```

#### Classes

```python
# ❌ Bad
class data:
    pass

class USERAccount:
    pass

# ✅ Good
class UserAccount:
    """Represents a user account in the system."""
    pass

class PaymentProcessor:
    """Handles payment processing operations."""
    pass
```

#### Constants

```python
# ❌ Bad
maxconnections = 100
api_key = "secret123"

# ✅ Good
MAX_CONNECTIONS = 100
API_KEY = "secret123"
DEFAULT_TIMEOUT = 30
```

#### Complete Example

```python
# ❌ Bad
def f(x):
    tmp = []
    for i in x:
        if i > 0:
            tmp.append(i * 2)
    return tmp

# ✅ Good
def double_positive_numbers(numbers):
    """
    Double all positive numbers in the list.
    
    Args:
        numbers: List of integers
    
    Returns:
        List of doubled positive numbers
    """
    doubled_positives = []








# extras-more
Acknowledged. Here’s a clear, structured rationale and plan for this RAG MVP, aligned to what’s in the repo.
** Objective **
- Acknowledged. Here’s a crisp summary of the project’s objective and what it does.

**Objective**
- Build a local, privacy‑friendly RAG Q&A bot that answers user questions using scraped web content, with grounded responses and citations.

**What It Does**
- Ingests a target webpage via a scraper, then cleans, chunks, embeds, and stores text in a vector database.
- Retrieves the most relevant chunks for a user’s question and generates an answer constrained to that context.
- Produces answers with “Sources:” citing the original page(s) to support trust and verification.
- Caches repeated queries to return fast, consistent results.
- Persists a lightweight session log of queries/answers (optional) for audit or review.
- Runs entirely on your machine (Windows) using local LLM and embeddings, no cloud keys.

**Pipeline**
- Scrape: knowledge_base.py loads the page with browser headers.
- Index: Chunks via `RecursiveCharacterTextSplitter`, embeds with `nomic-embed-text`, stores in Chroma.
- Retrieve + Answer: `QABot` retrieves top‑k, builds a grounded prompt, calls local `llama3`, returns answer + citations.
- CLI: main.py provides an interactive loop for questions.
- Logging: storage.py optionally records `query`, `answer`, `sources`.

**Success Criteria**
- Answers “What is RAG?” with a correct definition and cites the RAG Wikipedia page.
- Declines cleanly when context is missing (“cannot find the relevant information”).
- Caches repeated queries and persists the vector store for reuse across runs.

# New 1

**Libraries & Rationale**
- **`langchain` ecosystem:** Composable building blocks for retrievers, prompts, and LLM orchestration; reduces boilerplate while keeping pipeline explicit.
- **`langchain-ollama` (`ChatOllama`, `OllamaEmbeddings`):** Local LLM and embedding inference via Ollama; chosen for privacy, reproducibility, and offline viability.
- **`langchain-chroma` + `chromadb`:** Persistent vector store with simple APIs; good dev UX, fast local similarity search; chosen over FAISS for built-in persistence and metadata.
- **`bs4` + `WebBaseLoader`:** Robust page ingestion with browser-like headers to avoid partial content; keeps scraping simple and reliable.
- **`langchain-text-splitters` (`RecursiveCharacterTextSplitter`):** Balanced chunking with configurable overlap; improves recall and reduces context loss.
- **`pytest` / `unittest`:** Lightweight test scaffolding to validate retrieval, caching, and error handling; both supported for convenience.

**Pros & Cons**
- **Pros:**
  - **Local-first:** No external API keys; reproducible demos.
  - **Citations:** Metadata preserved; responses include “Sources:” to aid trust.
  - **Simplicity:** Small, readable modules and minimal dependencies.
  - **Persistence:** Chroma survives restarts; rebuild controlled by deleting folder.
- **Cons:**
  - **Model availability:** Requires Ollama install and model pulls; GPU optional but CPU can be slow.
  - **Content variability:** Web pages change; deterministic assertions need pinning to known URLs.
  - **Memory footprint:** Local embeddings + DB consume disk/RAM.
  - **Limited scale:** Single-user CLI; not optimized for high throughput.

**Plan of Action**
- **Requirements & scope:** Documented in requirements.md.
- **Design:** High-level and low-level designs in hld.md and lld.md; data flow + schema in dataflow.md.
- **Working demo:** CLI in main.py with a deterministic `KB_URL` to the RAG Wikipedia page; ingestion in knowledge_base.py; retrieval + generation + caching in qa_bot.py.
- **Logging:** Optional SQLite `SessionStore` in storage.py; wired in main.py and qa_bot.py.
- **Tests:** Add `pytest` suite test_pipeline.py and retain `unittest` in test_bot.py.
- **Timeframe:** 
  - Day 1: Environment, ingestion, embeddings, Chroma persistence.
  - Day 2 (half): Retriever + LLM + prompt + citations + caching.
  - Day 2 (half): Tests, docs, and demo polish.
- **Deliverable:** Run instructions in README.md and a live CLI demo answering “What is RAG?”.

**Working Demo Steps**
- **Setup:**
  - `. .\.venv\Scripts\Activate.ps1`
  - `pip install --upgrade pip`
  - `pip install -r requirements.txt`
- **Ollama:**
  - `ollama pull llama3`
  - `ollama pull nomic-embed-text`
  - `Invoke-RestMethod http://127.0.0.1:11434/api/tags | ConvertTo-Json -Depth 3`
- **Run:**
  - `python main.py`
  - Ask “What is RAG?” → Answer references “Retrieval‑augmented generation” and includes “Sources: https://en.wikipedia.org/wiki/Retrieval-augmented_generation”.

**Assumptions**
- **Connectivity:** Internet access for scraping; Ollama reachable at `http://127.0.0.1:11434`.
- **Models:** `llama3` and `nomic-embed-text` are pulled.
- **Dataset size:** Small, single page or a few pages; single-user CLI.
- **Grounding:** Answers must adhere to retrieved context; if missing, respond with “cannot find the relevant information”.

**Features Considered Deal‑Breakers**
- **Grounded answers:** Must only use retrieved context and decline if unknown.
- **Citations:** “Sources:” required to build trust and support verification.
- **Deterministic ingestion:** Pin to a stable source (RAG Wikipedia) for consistent demos and tests.
- **Local operability:** No cloud keys; demo must run with Ollama locally.

**Confidence Areas**
- **Pipeline robustness:** Scrape → chunk → embed → store works reliably with `WebBaseLoader` and Chroma.
- **Retrieval quality:** `nomic-embed-text` improves semantic matching; chunk overlap mitigates context boundary loss.
- **Operational clarity:** Base URL explicitly set for Ollama; README includes connectivity checks.

**Risks & Mitigations**
- **Page changes:** If Wikipedia alters content, tests may fail; mitigate by caching or snapshotting text.
- **Ollama not running:** Add startup checks and clearer messages; already documented troubleshooting.
- **Performance:** CPU-only inference may be slow; acceptable for MVP; note in requirements.

**Next Enhancements (if time permits)**
- **Web UI:** Minimal FastAPI + UI for queries and citation display.
- **Multi-source ingestion:** Crawl a shortlist of curated URLs.
- **Session analytics:** Dashboard over SQLite to inspect queries and sources.
- **Improved ranking:** Hybrid retrieval (BM25 + embeddings) for better precision.

If you want, I can add a small startup health check that verifies Ollama connectivity before indexing or answering, and return a readable error instead of a stack trace.

## And
Acknowledged. Here’s a clear, well‑framed summary of the tech stack, libraries, modules, and models used, plus the “why” behind each, with pros and cons.

**Tech Stack**
- **Language:** Python 3.x on Windows — mature ecosystem for ML/RAG, easy local setup.
- **Runtime:** Local environment with virtualenv — isolates dependencies and versions.
- **LLM runtime:** Ollama — local inference, reproducibility, privacy, offline viability.
- **Vector DB:** Chroma — fast local similarity search with simple persistence.

**Core Libraries**
- **langchain:** Orchestration primitives for prompts, retrievers, and LLM calls; reduces boilerplate while keeping composition explicit.
- **langchain-ollama:** `ChatOllama` and `OllamaEmbeddings` to talk to Ollama cleanly; avoids custom HTTP calls and handles message formatting.
- **langchain-chroma + chromadb:** Integration layer plus engine for storing embeddings and metadata with disk persistence.
- **langchain-text-splitters:** `RecursiveCharacterTextSplitter` balances chunk size/overlap for good recall.
- **bs4 + requests / WebBaseLoader:** Robust page ingestion with browser-like headers to reliably fetch article content.
- **sqlite3:** Lightweight, built-in database to log sessions and queries without external services.
- **pytest / unittest:** Quick validation of retrieval/caching behavior and error handling.

**Models (via Ollama)**
- **`llama3` (generation):** General-purpose chat model for forming grounded answers from retrieved context.
  - Pros: Local, no API keys, decent quality for a demo.
  - Cons: Larger footprint; CPU inference can be slow without GPU.
- **`nomic-embed-text` (embeddings):** Strong semantic embeddings improve retrieval quality.
  - Pros: Better recall/precision vs general LLM embeddings; local.
  - Cons: Requires separate model pull; adds another download.

**Project Modules**
- **Scraper/Indexer:** knowledge_base.py
  - Loads the page (browser-like headers), splits text, embeds with `nomic-embed-text`, persists to Chroma with `source` metadata for citations.
- **Retriever + LLM + Cache:** qa_bot.py
  - Retrieves top‑k chunks, builds a grounded prompt, calls `llama3`, returns an answer with “Sources: …”; simple in-memory cache to avoid recomputation; optional SQLite logging.
- **CLI Runner:** main.py
  - Orchestrates ingestion (first run), loads the vector store, starts the Q&A loop, wires `SessionStore`.
- **Session Logging (DB):** storage.py
  - SQLite `sessions` table for time‑stamped query/answer/source logging.
- **Tests & Docs:** test_pipeline.py, requirements.md, hld.md, lld.md, dataflow.md.

**Why This Stack**
- **Local-first demo:** Eliminates cloud/API dependencies; reproducible and privacy-friendly.
- **Simplicity:** Chroma + LangChain integrations keep the code small and understandable.
- **Grounding & trust:** Chunk metadata carries the `source` URL; answers include citations.
- **Deterministic ingestion:** Default URL targets the RAG Wikipedia page to stabilize tests and demos.

**Pros**
- **Reproducible and private:** Runs entirely on your machine with Ollama.
- **Quick to reason about:** Clear RAG pipeline with minimal moving parts.
- **Persistent storage:** Chroma index survives restarts; SQLite logs sessions.

**Cons**
- **Model setup required:** Must install Ollama and pull models; potential friction on first run.
- **Performance:** CPU-only inference can be slow; not tuned for scale.
- **Content drift:** Live web pages may change; tests rely on a stable URL.

**Alternatives Considered**
- **FAISS vs Chroma:** FAISS is excellent for performance but lacks built-in metadata/persistence UX; Chroma was simpler for a local MVP.
- **Cloud LLMs (OpenAI/Anthropic):** Faster and higher quality but introduce keys, cost, and privacy concerns; not ideal for an offline MVP demo.
- **Redis for caching:** Good for multi-process; SQLite + in-memory cache is sufficient for single-user CLI.

If you’d like, I can add a small startup health check that pings Ollama and prints a friendly message before indexing/answering, or swap in a web UI for a more polished demo.
