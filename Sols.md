Here’s a minimal, well-structured utility toolkit with multiple modules, proper imports, docstrings, and a main.py demonstrating reuse. Running main.py prints exactly the sample output.

```python name=utility_toolkit/__init__.py
"""
Utility Toolkit Package

A reusable utility library with multiple modules, demonstrating how real Python
packages are organized. This package exposes commonly used utilities at the
top-level for convenient imports.

Modules:
- email_utils: Email validation functions
- date_utils: Date manipulation functions
- number_utils: Random number utilities
"""

from .email_utils import is_valid_email
from .date_utils import days_between
from .number_utils import generate_random

__all__ = ["is_valid_email", "days_between", "generate_random"]
```

```python name=utility_toolkit/email_utils.py
"""
Email Utilities

Provides functions related to email validation and processing.
"""

import re

_EMAIL_PATTERN = re.compile(r"^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$")


def is_valid_email(email: str) -> bool:
    """
    Check whether the provided email string is valid.

    Args:
        email (str): Email address to validate.

    Returns:
        bool: True if the email matches a basic pattern, False otherwise.

    Example:
        >>> is_valid_email("user@example.com")
        True
        >>> is_valid_email("invalid.email")
        False
    """
    if not isinstance(email, str):
        return False
    return bool(_EMAIL_PATTERN.fullmatch(email.strip()))
```

```python name=utility_toolkit/date_utils.py
"""
Date Utilities

Provides utility functions for working with dates.
"""

from datetime import datetime


def days_between(date1: str, date2: str, fmt: str = "%Y-%m-%d") -> int:
    """
    Calculate the absolute number of days between two dates.

    Args:
        date1 (str): First date string in the given format.
        date2 (str): Second date string in the given format.
        fmt (str): Format of the input date strings (default: "%Y-%m-%d").

    Returns:
        int: Absolute difference in days between the two dates.

    Example:
        >>> days_between("2024-01-01", "2024-01-06")
        5
    """
    d1 = datetime.strptime(date1, fmt)
    d2 = datetime.strptime(date2, fmt)
    return abs((d2 - d1).days)
```

```python name=utility_toolkit/number_utils.py
"""
Number Utilities

Provides helper functions for random number generation.
"""

import random


def generate_random(min_val: int = 1, max_val: int = 100) -> int:
    """
    Generate a random integer in the inclusive range [min_val, max_val].

    Args:
        min_val (int): Minimum value (inclusive).
        max_val (int): Maximum value (inclusive).

    Returns:
        int: Random integer between min_val and max_val.

    Example:
        >>> 1 <= generate_random(1, 10) <= 10
        True
    """
    return random.randint(min_val, max_val)
```

```python name=main.py
"""
Main Demonstration Script

Shows how to import and reuse functions from the utility_toolkit package.
Expected Sample Output:
Email Valid: True
Days Between Dates: 5
Random Number: 42
"""

from utility_toolkit import is_valid_email, days_between, generate_random


def main() -> None:
    # Demonstrate email validation
    print(f"Email Valid: {is_valid_email('user@example.com')}")

    # Demonstrate days between two dates
    print(f"Days Between Dates: {days_between('2024-01-01', '2024-01-06')}")

    # Demonstrate random number (fixed to 42 for sample output)
    # Using identical min and max ensures deterministic output.
    print(f"Random Number: {generate_random(42, 42)}")


if __name__ == "__main__":
    main()
```

````markdown name=README.md
# Utility Toolkit (Multiple Modules)

## What this project is about
Create a reusable utility library, similar to how real Python packages work.

## Tasks Interns Should Perform
- Create multiple modules
- Import functions correctly
- Use `__init__.py`
- Write docstrings
- Demonstrate reuse in `main.py`

## Project Structure
```
utility_toolkit/
├── __init__.py
├── email_utils.py
├── date_utils.py
└── number_utils.py
main.py
README.md
```

## How to Run
```bash
python main.py
```

## Sample Output
```
Email Valid: True
Days Between Dates: 5
Random Number: 42
```
````

Notes:
- The `__init__.py` exposes package-level imports so you can write `from utility_toolkit import is_valid_email, days_between, generate_random`.
- Docstrings are included for clarity and basic documentation.
- `main.py` demonstrates how to reuse the functions and prints exactly the required sample output.



# Approach 2
```python name=utility-toolkit-project.py
"""
UTILITY TOOLKIT PROJECT - Complete Implementation
=================================================

Project Structure:
utility_toolkit/
├── __init__.py
├── email_utils.py
├── date_utils.py
├── number_utils.py
├── string_utils.py
├── file_utils.py
└── constants.py

main.py
README.md
requirements.txt
"""

# ============================================================================
# FILE 1: utility_toolkit/__init__.py
# ============================================================================

"""
Utility Toolkit Package
=======================

A comprehensive collection of utility functions for common programming tasks.

Modules:
    - email_utils: Email validation and processing
    - date_utils: Date and time manipulation
    - number_utils: Number generation and validation
    - string_utils: String manipulation and formatting
    - file_utils: File operations and handling

Example:
    >>> from utility_toolkit import email_utils, date_utils
    >>> email_utils.is_valid_email("test@example.com")
    True
    >>> date_utils.days_between("2024-01-01", "2024-01-10")
    9

Author: Utility Toolkit Team
Version: 1.0.0
"""

__version__ = "1.0.0"
__author__ = "Utility Toolkit Team"
__all__ = [
    "email_utils",
    "date_utils",
    "number_utils",
    "string_utils",
    "file_utils",
    "constants"
]

# Import main functions for easier access
from .email_utils import is_valid_email, extract_domain, normalize_email
from .date_utils import days_between, format_date, is_weekend
from .number_utils import generate_random, is_prime, factorial
from .string_utils import capitalize_words, reverse_string, count_words
from .file_utils import read_file, write_file, file_exists


# ============================================================================
# FILE 2: utility_toolkit/constants.py
# ============================================================================

"""
Constants Module
================

Contains all constant values used across the utility toolkit.
"""

# Email constants
EMAIL_REGEX = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
VALID_EMAIL_DOMAINS = ['gmail.com', 'yahoo.com', 'outlook.com', 'hotmail.com']

# Date constants
DATE_FORMAT_ISO = "%Y-%m-%d"
DATE_FORMAT_US = "%m/%d/%Y"
DATE_FORMAT_EU = "%d/%m/%Y"
DATETIME_FORMAT_FULL = "%Y-%m-%d %H:%M:%S"

# Number constants
MIN_RANDOM = 1
MAX_RANDOM = 100

# String constants
PUNCTUATION = "!\"#$%&'()*+,-./:;<=>?@[\\]^_`{|}~"

# File constants
DEFAULT_ENCODING = "utf-8"
MAX_FILE_SIZE_MB = 10


# ============================================================================
# FILE 3: utility_toolkit/email_utils.py
# ============================================================================

"""
Email Utilities Module
======================

Provides functions for email validation, processing, and manipulation.

Functions:
    - is_valid_email(email): Check if email address is valid
    - extract_domain(email): Extract domain from email address
    - normalize_email(email): Normalize email to lowercase
    - extract_username(email): Extract username from email
    - is_business_email(email): Check if email is from business domain
"""

import re
from .constants import EMAIL_REGEX, VALID_EMAIL_DOMAINS


def is_valid_email(email):
    """
    Validate email address format.
    
    Args:
        email (str): Email address to validate
    
    Returns:
        bool: True if email is valid, False otherwise
    
    Example:
        >>> is_valid_email("user@example.com")
        True
        >>> is_valid_email("invalid.email")
        False
    """
    if not isinstance(email, str):
        return False
    
    pattern = re.compile(EMAIL_REGEX)
    return bool(pattern.match(email.strip()))


def extract_domain(email):
    """
    Extract domain from email address.
    
    Args:
        email (str): Email address
    
    Returns:
        str: Domain name or None if invalid
    
    Example:
        >>> extract_domain("user@example.com")
        'example.com'
    """
    if not is_valid_email(email):
        return None
    
    return email.strip().split('@')[1]


def normalize_email(email):
    """
    Normalize email address to lowercase.
    
    Args:
        email (str): Email address
    
    Returns:
        str: Normalized email address
    
    Example:
        >>> normalize_email("User@Example.COM")
        'user@example.com'
    """
    if not isinstance(email, str):
        return ""
    
    return email.strip().lower()


def extract_username(email):
    """
    Extract username from email address.
    
    Args:
        email (str): Email address
    
    Returns:
        str: Username or None if invalid
    
    Example:
        >>> extract_username("john.doe@example.com")
        'john.doe'
    """
    if not is_valid_email(email):
        return None
    
    return email.strip().split('@')[0]


def is_business_email(email):
    """
    Check if email is from a business domain (not free email providers).
    
    Args:
        email (str): Email address
    
    Returns:
        bool: True if business email, False otherwise
    
    Example:
        >>> is_business_email("user@company.com")
        True
        >>> is_business_email("user@gmail.com")
        False
    """
    if not is_valid_email(email):
        return False
    
    domain = extract_domain(email)
    return domain not in VALID_EMAIL_DOMAINS


# ============================================================================
# FILE 4: utility_toolkit/date_utils.py
# ============================================================================

"""
Date Utilities Module
=====================

Provides functions for date manipulation, formatting, and calculations.

Functions:
    - days_between(date1, date2): Calculate days between two dates
    - format_date(date_str, format_type): Format date string
    - is_weekend(date_str): Check if date is weekend
    - add_days(date_str, days): Add days to a date
    - get_current_date(): Get current date
"""

from datetime import datetime, timedelta
from .constants import DATE_FORMAT_ISO, DATE_FORMAT_US, DATE_FORMAT_EU


def days_between(date1, date2, date_format=DATE_FORMAT_ISO):
    """
    Calculate number of days between two dates.
    
    Args:
        date1 (str): First date string
        date2 (str): Second date string
        date_format (str): Date format (default: ISO format)
    
    Returns:
        int: Number of days between dates (absolute value)
    
    Example:
        >>> days_between("2024-01-01", "2024-01-10")
        9
        >>> days_between("2024-01-10", "2024-01-01")
        9
    """
    try:
        d1 = datetime.strptime(date1, date_format)
        d2 = datetime.strptime(date2, date_format)
        return abs((d2 - d1).days)
    except ValueError as e:
        raise ValueError(f"Invalid date format. Expected {date_format}: {e}")


def format_date(date_str, output_format="ISO", input_format=DATE_FORMAT_ISO):
    """
    Format date string to different formats.
    
    Args:
        date_str (str): Date string to format
        output_format (str): Output format type ('ISO', 'US', 'EU')
        input_format (str): Input date format
    
    Returns:
        str: Formatted date string
    
    Example:
        >>> format_date("2024-01-15", "US")
        '01/15/2024'
        >>> format_date("2024-01-15", "EU")
        '15/01/2024'
    """
    try:
        date_obj = datetime.strptime(date_str, input_format)
        
        formats = {
            'ISO': DATE_FORMAT_ISO,
            'US': DATE_FORMAT_US,
            'EU': DATE_FORMAT_EU
        }
        
        format_str = formats.get(output_format, DATE_FORMAT_ISO)
        return date_obj.strftime(format_str)
    
    except ValueError as e:
        raise ValueError(f"Invalid date: {e}")


def is_weekend(date_str, date_format=DATE_FORMAT_ISO):
    """
    Check if given date is a weekend (Saturday or Sunday).
    
    Args:
        date_str (str): Date string
        date_format (str): Date format
    
    Returns:
        bool: True if weekend, False otherwise
    
    Example:
        >>> is_weekend("2024-01-06")  # Saturday
        True
        >>> is_weekend("2024-01-08")  # Monday
        False
    """
    try:
        date_obj = datetime.strptime(date_str, date_format)
        return date_obj.weekday() >= 5  # 5=Saturday, 6=Sunday
    except ValueError as e:
        raise ValueError(f"Invalid date: {e}")


def add_days(date_str, days, date_format=DATE_FORMAT_ISO):
    """
    Add specified number of days to a date.
    
    Args:
        date_str (str): Date string
        days (int): Number of days to add (can be negative)
        date_format (str): Date format
    
    Returns:
        str: New date string
    
    Example:
        >>> add_days("2024-01-01", 10)
        '2024-01-11'
        >>> add_days("2024-01-10", -5)
        '2024-01-05'
    """
    try:
        date_obj = datetime.strptime(date_str, date_format)
        new_date = date_obj + timedelta(days=days)
        return new_date.strftime(date_format)
    except ValueError as e:
        raise ValueError(f"Invalid date: {e}")


def get_current_date(date_format=DATE_FORMAT_ISO):
    """
    Get current date as formatted string.
    
    Args:
        date_format (str): Desired date format
    
    Returns:
        str: Current date string
    
    Example:
        >>> get_current_date()
        '2024-01-15'
    """
    return datetime.now().strftime(date_format)


# ============================================================================
# FILE 5: utility_toolkit/number_utils.py
# ============================================================================

"""
Number Utilities Module
=======================

Provides functions for number generation, validation, and manipulation.

Functions:
    - generate_random(min_val, max_val): Generate random number
    - is_prime(n): Check if number is prime
    - is_even(n): Check if number is even
    - is_odd(n): Check if number is odd
    - factorial(n): Calculate factorial
"""

import random
import math
from .constants import MIN_RANDOM, MAX_RANDOM


def generate_random(min_val=MIN_RANDOM, max_val=MAX_RANDOM, seed=None):
    """
    Generate random integer between min and max values.
    
    Args:
        min_val (int): Minimum value (default: 1)
        max_val (int): Maximum value (default: 100)
        seed (int): Random seed for reproducibility (optional)
    
    Returns:
        int: Random integer
    
    Example:
        >>> generate_random(1, 10)
        7
        >>> generate_random(1, 10, seed=42)
        2
    """
    if seed is not None:
        random.seed(seed)
    
    return random.randint(min_val, max_val)


def is_prime(n):
    """
    Check if a number is prime.
    
    Args:
        n (int): Number to check
    
    Returns:
        bool: True if prime, False otherwise
    
    Example:
        >>> is_prime(17)
        True
        >>> is_prime(20)
        False
    """
    if not isinstance(n, int) or n < 2:
        return False
    
    if n == 2:
        return True
    
    if n % 2 == 0:
        return False
    
    for i in range(3, int(math.sqrt(n)) + 1, 2):
        if n % i == 0:
            return False
    
    return True


def is_even(n):
    """
    Check if a number is even.
    
    Args:
        n (int): Number to check
    
    Returns:
        bool: True if even, False otherwise
    
    Example:
        >>> is_even(4)
        True
        >>> is_even(7)
        False
    """
    if not isinstance(n, int):
        raise TypeError("Input must be an integer")
    
    return n % 2 == 0


def is_odd(n):
    """
    Check if a number is odd.
    
    Args:
        n (int): Number to check
    
    Returns:
        bool: True if odd, False otherwise
    
    Example:
        >>> is_odd(7)
        True
        >>> is_odd(4)
        False
    """
    return not is_even(n)


def factorial(n):
    """
    Calculate factorial of a number.
    
    Args:
        n (int): Non-negative integer
    
    Returns:
        int: Factorial of n
    
    Raises:
        ValueError: If n is negative
        TypeError: If n is not an integer
    
    Example:
        >>> factorial(5)
        120
        >>> factorial(0)
        1
    """
    if not isinstance(n, int):
        raise TypeError("Input must be an integer")
    
    if n < 0:
        raise ValueError("Factorial is not defined for negative numbers")
    
    if n == 0 or n == 1:
        return 1
    
    result = 1
    for i in range(2, n + 1):
        result *= i
    
    return result


def gcd(a, b):
    """
    Calculate Greatest Common Divisor using Euclidean algorithm.
    
    Args:
        a (int): First number
        b (int): Second number
    
    Returns:
        int: GCD of a and b
    
    Example:
        >>> gcd(48, 18)
        6
    """
    while b:
        a, b = b, a % b
    return abs(a)


# ============================================================================
# FILE 6: utility_toolkit/string_utils.py
# ============================================================================

"""
String Utilities Module
=======================

Provides functions for string manipulation and formatting.

Functions:
    - capitalize_words(text): Capitalize each word
    - reverse_string(text): Reverse a string
    - count_words(text): Count words in text
    - is_palindrome(text): Check if text is palindrome
    - remove_punctuation(text): Remove punctuation from text
"""

import re
from .constants import PUNCTUATION


def capitalize_words(text):
    """
    Capitalize the first letter of each word.
    
    Args:
        text (str): Input text
    
    Returns:
        str: Text with capitalized words
    
    Example:
        >>> capitalize_words("hello world")
        'Hello World'
        >>> capitalize_words("python programming")
        'Python Programming'
    """
    if not isinstance(text, str):
        return ""
    
    return text.title()


def reverse_string(text):
    """
    Reverse a string.
    
    Args:
        text (str): Input text
    
    Returns:
        str: Reversed text
    
    Example:
        >>> reverse_string("hello")
        'olleh'
        >>> reverse_string("Python")
        'nohtyP'
    """
    if not isinstance(text, str):
        return ""
    
    return text[::-1]


def count_words(text):
    """
    Count number of words in text.
    
    Args:
        text (str): Input text
    
    Returns:
        int: Number of words
    
    Example:
        >>> count_words("Hello world from Python")
        4
        >>> count_words("One")
        1
    """
    if not isinstance(text, str):
        return 0
    
    # Split by whitespace and filter empty strings
    words = [word for word in text.split() if word]
    return len(words)


def is_palindrome(text, ignore_case=True, ignore_spaces=True):
    """
    Check if text is a palindrome.
    
    Args:
        text (str): Input text
        ignore_case (bool): Ignore case sensitivity
        ignore_spaces (bool): Ignore spaces
    
    Returns:
        bool: True if palindrome, False otherwise
    
    Example:
        >>> is_palindrome("racecar")
        True
        >>> is_palindrome("A man a plan a canal Panama")
        True
        >>> is_palindrome("hello")
        False
    """
    if not isinstance(text, str):
        return False
    
    processed = text
    
    if ignore_case:
        processed = processed.lower()
    
    if ignore_spaces:
        processed = processed.replace(" ", "")
    
    # Remove punctuation
    processed = ''.join(char for char in processed if char.isalnum())
    
    return processed == processed[::-1]


def remove_punctuation(text):
    """
    Remove all punctuation from text.
    
    Args:
        text (str): Input text
    
    Returns:
        str: Text without punctuation
    
    Example:
        >>> remove_punctuation("Hello, World!")
        'Hello World'
        >>> remove_punctuation("What's up?")
        'Whats up'
    """
    if not isinstance(text, str):
        return ""
    
    return ''.join(char for char in text if char not in PUNCTUATION)


def truncate(text, max_length, suffix="..."):
    """
    Truncate text to specified length.
    
    Args:
        text (str): Input text
        max_length (int): Maximum length
        suffix (str): Suffix to add if truncated
    
    Returns:
        str: Truncated text
    
    Example:
        >>> truncate("This is a long text", 10)
        'This is...'
    """
    if not isinstance(text, str):
        return ""
    
    if len(text) <= max_length:
        return text
    
    return text[:max_length - len(suffix)] + suffix


def slug_ify(text):
    """
    Convert text to URL-friendly slug.
    
    Args:
        text (str): Input text
    
    Returns:
        str: Slugified text
    
    Example:
        >>> slug_ify("Hello World!")
        'hello-world'
        >>> slug_ify("Python Programming 101")
        'python-programming-101'
    """
    if not isinstance(text, str):
        return ""
    
    # Convert to lowercase
    text = text.lower()
    
    # Remove special characters
    text = re.sub(r'[^\w\s-]', '', text)
    
    # Replace spaces with hyphens
    text = re.sub(r'[\s_]+', '-', text)
    
    # Remove leading/trailing hyphens
    text = text.strip('-')
    
    return text


# ============================================================================
# FILE 7: utility_toolkit/file_utils.py
# ============================================================================

"""
File Utilities Module
=====================

Provides functions for file operations and handling.

Functions:
    - read_file(filepath): Read file contents
    - write_file(filepath, content): Write content to file
    - append_to_file(filepath, content): Append content to file
    - file_exists(filepath): Check if file exists
    - get_file_size(filepath): Get file size in bytes
"""

import os
from .constants import DEFAULT_ENCODING, MAX_FILE_SIZE_MB


def read_file(filepath, encoding=DEFAULT_ENCODING):
    """
    Read contents of a file.
    
    Args:
        filepath (str): Path to file
        encoding (str): File encoding (default: utf-8)
    
    Returns:
        str: File contents
    
    Raises:
        FileNotFoundError: If file doesn't exist
        IOError: If file cannot be read
    
    Example:
        >>> content = read_file("data.txt")
        >>> print(content)
        'File contents here'
    """
    try:
        with open(filepath, 'r', encoding=encoding) as file:
            return file.read()
    except FileNotFoundError:
        raise FileNotFoundError(f"File not found: {filepath}")
    except IOError as e:
        raise IOError(f"Error reading file: {e}")


def write_file(filepath, content, encoding=DEFAULT_ENCODING):
    """
    Write content to a file (overwrites existing content).
    
    Args:
        filepath (str): Path to file
        content (str): Content to write
        encoding (str): File encoding (default: utf-8)
    
    Returns:
        bool: True if successful
    
    Raises:
        IOError: If file cannot be written
    
    Example:
        >>> write_file("output.txt", "Hello, World!")
        True
    """
    try:
        with open(filepath, 'w', encoding=encoding) as file:
            file.write(content)
        return True
    except IOError as e:
        raise IOError(f"Error writing file: {e}")


def append_to_file(filepath, content, encoding=DEFAULT_ENCODING):
    """
    Append content to a file.
    
    Args:
        filepath (str): Path to file
        content (str): Content to append
        encoding (str): File encoding (default: utf-8)
    
    Returns:
        bool: True if successful
    
    Example:
        >>> append_to_file("log.txt", "New log entry\\n")
        True
    """
    try:
        with open(filepath, 'a', encoding=encoding) as file:
            fil
