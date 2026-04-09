# 🚀 COMPLETE BACKEND DEVELOPER PREPARATION DOCUMENT
## Industry-Level Comprehensive Guide: Python → Databases → Web Frameworks → Version Control

---

# TABLE OF CONTENTS

1. [Core Python Fundamentals](#core-python-fundamentals)
2. [Core Python: Logic Building](#core-python-logic-building)
3. [Advanced Python](#advanced-python)
4. [SQL Databases](#sql-databases)
5. [NoSQL Databases (MongoDB)](#nosql-databases-mongodb)
6. [Flask Framework](#flask-framework)
7. [FastAPI Framework](#fastapi-framework)
8. [Git & Version Control](#git--version-control)
9. [Test Preparation & Practice](#test-preparation--practice)
10. [Mini Projects](#mini-projects)
11. [Full Industry Project](#full-industry-project)
12. [Interview Cheat Sheet](#interview-cheat-sheet)

---

# CORE PYTHON FUNDAMENTALS

## 1) Complete Theory: Python Basics

### What is Python?
Python is a **high-level, interpreted, dynamically-typed programming language** designed for readability and simplicity. Unlike compiled languages (Java, C++), Python executes line-by-line through an interpreter.

### Why Python for Backend?
- **Syntax simplicity**: Learn faster, write less code
- **Rich ecosystem**: Flask, Django, FastAPI, SQLAlchemy, Celery
- **Scalability**: Used by YouTube, Instagram, Pinterest
- **Data science integration**: NumPy, Pandas, ML libraries
- **Fast prototyping**: From idea to production quickly

---

## 2) Variables & Assignment (Deep Dive)

### How Python Variables Actually Work

```python
# Python doesn't store values in variables
# Variables are LABELS pointing to objects in memory

x = 10
# This creates an integer object (10) in memory
# Then creates a label 'x' pointing to it

y = x
# y now points to THE SAME object as x
print(id(x) == id(y))  # True - same memory address

# Rebinding (most important concept)
x = 20
# Creates NEW integer object (20)
# Points 'x' label to it
# y still points to old object (10)
print(x)  # 20
print(y)  # 10
print(id(x) == id(y))  # False - different objects
```

### Key Interview Questions
**Q: What happens when you assign a mutable object?**
```python
list1 = [1, 2, 3]
list2 = list1
list2.append(4)

print(list1)  # [1, 2, 3, 4] - CHANGED!
# Because both point to same list object

# To avoid this
list2 = list1.copy()  # or list(list1)
list2.append(4)
print(list1)  # [1, 2, 3] - unchanged
```

---

## 3) Data Types (Complete Reference)

### Immutable Types (Cannot change after creation)

#### Strings
```python
# Strings are immutable
s = "hello"
# s[0] = "H"  # ❌ TypeError

# Operations create NEW strings
s = s.upper()  # "HELLO" - new object created

# String interning (Python optimization)
a = "hello"
b = "hello"
print(id(a) == id(b))  # True - same object (for small strings)

# But with variables
s1 = "hello" * 1000
s2 = "hello" * 1000
print(id(s1) == id(s2))  # False - different objects
```

**Why immutable is important:**
- Can be used as dictionary keys
- Thread-safe
- Can be cached/reused by Python

#### Tuples
```python
# Immutable sequence
t = (1, 2, 3)
# t[0] = 10  # ❌ TypeError

# Can contain mutable objects
t = (1, [2, 3], 4)
t[1].append(5)  # Modifies list inside tuple!
print(t)  # (1, [2, 3, 5], 4) - CHANGED!
# Tuple structure unchanged, but contents changed

# Tuple unpacking (very useful)
a, b, c = (1, 2, 3)
x, *middle, y = [1, 2, 3, 4, 5]
print(x, middle, y)  # 1 [2, 3, 4] 5

# Tuple returning multiple values
def get_coordinates():
    return (10, 20)

x, y = get_coordinates()
```

#### Numbers (int, float, complex)
```python
# Int has no size limit
big_num = 10 ** 100
print(type(big_num))  # <class 'int'>

# Float precision issues (IEEE 754)
print(0.1 + 0.2)  # 0.30000000000000004 (not 0.3!)
# Use Decimal for financial calculations
from decimal import Decimal
print(Decimal('0.1') + Decimal('0.2'))  # 0.3

# Type coercion
print(5 + 2.5)      # 7.5 (int + float = float)
print(5 + True)     # 6 (True is 1)
print(5 + False)    # 5 (False is 0)
```

#### Booleans
```python
# bool is subclass of int!
print(isinstance(True, int))  # True
print(True == 1)              # True
print(False == 0)             # True

# Truthiness/Falsiness
# Falsy: None, False, 0, 0.0, "", [], {}, (), set()
# Truthy: Everything else

if "hello":           # Truthy (non-empty string)
    print("Yes")

if []:                # Falsy (empty list)
    print("Won't print")
```

### Mutable Types (Can change after creation)

#### Lists (ordered, indexed, mutable)
```python
lst = [1, 2, 3]

# List has dynamic resizing (amortized O(1) append)
lst.append(4)       # Efficient - amortized constant time
lst.pop()           # O(1) - removes last element

lst.insert(0, 0)    # O(n) - shifts all elements
lst.pop(0)          # O(n) - shifts all elements

# Slicing creates NEW list
new_lst = lst[1:3]  # [2, 3]
new_lst.append(99)
print(lst)          # Unchanged

# Reference vs Copy
list2 = lst
list2.append(99)
print(lst)          # CHANGED - same object

list3 = lst.copy()  # Shallow copy
list3.append(99)
print(lst)          # Unchanged
```

#### Dictionaries (key-value pairs, ordered in 3.7+)
```python
# Dictionary ordering (Python 3.7+)
d = {}
d['z'] = 1
d['a'] = 2
d['m'] = 3
print(list(d.keys()))  # ['z', 'a', 'm'] - insertion order!

# Dictionary is hash-based
# Keys must be hashable (immutable)
d = {(1, 2): "point"}  # ✅ Tuple is hashable
# d = {[1, 2]: "point"}  # ❌ List is not hashable

# Keys must be unique
d = {'a': 1, 'a': 2}
print(d)  # {'a': 2} - last value wins

# Dictionary view objects (efficient)
keys = d.keys()
print('a' in keys)  # O(1) - fast lookup
```

#### Sets (unique, unordered, mutable)
```python
# Sets are unordered
s = {3, 1, 2}
print(s)  # Could print {1, 2, 3} or {2, 3, 1}

# Duplicate removal
s = {1, 1, 1, 2, 2, 3}
print(s)  # {1, 2, 3}

# Set operations
set1 = {1, 2, 3}
set2 = {2, 3, 4}

print(set1 & set2)   # {2, 3} - intersection
print(set1 | set2)   # {1, 2, 3, 4} - union
print(set1 - set2)   # {1} - difference

# No indexing
# s[0]  # ❌ TypeError

# Use for membership testing (O(1) vs O(n) for list)
large_list = list(range(1000000))
large_set = set(large_list)

# Testing membership
1000 in large_set   # Very fast O(1)
1000 in large_list  # Much slower O(n)
```

---

## 4) I/O Operations (Input/Output)

### Console I/O
```python
# input() - reads from console, returns STRING
age = input("Enter age: ")
print(type(age))  # <class 'str'>
age = int(age)    # Must convert

# print() with options
print("Hello", "World", sep="-", end="!\n")
# Output: Hello-World!

# Formatted output
name = "Alice"
score = 95.5
print(f"Name: {name}, Score: {score:.1f}")  # f-strings (Python 3.6+)
print("Name: {}, Score: {:.1f}".format(name, score))  # .format()
print("Name: %s, Score: %.1f" % (name, score))  # % formatting (old)
```

### File I/O (Critical for Backend)
```python
# Writing to file
with open("data.txt", "w") as f:
    f.write("Line 1\n")
    f.writelines(["Line 2\n", "Line 3\n"])
# File automatically closes

# Reading from file
with open("data.txt", "r") as f:
    content = f.read()       # Entire file as string
    lines = f.readlines()    # List of lines

# Appending to file
with open("data.txt", "a") as f:
    f.write("New line\n")

# File modes
# "r" - read only
# "w" - write (overwrites)
# "a" - append
# "x" - create (error if exists)
# "rb" - read binary
# "wb" - write binary

# Why 'with' is important
# Even if error occurs, file is properly closed
file = open("data.txt", "r")
# If error here, file never closes → resource leak
content = file.read()
file.close()

# ✅ Better - with statement ensures closing
with open("data.txt", "r") as f:
    content = f.read()
# File closed automatically
```

---

## 5) Operators Deep Dive

### Arithmetic Operators
```python
# Division types
print(10 / 3)       # 3.333... (float division)
print(10 // 3)      # 3 (floor division)
print(10 % 3)       # 1 (modulo - remainder)
print(2 ** 10)      # 1024 (exponentiation)

# Modulo with floats
print(10.5 % 3)     # 1.5
print(-10 % 3)      # 2 (rounds toward negative infinity)

# Power operator
print(2 ** 3 ** 2)  # 2 ** (3 ** 2) = 2 ** 9 = 512
# Right-associative!
```

### Comparison Operators
```python
# Comparison chains (Pythonic)
x = 50
print(0 < x < 100)    # True - same as (0 < x and x < 100)

# Identity vs Equality
a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)         # True - same content
print(a is b)         # False - different objects

# String comparison (lexicographic)
print("apple" < "banana")  # True
print("Apple" < "apple")   # True (uppercase < lowercase in ASCII)

# None comparison
x = None
print(x is None)      # Correct way
# print(x == None)    # Works but not recommended
```

### Logical Operators
```python
# and, or use short-circuit evaluation
x = 0
y = 5

# Short-circuit: 'and' stops if first is False
print(x and y)        # 0 (x is falsy, doesn't evaluate y)
print(x or y)         # 5 (x is falsy, evaluates y)

# Returns actual value, not boolean
print(5 and 10)       # 10 (both truthy, returns last)
print(0 or 10)        # 10 (0 falsy, returns truthy value)

# not always returns boolean
print(not 5)          # False
print(not 0)          # True
```

---

## 6) Strings - Complete Guide

### String Methods (Most Used)
```python
s = "  Hello World  "

# Case modification
s.upper()          # "  HELLO WORLD  "
s.lower()          # "  hello world  "
s.capitalize()     # "  hello world  " (first char only)
s.title()          # "  Hello World  " (each word)
s.swapcase()       # "  hELLO wORLD  "

# Whitespace handling
s.strip()          # "Hello World" (leading & trailing)
s.lstrip()         # "Hello World  " (left only)
s.rstrip()         # "  Hello World" (right only)
s.strip('l')       # "  Hello Wor  " (strip specific char)

# Finding & replacing
s.find("World")    # 8 (position)
s.find("xyz")      # -1 (not found)
s.index("World")   # 8 (same as find but raises error if not found)
s.count("l")       # 3 (occurrences)
s.replace("World", "Python")  # "  Hello Python  "

# Checking content
s.startswith("Hello")  # False (has leading spaces)
s.startswith("  ")     # True
s.endswith("World")    # False (has trailing spaces)
s.endswith("  ")       # True
s.isdigit()            # False
s.isalpha()            # False (has spaces)
s.isalnum()            # False
s.isspace()            # False
s.islower()            # False
s.isupper()            # False

# Splitting & joining
"a,b,c".split(",")     # ["a", "b", "c"]
"hello world".split()  # ["hello", "world"] - default whitespace
",".join(["a", "b", "c"])  # "a,b,c"

# Useful for data processing
",".join(str(x) for x in [1, 2, 3])  # "1,2,3"
```

### String Formatting (Interview Important)
```python
name = "Alice"
age = 30
score = 95.678

# 1. f-strings (Python 3.6+, RECOMMENDED)
print(f"Name: {name}, Age: {age}")
print(f"Score: {score:.2f}")           # Format to 2 decimals
print(f"Score: {score:06.2f}")         # Pad with 0, total 6 chars
print(f"Score: {score:<10}")           # Left align in 10 chars
print(f"Score: {score:>10}")           # Right align
print(f"Score: {score:^10}")           # Center

# 2. .format() method
"{} is {} years old".format(name, age)
"{0} is {1} years old".format(name, age)  # Positional
"{name} is {age} years old".format(name=name, age=age)  # Named
"{:10}".format(name)                     # Width
"{:.2f}".format(score)                   # Precision

# 3. % formatting (old, avoid in new code)
"%s is %d years old" % (name, age)

# 4. Raw strings (regex, paths)
path = r"C:\Users\Alice\file.txt"  # r prefix ignores escapes
regex = r"\d+"                      # Match digits
```

### String Slicing (Critical)
```python
s = "Hello"

# Positive indexing (from start)
s[0]        # 'H'
s[1:3]      # 'el' (from 1 to 3, 3 exclusive)
s[:3]       # 'Hel' (from start to 3)
s[2:]       # 'llo' (from 2 to end)
s[:]        # 'Hello' (entire string)

# Negative indexing (from end)
s[-1]       # 'o' (last char)
s[-3:]      # 'llo' (last 3 chars)
s[:-2]      # 'Hel' (all except last 2)

# Step/stride
s[::2]      # 'Hlo' (every 2nd char)
s[1::2]     # 'el' (starting from 1, every 2nd)
s[::-1]     # 'olleH' (reverse string)

# Slicing doesn't raise error for out-of-bounds
s[10]       # ❌ IndexError
s[10:]      # ✅ Returns '' (empty)
```

---

## 7) Booleans & Truth Values

### Truthy and Falsy Evaluation
```python
# FALSY values
print(bool(None))       # False
print(bool(False))      # False
print(bool(0))          # False
print(bool(0.0))        # False
print(bool(""))         # False
print(bool([]))         # False
print(bool({}))         # False
print(bool(()))         # False
print(bool(set()))      # False

# TRUTHY values
print(bool(True))       # True
print(bool(1))          # True
print(bool(-1))         # True
print(bool("hello"))    # True
print(bool([1]))        # True
print(bool({"a": 1}))   # True
print(bool((1,)))       # True
print(bool({1}))        # True

# Common pattern in code
user_input = input("Enter name: ")
if user_input:          # Checks if non-empty
    print(f"Hello {user_input}")

data = fetch_data()
if data:                # Checks if list is not empty
    process(data)

value = get_value()
if value is not None:   # Distinguish between None and falsy
    use(value)
```

---

## 8) Casting (Type Conversion)

### Explicit Casting
```python
# String conversions
int("10")              # 10
int("0x10", 16)        # 16 (hexadecimal)
int("0b10", 2)         # 2 (binary)
float("3.14")          # 3.14
bool("False")          # True (non-empty string is truthy!)

str(123)               # "123"
str([1, 2, 3])         # "[1, 2, 3]"
str({"a": 1})          # "{'a': 1}"

# Sequence conversions
list("abc")            # ['a', 'b', 'c']
list({1, 2, 3})        # [1, 2, 3] (order not guaranteed)
list((1, 2, 3))        # [1, 2, 3]

tuple("abc")           # ('a', 'b', 'c')
tuple([1, 2, 3])       # (1, 2, 3)

set("aabbcc")          # {'a', 'b', 'c'}
set([1, 2, 2, 3])      # {1, 2, 3}

dict([("a", 1), ("b", 2)])  # {"a": 1, "b": 2}

# Numeric conversions
float(5)               # 5.0
int(3.9)               # 3 (truncates, doesn't round)
int(True)              # 1
int(False)             # 0
float(True)            # 1.0
```

### Implicit Casting (Coercion)
```python
# Numeric operations promote to wider type
5 + 2.5                # 7.5 (int + float = float)
5 + True               # 6 (int + bool(1) = int)
5.0 + True             # 6.0 (float + bool = float)

# String concatenation requires matching types
"Hello" + "World"      # ✅ "HelloWorld"
"Hello" + str(5)       # ✅ "Hello5"
# "Hello" + 5          # ❌ TypeError
```

---

## 9) Comments & Code Documentation

### Comment Types
```python
# Single-line comment
# Describe what the next line does

# Multi-line comment
# Line 1
# Line 2
# Line 3

""" Multi-line string comment
Can be used for longer explanations
But technically creates a string object
"""

# Block comments
# Section: User authentication
# This section handles...
# Important: ...
```

### Docstrings (Professional Standard)
```python
def calculate_interest(principal, rate, time):
    """
    Calculate simple interest.
    
    Args:
        principal (float): Initial amount in currency
        rate (float): Annual interest rate as percentage (e.g., 5 for 5%)
        time (int): Time period in years
        
    Returns:
        float: Calculated interest amount
        
    Raises:
        ValueError: If any parameter is negative
        
    Example:
        >>> calculate_interest(1000, 5, 2)
        100.0
    """
    if principal < 0 or rate < 0 or time < 0:
        raise ValueError("Parameters cannot be negative")
    return (principal * rate * time) / 100

# Access docstring
help(calculate_interest)
print(calculate_interest.__doc__)
```

---

## 10) PIP & Virtual Environment (Critical Setup)

### Virtual Environment
```bash
# Create
python -m venv .venv

# Activate (Linux/Mac)
source .venv/bin/activate

# Activate (Windows)
.venv\Scripts\activate

# Deactivate
deactivate

# Why? Isolate dependencies per project
# Without venv: Project A needs Django 3.0, Project B needs Django 4.0
# Conflict! venv solves this
```

### PIP Package Management
```bash
# Install single package
pip install requests

# Install specific version
pip install requests==2.28.0

# Install with version constraint
pip install "requests>=2.28.0,<3.0.0"

# Install from requirements file
pip install -r requirements.txt

# Generate requirements file
pip freeze > requirements.txt

# Install in editable mode (for development)
pip install -e ./my_package

# List installed packages
pip list

# Check package info
pip show requests

# Uninstall
pip uninstall requests

# Update package
pip install --upgrade requests
```

### Best Practices
```bash
# Always use venv for professional projects
# requirements.txt should be committed to git
# .venv should be in .gitignore

# Professional requirements.txt structure
# requirements/
# ├── base.txt (common dependencies)
# ├── dev.txt (development only)
# └── prod.txt (production only)

# Production setup
pip install -r requirements/prod.txt
```

---

# CORE PYTHON: LOGIC BUILDING

## 1) Control Flow - Complete Theory

### if/elif/else Statements
```python
# Simple if
if age >= 18:
    print("Adult")

# if-else
if score >= 60:
    print("Pass")
else:
    print("Fail")

# if-elif-else chain
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"

# Nested conditions
if user_logged_in:
    if user.is_admin:
        show_admin_panel()
    else:
        show_user_panel()
else:
    show_login_page()

# Avoid excessive nesting
# Better: guard clause pattern
if not user_logged_in:
    show_login_page()
    return

if not user.is_admin:
    show_user_panel()
    return

show_admin_panel()
```

### Ternary Operator (Conditional Expression)
```python
# Traditional if-else
if age >= 18:
    status = "adult"
else:
    status = "minor"

# Ternary (one-liner)
status = "adult" if age >= 18 else "minor"

# Nested ternary (avoid - harder to read)
grade = "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "F"

# Better: use if-elif chain for multiple conditions
```

### Logical Operators with Short-Circuiting
```python
# AND operator - returns first falsy value or last value
print(5 and 10)           # 10 (both truthy, returns last)
print(0 and 10)           # 0 (first is falsy, returns 0)
print(5 and 0)            # 0 (second is falsy, returns 0)

# OR operator - returns first truthy value or last value
print(5 or 10)            # 5 (first is truthy, returns 5)
print(0 or 10)            # 10 (0 is falsy, returns 10)
print(0 or 0)             # 0 (both falsy, returns last)

# Practical uses
user = get_user()
name = user and user.name  # If user exists, get name; else None

default_value = user_input or "default"  # Use input or default

# Avoid unnecessary evaluation (performance)
if expensive_check() and quick_check():  # If expensive is False, quick_check() won't run
    pass
```

### Truthy/Falsy in Conditionals
```python
# Instead of explicit comparison
if len(items) > 0:      # ❌ Explicit but verbose
    pass

if items:               # ✅ Pythonic - relies on truthiness

# But be careful
if x is not None:       # ✅ Correct for None
if x:                   # ❌ Could miss 0 or ""

if x == 0:              # ✅ Correct for zero check
if not x:               # ❌ Could confuse readers

# Dictionary/list existence
config = get_config()
if config:              # Checks if not empty
    apply_config(config)
```

---

## 2) Loops Deep Dive

### for Loops
```python
# Basic for loop
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# Loop with custom range
for i in range(2, 8, 2):  # start, stop, step
    print(i)  # 2, 4, 6

# Iterating over collections
items = [10, 20, 30]
for item in items:
    print(item)

# Iterating with index
for i, item in enumerate(items):
    print(f"Index {i}: {item}")

for i, item in enumerate(items, start=1):  # Start index from 1
    print(f"Item {i}: {item}")

# Iterating over dictionary
user = {"name": "John", "age": 30}
for key in user:
    print(key, user[key])

for key, value in user.items():
    print(key, value)

# zip - parallel iteration
list1 = [1, 2, 3]
list2 = ['a', 'b', 'c']
for num, letter in zip(list1, list2):
    print(num, letter)  # 1 a, 2 b, 3 c

# Nested loops
for i in range(3):
    for j in range(3):
        print(f"({i},{j})", end=" ")
    print()  # Newline

# Equivalent with itertools
import itertools
for i, j in itertools.product(range(3), range(3)):
    print(f"({i},{j})", end=" ")
```

### while Loops
```python
# Basic while
count = 0
while count < 5:
    print(count)
    count += 1

# Infinite loop pattern
while True:
    user_input = input("Enter command (q to quit): ")
    if user_input == 'q':
        break
    process(user_input)

# do-while pattern (not native in Python)
# Run at least once
while True:
    result = get_result()
    if result is not None:
        break
```

### Loop Control Statements
```python
# break - exit loop
for i in range(10):
    if i == 5:
        break
# Output: 0 1 2 3 4

# continue - skip current iteration
for i in range(10):
    if i % 2 == 0:
        continue
    print(i)
# Output: 1 3 5 7 9

# pass - placeholder (do nothing)
for i in range(10):
    if i == 5:
        pass  # Handle later
    else:
        print(i)

# else clause (executes if loop completes without break)
for i in range(5):
    print(i)
else:
    print("Loop completed normally")

# With break, else doesn't execute
for i in range(5):
    if i == 3:
        break
else:
    print("Won't print")  # Skipped because of break
```

### Looping Best Practices
```python
# ❌ Modifying list while iterating
items = [1, 2, 3, 4, 5]
for item in items:
    if item == 3:
        items.remove(item)  # Dangerous!

# ✅ Iterate over copy or collect indices to remove
for item in items[:]:  # Iterate over copy
    if item == 3:
        items.remove(item)

# ✅ Or use list comprehension
items = [x for x in items if x != 3]

# ✅ Or use filter
items = list(filter(lambda x: x != 3, items))
```

---

## 3) Strings & Formatting (Complete Guide)

### String Operations
```python
# Concatenation
s = "Hello" + " " + "World"  # "Hello World"

# Repetition
s = "ab" * 3  # "ababab"

# Length
len("hello")  # 5

# Character at index
"hello"[1]   # 'e'

# Substring check
"ll" in "hello"     # True
"ll" not in "hello" # False

# Find position
"hello".find("ll")   # 2
"hello".find("xyz")  # -1

# Count occurrences
"hello".count("l")   # 2
"hello".count("ll")  # 1
```

### String Formatting Deep Dive
```python
name = "Alice"
age = 30
score = 95.6789

# 1. f-strings (Python 3.6+, RECOMMENDED for modern code)
f"{name} is {age} years old"

# Format specifiers
f"{score:.2f}"              # "95.68" - 2 decimal places
f"{score:.0f}"              # "96" - no decimals
f"{score:10.2f}"            # "     95.68" - width 10, 2 decimals
f"{score:<10.2f}"           # "95.68     " - left aligned
f"{score:>10.2f}"           # "     95.68" - right aligned
f"{score:^10.2f}"           # "  95.68   " - center aligned
f"{score:010.2f}"           # "0000095.68" - pad with zeros

# Hex, octal, binary
n = 255
f"{n:x}"                    # "ff" - hexadecimal
f"{n:o}"                    # "377" - octal
f"{n:b}"                    # "11111111" - binary
f"{n:e}"                    # "2.550000e+02" - scientific notation

# 2. .format() method (Python 3, still used)
"{} is {} years old".format(name, age)
"{0} is {1} years old".format(name, age)
"{name} is {age} years old".format(name=name, age=age)

"{:10.2f}".format(score)    # Same formatting options
"{:>10}".format(name)       # Right align

# 3. % formatting (old, avoid)
"%s is %d years old" % (name, age)
"%.2f" % score

# Practical example
products = [
    {"name": "Laptop", "price": 999.99},
    {"name": "Mouse", "price": 29.99}
]

for product in products:
    print(f"{product['name']:<15} ${product['price']:>8.2f}")
# Output:
# Laptop          $    999.99
# Mouse           $     29.99
```

### String Methods (Performance Important)
```python
# Performance consideration: strings are immutable
# Each operation creates new string
s = "hello"
s = s.upper()      # Creates new string object
s = s.replace("E", "e")  # Creates another new string

# For many operations, use join
items = ["a", "b", "c", "d", "e"]

# ❌ Inefficient - creates 4 intermediate strings
result = ""
for item in items:
    result = result + item  # O(n²)

# ✅ Efficient - single join operation
result = "".join(items)  # O(n)

# Common pattern: collecting strings
lines = []
for data in large_dataset:
    lines.append(process(data))
output = "\n".join(lines)  # Efficient
```

---

## 4) JSON Handling (Real-World Critical)

### JSON Basics
```python
import json

# Python to JSON (serialize)
data = {
    "name": "Alice",
    "age": 30,
    "is_active": True,
    "tags": ["python", "backend"],
    "meta": None
}

json_string = json.dumps(data)
# '{"name": "Alice", "age": 30, "is_active": true, "tags": ["python", "backend"], "meta": null}'

# JSON to Python (deserialize)
parsed = json.loads(json_string)
# {'name': 'Alice', 'age': 30, ...}

# File operations
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)  # indent for pretty printing

with open("data.json", "r") as f:
    data = json.load(f)
```

### JSON Handling Best Practices
```python
# Handle invalid JSON
try:
    data = json.loads(invalid_json)
except json.JSONDecodeError as e:
    print(f"Invalid JSON: {e}")

# Type conversion
def json_serializer(obj):
    """Handle datetime and other non-JSON types"""
    if isinstance(obj, datetime):
        return obj.isoformat()
    if isinstance(obj, Decimal):
        return float(obj)
    raise TypeError(f"Type {type(obj)} not serializable")

json.dumps(data, default=json_serializer)

# Real-world API handling
import requests

response = requests.get("https://api.example.com/users")
try:
    users = response.json()
    for user in users:
        print(user["name"])
except json.JSONDecodeError:
    print("Response is not valid JSON")
except KeyError as e:
    print(f"Missing key: {e}")
```

---

## 5) Regular Expressions (Regex) - Industry Level

### Regex Basics
```python
import re

# Basic pattern matching
pattern = r"\d+"  # Digits
text = "I have 123 apples"

# search - find first match
match = re.search(pattern, text)
if match:
    print(match.group())  # "123"
    print(match.start())  # 7
    print(match.end())    # 10

# findall - find all matches
pattern = r"\d+"
matches = re.findall(pattern, "I have 123 apples and 456 oranges")
# ["123", "456"]

# match - match at start
print(re.match(r"\d+", "123abc"))   # Matches
print(re.match(r"\d+", "abc123"))   # No match

# fullmatch - entire string must match
print(re.fullmatch(r"\d+", "123"))      # Matches
print(re.fullmatch(r"\d+", "123abc"))   # No match
```

### Regex Patterns (Common)
```python
# Character classes
\d      # Digit (0-9)
\D      # Non-digit
\w      # Word character (a-z, A-Z, 0-9, _)
\W      # Non-word
\s      # Whitespace
\S      # Non-whitespace
.       # Any character (except newline)

# Quantifiers
*       # 0 or more
+       # 1 or more
?       # 0 or 1
{n}     # Exactly n
{n,m}   # Between n and m

# Examples
r"\d{3}-\d{3}-\d{4}"      # Phone number: 123-456-7890
r"\w+@\w+\.\w+"           # Email (basic): user@example.com
r"^[A-Z]"                 # Starts with uppercase
r"\.pdf$"                 # Ends with .pdf
```

### Real-World Regex Patterns
```python
# Email validation (simplified)
email_pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
emails = [
    "john@example.com",
    "invalid.email",
    "user+tag@domain.co.uk"
]

for email in emails:
    if re.match(email_pattern, email):
        print(f"Valid: {email}")
    else:
        print(f"Invalid: {email}")

# Phone number extraction
text = "Call me at 123-456-7890 or (123) 456-7890"
phones = re.findall(r"(\d{3}[-.\s]?\d{3}[-.\s]?\d{4})", text)

# URL extraction
urls = re.findall(r"https?://[^\s]+", text)

# HTML tag removal
html = "<p>Hello <b>World</b></p>"
clean = re.sub(r"<[^>]+>", "", html)  # "Hello World"

# Split with regex
text = "apple,banana;orange:grape"
items = re.split(r"[,;:]", text)  # ["apple", "banana", "orange", "grape"]

# Substitute/Replace
text = "I have 2 apples and 3 oranges"
result = re.sub(r"\d+", "X", text)  # "I have X apples and X oranges"

# Substitute with capture groups
text = "John Smith"
result = re.sub(r"(\w+) (\w+)", r"\2, \1", text)  # "Smith, John"
```

---

## 6) Dates and Time (Production Critical)

### datetime Module
```python
from datetime import datetime, date, time, timedelta

# Current date/time
now = datetime.now()
today = date.today()
current_time = datetime.now().time()

# Creating specific datetime
d = datetime(2024, 1, 15)
d = datetime(2024, 1, 15, 10, 30, 45)

# Parsing from string
date_str = "2024-01-15"
d = datetime.strptime(date_str, "%Y-%m-%d")

# Formatting to string
d = datetime.now()
formatted = d.strftime("%Y-%m-%d %H:%M:%S")  # "2024-01-15 10:30:45"
formatted = d.strftime("%d/%m/%Y")           # "15/01/2024"
formatted = d.strftime("%A, %B %d, %Y")      # "Monday, January 15, 2024"

# Common format codes
%Y      # 4-digit year (2024)
%y      # 2-digit year (24)
%m      # Month (01-12)
%d      # Day (01-31)
%H      # Hour (00-23)
%M      # Minute (00-59)
%S      # Second (00-59)
%A      # Weekday name (Monday)
%a      # Weekday abbreviation (Mon)
%B      # Month name (January)
%b      # Month abbreviation (Jan)

# Date arithmetic
d = datetime(2024, 1, 15)
tomorrow = d + timedelta(days=1)
next_week = d + timedelta(weeks=1)
in_2_hours = d + timedelta(hours=2)
in_30_seconds = d + timedelta(seconds=30)

# Date comparison
d1 = datetime(2024, 1, 15)
d2 = datetime(2024, 1, 20)

print(d1 < d2)                    # True
print((d2 - d1).days)             # 5 days

# Getting components
d = datetime(2024, 1, 15, 10, 30, 45)
d.year                            # 2024
d.month                           # 1
d.day                             # 15
d.hour                            # 10
d.minute                          # 30
d.second                          # 45
d.weekday()                       # 0 (Monday)
d.isoformat()                     # "2024-01-15T10:30:45"
```

### Timezone Handling
```python
from datetime import datetime, timezone, timedelta

# UTC datetime
utc_now = datetime.now(timezone.utc)

# Create timezone offset
est = timezone(timedelta(hours=-5))
now_est = datetime.now(est)

# Using pytz (more reliable)
from pytz import timezone as tz

utc = tz('UTC')
est = tz('US/Eastern')
ist = tz('Asia/Kolkata')

utc_time = datetime.now(utc)
est_time = utc_time.astimezone(est)
ist_time = utc_time.astimezone(ist)
```

---

## 7) None, Type Checking (Interview Questions)

### Understanding None
```python
# None is a singleton
x = None
y = None
print(x is y)  # True - same object

# None vs False vs 0 vs ""
print(None == False)  # False
print(None == 0)      # False
print(None == "")     # False
print(None is None)   # True (correct way)

# None in collections
lst = [1, 2, None, 4]
print(None in lst)    # True

d = {"key": None}
print(d["key"] is None)  # True

# Default to None
def get_user(user_id):
    try:
        return User.query.get(user_id)
    except:
        return None

user = get_user(123)
if user is not None:
    print(user.name)
```

### Type Checking
```python
# type() - returns exact type
print(type(5))          # <class 'int'>
print(type("hello"))    # <class 'str'>
print(type([1, 2]))     # <class 'list'>

# isinstance() - preferred in production (handles inheritance)
print(isinstance(5, int))                    # True
print(isinstance(5, (int, float)))          # True (tuple of types)
print(isinstance(True, int))                # True (bool is subclass of int)

# Practical: type checking in functions
def process_input(data):
    if isinstance(data, str):
        return len(data)
    elif isinstance(data, (list, tuple)):
        return len(data)
    elif isinstance(data, dict):
        return len(data)
    else:
        raise TypeError(f"Expected str/list/dict, got {type(data)}")

# Type hints (Python 3.5+, not enforced but helpful)
def add(x: int, y: int) -> int:
    """Add two numbers."""
    return x + y

# Using typing module
from typing import List, Dict, Optional, Union

def process_users(users: List[Dict[str, str]]) -> Optional[int]:
    """Process users and return count or None."""
    if users:
        return len(users)
    return None

def flexible_param(data: Union[str, int]) -> str:
    """Accept string or int."""
    return str(data)
```

---

## 8) Math Module
```python
import math

# Basic functions
math.ceil(4.2)          # 5 (round up)
math.floor(4.8)         # 4 (round down)
math.trunc(4.8)         # 4 (truncate toward zero)
round(4.5)              # 4 (banker's rounding - round to even)
round(3.5)              # 4

# Roots and powers
math.sqrt(16)           # 4.0
math.pow(2, 3)          # 8.0
2 ** 3                  # 8 (preferred)

# Trigonometry (radians)
math.sin(math.pi / 2)   # 1.0
math.cos(0)             # 1.0
math.tan(math.pi / 4)   # 1.0

# Logarithms
math.log(10)            # 2.302... (natural log)
math.log10(100)         # 2.0
math.log2(8)            # 3.0

# Constants
math.pi                 # 3.14159...
math.e                  # 2.71828...
math.inf                # Infinity
math.nan                # Not a Number

# Useful functions
math.factorial(5)       # 120
math.gcd(12, 18)        # 6 (greatest common divisor)
math.fsum([0.1, 0.2, 0.3])  # More accurate than sum()
```

---

## 9) Random Numbers
```python
import random

# Random float [0, 1)
random.random()         # 0.3719...

# Random integer
random.randint(1, 100)  # 1 to 100 inclusive
random.randrange(1, 100)  # 1 to 99 (100 exclusive)
random.randrange(0, 100, 5)  # 0, 5, 10, ... (with step)

# Random choice from sequence
random.choice([1, 2, 3, 4, 5])      # Pick one
random.choices([1, 2, 3], k=5)      # Pick 5 with replacement
random.sample([1, 2, 3, 4, 5], k=3) # Pick 3 without replacement

# Shuffle list (in-place)
items = [1, 2, 3, 4, 5]
random.shuffle(items)
print(items)  # [3, 1, 5, 2, 4] (order varies)

# Seeding for reproducibility
random.seed(42)
print(random.randint(1, 100))  # Always same with seed 42
```

---

## 10) Mini Projects: Logic Building

### Project 1: Login Validator
```python
import re

def validate_email(email):
    """Validate email format using regex."""
    pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    return bool(re.match(pattern, email))

def validate_password(password):
    """
    Validate password:
    - Minimum 8 characters
    - At least 1 digit
    - At least 1 uppercase
    - At least 1 special character
    """
    if len(password) < 8:
        return False, "Password must be at least 8 characters"
    
    if not re.search(r"\d", password):
        return False, "Password must contain at least one digit"
    
    if not re.search(r"[A-Z]", password):
        return False, "Password must contain at least one uppercase letter"
    
    if not re.search(r"[!@#$%^&*(),.?\":{}|<>]", password):
        return False, "Password must contain at least one special character"
    
    return True, "Password is valid"

def login(email, password, registered_users):
    """Attempt login with validation."""
    if not validate_email(email):
        return False, "Invalid email format"
    
    is_valid, msg = validate_password(password)
    if not is_valid:
        return False, msg
    
    if email not in registered_users:
        return False, "Email not registered"
    
    if registered_users[email] != password:
        return False, "Incorrect password"
    
    return True, "Login successful"

# Test
users = {
    "john@example.com": "SecurePass123!",
    "alice@example.com": "MyPass456@"
}

print(login("john@example.com", "SecurePass123!", users))
print(login("john@example.com", "wrong", users))
print(login("invalid-email", "SecurePass123!", users))
```

### Project 2: Ticket Analyzer (JSON + JSON + Data Processing)
```python
import json
from datetime import datetime

def analyze_tickets(filename):
    """Analyze support tickets from JSON file."""
    with open(filename, 'r') as f:
        tickets = json.load(f)
    
    if not tickets:
        return None
    
    # Count statistics
    total = len(tickets)
    open_count = sum(1 for t in tickets if t['status'] == 'open')
    closed_count = total - open_count
    
    # Priority breakdown
    priority_counts = {}
    for ticket in tickets:
        priority = ticket.get('priority', 'unknown')
        priority_counts[priority] = priority_counts.get(priority, 0) + 1
    
    # Filter recent tickets
    target_date = datetime(2024, 1, 12)
    recent = [t for t in tickets 
              if datetime.strptime(t['created'], '%Y-%m-%d') >= target_date]
    
    return {
        'total': total,
        'open': open_count,
        'closed': closed_count,
        'by_priority': priority_counts,
        'recent_count': len(recent)
    }

def generate_report(analysis):
    """Generate formatted report."""
    if not analysis:
        print("No tickets to analyze")
        return
    
    print("=" * 50)
    print("TICKET ANALYSIS REPORT")
    print("=" * 50)
    print(f"Total Tickets: {analysis['total']}")
    print(f"Open: {analysis['open']}")
    print(f"Closed: {analysis['closed']}")
    print("\nPriority Breakdown:")
    for priority, count in analysis['by_priority'].items():
        print(f"  {priority.upper()}: {count}")
    print(f"\nRecent Tickets: {analysis['recent_count']}")

# Usage
analysis = analyze_tickets("tickets.json")
generate_report(analysis)
```

### Project 3: Log Parser (File I/O + Regex + Data Processing)
```python
import re
from datetime import datetime
from collections import defaultdict

def parse_logs(log_file):
    """Parse server log file and extract information."""
    # Log format: 2024-01-10 10:30:22 ERROR Database connection failed
    log_pattern = r"(\d{4}-\d{2}-\d{2}) (\d{2}:\d{2}:\d{2}) (\w+) (.+)"
    
    logs = {
        'errors': [],
        'warnings': [],
        'info': [],
        'by_level': defaultdict(int),
        'by_hour': defaultdict(int)
    }
    
    try:
        with open(log_file, 'r') as f:
            for line in f:
                match = re.search(log_pattern, line)
                if match:
                    date_str, time_str, level, message = match.groups()
                    
                    # Extract hour for statistics
                    hour = time_str.split(':')[0]
                    logs['by_hour'][hour] += 1
                    logs['by_level'][level] += 1
                    
                    # Categorize by level
                    entry = {
                        'date': date_str,
                        'time': time_str,
                        'message': message
                    }
                    
                    if level == 'ERROR':
                        logs['errors'].append(entry)
                    elif level == 'WARNING':
                        logs['warnings'].append(entry)
                    elif level == 'INFO':
                        logs['info'].append(entry)
    except FileNotFoundError:
        print(f"Log file '{log_file}' not found")
        return None
    
    return logs

def report_errors(logs):
    """Display error report."""
    print(f"Total Errors: {len(logs['errors'])}")
    print(f"Total Warnings: {len(logs['warnings'])}")
    print(f"Total Info: {len(logs['info'])}")
    print("\nError Messages:")
    for error in logs['errors']:
        print(f"  [{error['date']} {error['time']}] {error['message']}")

# Usage
logs = parse_logs("app.log")
if logs:
    report_errors(logs)
```

---

# ADVANCED PYTHON

## 1) Functions - Complete Industry Guide

### Function Fundamentals
```python
# Basic function
def greet(name):
    """Greet a person."""
    return f"Hello, {name}!"

result = greet("Alice")

# Positional arguments
def add(a, b):
    return a + b

add(5, 3)  # Correct order matters
```

### Default Arguments
```python
def create_user(name, role="user", is_active=True):
    """Create user with default role."""
    return {"name": name, "role": role, "is_active": is_active}

create_user("John")                      # role="user", is_active=True
create_user("John", "admin")             # role="admin", is_active=True
create_user("John", "admin", False)      # All specified
create_user("John", is_active=False)     # Skip role, use keyword for is_active

# ⚠️ Mutable default argument TRAP
def append_to_list(item, target=[]):
    """❌ WRONG - mutable default"""
    target.append(item)
    return target

print(append_to_list(1))  # [1]
print(append_to_list(2))  # [1, 2] - SHARED!
print(append_to_list(3))  # [1, 2, 3] - SHARED!

# ✅ CORRECT - use None
def append_to_list(item, target=None):
    """Correct approach"""
    if target is None:
        target = []
    target.append(item)
    return target

print(append_to_list(1))  # [1]
print(append_to_list(2))  # [2]
print(append_to_list(3))  # [3]
```

### *args and **kwargs (Variable Arguments)

```python
# *args - variable number of positional arguments (tuple)
def sum_numbers(*args):
    """Sum any number of arguments."""
    print(f"Received: {args}")  # args is a tuple
    return sum(args)

print(sum_numbers(1, 2, 3, 4, 5))  # 15
print(sum_numbers(10, 20))  # 30

# **kwargs - variable number of keyword arguments (dict)
def print_user_info(**kwargs):
    """Print user information."""
    print(f"Received: {kwargs}")  # kwargs is a dict
    for key, value in kwargs.items():
        print(f"  {key}: {value}")

print_user_info(name="John", age=30, city="NYC")

# Combining *args and **kwargs
def flexible_function(required, *args, **kwargs):
    """Mix required, positional, and keyword args."""
    print(f"Required: {required}")
    print(f"Args: {args}")
    print(f"Kwargs: {kwargs}")

flexible_function(1, 2, 3, name="John", age=30)
# Required: 1
# Args: (2, 3)
# Kwargs: {'name': 'John', 'age': 30}

# Unpacking with *args/**kwargs
def add(a, b, c):
    return a + b + c

numbers = [1, 2, 3]
print(add(*numbers))  # Unpacks to add(1, 2, 3)

def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

params = {"name": "Alice", "greeting": "Hi"}
print(greet(**params))  # Unpacks to greet(name="Alice", greeting="Hi")
```

### Lambda Functions
```python
# Basic lambda
square = lambda x: x ** 2
print(square(5))  # 25

# Lambda with multiple arguments
add = lambda x, y: x + y
print(add(3, 4))  # 7

# Commonly used with map/filter/sorted
numbers = [1, 2, 3, 4, 5]

# map - apply function to each element
squared = list(map(lambda x: x ** 2, numbers))  # [1, 4, 9, 16, 25]

# filter - keep elements matching condition
evens = list(filter(lambda x: x % 2 == 0, numbers))  # [2, 4]

# sorted with lambda key
students = [
    {"name": "Alice", "score": 85},
    {"name": "Bob", "score": 92},
    {"name": "Charlie", "score": 78}
]

sorted_students = sorted(students, key=lambda s: s["score"], reverse=True)
# [{'name': 'Bob', 'score': 92}, {'name': 'Alice', 'score': 85}, ...]

# ⚠️ When NOT to use lambda
# Lambda for simple operations is fine
# For complex logic, use regular function (more readable)

# ❌ Complex lambda (hard to debug)
result = list(map(lambda x: x ** 2 if x % 2 == 0 else x * 3 if x > 5 else 0, numbers))

# ✅ Better - define function
def complex_logic(x):
    if x % 2 == 0:
        return x ** 2
    elif x > 5:
        return x * 3
    else:
        return 0

result = list(map(complex_logic, numbers))
```

### Scope (Local vs Global)
```python
global_var = "I'm global"

def my_function():
    local_var = "I'm local"
    print(global_var)   # Can access global
    print(local_var)    # Can access local

my_function()
print(global_var)       # ✅ Works
# print(local_var)      # ❌ Error - doesn't exist outside function

# Modifying global variable
counter = 0

def increment():
    global counter      # Declare as global
    counter += 1

increment()
print(counter)  # 1 (modified successfully)

# Nonlocal for nested functions
def outer():
    x = 10
    
    def inner():
        nonlocal x  # Declare as nonlocal
        x = 20
    
    inner()
    print(x)  # 20 (modified in inner scope)

outer()

# Scope resolution order (LEGB)
x = "global"

def outer_func():
    x = "enclosing"
    
    def inner_func():
        x = "local"
        print(x)  # "local" (Local)
    
    inner_func()
    print(x)  # "enclosing" (Enclosing)

outer_func()
print(x)  # "global" (Global)

# LEGB = Local, Enclosing, Global, Built-in
```

### Docstrings (Professional Standard)
```python
def calculate_compound_interest(principal, rate, time, n=1):
    """
    Calculate compound interest.
    
    Args:
        principal (float): Initial amount in currency units
        rate (float): Annual interest rate (e.g., 5 for 5%)
        time (int): Time period in years
        n (int, optional): Compounding frequency per year. Defaults to 1.
        
    Returns:
        float: Total amount after interest
        
    Raises:
        ValueError: If any parameter is negative
        TypeError: If parameters are not numeric
        
    Example:
        >>> calculate_compound_interest(1000, 5, 2)
        1102.5
    """
    if not isinstance(principal, (int, float)):
        raise TypeError("Principal must be numeric")
    
    if principal < 0 or rate < 0 or time < 0:
        raise ValueError("Parameters cannot be negative")
    
    amount = principal * (1 + rate / (100 * n)) ** (n * time)
    return amount

# Access docstring
help(calculate_compound_interest)
print(calculate_compound_interest.__doc__)
```

### Decorators (Introduction)
```python
# Simple decorator
def my_decorator(func):
    def wrapper():
        print("Something before function")
        func()
        print("Something after function")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
# Output:
# Something before function
# Hello!
# Something after function

# Decorator with arguments
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
        result = func(*args, **kwargs)
        return result
    return wrapper

@my_decorator
def add(a, b):
    return a + b

print(add(5, 3))  # Prints calling info, then returns 8

# Real-world example: timing decorator
import time
from functools import wraps

def timer(func):
    @wraps(func)  # Preserve original function metadata
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(2)
    return "Done"

slow_function()  # Prints: slow_function took 2.0001 seconds
```

---

## 2) Imports & Modules

### Import Patterns
```python
# Import entire module
import math
print(math.sqrt(16))  # 4.0

# Import specific items
from math import sqrt, pi
print(sqrt(16))  # No need for math.sqrt
print(pi)

# Import with alias
import numpy as np
from datetime import datetime as dt

# Import all (AVOID - pollutes namespace)
from math import *  # ❌ Bad practice

# Conditional imports
try:
    import numpy
    HAS_NUMPY = True
except ImportError:
    HAS_NUMPY = False

if HAS_NUMPY:
    # Use numpy
    pass
else:
    # Use alternative
    pass
```

### Module Structure
```python
# my_module.py
"""
Module docstring describing the module.
"""

# Module-level constants
DEFAULT_TIMEOUT = 30

# Module-level functions
def helper_function():
    """Helper function."""
    pass

class MyClass:
    """My custom class."""
    pass

# Main execution block
if __name__ == "__main__":
    # This runs only when script is executed directly
    # Not when imported as module
    helper_function()
    print("Running module directly")
```

### Creating Packages
```
my_package/
├── __init__.py          # Makes directory a package
├── module1.py
├── module2.py
└── subpackage/
    ├── __init__.py
    └── module3.py

# __init__.py
"""Package docstring."""

from .module1 import function1
from .module2 import Class1

__all__ = ['function1', 'Class1']  # Public API

# Usage
from my_package import function1
from my_package.module1 import other_function
from my_package.subpackage import module3
```

---

## 3) Object-Oriented Programming (OOP)

### Classes and Objects
```python
class BankAccount:
    """Represent a bank account."""
    
    def __init__(self, account_number, owner, balance=0):
        """Initialize account."""
        self.account_number = account_number
        self.owner = owner
        self.balance = balance
        self.transaction_history = []
    
    def deposit(self, amount):
        """Deposit money."""
        if amount <= 0:
            raise ValueError("Deposit must be positive")
        self.balance += amount
        self.transaction_history.append(("DEPOSIT", amount))
        return self.balance
    
    def withdraw(self, amount):
        """Withdraw money."""
        if amount <= 0:
            raise ValueError("Withdrawal must be positive")
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount
        self.transaction_history.append(("WITHDRAW", amount))
        return self.balance
    
    def get_balance(self):
        """Get current balance."""
        return self.balance

# Usage
account = BankAccount("ACC001", "John", 5000)
print(account.deposit(1000))     # 6000
print(account.withdraw(500))     # 5500
print(account.get_balance())     # 5500
```

### Instance vs Class Variables
```python
class Car:
    wheels = 4  # Class variable - shared by all instances
    
    def __init__(self, brand, model):
        self.brand = brand  # Instance variable
        self.model = model
        self.speed = 0      # Instance variable
    
    def drive(self):
        self.speed = 100

car1 = Car("Toyota", "Camry")
car2 = Car("Honda", "Civic")

print(car1.wheels)  # 4 (from class)
print(car2.wheels)  # 4 (from class)

print(car1.brand)   # "Toyota" (instance)
print(car2.brand)   # "Honda" (instance)

# Modifying class variable affects all instances
Car.wheels = 5
print(car1.wheels)  # 5
print(car2.wheels)  # 5

# But reassigning to instance variable creates instance variable
car1.wheels = 6
print(car1.wheels)  # 6 (instance variable)
print(car2.wheels)  # 5 (still class variable)
```

### Inheritance
```python
# Parent class
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        return f"{self.name} makes a sound"

# Child class
class Dog(Animal):
    def speak(self):  # Override parent method
        return f"{self.name} barks: Woof! Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} meows: Meow!"

# Usage
dog = Dog("Buddy")
cat = Cat("Whiskers")

print(dog.speak())  # "Buddy barks: Woof! Woof!"
print(cat.speak())  # "Whiskers meows: Meow!"

# Method Resolution Order (MRO)
print(Dog.__mro__)  # (<class 'Dog'>, <class 'Animal'>, <class 'object'>)
```

### Polymorphism
```python
class Shape:
    def area(self):
        raise NotImplementedError

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14 * self.radius ** 2

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height

# Polymorphic behavior
shapes = [Circle(5), Rectangle(4, 6)]

for shape in shapes:
    print(shape.area())  # Calls appropriate method for each type
# Output:
# 78.5
# 24
```

### Encapsulation
```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.__balance = balance  # Private (double underscore)
        self._pin = "1234"        # Protected (single underscore - convention)
    
    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount
    
    def get_balance(self):
        """Public method to access private attribute."""
        return self.__balance
    
    # Using property decorator for cleaner access
    @property
    def balance(self):
        """Getter for balance."""
        return self.__balance
    
    @balance.setter
    def balance(self, value):
        """Setter for balance."""
        if value >= 0:
            self.__balance = value
        else:
            raise ValueError("Balance cannot be negative")

account = BankAccount("John", 5000)
print(account.balance)          # 5000 (via property)
account.balance = 6000          # Via property setter

# Name mangling (Python's privacy mechanism)
# print(account.__balance)      # ❌ AttributeError
print(account._BankAccount__balance)  # ✅ Works but BAD practice
```

### @classmethod and @staticmethod
```python
class User:
    count = 0
    
    def __init__(self, name):
        self.name = name
        User.count += 1
    
    # Instance method - first arg is 'self'
    def display(self):
        print(f"User: {self.name}")
    
    # Class method - first arg is 'cls' (the class itself)
    @classmethod
    def from_string(cls, user_string):
        """Create user from string 'name,age'."""
        name = user_string.split(",")[0]
        return cls(name)
    
    @classmethod
    def get_count(cls):
        """Get total user count."""
        return cls.count
    
    # Static method - no 'self' or 'cls'
    @staticmethod
    def validate_name(name):
        """Check if name is valid."""
        return len(name) > 0 and name.isalpha()

# Usage
user1 = User("Alice")
user2 = User.from_string("Bob,30")  # Create from string

print(User.get_count())              # 2
print(User.validate_name("John"))    # True
```

### Magic Methods
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    # String representation (used by print, str())
    def __str__(self):
        return f"Person(name='{self.name}', age={self.age})"
    
    # Developer representation (used by repr())
    def __repr__(self):
        return f"Person(name='{self.name}', age={self.age})"
    
    # Length
    def __len__(self):
        return self.age  # Example: length is age
    
    # Equality
    def __eq__(self, other):
        if not isinstance(other, Person):
            return False
        return self.age == other.age
    
    # Less than (for sorting)
    def __lt__(self, other):
        return self.age < other.age
    
    # Addition
    def __add__(self, years):
        return Person(self.name, self.age + years)
    
    # String representation for iterating
    def __iter__(self):
        return iter([self.name, self.age])
    
    # Indexing
    def __getitem__(self, index):
        if index == 0:
            return self.name
        elif index == 1:
            return self.age
        else:
            raise IndexError("Index out of range")

# Usage
p1 = Person("Alice", 30)
p2 = Person("Bob", 25)

print(str(p1))       # Person(name='Alice', age=30)
print(len(p1))       # 30
print(p1 == p1)      # True
print(p1 < p2)       # False
p3 = p1 + 5          # Person(name='Alice', age=35)
print(p1[0])         # Alice
```

---

## 4) Exception Handling

### try/except/finally
```python
# Basic exception handling
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")

# Multiple exceptions
try:
    value = int("abc")
except ValueError:
    print("Invalid integer")
except TypeError:
    print("Type error")
except Exception as e:
    print(f"Unexpected error: {e}")

# Exception with information
try:
    file = open("nonexistent.txt", "r")
except FileNotFoundError as e:
    print(f"File error: {e}")
    print(f"Error type: {type(e)}")

# finally - always executes
try:
    file = open("data.txt", "r")
    data = file.read()
except FileNotFoundError:
    print("File not found")
finally:
    file.close()  # Ensures file is closed

# else - executes if no exception
try:
    value = int("123")
except ValueError:
    print("Invalid")
else:
    print(f"Converted successfully: {value}")
```

### Raising Exceptions
```python
# Raise built-in exception
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError("Insufficient funds")
    return balance - amount

# Custom exception
class InsufficientBalanceError(Exception):
    """Raised when balance is insufficient."""
    pass

class InvalidAmountError(Exception):
    """Raised when amount is invalid."""
    pass

def withdraw_custom(balance, amount):
    if amount <= 0:
        raise InvalidAmountError("Amount must be positive")
    if amount > balance:
        raise InsufficientBalanceError(f"Balance {balance} < Amount {amount}")
    return balance - amount

# Usage
try:
    withdraw_custom(100, 50)
except InsufficientBalanceError as e:
    print(f"Balance error: {e}")
except InvalidAmountError as e:
    print(f"Amount error: {e}")
```

---

## 5) File Handling & Context Managers

### File Operations
```python
# Writing to file
with open("data.txt", "w") as f:
    f.write("Hello World\n")
    f.writelines(["Line 1\n", "Line 2\n", "Line 3\n"])

# Reading from file
with open("data.txt", "r") as f:
    content = f.read()              # Entire file
    lines = f.readlines()           # List of lines

# Appending to file
with open("data.txt", "a") as f:
    f.write("Appended line\n")

# Reading line by line (memory efficient)
with open("data.txt", "r") as f:
    for line in f:
        print(line.strip())

# File modes
"r"     # Read (default)
"w"     # Write (overwrites)
"a"     # Append
"x"     # Create (error if exists)
"b"     # Binary mode (e.g., "rb", "wb")
"+"     # Read and write (e.g., "r+", "w+")
```

### CSV Handling
```python
import csv

# Writing CSV
data = [
    ["Name", "Age", "Email"],
    ["Alice", "30", "alice@example.com"],
    ["Bob", "25", "bob@example.com"]
]

with open("users.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerows(data)

# Reading CSV
with open("users.csv", "r") as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)

# Using DictReader (easier)
with open("users.csv", "r") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row)  # {'Name': 'Alice', 'Age': '30', ...}
```

### Context Managers
```python
# The 'with' statement is context manager
# Ensures resources are properly closed

# File example
with open("file.txt", "r") as f:
    content = f.read()
# File automatically closed

# Creating custom context manager
class DatabaseConnection:
    def __enter__(self):
        print("Connecting to database...")
        return self  # This becomes the 'as' variable
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Closing database connection...")
        return False  # Don't suppress exceptions

# Usage
with DatabaseConnection() as db:
    print("Using database...")
    # Automatically calls __exit__ when done
```

---

## 6) Comprehensions & Generators

### List Comprehensions
```python
# Basic
squares = [x ** 2 for x in range(5)]  # [0, 1, 4, 9, 16]

# With condition
evens = [x for x in range(10) if x % 2 == 0]  # [0, 2, 4, 6, 8]

# Multiple conditions
evens_above_5 = [x for x in range(10) if x % 2 == 0 if x > 5]  # [6, 8]

# Nested
matrix = [[j for j in range(3)] for i in range(3)]

# From existing list
words = ["hello", "world", "python"]
uppercase = [w.upper() for w in words]  # ["HELLO", "WORLD", "PYTHON"]

# Conditional expression
result = [x if x % 2 == 0 else -x for x in range(-3, 4)]  # [3, -1, 0, 2, -3, 4, -5]
```

### Dictionary Comprehensions
```python
# Create dict from list
numbers = [1, 2, 3, 4, 5]
squares_dict = {x: x ** 2 for x in numbers}  # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# From existing dict
users = {"alice": 30, "bob": 25, "charlie": 35}
adults = {k: v for k, v in users.items() if v >= 30}  # {"alice": 30, "charlie": 35}

# Swapping keys and values
mapping = {"a": 1, "b": 2, "c": 3}
reversed_mapping = {v: k for k, v in mapping.items()}  # {1: "a", 2: "b", 3: "c"}

# From zip
keys = ["name", "age", "city"]
values = ["John", "30", "NYC"]
user_dict = {k: v for k, v in zip(keys, values)}  # {"name": "John", "age": "30", "city": "NYC"}
```

### Set Comprehensions
```python
numbers = [1, 2, 2, 3, 3, 3, 4]
unique = {x for x in numbers}  # {1, 2, 3, 4}

# Conditionals
evens = {x for x in range(10) if x % 2 == 0}  # {0, 2, 4, 6, 8}

# Transformations
squares = {x ** 2 for x in range(1, 6)}  # {1, 4, 9, 16, 25}
```

### Generators
```python
# Generator function
def count_up(n):
    """Generate numbers from 1 to n."""
    current = 0
    while current < n:
        current += 1
        yield current  # Pause here, return value

# Usage
for num in count_up(3):
    print(num)  # 1, 2, 3

# Generator expression (like list comprehension but lazy)
squares_gen = (x ** 2 for x in range(1000000))  # Doesn't create list

# Advantages
# - Memory efficient (yields one value at a time)
# - Can generate infinite sequences
# - Lazy evaluation (computes when needed)

# Infinite generator
def infinite_counter():
    n = 0
    while True:
        yield n
        n += 1

counter = infinite_counter()
print(next(counter))  # 0
print(next(counter))  # 1
print(next(counter))  # 2
# Doesn't crash - only generates what's asked for
```

---

## 7) Concurrency Basics

### Threading (I/O-Bound Tasks)
```python
import threading
import time

def download_file(filename):
    print(f"Downloading {filename}...")
    time.sleep(2)  # Simulates I/O (network request)
    print(f"Downloaded {filename}")

# Without threading (sequential) - 6 seconds
start = time.time()
download_file("file1.txt")
download_file("file2.txt")
download_file("file3.txt")
print(f"Total time: {time.time() - start:.2f}s")  # ~6 seconds

# With threading (concurrent) - 2 seconds
start = time.time()
threads = []
for i in range(3):
    t = threading.Thread(target=download_file, args=(f"file{i}.txt",))
    threads.append(t)
    t.start()

# Wait for all threads
for t in threads:
    t.join()

print(f"Total time: {time.time() - start:.2f}s")  # ~2 seconds
```

### Multiprocessing (CPU-Bound Tasks)
```python
from multiprocessing import Pool
import math

def calculate_factorial(n):
    return math.factorial(n)

# Without multiprocessing
start = time.time()
results = [calculate_factorial(n) for n in range(1, 50000)]
print(f"Time: {time.time() - start:.2f}s")  # Slow

# With multiprocessing
if __name__ == "__main__":
    start = time.time()
    with Pool(processes=4) as pool:
        results = pool.map(calculate_factorial, range(1, 50000))
    print(f"Time: {time.time() - start:.2f}s")  # Faster

# Why threading doesn't help for CPU tasks
# - Python GIL (Global Interpreter Lock) prevents true parallelism
# - Only one thread runs Python code at a time
# - Multiprocessing uses separate processes (bypass GIL)
```

---

## 8) Decorators (Advanced)

### Decorator with Arguments
```python
def repeat(times):
    """Decorator that repeats function execution."""
    def decorator(func):
        def wrapper(*args, **kwargs):
            results = []
            for _ in range(times):
                result = func(*args, **kwargs)
                results.append(result)
            return results
        return wrapper
    return decorator

@repeat(times=3)
def greet(name):
    return f"Hello, {name}!"

print(greet("Alice"))  # ["Hello, Alice!", "Hello, Alice!", "Hello, Alice!"]

# Stacking decorators
def uppercase(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result.upper() if isinstance(result, str) else result
    return wrapper

@uppercase
@repeat(times=2)
def greeting(name):
    return f"hello {name}"

print(greeting("Alice"))
# ["HELLO ALICE!", "HELLO ALICE!"]
```

### Preserving Function Metadata
```python
from functools import wraps

def timer(func):
    @wraps(func)  # Preserves original function's metadata
    def wrapper(*args, **kwargs):
        import time
        start = time.time()
        result = func(*args, **kwargs)
        print(f"Took {time.time() - start:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    """This is a slow function."""
    time.sleep(1)
    return "Done"

print(slow_function.__name__)  # "slow_function" (not "wrapper")
print(slow_function.__doc__)   # "This is a slow function."
```

---

## 9) Logging Basics

```python
import logging

# Configure logging
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('app.log'),
        logging.StreamHandler()
    ]
)

logger = logging.getLogger(__name__)

# Log levels (from least to most severe)
logger.debug("Debug message")           # Detailed diagnostic info
logger.info("Info message")             # General informational messages
logger.warning("Warning message")       # Something unexpected
logger.error("Error message")           # Error occurred
logger.critical("Critical message")     # Very serious error

# Example in real code
try:
    result = 10 / 0
except ZeroDivisionError as e:
    logger.error(f"Division error: {e}", exc_info=True)

# Best practices
# - Use __name__ to get logger name automatically
# - Log at appropriate levels
# - Don't log passwords or sensitive data
# - Use exc_info=True to log exceptions with traceback
```

---

# SQL DATABASES

## 1) Database Fundamentals

### RDBMS Concept
```
Relational Database = Data organized in TABLES
Table = Related records (like spreadsheet)
Row = Single record
Column = Field/Attribute

Example:
TABLE: users
┌────┬───────┬──────────┐
│ id │ name  │ email    │
├────┼───────┼──────────┤
│ 1  │ Alice │ a@ex.com │
│ 2  │ Bob   │ b@ex.com │
└────┴───────┴──────────┘
```

### Schema Definition
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 2) Keys & Constraints (Industry Critical)

### Primary Key
```sql
-- Single column primary key
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL
);

-- Composite primary key
CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id)
);

-- Why: Uniquely identifies each row
-- Ensures no duplicate records
```

### Foreign Key
```sql
CREATE TABLE orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES users(id)
        ON DELETE CASCADE  -- If user deleted, delete orders
        ON UPDATE CASCADE
);

-- Why: Maintains referential integrity
-- Prevents orphaned records
-- Enforces relationships
```

### Constraints
```sql
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    
    -- NOT NULL: Must have value
    name VARCHAR(100) NOT NULL,
    
    -- UNIQUE: No duplicates
    sku VARCHAR(50) UNIQUE,
    
    -- DEFAULT: Default value
    category VARCHAR(50) DEFAULT 'General',
    
    -- CHECK: Value validation
    price DECIMAL(10, 2) CHECK (price > 0),
    
    -- INDEX: Faster queries
    INDEX (name)
);
```

---

## 3) SQL Data Types

```sql
-- Numeric
INT, BIGINT, SMALLINT   -- Integers (different ranges)
FLOAT, DECIMAL(10, 2)   -- Decimals (DECIMAL for precision)

-- String
VARCHAR(50)             -- Variable length (up to 50)
CHAR(10)                -- Fixed length (always 10)
TEXT                    -- Long text

-- Datetime
DATE                    -- Only date
TIME                    -- Only time
DATETIME                -- Date + time
TIMESTAMP               -- Date + time + timezone

-- Boolean
BOOLEAN                 -- True/False

-- Special (PostgreSQL)
JSON, JSONB             -- Store JSON data
UUID                    -- Universal unique identifier
```

---

## 4) CRUD Operations (Complete)

### CREATE (Insert)
```sql
-- Single insert
INSERT INTO users (name, email, age) 
VALUES ('John', 'john@example.com', 30);

-- Multiple insert
INSERT INTO users (name, email, age) VALUES
('Alice', 'alice@example.com', 28),
('Bob', 'bob@example.com', 35),
('Charlie', 'charlie@example.com', 25);

-- Insert from select
INSERT INTO users_backup 
SELECT * FROM users WHERE age > 30;
```

### READ (Select)
```sql
-- All rows and columns
SELECT * FROM users;

-- Specific columns
SELECT name, email FROM users;

-- With WHERE clause
SELECT * FROM users WHERE age > 25;
SELECT * FROM users WHERE age > 25 AND city = 'NYC';
SELECT * FROM users WHERE status IN ('active', 'pending');

-- Distinct values
SELECT DISTINCT city FROM users;

-- With ORDER BY
SELECT * FROM users ORDER BY age DESC;
SELECT * FROM users ORDER BY city, name;

-- LIMIT and OFFSET
SELECT * FROM users LIMIT 10;           -- First 10
SELECT * FROM users LIMIT 10 OFFSET 20; -- Skip 20, take 10 (page 3)
```

### UPDATE
```sql
-- Update specific rows
UPDATE users SET age = 31 WHERE name = 'John';

-- Multiple columns
UPDATE users SET 
    age = 26, 
    city = 'LA' 
WHERE id = 1;

-- Conditional updates
UPDATE products SET 
    price = price * 1.1 
WHERE category = 'Electronics';
```

### DELETE
```sql
-- Delete specific rows
DELETE FROM users WHERE age < 18;

-- Delete all (dangerous!)
DELETE FROM users;

-- Safe pattern
DELETE FROM users 
WHERE email LIKE '%old@%';
```

---

## 5) Filtering & Sorting

### WHERE Clause
```sql
SELECT * FROM users WHERE age > 25;
SELECT * FROM users WHERE age > 25 AND city = 'NYC';
SELECT * FROM users WHERE age < 65 OR role = 'admin';
SELECT * FROM users WHERE NOT (age > 65);
```

### Comparison Operators
```sql
=       -- Equal
!=      -- Not equal
>       -- Greater than
<       -- Less than
>=      -- Greater or equal
<=      -- Less or equal
BETWEEN -- Range
IN      -- Multiple values
LIKE    -- Pattern matching
```

### BETWEEN, IN, LIKE
```sql
-- BETWEEN (inclusive)
SELECT * FROM orders WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';

-- IN
SELECT * FROM users WHERE status IN ('active', 'pending', 'trial');

-- LIKE (pattern matching)
SELECT * FROM users WHERE email LIKE '%@gmail.com';          -- Ends with
SELECT * FROM users WHERE name LIKE 'John%';                -- Starts with
SELECT * FROM users WHERE email LIKE '%john%';              -- Contains
SELECT * FROM users WHERE phone LIKE '555-____';            -- _ matches one char
```

### ORDER BY & LIMIT
```sql
-- Single column
SELECT * FROM users ORDER BY age;

-- Multiple columns
SELECT * FROM users ORDER BY department, salary DESC;

-- Pagination
SELECT * FROM users 
ORDER BY id 
LIMIT 10 OFFSET 20;  -- Page 3 (assuming 10 per page)

-- Get top 5
SELECT * FROM orders 
ORDER BY total_amount DESC 
LIMIT 5;
```

---

## 6) Aggregate Functions

### COUNT, SUM, AVG, MIN, MAX
```sql
-- COUNT
SELECT COUNT(*) FROM users;                    -- Total records
SELECT COUNT(email) FROM users;                -- Non-NULL emails
SELECT COUNT(DISTINCT city) FROM users;       -- Unique cities

-- SUM
SELECT SUM(amount) FROM orders;               -- Total sales

-- AVG
SELECT AVG(salary) FROM employees;            -- Average salary

-- MIN/MAX
SELECT MIN(price), MAX(price) FROM products;

-- Combined
SELECT 
    COUNT(*) as total_orders,
    SUM(amount) as total_sales,
    AVG(amount) as avg_order,
    MIN(amount) as min_order,
    MAX(amount) as max_order
FROM orders;
```

### GROUP BY & HAVING
```sql
-- Group by single column
SELECT category, COUNT(*) as count
FROM products
GROUP BY category;

-- Group by multiple columns
SELECT 
    department, 
    position, 
    COUNT(*) as count,
    AVG(salary) as avg_salary
FROM employees
GROUP BY department, position;

-- HAVING (filter groups)
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 50000;  -- Only departments with avg salary > 50000

-- Complex aggregation
SELECT 
    YEAR(order_date) as year,
    MONTH(order_date) as month,
    SUM(amount) as monthly_total,
    COUNT(*) as order_count
FROM orders
GROUP BY YEAR(order_date), MONTH(order_date)
HAVING SUM(amount) > 10000
ORDER BY year, month;
```

---

## 7) Joins (Critical)

### INNER JOIN
```sql
SELECT users.name, orders.order_date, orders.amount
FROM users
INNER JOIN orders ON users.id = orders.user_id;

-- Only rows that match in BOTH tables
```

### LEFT JOIN
```sql
SELECT users.name, COUNT(orders.id) as order_count
FROM users
LEFT JOIN orders ON users.id = orders.user_id
GROUP BY users.id;

-- All users, even with NO orders (NULL for orders)
```

### RIGHT JOIN
```sql
SELECT u.name, o.amount
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;

-- All orders, even if user not found
```

### FULL JOIN
```sql
SELECT u.name, o.order_date
FROM users u
FULL JOIN orders o ON u.id = o.user_id;

-- All rows from BOTH tables
-- NULL where no match
```

### Self Join
```sql
-- Find employees with same salary
SELECT 
    e1.name as employee,
    e2.name as colleague,
    e1.salary
FROM employees e1
JOIN employees e2 ON e1.salary = e2.salary 
    AND e1.id != e2.id;
```

---

## 8) Subqueries

### Basic Subquery
```sql
-- Subquery in WHERE
SELECT * FROM products
WHERE price > (
    SELECT AVG(price) FROM products
);

-- Subquery in FROM
SELECT avg_price FROM (
    SELECT AVG(price) as avg_price
    FROM products
    GROUP BY category
) subquery;
```

### Correlated Subquery
```sql
-- Get highest salary employee per department
SELECT e1.name, e1.salary
FROM employees e1
WHERE e1.salary = (
    SELECT MAX(salary)
    FROM employees e2
    WHERE e1.department = e2.department
);
```

---

## 9) Indexes & Performance

### Creating Indexes
```sql
-- Single column index
CREATE INDEX idx_email ON users(email);

-- Composite index (multiple columns)
CREATE INDEX idx_dept_salary ON employees(department, salary);

-- Unique index
CREATE UNIQUE INDEX idx_sku ON products(sku);

-- Drop index
DROP INDEX idx_email;
```

### When to Use Indexes
✅ Use for:
- WHERE clause frequently
- JOIN conditions
- ORDER BY columns
- Large tables

❌ Avoid for:
- Small tables (< 1000 rows)
- Columns with few unique values
- Columns frequently updated (index maintenance overhead)

---

## 10) Transactions & ACID

```sql
-- Basic transaction
BEGIN TRANSACTION;
    UPDATE accounts SET balance = balance - 100 WHERE id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- With rollback
BEGIN;
    UPDATE users SET status = 'inactive' WHERE age > 80;
    IF (some_error) THEN
        ROLLBACK;
    ELSE
        COMMIT;
    END IF;

-- Why ACID is important
-- Atomicity: Transfer money - all or nothing
-- Consistency: Account totals always correct
-- Isolation: Concurrent transfers don't interfere
-- Durability: Committed transactions survive crashes
```

---

# NOSQL DATABASES (MongoDB)

## 1) NoSQL vs SQL Quick Comparison

| Aspect | SQL | NoSQL |
|--------|-----|-------|
| Structure | Tables | Documents |
| Schema | Fixed | Flexible |
| Scaling | Vertical | Horizontal |
| Relationships | Foreign keys | References |
| Transactions | ACID | BASE (eventually consistent) |
| Best For | Complex queries | High volume, flexible structure |
| Example | MySQL, PostgreSQL | MongoDB, Redis |

---

## 2) MongoDB Architecture

### Core Concepts
```
Database → Collections → Documents

Database: "ecommerce"
Collection: "products"
Document: {
    "_id": ObjectId(...),
    "name": "Laptop",
    "price": 999.99,
    "tags": ["electronics", "computers"]
}
```

---

## 3) CRUD with PyMongo

### Insert
```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017")
db = client["my_database"]
users = db["users"]

# Insert one
result = users.insert_one({"name": "John", "email": "john@example.com"})
print(result.inserted_id)

# Insert many
users.insert_many([
    {"name": "Alice", "email": "alice@example.com"},
    {"name": "Bob", "email": "bob@example.com"}
])
```

### Find
```python
# Find all
all_users = users.find()
for user in all_users:
    print(user)

# Find one
user = users.find_one({"email": "john@example.com"})

# Find with filter
results = users.find({"age": {"$gt": 25}})  # age > 25

# Projection (select fields)
users.find({}, {"name": 1, "email": 1, "_id": 0})  # Only name, email
```

### Update
```python
# Update one
users.update_one(
    {"email": "john@example.com"},
    {"$set": {"age": 31}}
)

# Update multiple
users.update_many(
    {"status": "inactive"},
    {"$set": {"status": "active"}}
)

# Operators
{"$set": {"age": 25}}           # Set field
{"$inc": {"score": 10}}         # Increment
{"$push": {"tags": "new"}}      # Add to array
{"$pull": {"tags": "old"}}      # Remove from array
```

### Delete
```python
# Delete one
users.delete_one({"email": "john@example.com"})

# Delete many
users.delete_many({"status": "archived"})
```

---

## 4) Query Operators

```python
# Comparison
{"age": {"$eq": 30}}        # Equal
{"age": {"$gt": 25}}        # Greater than
{"age": {"$gte": 25}}       # Greater or equal
{"age": {"$lt": 30}}        # Less than
{"age": {"$lte": 30}}       # Less or equal
{"age": {"$ne": 30}}        # Not equal

# Logical
{"$and": [{"age": {"$gt": 25}}, {"city": "NYC"}]}
{"$or": [{"role": "admin"}, {"role": "moderator"}]}
{"$not": {"age": {"$lt": 18}}}

# Array
{"tags": {"$in": ["python", "javascript"]}}  # Any of these
{"tags": "python"}                           # Contains
{"tags": {"$all": ["python", "web"]}}        # Contains all

# Existence
{"phone": {"$exists": True}}       # Field exists
{"email": {"$exists": False}}      # Field missing
{"type": {"$type": "string"}}      # Is string type
```

---

## 5) Aggregation Framework

```python
# Example: Get sales by month
pipeline = [
    {"$match": {"status": "completed"}},
    {"$group": {
        "_id": {"$month": "$order_date"},
        "total": {"$sum": "$amount"},
        "count": {"$sum": 1}
    }},
    {"$sort": {"_id": 1}}
]

results = orders.aggregate(pipeline)

# Stages
$match      # Filter documents
$group      # Group and aggregate
$project    # Select/transform fields
$sort       # Sort
$limit      # Limit results
$skip       # Skip records
$lookup     # Join collections
$unwind     # Flatten array field
```

---

# FLASK FRAMEWORK

## 1) Flask Basics

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, World!"

@app.route("/users/<int:user_id>")
def get_user(user_id):
    return {"user_id": user_id}

if __name__ == "__main__":
    app.run(debug=True, port=5000)
```

---

## 2) Routing & HTTP Methods

```python
@app.route("/items", methods=["GET"])
def list_items():
    return {"items": []}

@app.route("/items", methods=["POST"])
def create_item():
    data = request.json
    return {"message": "Created", "data": data}, 201

@app.route("/items/<int:item_id>", methods=["GET"])
def get_item(item_id):
    return {"item_id": item_id}

@app.route("/items/<int:item_id>", methods=["PUT"])
def update_item(item_id):
    data = request.json
    return {"message": "Updated", "id": item_id}

@app.route("/items/<int:item_id>", methods=["DELETE"])
def delete_item(item_id):
    return {"message": "Deleted"}, 204
```

---

## 3) Database Integration (SQLAlchemy)

```python
from flask_sqlalchemy import SQLAlchemy

app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://user:pass@localhost/db'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(100), unique=True)

@app.route("/users", methods=["POST"])
def create_user():
    data = request.json
    user = User(name=data['name'], email=data['email'])
    db.session.add(user)
    db.session.commit()
    return {"id": user.id}
```

---

# FASTAPI FRAMEWORK

## 1) FastAPI Basics

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float

@app.get("/")
def root():
    return {"message": "Hello"}

@app.get("/items/{item_id}")
def get_item(item_id: int):
    return {"item_id": item_id}

@app.post("/items")
def create_item(item: Item):
    return {"created": item}
```

---

## 2) Pydantic Models & Validation

```python
from pydantic import BaseModel, EmailStr, Field

class UserCreate(BaseModel):
    name: str = Field(min_length=3)
    email: EmailStr
    age: int = Field(ge=0, le=150)

@app.post("/users")
def create_user(user: UserCreate):
    # Automatic validation
    return {"created": user}
```

---

## 3) Dependency Injection & JWT

```python
from fastapi import Depends, HTTPException
from jose import jwt

def get_current_user(token: str = Header()):
    try:
        payload = jwt.decode(token, "secret", algorithms=["HS256"])
        return {"user_id": payload["sub"]}
    except:
        raise HTTPException(status_code=401)

@app.get("/profile")
def get_profile(current_user = Depends(get_current_user)):
    return {"user": current_user}
```

---

# GIT & VERSION CONTROL

## 1) Git Basics

```bash
git init                    # Create repo
git add file.py            # Stage file
git commit -m "message"    # Commit
git log                    # View history
```

## 2) Branching

```bash
git branch feature          # Create branch
git checkout feature        # Switch branch
git checkout -b feature     # Create and switch
git merge feature           # Merge into current
git branch -d feature       # Delete
```

## 3) Remote

```bash
git remote add origin <url>
git push origin main
git pull origin main
```

---

# TEST PREPARATION & PRACTICE

## Section A: Theory Questions (Beginner → Advanced)

### Python Fundamentals

**Q1: What's the difference between list and tuple?**
Answer:
- Lists are mutable (changeable), tuples are immutable
- Lists use `[]`, tuples use `()`
- Tuples can be dict keys, lists cannot
- Tuples slightly faster for iteration

**Q2: What does `*args` do?**
Answer: Collects variable number of positional arguments as a tuple

**Q3: What's a decorator?**
Answer: A function that wraps another function to modify its behavior without changing the original

**Q4: What is GIL in Python?**
Answer: Global Interpreter Lock - prevents true parallelism in threading, but multiprocessing bypasses it

### Logic Building

**Q5: Explain the difference between `break`, `continue`, and `pass`**
Answer:
- `break`: exits loop completely
- `continue`: skips current iteration
- `pass`: placeholder, does nothing

**Q6: What does `else` clause do in a loop?**
Answer: Executes if loop completes normally (without `break`)

### Advanced Python

**Q7: What's the difference between `==` and `is`?**
Answer:
- `==`: compares values
- `is`: compares object identity (memory address)

**Q8: Explain shallow copy vs deep copy**
Answer:
- Shallow: copies references, nested objects still shared
- Deep: recursively copies all levels

### SQL

**Q9: What's the purpose of PRIMARY KEY?**
Answer: Uniquely identifies each row, ensures no duplicates

**Q10: Explain INNER JOIN vs LEFT JOIN**
Answer:
- INNER JOIN: only matching rows from both tables
- LEFT JOIN: all rows from left table, matching from right

### NoSQL

**Q11: What's a document in MongoDB?**
Answer: A JSON-like object with key-value pairs (BSON format)

**Q12: Explain embedded vs referenced documents**
Answer:
- Embedded: nested document (denormalized)
- Referenced: document ID pointing to another document (normalized)

### Flask/FastAPI

**Q13: What's a route in Flask?**
Answer: Maps URL to function, defines endpoint

**Q14: What's Pydantic in FastAPI?**
Answer: Validates request data types and constraints automatically

### Git

**Q15: What does `git stash` do?**
Answer: Temporarily saves uncommitted changes without committing

---

## Section B: Coding Questions (Easy → Hard)

### Easy Level

**Q1: Write a function to find the largest number in a list**
```python
def find_largest(numbers):
    return max(numbers)

# Or manual
def find_largest(numbers):
    if not numbers:
        return None
    largest = numbers[0]
    for num in numbers[1:]:
        if num > largest:
            largest = num
    return largest
```

**Q2: Reverse a string without using built-in methods**
```python
def reverse_string(s):
    return s[::-1]

# Or manual
def reverse_string(s):
    result = ""
    for char in s:
        result = char + result
    return result
```

**Q3: Count frequency of elements**
```python
def count_frequency(items):
    freq = {}
    for item in items:
        freq[item] = freq.get(item, 0) + 1
    return freq

# Or using Counter
from collections import Counter
def count_frequency(items):
    return dict(Counter(items))
```

### Medium Level

**Q4: Find duplicates in a list**
```python
def find_duplicates(items):
    seen = set()
    duplicates = set()
    for item in items:
        if item in seen:
            duplicates.add(item)
        seen.add(item)
    return list(duplicates)
```

**Q5: Validate email using regex**
```python
import re

def is_valid_email(email):
    pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    return bool(re.match(pattern, email))
```

**Q6: Parse JSON and filter data**
```python
import json

def filter_users(json_file, min_age):
    with open(json_file, 'r') as f:
        users = json.load(f)
    return [u for u in users if u.get('age', 0) >= min_age]
```

### Hard Level

**Q7: Design a cache with LRU eviction**
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key):
        if key not in self.cache:
            return None
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)
```

**Q8: Merge sorted arrays**
```python
def merge_sorted_arrays(arr1, arr2):
    result = []
    i = j = 0
    
    while i < len(arr1) and j < len(arr2):
        if arr1[i] <= arr2[j]:
            result.append(arr1[i])
            i += 1
        else:
            result.append(arr2[j])
            j += 1
    
    result.extend(arr1[i:])
    result.extend(arr2[j:])
    return result
```

---

## Practice Set 1: Python Fundamentals (45 minutes)

1. Write a function that converts temperature Celsius to Fahrenheit
2. Create a list of even numbers from 1 to 100 using comprehension
3. Find the second largest number in a list
4. Count vowels in a string
5. Check if a string is palindrome

---

## Practice Set 2: Logic & Data Processing (60 minutes)

1. Parse CSV file and convert to JSON
2. Group data by date and calculate statistics
3. Validate user input (email, phone, password)
4. Sort dictionary by values
5. Find common elements in multiple lists

---

# MINI PROJECTS

## Project 1: Ticket Analysis System

**Objectives**: Practice JSON, regex, data processing, file I/O

**Features**:
- Read tickets from JSON file
- Analyze by priority, status, date
- Generate reports
- Export filtered results

[Full code in projects section above]

---

## Project 2: User Management CLI

**Objectives**: Practice OOP, file handling, CLI arguments, CRUD

**Features**:
- Add/list/update/delete users
- Store in JSON file
- Validate input
- CLI interface with argparse

[Full code in projects section above]

---

# FULL INDUSTRY PROJECT: E-Commerce API

[Complete FastAPI + SQLAlchemy + MongoDB + JWT + Celery project provided in projects section]

---

# INTERVIEW CHEAT SHEET

## Python Quick Facts

| Concept | Quick Answer |
|---------|--------------|
| Mutable vs Immutable | Lists mutable, tuples immutable |
| PEP 8 | Python style guide, 4 spaces indent |
| `==` vs `is` | Equals compares value, `is` checks identity |
| GIL | Global Interpreter Lock, prevents threading parallelism |
| Generators | Yield values lazily, memory efficient |
| Decorators | Functions that modify other functions |
| *args/**kwargs | Variable positional/keyword arguments |
| Comprehensions | List/dict/set creation with inline logic |

## SQL Quick Facts

| Concept | Quick Answer |
|---------|--------------|
| ACID | Atomicity, Consistency, Isolation, Durability |
| Normalization | Remove redundancy, improve efficiency (1NF, 2NF, 3NF) |
| Index | Speed up queries, slower writes |
| Join Types | INNER (both), LEFT (all left), RIGHT (all right), FULL (all) |
| Subquery | Query inside another query |
| Transaction | Group of statements (all or nothing) |

## Backend Best Practices

- **Security**: Never expose secrets, use environment variables, validate input
- **Performance**: Use indexes, cache, pagination, async operations
- **Code Quality**: Type hints, docstrings, tests, clean architecture
- **Database**: Use ORM, migrations, proper relationships
- **API Design**: RESTful, consistent naming, proper status codes
- **Error Handling**: Try-except, logging, meaningful messages
- **Testing**: Unit tests, integration tests, mock external dependencies
- **Logging**: Structured logging, appropriate levels, no sensitive data

---

## Key Interview Topics

1. **Explain how you would scale a Flask API**
   - Database optimization (indexes, caching)
   - Horizontal scaling (multiple servers)
   - Load balancing
   - Async processing (Celery)
   - Database replication

2. **Describe a project where you used OOP**
   - Class design
   - Inheritance/polymorphism
   - Error handling
   - Testing

3. **SQL Query Optimization**
   - Use indexes
   - Avoid N+1 queries
   - Use joins instead of multiple queries
   - Proper WHERE clauses

4. **Database Design**
   - Normalization
   - Key relationships
   - Constraints
   - Indexes

5. **API Security**
   - Authentication (JWT, OAuth)
   - Authorization
   - Input validation
   - Rate limiting

---

This comprehensive guide covers all essential backend development topics from basics to advanced level, with real-world examples, best practices, and interview preparation. Use this as your reference while learning and before interviews.

# AI - 2
# Complete Backend Developer Preparation Guide
## Core Python → Advanced Python → SQL → NoSQL → Flask → FastAPI → Git

---

# Table of Contents

1. [Core Python (Fundamentals)](#1-core-python-fundamentals)
2. [Core Python (Logic Building)](#2-core-python-logic-building)
3. [Advanced Python](#3-advanced-python)
4. [Database (SQL)](#4-database-sql)
5. [Database (NoSQL - MongoDB)](#5-database-nosql---mongodb)
6. [Flask](#6-flask)
7. [FastAPI](#7-fastapi)
8. [Git (Version Control)](#8-git-version-control)
9. [Test Preparation](#9-test-preparation)
10. [Practice Projects](#10-practice-projects)
11. [Revision & Cheat Sheets](#11-revision--cheat-sheets)
12. [Final Master Test](#12-final-master-test)

---

# 1. Core Python (Fundamentals)

## 1.1 Introduction to Python

### What is Python?
Python is a high-level, interpreted, dynamically-typed programming language known for its simplicity and readability. Created by Guido van Rossum in 1991, Python emphasizes code readability and allows developers to express concepts in fewer lines of code.

**Key Characteristics:**
- **Interpreted**: Code is executed line by line
- **Dynamically Typed**: No need to declare variable types
- **Multi-paradigm**: Supports procedural, object-oriented, and functional programming
- **Extensive Standard Library**: "Batteries included" philosophy

**Real-world Applications:**
- Web Development (Django, Flask, FastAPI)
- Data Science & Machine Learning
- Automation & Scripting
- Backend APIs
- DevOps Tools

---

## 1.2 Variables & Data Types

### Variables

Variables are containers that store data values. In Python, you don't need to declare the type explicitly.

```python
# Variable assignment
name = "John"           # String
age = 25               # Integer
salary = 50000.50      # Float
is_active = True       # Boolean

# Multiple assignment
x, y, z = 10, 20, 30

# Same value to multiple variables
a = b = c = 100
```

**Naming Conventions:**
- Must start with a letter or underscore
- Can contain letters, numbers, underscores
- Case-sensitive (`name` ≠ `Name`)
- Use snake_case for variables (Python convention)
- Avoid Python keywords

```python
# Good names
user_name = "Alice"
total_count = 100
is_valid = True

# Bad names
1name = "Bob"        # Starts with number
user-name = "Eve"    # Contains hyphen
class = "Python"     # Reserved keyword
```

### Data Types

#### 1. Numeric Types

**Integer (int)**
```python
age = 25
population = 1000000000
negative = -50

# Python 3 has unlimited integer size
big_number = 999999999999999999999999999999
```

**Float (float)**
```python
price = 99.99
pi = 3.14159
scientific = 2.5e3  # 2500.0

# Precision issues
print(0.1 + 0.2)  # 0.30000000000000004

# Use Decimal for precise calculations
from decimal import Decimal
price1 = Decimal('10.10')
price2 = Decimal('20.20')
print(price1 + price2)  # 30.30
```

**Complex (complex)**
```python
z = 3 + 4j
print(z.real)  # 3.0
print(z.imag)  # 4.0
```

#### 2. String (str)

Strings are immutable sequences of characters.

```python
# String creation
name = "Alice"
message = 'Hello World'
multiline = """This is
a multiline
string"""

# String operations
greeting = "Hello" + " " + "World"  # Concatenation
repeated = "Ha" * 3  # HaHaHa

# String indexing
text = "Python"
print(text[0])   # P
print(text[-1])  # n

# String slicing
print(text[0:3])   # Pyt
print(text[2:])    # thon
print(text[:4])    # Pyth
print(text[::2])   # Pto (every 2nd char)
print(text[::-1])  # nohtyP (reverse)

# String methods
name = "  john doe  "
print(name.strip())        # "john doe"
print(name.upper())        # "  JOHN DOE  "
print(name.lower())        # "  john doe  "
print(name.title())        # "  John Doe  "
print(name.replace("john", "jane"))

# String formatting
name = "Alice"
age = 25

# Old style
msg1 = "Name: %s, Age: %d" % (name, age)

# str.format()
msg2 = "Name: {}, Age: {}".format(name, age)
msg3 = "Name: {n}, Age: {a}".format(n=name, a=age)

# f-strings (Python 3.6+) - RECOMMENDED
msg4 = f"Name: {name}, Age: {age}"
msg5 = f"Next year: {age + 1}"
msg6 = f"Price: {99.99:.2f}"  # 99.99
```

**Common String Methods:**
```python
text = "Hello World Python"

# Case conversion
text.upper()        # HELLO WORLD PYTHON
text.lower()        # hello world python
text.capitalize()   # Hello world python
text.title()        # Hello World Python
text.swapcase()     # hELLO wORLD pYTHON

# Searching
text.find("World")      # 6 (index)
text.find("Java")       # -1 (not found)
text.index("World")     # 6 (raises error if not found)
text.count("o")         # 3

# Checking
text.startswith("Hello")  # True
text.endswith("Python")   # True
text.isalpha()           # False (has spaces)
text.isdigit()           # False
text.isalnum()           # False

# Splitting and joining
words = text.split()     # ['Hello', 'World', 'Python']
" ".join(words)          # "Hello World Python"
text.split("o")          # ['Hell', ' W', 'rld Pyth', 'n']

# Trimming
"  hello  ".strip()      # "hello"
"  hello  ".lstrip()     # "hello  "
"  hello  ".rstrip()     # "  hello"
```

#### 3. Boolean (bool)

```python
is_active = True
is_valid = False

# Boolean operations
print(True and False)   # False
print(True or False)    # True
print(not True)         # False

# Truthy and Falsy values
# Falsy: False, None, 0, 0.0, "", [], {}, ()
# Everything else is Truthy

if "":
    print("This won't print")

if "Hello":
    print("This will print")

# Comparison returns boolean
print(5 > 3)      # True
print(10 == 10)   # True
print(5 != 3)     # True
```

#### 4. None Type

```python
# None represents absence of value
result = None

def get_user():
    return None

user = get_user()
if user is None:
    print("No user found")

# Use 'is' for None comparison, not ==
if result is None:  # Correct
    pass

if result == None:  # Works but not recommended
    pass
```

---

## 1.3 Type Conversion (Casting)

### Implicit Conversion
Python automatically converts one data type to another.

```python
# int + float = float
x = 10
y = 5.5
result = x + y  # 15.5 (float)

# bool to int in arithmetic
print(True + 5)   # 6
print(False * 10) # 0
```

### Explicit Conversion

```python
# String to Integer
age = int("25")       # 25
# age = int("25.5")   # ValueError
# age = int("abc")    # ValueError

# String to Float
price = float("99.99")      # 99.99
price2 = float("100")       # 100.0

# Integer to String
num_str = str(100)          # "100"

# String to Boolean
# bool() returns False only for empty string
print(bool(""))             # False
print(bool("0"))            # True (non-empty string)
print(bool("False"))        # True (non-empty string)

# List to Set (removes duplicates)
numbers = [1, 2, 2, 3, 3, 4]
unique = set(numbers)       # {1, 2, 3, 4}

# String to List
chars = list("Hello")       # ['H', 'e', 'l', 'l', 'o']

# Safe conversion with error handling
def safe_int(value, default=0):
    try:
        return int(value)
    except ValueError:
        return default

print(safe_int("123"))      # 123
print(safe_int("abc"))      # 0
print(safe_int("abc", -1))  # -1
```

---

## 1.4 Operators

### 1. Arithmetic Operators

```python
a, b = 10, 3

print(a + b)    # 13 - Addition
print(a - b)    # 7  - Subtraction
print(a * b)    # 30 - Multiplication
print(a / b)    # 3.333... - Division (always float)
print(a // b)   # 3  - Floor division (integer)
print(a % b)    # 1  - Modulus (remainder)
print(a ** b)   # 1000 - Exponentiation

# Compound assignment
x = 10
x += 5   # x = x + 5  → 15
x -= 3   # x = x - 3  → 12
x *= 2   # x = x * 2  → 24
x /= 4   # x = x / 4  → 6.0
x //= 2  # x = x // 2 → 3.0
x %= 2   # x = x % 2  → 1.0
x **= 3  # x = x ** 3 → 1.0
```

### 2. Comparison Operators

```python
a, b = 10, 20

print(a == b)   # False - Equal to
print(a != b)   # True  - Not equal to
print(a > b)    # False - Greater than
print(a < b)    # True  - Less than
print(a >= b)   # False - Greater than or equal
print(a <= b)   # True  - Less than or equal

# Chained comparisons
x = 15
print(10 < x < 20)  # True
print(10 < x > 20)  # False

# String comparison (lexicographic)
print("apple" < "banana")  # True
print("abc" == "ABC")      # False
```

### 3. Logical Operators

```python
# and - Returns True if both are True
print(True and True)    # True
print(True and False)   # False

# or - Returns True if at least one is True
print(True or False)    # True
print(False or False)   # False

# not - Reverses the boolean value
print(not True)         # False
print(not False)        # True

# Short-circuit evaluation
def check1():
    print("Check1 called")
    return True

def check2():
    print("Check2 called")
    return False

# and: stops at first False
result = check2() and check1()  # Only "Check2 called" prints

# or: stops at first True
result = check1() or check2()   # Only "Check1 called" prints

# Practical example
age = 25
if age >= 18 and age <= 60:
    print("Working age")

# Better way
if 18 <= age <= 60:
    print("Working age")
```

### 4. Identity Operators

```python
# is - Checks if two variables point to same object
# == - Checks if values are equal

a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)   # True  (same values)
print(a is b)   # False (different objects)
print(a is c)   # True  (same object)

# For small integers and strings, Python caches objects
x = 10
y = 10
print(x is y)   # True (same cached object)

# For None, always use 'is'
result = None
if result is None:  # Correct
    print("No result")

if result == None:  # Works but not Pythonic
    print("No result")
```

### 5. Membership Operators

```python
# in - Checks if value exists in sequence
# not in - Checks if value doesn't exist

numbers = [1, 2, 3, 4, 5]
print(3 in numbers)      # True
print(10 in numbers)     # False
print(10 not in numbers) # True

# Works with strings
text = "Hello World"
print("World" in text)   # True
print("Python" in text)  # False

# Works with dictionaries (checks keys)
user = {"name": "Alice", "age": 25}
print("name" in user)    # True
print("email" in user)   # False
```

### 6. Bitwise Operators (Advanced)

```python
a = 10  # 1010 in binary
b = 4   # 0100 in binary

print(a & b)   # 0  - AND
print(a | b)   # 14 - OR
print(a ^ b)   # 14 - XOR
print(~a)      # -11 - NOT
print(a << 2)  # 40 - Left shift
print(a >> 2)  # 2  - Right shift
```

---

## 1.5 Input/Output

### Output (print)

```python
# Basic print
print("Hello World")

# Multiple values
print("Name:", "Alice", "Age:", 25)
# Output: Name: Alice Age: 25

# Custom separator
print("A", "B", "C", sep="-")
# Output: A-B-C

# Custom ending
print("Hello", end=" ")
print("World")
# Output: Hello World

# Formatted output
name = "Alice"
age = 25
print(f"Name: {name}, Age: {age}")

# Print to file
with open("output.txt", "w") as f:
    print("Hello File", file=f)
```

### Input (input)

```python
# Basic input (always returns string)
name = input("Enter your name: ")
print(f"Hello, {name}")

# Convert to integer
age = int(input("Enter your age: "))

# Safe input with validation
def get_int_input(prompt):
    while True:
        try:
            return int(input(prompt))
        except ValueError:
            print("Invalid input. Please enter a number.")

age = get_int_input("Enter your age: ")

# Multiple inputs in one line
x, y = input("Enter two numbers: ").split()
x, y = int(x), int(y)

# Or using map
x, y = map(int, input("Enter two numbers: ").split())
```

---

## 1.6 Comments

```python
# Single line comment

"""
Multi-line comment
or docstring
"""

'''
Also a multi-line comment
'''

# Inline comment
x = 10  # This is an inline comment

# Docstrings for functions
def add(a, b):
    """
    Adds two numbers and returns the result.
    
    Args:
        a (int): First number
        b (int): Second number
        
    Returns:
        int: Sum of a and b
    """
    return a + b

# Access docstring
print(add.__doc__)
```

---

## 1.7 Data Structures

### 1. Lists

Lists are ordered, mutable collections that can contain mixed data types.

```python
# Creating lists
numbers = [1, 2, 3, 4, 5]
mixed = [1, "Hello", 3.14, True]
empty = []
matrix = [[1, 2], [3, 4], [5, 6]]

# Accessing elements
print(numbers[0])    # 1 (first element)
print(numbers[-1])   # 5 (last element)
print(numbers[1:4])  # [2, 3, 4] (slicing)

# Modifying lists
numbers[0] = 10      # Change element
numbers.append(6)    # Add to end
numbers.insert(0, 0) # Insert at index
numbers.extend([7, 8, 9])  # Add multiple

# Removing elements
numbers.pop()        # Remove last, returns it
numbers.pop(0)       # Remove at index
numbers.remove(10)   # Remove by value (first occurrence)
del numbers[0]       # Delete by index
numbers.clear()      # Remove all elements

# List operations
nums1 = [1, 2, 3]
nums2 = [4, 5, 6]
combined = nums1 + nums2  # [1, 2, 3, 4, 5, 6]
repeated = nums1 * 2      # [1, 2, 3, 1, 2, 3]

# List methods
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
numbers.sort()           # Sort in place
numbers.reverse()        # Reverse in place
count = numbers.count(1) # Count occurrences
index = numbers.index(4) # Find index of first occurrence

# List comprehension
squares = [x**2 for x in range(10)]
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

evens = [x for x in range(20) if x % 2 == 0]
# [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

# Nested list comprehension
matrix = [[i*j for j in range(3)] for i in range(3)]
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]

# Copying lists
original = [1, 2, 3]
shallow_copy = original.copy()  # or original[:]
import copy
deep_copy = copy.deepcopy(original)
```

**Common List Patterns:**
```python
# Find max/min
numbers = [3, 1, 4, 1, 5, 9]
print(max(numbers))  # 9
print(min(numbers))  # 1
print(sum(numbers))  # 23
print(len(numbers))  # 6

# Check if list is empty
if not numbers:  # Better than if len(numbers) == 0
    print("Empty list")

# Remove duplicates (preserves order)
def remove_duplicates(lst):
    seen = set()
    result = []
    for item in lst:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

# Or using dict (Python 3.7+)
unique = list(dict.fromkeys(numbers))

# Flatten nested list
nested = [[1, 2], [3, 4], [5, 6]]
flat = [item for sublist in nested for item in sublist]
# [1, 2, 3, 4, 5, 6]

# Split list into chunks
def chunks(lst, n):
    for i in range(0, len(lst), n):
        yield lst[i:i + n]

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
for chunk in chunks(numbers, 3):
    print(chunk)  # [1,2,3], [4,5,6], [7,8,9]
```

### 2. Tuples

Tuples are ordered, immutable collections.

```python
# Creating tuples
coordinates = (10, 20)
person = ("Alice", 25, "Engineer")
single = (42,)  # Note the comma
empty = ()

# Accessing elements
print(coordinates[0])  # 10
print(person[-1])      # Engineer

# Tuple unpacking
x, y = coordinates
name, age, job = person

# Can't modify tuples
# coordinates[0] = 15  # TypeError

# But can create new tuple
new_coords = (15, 25)

# Tuple methods (only 2)
numbers = (1, 2, 3, 2, 1, 2)
print(numbers.count(2))  # 3
print(numbers.index(3))  # 2

# When to use tuples vs lists?
# Tuples: Fixed data, immutable, faster, can be dict keys
# Lists: Dynamic data, mutable, more methods
```

**Tuple Use Cases:**
```python
# Function returning multiple values
def get_user():
    return ("Alice", 25, "alice@example.com")

name, age, email = get_user()

# Using as dictionary keys
locations = {
    (0, 0): "Origin",
    (1, 0): "Right",
    (0, 1): "Up"
}

# Immutable configuration
CONFIG = ("localhost", 5432, "mydb")
```

### 3. Sets

Sets are unordered collections of unique elements.

```python
# Creating sets
numbers = {1, 2, 3, 4, 5}
fruits = {"apple", "banana", "cherry"}
empty = set()  # Note: {} creates a dict, not set

# From list (removes duplicates)
numbers = set([1, 2, 2, 3, 3, 4])  # {1, 2, 3, 4}

# Adding elements
fruits.add("orange")
fruits.update(["mango", "grape"])

# Removing elements
fruits.remove("apple")     # Raises KeyError if not found
fruits.discard("banana")   # Doesn't raise error
popped = fruits.pop()      # Remove random element
fruits.clear()             # Remove all

# Set operations
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# Union (all elements)
print(a | b)           # {1, 2, 3, 4, 5, 6}
print(a.union(b))

# Intersection (common elements)
print(a & b)           # {3, 4}
print(a.intersection(b))

# Difference (in a but not in b)
print(a - b)           # {1, 2}
print(a.difference(b))

# Symmetric difference (in either but not both)
print(a ^ b)           # {1, 2, 5, 6}
print(a.symmetric_difference(b))

# Subset/Superset
c = {1, 2}
print(c.issubset(a))   # True
print(a.issuperset(c)) # True

# Set comprehension
squares = {x**2 for x in range(10)}
```

**Set Use Cases:**
```python
# Remove duplicates from list
numbers = [1, 2, 2, 3, 3, 4]
unique = list(set(numbers))

# Check membership (faster than list for large datasets)
valid_users = {"alice", "bob", "charlie"}
if username in valid_users:  # O(1) average
    print("Valid user")

# Find unique elements across lists
list1 = [1, 2, 3, 4]
list2 = [3, 4, 5, 6]
unique_to_list1 = set(list1) - set(list2)  # {1, 2}
common = set(list1) & set(list2)           # {3, 4}
```

### 4. Dictionaries

Dictionaries are unordered collections of key-value pairs.

```python
# Creating dictionaries
user = {
    "name": "Alice",
    "age": 25,
    "email": "alice@example.com"
}

# Or using dict()
user = dict(name="Alice", age=25)

# Empty dictionary
empty = {}

# Accessing values
print(user["name"])        # Alice
print(user.get("age"))     # 25
print(user.get("phone"))   # None
print(user.get("phone", "Not provided"))  # Default value

# Adding/Modifying
user["phone"] = "1234567890"
user["age"] = 26

# Removing
del user["phone"]
age = user.pop("age")           # Remove and return
email = user.pop("email", None) # With default
user.clear()                    # Remove all

# Dictionary methods
user = {"name": "Alice", "age": 25, "city": "NYC"}

print(user.keys())     # dict_keys(['name', 'age', 'city'])
print(user.values())   # dict_values(['Alice', 25, 'NYC'])
print(user.items())    # dict_items([('name', 'Alice'), ...])

# Looping through dictionary
for key in user:
    print(key, user[key])

for key, value in user.items():
    print(f"{key}: {value}")

# Merging dictionaries
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}

# Python 3.9+
merged = dict1 | dict2  # {'a': 1, 'b': 3, 'c': 4}

# Python 3.5+
merged = {**dict1, **dict2}

# Or using update
dict1.update(dict2)

# Dictionary comprehension
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Nested dictionaries
users = {
    "user1": {"name": "Alice", "age": 25},
    "user2": {"name": "Bob", "age": 30}
}

print(users["user1"]["name"])  # Alice

# setdefault - get value or set default
user = {}
user.setdefault("visits", 0)
user["visits"] += 1

# defaultdict
from collections import defaultdict

word_count = defaultdict(int)
for word in ["apple", "banana", "apple"]:
    word_count[word] += 1

print(dict(word_count))  # {'apple': 2, 'banana': 1}
```

**Dictionary Use Cases:**
```python
# Counting frequency
def count_frequency(items):
    freq = {}
    for item in items:
        freq[item] = freq.get(item, 0) + 1
    return freq

# Or using Counter
from collections import Counter
items = ['apple', 'banana', 'apple', 'cherry']
freq = Counter(items)

# Grouping data
students = [
    {"name": "Alice", "grade": "A"},
    {"name": "Bob", "grade": "B"},
    {"name": "Charlie", "grade": "A"}
]

by_grade = {}
for student in students:
    grade = student["grade"]
    if grade not in by_grade:
        by_grade[grade] = []
    by_grade[grade].append(student["name"])

# Caching/Memoization
cache = {}

def fibonacci(n):
    if n in cache:
        return cache[n]
    if n <= 1:
        return n
    result = fibonacci(n-1) + fibonacci(n-2)
    cache[n] = result
    return result

# Configuration
config = {
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "mydb"
    },
    "api": {
        "key": "secret123",
        "timeout": 30
    }
}
```

---

## 1.8 Virtual Environments & PIP

### Why Virtual Environments?

Virtual environments isolate project dependencies, preventing conflicts between different projects.

**Problem without venv:**
```
Project A needs: Django 3.2
Project B needs: Django 4.0
System Python: Only one version can be installed globally
```

### Creating Virtual Environment

```bash
# Create virtual environment
python -m venv myenv

# Activate
# Windows
myenv\Scripts\activate

# Linux/Mac
source myenv/bin/activate

# Deactivate
deactivate
```

### PIP (Package Manager)

```bash
# Install package
pip install requests

# Install specific version
pip install Django==3.2

# Install from requirements.txt
pip install -r requirements.txt

# List installed packages
pip list

# Show package info
pip show requests

# Uninstall package
pip uninstall requests

# Freeze current environment
pip freeze > requirements.txt

# Upgrade package
pip install --upgrade requests

# Search package
pip search django
```

**requirements.txt example:**
```
Django==3.2.0
requests>=2.25.0
psycopg2-binary==2.9.1
python-dotenv~=0.19.0
```

---

## 1.9 Common Mistakes & Best Practices

### Common Mistakes

```python
# ❌ Mutable default arguments
def add_item(item, lst=[]):  # WRONG!
    lst.append(item)
    return lst

print(add_item(1))  # [1]
print(add_item(2))  # [1, 2] - Not [2]!

# ✅ Correct way
def add_item(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst

# ❌ Modifying list during iteration
numbers = [1, 2, 3, 4, 5]
for num in numbers:
    if num % 2 == 0:
        numbers.remove(num)  # WRONG!

# ✅ Correct way
numbers = [num for num in numbers if num % 2 != 0]

# ❌ Using == instead of is for None
if value == None:  # Works but not Pythonic

# ✅ Correct way
if value is None:

# ❌ Not closing files
f = open("file.txt")
data = f.read()
# File not closed!

# ✅ Correct way
with open("file.txt") as f:
    data = f.read()
# Automatically closed

# ❌ Ignoring exceptions
try:
    result = 10 / 0
except:  # Too broad!
    pass

# ✅ Correct way
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
```

### Best Practices

```python
# ✅ Use meaningful variable names
# Bad
x = 10
d = {}

# Good
age = 10
user_data = {}

# ✅ Use constants for magic numbers
# Bad
if age > 18:
    pass

# Good
ADULT_AGE = 18
if age > ADULT_AGE:
    pass

# ✅ Use list comprehensions for simple loops
# Bad
squares = []
for x in range(10):
    squares.append(x**2)

# Good
squares = [x**2 for x in range(10)]

# ✅ Use enumerate for index and value
# Bad
for i in range(len(items)):
    print(i, items[i])

# Good
for i, item in enumerate(items):
    print(i, item)

# ✅ Use with statement for resources
# Good
with open("file.txt") as f:
    data = f.read()

# ✅ Use f-strings for formatting
name = "Alice"
age = 25

# Bad
msg = "Name: " + name + ", Age: " + str(age)

# Good
msg = f"Name: {name}, Age: {age}"

# ✅ Use truthiness
# Bad
if len(items) != 0:
    pass

# Good
if items:
    pass

# Bad
if flag == True:
    pass

# Good
if flag:
    pass
```

---

# 2. Core Python (Logic Building)

## 2.1 Control Flow

### If-Elif-Else Statements

```python
# Basic if
age = 20
if age >= 18:
    print("Adult")

# If-else
if age >= 18:
    print("Adult")
else:
    print("Minor")

# If-elif-else
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"

# Nested if
age = 20
has_license = True

if age >= 18:
    if has_license:
        print("Can drive")
    else:
        print("Need license")
else:
    print("Too young")

# Ternary operator (conditional expression)
age = 20
status = "Adult" if age >= 18 else "Minor"

# Can be nested (but avoid for readability)
result = "A" if score >= 90 else "B" if score >= 80 else "C"
```

### Truthy and Falsy Values

```python
# Falsy values
False
None
0
0.0
""
[]
{}
()
set()

# Everything else is Truthy
"0"        # Truthy (non-empty string)
[0]        # Truthy (non-empty list)
{0: 0}     # Truthy (non-empty dict)

# Practical usage
name = ""
if not name:
    print("Name is required")

items = []
if not items:
    print("No items")

# Check for None specifically
value = 0
if value is None:  # False
    print("None")

if not value:      # True (0 is falsy)
    print("Falsy value")
```

### Logical Operators

```python
# and - all conditions must be True
age = 25
income = 50000

if age >= 18 and income >= 30000:
    print("Eligible for loan")

# or - at least one condition must be True
if age < 18 or age > 60:
    print("Special discount")

# not - reverses boolean
is_banned = False
if not is_banned:
    print("Access granted")

# Combining operators
if (age >= 18 and income >= 30000) or has_guarantor:
    print("Loan approved")

# Short-circuit evaluation
def expensive_check():
    print("Expensive check running")
    return True

# expensive_check() won't run if first condition is False
if False and expensive_check():
    pass

# expensive_check() won't run if first condition is True
if True or expensive_check():
    pass
```

---

## 2.2 Loops

### For Loops

```python
# Iterate over list
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# Iterate over range
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# Range with start and stop
for i in range(2, 5):
    print(i)  # 2, 3, 4

# Range with step
for i in range(0, 10, 2):
    print(i)  # 0, 2, 4, 6, 8

# Reverse range
for i in range(10, 0, -1):
    print(i)  # 10, 9, 8, ..., 1

# Enumerate (index and value)
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Start enumerate from different number
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}: {fruit}")

# Iterate over dictionary
user = {"name": "Alice", "age": 25, "city": "NYC"}

# Keys only
for key in user:
    print(key)

# Values only
for value in user.values():
    print(value)

# Both keys and values
for key, value in user.items():
    print(f"{key}: {value}")

# Zip - iterate over multiple lists
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
cities = ["NYC", "LA", "Chicago"]

for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age}, {city}")

# Nested loops
for i in range(3):
    for j in range(3):
        print(f"({i}, {j})", end=" ")
    print()  # Newline after inner loop
```

### While Loops

```python
# Basic while loop
count = 0
while count < 5:
    print(count)
    count += 1

# While with condition
password = ""
while password != "secret":
    password = input("Enter password: ")

# Infinite loop (with break)
while True:
    command = input("Enter command (exit to quit): ")
    if command == "exit":
        break
    print(f"Executing: {command}")

# While-else (else runs if loop completes normally)
count = 0
while count < 5:
    print(count)
    count += 1
else:
    print("Loop completed")

# Doesn't run else if break is used
count = 0
while count < 10:
    if count == 5:
        break
    count += 1
else:
    print("This won't print")
```

### Loop Control Statements

```python
# break - exit loop immediately
for i in range(10):
    if i == 5:
        break
    print(i)  # 0, 1, 2, 3, 4

# continue - skip current iteration
for i in range(10):
    if i % 2 == 0:
        continue
    print(i)  # 1, 3, 5, 7, 9

# pass - do nothing (placeholder)
for i in range(5):
    if i == 2:
        pass  # TODO: implement later
    else:
        print(i)

# Nested loops with break
found = False
for i in range(3):
    for j in range(3):
        if i == 1 and j == 1:
            found = True
            break
    if found:
        break

# Or use else clause
for i in range(3):
    for j in range(3):
        if i == 1 and j == 1:
            break
    else:
        continue
    break
```

### Common Loop Patterns

```python
# Sum of list
numbers = [1, 2, 3, 4, 5]
total = 0
for num in numbers:
    total += num
# Or: total = sum(numbers)

# Find maximum
maximum = numbers[0]
for num in numbers:
    if num > maximum:
        maximum = num
# Or: maximum = max(numbers)

# Count occurrences
items = ['apple', 'banana', 'apple', 'cherry']
count = 0
for item in items:
    if item == 'apple':
        count += 1

# Build new list from old
squares = []
for x in range(10):
    squares.append(x**2)
# Or: squares = [x**2 for x in range(10)]

# Early exit pattern
def find_user(user_id, users):
    for user in users:
        if user['id'] == user_id:
            return user
    return None

# Process pairs
numbers = [1, 2, 3, 4, 5, 6]
for i in range(0, len(numbers), 2):
    if i + 1 < len(numbers):
        pair = (numbers[i], numbers[i+1])
        print(pair)
```

---

## 2.3 Strings & Formatting

### String Methods Deep Dive

```python
text = "  Hello World Python  "

# Cleaning
text.strip()      # "Hello World Python"
text.lstrip()     # "Hello World Python  "
text.rstrip()     # "  Hello World Python"
text.strip("Py")  # Custom characters

# Case conversion
text.upper()      # "  HELLO WORLD PYTHON  "
text.lower()      # "  hello world python  "
text.capitalize() # "  hello world python  "
text.title()      # "  Hello World Python  "
text.swapcase()   # "  hELLO wORLD pYTHON  "

# Searching
text.find("World")        # 8 (index of first occurrence)
text.find("Java")         # -1 (not found)
text.rfind("o")          # 16 (last occurrence)
text.index("World")       # 8 (raises ValueError if not found)
text.count("o")          # 3

# Checking
text.startswith("  Hello")  # True
text.endswith("  ")         # True
text.isalpha()             # False
text.isdigit()             # False
text.isalnum()             # False
text.isspace()             # False
text.islower()             # False
text.isupper()             # False

# Splitting and joining
words = text.split()           # ['Hello', 'World', 'Python']
parts = text.split("o")        # ['  Hell', ' W', 'rld Pyth', 'n  ']
" | ".join(words)              # "Hello | World | Python"

# Replacing
text.replace("World", "Universe")
text.replace("o", "0", 2)      # Replace first 2 occurrences

# Padding and alignment
"42".zfill(5)                  # "00042"
"hello".center(10)             # "  hello   "
"hello".ljust(10, "*")         # "hello*****"
"hello".rjust(10, "*")         # "*****hello"
```

### Advanced String Formatting

```python
name = "Alice"
age = 25
balance = 1234.567

# f-strings (Python 3.6+) - RECOMMENDED
msg = f"Hello, {name}!"
msg = f"Age: {age}, Next year: {age + 1}"
msg = f"Balance: ${balance:.2f}"      # $1234.57
msg = f"Balance: ${balance:,.2f}"     # $1,234.57

# Expressions in f-strings
msg = f"Status: {'Adult' if age >= 18 else 'Minor'}"
msg = f"Sum: {2 + 2}"
msg = f"Uppercase: {name.upper()}"

# Formatting numbers
num = 42
msg = f"{num:05d}"        # "00042" (5 digits, zero-padded)
msg = f"{num:>10}"        # "        42" (right-aligned, width 10)
msg = f"{num:<10}"        # "42        " (left-aligned)
msg = f"{num:^10}"        # "    42    " (centered)

price = 1234.5
msg = f"{price:.2f}"      # "1234.50" (2 decimal places)
msg = f"{price:,.2f}"     # "1,234.50" (with comma separator)
msg = f"{price:10.2f}"    # "   1234.50" (width 10, 2 decimals)

# Percentages
ratio = 0.835
msg = f"{ratio:.1%}"      # "83.5%"

# Dates
from datetime import datetime
now = datetime.now()
msg = f"{now:%Y-%m-%d}"              # "2024-04-09"
msg = f"{now:%Y-%m-%d %H:%M:%S}"     # "2024-04-09 14:30:45"

# str.format() - Older but still common
msg = "Name: {}, Age: {}".format(name, age)
msg = "Name: {0}, Age: {1}, Name again: {0}".format(name, age)
msg = "Name: {n}, Age: {a}".format(n=name, a=age)

# Old-style % formatting
msg = "Name: %s, Age: %d" % (name, age)
msg = "Price: %.2f" % balance
```

### String Slicing

```python
text = "Python Programming"

# Basic slicing [start:stop:step]
text[0:6]      # "Python"
text[7:]       # "Programming"
text[:6]       # "Python"
text[::2]      # "Pto rgamn" (every 2nd char)
text[::-1]     # "gnimmargorP nohtyP" (reverse)

# Negative indices
text[-4:]      # "ming" (last 4 chars)
text[:-4]      # "Python Program" (all but last 4)
text[-11:-4]   # "Program" (from -11 to -4)

# Step
text[::3]      # "Ph ormn" (every 3rd char)
text[1::2]     # "yhnPoamn" (from index 1, every 2nd)

# Practical examples
# Reverse string
reversed_text = text[::-1]

# Check palindrome
def is_palindrome(s):
    s = s.lower().replace(" ", "")
    return s == s[::-1]

# Get first and last n characters
def get_first_last(s, n):
    return s[:n], s[-n:]

# Extract extension from filename
filename = "document.pdf"
extension = filename[filename.rfind('.'):]  # ".pdf"
# Or better: extension = filename.split('.')[-1]
```

### String Parsing

```python
# Parse CSV line
line = "John,25,Engineer,NYC"
name, age, job, city = line.split(',')
age = int(age)

# Parse key-value pairs
config = "host=localhost;port=5432;db=mydb"
parts = config.split(';')
config_dict = {}
for part in parts:
    key, value = part.split('=')
    config_dict[key] = value

# Or using dict comprehension
config_dict = dict(part.split('=') for part in config.split(';'))

# Parse multiline text
text = """Line 1
Line 2
Line 3"""
lines = text.split('\n')
# Or: lines = text.splitlines()

# Extract numbers from string
import re
text = "Price: $99.99, Quantity: 5"
numbers = re.findall(r'\d+\.?\d*', text)  # ['99.99', '5']

# Clean whitespace from multiline string
text = """
    Hello
    World
    """
lines = [line.strip() for line in text.strip().split('\n')]
```

---

## 2.4 Working with Dates & Time

### datetime Module

```python
from datetime import datetime, date, time, timedelta

# Current date and time
now = datetime.now()
print(now)  # 2024-04-09 14:30:45.123456

today = date.today()
print(today)  # 2024-04-09

# Create specific datetime
dt = datetime(2024, 4, 9, 14, 30, 45)
d = date(2024, 4, 9)
t = time(14, 30, 45)

# Accessing components
print(now.year)     # 2024
print(now.month)    # 4
print(now.day)      # 9
print(now.hour)     # 14
print(now.minute)   # 30
print(now.second)   # 45
print(now.weekday())  # 0 (Monday) to 6 (Sunday)

# Formatting datetime
now.strftime("%Y-%m-%d")              # "2024-04-09"
now.strftime("%Y-%m-%d %H:%M:%S")     # "2024-04-09 14:30:45"
now.strftime("%B %d, %Y")             # "April 09, 2024"
now.strftime("%d/%m/%Y")              # "09/04/2024"
now.strftime("%I:%M %p")              # "02:30 PM"

# Common format codes:
# %Y - Year (4 digit)
# %y - Year (2 digit)
# %m - Month (01-12)
# %d - Day (01-31)
# %H - Hour (00-23)
# %I - Hour (01-12)
# %M - Minute
# %S - Second
# %p - AM/PM
# %B - Month name
# %b - Month abbr
# %A - Weekday name
# %a - Weekday abbr

# Parsing datetime from string
date_str = "2024-04-09"
dt = datetime.strptime(date_str, "%Y-%m-%d")

date_str = "09/04/2024 14:30"
dt = datetime.strptime(date_str, "%d/%m/%Y %H:%M")

# Date arithmetic
from datetime import timedelta

# Add days
tomorrow = today + timedelta(days=1)
next_week = today + timedelta(weeks=1)
next_month = today + timedelta(days=30)

# Subtract dates
date1 = date(2024, 4, 9)
date2 = date(2024, 1, 1)
diff = date1 - date2
print(diff.days)  # 99

# Compare dates
if date1 > date2:
    print("date1 is later")

# Practical examples
def days_until_deadline(deadline_str):
    deadline = datetime.strptime(deadline_str, "%Y-%m-%d").date()
    today = date.today()
    return (deadline - today).days

def format_relative_time(dt):
    now = datetime.now()
    diff = now - dt
    
    if diff.days > 365:
        return f"{diff.days // 365} years ago"
    elif diff.days > 30:
        return f"{diff.days // 30} months ago"
    elif diff.days > 0:
        return f"{diff.days} days ago"
    elif diff.seconds > 3600:
        return f"{diff.seconds // 3600} hours ago"
    elif diff.seconds > 60:
        return f"{diff.seconds // 60} minutes ago"
    else:
        return "just now"

# Get age from birthdate
def calculate_age(birthdate):
    today = date.today()
    age = today.year - birthdate.year
    if today.month < birthdate.month or \
       (today.month == birthdate.month and today.day < birthdate.day):
        age -= 1
    return age

birthdate = date(1998, 5, 15)
age = calculate_age(birthdate)
```

### Time Zones (pytz)

```python
from datetime import datetime
import pytz

# UTC time
utc_now = datetime.now(pytz.UTC)

# Specific timezone
tz = pytz.timezone('America/New_York')
ny_time = datetime.now(tz)

# Convert between timezones
utc_time = datetime.now(pytz.UTC)
ny_tz = pytz.timezone('America/New_York')
ny_time = utc_time.astimezone(ny_tz)

# List all timezones
# print(pytz.all_timezones)
```

---

## 2.5 JSON Handling

### Reading and Writing JSON

```python
import json

# Python dict to JSON string
user = {
    "name": "Alice",
    "age": 25,
    "email": "alice@example.com",
    "is_active": True,
    "balance": 1234.56
}

# dict → JSON string
json_str = json.dumps(user)
print(json_str)
# {"name": "Alice", "age": 25, "email": "alice@example.com", "is_active": true, "balance": 1234.56}

# Pretty print
json_str = json.dumps(user, indent=4)
print(json_str)
# {
#     "name": "Alice",
#     "age": 25,
#     "email": "alice@example.com",
#     "is_active": true,
#     "balance": 1234.56
# }

# JSON string → dict
json_str = '{"name": "Bob", "age": 30}'
user = json.loads(json_str)
print(user["name"])  # Bob

# Write to file
with open("user.json", "w") as f:
    json.dump(user, f, indent=4)

# Read from file
with open("user.json", "r") as f:
    user = json.load(f)

# Handling None and special types
data = {
    "name": "Alice",
    "value": None,        # Becomes null in JSON
    "active": True,       # Becomes true
    "inactive": False     # Becomes false
}

# Sorting keys
json_str = json.dumps(data, sort_keys=True, indent=4)

# Custom JSON encoder
from datetime import datetime

class DateTimeEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        return super().default(obj)

data = {
    "timestamp": datetime.now(),
    "event": "login"
}

json_str = json.dumps(data, cls=DateTimeEncoder)

# Or using default parameter
def datetime_handler(obj):
    if isinstance(obj, datetime):
        return obj.isoformat()
    raise TypeError(f"Type {type(obj)} not serializable")

json_str = json.dumps(data, default=datetime_handler)
```

### Working with JSON Data

```python
# Example: API response
response = '''
{
    "status": "success",
    "data": {
        "users": [
            {"id": 1, "name": "Alice", "role": "admin"},
            {"id": 2, "name": "Bob", "role": "user"},
            {"id": 3, "name": "Charlie", "role": "user"}
        ]
    }
}
'''

data = json.loads(response)

# Access nested data
status = data["status"]
users = data["data"]["users"]

# Filter users
admins = [u for u in users if u["role"] == "admin"]

# Find user by id
def find_user(users, user_id):
    return next((u for u in users if u["id"] == user_id), None)

user = find_user(users, 2)

# Transform data
user_names = [u["name"] for u in users]

# Example: Config file
config = {
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "mydb"
    },
    "api": {
        "key": "secret123",
        "timeout": 30
    }
}

# Save config
with open("config.json", "w") as f:
    json.dump(config, f, indent=4)

# Load config
with open("config.json", "r") as f:
    config = json.load(f)

db_host = config["database"]["host"]
```

### Handling JSON Errors

```python
import json

# Malformed JSON
json_str = '{"name": "Alice", "age": 25'  # Missing closing brace

try:
    data = json.loads(json_str)
except json.JSONDecodeError as e:
    print(f"JSON Error: {e}")
    print(f"Line: {e.lineno}, Column: {e.colno}")

# Safe JSON loading
def safe_load_json(json_str, default=None):
    try:
        return json.loads(json_str)
    except json.JSONDecodeError:
        return default

data = safe_load_json('invalid json', default={})

# Validate JSON structure
def validate_user(data):
    required_fields = ["name", "email", "age"]
    for field in required_fields:
        if field not in data:
            raise ValueError(f"Missing required field: {field}")
    
    if not isinstance(data["age"], int):
        raise ValueError("Age must be an integer")
    
    if not "@" in data["email"]:
        raise ValueError("Invalid email format")
    
    return True

try:
    user_data = json.loads(json_str)
    validate_user(user_data)
except (json.JSONDecodeError, ValueError) as e:
    print(f"Error: {e}")
```

---

## 2.6 Regular Expressions (Regex)

### Regex Basics

```python
import re

# Basic patterns
# . - Any character (except newline)
# ^ - Start of string
# $ - End of string
# * - 0 or more
# + - 1 or more
# ? - 0 or 1
# {n} - Exactly n times
# {n,} - n or more times
# {n,m} - Between n and m times
# [] - Character set
# | - OR
# () - Group
# \ - Escape special character

# re.search() - Find first match
text = "Hello World"
match = re.search(r"World", text)
if match:
    print("Found")  # Found

# re.match() - Match from start
match = re.match(r"Hello", text)
if match:
    print("Matched")  # Matched

match = re.match(r"World", text)
if match:
    print("Won't print")

# re.findall() - Find all matches
text = "Phone: 123-456-7890 or 098-765-4321"
phones = re.findall(r"\d{3}-\d{3}-\d{4}", text)
print(phones)  # ['123-456-7890', '098-765-4321']

# re.finditer() - Iterator of match objects
for match in re.finditer(r"\d{3}-\d{3}-\d{4}", text):
    print(match.group(), match.start(), match.end())

# re.sub() - Replace
text = "Hello World"
new_text = re.sub(r"World", "Python", text)
print(new_text)  # Hello Python

# Case insensitive
text = "Hello WORLD"
match = re.search(r"world", text, re.IGNORECASE)

# Multiline mode
text = """Line 1
Line 2
Line 3"""
matches = re.findall(r"^Line", text, re.MULTILINE)
print(matches)  # ['Line', 'Line', 'Line']
```

### Common Patterns

```python
import re

# Email validation
def is_valid_email(email):
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return re.match(pattern, email) is not None

print(is_valid_email("user@example.com"))  # True
print(is_valid_email("invalid.email"))     # False

# Phone number
def extract_phone_numbers(text):
    # Matches: 123-456-7890, (123) 456-7890, 123.456.7890
    pattern = r'\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}'
    return re.findall(pattern, text)

text = "Call me at 123-456-7890 or (098) 765-4321"
phones = extract_phone_numbers(text)

# URL extraction
def extract_urls(text):
    pattern = r'https?://[^\s]+'
    return re.findall(pattern, text)

text = "Visit https://example.com or http://test.com"
urls = extract_urls(text)

# Extract hashtags
def extract_hashtags(text):
    pattern = r'#\w+'
    return re.findall(pattern, text)

text = "Love #python and #programming #coding"
tags = extract_hashtags(text)  # ['#python', '#programming', '#coding']

# Extract numbers
def extract_numbers(text):
    # Matches integers and floats
    pattern = r'-?\d+\.?\d*'
    return [float(n) for n in re.findall(pattern, text)]

text = "Price: $99.99, Discount: -10%, Quantity: 5"
numbers = extract_numbers(text)  # [99.99, -10, 5]

# Password validation
def is_strong_password(password):
    """
    At least 8 characters
    At least one uppercase
    At least one lowercase
    At least one digit
    At least one special character
    """
    if len(password) < 8:
        return False
    
    has_upper = bool(re.search(r'[A-Z]', password))
    has_lower = bool(re.search(r'[a-z]', password))
    has_digit = bool(re.search(r'\d', password))
    has_special = bool(re.search(r'[!@#$%^&*(),.?":{}|<>]', password))
    
    return has_upper and has_lower and has_digit and has_special

print(is_strong_password("Weak123"))      # False
print(is_strong_password("Strong@123"))   # True

# Remove HTML tags
def remove_html_tags(text):
    pattern = r'<[^>]+>'
    return re.sub(pattern, '', text)

html = "<p>Hello <b>World</b></p>"
clean = remove_html_tags(html)  # "Hello World"

# Extract date
def extract_dates(text):
    # Matches: DD/MM/YYYY, DD-MM-YYYY, DD.MM.YYYY
    pattern = r'\d{2}[/-\.]\d{2}[/-\.]\d{4}'
    return re.findall(pattern, text)

text = "Event on 09/04/2024 or 10-05-2024"
dates = extract_dates(text)

# Split by multiple delimiters
def split_by_delimiters(text):
    pattern = r'[,;|\s]+'
    return re.split(pattern, text)

text = "apple,banana;cherry|orange grape"
items = split_by_delimiters(text)
```

### Groups and Capturing

```python
import re

# Groups
text = "John Doe, 25 years old"
pattern = r'(\w+) (\w+), (\d+) years old'
match = re.search(pattern, text)

if match:
    first_name = match.group(1)  # "John"
    last_name = match.group(2)   # "Doe"
    age = match.group(3)         # "25"
    full_match = match.group(0)  # "John Doe, 25 years old"

# Named groups
pattern = r'(?P<first>\w+) (?P<last>\w+), (?P<age>\d+) years old'
match = re.search(pattern, text)

if match:
    print(match.group('first'))  # "John"
    print(match.groupdict())     # {'first': 'John', 'last': 'Doe', 'age': '25'}

# Non-capturing group
pattern = r'(?:\d{3}-)?\d{3}-\d{4}'  # ?: makes it non-capturing
match = re.search(pattern, "123-456-7890")

# Backreferences
# Find repeated words
pattern = r'\b(\w+)\s+\1\b'
text = "This is is a test test"
matches = re.findall(pattern, text)  # ['is', 'test']

# Replace with captured groups
text = "John Doe"
pattern = r'(\w+) (\w+)'
result = re.sub(pattern, r'\2, \1', text)  # "Doe, John"

# Parse log entry
log = "2024-04-09 14:30:45 ERROR Database connection failed"
pattern = r'(\d{4}-\d{2}-\d{2}) (\d{2}:\d{2}:\d{2}) (\w+) (.+)'
match = re.search(pattern, log)

if match:
    date = match.group(1)
    time = match.group(2)
    level = match.group(3)
    message = match.group(4)
```

### Regex Flags

```python
import re

text = "Hello WORLD"

# re.IGNORECASE or re.I
match = re.search(r'world', text, re.IGNORECASE)  # Matches

# re.MULTILINE or re.M
text = """Line 1
Line 2
Line 3"""
matches = re.findall(r'^Line', text, re.MULTILINE)

# re.DOTALL or re.S - . matches newline too
text = "Hello\nWorld"
match = re.search(r'Hello.World', text, re.DOTALL)

# Multiple flags
match = re.search(r'world', text, re.IGNORECASE | re.MULTILINE)

# Compiled regex (better performance for repeated use)
pattern = re.compile(r'\d{3}-\d{3}-\d{4}', re.IGNORECASE)
matches = pattern.findall(text)
```

---

## 2.7 Math Operations

```python
import math

# Basic math operations
print(abs(-10))        # 10 - Absolute value
print(pow(2, 3))       # 8 - Power (2^3)
print(round(3.7))      # 4 - Round to nearest int
print(round(3.14159, 2))  # 3.14 - Round to 2 decimals

# Math module
print(math.ceil(3.2))    # 4 - Ceiling
print(math.floor(3.9))   # 3 - Floor
print(math.trunc(3.9))   # 3 - Truncate

print(math.sqrt(16))     # 4.0 - Square root
print(math.pow(2, 3))    # 8.0 - Power

print(math.pi)           # 3.14159...
print(math.e)            # 2.71828...

# Trigonometry
print(math.sin(math.pi/2))  # 1.0
print(math.cos(0))          # 1.0
print(math.tan(math.pi/4))  # 1.0

# Logarithms
print(math.log(10))      # Natural log
print(math.log10(100))   # Base-10 log (2.0)
print(math.log2(8))      # Base-2 log (3.0)

# Other
print(math.factorial(5))  # 120
print(math.gcd(12, 18))   # 6 - Greatest common divisor

# Random numbers
import random

print(random.random())           # Random float 0.0 to 1.0
print(random.randint(1, 10))     # Random int 1 to 10
print(random.uniform(1.5, 10.5)) # Random float 1.5 to 10.5

items = ['apple', 'banana', 'cherry']
print(random.choice(items))      # Random item
random.shuffle(items)            # Shuffle in place
print(random.sample(items, 2))   # Random sample of 2

# Seed for reproducible random
random.seed(42)
print(random.random())           # Always same result
```

---

This completes the **Core Python Fundamentals** section. Due to length constraints, I'll continue with the remaining sections in the next response.

Would you like me to continue with:
- **Core Python (Logic Building)** - Complete mini projects
- **Advanced Python** - OOP, File Handling, Exception Handling, etc.
- **Database (SQL)**
- And remaining sections?

Let me know if you want me to proceed with the complete document!
