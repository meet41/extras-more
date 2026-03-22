Perfect! I'll generate **all comprehensive notes content** for you now. Let me create detailed documentation for all 19 topics:

---

# **COMPREHENSIVE FASTAPI LEARNING GUIDE**
## Complete Notes from Beginner to Advanced

---

# **TOPIC 1: Introduction to FastAPI & Environment Setup**

## What is FastAPI?

FastAPI is a modern, high-performance Python web framework for building APIs using standard Python type hints. It's built on top of Starlette (ASGI toolkit) and Pydantic (data validation library).

### Key Characteristics:
- **Fast**: Comparable performance to Node.js and Go
- **Easy to learn**: Simple, intuitive syntax
- **Type-safe**: Uses Python type hints for validation and documentation
- **Auto-documented**: Generates interactive API documentation automatically
- **Production-ready**: Built for building reliable, scalable APIs
- **Async-first**: Native support for async/await

### Why Choose FastAPI?
1. **Speed**: One of the fastest Python frameworks available
2. **Developer Experience**: Less boilerplate, more intuitive code
3. **Automatic validation**: Pydantic validates all input data
4. **Documentation**: Swagger UI and ReDoc generated automatically
5. **Testing**: Easy to test with built-in TestClient
6. **Modern Python**: Uses Python 3.6+ features (type hints, async)

## Installation & Setup

### Step 1: Create Virtual Environment
```bash
# Using venv
python -m venv fastapi_env

# Activate it
# On Windows:
fastapi_env\Scripts\activate
# On macOS/Linux:
source fastapi_env/bin/activate
```

### Step 2: Install FastAPI and Uvicorn
```bash
pip install fastapi
pip install "uvicorn[standard]"
```

### Step 3: Create First FastAPI Application
```python
from fastapi import FastAPI

# Create an instance of FastAPI
app = FastAPI()

# Define a route using a decorator
@app.get("/")
def read_root():
    return {"message": "Hello, World!"}

# Another route
@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}
```

### Step 4: Run Development Server
```bash
# Run with auto-reload (watches for changes)
uvicorn main:app --reload

# Without reload
uvicorn main:app

# Custom host and port
uvicorn main:app --host 0.0.0.0 --port 8000
```

Access your API at: `http://localhost:8000`
- API Documentation: `http://localhost:8000/docs` (Swagger UI)
- Alternative Docs: `http://localhost:8000/redoc` (ReDoc)

## Project Structure Best Practices

### Simple Project Structure (Beginner)
```
project/
├── main.py           # Entry point
├── requirements.txt  # Dependencies
└── README.md        # Documentation
```

### Intermediate Project Structure
```
project/
├── app/
│   ├── __init__.py
│   ├── main.py            # FastAPI app creation
│   ├── models.py          # Pydantic models
│   └── routers/           # API routes
│       ├── users.py
│       ├── items.py
│       └── posts.py
├── tests/
│   └── test_main.py
├── requirements.txt
└── README.md
```

### Production Project Structure
```
project/
├── app/
│   ├── __init__.py
│   ├── main.py                 # App initialization
│   ├── api/
│   │   ├── __init__.py
│   │   └── v1/
│   │       ├── __init__.py
│   │       ├── endpoints/
│   │       │   ├── users.py
│   │       │   ├── items.py
│   │       │   └── posts.py
│   │       └── dependencies.py
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py          # Settings
│   │   ├── security.py        # Security utilities
│   │   └── constants.py       # Constants
│   ├── models/
│   │   ├── __init__.py
│   │   ├── database.py        # SQLAlchemy models
│   │   └── schemas.py         # Pydantic schemas
│   ├── db/
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── session.py         # Database session
│   │   └── crud.py            # Database operations
│   └── utils/
│       ├── __init__.py
│       └── helpers.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py            # Test configuration
│   ├── test_users.py
│   ├── test_items.py
│   └── test_posts.py
├── .env                        # Environment variables
├── .env.example               # Example env file
├── .gitignore                 # Git ignore rules
├── requirements.txt           # Dependencies
├── docker-compose.yml         # Docker setup
├── Dockerfile                 # Container image
└── README.md                  # Documentation
```

## Understanding the Request/Response Cycle

```
Client Request
    ↓
FastAPI Router (matches URL pattern)
    ↓
Path/Query Parameters Extracted
    ↓
Request Body Validated (Pydantic)
    ↓
Dependencies Resolved (Depends)
    ↓
Endpoint Function Called
    ↓
Response Serialized (Pydantic)
    ↓
Response Returned to Client
```

## Key Concepts

### ASGI (Asynchronous Server Gateway Interface)
- Standard interface for Python async web frameworks
- Allows handling multiple concurrent requests
- Required for async/await support

### Uvicorn
- ASGI web server for running FastAPI applications
- Single-threaded, event-driven architecture
- Perfect for development and production

### Pydantic
- Data validation using Python type hints
- Automatic parsing and error reporting
- Generates JSON schema automatically

---

# **TOPIC 2: Creating APIs (Routing & HTTP Methods)**

## REST API Concepts

**REST** (Representational State Transfer) is an architectural style for designing networked applications.

### REST Principles:
1. **Resources**: Everything is a resource (users, posts, comments)
2. **Identifiers**: Resources have unique identifiers (URLs/URIs)
3. **Representations**: Resources are represented in various formats (JSON, XML)
4. **Standard Methods**: Use standard HTTP methods to manipulate resources
5. **Stateless**: Each request contains all information needed
6. **Self-documenting**: API structure is clear and predictable

### Example REST Concepts:
```
Resource: /users
Operations:
  GET /users          → Get all users
  POST /users         → Create new user
  GET /users/1        → Get user with ID 1
  PUT /users/1        → Update user with ID 1
  DELETE /users/1     → Delete user with ID 1
```

## HTTP Methods in FastAPI

### GET - Retrieve Data (Safe & Idempotent)
- Used to retrieve data without modifying anything
- Safe: Doesn't modify server state
- Idempotent: Multiple calls produce same result
- Can be cached

```python
@app.get("/users")
def get_all_users():
    return [{"id": 1, "name": "John"}, {"id": 2, "name": "Jane"}]

@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"id": user_id, "name": "John"}
```

### POST - Create Data (Not Idempotent)
- Used to create new resources
- Not safe: Modifies server state
- Not idempotent: Multiple calls create multiple resources
- Sends data in request body
- Returns 201 Created status code

```python
@app.post("/users")
def create_user(user_data: dict):
    # Process and save user
    return {"id": 1, "name": user_data["name"], "message": "User created"}
```

### PUT - Replace Complete Resource (Idempotent)
- Used to replace entire resource
- Not safe: Modifies server state
- Idempotent: Multiple calls result in same state
- Replaces whole object, all fields required
- Returns 200 OK or 204 No Content

```python
@app.put("/users/{user_id}")
def update_user(user_id: int, updated_data: dict):
    # Replace entire user
    return {"id": user_id, "name": updated_data["name"], "message": "User updated"}
```

### PATCH - Partial Update (May/May Not Be Idempotent)
- Used to partially update resource
- Not safe: Modifies server state
- Can update specific fields only
- More flexible than PUT

```python
@app.patch("/users/{user_id}")
def partial_update_user(user_id: int, updated_data: dict):
    # Update only provided fields
    return {"id": user_id, "message": "User partially updated"}
```

### DELETE - Remove Resource (Idempotent)
- Used to delete resources
- Not safe: Modifies server state
- Idempotent: Multiple calls result in same state
- Returns 204 No Content on success

```python
@app.delete("/users/{user_id}")
def delete_user(user_id: int):
    # Delete user
    return {"message": f"User {user_id} deleted"}
```

### HEAD - Like GET but no response body (Idempotent)
```python
@app.head("/users/{user_id}")
def head_user(user_id: int):
    return None  # Headers only, no body
```

### OPTIONS - Describe communication options (Safe & Idempotent)
```python
@app.options("/users")
def options_users():
    return {"allowed_methods": ["GET", "POST"]}
```

## Path Parameters

Path parameters are variable parts of the URL path.

### Single Path Parameter
```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    # user_id is extracted from the URL path
    return {"user_id": user_id, "name": "John"}

# Access: http://localhost:8000/users/123
```

### Multiple Path Parameters
```python
@app.get("/users/{user_id}/posts/{post_id}")
def get_user_post(user_id: int, post_id: int):
    return {"user_id": user_id, "post_id": post_id}

# Access: http://localhost:8000/users/123/posts/456
```

### Type Validation in Path Parameters
```python
@app.get("/items/{item_id}")
def get_item(item_id: int):  # Type hint ensures integer
    return {"item_id": item_id}

# This works: /items/42
# This returns 422 error: /items/abc
```

### Enum Path Parameters
```python
from enum import Enum

class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"

@app.get("/models/{model_name}")
def get_model(model_name: ModelName):
    if model_name == ModelName.alexnet:
        return {"model": "AlexNet"}
    return {"model": model_name}

# Access: /models/resnet
```

### File Path Parameters
```python
@app.get("/files/{file_path:path}")
def get_file(file_path: str):
    return {"file_path": file_path}

# Access: /files/home/user/documents/file.txt
# file_path = "home/user/documents/file.txt"
```

## Query Parameters

Query parameters are additional parameters in the URL after `?`.

### Single Query Parameter
```python
@app.get("/items/")
def read_items(skip: int = 0):
    return {"skip": skip}

# Access: http://localhost:8000/items/?skip=10
# If not provided: skip defaults to 0
```

### Multiple Query Parameters
```python
@app.get("/items/")
def read_items(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}

# Access: http://localhost:8000/items/?skip=5&limit=20
```

### Optional Query Parameters
```python
from typing import Optional

@app.get("/items/")
def read_items(skip: int = 0, limit: Optional[int] = None):
    return {"skip": skip, "limit": limit}

# Both work:
# /items/?skip=5
# /items/?skip=5&limit=20
```

### Query Parameter with Default None
```python
from typing import Optional

@app.get("/items/")
def read_items(q: Optional[str] = None):
    if q:
        return {"items": [1, 2, 3], "q": q}
    return {"items": [1, 2, 3]}

# Both work:
# /items/
# /items/?q=hello
```

### Required Query Parameters
```python
@app.get("/items/")
def read_items(q: str):  # No default value = required
    return {"q": q}

# /items/?q=hello  ✓ Works
# /items/           ✗ Returns 422 error
```

### Query Parameter List/Array
```python
from typing import List

@app.get("/items/")
def read_items(q: List[str] = []):
    return {"q": q}

# Access: /items/?q=a&q=b&q=c
# Result: {"q": ["a", "b", "c"]}
```

## Request Body Handling

Request body is typically used with POST, PUT, PATCH to send data to server.

### Simple Request Body
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float
    description: str = None

@app.post("/items/")
def create_item(item: Item):
    return item

# Request body (JSON):
# {
#   "name": "Laptop",
#   "price": 999.99,
#   "description": "A powerful laptop"
# }
```

### Combining Path, Query, and Body Parameters
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

@app.post("/users/{user_id}/items/")
def create_user_item(
    user_id: int,              # Path parameter
    item: Item,                # Body parameter
    q: str = None              # Query parameter
):
    return {
        "user_id": user_id,
        "item": item,
        "query": q
    }

# Access: POST /users/42/items/?q=expensive
# Body:
# {
#   "name": "Phone",
#   "price": 899.99
# }
```

### Multiple Body Parameters
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

class User(BaseModel):
    name: str
    email: str

@app.post("/create/")
def create(item: Item, user: User):
    return {"item": item, "user": user}

# Request body:
# {
#   "item": {"name": "Laptop", "price": 999},
#   "user": {"name": "John", "email": "john@example.com"}
# }
```

## Organizing Routes with APIRouter

APIRouter allows modular organization of routes.

### Basic APIRouter Usage
```python
from fastapi import APIRouter

# Create a router
router = APIRouter()

@router.get("/")
def read_users():
    return [{"id": 1, "name": "John"}]

@router.post("/")
def create_user():
    return {"id": 1, "name": "New User"}

# In main.py
from fastapi import FastAPI
from routers import users

app = FastAPI()
app.include_router(users.router)

# Routes are now:
# GET /
# POST /
```

### APIRouter with Prefix
```python
from fastapi import APIRouter

router = APIRouter(prefix="/users", tags=["users"])

@router.get("/")
def read_users():
    return [{"id": 1, "name": "John"}]

@router.get("/{user_id}")
def read_user(user_id: int):
    return {"id": user_id, "name": "John"}

# In main.py
app.include_router(router)

# Routes are now:
# GET /users
# GET /users/{user_id}
```

### APIRouter with Dependencies
```python
from fastapi import APIRouter, Depends

async def verify_token(token: str):
    return token

router = APIRouter(
    prefix="/items",
    tags=["items"],
    dependencies=[Depends(verify_token)]
)

@router.get("/")
def read_items():
    return [{"id": 1, "name": "Item"}]

# All routes in this router require token
```

### Multiple Routers
```python
# routers/users.py
from fastapi import APIRouter
router = APIRouter(prefix="/users", tags=["users"])

@router.get("/")
def read_users():
    return []

# routers/items.py
from fastapi import APIRouter
router = APIRouter(prefix="/items", tags=["items"])

@router.get("/")
def read_items():
    return []

# main.py
from fastapi import FastAPI
from routers import users, items

app = FastAPI()
app.include_router(users.router)
app.include_router(items.router)

# Routes:
# GET /users
# GET /items
```

---

# **TOPIC 3: Pydantic Models**

## What is Pydantic?

Pydantic is a data validation library that uses Python type hints. It ensures data is valid before your application processes it.

### Key Features:
- Type validation based on Python type hints
- Automatic error messages for invalid data
- Converts data types when possible
- Creates JSON Schema automatically
- Inheritable and composable

## Creating Pydantic Models

### Basic Model
```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int
    email: str

# Usage
user = User(name="John", age=30, email="john@example.com")
print(user.name)  # "John"
```

### Model with Default Values
```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int = 18  # Default value
    email: str = "no-email@example.com"
    is_active: bool = True

# Both work:
user1 = User(name="John", age=30, email="john@example.com")
user2 = User(name="Jane")  # Uses default values for age, email, is_active
```

## Request and Response Models

### Using Model for Request Validation
```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float
    description: str = None

@app.post("/items/")
def create_item(item: Item):
    return item

# FastAPI automatically:
# 1. Reads the request body as JSON
# 2. Validates the data with Item model
# 3. Returns error if data is invalid
# 4. Serializes response as JSON
```

### Using Model for Response
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

@app.post("/items/", response_model=Item)
def create_item(item: Item):
    # Only fields defined in Item model are returned
    return item
```

### Different Request and Response Models
```python
from pydantic import BaseModel

class UserCreate(BaseModel):
    name: str
    email: str
    password: str

class UserResponse(BaseModel):
    name: str
    email: str
    id: int

@app.post("/users/", response_model=UserResponse)
def create_user(user: UserCreate):
    # user has name, email, password
    # response only has name, email, id
    return {"id": 1, "name": user.name, "email": user.email}
```

## Field Validation and Constraints

### Using Field for Validation
```python
from pydantic import BaseModel, Field

class Item(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    price: float = Field(..., gt=0)
    quantity: int = Field(default=1, ge=0)

# ... is a required field
# gt = greater than
# ge = greater than or equal
# lt = less than
# le = less than or equal
```

### Common Field Constraints
```python
from pydantic import BaseModel, Field

class User(BaseModel):
    username: str = Field(..., min_length=3, max_length=50)
    email: str = Field(..., regex=r"^[\w\.-]+@[\w\.-]+\.\w+$")
    age: int = Field(..., ge=0, le=150)
    price: float = Field(..., gt=0, decimal_places=2)
```

### Validation with Validators
```python
from pydantic import BaseModel, validator

class User(BaseModel):
    name: str
    age: int

    @validator('age')
    def age_must_be_positive(cls, v):
        if v < 0:
            raise ValueError('Age must be positive')
        return v

    @validator('name')
    def name_must_not_be_empty(cls, v):
        if not v or v.isspace():
            raise ValueError('Name cannot be empty')
        return v
```

## Optional Fields and Default Values

### Optional Fields
```python
from typing import Optional
from pydantic import BaseModel

class User(BaseModel):
    name: str
    email: Optional[str] = None
    phone: Optional[str] = None

# Works:
user1 = User(name="John")
user2 = User(name="John", email="john@example.com")
```

### Default Values
```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int = 18
    is_active: bool = True
    role: str = "user"
```

### Required vs Optional
```python
from typing import Optional
from pydantic import BaseModel

class User(BaseModel):
    name: str              # Required, no default
    email: str             # Required, no default
    phone: Optional[str] = None  # Optional
    age: int = 18          # Optional with default
```

## Model Inheritance

### Basic Inheritance
```python
from pydantic import BaseModel

class BaseUser(BaseModel):
    name: str
    email: str

class User(BaseUser):
    user_id: int
    is_active: bool = True

# User inherits name and email from BaseUser
user = User(name="John", email="john@example.com", user_id=1)
```

### Multiple Inheritance
```python
from pydantic import BaseModel

class PersonBase(BaseModel):
    name: str
    age: int

class EmployeeBase(BaseModel):
    employee_id: str
    department: str

class Employee(PersonBase, EmployeeBase):
    salary: float

emp = Employee(
    name="John",
    age=30,
    employee_id="E123",
    department="Engineering",
    salary=50000
)
```

## Nested Models

### Simple Nested Model
```python
from pydantic import BaseModel

class Address(BaseModel):
    street: str
    city: str
    country: str

class User(BaseModel):
    name: str
    address: Address

# Usage
user = User(
    name="John",
    address={
        "street": "123 Main St",
        "city": "New York",
        "country": "USA"
    }
)

print(user.address.city)  # "New York"
```

### Nested Lists
```python
from pydantic import BaseModel
from typing import List

class Item(BaseModel):
    name: str
    price: float

class Order(BaseModel):
    order_id: int
    items: List[Item]

# Usage
order = Order(
    order_id=1,
    items=[
        {"name": "Laptop", "price": 999},
        {"name": "Mouse", "price": 29}
    ]
)

print(order.items[0].name)  # "Laptop"
```

### Deeply Nested Models
```python
from pydantic import BaseModel
from typing import List

class Address(BaseModel):
    street: str
    city: str

class Contact(BaseModel):
    email: str
    phone: str
    address: Address

class User(BaseModel):
    name: str
    contact: Contact

user = User(
    name="John",
    contact={
        "email": "john@example.com",
        "phone": "555-1234",
        "address": {
            "street": "123 Main St",
            "city": "New York"
        }
    }
)
```

## Model Configuration

### Config Class
```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    email: str

    class Config:
        schema_extra = {
            "example": {
                "name": "John Doe",
                "email": "john@example.com"
            }
        }
```

### JSON Schema Configuration
```python
from pydantic import BaseModel, Field

class User(BaseModel):
    name: str = Field(..., description="User's full name")
    age: int = Field(..., gt=0, description="User's age")

    class Config:
        title = "User Model"
        schema_extra = {
            "example": {
                "name": "John Doe",
                "age": 30
            }
        }
```

---

# **TOPIC 4: Automatic API Documentation**

## Swagger UI (/docs)

FastAPI automatically generates interactive API documentation at `/docs`.

### Accessing Swagger UI
```
http://localhost:8000/docs
```

### Features:
- Try out endpoints directly from the browser
- See request/response examples
- View all parameters and their types
- Automatic form generation for POST/PUT requests
- Real API calls executed from documentation

### Example API with Documentation
```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float

@app.get("/items/{item_id}")
def get_item(item_id: int):
    """
    Get an item by ID.
    
    - **item_id**: The ID of the item to retrieve
    """
    return {"item_id": item_id}

@app.post("/items/")
def create_item(item: Item):
    """Create a new item."""
    return item
```

## ReDoc (/redoc)

Alternative API documentation interface at `/redoc`.

### Accessing ReDoc
```
http://localhost:8000/redoc
```

### Features:
- Organized, hierarchical documentation
- Mobile-friendly design
- Good for exploring API structure
- Read-only (can't test endpoints)

## Customizing API Metadata

### Title, Version, and Description
```python
from fastapi import FastAPI

app = FastAPI(
    title="My Awesome API",
    version="1.0.0",
    description="This is an awesome API with documentation."
)
```

### Complete Metadata Configuration
```python
from fastapi import FastAPI

app = FastAPI(
    title="Blog API",
    version="1.0.0",
    description="A simple blog API with posts and comments",
    terms_of_service="https://example.com/terms/",
    contact={
        "name": "API Support",
        "url": "https://example.com/support/",
        "email": "support@example.com",
    },
    license_info={
        "name": "Apache 2.0",
        "url": "https://www.apache.org/licenses/LICENSE-2.0.html",
    },
)
```

## Adding Examples and Descriptions

### Field Examples
```python
from pydantic import BaseModel, Field

class Item(BaseModel):
    name: str = Field(..., example="Laptop Computer")
    price: float = Field(..., example=999.99)
    description: str = Field(..., example="A powerful laptop")
```

### Method Documentation
```python
@app.post("/items/", response_description="The created item")
def create_item(item: Item):
    """
    Create an item with all the information:
    
    - **name**: Item name
    - **price**: Item price
    - **description**: Item description
    """
    return item
```

### Response Descriptions
```python
@app.get(
    "/items/{item_id}",
    response_description="The item with the requested ID"
)
def get_item(item_id: int):
    """Retrieve an item by its ID."""
    return {"item_id": item_id}
```

### tags Parameter
```python
@app.get("/users/", tags=["users"])
def get_users():
    """Get all users"""
    return []

@app.post("/users/", tags=["users"])
def create_user():
    """Create a new user"""
    return {}

@app.get("/posts/", tags=["posts"])
def get_posts():
    """Get all posts"""
    return []
```

## Complete Documentation Example

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field
from typing import Optional

app = FastAPI(
    title="Blog API",
    version="1.0.0",
    description="A complete blog API with users, posts, and comments"
)

class Item(BaseModel):
    """Item model for creating and updating items"""
    name: str = Field(..., min_length=1, max_length=100, example="Laptop")
    price: float = Field(..., gt=0, example=999.99)
    description: Optional[str] = Field(None, example="A powerful laptop")
    
    class Config:
        schema_extra = {
            "example": {
                "name": "Laptop Computer",
                "price": 1299.99,
                "description": "A high-performance laptop for professionals"
            }
        }

@app.get(
    "/items/{item_id}",
    response_model=Item,
    tags=["items"],
    summary="Get an item",
    response_description="The item with the requested ID"
)
def get_item(item_id: int):
    """
    Get a single item by its ID.
    
    Parameters:
    - **item_id**: The unique identifier of the item
    
    Returns:
    - **name**: Item name
    - **price**: Item price
    - **description**: Item description
    """
    return {"name": "Laptop", "price": 999.99, "description": "A laptop"}

@app.post(
    "/items/",
    response_model=Item,
    tags=["items"],
    status_code=201,
    summary="Create a new item",
    response_description="The created item"
)
def create_item(item: Item):
    """
    Create a new item.
    
    The endpoint accepts an item with:
    - **name**: Item name (1-100 characters)
    - **price**: Item price (must be positive)
    - **description**: Optional item description
    
    Returns the created item with all details.
    """
    return item
```

---

# **TOPIC 5: Status Codes & Response Handling**

## Understanding HTTP Status Codes

### 2xx Success Codes
- **200 OK**: Request successful, data returned
- **201 Created**: Resource successfully created
- **204 No Content**: Request successful, no content to return

### 3xx Redirect Codes
- **301 Moved Permanently**: Resource moved to new location
- **302 Found**: Temporary redirect
- **304 Not Modified**: Cache still valid

### 4xx Client Error Codes
- **400 Bad Request**: Invalid request syntax
- **401 Unauthorized**: Authentication required
- **403 Forbidden**: Authenticated but not allowed
- **404 Not Found**: Resource doesn't exist
- **422 Unprocessable Entity**: Validation error

### 5xx Server Error Codes
- **500 Internal Server Error**: Server error
- **502 Bad Gateway**: Gateway error
- **503 Service Unavailable**: Server temporarily unavailable

## Returning Proper HTTP Status Codes

### Default Status Codes
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/items/")           # Defaults to 200
def get_items():
    return [{"id": 1}]

@app.post("/items/")          # Defaults to 200
def create_item():
    return {"id": 1}
```

### Specifying Custom Status Code
```python
from fastapi import FastAPI, status

app = FastAPI()

@app.post("/items/", status_code=status.HTTP_201_CREATED)
def create_item():
    return {"id": 1, "message": "Item created"}

@app.delete("/items/{item_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_item(item_id: int):
    return None  # 204 No Content expects no response body
```

### Status Code Constants
```python
from fastapi import status

# Instead of using magic numbers, use constants:
status.HTTP_200_OK           # 200
status.HTTP_201_CREATED      # 201
status.HTTP_204_NO_CONTENT   # 204
status.HTTP_400_BAD_REQUEST  # 400
status.HTTP_401_UNAUTHORIZED # 401
status.HTTP_403_FORBIDDEN    # 403
status.HTTP_404_NOT_FOUND    # 404
status.HTTP_422_UNPROCESSABLE_ENTITY  # 422
status.HTTP_500_INTERNAL_SERVER_ERROR # 500
```

## JSONResponse and Custom Responses

### JSONResponse
```python
from fastapi import FastAPI
from fastapi.responses import JSONResponse

app = FastAPI()

@app.post("/items/")
def create_item():
    return JSONResponse(
        status_code=201,
        content={"id": 1, "message": "Item created successfully"}
    )
```

### HTMLResponse
```python
from fastapi.responses import HTMLResponse

@app.get("/html-page/", response_class=HTMLResponse)
def get_html():
    return "<html><body><h1>Hello World</h1></body></html>"
```

### FileResponse
```python
from fastapi.responses import FileResponse

@app.get("/download/")
def download_file():
    return FileResponse("path/to/file.pdf", media_type="application/pdf")
```

### StreamingResponse
```python
from fastapi.responses import StreamingResponse
import io

@app.get("/stream/")
def stream():
    def generate():
        for i in range(10):
            yield f"Line {i}\n"
    
    return StreamingResponse(generate(), media_type="text/plain")
```

### RedirectResponse
```python
from fastapi.responses import RedirectResponse

@app.get("/redirect/")
def redirect():
    return RedirectResponse(url="https://example.com")
```

## Handling Different Response Types

### Union Response Types
```python
from fastapi import FastAPI
from pydantic import BaseModel
from typing import Union

app = FastAPI()

class Success(BaseModel):
    message: str
    data: dict

class Error(BaseModel):
    error: str

@app.get("/items/{item_id}")
def get_item(item_id: int) -> Union[Success, Error]:
    if item_id > 0:
        return Success(message="Success", data={"id": item_id})
    else:
        return Error(error="Invalid ID")
```

### Multiple Response Models
```python
from fastapi import FastAPI

app = FastAPI()

@app.get(
    "/items/{item_id}",
    responses={
        200: {"description": "Item found"},
        404: {"description": "Item not found"}
    }
)
def get_item(item_id: int):
    if item_id == 1:
        return {"name": "Item"}
    else:
        return {"detail": "Item not found"}
```

## Creating Standardized API Response Formats

### Unified Response Wrapper
```python
from pydantic import BaseModel
from typing import Optional, Any, Generic, TypeVar

T = TypeVar('T')

class APIResponse(BaseModel, Generic[T]):
    """
    Standard API Response format
    """
    success: bool
    data: Optional[T] = None
    error: Optional[str] = None
    message: Optional[str] = None

# Usage
from fastapi import FastAPI

app = FastAPI()

class Item(BaseModel):
    id: int
    name: str

@app.get("/items/{item_id}", response_model=APIResponse[Item])
def get_item(item_id: int):
    return APIResponse(
        success=True,
        data=Item(id=item_id, name="Laptop"),
        message="Item retrieved successfully"
    )

@app.get("/items/error/", response_model=APIResponse)
def get_item_error():
    return APIResponse(
        success=False,
        error="Item not found",
        message="The requested item does not exist"
    )
```

### Enhanced Response with Metadata
```python
from pydantic import BaseModel
from typing import Optional, Any, List
from datetime import datetime

class PaginationMeta(BaseModel):
    page: int
    page_size: int
    total: int
    total_pages: int

class APIResponse(BaseModel):
    success: bool
    code: int
    message: str
    data: Optional[Any] = None
    error: Optional[str] = None
    timestamp: datetime = datetime.now()
    path: Optional[str] = None
    pagination: Optional[PaginationMeta] = None

# Usage in endpoint
@app.get("/items/", response_model=APIResponse)
def get_items(page: int = 1, page_size: int = 10):
    total = 100
    return APIResponse(
        success=True,
        code=200,
        message="Items retrieved successfully",
        data=[{"id": 1, "name": "Item 1"}],
        pagination=PaginationMeta(
            page=page,
            page_size=page_size,
            total=total,
            total_pages=(total + page_size - 1) // page_size
        )
    )
```

---

# **TOPIC 6: Dependency Injection**

## Understanding Dependency Injection

**Dependency Injection (DI)** is a design pattern where dependencies are provided to a function/class rather than created inside it.

### Benefits of DI:
- **Reusability**: Same dependency used across multiple endpoints
- **Testability**: Easy to mock dependencies in tests
- **Separation of Concerns**: Dependencies are separate from business logic
- **Maintainability**: Changes to dependency don't affect endpoints
- **Flexibility**: Easy to swap implementations

### Example Without DI
```python
# Hard to test, dependencies mixed with logic
def get_user(user_id: int):
    db = create_database_connection()  # Created inside
    user = db.query(User).get(user_id)
    return user
```

### Example With DI
```python
# Easy to test, dependencies are provided
def get_user(user_id: int, db=Depends(get_db)):
    user = db.query(User).get(user_id)
    return user
```

## Using Depends()

### Basic Dependency
```python
from fastapi import FastAPI, Depends

def get_query(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}

app = FastAPI()

@app.get("/items/")
def read_items(commons: dict = Depends(get_query)):
    return commons
    
# Call: /items/?skip=5&limit=20
# Result: {"skip": 5, "limit": 20}
```

### Dependency with Database
```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session

# Simulated database
DATABASE_URL = "sqlite:///./test.db"
db = None

def get_db():
    global db
    if db is None:
        db = connect_to_database()
    try:
        yield db
    finally:
        db.close()

app = FastAPI()

@app.get("/users/{user_id}")
def get_user(user_id: int, db: Session = Depends(get_db)):
    # db session provided automatically
    user = db.query(User).get(user_id)
    return user
```

### Nested Dependencies
```python
from fastapi import FastAPI, Depends

def get_db():
    return "database_connection"

def get_user_id(token: str, db=Depends(get_db)):
    # Depends on get_db, uses db
    user_id = verify_token(token, db)
    return user_id

app = FastAPI()

@app.get("/user-data/")
def get_user_data(user_id: int = Depends(get_user_id)):
    # Depends on get_user_id, which depends on get_db
    # get_db is only called once, not twice (cached)
    return {"user_id": user_id}
```

## Creating Reusable Dependencies

### Pagination Dependency
```python
from fastapi import FastAPI, Depends

class Pagination:
    def __init__(self, skip: int = 0, limit: int = 10):
        self.skip = skip
        self.limit = limit

def get_pagination(skip: int = 0, limit: int = 10):
    return Pagination(skip=skip, limit=limit)

app = FastAPI()

@app.get("/items/")
def get_items(pagination: Pagination = Depends(get_pagination)):
    return {
        "skip": pagination.skip,
        "limit": pagination.limit,
        "items": [1, 2, 3]
    }
```

### Search/Filter Dependency
```python
from fastapi import FastAPI, Depends
from typing import Optional

class CommonQueryParams:
    def __init__(
        self,
        skip: int = 0,
        limit: int = 10,
        search: Optional[str] = None
    ):
        self.skip = skip
        self.limit = limit
        self.search = search

def common_params(
    skip: int = 0,
    limit: int = 10,
    search: Optional[str] = None
):
    return CommonQueryParams(skip=skip, limit=limit, search=search)

app = FastAPI()

@app.get("/items/")
def read_items(commons: CommonQueryParams = Depends(common_params)):
    return {
        "skip": commons.skip,
        "limit": commons.limit,
        "search": commons.search
    }
```

## Database Session Dependency

### SQLAlchemy Session Management
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session
from fastapi import FastAPI, Depends

DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

app = FastAPI()

@app.get("/users/{user_id}")
def get_user(user_id: int, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.id == user_id).first()
    return user

@app.post("/users/")
def create_user(user_data: dict, db: Session = Depends(get_db)):
    user = User(**user_data)
    db.add(user)
    db.commit()
    db.refresh(user)
    return user
```

## Security Dependencies

### Token Validation Dependency
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="login")

def verify_token(token: str = Depends(oauth2_scheme)):
    if not token.startswith("Bearer "):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )
    return token[7:]  # Remove "Bearer " prefix

app = FastAPI()

@app.get("/protected/")
def protected_route(token: str = Depends(verify_token)):
    return {"token": token}
```

### User Authentication Dependency
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

class User(BaseModel):
    id: int
    name: str
    email: str

def get_current_user(token: str = Depends(oauth2_scheme)) -> User:
    # Validate token and return user
    try:
        user_id = decode_token(token)
        user = get_user_from_db(user_id)
        if not user:
            raise HTTPException(status_code=404, detail="User not found")
        return user
    except:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Could not validate credentials"
        )

@app.get("/me/")
def get_me(current_user: User = Depends(get_current_user)):
    return current_user
```

### Role-Based Access Control Dependency
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def get_current_user(token: str = Depends(oauth2_scheme)):
    user = decode_token(token)
    return user

def require_admin(current_user = Depends(get_current_user)):
    if current_user.role != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not enough permissions"
        )
    return current_user

@app.delete("/users/{user_id}")
def delete_user(user_id: int, admin = Depends(require_admin)):
    # Only admins can delete users
    return {"message": f"User {user_id} deleted"}
```

---

# **TOPIC 7: Middleware**

## Understanding Middleware

**Middleware** is code that runs before your endpoint and/or after your endpoint sends a response. It processes every request and response.

### Request/Response Flow with Middleware
```
Client Request
    ↓
Middleware 1 (before request)
    ↓
Middleware 2 (before request)
    ↓
Endpoint Handler
    ↓
Middleware 2 (after response)
    ↓
Middleware 1 (after response)
    ↓
Client Response
```

## Adding Custom Middleware

### Basic Custom Middleware
```python
from fastapi import FastAPI
from starlette.middleware.base import BaseHTTPMiddleware
from starlette.requests import Request
import time

app = FastAPI()

class TimingMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        start_time = time.time()
        
        # Process request
        response = await call_next(request)
        
        # Process response
        process_time = time.time() - start_time
        response.headers["X-Process-Time"] = str(process_time)
        
        return response

app.add_middleware(TimingMiddleware)

@app.get("/")
def read_root():
    return {"message": "Hello"}
```

### Middleware with Custom Logic
```python
from fastapi import FastAPI
from starlette.middleware.base import BaseHTTPMiddleware
from starlette.requests import Request
import logging

logger = logging.getLogger(__name__)
app = FastAPI()

class LoggingMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        logger.info(f"Request: {request.method} {request.url.path}")
        
        response = await call_next(request)
        
        logger.info(f"Response: {response.status_code}")
        
        return response

app.add_middleware(LoggingMiddleware)
```

## Logging Requests and Responses

### Complete Request/Response Logging
```python
from fastapi import FastAPI
from starlette.middleware.base import BaseHTTPMiddleware
from starlette.requests import Request
import logging
import json
import time

logger = logging.getLogger(__name__)
app = FastAPI()

class RequestResponseLoggingMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        # Log request
        request_body = await request.body()
        logger.info(
            f"Request: {request.method} {request.url.path}\n"
            f"Headers: {dict(request.headers)}\n"
            f"Body: {request_body.decode()}"
        )
        
        start_time = time.time()
        response = await call_next(request)
        
        # Log response
        process_time = time.time() - start_time
        logger.info(
            f"Response: {response.status_code}\n"
            f"Process Time: {process_time:.3f}s"
        )
        
        return response

app.add_middleware(RequestResponseLoggingMiddleware)
```

### Middleware with Request ID Tracking
```python
from fastapi import FastAPI
from starlette.middleware.base import BaseHTTPMiddleware
from starlette.requests import Request
import uuid
import logging

logger = logging.getLogger(__name__)
app = FastAPI()

class RequestIDMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        request_id = str(uuid.uuid4())
        request.state.request_id = request_id
        
        logger.info(f"[{request_id}] {request.method} {request.url.path}")
        
        response = await call_next(request)
        response.headers["X-Request-ID"] = request_id
        
        return response

app.add_middleware(RequestIDMiddleware)

@app.get("/items/")
def get_items(request: Request):
    # Access request ID in endpoint
    request_id = request.state.request_id
    return {"request_id": request_id}
```

## CORS Configuration

### Basic CORS Setup
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://example.com"],  # Specific origin
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/items/")
def get_items():
    return [{"id": 1}]
```

### CORS with Multiple Origins
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

allowed_origins = [
    "http://localhost:3000",
    "http://localhost:8080",
    "https://example.com",
    "https://www.example.com",
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=allowed_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["*"],
)
```

### CORS with Wildcard (Not Recommended for Production)
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Allow all origins
    allow_credentials=False,  # Cannot use with allow_origins=["*"]
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Production CORS Configuration
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import os

app = FastAPI()

allowed_origins = os.getenv("CORS_ORIGINS", "").split(",")

app.add_middleware(
    CORSMiddleware,
    allow_origins=allowed_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "DELETE", "OPTIONS"],
    allow_headers=[
        "Content-Type",
        "Authorization",
        "X-Requested-With",
    ],
    max_age=3600,  # Cache preflight for 1 hour
)
```

---

# **TOPIC 8: Authentication & Authorization**

## Understanding Authentication vs Authorization

- **Authentication**: Verifying who you are (login)
- **Authorization**: Verifying what you can do (permissions)

## Basic Authentication

### HTTP Basic Auth (Simple but Insecure)
```python
from fastapi import FastAPI, HTTPException, status
from fastapi.security import HTTPBasic, HTTPBasicCredentials
import secrets

app = FastAPI()
security = HTTPBasic()

# Simple user store
users = {
    "john": "secret123",
    "jane": "password456"
}

@app.get("/secure/")
def read_secure(credentials: HTTPBasicCredentials = Depends(security)):
    username = credentials.username
    password = credentials.password
    
    if username not in users or users[username] != password:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid credentials"
        )
    
    return {"username": username, "message": "Authenticated"}
```

### Better Basic Auth with Password Hashing
```python
from fastapi import FastAPI, HTTPException, Depends, status
from fastapi.security import HTTPBasic, HTTPBasicCredentials
from passlib.context import CryptContext

app = FastAPI()
security = HTTPBasic()

# Password hashing context
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

# User database
users_db = {
    "john": pwd_context.hash("secret123"),
    "jane": pwd_context.hash("password456")
}

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

@app.post("/login/")
def login(credentials: HTTPBasicCredentials = Depends(security)):
    if credentials.username not in users_db:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid username or password"
        )
    
    if not verify_password(credentials.password, users_db[credentials.username]):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid username or password"
        )
    
    return {"username": credentials.username, "message": "Logged in successfully"}
```

## OAuth2 with Password Flow

### Complete OAuth2 Implementation
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel
from passlib.context import CryptContext
from datetime import datetime, timedelta
from typing import Optional
import jwt

app = FastAPI()

# Configuration
SECRET_KEY = "your-secret-key-change-in-production"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

# Password context
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

# OAuth2 scheme
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Pydantic models
class Token(BaseModel):
    access_token: str
    token_type: str

class TokenData(BaseModel):
    username: Optional[str] = None

class User(BaseModel):
    username: str
    email: Optional[str] = None
    full_name: Optional[str] = None
    disabled: Optional[bool] = None

class UserInDB(User):
    hashed_password: str

# Fake user database
fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "john@example.com",
        "hashed_password": pwd_context.hash("secret123"),
        "disabled": False,
    }
}

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)

def get_user(username: str) -> Optional[UserInDB]:
    if username in fake_users_db:
        user_dict = fake_users_db[username]
        return UserInDB(**user_dict)

def authenticate_user(username: str, password: str) -> Optional[UserInDB]:
    user = get_user(username)
    if not user:
        return False
    if not verify_password(password, user.hashed_password):
        return False
    return user

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)
    
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt

async def get_current_user(token: str = Depends(oauth2_scheme)):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
        token_data = TokenData(username=username)
    except jwt.InvalidTokenError:
        raise credentials_exception
    
    user = get_user(username=token_data.username)
    if user is None:
        raise credentials_exception
    
    return user

async def get_current_active_user(current_user: User = Depends(get_current_user)):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user

@app.post("/token", response_model=Token)
async def login_for_access_token(form_data: OAuth2PasswordRequestForm = Depends()):
    user = authenticate_user(form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
        )
    
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user.username}, expires_delta=access_token_expires
    )
    
    return {"access_token": access_token, "token_type": "bearer"}

@app.get("/users/me", response_model=User)
async def read_users_me(current_user: User = Depends(get_current_active_user)):
    return current_user

@app.get("/users/me/items/")
async def read_own_items(current_user: User = Depends(get_current_active_user)):
    return [{"item_id": "Foo", "owner": current_user.username}]
```

## JWT Token-Based Authentication

### Simple JWT Implementation
```python
from fastapi import FastAPI, HTTPException, Depends, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel
import jwt
from datetime import datetime, timedelta

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"

class User(BaseModel):
    username: str
    email: str

def create_jwt_token(data: dict, expires_in_minutes: int = 30):
    payload = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=expires_in_minutes)
    payload.update({"exp": expire})
    token = jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)
    return token

def verify_jwt_token(token: str) -> dict:
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return payload
    except jwt.InvalidTokenError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )

@app.post("/login/")
def login(username: str, password: str):
    # Verify credentials (simplified)
    if username == "john" and password == "secret":
        token = create_jwt_token({"sub": username})
        return {"access_token": token, "token_type": "bearer"}
    
    raise HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Invalid credentials"
    )

@app.get("/protected/")
def protected_route(token: str = Depends(oauth2_scheme)):
    payload = verify_jwt_token(token)
    username = payload.get("sub")
    return {"username": username}
```

## Role-Based Access Control

### RBAC Implementation
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel
from typing import Optional
import jwt

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

SECRET_KEY = "secret"
ALGORITHM = "HS256"

class User(BaseModel):
    username: str
    role: str  # "admin", "moderator", "user"

# Mock user database
users_db = {
    "admin_user": {"username": "admin_user", "role": "admin"},
    "moderator": {"username": "moderator", "role": "moderator"},
    "user": {"username": "user", "role": "user"},
}

def get_current_user(token: str = Depends(oauth2_scheme)) -> User:
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username = payload.get("sub")
        if username not in users_db:
            raise HTTPException(status_code=401, detail="Invalid token")
        user_data = users_db[username]
        return User(**user_data)
    except:
        raise HTTPException(status_code=401, detail="Invalid token")

def require_admin(current_user: User = Depends(get_current_user)):
    if current_user.role != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Admin access required"
        )
# **TOPIC 8: Authentication & Authorization (CONTINUED)**

## Role-Based Access Control (CONTINUED)

### Multiple Role Requirements
```python
from fastapi import FastAPI, Depends, HTTPException, status
from typing import List

app = FastAPI()

def require_roles(allowed_roles: List[str]):
    def role_checker(current_user: User = Depends(get_current_user)):
        if current_user.role not in allowed_roles:
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail=f"Required roles: {', '.join(allowed_roles)}"
            )
        return current_user
    return role_checker

@app.delete("/users/{user_id}")
def delete_user(
    user_id: int,
    current_user: User = Depends(require_roles(["admin"]))
):
    return {"message": f"User {user_id} deleted by {current_user.username}"}

@app.put("/posts/{post_id}")
def update_post(
    post_id: int,
    current_user: User = Depends(require_roles(["admin", "moderator"]))
):
    return {"message": f"Post {post_id} updated"}
```

## Securing Protected Endpoints

### Complete Secured Endpoint Example
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

class Post(BaseModel):
    title: str
    content: str
    owner_id: int

def get_current_user(token: str = Depends(oauth2_scheme)):
    # Validate token and return user
    return {"id": 1, "username": "john", "role": "user"}

def get_post_owner(post_id: int):
    # Get post from database
    return {"id": post_id, "owner_id": 1, "title": "Test Post"}

@app.get("/posts/{post_id}")
def get_post(post_id: int, current_user = Depends(get_current_user)):
    # Public endpoint, authentication required
    post = get_post_owner(post_id)
    return post

@app.put("/posts/{post_id}")
def update_post(
    post_id: int,
    post_data: Post,
    current_user = Depends(get_current_user)
):
    # Only owner or admin can update
    post = get_post_owner(post_id)
    
    if current_user["id"] != post["owner_id"] and current_user["role"] != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not allowed to update this post"
        )
    
    return {"message": "Post updated"}

@app.delete("/posts/{post_id}")
def delete_post(
    post_id: int,
    current_user = Depends(get_current_user)
):
    post = get_post_owner(post_id)
    
    if current_user["id"] != post["owner_id"] and current_user["role"] != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not allowed to delete this post"
        )
    
    return {"message": "Post deleted"}
```

### Permission Dependency
```python
from fastapi import HTTPException, status

def check_post_permission(post_id: int, current_user: User):
    """Check if user can modify this post"""
    post = get_post_from_db(post_id)
    
    if current_user.id != post.owner_id and current_user.role != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="You don't have permission to modify this post"
        )
    
    return post

@app.put("/posts/{post_id}")
def update_post(
    post_id: int,
    post_data: PostUpdate,
    current_user: User = Depends(get_current_user)
):
    post = check_post_permission(post_id, current_user)
    # Update post...
    return {"message": "Post updated"}
```

---

# **TOPIC 9: Database Integration**

## Understanding Databases

There are different types of databases:
- **SQL Databases**: Structured, relational (PostgreSQL, MySQL, SQLite)
- **NoSQL Databases**: Flexible, document-based (MongoDB, Redis)
- **Key-Value Stores**: Fast, simple (Redis, Memcached)

## Setting Up SQLite (Easy for Development)

### Installation
```bash
# SQLite3 is built into Python, just install SQLAlchemy
pip install sqlalchemy
```

### Basic SQLite Setup
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

# SQLite database (file-based)
DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(
    DATABASE_URL,
    connect_args={"check_same_thread": False}  # Only for SQLite
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

## Setting Up PostgreSQL (Production)

### Installation
```bash
pip install psycopg2-binary  # PostgreSQL adapter
pip install sqlalchemy
```

### PostgreSQL Connection
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

# PostgreSQL
DATABASE_URL = "postgresql://username:password@localhost:5432/databasename"

engine = create_engine(
    DATABASE_URL,
    echo=True,  # Print SQL statements (disable in production)
    pool_size=10,
    max_overflow=20
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

### Environment-based Configuration
```python
import os
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

# From environment variables
DATABASE_URL = os.getenv(
    "DATABASE_URL",
    "postgresql://user:password@localhost/dbname"
)

engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(bind=engine)
Base = declarative_base()
```

## Setting Up MySQL (Production)

### Installation
```bash
pip install mysql-connector-python  # or PyMySQL
pip install sqlalchemy
```

### MySQL Connection
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

# MySQL with mysql-connector
DATABASE_URL = "mysql+mysqlconnector://user:password@localhost:3306/dbname"

# Or with PyMySQL
DATABASE_URL = "mysql+pymysql://user:password@localhost:3306/dbname"

engine = create_engine(
    DATABASE_URL,
    pool_size=10,
    max_overflow=20,
    pool_recycle=3600  # Recycle connections every hour
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

## Using SQLAlchemy ORM

### Defining Models

#### User Model
```python
from sqlalchemy import Column, Integer, String, Boolean, DateTime, ForeignKey
from sqlalchemy.orm import relationship
from datetime import datetime
from database import Base

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String(50), unique=True, index=True)
    email = Column(String(100), unique=True, index=True)
    hashed_password = Column(String(255))
    full_name = Column(String(100), nullable=True)
    is_active = Column(Boolean, default=True)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
    
    # Relationships
    posts = relationship("Post", back_populates="author")
```

#### Post Model with Relationships
```python
from sqlalchemy import Column, Integer, String, Text, DateTime, ForeignKey
from sqlalchemy.orm import relationship
from datetime import datetime
from database import Base

class Post(Base):
    __tablename__ = "posts"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(200))
    content = Column(Text)
    author_id = Column(Integer, ForeignKey("users.id"))
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
    
    # Relationships
    author = relationship("User", back_populates="posts")
    comments = relationship("Comment", back_populates="post")

class Comment(Base):
    __tablename__ = "comments"

    id = Column(Integer, primary_key=True, index=True)
    content = Column(Text)
    post_id = Column(Integer, ForeignKey("posts.id"))
    user_id = Column(Integer, ForeignKey("users.id"))
    created_at = Column(DateTime, default=datetime.utcnow)
    
    # Relationships
    post = relationship("Post", back_populates="comments")
    user = relationship("User")
```

### Creating Tables
```python
from database import engine, Base
from models import User, Post, Comment

# Create all tables
Base.metadata.create_all(bind=engine)
```

## CRUD Operations

### Create Operations

```python
from sqlalchemy.orm import Session
from models import User, Post

def create_user(db: Session, username: str, email: str, password: str):
    """Create a new user"""
    db_user = User(
        username=username,
        email=email,
        hashed_password=password  # Should be hashed in real app
    )
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user

def create_post(db: Session, title: str, content: str, author_id: int):
    """Create a new post"""
    db_post = Post(
        title=title,
        content=content,
        author_id=author_id
    )
    db.add(db_post)
    db.commit()
    db.refresh(db_post)
    return db_post
```

### Read Operations

```python
from sqlalchemy.orm import Session

def get_user(db: Session, user_id: int):
    """Get user by ID"""
    return db.query(User).filter(User.id == user_id).first()

def get_user_by_email(db: Session, email: str):
    """Get user by email"""
    return db.query(User).filter(User.email == email).first()

def get_users(db: Session, skip: int = 0, limit: int = 10):
    """Get paginated users"""
    return db.query(User).offset(skip).limit(limit).all()

def get_user_posts(db: Session, user_id: int):
    """Get all posts by a user"""
    return db.query(Post).filter(Post.author_id == user_id).all()

def get_posts(db: Session, skip: int = 0, limit: int = 10):
    """Get paginated posts"""
    return db.query(Post).offset(skip).limit(limit).all()

def get_post(db: Session, post_id: int):
    """Get post by ID"""
    return db.query(Post).filter(Post.id == post_id).first()
```

### Update Operations

```python
from sqlalchemy.orm import Session

def update_user(db: Session, user_id: int, **kwargs):
    """Update user fields"""
    user = db.query(User).filter(User.id == user_id).first()
    if user:
        for key, value in kwargs.items():
            setattr(user, key, value)
        db.commit()
        db.refresh(user)
    return user

def update_post(db: Session, post_id: int, title: str = None, content: str = None):
    """Update post"""
    post = db.query(Post).filter(Post.id == post_id).first()
    if post:
        if title:
            post.title = title
        if content:
            post.content = content
        db.commit()
        db.refresh(post)
    return post
```

### Delete Operations

```python
from sqlalchemy.orm import Session

def delete_user(db: Session, user_id: int):
    """Delete a user"""
    user = db.query(User).filter(User.id == user_id).first()
    if user:
        db.delete(user)
        db.commit()
    return user

def delete_post(db: Session, post_id: int):
    """Delete a post"""
    post = db.query(Post).filter(Post.id == post_id).first()
    if post:
        db.delete(post)
        db.commit()
    return post
```

## Complete CRUD Module Example

```python
# crud.py
from sqlalchemy.orm import Session
from models import User, Post, Comment
from schemas import UserCreate, PostCreate, CommentCreate

class UserCRUD:
    @staticmethod
    def create(db: Session, user: UserCreate):
        db_user = User(**user.dict())
        db.add(db_user)
        db.commit()
        db.refresh(db_user)
        return db_user
    
    @staticmethod
    def get_by_id(db: Session, user_id: int):
        return db.query(User).filter(User.id == user_id).first()
    
    @staticmethod
    def get_by_email(db: Session, email: str):
        return db.query(User).filter(User.email == email).first()
    
    @staticmethod
    def get_all(db: Session, skip: int = 0, limit: int = 10):
        return db.query(User).offset(skip).limit(limit).all()
    
    @staticmethod
    def update(db: Session, user_id: int, user: UserCreate):
        db_user = db.query(User).filter(User.id == user_id).first()
        if db_user:
            for key, value in user.dict().items():
                setattr(db_user, key, value)
            db.commit()
            db.refresh(db_user)
        return db_user
    
    @staticmethod
    def delete(db: Session, user_id: int):
        db_user = db.query(User).filter(User.id == user_id).first()
        if db_user:
            db.delete(db_user)
            db.commit()
        return db_user

class PostCRUD:
    @staticmethod
    def create(db: Session, post: PostCreate):
        db_post = Post(**post.dict())
        db.add(db_post)
        db.commit()
        db.refresh(db_post)
        return db_post
    
    @staticmethod
    def get_by_id(db: Session, post_id: int):
        return db.query(Post).filter(Post.id == post_id).first()
    
    @staticmethod
    def get_all(db: Session, skip: int = 0, limit: int = 10):
        return db.query(Post).offset(skip).limit(limit).all()
    
    @staticmethod
    def get_user_posts(db: Session, user_id: int):
        return db.query(Post).filter(Post.author_id == user_id).all()
    
    @staticmethod
    def update(db: Session, post_id: int, post: PostCreate):
        db_post = db.query(Post).filter(Post.id == post_id).first()
        if db_post:
            for key, value in post.dict().items():
                setattr(db_post, key, value)
            db.commit()
            db.refresh(db_post)
        return db_post
    
    @staticmethod
    def delete(db: Session, post_id: int):
        db_post = db.query(Post).filter(Post.id == post_id).first()
        if db_post:
            db.delete(db_post)
            db.commit()
        return db_post
```

## Managing Database Sessions

### Session Dependency
```python
from sqlalchemy.orm import Session
from fastapi import FastAPI, Depends
from database import SessionLocal

app = FastAPI()

def get_db():
    """Dependency to get database session"""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users/{user_id}")
def get_user(user_id: int, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.id == user_id).first()
    return user
```

### Session with Context Manager
```python
from sqlalchemy.orm import Session

class Database:
    def __init__(self, database_url: str):
        self.engine = create_engine(database_url)
        self.SessionLocal = sessionmaker(bind=self.engine)
    
    def get_session(self):
        db = self.SessionLocal()
        try:
            yield db
        finally:
            db.close()
```

## Alembic Migrations Overview

### Installation
```bash
pip install alembic
```

### Initialize Alembic
```bash
alembic init migrations
```

### Configure Alembic (alembic/env.py)
```python
from sqlalchemy import engine_from_config
from alembic import context
import os
from models import Base

config = context.config
sqlalchemy_url = os.getenv("DATABASE_URL", "sqlite:///./test.db")
config.set_main_option("sqlalchemy.url", sqlalchemy_url)
target_metadata = Base.metadata

def run_migrations_online() -> None:
    """Run migrations in 'online' mode"""
    connectable = engine_from_config(
        config.get_section(config.config_ini_section),
        prefix="sqlalchemy.",
        poolclass=pool.NullPool,
    )

    with connectable.connect() as connection:
        context.configure(
            connection=connection, target_metadata=target_metadata
        )

        with context.begin_transaction():
            context.run_migrations()

run_migrations_online()
```

### Create Migration
```bash
# Auto-generate migration based on model changes
alembic revision --autogenerate -m "Add user table"

# View migration file and modify if needed
# File: alembic/versions/xxx_add_user_table.py
```

### Run Migrations
```bash
# Apply all pending migrations
alembic upgrade head

# Rollback to specific migration
alembic downgrade <revision>

# View migration history
alembic current
alembic history
```

## MongoDB Integration (NoSQL)

### Installation
```bash
pip install pymongo
pip install motor  # Async MongoDB driver
```

### Basic MongoDB Connection
```python
from pymongo import MongoClient
from typing import Optional

MONGODB_URL = "mongodb://localhost:27017"
DATABASE_NAME = "my_app"

client = MongoClient(MONGODB_URL)
db = client[DATABASE_NAME]
users_collection = db["users"]
posts_collection = db["posts"]
```

### Async MongoDB with Motor
```python
from motor.motor_asyncio import AsyncClient
import os

MONGODB_URL = os.getenv("MONGODB_URL", "mongodb://localhost:27017")

async def get_database():
    client = AsyncClient(MONGODB_URL)
    database = client["my_app"]
    yield database
    client.close()
```

### MongoDB CRUD Operations
```python
from pymongo import MongoClient
from bson import ObjectId

client = MongoClient("mongodb://localhost:27017")
db = client["my_app"]

# Create
def create_user(username: str, email: str):
    user = {"username": username, "email": email}
    result = db.users.insert_one(user)
    return str(result.inserted_id)

# Read
def get_user(user_id: str):
    return db.users.find_one({"_id": ObjectId(user_id)})

def get_all_users(skip: int = 0, limit: int = 10):
    return list(db.users.find().skip(skip).limit(limit))

# Update
def update_user(user_id: str, **kwargs):
    db.users.update_one(
        {"_id": ObjectId(user_id)},
        {"$set": kwargs}
    )
    return get_user(user_id)

# Delete
def delete_user(user_id: str):
    result = db.users.delete_one({"_id": ObjectId(user_id)})
    return result.deleted_count > 0
```

### MongoDB with FastAPI
```python
from fastapi import FastAPI, Depends
from motor.motor_asyncio import AsyncClient
from bson import ObjectId
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    username: str
    email: str

client = AsyncClient("mongodb://localhost:27017")
db = client["my_app"]

@app.post("/users/")
async def create_user(user: User):
    result = await db.users.insert_one(user.dict())
    return {"id": str(result.inserted_id)}

@app.get("/users/{user_id}")
async def get_user(user_id: str):
    user = await db.users.find_one({"_id": ObjectId(user_id)})
    if user:
        user["_id"] = str(user["_id"])
        return user
    return {"error": "User not found"}

@app.get("/users/")
async def get_all_users(skip: int = 0, limit: int = 10):
    users = []
    async for user in db.users.find().skip(skip).limit(limit):
        user["_id"] = str(user["_id"])
        users.append(user)
    return users

@app.put("/users/{user_id}")
async def update_user(user_id: str, user: User):
    result = await db.users.update_one(
        {"_id": ObjectId(user_id)},
        {"$set": user.dict()}
    )
    if result.modified_count > 0:
        return await get_user(user_id)
    return {"error": "User not found"}

@app.delete("/users/{user_id}")
async def delete_user(user_id: str):
    result = await db.users.delete_one({"_id": ObjectId(user_id)})
    return {"deleted": result.deleted_count > 0}
```

## Comparison: SQL vs NoSQL vs ORM

### SQL (Relational) Approach
```python
# Structured, relationships enforced
# Good for: Complex data relationships, strict schemas
# Examples: PostgreSQL, MySQL, SQLite

# Setup
DATABASE_URL = "postgresql://user:password@localhost/db"
engine = create_engine(DATABASE_URL)

# Strongly typed, migrations
class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    username = Column(String(50), unique=True)
    posts = relationship("Post")

# Structured queries
users = db.query(User).filter(User.id == 1).all()
```

### NoSQL (Document) Approach
```python
# Flexible schema, denormalized
# Good for: Rapid prototyping, flexible data
# Examples: MongoDB, CouchDB

# Setup
client = MongoClient("mongodb://localhost:27017")
db = client["my_app"]

# No schema enforced, flexible
user = {
    "username": "john",
    "email": "john@example.com",
    "metadata": {...}
}
db.users.insert_one(user)

# Simple queries
user = db.users.find_one({"username": "john"})
```

### ORM (Object-Relational Mapping)
```python
# Abstraction layer over SQL
# Good for: Rapid development, database agnostic
# Examples: SQLAlchemy, Tortoise ORM

# Models map to tables
class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    username = Column(String)

# Python-like queries
user = db.query(User).filter_by(username="john").first()

# Works with multiple databases
# Just change connection string
```

## Advanced Queries

### Filtering with Multiple Conditions
```python
from sqlalchemy import and_, or_

# AND condition
users = db.query(User).filter(
    and_(
        User.is_active == True,
        User.created_at > datetime(2024, 1, 1)
    )
).all()

# OR condition
users = db.query(User).filter(
    or_(
        User.username == "john",
        User.email == "john@example.com"
    )
).all()

# Complex conditions
users = db.query(User).filter(
    and_(
        User.is_active == True,
        or_(
            User.role == "admin",
            User.role == "moderator"
        )
    )
).all()
```

### Ordering and Pagination
```python
# Order by
users = db.query(User).order_by(User.created_at.desc()).all()

# Pagination
page = 1
page_size = 10
skip = (page - 1) * page_size

users = db.query(User).offset(skip).limit(page_size).all()
```

### Aggregations
```python
from sqlalchemy import func

# Count
user_count = db.query(func.count(User.id)).scalar()

# Sum
total_likes = db.query(func.sum(Post.likes)).scalar()

# Group By
posts_per_user = db.query(
    User.username,
    func.count(Post.id).label("post_count")
).join(Post).group_by(User.id).all()
```

### Relationships and Joins
```python
# Eager loading
user = db.query(User).options(
    joinedload(User.posts)
).filter(User.id == 1).first()

# Access related data
for post in user.posts:
    print(post.title)

# Inner join
posts = db.query(Post).join(User).filter(
    User.username == "john"
).all()
```

---

# **TOPIC 10: Background Tasks**

## Understanding Background Tasks

Background tasks run after sending a response to the client. Useful for:
- Sending emails
- Logging operations
- File processing
- Notifications
- Heavy computations

## Running Tasks with BackgroundTasks

### Basic Background Task
```python
from fastapi import FastAPI, BackgroundTasks

app = FastAPI()

def write_notification(email: str, message: str = ""):
    with open("notifications.log", "a") as log:
        content = f"Notification for {email}: {message}\n"
        log.write(content)

@app.post("/send-notification/")
async def send_notification(email: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(write_notification, email, message="some notification")
    return {"message": "Notification sent in background"}
```

### Multiple Background Tasks
```python
@app.post("/send-email-and-log/")
async def send_email_and_log(
    email: str,
    background_tasks: BackgroundTasks
):
    # Add multiple tasks
    background_tasks.add_task(send_email, email)
    background_tasks.add_task(log_operation, "email_sent", email)
    background_tasks.add_task(update_statistics, "emails_sent")
    
    return {"message": "Processing..."}
```

### Background Task with Parameters
```python
def send_email(recipient: str, subject: str, body: str):
    """Send email"""
    # Simulate sending email
    print(f"Sending email to {recipient}")
    print(f"Subject: {subject}")
    print(f"Body: {body}")

@app.post("/send-email/")
async def send_email_endpoint(
    recipient: str,
    subject: str,
    background_tasks: BackgroundTasks
):
    background_tasks.add_task(send_email, recipient, subject, "Email body")
    return {"message": "Email will be sent in background"}
```

## Email Sending in Background

### Setup Email
```bash
pip install python-dotenv
pip install aiosmtplib  # For async email
```

### Async Email Sending
```python
from fastapi import FastAPI, BackgroundTasks
from pydantic import BaseModel
import aiosmtplib
from email.mime.text import MIMEText
import os
from dotenv import load_dotenv

load_dotenv()

app = FastAPI()

class EmailSchema(BaseModel):
    recipient: str
    subject: str
    body: str

async def send_email(recipient: str, subject: str, body: str):
    """Send email asynchronously"""
    smtp_host = os.getenv("SMTP_HOST", "smtp.gmail.com")
    smtp_port = int(os.getenv("SMTP_PORT", 587))
    sender_email = os.getenv("SENDER_EMAIL")
    sender_password = os.getenv("SENDER_PASSWORD")
    
    try:
        async with aiosmtplib.SMTP(hostname=smtp_host, port=smtp_port) as smtp:
            await smtp.login(sender_email, sender_password)
            
            message = MIMEText(body)
            message["Subject"] = subject
            message["From"] = sender_email
            message["To"] = recipient
            
            await smtp.send_message(message)
            print(f"Email sent to {recipient}")
    except Exception as e:
        print(f"Failed to send email: {e}")

@app.post("/send-email/")
async def send_email_endpoint(
    email: EmailSchema,
    background_tasks: BackgroundTasks
):
    background_tasks.add_task(
        send_email,
        email.recipient,
        email.subject,
        email.body
    )
    return {"message": "Email will be sent in background"}
```

## Logging Operations in Background

### Background Logging
```python
from fastapi import FastAPI, BackgroundTasks
from datetime import datetime
import json

app = FastAPI()

def log_operation(
    operation: str,
    user_id: int,
    details: dict = None
):
    """Log operation to file"""
    log_entry = {
        "timestamp": datetime.now().isoformat(),
        "operation": operation,
        "user_id": user_id,
        "details": details or {}
    }
    
    with open("operations.log", "a") as log_file:
        log_file.write(json.dumps(log_entry) + "\n")

@app.post("/create-post/")
async def create_post(
    title: str,
    content: str,
    user_id: int,
    background_tasks: BackgroundTasks
):
    # Create post (foreground)
    post_id = 123  # Simulated
    
    # Log operation (background)
    background_tasks.add_task(
        log_operation,
        "post_created",
        user_id,
        {"post_id": post_id, "title": title}
    )
    
    return {"post_id": post_id, "message": "Post created"}
```

## When to Use Celery vs BackgroundTasks

### BackgroundTasks (Use When):
- Tasks are lightweight and quick (< 1 minute)
- Running on a single server
- No need for task persistence
- Simple email sending, logging
- No complex scheduling needed

```python
# Good use case: Quick notifications
@app.post("/notify/")
async def notify(background_tasks: BackgroundTasks):
    background_tasks.add_task(send_email, "user@example.com")
    return {"message": "Notifying..."}
```

### Celery (Use When):
- Tasks are heavy or long-running
- Distributed systems with multiple workers
- Need task scheduling and retries
- Complex workflows
- Need task monitoring and persistence

```bash
# Install Celery
pip install celery redis

# tasks.py
from celery import Celery

app = Celery('tasks', broker='redis://localhost:6379')

@app.task(bind=True, max_retries=3)
def heavy_task(self, param):
    try:
        # Heavy processing
        result = process_data(param)
        return result
    except Exception as exc:
        # Retry with exponential backoff
        self.retry(exc=exc, countdown=2 ** self.request.retries)
```

## Combining BackgroundTasks with Database

```python
from fastapi import FastAPI, BackgroundTasks, Depends
from sqlalchemy.orm import Session

app = FastAPI()

def log_user_activity(user_id: int, action: str, db: Session):
    """Log user activity to database"""
    activity = Activity(user_id=user_id, action=action)
    db.add(activity)
    db.commit()

@app.post("/user-action/")
async def user_action(
    user_id: int,
    action: str,
    background_tasks: BackgroundTasks,
    db: Session = Depends(get_db)
):
    # Process action
    result = process_action(action)
    
    # Log to database (background)
    background_tasks.add_task(log_user_activity, user_id, action, db)
    
    return {"result": result}
```

---

# **TOPIC 11: File Handling**

## Uploading Files

### Single File Upload
```python
from fastapi import FastAPI, File, UploadFile
import shutil

app = FastAPI()

@app.post("/upload/")
async def upload_file(file: UploadFile = File(...)):
    """Upload a single file"""
    # Save file to disk
    file_location = f"uploads/{file.filename}"
    with open(file_location, "wb") as buffer:
        shutil.copyfileobj(file.file, buffer)
    
    return {
        "filename": file.filename,
        "content_type": file.content_type,
        "size": file.size
    }
```

### Multiple Files Upload
```python
from typing import List

@app.post("/upload-multiple/")
async def upload_multiple_files(files: List[UploadFile] = File(...)):
    """Upload multiple files"""
    uploaded_files = []
    
    for file in files:
        file_location = f"uploads/{file.filename}"
        with open(file_location, "wb") as buffer:
            shutil.copyfileobj(file.file, buffer)
        
        uploaded_files.append({
            "filename": file.filename,
            "content_type": file.content_type
        })
    
    return {"uploaded_files": uploaded_files}
```

## Handling Images and Documents

### Image Upload with Validation
```python
from fastapi import FastAPI, File, UploadFile, HTTPException, status
from PIL import Image
import os

app = FastAPI()

ALLOWED_IMAGE_EXTENSIONS = {".jpg", ".jpeg", ".png", ".gif", ".webp"}
MAX_FILE_SIZE = 5 * 1024 * 1024  # 5MB

@app.post("/upload-image/")
async def upload_image(file: UploadFile = File(...)):
    """Upload and validate image"""
    # Check file extension
    file_extension = os.path.splitext(file.filename)[1].lower()
    if file_extension not in ALLOWED_IMAGE_EXTENSIONS:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Invalid image format"
        )
    
    # Check file size
    contents = await file.read()
    if len(contents) > MAX_FILE_SIZE:
        raise HTTPException(
            status_code=status.HTTP_413_REQUEST_ENTITY_TOO_LARGE,
            detail="File too large"
        )
    
    # Validate image
    try:
        image = Image.open(file.file)
        image.verify()
    except Exception:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Invalid image"
        )
    
    # Save file
    file_location = f"uploads/images/{file.filename}"
    os.makedirs(os.path.dirname(file_location), exist_ok=True)
    with open(file_location, "wb") as buffer:
        buffer.write(contents)
    
    return {
        "filename": file.filename,
        "message": "Image uploaded successfully"
    }
```

### Document Upload with Validation
```python
import mimetypes

ALLOWED_DOCUMENTS = {".pdf", ".doc", ".docx", ".txt", ".xlsx"}

@app.post("/upload-document/")
async def upload_document(file: UploadFile = File(...)):
    """Upload document"""
    file_extension = os.path.splitext(file.filename)[1].lower()
    
    if file_extension not in ALLOWED_DOCUMENTS:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=f"Document type {file_extension} not allowed"
        )
    
    # Check MIME type
    mime_type, _ = mimetypes.guess_type(file.filename)
    if not mime_type or "document" not in mime_type and "pdf" not in mime_type:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Invalid document"
        )
    
    # Save file
    file_location = f"uploads/documents/{file.filename}"
    os.makedirs(os.path.dirname(file_location), exist_ok=True)
    
    contents = await file.read()
    with open(file_location, "wb") as buffer:
        buffer.write(contents)
    
    return {
        "filename": file.filename,
        "message": "Document uploaded successfully"
    }
```

## Saving Files Securely

### Secure File Naming
```python
from uuid import uuid4
import os

def get_unique_filename(original_filename: str) -> str:
    """Generate secure filename"""
    file_extension = os.path.splitext(original_filename)[1]
    unique_name = f"{uuid4()}{file_extension}"
    return unique_name

@app.post("/secure-upload/")
async def secure_upload(file: UploadFile = File(...)):
    """Upload with secure filename"""
    # Generate unique filename
    unique_filename = get_unique_filename(file.filename)
    
    # Save file
    file_location = f"uploads/{unique_filename}"
    contents = await file.read()
    
    with open(file_location, "wb") as buffer:
        buffer.write(contents)
    
    return {
        "original_filename": file.filename,
        "saved_as": unique_filename
    }
```

### Organize Files by Directory
```python
from datetime import datetime

@app.post("/organized-upload/")
async def organized_upload(file: UploadFile = File(...)):
    """Upload with organized directory structure"""
    # Create date-based directory
    now = datetime.now()
    year_month_day = now.strftime("%Y/%m/%d")
    
    unique_filename = get_unique_filename(file.filename)
    file_location = f"uploads/{year_month_day}/{unique_filename}"
    
    # Create directories
    os.makedirs(os.path.dirname(file_location), exist_ok=True)
    
    # Save file
    contents = await file.read()
    with open(file_location, "wb") as buffer:
        buffer.write(contents)
    
    return {
        "stored_path": file_location,
        "filename": file.filename
    }
```

## Returning File Responses

### Download File
```python
from fastapi.responses import FileResponse

@app.get("/download/{filename}")
async def download_file(filename: str):
    """Download a file"""
    file_path = f"uploads/{filename}"
    
    if not os.path.exists(file_path):
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="File not found"
        )
    
    return FileResponse(
        path=file_path,
        filename=filename,
        media_type="application/octet-stream"
    )
```

### Stream Large File
```python
from fastapi.responses import StreamingResponse
import io

@app.get("/stream-download/{filename}")
async def stream_download(filename: str):
    """Stream file for large files"""
    file_path = f"uploads/{filename}"
    
    if not os.path.exists(file_path):
        raise HTTPException(status_code=404, detail="File not found")
    
    def iterfile():
        with open(file_path, "rb") as file_obj:
            yield from file_obj
    
    return StreamingResponse(
        iterfile(),
        media_type="application/octet-stream",
        headers={"Content-Disposition": f"attachment; filename={filename}"}
    )
```

### Serve Image
```python
@app.get("/image/{filename}")
async def get_image(filename: str):
    """Return image file"""
    file_path = f"uploads/images/{filename}"
    
    if not os.path.exists(file_path):
        raise HTTPException(status_code=404, detail="Image not found")
    
    return FileResponse(
        path=file_path,
        media_type="image/jpeg"
    )
```

### Return File as JSON
```python
@app.get("/file-info/{filename}")
async def get_file_info(filename: str):
    """Return file information"""
    file_path = f"uploads/{filename}"
    
    if not os.path.exists(file_path):
        raise HTTPException(status_code=404, detail="File not found")
    
    file_size = os.path.getsize(file_path)
    file_stat = os.stat(file_path)
    
    return {
        "filename": filename,
        "size": file_size,
        "created_at": file_stat.st_ctime,
        "modified_at": file_stat.st_mtime
    }
```

---

# **TOPIC 12: Async Programming**

## Understanding Async & Await

**Async/Await** allows handling multiple operations concurrently without blocking.

### Sync vs Async Execution
```python
import time

# SYNC (Blocking) - Waits for each operation
def sync_function():
    print("Start")
    time.sleep(1)
    print("After 1 second")
    time.sleep(1)
    print("After another second")
    # Total: 2 seconds

# ASYNC (Non-blocking) - Can do other work while waiting
async def async_function():
    print("Start")
    await asyncio.sleep(1)
    print("After 1 second")
    await asyncio.sleep(1)
    print("After another second")
    # Total: 2 seconds if run sequentially
    # But can run other async operations concurrently
```

## Async Endpoints

### Basic Async Endpoint
```python
from fastapi import FastAPI
import asyncio

app = FastAPI()

@app.get("/items/")
async def get_items():
    """Async endpoint"""
    await asyncio.sleep(1)  # Simulated async operation
    return [{"id": 1, "name": "Item 1"}]
```

### Async with Database
```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

# Async SQLAlchemy setup
DATABASE_URL = "sqlite+aiosqlite:///./test.db"

engine = create_async_engine(DATABASE_URL)
AsyncSessionLocal = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/users/")
async def get_users(db: AsyncSession = Depends(get_db)):
    """Get users asynchronously"""
    result = await db.execute(select(User))
    users = result.scalars().all()
    return users
```

### Multiple Concurrent Requests
```python
import asyncio

@app.get("/concurrent/")
async def concurrent_requests():
    """Make multiple async calls concurrently"""
    # All operations run in parallel
    results = await asyncio.gather(
        fetch_users(),
        fetch_posts(),
        fetch_comments()
    )
    return {
        "users": results[0],
        "posts": results[1],
        "comments": results[2]
    }

async def fetch_users():
    await asyncio.sleep(0.5)
    return [{"id": 1, "name": "User"}]

async def fetch_posts():
    await asyncio.sleep(0.5)
    return [{"id": 1, "title": "Post"}]

async def fetch_comments():
    await asyncio.sleep(0.5)
    return [{"id": 1, "content": "Comment"}]
```

## Blocking vs Non-Blocking Operations

### Blocking Operation
```python
import time

@app.get("/slow-sync/")
def slow_sync_endpoint():
    """Blocking operation - freezes event loop"""
    time.sleep(5)  # This blocks all other requests!
    return {"message": "Done"}
```

### Non-Blocking Operation
```python
import asyncio

@app.get("/slow-async/")
async def slow_async_endpoint():
    """Non-blocking - other requests can run"""
    await asyncio.sleep(5)  # Doesn't block other requests
    return {"message": "Done"}
```

### CPU-Bound in Async
```python
from concurrent.futures import ThreadPoolExecutor

executor = ThreadPoolExecutor(max_workers=4)

def cpu_bound_task(n):
    """Heavy CPU calculation"""
    result = 0
    for i in range(n):
        result += i
    return result

@app.get("/cpu-task/")
async def cpu_task_endpoint():
    """Run CPU-bound task without blocking"""
    loop = asyncio.get_event_loop()
    result = await loop.run_in_executor(executor, cpu_bound_task, 1000000)
    return {"result": result}
```

## Async External API Calls

### Using httpx for Async Requests
```bash
pip install httpx
```

### Async HTTP Requests
```python
import httpx

@app.get("/external-data/")
async def get_external_data():
    """Call external API asynchronously"""
    async with httpx.AsyncClient() as client:
        response = await client.get("https://api.example.com/data")
        return response.json()

@app.get("/multiple-apis/")
async def get_from_multiple_apis():
    """Call multiple APIs concurrently"""
    async with httpx.AsyncClient() as client:
        responses = await asyncio.gather(
            client.get("https://api1.example.com/data"),
            client.get("https://api2.example.com/data"),
            client.get("https://api3.example.com/data")
        )
        return {
            "api1": responses[0].json(),
            "api2": responses[1].json(),
            "api3": responses[2].json()
        }
```

## Performance Benefits of Async

### Benchmark: Sync vs Async
```python
import time
import asyncio
import httpx

# SYNC VERSION (Slow)
def sync_multiple_requests():
    import requests
    start = time.time()
    
    for i in range(5):
        response = requests.get("https://api.example.com/data")
    
    return time.time() - start  # ~5+ seconds

# ASYNC VERSION (Fast)
async def async_multiple_requests():
    start = time.time()
    
    async with httpx.AsyncClient() as client:
        await asyncio.gather(*[
            client.get("https://api.example.com/data")
            for _ in range(5)
        ])
    
    return time.time() - start  # ~1+ second
```

---

# **TOPIC 13: Error Handling & Validation**

## Raising HTTPException

### Basic HTTPException
```python
from fastapi import FastAPI, HTTPException, status

app = FastAPI()

@app.get("/items/{item_id}")
def get_item(item_id: int):
    if item_id < 0:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Item ID must be positive"
        )
    
    if item_id > 1000:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Item not found"
        )
    
    return {"item_id": item_id}
```

### HTTPException with Headers
```python
@app.get("/protected/")
def protected_endpoint(token: str = None):
    if not token:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Not authenticated",
            headers={"WWW-Authenticate": "Bearer"}
        )
    return {"message": "Protected data"}
```

## Custom Exception Classes

### Create Custom Exceptions
```python
from fastapi import FastAPI, HTTPException, status

class ItemNotFoundError(HTTPException):
    def __init__(self, item_id: int):
        super().__init__(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"Item with ID {item_id} not found"
        )

class UnauthorizedError(HTTPException):
    def __init__(self, message: str = "Not authenticated"):
        super().__init__(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail=message,
            headers={"WWW-Authenticate": "Bearer"}
        )

class PermissionDeniedError(HTTPException):
    def __init__(self, message: str = "Permission denied"):
        super().__init__(
            status_code=status.HTTP_403_FORBIDDEN,
            detail=message
        )

app = FastAPI()

@app.get("/items/{item_id}")
def get_item(item_id: int):
    if item_id not in items_db:
        raise ItemNotFoundError(item_id)
    return items_db[item_id]
```

## Custom Exception Handlers

### Global Exception Handler
```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

app = FastAPI()

class CustomException(Exception):
    def __init__(self, message: str, code: int = 400):
        self.message = message
        self.code = code

@app.exception_handler(CustomException)
async def custom_exception_handler(request: Request, exc: CustomException):
    return JSONResponse(
        status_code=exc.code,
        content={"detail": exc.message}
    )

@app.get("/items/{item_id}")
def get_item(item_id: int):
    if item_id not in items_db:
        raise CustomException("Item not found", code=404)
    return items_db[item_id]
```

### Multiple Exception Handlers
```python
class DatabaseError(Exception):
    pass

class ValidationError(Exception):
    pass

@app.exception_handler(DatabaseError)
async def database_error_handler(request: Request, exc: DatabaseError):
    return JSONResponse(
        status_code=500,
        content={"detail": "Database error occurred"}
    )

@app.exception_handler(ValidationError)
async def validation_error_handler(request: Request, exc: ValidationError):
    return JSONResponse(
        status_code=422,
        content={"detail": str(exc)}
    )

@app.get("/data/")
def get_data():
    try:
        data = fetch_from_db()
    except Exception as e:
        raise DatabaseError("Failed to fetch data")
    return data
```

## Validation Errors Handling

### Automatic Pydantic Validation
```python
from pydantic import BaseModel, Field

class Item(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    price: float = Field(..., gt=0)

@app.post("/items/")
def create_item(item: Item):
    # Pydantic automatically validates
    # Returns 422 Unprocessable Entity if invalid
    return item

# Invalid request returns:
# {
#   "detail": [
#     {
#       "loc": ["body", "price"],
#       "msg": "ensure this value is greater than 0",
#       "type": "value_error.number.not_gt"
#     }
#   ]
# }
```

### Custom Validation
```python
from pydantic import BaseModel, validator

class User(BaseModel):
    username: str
    email: str
    age: int

    @validator('username')
    def username_valid(cls, v):
        if len(v) < 3:
            raise ValueError('Username must be at least 3 characters')
        return v
    
    @validator('age')
    def age_valid(cls, v):
        if v < 18:
            raise ValueError('User must be 18 or older')
        return v

@app.post("/users/")
def create_user(user: User):
    return user
```

## Global Exception Handling

### Catch All Exceptions
```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

app = FastAPI()

@app.exception_handler(Exception)
async def general_exception_handler(request: Request, exc: Exception):
    return JSONResponse(
        status_code=500,
        content={
            "detail": "Internal server error",
            "error": str(exc)  # Remove in production!
        }
    )
```

### Structured Error Response
```python
from pydantic import BaseModel
from typing import Optional

class ErrorResponse(BaseModel):
    success: bool = False
    error_code: str
    message: str
    details: Optional[dict] = None
    path: str

@app.exception_handler(Exception)
async def exception_handler(request: Request, exc: Exception):
    return JSONResponse(
        status_code=500,
        content={
            "success": False,
            "error_code": "INTERNAL_SERVER_ERROR",
            "message": "An unexpected error occurred",
            "path": str(request.url.path)
        }
    )
```

---

# **TOPIC 14: API Security Best Practices**

## Securing Endpoints

### Require Authentication
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def verify_token(token: str = Depends(oauth2_scheme)):
    if not token:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Not authenticated"
        )
    return token

@app.get("/secure/")
def secure_endpoint(token: str = Depends(verify_token)):
    return {"message": "Secure data", "token": token}
```

### Role-Based Access
```python
def require_admin(current_user = Depends(get_current_user)):
    if current_user.role != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Admin access required"
        )
    return current_user

@app.delete("/users/{user_id}")
def delete_user(user_id: int, admin = Depends(require_admin)):
    return {"message": "User deleted"}
```

## Rate Limiting Concepts

### Using SlowAPI for Rate Limiting
```bash
pip install slowapi
```

### Basic Rate Limiting
```python
from fastapi import FastAPI
from slowapi import Limiter
from slowapi.util import get_remote_address

app = FastAPI()
limiter = Limiter(key_func=get_remote_address)

@app.get("/items/")
@limiter.limit("5/minute")
def get_items(request: Request):
    return [{"id": 1}]

@app.post("/login/")
@limiter.limit("3/minute")
def login(request: Request, credentials: dict):
    return {"token": "abc123"}
```

### Custom Rate Limiting
```python
from datetime import datetime, timedelta
from collections import defaultdict

class RateLimiter:
    def __init__(self, calls: int, period: int):
        self.calls = calls
        self.period = period
        self.calls_made = defaultdict(list)
    
    def is_allowed(self, client_id: str):
        now = datetime.now()
        # Remove old calls outside period
        self.calls_made[client_id] = [
            call_time for call_time in self.calls_made[client_id]
            if call_time > now - timedelta(seconds=self.period)
        ]
        
        if len(self.calls_made[client_id]) < self.calls:
            self.calls_made[client_id].append(now)
            return True
        return False

limiter = RateLimiter(calls=10, period=60)

@app.get("/api/data/")
def get_data(request: Request):
    client_id = request.client.host
    if not limiter.is_allowed(client_id):
        raise HTTPException(
            status_code=429,
            detail="Rate limit exceeded"
        )
    return {"data": "value"}
```

## Input Validation

### Type Validation
```python
from pydantic import BaseModel, Field

class Item(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    price: float = Field(..., gt=0, le=999999)
    quantity: int = Field(..., ge=0)

@app.post("/items/")
def create_item(item: Item):
    # Pydantic validates all fields
    return item
```

### String Validation
```python
from pydantic import BaseModel, EmailStr, constr
from typing import Optional

class User(BaseModel):
    username: constr(min_length=3, max_length=50, regex="^[a-zA-Z0-9_]*$")
    email: EmailStr
    phone: Optional[constr(regex="^\\d{10}$")] = None

@app.post("/users/")
def create_user(user: User):
    return user
```

## Preventing SQL Injection

### Use Parameterized Queries (SQLAlchemy)
```python
from sqlalchemy import select

# SAFE - Parameterized query
@app.get("/users/{user_id}")
def get_user(user_id: int, db: Session = Depends(get_db)):
    # SQLAlchemy handles parameterization automatically
    user = db.query(User).filter(User.id == user_id).first()
    return user

# UNSAFE - String concatenation (DON'T DO THIS!)
# query = f"SELECT * FROM users WHERE id = {user_id}"
```

### Input Sanitization
```python
import html

def sanitize_input(user_input: str) -> str:
    """Sanitize user input"""
    return html.escape(user_input)

@app.post("/comments/")
def create_comment(content: str):
    safe_content = sanitize_input(content)
    # Store safe_content
    return {"content": safe_content}
```

## Environment Variable Management

### Using python-dotenv
```bash
pip install python-dotenv
```

### .env File
```
DATABASE_URL=postgresql://user:password@localhost/dbname
SECRET_KEY=your-secret-key-change-in-production
API_KEY=your-api-key
DEBUG=False
```

### Load Environment Variables
```python
import os
from dotenv import load_dotenv

load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")
SECRET_KEY = os.getenv("SECRET_KEY")
API_KEY = os.getenv("API_KEY")
DEBUG = os.getenv("DEBUG", "False") == "True"

# Validate required variables
if not DATABASE_URL:
    raise ValueError("DATABASE_URL environment variable is required")
```

### Settings Class
```python
from pydantic import BaseSettings

class Settings(BaseSettings):
    database_url: str
    secret_key: str
    api_key: str
    debug: bool = False
    
    class Config:
        env_file = ".env"

settings = Settings()

# Usage
from fastapi import FastAPI
app = FastAPI()

# Use settings.secret_key, settings.database_url, etc.
```

## HTTPS & Production Security

### Enforce HTTPS
```python
from fastapi.middleware.trustedhost import TrustedHostMiddleware

app = FastAPI()

app.add_middleware(
    TrustedHostMiddleware,
    allowed_hosts=["example.com", "www.example.com"]
)
```

### Security Headers
```python
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://example.com"],
    allow_credentials=True,
    allow_methods=["GET", "POST"],
    allow_headers=["*"],
)

@app.middleware("http")
async def add_security_headers(request: Request, call_next):
    response = await call_next(request)
    
    # Add security headers
    response.headers["X-Content-Type-Options"] = "nosniff"
    response.headers["X-Frame-Options"] = "DENY"
    response.headers["X-XSS-Protection"] = "1; mode=block"
    response.headers["Strict-Transport-Security"] = "max-age=31536000; includeSubDomains"
    
    return response
```

---

# **TOPIC 15: Testing FastAPI Applications**

## Using TestClient

### Basic Test Setup
```python
from fastapi import FastAPI
from fastapi.testclient import TestClient

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello World"}

client = TestClient(app)

def test_read_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello World"}
```

### Testing POST Requests
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

@app.post("/items/")
def create_item(item: Item):
    return item

def test_create_item():
    response = client.post(
        "/items/",
        json={"name": "Laptop", "price": 999.99}
    )
    assert

# **TOPIC 15: Testing FastAPI Applications (CONTINUED)**

## Using TestClient (CONTINUED)

### Testing POST Requests (CONTINUED)
```python
def test_create_item():
    response = client.post(
        "/items/",
        json={"name": "Laptop", "price": 999.99}
    )
    assert response.status_code == 200
    data = response.json()
    assert data["name"] == "Laptop"
    assert data["price"] == 999.99

def test_create_item_invalid():
    response = client.post(
        "/items/",
        json={"name": "Laptop"}  # Missing price
    )
    assert response.status_code == 422  # Validation error
```

### Testing with Path Parameters
```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id, "name": "John"}

def test_get_user():
    response = client.get("/users/123")
    assert response.status_code == 200
    assert response.json()["user_id"] == 123

def test_get_user_not_found():
    response = client.get("/users/99999")
    assert response.status_code == 404
```

### Testing with Query Parameters
```python
@app.get("/items/")
def get_items(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}

def test_get_items_default():
    response = client.get("/items/")
    assert response.status_code == 200
    assert response.json() == {"skip": 0, "limit": 10}

def test_get_items_with_params():
    response = client.get("/items/?skip=5&limit=20")
    assert response.status_code == 200
    assert response.json() == {"skip": 5, "limit": 20}
```

## Writing Unit Tests with Pytest

### Basic Test Structure
```python
# test_main.py
import pytest
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

class TestItems:
    """Test Item endpoints"""
    
    def test_create_item(self):
        response = client.post(
            "/items/",
            json={"name": "Item", "price": 100.0}
        )
        assert response.status_code == 200
    
    def test_get_items(self):
        response = client.get("/items/")
        assert response.status_code == 200
    
    def test_get_item_by_id(self):
        response = client.get("/items/1")
        assert response.status_code == 200

class TestUsers:
    """Test User endpoints"""
    
    def test_create_user(self):
        response = client.post(
            "/users/",
            json={"username": "john", "email": "john@example.com"}
        )
        assert response.status_code == 200
```

### Parametrized Tests
```python
import pytest

@pytest.mark.parametrize("item_id,expected_status", [
    (1, 200),
    (999, 404),
    (0, 422),
    (-1, 422),
])
def test_get_item_various_ids(item_id, expected_status):
    response = client.get(f"/items/{item_id}")
    assert response.status_code == expected_status
```

### Fixtures for Reusable Test Data
```python
@pytest.fixture
def sample_item():
    """Fixture providing sample item"""
    return {"name": "Test Item", "price": 100.0}

@pytest.fixture
def sample_user():
    """Fixture providing sample user"""
    return {"username": "testuser", "email": "test@example.com"}

def test_create_item_with_fixture(sample_item):
    response = client.post("/items/", json=sample_item)
    assert response.status_code == 200
```

## Testing Endpoints

### Test All HTTP Methods
```python
def test_item_crud():
    """Test full CRUD cycle"""
    # CREATE
    create_response = client.post(
        "/items/",
        json={"name": "New Item", "price": 50.0}
    )
    assert create_response.status_code == 201
    item_id = create_response.json()["id"]
    
    # READ
    read_response = client.get(f"/items/{item_id}")
    assert read_response.status_code == 200
    assert read_response.json()["name"] == "New Item"
    
    # UPDATE
    update_response = client.put(
        f"/items/{item_id}",
        json={"name": "Updated Item", "price": 75.0}
    )
    assert update_response.status_code == 200
    
    # DELETE
    delete_response = client.delete(f"/items/{item_id}")
    assert delete_response.status_code == 204
```

### Test Status Codes
```python
def test_status_codes():
    """Test various status codes"""
    # 200 OK
    assert client.get("/items/").status_code == 200
    
    # 201 Created
    response = client.post("/items/", json={"name": "Item", "price": 100})
    assert response.status_code == 201
    
    # 204 No Content
    assert client.delete("/items/1").status_code == 204
    
    # 400 Bad Request
    response = client.post("/items/", json={"name": "Item"})  # Missing price
    assert response.status_code == 422
    
    # 401 Unauthorized
    assert client.get("/protected/").status_code == 401
    
    # 403 Forbidden
    assert client.delete("/admin/delete").status_code == 403
    
    # 404 Not Found
    assert client.get("/items/99999").status_code == 404
```

## Mocking Dependencies

### Override Dependencies in Tests
```python
from fastapi import Depends

def get_db():
    # Real database
    pass

def get_current_user():
    # Real authentication
    pass

# Override in tests
@pytest.fixture
def test_db():
    """Mock database"""
    return {"users": [], "items": []}

@pytest.fixture
def test_user():
    """Mock user"""
    return {"id": 1, "username": "testuser"}

def test_with_override_db(test_db):
    app.dependency_overrides[get_db] = lambda: test_db
    
    response = client.get("/items/")
    assert response.status_code == 200
    
    # Cleanup
    app.dependency_overrides.clear()

def test_with_override_user(test_user):
    app.dependency_overrides[get_current_user] = lambda: test_user
    
    response = client.get("/me/")
    assert response.status_code == 200
    assert response.json()["username"] == "testuser"
    
    app.dependency_overrides.clear()
```

### Mock External API Calls
```python
from unittest.mock import patch
import pytest

@patch("httpx.AsyncClient.get")
def test_external_api_call(mock_get):
    """Mock external API"""
    mock_get.return_value.json.return_value = {"data": "mocked"}
    
    response = client.get("/external-data/")
    assert response.status_code == 200
```

## API Validation Testing

### Test Request Validation
```python
def test_validation_errors():
    """Test Pydantic validation"""
    # Missing required field
    response = client.post("/users/", json={"username": "john"})
    assert response.status_code == 422
    errors = response.json()["detail"]
    assert len(errors) > 0
    assert errors[0]["loc"][0] == "body"

def test_field_constraints():
    """Test field validation constraints"""
    # Name too short
    response = client.post(
        "/users/",
        json={"username": "ab", "email": "test@example.com"}  # min_length=3
    )
    assert response.status_code == 422
    
    # Invalid email
    response = client.post(
        "/users/",
        json={"username": "john", "email": "invalid-email"}
    )
    assert response.status_code == 422
```

### Test Response Models
```python
def test_response_model():
    """Test response model serialization"""
    response = client.get("/users/1")
    assert response.status_code == 200
    
    data = response.json()
    # Verify response structure
    assert "id" in data
    assert "username" in data
    assert "email" in data
    
    # Sensitive field should not be included
    assert "password_hash" not in data
```

## Complete Test Example

```python
# tests/conftest.py
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from main import app, get_db
from models import Base

SQLALCHEMY_TEST_DATABASE_URL = "sqlite:///./test_db.db"

engine = create_engine(
    SQLALCHEMY_TEST_DATABASE_URL,
    connect_args={"check_same_thread": False},
)
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base.metadata.create_all(bind=engine)

def override_get_db():
    try:
        db = TestingSessionLocal()
        yield db
    finally:
        db.close()

app.dependency_overrides[get_db] = override_get_db

@pytest.fixture
def client():
    return TestClient(app)

# tests/test_users.py
import pytest

def test_create_user(client):
    response = client.post(
        "/users/",
        json={"username": "john", "email": "john@example.com", "password": "secret"}
    )
    assert response.status_code == 201
    assert response.json()["username"] == "john"

def test_get_user(client):
    # Create first
    client.post(
        "/users/",
        json={"username": "jane", "email": "jane@example.com", "password": "secret"}
    )
    
    # Get user
    response = client.get("/users/jane")
    assert response.status_code == 200
    assert response.json()["email"] == "jane@example.com"

def test_get_nonexistent_user(client):
    response = client.get("/users/nonexistent")
    assert response.status_code == 404
```

---

# **TOPIC 16: Performance & Optimization**

## Understanding Performance Bottlenecks

### Identify Bottlenecks
```python
import time
import logging

logger = logging.getLogger(__name__)

@app.get("/slow-endpoint/")
async def slow_endpoint():
    """Endpoint with performance issues"""
    
    # Measure database query time
    start_db = time.time()
    users = db.query(User).all()
    db_time = time.time() - start_db
    logger.info(f"Database query took {db_time:.3f}s")
    
    # Measure processing time
    start_process = time.time()
    processed_data = process_users(users)
    process_time = time.time() - start_process
    logger.info(f"Processing took {process_time:.3f}s")
    
    return {"data": processed_data}
```

### Use APM Tools
```python
# Application Performance Monitoring example with Sentry
import sentry_sdk
from sentry_sdk.integrations.fastapi import FastApiIntegration

sentry_sdk.init(
    dsn="your-sentry-dsn",
    integrations=[FastApiIntegration()],
    traces_sample_rate=1.0
)

# Now Sentry tracks all requests and errors
```

## Using Async Properly

### Async Reduces Response Time
```python
import asyncio
import time

# WRONG - Blocks event loop
@app.get("/wrong/")
async def wrong_endpoint():
    time.sleep(5)  # Blocks all other requests!
    return {"message": "Done"}

# CORRECT - Non-blocking
@app.get("/correct/")
async def correct_endpoint():
    await asyncio.sleep(5)  # Other requests can run
    return {"message": "Done"}
```

### Concurrent Operations
```python
@app.get("/concurrent/")
async def concurrent_operations():
    """Run multiple async operations concurrently"""
    start = time.time()
    
    # All three run in parallel, not sequentially
    results = await asyncio.gather(
        fetch_from_api1(),
        fetch_from_api2(),
        fetch_from_api3()
    )
    
    elapsed = time.time() - start
    # Total time = longest operation, not sum of all
    return {
        "results": results,
        "elapsed": elapsed
    }
```

## Query Optimization

### Use Indexes
```python
from sqlalchemy import Column, Integer, String, Index

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True)
    username = Column(String(50), unique=True, index=True)  # Index
    email = Column(String(100), unique=True, index=True)    # Index
    created_at = Column(DateTime, index=True)               # Index frequent queries

# Migration
# CREATE INDEX idx_username ON users(username)
# CREATE INDEX idx_email ON users(email)
```

### Eager Loading
```python
from sqlalchemy.orm import joinedload

# SLOW - N+1 queries
def get_users_with_posts_slow():
    users = db.query(User).all()
    for user in users:
        print(user.posts)  # Executes query for each user!

# FAST - One query with join
def get_users_with_posts_fast():
    users = db.query(User).options(joinedload(User.posts)).all()
    for user in users:
        print(user.posts)  # Already loaded
```

### Select Only Needed Columns
```python
# SLOW - Loads all columns
users = db.query(User).all()

# FAST - Load only needed columns
users = db.query(User.id, User.username, User.email).all()
```

### Use LIMIT in Pagination
```python
# Don't load all records
def get_posts_paginated(skip: int = 0, limit: int = 10):
    posts = db.query(Post).offset(skip).limit(limit).all()
    return posts

# Good pagination
@app.get("/posts/")
def get_posts(skip: int = 0, limit: int = 10):
    if limit > 100:
        limit = 100  # Cap maximum limit
    return get_posts_paginated(skip, limit)
```

## Response Model Optimization

### Exclude Unnecessary Fields
```python
from pydantic import BaseModel

class UserFull(BaseModel):
    id: int
    username: str
    email: str
    password_hash: str  # Never return this!
    created_at: datetime

class UserPublic(BaseModel):
    id: int
    username: str
    email: str

@app.get("/users/{user_id}", response_model=UserPublic)
def get_user(user_id: int):
    user = db.query(User).get(user_id)
    return user  # Only return public fields
```

### Use response_model_exclude
```python
@app.get("/users/")
def get_users():
    users = db.query(User).all()
    return {
        "data": users,
        "response_model_exclude": {"password_hash"}  # Exclude field
    }

# Or in endpoint
@app.get(
    "/users/",
    response_model=List[User],
    response_model_exclude={"password_hash", "created_at"}
)
def get_users():
    return db.query(User).all()
```

## Caching Basics

### In-Memory Caching
```python
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_operation(param: str):
    """Result is cached for 128 unique param values"""
    return process_data(param)

@app.get("/cached-data/{param}")
def get_cached_data(param: str):
    return expensive_operation(param)
```

### Manual Caching
```python
from datetime import datetime, timedelta

cache = {}
cache_expiry = {}

def get_cached_data(key: str, ttl_seconds: int = 300):
    """Get from cache if not expired"""
    if key in cache:
        if datetime.now() < cache_expiry[key]:
            return cache[key]
        else:
            del cache[key]
            del cache_expiry[key]
    
    # Fetch fresh data
    data = fetch_from_db(key)
    
    # Cache it
    cache[key] = data
    cache_expiry[key] = datetime.now() + timedelta(seconds=ttl_seconds)
    
    return data
```

### Redis Caching
```bash
pip install redis
```

```python
import redis
import json

r = redis.Redis(host='localhost', port=6379, db=0)

@app.get("/user/{user_id}")
def get_user(user_id: int):
    # Check cache
    cache_key = f"user:{user_id}"
    cached = r.get(cache_key)
    
    if cached:
        return json.loads(cached)
    
    # Fetch from database
    user = db.query(User).get(user_id)
    
    # Cache for 1 hour
    r.setex(cache_key, 3600, json.dumps(user.dict()))
    
    return user
```

---

# **TOPIC 17: Deployment Basics**

## Running with Uvicorn

### Basic Command
```bash
# Simple run
uvicorn main:app --host 0.0.0.0 --port 8000

# With workers (for better performance)
uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4

# Custom log level
uvicorn main:app --log-level debug

# Without access log
uvicorn main:app --access-log false
```

### Uvicorn Configuration File
```python
# uvicorn_config.py
import logging

# Logging configuration
log_config = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "default": {
            "format": "%(asctime)s - %(name)s - %(levelname)s - %(message)s",
        },
    },
    "handlers": {
        "default": {
            "formatter": "default",
            "class": "logging.StreamHandler",
            "stream": "ext://sys.stdout",
        },
    },
    "loggers": {
        "uvicorn": {"handlers": ["default"], "level": "INFO"},
    },
}

# Server configuration
server_config = {
    "app": "main:app",
    "host": "0.0.0.0",
    "port": 8000,
    "workers": 4,
    "log_config": log_config,
    "access_log": True,
}
```

## Running with Gunicorn

### Installation
```bash
pip install gunicorn
```

### Basic Command
```bash
# Run with Uvicorn worker
gunicorn -k uvicorn.workers.UvicornWorker main:app --workers 4 --bind 0.0.0.0:8000
```

### Gunicorn Configuration File
```python
# gunicorn_config.py
import multiprocessing

# Server socket
bind = "0.0.0.0:8000"

# Number of worker processes
workers = multiprocessing.cpu_count() * 2 + 1

# Worker class
worker_class = "uvicorn.workers.UvicornWorker"

# Maximum number of requests before worker restart
max_requests = 1000
max_requests_jitter = 50

# Timeout
timeout = 120

# Keep alive
keepalive = 5

# Logging
accesslog = "-"
errorlog = "-"
loglevel = "info"

# Process naming
proc_name = "fastapi-app"
```

### Run with Config
```bash
gunicorn -c gunicorn_config.py main:app
```

## Dockerizing FastAPI App

### Basic Dockerfile
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Copy requirements
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY . .

# Expose port
EXPOSE 8000

# Run application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Production Dockerfile (Multi-stage)
```dockerfile
# Build stage
FROM python:3.11-slim as builder

WORKDIR /app

COPY requirements.txt .

RUN pip install --user --no-cache-dir -r requirements.txt

# Runtime stage
FROM python:3.11-slim

WORKDIR /app

# Copy Python dependencies from builder
COPY --from=builder /root/.local /root/.local

ENV PATH=/root/.local/bin:$PATH

# Copy application
COPY . .

# Create non-root user
RUN useradd -m appuser && chown -R appuser:appuser /app
USER appuser

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## Docker Compose

### Basic docker-compose.yml
```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:password@db:5432/app
      - SECRET_KEY=your-secret-key
    depends_on:
      - db
    volumes:
      - ./:/app  # Hot reload in development
    command: uvicorn main:app --host 0.0.0.0 --reload

  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=app
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```

### Running Docker Compose
```bash
# Start services
docker-compose up

# Start in background
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f web
```

## Environment Configuration

### .env File
```
# Database
DATABASE_URL=postgresql://user:password@localhost/dbname

# Security
SECRET_KEY=your-super-secret-key-change-in-production
API_KEY=your-api-key

# Settings
DEBUG=False
ENVIRONMENT=production
LOG_LEVEL=info

# CORS
CORS_ORIGINS=https://example.com,https://www.example.com

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SENDER_EMAIL=noreply@example.com
SENDER_PASSWORD=your-app-password
```

### Load Configuration
```python
from pydantic import BaseSettings
from functools import lru_cache

class Settings(BaseSettings):
    # Database
    database_url: str
    
    # Security
    secret_key: str
    api_key: str
    
    # Settings
    debug: bool = False
    environment: str = "development"
    log_level: str = "info"
    
    # CORS
    cors_origins: list = ["*"]
    
    class Config:
        env_file = ".env"

@lru_cache()
def get_settings():
    return Settings()

settings = get_settings()

# Usage in app
app = FastAPI(debug=settings.debug)
```

## Basic CI/CD Overview

### GitHub Actions Example
```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'
    
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        pip install pytest pytest-cov
    
    - name: Run tests
      run: pytest --cov=app tests/
    
    - name: Upload coverage
      uses: codecov/codecov-action@v3
  
  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Deploy to production
      run: |
        # Deploy script here
        echo "Deploying to production..."
```

---

# **TOPIC 18: Logging & Monitoring**

## Logging Best Practices

### Setup Logging
```python
import logging
import sys

# Create logger
logger = logging.getLogger(__name__)

# Create handler
handler = logging.StreamHandler(sys.stdout)

# Create formatter
formatter = logging.Formatter(
    '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

# Add formatter to handler
handler.setFormatter(formatter)

# Add handler to logger
logger.addHandler(handler)
logger.setLevel(logging.INFO)

# Usage
logger.info("Application started")
logger.warning("This is a warning")
logger.error("An error occurred")
```

## Structured Logging

### JSON Logging
```bash
pip install python-json-logger
```

```python
import logging
import sys
from pythonjsonlogger import jsonlogger

# Create logger
logger = logging.getLogger(__name__)

# JSON formatter
formatter = jsonlogger.JsonFormatter()
handler = logging.StreamHandler(sys.stdout)
handler.setFormatter(formatter)
logger.addHandler(handler)
logger.setLevel(logging.INFO)

# Usage - outputs JSON
logger.info("User logged in", extra={"user_id": 123, "ip": "192.168.1.1"})
# Output: {"asctime": "...", "name": "...", "levelname": "INFO", "message": "User logged in", "user_id": 123, "ip": "192.168.1.1"}
```

### Structured Logging in FastAPI
```python
import logging
from pythonjsonlogger import jsonlogger

logger = logging.getLogger(__name__)

@app.middleware("http")
async def log_requests(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    
    logger.info(
        "Request processed",
        extra={
            "method": request.method,
            "path": request.url.path,
            "status_code": response.status_code,
            "process_time": process_time,
            "client": request.client.host
        }
    )
    
    return response
```

## Monitoring Application Performance

### Health Check Endpoint
```python
from datetime import datetime

@app.get("/health/")
def health_check():
    """Health check endpoint"""
    return {
        "status": "healthy",
        "timestamp": datetime.utcnow(),
        "version": "1.0.0"
    }

@app.get("/health/ready/")
def readiness_check():
    """Readiness check - verify dependencies"""
    try:
        # Check database
        db.query(User).count()
        
        # Check external services
        # response = httpx.get("https://external-api.com/health")
        
        return {"status": "ready"}
    except Exception as e:
        raise HTTPException(
            status_code=503,
            detail=f"Service not ready: {str(e)}"
        )

@app.get("/health/live/")
def liveness_check():
    """Liveness check - is app still running"""
    return {"status": "alive"}
```

### Metrics Collection
```bash
pip install prometheus-client
```

```python
from prometheus_client import Counter, Histogram, generate_latest

# Define metrics
request_count = Counter(
    'fastapi_requests_total',
    'Total requests',
    ['method', 'endpoint', 'status']
)

request_duration = Histogram(
    'fastapi_request_duration_seconds',
    'Request duration',
    ['method', 'endpoint']
)

@app.middleware("http")
async def add_metrics(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    duration = time.time() - start_time
    
    # Record metrics
    request_count.labels(
        method=request.method,
        endpoint=request.url.path,
        status=response.status_code
    ).inc()
    
    request_duration.labels(
        method=request.method,
        endpoint=request.url.path
    ).observe(duration)
    
    return response

@app.get("/metrics/")
def metrics():
    """Prometheus metrics endpoint"""
    return generate_latest()
```

---

# **TOPIC 19: Microservice Architecture**

## Monolith vs Microservices

### Monolithic Architecture
```
Single FastAPI Application
├── Users Module
├── Posts Module
├── Comments Module
├── Payments Module
└── Notifications Module

Single Database
```

**Pros:**
- Easier to develop
- Simple deployment
- Easier testing

**Cons:**
- Hard to scale individual components
- Technology locked to one framework
- Single point of failure

### Microservices Architecture
```
User Service
├── API
├── Database
└── Logic

Post Service
├── API
├── Database
└── Logic

Comment Service
├── API
├── Database
└── Logic

Payment Service
├── API
├── Database
└── Logic

API Gateway
└── Routes requests to services
```

**Pros:**
- Scale individual services
- Independent deployment
- Technology flexibility
- Better fault isolation

**Cons:**
- Operational complexity
- Network latency
- Distributed debugging
- Data consistency challenges

## Designing FastAPI as a Microservice

### User Microservice
```python
# user_service/main.py
from fastapi import FastAPI

app = FastAPI(title="User Service", version="1.0.0")

@app.post("/api/users/")
def create_user(user_data: dict):
    """Create user"""
    user = save_to_db(user_data)
    return user

@app.get("/api/users/{user_id}")
def get_user(user_id: int):
    """Get user"""
    user = get_from_db(user_id)
    return user

@app.get("/health/")
def health():
    return {"status": "healthy"}
```

### Post Microservice
```python
# post_service/main.py
from fastapi import FastAPI
import httpx

app = FastAPI(title="Post Service", version="1.0.0")

@app.post("/api/posts/")
async def create_post(post_data: dict):
    """Create post"""
    # Call User Service to verify user exists
    async with httpx.AsyncClient() as client:
        user_response = await client.get(
            f"http://user-service:8001/api/users/{post_data['author_id']}"
        )
        if user_response.status_code != 200:
            raise HTTPException(status_code=400, detail="User not found")
    
    post = save_post(post_data)
    return post

@app.get("/api/posts/{post_id}")
def get_post(post_id: int):
    """Get post"""
    post = get_from_db(post_id)
    return post

@app.get("/health/")
def health():
    return {"status": "healthy"}
```

## API Communication Between Services

### Service-to-Service HTTP Calls
```python
import httpx
from fastapi import HTTPException

async def call_user_service(user_id: int):
    """Call user microservice"""
    try:
        async with httpx.AsyncClient() as client:
            response = await client.get(
                f"http://user-service:8001/api/users/{user_id}",
                timeout=5.0
            )
            response.raise_for_status()
            return response.json()
    except httpx.HTTPError as e:
        raise HTTPException(
            status_code=503,
            detail="User service unavailable"
        )

@app.post("/api/posts/")
async def create_post(post_data: dict):
    """Create post, verify user exists"""
    # Call user service
    user = await call_user_service(post_data["author_id"])
    
    # Create post
    post = save_post(post_data)
    return post
```

### Service Discovery
```python
import os

# Service URLs from environment
SERVICES = {
    "user_service": os.getenv("USER_SERVICE_URL", "http://localhost:8001"),
    "post_service": os.getenv("POST_SERVICE_URL", "http://localhost:8002"),
    "comment_service": os.getenv("COMMENT_SERVICE_URL", "http://localhost:8003"),
}

async def call_service(service_name: str, endpoint: str):
    """Call another service using service discovery"""
    url = f"{SERVICES[service_name]}{endpoint}"
    async with httpx.AsyncClient() as client:
        return await client.get(url)
```

### Message Queue for Async Communication
```bash
pip install pika  # RabbitMQ
pip install redis  # Redis
```

```python
import pika
import json
from fastapi import FastAPI

app = FastAPI()

# RabbitMQ connection
connection = pika.BlockingConnection(
    pika.ConnectionParameters('rabbitmq')
)
channel = connection.channel()

# Declare queue
channel.queue_declare(queue='post_created', durable=True)

def publish_event(event_name: str, data: dict):
    """Publish event to message queue"""
    message = {
        "event": event_name,
        "data": data
    }
    channel.basic_publish(
        exchange='',
        routing_key='post_created',
        body=json.dumps(message)
    )

@app.post("/api/posts/")
def create_post(post_data: dict):
    """Create post and publish event"""
    post = save_post(post_data)
    
    # Publish event - notification service can consume it
    publish_event("post.created", post.dict())
    
    return post
```

## Service-to-Service Authentication

### JWT Between Services
```python
import jwt
from datetime import datetime, timedelta
from fastapi import Depends, HTTPException

SECRET_KEY = "service-secret-key"

def create_service_token(service_id: str):
    """Create token for service"""
    payload = {
        "service_id": service_id,
        "exp": datetime.utcnow() + timedelta(hours=1)
    }
    return jwt.encode(payload, SECRET_KEY, algorithm="HS256")

def verify_service_token(token: str):
    """Verify service token"""
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        return payload["service_id"]
    except jwt.InvalidTokenError:
        raise HTTPException(status_code=401, detail="Invalid token")

# When calling another service
async def call_user_service(user_id: int):
    """Call user service with authentication"""
    token = create_service_token("post-service")
    
    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"http://user-service:8001/api/users/{user_id}",
            headers={"Authorization": f"Bearer {token}"}
        )
        return response.json()

# Endpoint that verifies service calling it
@app.get("/api/users/{user_id}")
def get_user(user_id: int, token: str = Header(None)):
    """Get user - verify calling service"""
    service_id = verify_service_token(token)
    # Only allow calls from authorized services
    if service_id not in ["post-service", "comment-service"]:
        raise HTTPException(status_code=403, detail="Not authorized")
    
    user = get_from_db(user_id)
    return user
```

### API Keys for Service-to-Service
```python
from fastapi.security import APIKeyHeader

api_key_header = APIKeyHeader(name="X-API-Key")

# Allowed services and their keys
SERVICE_KEYS = {
    "post-service": "post-service-key-12345",
    "comment-service": "comment-service-key-67890",
    "notification-service": "notification-service-key-11111"
}

def verify_service_key(api_key: str = Depends(api_key_header)):
    """Verify service API key"""
    for service, key in SERVICE_KEYS.items():
        if api_key == key:
            return service
    
    raise HTTPException(status_code=401, detail="Invalid API key")

@app.get("/api/users/{user_id}")
def get_user(user_id: int, service: str = Depends(verify_service_key)):
    """Get user - only from authorized services"""
    user = get_from_db(user_id)
    return user
```

## Complete Microservice Deployment with Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  api-gateway:
    build: ./api_gateway
    ports:
      - "8000:8000"
    environment:
      - USER_SERVICE_URL=http://user-service:8001
      - POST_SERVICE_URL=http://post-service:8002
      - COMMENT_SERVICE_URL=http://comment-service:8003
    depends_on:
      - user-service
      - post-service
      - comment-service

  user-service:
    build: ./user_service
    expose:
      - "8001"
    environment:
      - DATABASE_URL=postgresql://user:password@user-db:5432/users
    depends_on:
      - user-db
      - rabbitmq

  post-service:
    build: ./post_service
    expose:
      - "8002"
    environment:
      - DATABASE_URL=postgresql://user:password@post-db:5432/posts
      - USER_SERVICE_URL=http://user-service:8001
    depends_on:
      - post-db
      - user-service
      - rabbitmq

  comment-service:
    build: ./comment_service
    expose:
      - "8003"
    environment:
      - DATABASE_URL=postgresql://user:password@comment-db:5432/comments
      - USER_SERVICE_URL=http://user-service:8001
      - POST_SERVICE_URL=http://post-service:8002
    depends_on:
      - comment-db
      - user-service
      - post-service
      - rabbitmq

  user-db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=users
    volumes:
      - user-db-data:/var/lib/postgresql/data

  post-db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=posts
    volumes:
      - post-db-data:/var/lib/postgresql/data

  comment-db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=comments
    volumes:
      - comment-db-data:/var/lib/postgresql/data

  rabbitmq:
    image: rabbitmq:3.12-management
    ports:
      - "5672:5672"
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest
    volumes:
      - rabbitmq-data:/var/lib/rabbitmq

volumes:
  user-db-data:
  post-db-data:
  comment-db-data:
  rabbitmq-data:
```

---

# **CONCLUSION: Your FastAPI Learning Path**

## Recommended Learning Timeline

**Week 1-2: Foundations**
- Topics 1-3: Setup, routing, models
- Build: Simple TODO API

**Week 3-4: API Features**
- Topics 4-7: Documentation, responses, dependencies, middleware
- Build: Expand TODO with users

**Week 5-6: Data & Security**
- Topics 8-9: Authentication, database
- Build: Blog API with users and posts

**Week 7-8: Advanced Features**
- Topics 10-15: Background tasks, files, async, testing
- Build: Complete blog with comments and file uploads

**Week 9-10: Production Ready**
- Topics 16-19: Performance, deployment, monitoring, microservices
- Build: Production deployment with Docker

## Key Takeaways

1. **FastAPI is powerful** - Combines simplicity with production-grade features
2. **Type hints are essential** - Leverage Python's typing system
3. **Async by default** - Build non-blocking, high-performance APIs
4. **Test thoroughly** - Use TestClient and pytest for coverage
5. **Security first** - Validate, authenticate, authorize, encrypt
6. **Monitor everything** - Logging and metrics are critical
7. **Start simple, scale smart** - Begin with monolith, split to microservices if needed

## Further Learning Resources

- **Official Documentation**: https://fastapi.tiangolo.com/
- **Starlette**: https://www.starlette.io/ (underlying framework)
- **SQLAlchemy**: https://www.sqlalchemy.org/
- **Pydantic**: https://pydantic-settings.readthedocs.io/
- **JWT**: https://pyjwt.readthedocs.io/
- **Docker**: https://docs.docker.com/

## Practice Projects

1. **Twitter Clone API** - Users, tweets, likes, follows
2. **E-commerce API** - Products, orders, payments
3. **Social Media** - Posts, comments, likes, notifications
4. **Job Board** - Job listings, applications, companies
5. **Expense Tracker** - User expenses, categories, reports

---

## **COMPLETE! 🎉**

You now have comprehensive notes covering all 19 topics from beginner to advanced FastAPI development. Use these notes as your:
- **Learning Guide** - Study systematically
- **Reference Material** - Quick lookup for concepts
- **Code Examples** - Copy and modify for your projects
- **Interview Prep** - Key concepts and patterns

**Happy Coding! Build amazing APIs with FastAPI! 🚀**
