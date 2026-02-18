```python name=app.py
#!/usr/bin/env python3
"""
User Management CLI App

A command-line application to manage users—very close to real admin tools.

Features:
- Add user
- View users
- Update user
- Delete user
- Store users in a JSON file
- Use CLI arguments via argparse
- Validate input
- Handle errors gracefully

Example CLI Commands:
    python app.py add --name John --email john@test.com
    python app.py list
    python app.py update --email john@test.com --name "John Doe"
    python app.py delete --email john@test.com
"""

import argparse
import json
import os
import re
import sys
from datetime import datetime
from typing import List, Dict, Optional

DEFAULT_DB_PATH = "users.json"
EMAIL_REGEX = re.compile(r"^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$")


# -------------------------------
# Storage and Validation Utilities
# -------------------------------
def load_users(path: str = DEFAULT_DB_PATH) -> List[Dict]:
    """Load users from a JSON file. Returns an empty list if file doesn't exist or is empty."""
    if not os.path.exists(path):
        return []
    try:
        with open(path, "r", encoding="utf-8") as f:
            data = f.read().strip()
            if not data:
                return []
            return json.loads(data)
    except json.JSONDecodeError:
        print(f"Error: '{path}' contains invalid JSON.", file=sys.stderr)
        return []
    except OSError as e:
        print(f"Error reading '{path}': {e}", file=sys.stderr)
        sys.exit(1)


def save_users(users: List[Dict], path: str = DEFAULT_DB_PATH) -> None:
    """Save users to a JSON file with pretty indentation."""
    try:
        with open(path, "w", encoding="utf-8") as f:
            json.dump(users, f, indent=2, ensure_ascii=False)
    except OSError as e:
        print(f"Error writing '{path}': {e}", file=sys.stderr)
        sys.exit(1)


def validate_email(email: str) -> bool:
    """Return True if email is valid."""
    return isinstance(email, str) and bool(EMAIL_REGEX.fullmatch(email.strip()))


def validate_name(name: str) -> bool:
    """Return True if name is non-empty after stripping whitespace."""
    return isinstance(name, str) and bool(name.strip())


def find_user_index(users: List[Dict], email: str) -> Optional[int]:
    """Return index of user by email or None if not found."""
    email = email.strip().lower()
    for i, u in enumerate(users):
        if u.get("email", "").lower() == email:
            return i
    return None


# -------------------------------
# CRUD Operations
# -------------------------------
def add_user(name: str, email: str, path: str) -> int:
    """Add a user. Prints success info and returns exit code (0 for success, 1 for failure)."""
    if not validate_name(name):
        print("Error: Name is required and must be non-empty.", file=sys.stderr)
        return 1
    if not validate_email(email):
        print("Error: Invalid email format.", file=sys.stderr)
        return 1

    users = load_users(path)
    if find_user_index(users, email) is not None:
        print("Error: A user with this email already exists.", file=sys.stderr)
        return 1

    user = {
        "name": name.strip(),
        "email": email.strip().lower(),
        "created_at": datetime.utcnow().isoformat(timespec="seconds") + "Z",
        "updated_at": None,
    }
    users.append(user)
    save_users(users, path)

    # Sample Output requirements
    print("User added successfully")
    print(f"Name: {user['name']}")
    print(f"Email: {user['email']}")
    return 0


def list_users(path: str) -> int:
    """List users. Prints each user's name/email and returns exit code."""
    users = load_users(path)
    if not users:
        print("No users found.")
        return 0

    for user in users:
        print(f"Name: {user.get('name', '')}")
        print(f"Email: {user.get('email', '')}")
        print("-" * 20)
    return 0


def update_user(email: str, new_name: Optional[str], new_email: Optional[str], path: str) -> int:
    """Update a user. Returns exit code."""
    if not validate_email(email):
        print("Error: Invalid current email format.", file=sys.stderr)
        return 1

    users = load_users(path)
    idx = find_user_index(users, email)
    if idx is None:
        print("Error: User not found.", file=sys.stderr)
        return 1

    updated = False

    if new_name is not None:
        if not validate_name(new_name):
            print("Error: New name must be non-empty.", file=sys.stderr)
            return 1
        users[idx]["name"] = new_name.strip()
        updated = True

    if new_email is not None:
        if not validate_email(new_email):
            print("Error: New email format is invalid.", file=sys.stderr)
            return 1
        # Ensure the new email isn't already used by another user
        if (new_email.strip().lower() != users[idx]["email"].lower()
                and find_user_index(users, new_email) is not None):
            print("Error: Another user already has this email.", file=sys.stderr)
            return 1
        users[idx]["email"] = new_email.strip().lower()
        updated = True

    if not updated:
        print("Nothing to update. Provide --name and/or --new-email.", file=sys.stderr)
        return 1

    users[idx]["updated_at"] = datetime.utcnow().isoformat(timespec="seconds") + "Z"
    save_users(users, path)
    print("User updated successfully")
    print(f"Name: {users[idx]['name']}")
    print(f"Email: {users[idx]['email']}")
    return 0


def delete_user(email: str, path: str) -> int:
    """Delete a user by email. Returns exit code."""
    if not validate_email(email):
        print("Error: Invalid email format.", file=sys.stderr)
        return 1

    users = load_users(path)
    idx = find_user_index(users, email)
    if idx is None:
        print("Error: User not found.", file=sys.stderr)
        return 1

    removed = users.pop(idx)
    save_users(users, path)
    print("User deleted successfully")
    print(f"Name: {removed.get('name', '')}")
    print(f"Email: {removed.get('email', '')}")
    return 0


# -------------------------------
# Argparse CLI
# -------------------------------
def build_parser() -> argparse.ArgumentParser:
    parser = argparse.ArgumentParser(
        description="User Management CLI App - Manage users with CRUD operations.",
        epilog="Examples:\n"
               "  python app.py add --name John --email john@test.com\n"
               "  python app.py list\n"
               "  python app.py update --email john@test.com --name 'John Doe'\n"
               "  python app.py delete --email john@test.com",
        formatter_class=argparse.RawDescriptionHelpFormatter,
    )
    parser.add_argument(
        "--file", "-f",
        default=DEFAULT_DB_PATH,
        help=f"Path to users JSON file (default: {DEFAULT_DB_PATH})"
    )

    subparsers = parser.add_subparsers(dest="command", required=True)

    # add
    p_add = subparsers.add_parser("add", help="Add a new user")
    p_add.add_argument("--name", required=True, help="User's full name")
    p_add.add_argument("--email", required=True, help="User's email address")

    # list
    subparsers.add_parser("list", help="List all users")

    # update
    p_update = subparsers.add_parser("update", help="Update an existing user")
    p_update.add_argument("--email", required=True, help="Current email of the user")
    p_update.add_argument("--name", help="New full name")
    p_update.add_argument("--new-email", help="New email address")

    # delete
    p_delete = subparsers.add_parser("delete", help="Delete a user")
    p_delete.add_argument("--email", required=True, help="Email of the user to delete")

    return parser


def main() -> None:
    parser = build_parser()
    args = parser.parse_args()

    db_path = args.file

    if args.command == "add":
        sys.exit(add_user(args.name, args.email, db_path))
    elif args.command == "list":
        sys.exit(list_users(db_path))
    elif args.command == "update":
        sys.exit(update_user(args.email, args.name, args.new_email, db_path))
    elif args.command == "delete":
        sys.exit(delete_user(args.email, db_path))
    else:
        parser.print_help()
        sys.exit(1)


if __name__ == "__main__":
    main()
```

````markdown name=README.md
# User Management CLI App

A command-line application to manage users—very close to real admin tools.

## Features
- Add user
- View users
- Update user
- Delete user
- Store users in a JSON file
- Use CLI arguments (`argparse`)
- Validate input (name, email)
- Handle errors gracefully

## Requirements
- Python 3.8+
- No external dependencies

## Usage

```bash
# Add a user
python app.py add --name John --email john@test.com

# List users
python app.py list

# Update user's name
python app.py update --email john@test.com --name "John Doe"

# Update user's email
python app.py update --email john@test.com --new-email john.doe@test.com

# Delete a user
python app.py delete --email john@test.com
```

## Sample Output

```
User added successfully
Name: John
Email: john@test.com
```

## Data Storage

- Default file: `users.json` (can be changed with `--file`)
- Structure per user:
```json
{
  "name": "John",
  "email": "john@test.com",
  "created_at": "2026-02-18T10:00:00Z",
  "updated_at": null
}
```

## Error Handling Examples
- Duplicate email → "Error: A user with this email already exists."
- Invalid email → "Error: Invalid email format."
- Missing user → "Error: User not found."
- Empty name → "Error: Name is required and must be non-empty."

## Suggested Enhancements (New Functionality)
- Import/Export:
  - Import users from CSV.
  - Export users to CSV/Excel.
- Search & Filters:
  - Search by name/email (substring, exact match).
  - Filter by creation date ranges.
- Sorting:
  - Sort list by name, email, created_at.
- Authentication:
  - Secure operations behind a simple admin password or token.
- Bulk Operations:
  - Bulk add/update/delete via file input.
- Validation:
  - Domain whitelist/blacklist for emails.
  - Disallow disposable email addresses.
- Logging & Audit:
  - Log all changes to a file.
  - Keep audit history per user (who changed what and when).
- Backups:
  - Automatic daily backups of `users.json` with rotation.
- Concurrency Safety:
  - File locking (e.g., `fcntl`/portalocker) to prevent concurrent writes corruption.
- Configurable Settings:
  - Read defaults from `config.toml` or environment variables.
- Performance:
  - Switch to SQLite with `sqlite3` for larger datasets.
- UX:
  - Colored CLI output for success/error messages.
  - Interactive prompts when arguments are omitted (e.g., guided add/update).

## Developer Notes
- Extend the schema by adding fields like `role`, `status`, `last_login`.
- Add unit tests using `unittest` or `pytest`.
- Integrate with CI to run tests on each change.
````
