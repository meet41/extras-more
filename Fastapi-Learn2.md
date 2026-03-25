# FastAPI From Scratch to Advanced – Complete Guide

You’ll build understanding step by step and end up capable of designing, building, testing, and deploying production-ready FastAPI services.

---

## 0. FastAPI Learning Roadmap (Big Picture)

**Phase 1 – Basics**

- Python refresher (functions, types, venv)
- Introduction to FastAPI
- First API: routing, path/query params, request body
- Automatic docs

**Phase 2 – Core API Design**

- Pydantic models (validation, response models)
- Status codes & responses
- Dependency Injection basics
- Middleware, CORS

**Phase 3 – Real Apps**

- Authentication (JWT)
- Database (SQLAlchemy, Postgres/SQLite)
- CRUD operations
- Background tasks, file uploads

**Phase 4 – Quality & Security**

- Error handling, validation
- Testing with TestClient & pytest
- Security best practices, config via env vars

**Phase 5 – Performance & Production**

- Async I/O, bottlenecks
- Caching & optimization
- Logging & monitoring
- Deployment (Uvicorn/Gunicorn, Docker)
- Microservice architecture concepts

We’ll use a **simple “Book Store API”** as a running example, growing it chapter by chapter.

---

## 1. Introduction to FastAPI & Environment Setup

### 1.1 What is FastAPI and Why Use It?

- **FastAPI** is a modern, fast (high-performance) web framework for building APIs in Python.
- Key features:
  - Built on **ASGI** (Asynchronous Server Gateway Interface)
  - Uses **Pydantic** for data validation
  - Auto-generated API docs via **OpenAPI** (Swagger UI, ReDoc)
  - First-class support for **async**/`await`

**Compared to Flask:**

- Flask:
  - Very flexible, minimal, but you must add many things yourself.
  - No built-in validation or automatic docs.
- FastAPI:
  - Typed endpoints → validation + docs auto-generated.
  - Better for **APIs-first** backend.

**Compared to Django:**

- Django:
  - Full-stack framework (ORM, templates, admin, etc.).
  - Great for monolithic web apps.
- FastAPI:
  - Focused on APIs.
  - Easier to use in microservices / modern, async backends.

Use FastAPI when:

- You’re building **JSON APIs / microservices**.
- You want **high performance** and **clean type hints**.
- You care about **developer productivity** (auto docs, validation).

---

### 1.2 Environment Setup

#### Step 1: Create Project Folder

```bash
mkdir fastapi-bookstore
cd fastapi-bookstore
```

#### Step 2: Create Virtual Environment (Windows PowerShell)

```bash
python -m venv venv
.\venv\Scripts\activate
```

#### Step 3: Install FastAPI and Uvicorn

```bash
pip install "fastapi[standard]" uvicorn
```

Or minimal:

```bash
pip install fastapi uvicorn
```

---

### 1.3 First FastAPI App

Create `main.py`:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Welcome to FastAPI Bookstore!"}
```

#### Run Dev Server

```bash
uvicorn main:app --reload
```

- `main` → filename (`main.py`)
- `app` → FastAPI instance (`app = FastAPI()`)
- `--reload` → auto-reload on code changes

Open in browser:

- `http://127.0.0.1:8000/`
- `http://127.0.0.1:8000/docs` (Swagger UI)
- `http://127.0.0.1:8000/redoc` (ReDoc)

---

### 1.4 Project Structure Best Practices

For anything non-trivial:

```text
fastapi-bookstore/
├─ app/
│  ├─ main.py          # entrypoint, create FastAPI app here
│  ├─ api/
│  │  ├─ v1/
│  │  │  ├─ routes_books.py
│  │  │  ├─ routes_auth.py
│  ├─ core/
│  │  ├─ config.py     # settings, env vars
│  ├─ models/
│  │  ├─ book.py
│  ├─ schemas/
│  │  ├─ book.py
│  ├─ db/
│  │  ├─ session.py
│  │  ├─ base.py
│  ├─ services/
│  │  ├─ auth.py
│  │  ├─ email.py
├─ tests/
├─ requirements.txt
```

Mini summary:

- Use **structured folders** (api, models, schemas, db).
- Keep `main.py` small (app creation + router inclusion only).
- Use `venv` and `requirements.txt`.

---

## 2. Creating APIs (Routing & HTTP Methods)

### 2.1 REST Concepts (Brief)

- **Resource**: something you expose via API (e.g., `/books`, users).
- **HTTP methods**:
  - `GET` → Read
  - `POST` → Create
  - `PUT` → Replace/Update
  - `PATCH` → Partial update
  - `DELETE` → Delete

Good REST endpoint example:

```text
GET    /books          # list books
GET    /books/{id}     # get single book
POST   /books          # create book
PUT    /books/{id}     # full update
DELETE /books/{id}     # delete
```

---

### 2.2 Basic Routes & Methods

`app/main.py`:

```python
from fastapi import FastAPI

app = FastAPI()

fake_books_db = [
    {"id": 1, "title": "Clean Code", "author": "Robert C. Martin"},
    {"id": 2, "title": "The Pragmatic Programmer", "author": "Andrew Hunt"},
]

@app.get("/books")
def list_books():
    return fake_books_db

@app.get("/books/{book_id}")
def get_book(book_id: int):
    for book in fake_books_db:
        if book["id"] == book_id:
            return book
    return {"error": "Book not found"}

@app.post("/books")
def create_book(book: dict):
    new_id = max(b["id"] for b in fake_books_db) + 1
    book["id"] = new_id
    fake_books_db.append(book)
    return book

@app.delete("/books/{book_id}")
def delete_book(book_id: int):
    for book in fake_books_db:
        if book["id"] == book_id:
            fake_books_db.remove(book)
            return {"message": "Book deleted"}
    return {"error": "Book not found"}
```

We’ll improve this later with Pydantic models & proper errors.

---

### 2.3 Path & Query Parameters

```python
from typing import Optional

@app.get("/books/{book_id}")
def read_book(book_id: int, q: Optional[str] = None):
    """
    - book_id is a path parameter (part of URL)
    - q is a query parameter: /books/1?q=python
    """
    book = next((b for b in fake_books_db if b["id"] == book_id), None)
    if not book:
        return {"error": "Not found"}
    if q:
        book["query"] = q
    return book

@app.get("/search")
def search_books(author: Optional[str] = None, limit: int = 10):
    """
    /search?author=Martin&limit=5
    """
    results = fake_books_db
    if author:
        results = [b for b in results if author.lower() in b["author"].lower()]
    return results[:limit]
```

---

### 2.4 Request Body Handling

Use Pydantic models (next chapter explains more deeply):

```python
from pydantic import BaseModel

class BookCreate(BaseModel):
    title: str
    author: str
    pages: int

@app.post("/books")
def create_book(book: BookCreate):
    new_id = max(b["id"] for b in fake_books_db) + 1
    new_book = book.dict()
    new_book["id"] = new_id
    fake_books_db.append(new_book)
    return new_book
```

---

### 2.5 Modular Routing with APIRouter

For larger apps, split routes:

`app/api/v1/routes_books.py`:

```python
from fastapi import APIRouter
from pydantic import BaseModel
from typing import List

router = APIRouter(prefix="/books", tags=["Books"])

class Book(BaseModel):
    id: int
    title: str
    author: str

fake_books_db = [
    Book(id=1, title="Clean Code", author="Robert C. Martin"),
    Book(id=2, title="The Pragmatic Programmer", author="Andrew Hunt"),
]

@router.get("/", response_model=List[Book])
def list_books():
    return fake_books_db
```

`app/main.py`:

```python
from fastapi import FastAPI
from app.api.v1.routes_books import router as books_router

app = FastAPI()

app.include_router(books_router)
```

**Common mistakes:**

- Forgetting to include router in `main.py`.
- Mis-using path vs query parameters.

Mini summary:

- Use HTTP methods correctly for CRUD.
- Use **path parameters** for resource identity, **query** for filters.
- Use `APIRouter` to keep routes organized.

---

## 3. Pydantic Models

Pydantic powers most of FastAPI’s validation and serialization.

### 3.1 Data Validation Basics

```python
from pydantic import BaseModel

class Book(BaseModel):
    id: int
    title: str
    author: str
    pages: int
    in_stock: bool
```

FastAPI automatically:

- Validates input types.
- Converts compatible types (e.g. `"1"` → `int` if possible).
- Returns clear error messages for invalid input.

---

### 3.2 Request vs Response Models

```python
from typing import List, Optional

class BookCreate(BaseModel):
    title: str
    author: str
    pages: int

class BookOut(BaseModel):
    id: int
    title: str
    author: str

@app.post("/books", response_model=BookOut)
def create_book(book: BookCreate):
    new_book = BookOut(id=3, **book.dict())
    return new_book
```

- `BookCreate` used for **input**.
- `BookOut` used for **output** (you can hide internal fields like DB id, timestamps, etc.).

---

### 3.3 Field Constraints & Validation

```python
from pydantic import BaseModel, Field, validator

class BookCreate(BaseModel):
    title: str = Field(..., min_length=1, max_length=100)
    author: str = Field(..., min_length=1)
    pages: int = Field(..., gt=0, lt=10000)
    price: float = Field(..., ge=0)

    @validator("title")
    def title_must_not_be_empty(cls, v):
        if not v.strip():
            raise ValueError("Title cannot be empty or whitespace")
        return v
```

---

### 3.4 Optional Fields & Defaults

```python
from typing import Optional

class BookCreate(BaseModel):
    title: str
    author: str
    description: Optional[str] = None
    in_stock: bool = True
```

---

### 3.5 Nested Models & Inheritance

```python
from typing import List

class Author(BaseModel):
    name: str
    country: str

class BookBase(BaseModel):
    title: str
    pages: int

class BookCreate(BookBase):
    author: Author

class BookOut(BookBase):
    id: int
    author: Author
    tags: List[str] = []
```

**Mini summary:**

- Pydantic models = schema + validation + docs.
- Use different models for **input** and **output**.
- Use `Field` for constraints, `validator` for custom rules.
- Use nested models to model complex JSON.

---

## 4. Automatic API Documentation

### 4.1 Swagger UI & ReDoc

- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

FastAPI generates these from:

- Path operations (`@app.get`, `@router.post`, etc.).
- Pydantic models.
- Docstrings, parameter types.

---

### 4.2 Custom API Metadata

`app/main.py`:

```python
from fastapi import FastAPI

app = FastAPI(
    title="Bookstore API",
    description="A simple API for managing books and users.",
    version="1.0.0",
    contact={
        "name": "Your Name",
        "email": "you@example.com",
    },
    license_info={
        "name": "MIT",
    },
)
```

---

### 4.3 Adding Examples & Response Descriptions

```python
from typing import List
from fastapi import APIRouter
from pydantic import BaseModel, Field

router = APIRouter(prefix="/books", tags=["Books"])

class Book(BaseModel):
    id: int
    title: str = Field(..., example="Clean Code")
    author: str = Field(..., example="Robert C. Martin")
    pages: int = Field(..., example=464)

fake_books_db = [
    Book(id=1, title="Clean Code", author="Robert C. Martin", pages=464)
]

@router.get(
    "/",
    response_model=List[Book],
    summary="List all books",
    description="Returns a list of all books in the store."
)
def list_books():
    return fake_books_db

@router.post(
    "/",
    response_model=Book,
    status_code=201,
    summary="Create a book",
    responses={
        201: {"description": "Book created successfully"},
        400: {"description": "Validation error"},
    },
)
def create_book(book: Book):
    new_id = max(b.id for b in fake_books_db) + 1
    new_book = Book(id=new_id, **book.dict())
    fake_books_db.append(new_book)
    return new_book
```

Mini summary:

- Docs are **automatic**, but you can enhance them.
- Use `summary`, `description`, `responses`, `example`.

---

## 5. Status Codes & Response Handling

### 5.1 Proper HTTP Status Codes

Common ones:

- `200 OK` – successful GET
- `201 Created` – successful resource creation
- `204 No Content` – successful delete without body
- `400 Bad Request` – invalid input
- `401 Unauthorized` – not authenticated
- `403 Forbidden` – not enough permissions
- `404 Not Found`
- `500 Internal Server Error`

Example:

```python
from fastapi import status

@app.post("/books", status_code=status.HTTP_201_CREATED)
def create_book(book: BookCreate):
    ...
    return new_book
```

---

### 5.2 JSONResponse & Custom Responses

```python
from fastapi import FastAPI
from fastapi.responses import JSONResponse, PlainTextResponse

app = FastAPI()

@app.get("/custom-response")
def custom():
    data = {"message": "Custom JSON response"}
    return JSONResponse(content=data, status_code=200)

@app.get("/plaintext")
def plain():
    return PlainTextResponse("Hello, world!", status_code=200)
```

---

### 5.3 Standardized API Response Format

Define a standard wrapper:

```python
from pydantic import BaseModel
from typing import Any, Optional

class APIResponse(BaseModel):
    success: bool
    data: Optional[Any] = None
    error: Optional[str] = None
    message: Optional[str] = None

@app.get("/books", response_model=APIResponse)
def list_books():
    return APIResponse(success=True, data=fake_books_db)

@app.get("/books/{book_id}", response_model=APIResponse)
def get_book(book_id: int):
    book = next((b for b in fake_books_db if b.id == book_id), None)
    if not book:
        return APIResponse(success=False, error="Book not found")
    return APIResponse(success=True, data=book)
```

Mini summary:

- Use appropriate **status codes**.
- Prefer JSON responses; standardize response shape if needed.
- You can use richer `Response` classes (JSONResponse, HTMLResponse, FileResponse, etc.).

---

## 6. Dependency Injection

FastAPI has a powerful DI system via `Depends`.

### 6.1 Concept

- Dependencies are functions that FastAPI calls for you and **injects the result** into your path operation.
- Reusable for:
  - DB sessions
  - Auth/user extraction
  - Rate limiting
  - Common logic (e.g. pagination)

### 6.2 Basic Depends Example

```python
from fastapi import Depends

def common_params(
    skip: int = 0,
    limit: int = 10
):
    return {"skip": skip, "limit": limit}

@app.get("/books")
def list_books(params: dict = Depends(common_params)):
    skip = params["skip"]
    limit = params["limit"]
    return fake_books_db[skip : skip + limit]
```

---

### 6.3 Database Session Dependency (SQLAlchemy + SQLite)

Install:

```bash
pip install sqlalchemy
```

`app/db/session.py`:

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session

SQLALCHEMY_DATABASE_URL = "sqlite:///./books.db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def get_db() -> Session:
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

Usage in route:

```python
from fastapi import Depends
from sqlalchemy.orm import Session
from app.db.session import get_db
from app.models.book import Book as BookModel

@app.get("/books")
def list_books(db: Session = Depends(get_db)):
    return db.query(BookModel).all()
```

---

### 6.4 Security Dependencies

Later in Auth section we’ll use:

```python
from fastapi import Depends, HTTPException, status

def get_current_user(token: str = Depends(oauth2_scheme)):
    # verify token, get user
    user = ...
    if not user:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED)
    return user

@app.get("/me")
def read_me(current_user = Depends(get_current_user)):
    return current_user
```

Mini summary:

- Use `Depends` for **cross-cutting concerns**.
- DB sessions + auth are the most common dependencies.
- `yield`-based dependencies support setup/teardown (opening/closing resources).

---

## 7. Middleware

### 7.1 What is Middleware?

- Code that runs **before and/or after** every request.
- Common uses:
  - Logging
  - CORS
  - Security headers
  - Timing/metrics

---

### 7.2 Adding Custom Middleware

```python
from fastapi import FastAPI, Request
import time

app = FastAPI()

@app.middleware("http")
async def log_requests(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = (time.time() - start_time) * 1000
    print(f"{request.method} {request.url} completed in {process_time:.2f}ms")
    return response
```

---

### 7.3 CORS Configuration

Install:

```bash
pip install "fastapi[standard]"  # already includes CORSMiddleware
```

Use:

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

Mini summary:

- Middleware runs around every request.
- Use it when you need **global behavior** (all routes).
- For per-route logic, use **dependencies** instead.

---

## 8. Authentication & Authorization

We’ll build a basic JWT-based auth flow.

### 8.1 Concepts

- **Authentication**: Who are you?
- **Authorization**: Are you allowed to do X?
- **JWT (JSON Web Token)**:
  - Encodes user data in a signed token.
  - Client sends token in `Authorization: Bearer <token>` header.

---

### 8.2 Install Dependencies

```bash
pip install "python-jose[cryptography]" passlib[bcrypt]
```

---

### 8.3 Basic OAuth2 Password Flow + JWT

`app/core/security.py`:

```python
from datetime import datetime, timedelta
from typing import Optional
from jose import JWTError, jwt
from passlib.context import CryptContext

SECRET_KEY = "super-secret-key-change-in-env"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    expire = datetime.utcnow() + (expires_delta or timedelta(minutes=15))
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
```

`app/api/v1/routes_auth.py`:

```python
from datetime import timedelta
from fastapi import APIRouter, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel
from typing import Optional
from jose import JWTError, jwt
from app.core.security import (
    verify_password,
    get_password_hash,
    create_access_token,
    SECRET_KEY,
    ALGORITHM,
    ACCESS_TOKEN_EXPIRE_MINUTES
)

router = APIRouter(tags=["Auth"])

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="auth/token")

# Fake DB
fake_users_db = {
    "alice@example.com": {
        "email": "alice@example.com",
        "full_name": "Alice",
        "hashed_password": get_password_hash("password123"),
        "is_admin": True,
    }
}

class Token(BaseModel):
    access_token: str
    token_type: str

class User(BaseModel):
    email: str
    full_name: str
    is_admin: bool

class UserInDB(User):
    hashed_password: str

def get_user(email: str) -> Optional[UserInDB]:
    user_dict = fake_users_db.get(email)
    if user_dict:
        return UserInDB(**user_dict)

def authenticate_user(email: str, password: str) -> Optional[UserInDB]:
    user = get_user(email)
    if not user or not verify_password(password, user.hashed_password):
        return None
    return user

@router.post("/auth/token", response_model=Token)
async def login_for_access_token(form_data: OAuth2PasswordRequestForm = Depends()):
    user = authenticate_user(form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED, detail="Incorrect credentials"
        )
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user.email, "is_admin": user.is_admin},
        expires_delta=access_token_expires,
    )
    return {"access_token": access_token, "token_type": "bearer"}

async def get_current_user(token: str = Depends(oauth2_scheme)) -> User:
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED, detail="Could not validate credentials"
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        email: str = payload.get("sub")
        if email is None:
            raise credentials_exception
    except JWTError:
        raise credentials_exception
    user = get_user(email)
    if user is None:
        raise credentials_exception
    return user
```

Protected endpoint:

```python
from fastapi import Depends

@router.get("/me", response_model=User)
async def read_me(current_user: User = Depends(get_current_user)):
    return current_user
```

---

### 8.4 Role-Based Access Control (RBAC)

```python
def require_admin(current_user: User = Depends(get_current_user)):
    if not current_user.is_admin:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="You do not have enough privileges",
        )
    return current_user

@router.delete("/admin/books/{book_id}")
async def admin_delete_book(
    book_id: int,
    admin: User = Depends(require_admin),
):
    # Only admins can delete
    ...
```

Mini summary:

- Use OAuth2 + JWT for stateless auth.
- `Depends` is key for **auth dependencies**.
- Implement **RBAC** by checking roles in dedicated dependency.

---

## 9. Database Integration (SQLAlchemy + SQLite/Postgres)

We’ll show SQLite for local dev; you can switch to Postgres.

### 9.1 Install

```bash
pip install sqlalchemy psycopg2-binary  # psycopg2 for PostgreSQL (optional)
```

---

### 9.2 Database URL

SQLite (dev):

```python
SQLALCHEMY_DATABASE_URL = "sqlite:///./books.db"
```

Postgres (prod):

```python
SQLALCHEMY_DATABASE_URL = "postgresql://user:password@localhost:5432/booksdb"
```

---

### 9.3 SQLAlchemy Models

`app/models/book.py`:

```python
from sqlalchemy import Column, Integer, String, Boolean
from app.db.base import Base

class Book(Base):
    __tablename__ = "books"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True, nullable=False)
    author = Column(String, index=True, nullable=False)
    pages = Column(Integer, nullable=False)
    in_stock = Column(Boolean, default=True)
```

`app/db/base.py`:

```python
from sqlalchemy.orm import declarative_base

Base = declarative_base()
```

Create tables (simple way – later use Alembic):

```python
# app/main.py
from fastapi import FastAPI
from app.db.session import engine
from app.db.base import Base

app = FastAPI()

Base.metadata.create_all(bind=engine)
```

---

### 9.4 CRUD Operations

`app/schemas/book.py`:

```python
from pydantic import BaseModel
from typing import Optional

class BookBase(BaseModel):
    title: str
    author: str
    pages: int
    in_stock: bool = True

class BookCreate(BookBase):
    pass

class BookUpdate(BaseModel):
    title: Optional[str] = None
    author: Optional[str] = None
    pages: Optional[int] = None
    in_stock: Optional[bool] = None

class BookOut(BookBase):
    id: int

    class Config:
        orm_mode = True
```

`app/api/v1/routes_books.py`:

```python
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from typing import List
from app.db.session import get_db
from app.models.book import Book as BookModel
from app.schemas.book import BookCreate, BookUpdate, BookOut

router = APIRouter(prefix="/books", tags=["Books"])

@router.post("/", response_model=BookOut, status_code=status.HTTP_201_CREATED)
def create_book(book_in: BookCreate, db: Session = Depends(get_db)):
    book = BookModel(**book_in.dict())
    db.add(book)
    db.commit()
    db.refresh(book)
    return book

@router.get("/", response_model=List[BookOut])
def list_books(db: Session = Depends(get_db)):
    return db.query(BookModel).all()

@router.get("/{book_id}", response_model=BookOut)
def get_book(book_id: int, db: Session = Depends(get_db)):
    book = db.query(BookModel).filter(BookModel.id == book_id).first()
    if not book:
        raise HTTPException(status_code=404, detail="Book not found")
    return book

@router.put("/{book_id}", response_model=BookOut)
def update_book(book_id: int, book_in: BookUpdate, db: Session = Depends(get_db)):
    book = db.query(BookModel).filter(BookModel.id == book_id).first()
    if not book:
        raise HTTPException(status_code=404, detail="Book not found")
    for field, value in book_in.dict(exclude_unset=True).items():
        setattr(book, field, value)
    db.commit()
    db.refresh(book)
    return book

@router.delete("/{book_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_book(book_id: int, db: Session = Depends(get_db)):
    book = db.query(BookModel).filter(BookModel.id == book_id).first()
    if not book:
        raise HTTPException(status_code=404, detail="Book not found")
    db.delete(book)
    db.commit()
    return None
```

---

### 9.5 Alembic (Migrations) – Overview

Quick idea (not full setup):

1. Install:

   ```bash
   pip install alembic
   alembic init migrations
   ```

2. Configure DB URL in `alembic.ini`.
3. Configure models in `env.py`.
4. Generate migration:

   ```bash
   alembic revision --autogenerate -m "create books table"
   alembic upgrade head
   ```

Mini summary:

- Use SQLAlchemy models for DB tables.
- Use Pydantic schemas for API I/O.
- Use dependencies for DB sessions.
- Use Alembic for **schema migrations** as project grows.

---

## 10. Background Tasks

You already saw a dedicated guide; here’s a concise version.

### 10.1 Concept

- Tasks that run **after** response is sent.
- Good for:
  - Emails
  - Logging
  - Non-critical side effects

---

### 10.2 Implementation

```python
from fastapi import BackgroundTasks

def send_welcome_email(email: str):
    print(f"Sending welcome email to {email}")

@app.post("/register")
def register(email: str, background_tasks: BackgroundTasks):
    # Save user to DB here
    background_tasks.add_task(send_welcome_email, email)
    return {"message": "User registered. Email will be sent."}
```

---

### 10.3 When to Use Celery Instead

Use `BackgroundTasks` when tasks are:

- Short-lived
- Low volume
- Non-critical

Use **Celery** when:

- High volume
- Long-running
- Need retries, scheduling, status tracking

Mini summary:

- Simple background side-effects → `BackgroundTasks`.
- Heavy/critical tasks → Celery or other task queue.

---

## 11. File Handling

### 11.1 Uploading Files

```python
from fastapi import File, UploadFile

@app.post("/upload")
async def upload_file(file: UploadFile = File(...)):
    content = await file.read()
    with open(f"uploads/{file.filename}", "wb") as f:
        f.write(content)
    return {"filename": file.filename, "size": len(content)}
```

---

### 11.2 Handling Images & Documents

Use `Pillow`, `PyPDF2`, etc., as needed.

```python
from fastapi import UploadFile, File

@app.post("/upload/profile-picture")
async def upload_profile_picture(file: UploadFile = File(...)):
    if not file.content_type.startswith("image/"):
        raise HTTPException(status_code=400, detail="Invalid image type")
    # Save & process
```

---

### 11.3 Returning File Responses

```python
from fastapi.responses import FileResponse

@app.get("/download/{filename}")
def download_file(filename: str):
    file_path = f"uploads/{filename}"
    return FileResponse(path=file_path, filename=filename)
```

Mini summary:

- Use `UploadFile` for efficient file uploads.
- Validate type & size.
- Use `FileResponse` for downloads.

---

## 12. Async Programming

### 12.1 `async` / `await` Fundamentals

- `async def` functions are **coroutines**.
- `await` is used to pause execution until async call completes.
- Benefit: server can handle other requests while waiting for I/O.

---

### 12.2 Async Endpoints in FastAPI

```python
import httpx  # async HTTP client

@app.get("/external")
async def call_external():
    async with httpx.AsyncClient() as client:
        resp = await client.get("https://httpbin.org/get")
        return resp.json()
```

---

### 12.3 Blocking vs Non-blocking

- **Blocking**: `time.sleep(5)` → freezes thread.
- **Non-blocking**: `await asyncio.sleep(5)` → other requests can run.

Mini summary:

- Prefer `async` endpoints when doing external I/O (HTTP calls, DB with async drivers).
- Don’t mix heavy CPU-bound logic in async endpoints (use separate workers).

---

## 13. Error Handling & Validation

### 13.1 HTTPException

```python
from fastapi import HTTPException, status

@app.get("/books/{book_id}")
def get_book(book_id: int):
    book = ...
    if not book:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Book not found"
        )
    return book
```

---

### 13.2 Custom Exception Handlers

```python
from fastapi import Request
from fastapi.responses import JSONResponse

class BookstoreException(Exception):
    def __init__(self, name: str):
        self.name = name

@app.exception_handler(BookstoreException)
async def bookstore_exception_handler(request: Request, exc: BookstoreException):
    return JSONResponse(
        status_code=418,
        content={"message": f"Oops, error with {exc.name}"},
    )
```

Raise it:

```python
@app.get("/broken")
def broken():
    raise BookstoreException(name="test")
```

---

### 13.3 Validation Errors

Pydantic & FastAPI automatically return `422 Unprocessable Entity` with error details.

Mini summary:

- Use `HTTPException` for client-visible errors.
- Create custom exceptions + handlers for reusable patterns.

---

## 14. API Security Best Practices

### 14.1 Securing Endpoints

- Use JWT auth & RBAC.
- Disable/lock down critical admin routes.

### 14.2 Rate Limiting (Conceptual)

- Protect against abuse (DDOS, brute force).
- Implement via:
  - API gateway (e.g., Nginx, Kong)
  - Third-party tools
  - Middleware + Redis (custom)

### 14.3 Input Validation & SQL Injection

- Use **SQLAlchemy ORM** or parameterized queries.
- Never concatenate raw user input into SQL.

Bad:

```python
query = f"SELECT * FROM books WHERE title = '{title}'"
```

Good:

```python
db.query(Book).filter(Book.title == title)
```

### 14.4 Environment Variables

Use `python-dotenv` or settings library:

```bash
pip install python-dotenv
```

`.env`:

```env
DATABASE_URL=postgresql://user:pass@localhost:5432/booksdb
SECRET_KEY=super-secret
```

Config:

```python
from pydantic import BaseSettings

class Settings(BaseSettings):
    database_url: str
    secret_key: str

    class Config:
        env_file = ".env"

settings = Settings()
```

---

### 14.5 HTTPS Basics

- Always use HTTPS in production.
- Terminate TLS at:
  - Reverse proxy (Nginx, Traefik)
  - Cloud load balancer

Mini summary:

- Validate all input.
- Don’t expose secrets.
- Use HTTPS + proper auth.
- Avoid raw SQL where possible.

---

## 15. Testing FastAPI Applications

### 15.1 Setup

```bash
pip install pytest httpx
```

### 15.2 TestClient

```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_read_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Welcome to FastAPI Bookstore!"}
```

Run:

```bash
pytest
```

---

### 15.3 Testing Endpoints with DB

Use **test DB** (e.g., SQLite in-memory) and override `get_db` dependency.

Mini summary:

- Use `TestClient` (sync) or `AsyncClient` (httpx) for testing.
- Override dependencies (`Depends`) in tests for isolation.

---

## 16. Performance & Optimization

### 16.1 Bottlenecks

- Blocking I/O (file, network)
- Heavy CPU tasks
- Inefficient DB queries (`N+1` query problem)

### 16.2 Tips

- Use `async` and async drivers.
- Use indexes in DB.
- Limit response size (pagination).
- Cache heavy computations (Redis, in-memory).

Mini summary:

- Measure first (profiling, logs).
- Optimize DB & I/O before micro-optimizations.

---

## 17. Deployment Basics

### 17.1 Uvicorn vs Gunicorn

- **Uvicorn**: ASGI server.
- **Gunicorn**: process manager; runs Uvicorn workers.

Production example:

```bash
pip install gunicorn uvicorn
gunicorn -k uvicorn.workers.UvicornWorker app.main:app --workers 4 --bind 0.0.0.0:8000
```

---

### 17.2 Dockerizing FastAPI

`Dockerfile` (simple):

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Build & run:

```bash
docker build -t fastapi-bookstore .
docker run -p 8000:8000 fastapi-bookstore
```

Mini summary:

- For local: `uvicorn main:app --reload`.
- For prod: Gunicorn + Uvicorn workers, behind Nginx or load balancer.
- Docker is the standard for packaging.

---

## 18. Logging & Monitoring

### 18.1 Logging

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

@app.get("/books")
def list_books():
    logger.info("Listing books")
    return [...]
```

### 18.2 Health Check Endpoint

```python
@app.get("/health")
def health():
    return {"status": "ok"}
```

Monitoring tools:

- Prometheus + Grafana
- Cloud provider metrics
- APM: New Relic, Datadog, etc.

Mini summary:

- Log structured data (JSON) where possible.
- Expose `/health` or `/healthz` for uptime checks.

---

## 19. Microservice Architecture

### 19.1 Monolith vs Microservices

- **Monolith**:
  - All features in one codebase & deploy.
- **Microservices**:
  - Multiple small services (e.g., `users`, `orders`, `books`), each with own DB, deployed independently.

---

### 19.2 Designing FastAPI Microservices

- Each service:
  - Own FastAPI app.
  - Own DB.
  - Clear API boundary (versioned routes).
- Communication:
  - HTTP/REST
  - gRPC (if needed)
  - Event-driven (Kafka, RabbitMQ)

---

### 19.3 Service-to-Service Auth

- Use:
  - Signed JWTs between services.
  - API gateway.
  - mTLS (mutual TLS) for strict security.

Mini summary:

- Start monolith; break into microservices when needed.
- FastAPI is a good fit for small, focused services.

---

## Putting It All Together: From Setup to Run

1. **Create project & venv**  
   `mkdir fastapi-bookstore && cd fastapi-bookstore`  
   `python -m venv venv && .\venv\Scripts\activate`

2. **Install dependencies**  
   `pip install fastapi uvicorn sqlalchemy "python-jose[cryptography]" passlib[bcrypt] python-dotenv`

3. **Create app structure** (as shown earlier).

4. **Implement**:
   - Core routes (books) with Pydantic & SQLAlchemy.
   - Auth routes with JWT.
   - Middleware, background tasks, file uploads as needed.

5. **Run locally**:  
   `uvicorn app.main:app --reload`

6. **Open docs**:  
   `http://127.0.0.1:8000/docs`

7. **Add tests** (`pytest`), improve security & performance as you grow.

---

If you’d like, next I can:

- Design a **complete mini project** (e.g. “Bookstore with users, JWT auth, and orders”) and give you **full code layout** step-by-step, or
- Focus on one chapter (e.g. Auth + JWT) with a deeper, end-to-end implementation.

## Ways to implement Rate Limiting in FastAPI:
Rate limiting in FastAPI is typically implemented using third-party libraries like fastapi-limiter (recommended for scalable, production use with Redis) or slowapi (simpler, in-memory option for single-server deployments). 
Full Stack Data Science
Full Stack Data Science
 +2
Option 1: Using fastapi-limiter (Scalable, Production-Ready) 
This library uses Redis as a backend to store rate limit data, which is essential for horizontally scaled (multiple instance) deployments. 
Full Stack Data Science
Full Stack Data Science
Prerequisites: A running Redis server. You can run one using Docker: 
bash
docker run --name redis-server -p 6379:6379 -d redis
Installation:
bash
pip install fastapi-limiter pyrate-limiter "aioredis<2"
Implementation Steps:
Initialize FastAPI and Redis: Use a lifespan event handler to connect and disconnect the Redis client.
Add RateLimiterMiddleware: Apply rate limiting globally or use Depends(RateLimiter(...)) for specific routes. 
Medium
Medium
 +3
Example Code (main.py):
python
import uvicorn
from fastapi import FastAPI, Depends, Request, HTTPException
from fastapi_limiter import FastAPILimiter
from fastapi_limiter.depends import RateLimiter
from pyrate_limiter import Duration, Limiter, Rate
from redis import asyncio as aioredis # Use the async Redis client

app = FastAPI()

# 1. Define a lifespan event to manage the Redis connection
@app.on_event("startup")
async def startup():
    redis = aioredis.from_url("redis://localhost:6379", encoding="utf8", decode_responses=True)
    await FastAPILimiter.init(redis)

@app.on_event("shutdown")
async def shutdown():
    await FastAPILimiter.close()

# 2. Apply rate limiting as a dependency
@app.get(
    "/limited-route",
    dependencies=[Depends(RateLimiter(limiter=Limiter(Rate(2, Duration.SECOND * 5))))], # 2 requests per 5 seconds
)
async def limited_endpoint():
    return {"msg": "This route has a limit of 2 requests per 5 seconds."}

# You can also use middleware for global limits (see documentation on GitHub for details)
# app.add_middleware(
#     RateLimiterMiddleware,
#     limiter=Limiter(Rate(5, Duration.MINUTE)),
# )

if __name__ == "__main__":
    uvicorn.run("main:app", reload=True)
Option 2: Using slowapi (Simple, In-Memory) 
slowapi is easier to set up as it doesn't require an external service like Redis, making it suitable for single-server setups. 
Medium
Medium
 +3
Installation:
bash
pip install slowapi
Example Code (main.py):
python
import uvicorn
from fastapi import FastAPI, Request
from slowapi import Limiter, _rate_limit_exceeded_handler
from slowapi.errors import RateLimitExceeded
from slowapi.util import get_remote_address

# Initialize limiter, tracking by client IP

limiter = Limiter(key_func=get_remote_address, default_limits=["5/minute"]) # Default 5 requests per minute
app = FastAPI()
app.state.limiter = limiter
app.add_exception_handler(RateLimitExceeded, _rate_limit_exceeded_handler)

@app.get("/limited-route")
@limiter.limit("2/5second") # Apply specific limit (2 requests per 5 seconds)
async def limited_endpoint(request: Request): # Request object must be present
    return {"message": "This endpoint has a rate limit."}

@app.get("/unlimited")
async def unlimited_endpoint():
    return {"message": "This endpoint has no rate limit."}

if __name__ == "__main__":
    uvicorn.run("main:app", reload=True)

Best Practices
Provide Headers: Always return X-RateLimit-Limit, X-RateLimit-Remaining, and Retry-After headers so clients know how to adapt their request rates.
Identify Clients Correctly: Limit requests by User ID (if authenticated) or API key, rather than just IP address, which can be shared among many users (e.g., behind a NAT).
Use Middleware for Global Limits: Add rate limiting as a middleware to apply a default limit across all routes.
Handle 429 Too Many Requests: The libraries automatically raise an HTTP 429 error when the limit is exceeded. Make sure clients understand this response.


I’ll extend your existing FastAPI notes with:

- DB integration examples for **MySQL** and **MongoDB**
- **Complete rate limiting implementations** (from simple to production-style)
- A **capstone project** definition that touches almost every topic, with example outputs

---

## 1. Database Integration: MySQL (SQLAlchemy)

### 1.1 Install Dependencies

```bash
pip install sqlalchemy pymysql
```

> `pymysql` is a pure-Python MySQL driver used by SQLAlchemy.

### 1.2 Configure Database URL & Session

```python
# db_mysql.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session

MYSQL_USER = "root"
MYSQL_PASSWORD = "password"
MYSQL_HOST = "localhost"
MYSQL_PORT = "3306"
MYSQL_DB = "bookstore"

SQLALCHEMY_DATABASE_URL = (
    f"mysql+pymysql://{MYSQL_USER}:{MYSQL_PASSWORD}@{MYSQL_HOST}:{MYSQL_PORT}/{MYSQL_DB}"
)

engine = create_engine(
    SQLALCHEMY_DATABASE_URL,
    pool_pre_ping=True,  # detect stale connections
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def get_db() -> Session:
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

### 1.3 Base & Models

```python
# db_base.py
from sqlalchemy.orm import declarative_base

Base = declarative_base()
```

```python
# models_mysql.py
from sqlalchemy import Column, Integer, String, Boolean
from db_base import Base

class Book(Base):
    __tablename__ = "books"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(200), nullable=False, index=True)
    author = Column(String(100), nullable=False, index=True)
    pages = Column(Integer, nullable=False)
    in_stock = Column(Boolean, default=True)
```

### 1.4 Pydantic Schemas

```python
# schemas_mysql.py
from pydantic import BaseModel
from typing import Optional

class BookBase(BaseModel):
    title: str
    author: str
    pages: int
    in_stock: bool = True

class BookCreate(BookBase):
    pass

class BookUpdate(BaseModel):
    title: Optional[str] = None
    author: Optional[str] = None
    pages: Optional[int] = None
    in_stock: Optional[bool] = None

class BookOut(BookBase):
    id: int

    class Config:
        orm_mode = True
```

### 1.5 CRUD Routes (MySQL-Backed)

```python
# routes_books_mysql.py
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from typing import List

from db_mysql import get_db, engine
from db_base import Base
from models_mysql import Book
from schemas_mysql import BookCreate, BookUpdate, BookOut

router = APIRouter(prefix="/mysql/books", tags=["MySQL Books"])

# Create tables once (or via Alembic in real projects)
Base.metadata.create_all(bind=engine)

@router.post("/", response_model=BookOut, status_code=status.HTTP_201_CREATED)
def create_book(book_in: BookCreate, db: Session = Depends(get_db)):
    book = Book(**book_in.dict())
    db.add(book)
    db.commit()
    db.refresh(book)
    return book

@router.get("/", response_model=List[BookOut])
def list_books(db: Session = Depends(get_db)):
    return db.query(Book).all()

@router.get("/{book_id}", response_model=BookOut)
def get_book(book_id: int, db: Session = Depends(get_db)):
    book = db.query(Book).filter(Book.id == book_id).first()
    if not book:
        raise HTTPException(status_code=404, detail="Book not found")
    return book

@router.put("/{book_id}", response_model=BookOut)
def update_book(book_id: int, book_in: BookUpdate, db: Session = Depends(get_db)):
    book = db.query(Book).filter(Book.id == book_id).first()
    if not book:
        raise HTTPException(status_code=404, detail="Book not found")
    for field, value in book_in.dict(exclude_unset=True).items():
        setattr(book, field, value)
    db.commit()
    db.refresh(book)
    return book

@router.delete("/{book_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_book(book_id: int, db: Session = Depends(get_db)):
    book = db.query(Book).filter(Book.id == book_id).first()
    if not book:
        raise HTTPException(status_code=404, detail="Book not found")
    db.delete(book)
    db.commit()
    return None
```

### 1.6 Wire Into FastAPI App

```python
# main_mysql.py
from fastapi import FastAPI
from routes_books_mysql import router as mysql_books_router

app = FastAPI(title="Bookstore with MySQL")
app.include_router(mysql_books_router)
```

Run:

```bash
uvicorn main_mysql:app --reload
```

---

## 2. Database Integration: MongoDB (Motor – Async)

MongoDB is document-based and fits well with async.

### 2.1 Install Dependencies

```bash
pip install motor "pydantic[email]"
```

### 2.2 Basic MongoDB Setup

```python
# db_mongo.py
from motor.motor_asyncio import AsyncIOMotorClient
from pydantic import BaseSettings

class Settings(BaseSettings):
    mongodb_url: str = "mongodb://localhost:27017"
    mongodb_db_name: str = "bookstore"

    class Config:
        env_file = ".env"

settings = Settings()

client: AsyncIOMotorClient = AsyncIOMotorClient(settings.mongodb_url)
db = client[settings.mongodb_db_name]

async def get_mongo_db():
    return db
```

### 2.3 Schemas (Pydantic) + Helpers

MongoDB uses `_id` (ObjectId). For simple learning, we’ll store string IDs.

```python
# schemas_mongo.py
from pydantic import BaseModel, Field
from typing import Optional

class BookBase(BaseModel):
    title: str
    author: str
    pages: int
    in_stock: bool = True

class BookCreate(BookBase):
    pass

class BookInDB(BookBase):
    id: str = Field(..., alias="_id")

    class Config:
        allow_population_by_field_name = True
        orm_mode = True
```

### 2.4 CRUD Routes (Mongo-Backed)

```python
# routes_books_mongo.py
from fastapi import APIRouter, Depends, HTTPException, status
from typing import List
from bson import ObjectId

from db_mongo import get_mongo_db
from schemas_mongo import BookCreate, BookInDB

router = APIRouter(prefix="/mongo/books", tags=["MongoDB Books"])

def doc_to_book(doc) -> BookInDB:
    doc["_id"] = str(doc["_id"])
    return BookInDB(**doc)

@router.post("/", response_model=BookInDB, status_code=status.HTTP_201_CREATED)
async def create_book(book_in: BookCreate, db = Depends(get_mongo_db)):
    book_dict = book_in.dict()
    result = await db.books.insert_one(book_dict)
    created = await db.books.find_one({"_id": result.inserted_id})
    return doc_to_book(created)

@router.get("/", response_model=List[BookInDB])
async def list_books(db = Depends(get_mongo_db)):
    books = []
    cursor = db.books.find({})
    async for doc in cursor:
        books.append(doc_to_book(doc))
    return books

@router.get("/{book_id}", response_model=BookInDB)
async def get_book(book_id: str, db = Depends(get_mongo_db)):
    doc = await db.books.find_one({"_id": ObjectId(book_id)})
    if not doc:
        raise HTTPException(status_code=404, detail="Book not found")
    return doc_to_book(doc)

@router.delete("/{book_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_book(book_id: str, db = Depends(get_mongo_db)):
    result = await db.books.delete_one({"_id": ObjectId(book_id)})
    if result.deleted_count == 0:
        raise HTTPException(status_code=404, detail="Book not found")
    return None
```

### 2.5 Wire Into FastAPI App

```python
# main_mongo.py
from fastapi import FastAPI
from routes_books_mongo import router as mongo_books_router

app = FastAPI(title="Bookstore with MongoDB")
app.include_router(mongo_books_router)
```

Run:

```bash
uvicorn main_mongo:app --reload
```

---

## 3. Rate Limiting in FastAPI – Complete Guide

### 3.1 Approaches Overview

1. **Simple in-memory rate limiting** (single instance, not production-proof).
2. **Redis-backed rate limiting using a library** (scalable, closer to production).
3. (Advanced) API gateway / reverse proxy (Nginx, Cloudflare) – conceptual.

We’ll implement (1) and (2) with full code.

---

### 3.2 Approach 1: Simple In-Memory Rate Limiting (Per IP)

Good for:

- Learning
- Small, single-instance dev environments

#### 3.2.1 Concept

- Track requests per **IP + endpoint** within a rolling time window (e.g., 60 seconds).
- If count exceeds limit, return `429 Too Many Requests`.

#### 3.2.2 Implementation

```python
# rate_limit_simple.py
import time
from typing import Dict, Tuple
from fastapi import FastAPI, Request, HTTPException, status

app = FastAPI(title="Rate Limiting Demo")

# key: (client_ip, route), value: list[timestamps]
REQUEST_LOG: Dict[Tuple[str, str], list[float]] = {}

RATE_LIMIT = 5          # requests
WINDOW_SECONDS = 60.0   # per 60 seconds

def is_rate_limited(client_ip: str, route: str) -> bool:
    now = time.time()
    key = (client_ip, route)

    timestamps = REQUEST_LOG.get(key, [])
    # remove timestamps outside window
    timestamps = [t for t in timestamps if now - t <= WINDOW_SECONDS]
    if len(timestamps) >= RATE_LIMIT:
        REQUEST_LOG[key] = timestamps  # save cleaned
        return True

    timestamps.append(now)
    REQUEST_LOG[key] = timestamps
    return False

@app.middleware("http")
async def rate_limit_middleware(request: Request, call_next):
    client_ip = request.client.host
    route = request.url.path

    if is_rate_limited(client_ip, route):
        # basic 429 response
        raise HTTPException(
            status_code=status.HTTP_429_TOO_MANY_REQUESTS,
            detail="Too many requests. Please try again later.",
        )

    response = await call_next(request)
    return response

@app.get("/limited")
async def limited_endpoint():
    return {"message": "This endpoint is rate-limited: 5 req/min per IP"}

@app.get("/unlimited")
async def unlimited_endpoint():
    return {"message": "This endpoint is NOT rate-limited"}
```

Test:

- Call `/limited` 6+ times quickly → 429.
- `/unlimited` remains open.

> Limitations: memory-only, per-process, not suitable for multi-instance deployments.

---

### 3.3 Approach 2: Redis-Based Rate Limiting with fastapi-limiter

Good for:

- Realistic, production-like environments
- Multiple app instances
- Centralized rate limits

#### 3.3.1 Install & Run Redis

Install:

```bash
pip install fastapi-limiter aioredis
```

You need a Redis server running (locally or in Docker):

```bash
docker run -d --name redis -p 6379:6379 redis
```

#### 3.3.2 Initialize Limiter

```python
# rate_limit_redis.py
from fastapi import FastAPI, Depends
from fastapi_limiter import FastAPILimiter, RateLimiter
import aioredis
from starlette.requests import Request

app = FastAPI(title="Redis Rate Limiting Demo")

@app.on_event("startup")
async def startup():
    redis = aioredis.from_url("redis://localhost", encoding="utf-8", decode_responses=True)
    await FastAPILimiter.init(redis)
```

#### 3.3.3 Rate-Limited Endpoints

```python
@app.get(
    "/public",
    dependencies=[Depends(RateLimiter(times=5, seconds=60))]
)
async def public_endpoint():
    return {"message": "Max 5 requests per 60 seconds from one IP"}

@app.get(
    "/search",
    dependencies=[Depends(RateLimiter(times=20, seconds=60))]
)
async def search_endpoint(q: str):
    return {"query": q}

# Different limit per-user (if you have auth)
from fastapi import HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

async def get_current_user(token: str = Depends(oauth2_scheme)):
    # decode token, return user with .id
    # for demo, pretend user_id is token itself
    if not token:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED)
    class User:  # simple object
        id = token
    return User()

@app.get(
    "/user-action",
    dependencies=[Depends(RateLimiter(times=10, seconds=60, key_func=lambda req: req.state.user_id))]
)
async def user_action(request: Request, user = Depends(get_current_user)):
    # attach user id to request state so key_func can read it
    request.state.user_id = user.id
    return {"message": f"User {user.id} can call this 10 times per minute"}
```

Notes:

- `FastAPILimiter.init(redis)` wires rate limiting to Redis.
- `RateLimiter(times=X, seconds=Y)` sets **X** requests per **Y** seconds.
- Default key is client IP; you can customize `key_func`.

#### 3.3.4 Running

```bash
uvicorn rate_limit_redis:app --reload
```

Then:

- Hit `/public` more than 5 times in 60 seconds → 429 from Redis-based limiter.

---

### 3.4 Conceptual: Rate Limiting via API Gateway / Reverse Proxy

Real-world options:

- Nginx: `limit_req_zone` + `limit_req` directives.
- Cloud providers: API Gateway (AWS), Azure API Management, Cloudflare.

Benefit:

- Offload rate limiting from the app code.
- Centralized config for multiple services.

---

## 4. Capstone Project Suggestion (Using All Topics)

### 4.1 Project: “Online Learning Platform API” (LMS)

**Goal:** Build a FastAPI backend for an online course platform (like a simplified Udemy) that exercises:

- Routing, Pydantic, docs
- Auth + JWT + RBAC
- DB (MySQL and/or MongoDB)
- Background tasks
- File uploads
- Rate limiting
- Async I/O
- Error handling, testing, deployment basics, etc.

### 4.2 Core Features

1. **User Management**
   - Register/login with email & password.
   - Roles: `student`, `instructor`, `admin`.
   - JWT-based auth.
   - RBAC:
     - Students: view/enroll in courses.
     - Instructors: create/update own courses.
     - Admin: manage users & courses.

2. **Courses & Lessons**
   - CRUD for courses (title, description, price, category).
   - Each course has modules/lessons (video, text).
   - Store:
     - Course metadata in MySQL/Postgres.
     - Optional lesson content or tracking in MongoDB.

3. **Enrollments & Progress Tracking**
   - Students enroll in courses.
   - Track which lessons are completed (MongoDB collection).
   - Provide student dashboard.

4. **File Handling**
   - Upload course thumbnails (images).
   - Upload lesson attachments (PDFs).
   - Return file URLs and file responses.

5. **Background Tasks**
   - Send welcome email on registration.
   - Send “new course published” notification to followers.
   - Log analytics (course views, enrollments) asynchronously.

6. **Rate Limiting**
   - Limit:
     - `/auth/login` → 5 attempts per 5 minutes per IP.
     - `/courses/search` → 30 requests per minute per IP.

7. **Security**
   - Input validation with Pydantic.
   - Avoid SQL injection via ORM.
   - Secrets via env vars.
   - HTTPS assumed in production.

8. **Testing**
   - pytest tests for:
     - `/auth/register`
     - `/auth/login`
     - `/courses` CRUD
     - Permission checks for roles.

9. **Deployment**
   - Dockerfile.
   - Uvicorn with Gunicorn.
   - Simple `/health` endpoint.

---

### 4.3 Example API Flows & Expected Outputs

#### 4.3.1 Register User

Request:

```http
POST /auth/register
Content-Type: application/json

{
  "email": "alice@example.com",
  "password": "Password123!",
  "full_name": "Alice",
  "role": "student"
}
```

Response (201):

```json
{
  "id": 1,
  "email": "alice@example.com",
  "full_name": "Alice",
  "role": "student"
}
```

Background task:

- Sends “Welcome to LMS” email.

---

#### 4.3.2 Login

```http
POST /auth/token
Content-Type: application/x-www-form-urlencoded

username=alice@example.com&password=Password123!
```

Response (200):

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer"
}
```

Rate limit:

- If more than 5 login attempts in 5 minutes → 429 “Too many login attempts”.

---

#### 4.3.3 Create Course (Instructor Only)

Request:

```http
POST /courses
Authorization: Bearer <JWT>
Content-Type: application/json

{
  "title": "FastAPI for Beginners",
  "description": "Learn FastAPI from scratch.",
  "price": 49.99,
  "category": "Backend"
}
```

Response (201):

```json
{
  "id": 101,
  "title": "FastAPI for Beginners",
  "description": "Learn FastAPI from scratch.",
  "price": 49.99,
  "category": "Backend",
  "instructor_id": 2
}
```

---

#### 4.3.4 Enroll in Course (Student)

```http
POST /courses/101/enroll
Authorization: Bearer <student-jwt>
```

Response:

```json
{
  "course_id": 101,
  "student_id": 1,
  "status": "enrolled"
}
```

Background tasks:

- Send “You enrolled in FastAPI for Beginners!” email.
- Log analytics entry in MongoDB.

---

#### 4.3.5 Upload Course Thumbnail

```http
POST /courses/101/thumbnail
Authorization: Bearer <instructor-jwt>
Content-Type: multipart/form-data

file: (binary image)
```

Response:

```json
{
  "course_id": 101,
  "thumbnail_url": "/files/courses/101/thumbnail.jpg"
}
```

---

#### 4.3.6 Get Course Details

```http
GET /courses/101
Authorization: Bearer <any-jwt>
```

Response:

```json
{
  "id": 101,
  "title": "FastAPI for Beginners",
  "description": "Learn FastAPI from scratch.",
  "price": 49.99,
  "category": "Backend",
  "instructor": {
    "id": 2,
    "full_name": "Bob Instructor"
  },
  "thumbnail_url": "/files/courses/101/thumbnail.jpg"
}
```

---

### 4.4 How This Project Uses Your Topics

- **Routing & HTTP Methods** – CRUD on users, courses, enrollments.
- **Pydantic Models** – request/response models, validation.
- **Docs** – auto docs + metadata, examples for critical endpoints.
- **Status Codes & Responses** – 201 for creation, 401/403/404, standardized response envelopes.
- **Dependency Injection** – DB session, current user, role checks.
- **Middleware** – logging, CORS, (optional) simple auth checks.
- **Auth & AuthZ** – JWT, roles, RBAC for routes.
- **Database Integration** – MySQL (relational) + MongoDB (analytics/progress).
- **Background Tasks** – emails, analytics logging.
- **File Handling** – thumbnail & attachment uploads.
- **Async Programming** – async DB calls (Mongo), external APIs (e.g., sending emails).
- **Error Handling** – custom exceptions for business logic (e.g., duplicate enrollment).
- **Security Best Practices** – env vars, validated input, HTTPS assumption.
- **Testing** – pytest suites for main flows.
- **Performance & Optimization** – pagination, async I/O, indexes.
- **Deployment** – Docker + Uvicorn/Gunicorn.
- **Logging & Monitoring** – structured logs and `/health`.
- **Microservices** – can later split `auth`, `courses`, `analytics` into separate services.

---

If you tell me which stack you want to prioritize (MySQL-only, Mongo-only, or both), I can next give you a **concrete folder structure and starter code** for this LMS project so you can actually build it step by step.
