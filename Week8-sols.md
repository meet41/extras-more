Below are **3 separate FastAPI solutions** (each self-contained) using **MongoDB** as the database (Motor async driver).  
Note: your prompt mentions “SQLAlchemy” in Q1, but SQLAlchemy is for SQL databases; for MongoDB we use **Motor** + dependency injection instead.

---

## Question 1) Secure Product Management API (FastAPI + MongoDB + JWT, admin-only delete) + Tests

### Project structure (suggested)
- `q1/main.py`
- `q1/db.py`
- `q1/models.py`
- `q1/auth.py`
- `q1/test_main.py`

### `q1/db.py`
```python name=q1/db.py
import os
from motor.motor_asyncio import AsyncIOMotorClient

MONGO_URL = os.getenv("MONGO_URL", "mongodb://localhost:27017")
DB_NAME = os.getenv("MONGO_DB", "product_db")

_client: AsyncIOMotorClient | None = None

def get_client() -> AsyncIOMotorClient:
    global _client
    if _client is None:
        _client = AsyncIOMotorClient(MONGO_URL)
    return _client

async def get_db():
    client = get_client()
    db = client[DB_NAME]
    yield db
```

### `q1/models.py`
```python name=q1/models.py
from datetime import datetime, timezone
from typing import Optional
from pydantic import BaseModel, Field, ConfigDict

class ProductCreate(BaseModel):
    name: str = Field(..., min_length=3)
    description: Optional[str] = None
    price: float = Field(..., gt=0)
    stock: int = Field(..., ge=0)

class ProductUpdate(BaseModel):
    name: Optional[str] = Field(None, min_length=3)
    description: Optional[str] = None
    price: Optional[float] = Field(None, gt=0)
    stock: Optional[int] = Field(None, ge=0)

class ProductOut(BaseModel):
    model_config = ConfigDict(from_attributes=True)

    id: str
    name: str
    description: Optional[str] = None
    price: float
    stock: int
    created_at: datetime

def now_utc() -> datetime:
    return datetime.now(timezone.utc)
```

### `q1/auth.py` (simple JWT auth with admin/user roles)
```python name=q1/auth.py
import os
from datetime import datetime, timedelta, timezone
from typing import Literal, Optional

import jwt
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

SECRET_KEY = os.getenv("SECRET_KEY", "dev_only_change_me")
ALGORITHM = os.getenv("JWT_ALG", "HS256")
ACCESS_TOKEN_EXPIRE_MINUTES = int(os.getenv("ACCESS_TOKEN_EXPIRE_MINUTES", "30"))

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/auth/token")

class TokenData(BaseModel):
    sub: str
    role: Literal["admin", "user"]

def create_access_token(*, subject: str, role: Literal["admin","user"]) -> str:
    expire = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    payload = {"sub": subject, "role": role, "exp": expire}
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)

def decode_token(token: str) -> TokenData:
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return TokenData(sub=payload["sub"], role=payload["role"])
    except jwt.ExpiredSignatureError:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Token expired")
    except Exception:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid token")

def get_current_user(token: str = Depends(oauth2_scheme)) -> TokenData:
    return decode_token(token)

def require_admin(user: TokenData = Depends(get_current_user)) -> TokenData:
    if user.role != "admin":
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN, detail="Admin required")
    return user
```

### `q1/main.py`
```python name=q1/main.py
from bson import ObjectId
from fastapi import FastAPI, Depends, HTTPException, status, Query
from fastapi.security import OAuth2PasswordRequestForm

from .db import get_db
from .models import ProductCreate, ProductUpdate, ProductOut, now_utc
from .auth import create_access_token, get_current_user, require_admin

app = FastAPI(title="Q1 - Product Management API")

# --- auth endpoint (demo): username "admin" => admin role; anything else => user role
@app.post("/auth/token")
async def token(form: OAuth2PasswordRequestForm = Depends()):
    role = "admin" if form.username == "admin" else "user"
    access_token = create_access_token(subject=form.username, role=role)
    return {"access_token": access_token, "token_type": "bearer"}

def _doc_to_out(doc) -> ProductOut:
    return ProductOut(
        id=str(doc["_id"]),
        name=doc["name"],
        description=doc.get("description"),
        price=doc["price"],
        stock=doc["stock"],
        created_at=doc["created_at"],
    )

@app.post("/products/", response_model=ProductOut, status_code=status.HTTP_201_CREATED)
async def create_product(payload: ProductCreate, db=Depends(get_db), user=Depends(get_current_user)):
    doc = payload.model_dump()
    doc["created_at"] = now_utc()
    res = await db.products.insert_one(doc)
    created = await db.products.find_one({"_id": res.inserted_id})
    return _doc_to_out(created)

@app.get("/products/{product_id}", response_model=ProductOut)
async def get_product(product_id: str, db=Depends(get_db), user=Depends(get_current_user)):
    if not ObjectId.is_valid(product_id):
        raise HTTPException(status_code=400, detail="Invalid product_id")
    doc = await db.products.find_one({"_id": ObjectId(product_id)})
    if not doc:
        raise HTTPException(status_code=404, detail="Product not found")
    return _doc_to_out(doc)

@app.get("/products/", response_model=list[ProductOut])
async def list_products(
    page: int = Query(1, ge=1),
    page_size: int = Query(10, ge=1, le=100),
    db=Depends(get_db),
    user=Depends(get_current_user),
):
    skip = (page - 1) * page_size
    cursor = db.products.find({}).sort("created_at", -1).skip(skip).limit(page_size)
    docs = await cursor.to_list(length=page_size)
    return [_doc_to_out(d) for d in docs]

@app.put("/products/{product_id}", response_model=ProductOut)
async def update_product(product_id: str, payload: ProductUpdate, db=Depends(get_db), user=Depends(get_current_user)):
    if not ObjectId.is_valid(product_id):
        raise HTTPException(status_code=400, detail="Invalid product_id")
    updates = {k: v for k, v in payload.model_dump().items() if v is not None}
    if not updates:
        raise HTTPException(status_code=400, detail="No fields to update")

    res = await db.products.update_one({"_id": ObjectId(product_id)}, {"$set": updates})
    if res.matched_count == 0:
        raise HTTPException(status_code=404, detail="Product not found")

    doc = await db.products.find_one({"_id": ObjectId(product_id)})
    return _doc_to_out(doc)

@app.delete("/products/{product_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_product(product_id: str, db=Depends(get_db), admin=Depends(require_admin)):
    if not ObjectId.is_valid(product_id):
        raise HTTPException(status_code=400, detail="Invalid product_id")
    res = await db.products.delete_one({"_id": ObjectId(product_id)})
    if res.deleted_count == 0:
        raise HTTPException(status_code=404, detail="Product not found")
    return None
```

### `q1/test_main.py` (at least 2 tests)
These tests mock auth tokens by actually calling `/auth/token`. For DB, simplest is using a real test MongoDB; if you want pure unit tests, tell me and I’ll show dependency override with an in-memory fake.

```python name=q1/test_main.py
import pytest
from fastapi.testclient import TestClient
from q1.main import app

client = TestClient(app)

def get_token(username: str) -> str:
    r = client.post("/auth/token", data={"username": username, "password": "x"})
    assert r.status_code == 200
    return r.json()["access_token"]

def test_create_and_get_product():
    token = get_token("user1")
    headers = {"Authorization": f"Bearer {token}"}

    r = client.post("/products/", json={"name": "Phone", "description": "Nice", "price": 10.5, "stock": 2}, headers=headers)
    assert r.status_code == 201
    pid = r.json()["id"]

    r2 = client.get(f"/products/{pid}", headers=headers)
    assert r2.status_code == 200
    assert r2.json()["name"] == "Phone"

def test_delete_requires_admin():
    user_token = get_token("user2")
    admin_token = get_token("admin")

    # create with user
    r = client.post(
        "/products/",
        json={"name": "Laptop", "price": 999.0, "stock": 0},
        headers={"Authorization": f"Bearer {user_token}"},
    )
    assert r.status_code == 201
    pid = r.json()["id"]

    # delete with non-admin => 403
    r2 = client.delete(f"/products/{pid}", headers={"Authorization": f"Bearer {user_token}"})
    assert r2.status_code == 403

    # delete with admin => 204
    r3 = client.delete(f"/products/{pid}", headers={"Authorization": f"Bearer {admin_token}"})
    assert r3.status_code == 204
```

---

## Question 2) Async Order Processing API + Background Task + Middleware logging + custom error format + Swagger examples

### `q2/main.py`
```python name=q2/main.py
import os
import json
import logging
from datetime import datetime, timezone
from typing import Any, Optional

from motor.motor_asyncio import AsyncIOMotorClient
from fastapi import FastAPI, BackgroundTasks, Request
from fastapi.responses import JSONResponse
from pydantic import BaseModel, Field, ConfigDict

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("orders")

MONGO_URL = os.getenv("MONGO_URL", "mongodb://localhost:27017")
DB_NAME = os.getenv("MONGO_DB", "orders_db")

client = AsyncIOMotorClient(MONGO_URL)
db = client[DB_NAME]

app = FastAPI(title="Q2 - Async Orders API")

# --- Custom error response format
class ErrorResponse(BaseModel):
    error: dict[str, Any]

@app.exception_handler(Exception)
async def global_exception_handler(request: Request, exc: Exception):
    # Let FastAPI's HTTPException behave normally (handled internally).
    # This handler is for unexpected exceptions.
    logger.exception("Unhandled error")
    return JSONResponse(
        status_code=500,
        content=ErrorResponse(error={"code": "INTERNAL_ERROR", "message": "Unexpected error"}).model_dump(),
    )

# --- Middleware: log order details (request logging)
@app.middleware("http")
async def log_requests(request: Request, call_next):
    body_bytes = await request.body()
    body_text = body_bytes.decode("utf-8") if body_bytes else ""
    logger.info("request method=%s path=%s body=%s", request.method, request.url.path, body_text)
    response = await call_next(request)
    return response

# --- Models with Swagger examples
class OrderCreate(BaseModel):
    user_id: str = Field(..., examples=["user_123"])
    product_id: str = Field(..., examples=["prod_abc"])
    quantity: int = Field(..., gt=0, examples=[2])

class OrderOut(BaseModel):
    model_config = ConfigDict(from_attributes=True)
    id: str
    user_id: str
    product_id: str
    quantity: int
    created_at: datetime
    status: str

class OrderResponse(BaseModel):
    data: OrderOut

async def send_confirmation_email(order_doc: dict):
    # Simulate side effect
    logger.info("Sending confirmation email for order_id=%s to user_id=%s", str(order_doc["_id"]), order_doc["user_id"])

@app.post(
    "/orders/",
    response_model=OrderResponse,
    status_code=201,
    responses={
        422: {
            "model": ErrorResponse,
            "description": "Validation error (FastAPI will produce its own by default unless overridden)."
        }
    },
)
async def create_order(payload: OrderCreate, background_tasks: BackgroundTasks):
    try:
        doc = payload.model_dump()
        doc["created_at"] = datetime.now(timezone.utc)
        doc["status"] = "created"

        res = await db.orders.insert_one(doc)
        created = await db.orders.find_one({"_id": res.inserted_id})
        background_tasks.add_task(send_confirmation_email, created)

        out = OrderOut(
            id=str(created["_id"]),
            user_id=created["user_id"],
            product_id=created["product_id"],
            quantity=created["quantity"],
            created_at=created["created_at"],
            status=created["status"],
        )
        return OrderResponse(data=out)
    except ValueError as e:
        # Example of custom exception handling
        return JSONResponse(
            status_code=400,
            content=ErrorResponse(error={"code": "BAD_REQUEST", "message": str(e)}).model_dump(),
        )
```

What this includes:
- **Async** endpoint (`async def`)
- **BackgroundTasks** to simulate sending email
- **Middleware** that logs request method/path/body (order details included for `/orders/`)
- **Custom response model**: `OrderResponse { data: ... }`
- **Swagger examples** via `Field(..., examples=[...])`
- Basic **custom error response format** for unexpected errors (`{"error": {...}}`)

---

## Question 3) Secure Microservice User Authentication (Register/Login/Profile) + roles + tests + deployment explanation

### Project structure (suggested)
- `q3/main.py`
- `q3/db.py`
- `q3/security.py`
- `q3/models.py`
- `q3/test_main.py`

### `q3/security.py`
```python name=q3/security.py
import os
from datetime import datetime, timedelta, timezone
from typing import Literal

import jwt
from passlib.context import CryptContext
from fastapi import HTTPException, status

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

SECRET_KEY = os.getenv("SECRET_KEY", "dev_only_change_me")
ALGORITHM = os.getenv("JWT_ALG", "HS256")
ACCESS_TOKEN_EXPIRE_MINUTES = int(os.getenv("ACCESS_TOKEN_EXPIRE_MINUTES", "30"))

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def verify_password(password: str, password_hash: str) -> bool:
    return pwd_context.verify(password, password_hash)

def create_access_token(subject: str, role: Literal["admin","user"]) -> str:
    exp = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    payload = {"sub": subject, "role": role, "exp": exp}
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)

def decode_access_token(token: str) -> dict:
    try:
        return jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
    except jwt.ExpiredSignatureError:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Token expired")
    except Exception:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid token")
```

### `q3/db.py`
```python name=q3/db.py
import os
from motor.motor_asyncio import AsyncIOMotorClient

MONGO_URL = os.getenv("MONGO_URL", "mongodb://localhost:27017")
DB_NAME = os.getenv("MONGO_DB", "auth_db")

_client: AsyncIOMotorClient | None = None

def get_client() -> AsyncIOMotorClient:
    global _client
    if _client is None:
        _client = AsyncIOMotorClient(MONGO_URL)
    return _client

async def get_db():
    db = get_client()[DB_NAME]
    yield db
```

### `q3/models.py`
```python name=q3/models.py
from typing import Literal
from pydantic import BaseModel, EmailStr, Field

class UserRegister(BaseModel):
    email: EmailStr
    password: str = Field(..., min_length=8)
    role: Literal["admin","user"] = "user"

class UserOut(BaseModel):
    id: str
    email: EmailStr
    role: Literal["admin","user"]

class TokenOut(BaseModel):
    access_token: str
    token_type: str = "bearer"
```

### `q3/main.py`
```python name=q3/main.py
from bson import ObjectId
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm

from .db import get_db
from .models import UserRegister, UserOut, TokenOut
from .security import hash_password, verify_password, create_access_token, decode_access_token

app = FastAPI(title="Q3 - Auth Microservice")

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/auth/login")

async def get_current_user(db=Depends(get_db), token: str = Depends(oauth2_scheme)) -> dict:
    payload = decode_access_token(token)
    email = payload.get("sub")
    if not email:
        raise HTTPException(status_code=401, detail="Invalid token")
    user = await db.users.find_one({"email": email})
    if not user:
        raise HTTPException(status_code=401, detail="User not found")
    return user

def require_role(*roles: str):
    async def _dep(user=Depends(get_current_user)):
        if user.get("role") not in roles:
            raise HTTPException(status_code=403, detail="Insufficient role")
        return user
    return _dep

@app.on_event("startup")
async def ensure_indexes():
    # prevent duplicate registration by email (unique index)
    db = (await anext(get_db()))
    await db.users.create_index("email", unique=True)

@app.post("/auth/register", response_model=UserOut, status_code=201)
async def register(payload: UserRegister, db=Depends(get_db)):
    # Input validation handled by Pydantic (EmailStr, min_length password)
    doc = {"email": payload.email.lower(), "password_hash": hash_password(payload.password), "role": payload.role}
    try:
        res = await db.users.insert_one(doc)
    except Exception:
        # If unique index triggers, motor raises duplicate key error (pymongo error)
        raise HTTPException(status_code=409, detail="Email already registered")

    return UserOut(id=str(res.inserted_id), email=doc["email"], role=doc["role"])

@app.post("/auth/login", response_model=TokenOut)
async def login(form: OAuth2PasswordRequestForm = Depends(), db=Depends(get_db)):
    # OAuth2PasswordRequestForm uses 'username' field for email in practice
    email = form.username.lower()
    user = await db.users.find_one({"email": email})
    if not user or not verify_password(form.password, user["password_hash"]):
        raise HTTPException(status_code=401, detail="Invalid credentials")

    token = create_access_token(subject=user["email"], role=user["role"])
    return TokenOut(access_token=token)

@app.get("/auth/me", response_model=UserOut)
async def me(user=Depends(get_current_user)):
    return UserOut(id=str(user["_id"]), email=user["email"], role=user["role"])

# Example of role-based protected route
@app.get("/admin/health", dependencies=[Depends(require_role("admin"))])
async def admin_only_health():
    return {"status": "ok"}
```

### `q3/test_main.py` (login success & failure + protected route access)
```python name=q3/test_main.py
from fastapi.testclient import TestClient
from q3.main import app

client = TestClient(app)

def register(email: str, password: str, role: str = "user"):
    return client.post("/auth/register", json={"email": email, "password": password, "role": role})

def login(email: str, password: str):
    return client.post("/auth/login", data={"username": email, "password": password})

def test_login_success_and_me_access():
    r = register("a@example.com", "password123", "user")
    assert r.status_code in (201, 409)  # allow re-run without cleaning DB

    r2 = login("a@example.com", "password123")
    assert r2.status_code == 200
    token = r2.json()["access_token"]

    r3 = client.get("/auth/me", headers={"Authorization": f"Bearer {token}"})
    assert r3.status_code == 200
    assert r3.json()["email"] == "a@example.com"

def test_login_failure_and_protected_route_denied():
    r = login("noone@example.com", "wrongpass")
    assert r.status_code == 401

    # no token => denied
    r2 = client.get("/auth/me")
    assert r2.status_code == 401
```

### Explain (Microservice deployment + how other services verify JWT)

**Deploying as a microservice**
- Package this service as a container (Docker) exposing one port (e.g., `8000`).
- Run behind an API gateway / ingress (Kubernetes Ingress, NGINX, Kong, etc.).
- Keep it stateless: all state is in MongoDB; scale horizontally by running multiple replicas.
- Configure secrets via environment variables (e.g., `SECRET_KEY`, `MONGO_URL`) using Kubernetes Secrets or a secret manager.

*How other services verify JWT tokens Two common patterns:*

Shared secret (HS256)

Other services are configured with the same SECRET_KEY and verify tokens locally:
validate signature
validate exp
read claims like sub (user) and role
Simple, but secret distribution is sensitive.
Asymmetric keys (recommended) (RS256/ES256)

Auth service signs tokens with a private key.
Other services verify using the public key (no need for the signing secret).
Typically you also expose a JWKS endpoint so services can fetch rotating public keys.
This is the standard approach for multi-service ecosystems.
If you want, I can also:
Add MongoDB dependency overrides for tests (so tests don’t need a real DB)
Convert Q1/Q3 into a single “clean architecture” layout with routers/services/repositories
Use RS256 + JWKS in Q3 (more realistic for microservices)
Tell me which you prefer and whether you want one combined repo layout or three separate folders exactly as above.


## Question 1 — Secure Product Management API (JWT, admin-only delete, MongoDB)  
Below is the **step-by-step flow**, what to run, and **expected outputs** you can compare to validate.

### Step-by-step (what each part does)

1. **MongoDB connection + DI (`get_db`)**
   - `get_db()` yields a MongoDB database object.
   - Each request that needs DB uses `db=Depends(get_db)`.

2. **Pydantic validation**
   - `ProductCreate` enforces:
     - `name`: required, min length 3
     - `price`: `> 0`
     - `stock`: `>= 0`
   - If invalid, FastAPI returns **422** automatically.

3. **JWT Authentication**
   - `/auth/token` issues a JWT.
   - In the sample code:
     - username `"admin"` → role `"admin"`
     - otherwise → role `"user"`
   - Protected endpoints use `Depends(get_current_user)`.

4. **Admin-only delete**
   - `DELETE /products/{id}` uses `Depends(require_admin)`
   - If role isn’t admin → **403 Forbidden**

5. **Correct HTTP status codes**
   - Create: **201**
   - Get/List/Update: **200**
   - Delete: **204**
   - Not found: **404**
   - Bad ID: **400**
   - Forbidden: **403**
   - Unauthorized token issues: **401**

6. **Pagination**
   - `GET /products?page=1&page_size=10` uses `skip` + `limit`.

7. **Tests**
   - Use `TestClient`:
     - Create & get product
     - Delete requires admin

---

### How to run (validation steps)

Assuming folder structure like earlier (`q1/`).

1) Start MongoDB locally:
- If using local MongoDB:
  - `mongodb://localhost:27017`

2) Run API:
```bash
uvicorn q1.main:app --reload
```

Open docs:
- `http://127.0.0.1:8000/docs`

---

### Manual validation with expected outputs

#### A) Get token (user)
**Request**
`POST /auth/token` (form)
- username: `user1`
- password: `x`

**Expected response (200)**
```json
{
  "access_token": "eyJ....",
  "token_type": "bearer"
}
```

#### B) Create product
**Request**
`POST /products/` with header:
- `Authorization: Bearer <token>`

Body:
```json
{
  "name": "Phone",
  "description": "Nice",
  "price": 10.5,
  "stock": 2
}
```

**Expected response (201)**
```json
{
  "id": "65f0....",
  "name": "Phone",
  "description": "Nice",
  "price": 10.5,
  "stock": 2,
  "created_at": "2026-03-26T...+00:00"
}
```
Validate:
- `created_at` exists and is auto-generated
- `id` is returned

#### C) Create invalid product (name too short)
Body:
```json
{
  "name": "AB",
  "price": 10,
  "stock": 1
}
```

**Expected response (422)**
You should see validation errors mentioning min length 3.

#### D) Get single product
`GET /products/{id}` with auth header.

**Expected response (200)**: same product JSON.

#### E) List products
`GET /products?page=1&page_size=10`

**Expected response (200)**
```json
[
  {
    "id": "...",
    "name": "Phone",
    "description": "Nice",
    "price": 10.5,
    "stock": 2,
    "created_at": "..."
  }
]
```

#### F) Delete product as non-admin
`DELETE /products/{id}` using a non-admin token.

**Expected response (403)**
```json
{"detail":"Admin required"}
```

#### G) Delete product as admin
Get admin token (username `admin`), then call delete.

**Expected response (204)**
- No response body.

---

## Question 2 — Async Order API + BackgroundTasks + Middleware Logging + Custom Response Format  
This one is about **async order creation**, **logging**, and **background task** execution.

### Step-by-step (what each part does)

1. **Async endpoint**
   - `POST /orders/` is `async def`, uses Motor (async MongoDB driver)

2. **Pydantic validation**
   - `quantity` must be `> 0` else **422**

3. **Middleware logging**
   - Logs request method, path, and raw body.
   - You validate it by checking console logs after calling `/orders/`.

4. **BackgroundTasks**
   - After DB insert, a background task runs:
     - simulates sending a confirmation email
   - You validate it via log line: “Sending confirmation email…”

5. **Custom response model**
   - Response is wrapped:
     - `{ "data": { ...order... } }`

6. **Swagger examples**
   - Field examples show in `/docs`.

---

### How to run
```bash
uvicorn q2.main:app --reload
```
Docs:
- `http://127.0.0.1:8000/docs`

---

### Manual validation with expected outputs

#### A) Create order (valid)
**Request**
`POST /orders/`
Body:
```json
{
  "user_id": "user_123",
  "product_id": "prod_abc",
  "quantity": 2
}
```

**Expected response (201)**
```json
{
  "data": {
    "id": "65f0....",
    "user_id": "user_123",
    "product_id": "prod_abc",
    "quantity": 2,
    "created_at": "2026-03-26T...+00:00",
    "status": "created"
  }
}
```

#### B) Validate middleware logging (console output)
In your terminal running Uvicorn, you should see something like:
- `request method=POST path=/orders/ body={"user_id":"user_123","product_id":"prod_abc","quantity":2}`

This confirms the middleware ran.

#### C) Validate background task (console output)
Right after the request, you should also see:
- `Sending confirmation email for order_id=... to user_id=user_123`

That confirms the background task executed.

#### D) Invalid quantity
Body:
```json
{
  "user_id": "user_123",
  "product_id": "prod_abc",
  "quantity": 0
}
```

**Expected response (422)**
FastAPI validation error for `quantity` (must be > 0).

---

## Question 3 — Auth Microservice (Register/Login/Profile, hashing, JWT expiry, roles) + Tests + Deployment explanation

### Step-by-step (what each part does)

1. **Register user**
   - Validates:
     - email format (`EmailStr`)
     - password min length (8)
   - Hashes password using bcrypt via Passlib
   - Stores `email`, `password_hash`, `role`

2. **Prevent duplicate emails**
   - Uses MongoDB unique index on `email`
   - If duplicate → **409 Conflict**

3. **Login (OAuth2 password flow)**
   - Uses `OAuth2PasswordRequestForm` (fields: username/password)
   - Here `username` is treated as email (common practice)
   - Verifies password hash
   - Returns JWT with:
     - `sub` = email
     - `role` = admin/user
     - `exp` = expiry timestamp

4. **Get current user profile**
   - Protected route `/auth/me`
   - Requires Bearer token
   - Decodes JWT, finds user in DB, returns profile

5. **Role-based access**
   - `require_role("admin")` example for admin-only routes

6. **Security best practices**
   - `SECRET_KEY` from environment variable (don’t hardcode in real deployments)
   - Short-lived tokens via `ACCESS_TOKEN_EXPIRE_MINUTES`
   - Input validation, proper status codes

7. **Tests**
   - Login success & failure
   - Protected route access denied without token

---

### How to run
Set env vars (recommended):
```bash
export SECRET_KEY="a-very-long-random-secret"
export ACCESS_TOKEN_EXPIRE_MINUTES="30"
export MONGO_URL="mongodb://localhost:27017"
```

Run:
```bash
uvicorn q3.main:app --reload
```

Docs:
- `http://127.0.0.1:8000/docs`

---

### Manual validation with expected outputs

#### A) Register
`POST /auth/register`
```json
{
  "email": "a@example.com",
  "password": "password123",
  "role": "user"
}
```

**Expected response (201)**
```json
{
  "id": "65f0....",
  "email": "a@example.com",
  "role": "user"
}
```

Duplicate register same email again:

**Expected response (409)**
```json
{"detail":"Email already registered"}
```

#### B) Login success
`POST /auth/login` (form fields)
- username: `a@example.com`
- password: `password123`

**Expected response (200)**
```json
{
  "access_token": "eyJ....",
  "token_type": "bearer"
}
```

#### C) Login failure
- username: `a@example.com`
- password: `wrongpass`

**Expected response (401)**
```json
{"detail":"Invalid credentials"}
```

#### D) Access protected route `/auth/me` without token
**Expected response (401)**
```json
{"detail":"Not authenticated"}
```

#### E) Access `/auth/me` with token
Header: `Authorization: Bearer <token>`

**Expected response (200)**
```json
{
  "id": "65f0....",
  "email": "a@example.com",
  "role": "user"
}
```

---

### Explain: Deploy as a microservice
A practical microservice deployment looks like this:

1. **Containerize** this auth service (Docker) and run it as its own deployable unit.
2. **Environment configuration**:
   - Inject `SECRET_KEY`, `MONGO_URL`, expiry settings at runtime (Kubernetes Secrets / AWS Secrets Manager / Vault).
3. **Scale independently**:
   - Run multiple replicas behind a load balancer/API gateway.
4. **Stateless**:
   - No server memory sessions; JWT carries identity claims; DB stores users.

---

### Explain: How other services verify JWT tokens
Other services should not call the auth DB; they should validate the token.

Two main approaches:

1) **Shared secret (HS256)** (simple, less ideal)
- Every service gets the same `SECRET_KEY`
- They verify signature + expiry locally
- Downside: secret must be distributed to all services securely

2) **Asymmetric signing (RS256/ES256)** (recommended for microservices)
- Auth service signs tokens with a **private key**
- Other services verify with the **public key**
- Auth service can expose a **JWKS** endpoint for key rotation
- Benefit: other services never get the signing secret

---

## If you want the validation to be even clearer
Tell me which format you prefer:
1) **cURL commands** for every endpoint + expected responses  
2) A **Postman collection** outline  
3) Fully automated **pytest** integration tests for all 3 questions (including Q2 background-task log assertion via caplog / logging capture)
