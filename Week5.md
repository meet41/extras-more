Below are structured, “notes-style” deep-dive explanations with practical examples for each topic. I’ll use a consistent sample domain: **E‑commerce** with collections like `users`, `products`, `orders`.

> Assumptions: MongoDB 6+ syntax, examples shown in **MongoDB Shell / mongosh** and **PyMongo**.  
> Tip: For best performance, always design schema + indexes around your **most frequent queries**.

---

## 1) Query Operators

### 1.1 Comparison Operators: `$eq, $gt, $lt, $gte, $lte`
Used in `find()` filters to compare values.

**Syntax**
```javascript name=mongo_comparison_ops.js
db.products.find({ price: { $gte: 100, $lte: 500 } })
db.orders.find({ total: { $gt: 1000 } })
db.users.find({ status: { $eq: "ACTIVE" } }) // same as {status: "ACTIVE"}
```

**Common notes**
- `$eq` is implicit: `{field: value}`.
- Range queries often benefit from indexes on the ranged field (e.g., `price`).

**Problem example**
> Find products priced between 100 and 500 and in stock.
```javascript name=mongo_comparison_problem.js
db.products.find({
  price: { $gte: 100, $lte: 500 },
  stock: { $gt: 0 }
})
```

---

### 1.2 Logical Operators: `$and, $or, $not`
Combine conditions.

**`$and`**
- Implicit AND happens when you put multiple fields in the same filter.
- Use explicit `$and` when you need multiple conditions on same field or more complex expressions.

```javascript name=mongo_and_or_not.js
// implicit AND
db.users.find({ country: "IN", status: "ACTIVE" })

// explicit $and
db.products.find({
  $and: [
    { price: { $gte: 100 } },
    { price: { $lte: 500 } }
  ]
})

// $or: match any
db.users.find({
  $or: [{ country: "IN" }, { country: "US" }]
})

// $not: negates a condition (often used with regex or comparisons)
db.products.find({ price: { $not: { $gte: 1000 } } }) // price < 1000 OR missing/non-numeric nuances
```

**Important**
- `$not` works on *operators*, not directly like SQL NOT.  
  For “not equals”, typically use `$ne`.

**Problem example**
> Find orders that are either PAID or SHIPPED and total > 100.
```javascript name=mongo_logical_problem.js
db.orders.find({
  $and: [
    { total: { $gt: 100 } },
    { $or: [{ status: "PAID" }, { status: "SHIPPED" }] }
  ]
})
```

---

### 1.3 Element Operators: `$exists, $type`
**`$exists`**: field presence check
```javascript name=mongo_exists.js
db.users.find({ phone: { $exists: true } })
db.users.find({ phone: { $exists: false } }) // field missing
```

**`$type`**: enforce BSON type in query
```javascript name=mongo_type.js
db.users.find({ age: { $type: "int" } }) // or numeric codes
db.events.find({ createdAt: { $type: "date" } })
```

**Use cases**
- Cleaning inconsistent data
- Debugging / migration checks

---

### 1.4 Array Operators (core ones you’ll use)
MongoDB arrays are first-class; these operators help query them.

**Common array query patterns**
```javascript name=mongo_array_ops.js
// Match docs where tags array contains "sale" (simple contains)
db.products.find({ tags: "sale" })

// $all: must contain all specified values
db.products.find({ tags: { $all: ["sale", "electronics"] } })

// $in: field value matches any (works for non-array too)
db.products.find({ category: { $in: ["phone", "laptop"] } })

// $size: array length equals N (exact match only)
db.products.find({ tags: { $size: 3 } })

// $elemMatch: multiple conditions must apply to the same array element
db.orders.find({
  items: {
    $elemMatch: { sku: "A1", qty: { $gte: 2 } }
  }
})
```

**Problem example**
> Find orders where at least one item has qty >= 5 and price >= 100 (same item).
```javascript name=mongo_array_problem.js
db.orders.find({
  items: { $elemMatch: { qty: { $gte: 5 }, price: { $gte: 100 } } }
})
```

---

### 1.5 Regex Queries
Used for text-like matching (not a full-text search replacement).

```javascript name=mongo_regex.js
// Case-insensitive "meet" anywhere
db.users.find({ name: { $regex: "meet", $options: "i" } })

// Prefix search (index-friendly only if it’s a left-anchored regex)
db.users.find({ email: { $regex: "^meet", $options: "i" } })
```

**Performance notes**
- Regex without left anchor (`^`) often causes collection scan unless you use specialized indexing/text search.
- If you need search-like behavior, consider **Atlas Search** or **text indexes**.

**Problem example**
> Find products whose name starts with “iPhone” (prefix search).
```javascript name=mongo_regex_problem.js
db.products.find({ name: { $regex: "^iPhone", $options: "i" } })
```

---

## 2) Projection & Filtering

### 2.1 Selecting specific fields (Projection)
Projection reduces network payload and improves performance.

```javascript name=mongo_projection.js
// Include only name and price (and _id included by default)
db.products.find({ stock: { $gt: 0 } }, { name: 1, price: 1 })

// Exclude _id explicitly
db.products.find({ stock: { $gt: 0 } }, { _id: 0, name: 1, price: 1 })
```

### 2.2 Including vs excluding fields
Rules:
- You usually **either include or exclude**, not both.
- Exception: you may exclude `_id` while including others.

```javascript name=mongo_projection_exclude.js
// Exclude internal fields
db.users.find({ status: "ACTIVE" }, { passwordHash: 0, tokens: 0 })
```

### 2.3 Filtering documents using conditions
Same as section 1 operators; combine filter + projection:

**Problem**
> API endpoint returns product cards: only `name`, `price`, `thumbnailUrl`.
```javascript name=mongo_filter_projection_problem.js
db.products.find(
  { isActive: true, stock: { $gt: 0 } },
  { _id: 0, name: 1, price: 1, thumbnailUrl: 1 }
)
```

---

## 3) Sorting, Limit & Skip (Pagination)

### 3.1 Sorting results (`1` asc, `-1` desc)
```javascript name=mongo_sort.js
db.orders.find({ status: "PAID" }).sort({ createdAt: -1 })
db.products.find().sort({ price: 1, name: 1 }) // tie-breaker
```

### 3.2 Pagination using `limit()` and `skip()`
```javascript name=mongo_limit_skip.js
db.orders.find().sort({ createdAt: -1 }).skip(20).limit(10) // page 3 if pageSize=10
```

**Important performance note**
- `skip()` gets slower for large page numbers (MongoDB still walks skipped docs).
- Better approach at scale: **cursor-based pagination** using `createdAt` + `_id`.

### 3.3 Real-world API use case (page-based)
**Problem**
> GET `/orders?page=3&pageSize=10` returns newest orders.

**Query**
```javascript name=mongo_page_based_example.js
const page = 3;
const pageSize = 10;

db.orders.find({ customerId: ObjectId("...") })
  .sort({ createdAt: -1 })
  .skip((page - 1) * pageSize)
  .limit(pageSize)
```

**Better (cursor-based)**
Use `createdAt` and `_id` as cursor:
```javascript name=mongo_cursor_pagination.js
// next page: createdAt < lastCreatedAt OR (createdAt == lastCreatedAt AND _id < lastId)
db.orders.find({
  customerId: ObjectId("..."),
  $or: [
    { createdAt: { $lt: lastCreatedAt } },
    { createdAt: lastCreatedAt, _id: { $lt: lastId } }
  ]
})
.sort({ createdAt: -1, _id: -1 })
.limit(10)
```

---

## 4) Indexes in MongoDB

### 4.1 Why indexes are important
Indexes make reads faster by avoiding full collection scans:
- `find()` filters
- `sort()`
- `join-like` operations (`$lookup`) when indexed appropriately

### 4.2 Single-field index
```javascript name=mongo_single_index.js
db.users.createIndex({ email: 1 })
db.orders.createIndex({ createdAt: -1 })
```

### 4.3 Compound index
Order matters: `{a: 1, b: 1}` helps queries filtering by `a` and optionally `b`, and sorts aligned with index.

```javascript name=mongo_compound_index.js
db.orders.createIndex({ customerId: 1, createdAt: -1 })
```

### 4.4 Unique index
Enforces uniqueness.
```javascript name=mongo_unique_index.js
db.users.createIndex({ email: 1 }, { unique: true })
```

### 4.5 How indexes improve query performance
Use `explain()` to see if an index is used:
```javascript name=mongo_explain.js
db.orders.find({ customerId: ObjectId("...") })
  .sort({ createdAt: -1 })
  .explain("executionStats")
```

Look for:
- `IXSCAN` (index scan) vs `COLLSCAN`
- low `totalDocsExamined`

### 4.6 When indexes slow down writes
Every insert/update/delete must also update indexes.
- Too many indexes → slower writes, more RAM/disk usage.
- Only keep indexes that support real query patterns.

**Rule of thumb**
- Index for your **top queries**
- Remove unused indexes after observing usage

---

## 5) Aggregation Framework

### 5.1 What is aggregation?
Aggregation is MongoDB’s powerful data processing pipeline (like SQL `GROUP BY`, joins, transformations).

### 5.2 Aggregation Pipeline concept
Pipeline = array of stages: each stage transforms the documents.

```javascript name=mongo_aggregation_intro.js
db.orders.aggregate([
  { $match: { status: "PAID" } },
  { $group: { _id: "$customerId", total: { $sum: "$total" } } }
])
```

### 5.3 Important stages

#### `$match` (filter early)
```javascript name=mongo_stage_match.js
{ $match: { status: "PAID", createdAt: { $gte: ISODate("2026-01-01") } } }
```

#### `$group` (aggregate)
```javascript name=mongo_stage_group.js
{ $group: { _id: "$customerId", totalSpend: { $sum: "$total" } } }
```

#### `$project` (shape output)
```javascript name=mongo_stage_project.js
{ $project: { _id: 0, customerId: "$_id", totalSpend: 1 } }
```

#### `$sort`
```javascript name=mongo_stage_sort.js
{ $sort: { totalSpend: -1 } }
```

#### `$lookup` (join collections)
```javascript name=mongo_stage_lookup.js
{
  $lookup: {
    from: "users",
    localField: "customerId",
    foreignField: "_id",
    as: "customer"
  }
}
```

Often followed by `$unwind` to flatten:
```javascript name=mongo_unwind.js
{ $unwind: "$customer" }
```

### 5.4 Real-world examples

#### Example A: Sales report (daily revenue)
**Problem**
> Total revenue per day for paid orders.

```javascript name=mongo_sales_report_daily.js
db.orders.aggregate([
  { $match: { status: "PAID" } },
  {
    $group: {
      _id: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } },
      revenue: { $sum: "$total" },
      orders: { $sum: 1 }
    }
  },
  { $sort: { _id: 1 } }
])
```

#### Example B: Total revenue calculation (by product SKU from items array)
**Problem**
> Compute revenue per SKU using `items: [{sku, qty, price}]`.

```javascript name=mongo_revenue_per_sku.js
db.orders.aggregate([
  { $match: { status: "PAID" } },
  { $unwind: "$items" },
  {
    $group: {
      _id: "$items.sku",
      revenue: { $sum: { $multiply: ["$items.qty", "$items.price"] } },
      units: { $sum: "$items.qty" }
    }
  },
  { $sort: { revenue: -1 } }
])
```

---

## 6) Embedded vs Referenced Documents

### Embedding (Denormalization)
Store related data inside one document.

**Example (embed order items in order)**
```javascript name=mongo_embed_example.js
{
  _id: ObjectId("..."),
  customerId: ObjectId("..."),
  status: "PAID",
  items: [
    { sku: "A1", name: "Pen", qty: 2, price: 1.5 }
  ],
  createdAt: ISODate("2026-03-05T10:00:00Z")
}
```

**When to use embedding**
- “Read together, write together”
- One-to-few relationships
- Need fast reads without joins

### Referencing (Normalization)
Store IDs and fetch related docs as needed (or with `$lookup`).

**Example (order references products)**
```javascript name=mongo_reference_example.js
{
  _id: ObjectId("..."),
  customerId: ObjectId("..."),
  items: [
    { productId: ObjectId("..."), qty: 2, priceAtPurchase: 1.5 }
  ]
}
```

**When to use referencing**
- Many-to-many or large one-to-many relationships
- Child objects change frequently and you don’t want to rewrite big parent docs
- You must avoid doc growth / size limits

### Performance trade-offs
- Embedding: fewer queries, bigger documents, can hit 16MB doc limit
- Referencing: more queries / `$lookup`, smaller docs, more flexible

---

## 7) Schema Design Best Practices

### 7.1 Design for queries (most important)
Start from your endpoints/reports:
- “Get last 20 orders for a customer”
- “Search products by category and price range”
Then design fields + indexes for those.

### 7.2 Avoid unnecessary nesting
Deep nesting can:
- complicate updates
- reduce readability
Prefer flatter structures unless nesting is truly bounded and queried as a unit.

### 7.3 Manage document size
MongoDB document limit: **16MB**.
Avoid unbounded arrays (e.g., embedding all user logins forever).
Use bucketed designs or separate collections.

### 7.4 Handle relationships properly
- one-to-few → embed
- one-to-many (large) → reference
- many-to-many → reference with linking collection (e.g., `user_roles`)

### 7.5 Plan for scalability
- Use stable shard keys (if sharding later)
- Avoid “hot documents” updated constantly by many requests

---

## 8) Transactions (Basics) — Replica Set required

### 8.1 What is a transaction?
A transaction ensures a set of operations succeed or fail together.

### 8.2 Multi-document transactions (basic idea)
MongoDB supports multi-document transactions on replica sets (and sharded clusters).

**When transactions are required**
- You must update multiple documents/collections and maintain strict consistency
- Money transfers, inventory decrement + order create, etc.

### 8.3 ACID properties in MongoDB (overview)
- **Atomicity**: all ops in transaction commit or rollback
- **Consistency**: constraints/validation enforced
- **Isolation**: snapshot isolation semantics
- **Durability**: committed writes persist (depends on write concern)

**Problem example**
> Create order + decrement inventory, both must succeed or neither.

*(Shell example conceptually; exact session syntax depends on driver. In PyMongo, see later section.)*

---

## 9) Data Validation

MongoDB supports schema validation using `$jsonSchema` on collections.

### 9.1 Schema validation rules (example)
```javascript name=mongo_schema_validation.js
db.createCollection("products", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "price", "stock", "isActive"],
      properties: {
        name: { bsonType: "string", minLength: 1 },
        price: { bsonType: "double", minimum: 0 },
        stock: { bsonType: "int", minimum: 0 },
        isActive: { bsonType: "bool" },
        tags: {
          bsonType: "array",
          items: { bsonType: "string" }
        }
      }
    }
  }
})
```

### 9.2 Preventing invalid data insertion
With validation enabled:
- invalid inserts/updates are rejected
- keeps data clean, simplifies app logic

**Note**
- Validation is not a replacement for application-level validation, but a strong safety net.

---

## 10) MongoDB Security Basics

### 10.1 Authentication
Enable auth and require username/password (or x.509, etc).
- In Atlas, it’s enabled by default.
- Locally, you enable authorization in config and create users.

### 10.2 Authorization (Roles)
Grant least privilege:
- app user: readWrite on specific DB
- admin user: only for admin tasks

### 10.3 IP Whitelisting
- Atlas: network access list (allow only your server IPs)
- Self-hosted: firewall rules/security groups

### 10.4 Avoid exposing DB to public internet
- Never bind MongoDB directly to `0.0.0.0` without firewall and auth.
- Prefer private networking / VPC peering / VPN.

### 10.5 Environment variables for credentials
Do not hardcode credentials in code.

Example env vars:
- `MONGODB_URI`
- `MONGODB_DB`

---

## 11) Performance Optimization Basics

### 11.1 Proper indexing strategy
- Index fields used in frequent filters and sorts.
- Use compound indexes matching your query shape:
  - filter fields first
  - then sort fields (in same direction if possible)

### 11.2 Avoid full collection scans
Use `explain()` and look for `COLLSCAN`.
```javascript name=mongo_avoid_collscan.js
db.users.find({ email: "a@example.com" }).explain("executionStats")
```

### 11.3 Use projection wisely
Return only needed fields to reduce network + decoding cost.

### 11.4 Monitor slow queries
- Use profiler / logs
- In Atlas: Performance Advisor

### 11.5 Document size optimization
- Don’t embed unlimited arrays
- Avoid duplicating huge subdocuments unnecessarily
- Store blobs elsewhere (S3/GridFS) and keep references

---

## 12) PyMongo (MongoDB with Python)

### 12.1 Installing PyMongo
```bash name=pymongo_install.sh
pip install pymongo
```

If using SRV connection strings (Atlas), PyMongo typically handles it; sometimes you may need:
```bash name=pymongo_dns_install.sh
pip install "pymongo[srv]"
```

### 12.2 Connecting to MongoDB
```python name=pymongo_connect.py
import os
from pymongo import MongoClient

MONGODB_URI = os.environ["MONGODB_URI"]  # e.g. "mongodb+srv://user:pass@cluster/..."
client = MongoClient(MONGODB_URI)

db = client["shop"]
users = db["users"]
```

### 12.3 CRUD operations

**Create**
```python name=pymongo_create.py
from datetime import datetime

doc = {"email": "a@example.com", "status": "ACTIVE", "createdAt": datetime.utcnow()}
res = users.insert_one(doc)
print("inserted_id:", res.inserted_id)
```

**Read**
```python name=pymongo_read.py
from bson import ObjectId

u = users.find_one({"email": "a@example.com"}, {"_id": 1, "email": 1, "status": 1})
print(u)

user_id = ObjectId("65f000000000000000000000")
u2 = users.find_one({"_id": user_id})
```

**Update**
```python name=pymongo_update.py
res = users.update_one(
    {"email": "a@example.com"},
    {"$set": {"status": "SUSPENDED"}}
)
print(res.matched_count, res.modified_count)
```

**Delete**
```python name=pymongo_delete.py
res = users.delete_one({"email": "a@example.com"})
print(res.deleted_count)
```

### 12.4 Handling `ObjectId`
- MongoDB `_id` is often a `bson.ObjectId`.
- Convert from string using `ObjectId("...")`.
- When returning JSON via APIs, convert `ObjectId` to string.

```python name=objectid_to_str.py
from bson import ObjectId

def serialize_doc(doc: dict) -> dict:
    doc = dict(doc)
    if "_id" in doc and isinstance(doc["_id"], ObjectId):
        doc["_id"] = str(doc["_id"])
    return doc
```

### 12.5 Error handling in DB operations
Catch common exceptions:
- `DuplicateKeyError` for unique index violations
- `ServerSelectionTimeoutError` for connection issues
- `PyMongoError` for general driver errors

```python name=pymongo_error_handling.py
from pymongo.errors import DuplicateKeyError, PyMongoError

try:
    users.insert_one({"email": "a@example.com"})
except DuplicateKeyError:
    print("Email already exists")
except PyMongoError as e:
    print("Database error:", e)
```

### 12.6 Transactions in PyMongo (replica set)
**Problem**
> Create order + decrement stock atomically.

```python name=pymongo_transaction_example.py
from pymongo import MongoClient
from pymongo.errors import PyMongoError
from bson import ObjectId
from datetime import datetime

client = MongoClient(os.environ["MONGODB_URI"])
db = client["shop"]

orders = db["orders"]
products = db["products"]

def create_order(customer_id: str, product_id: str, qty: int):
    with client.start_session() as session:
        try:
            with session.start_transaction():
                prod = products.find_one({"_id": ObjectId(product_id)}, session=session)
                if not prod or prod["stock"] < qty:
                    raise ValueError("Insufficient stock")

                products.update_one(
                    {"_id": ObjectId(product_id)},
                    {"$inc": {"stock": -qty}},
                    session=session
                )

                order_doc = {
                    "customerId": ObjectId(customer_id),
                    "items": [{"productId": ObjectId(product_id), "qty": qty, "price": prod["price"]}],
                    "status": "NEW",
                    "createdAt": datetime.utcnow()
                }
                res = orders.insert_one(order_doc, session=session)
                return str(res.inserted_id)
        except (PyMongoError, ValueError) as e:
            # transaction aborts automatically on exception
            raise

```

### 12.7 Integrating MongoDB with Flask API (minimal pattern)
```python name=flask_mongo_api.py
import os
from flask import Flask, request, jsonify
from pymongo import MongoCli





## Comprehensive
## 1) Query Operators (Deep Theory + Usage)

### 1.0 Mental model: how MongoDB evaluates a query
MongoDB queries (e.g., `find(filter)`) match documents in a collection using:
- **Predicates** (conditions) over fields
- **BSON types** (type matters; `"10"` is not `10`)
- **Array semantics** (arrays match differently than scalar fields)
- **Index usage** (if an index can satisfy filter/sort efficiently)

**Key theoretical ideas**
- MongoDB is **schema-flexible**, but the query engine still relies on **consistent field types** for predictable results and good index use.
- Many operators behave differently when the field is **missing**, **null**, **array**, or **wrong type**. This is why validation and careful schema design matter.

---

### 1.1 Comparison operators: `$eq, $gt, $lt, $gte, $lte`

#### What they do
They compare a field’s value to a target value.

```javascript name=mongo_comparison_deep.js
db.products.find({ price: { $gte: 100, $lte: 500 } })
db.orders.find({ total: { $gt: 1000 } })
db.users.find({ status: { $eq: "ACTIVE" } }) // same as {status: "ACTIVE"}
```

#### Theory notes
- Comparisons are **type-aware**. If your data contains mixed types (e.g., price stored as `"199"` string in some docs), range queries become unreliable.
- Range queries benefit strongly from indexes (e.g., `{price: 1}`), but the selectivity (how many docs match) affects actual speed.
- `$eq` is implicit: `{field: value}` is the most common style.

#### When to use
- Filtering numeric ranges (price, age, timestamps)
- “After/before” time windows on `Date` fields

#### Problem: “Find active products in stock priced 100–500”
```javascript name=mongo_comparison_problem_deep.js
db.products.find({
  isActive: true,
  stock: { $gt: 0 },
  price: { $gte: 100, $lte: 500 }
})
```

---

### 1.2 Logical operators: `$and, $or, $not`

#### What they do
Combine multiple predicates into a larger boolean expression.

```javascript name=mongo_logical_deep.js
db.users.find({
  $or: [{ country: "IN" }, { country: "US" }]
})

db.orders.find({
  $and: [
    { total: { $gt: 100 } },
    { $or: [{ status: "PAID" }, { status: "SHIPPED" }] }
  ]
})
```

#### Theory notes (important)
- **Implicit AND**: `{a:1, b:2}` is already an AND.
- `$and` is typically used when:
  - you need multiple conditions on the same field and want explicit structure
  - you’re building queries dynamically in code
- `$or` can be expensive if it matches many documents; indexing each branch helps.
- `$not` negates an **operator expression**. For “not equals”, prefer `$ne`.

```javascript name=mongo_not_vs_ne.js
// Prefer $ne for inequality
db.users.find({ status: { $ne: "ACTIVE" } })

// $not is usually used with regex or specific operator forms:
db.users.find({ email: { $not: { $regex: "@example\\.com$" } } })
```

#### When to use
- Complex business rules (status OR conditions, optional filters)
- Search screens with many toggles

---

### 1.3 Element operators: `$exists, $type`

#### `$exists`
Checks whether a field is present in the document, regardless of value.

```javascript name=mongo_exists_deep.js
db.users.find({ phone: { $exists: true } })
db.users.find({ phone: { $exists: false } })
```

**Theory: missing vs null**
- A field can be:
  - missing entirely
  - present with value `null`
These are different states and matter for queries and schema quality.

```javascript name=mongo_missing_vs_null.js
// Matches docs where phone is explicitly null (field exists and equals null)
db.users.find({ phone: null })

// Matches docs where phone missing OR phone is null
db.users.find({ phone: { $eq: null } })

// To match only missing:
db.users.find({ phone: { $exists: false } })
```

#### `$type`
Matches documents where the field has a certain BSON type.

```javascript name=mongo_type_deep.js
db.users.find({ age: { $type: "int" } })
db.events.find({ createdAt: { $type: "date" } })
```

**When to use**
- Data audits and migrations
- Debugging inconsistent ingestion pipelines

---

### 1.4 Array operators and array query semantics

#### Theory: how arrays match
MongoDB has special behavior:
- If a field is an array, `{"tags": "sale"}` matches if `"sale"` is **any element** of the array.
- Without `$elemMatch`, multiple conditions can match **different elements** of an array (a common bug).

```javascript name=mongo_array_semantics_bug.js
// Potential bug: qty and price could match different items
db.orders.find({ "items.qty": { $gte: 5 }, "items.price": { $gte: 100 } })

// Correct: both conditions must match the SAME item
db.orders.find({
  items: { $elemMatch: { qty: { $gte: 5 }, price: { $gte: 100 } } }
})
```

#### Useful operators/patterns
```javascript name=mongo_array_ops_deep.js
db.products.find({ tags: "sale" }) // contains

db.products.find({ tags: { $all: ["sale", "electronics"] } })

db.products.find({ tags: { $size: 3 } }) // exact length

db.orders.find({
  items: { $elemMatch: { sku: "A1", qty: { $gte: 2 } } }
})
```

#### When to use arrays
- “one-to-few” embedded items (order line items, a few addresses)
- bounded lists that won’t grow without limit

Avoid embedding unbounded arrays that grow forever (risk doc bloat and 16MB limit).

---

### 1.5 Regex queries

#### What they do
Pattern matching on strings.

```javascript name=mongo_regex_deep.js
db.users.find({ name: { $regex: "meet", $options: "i" } })
db.users.find({ email: { $regex: "^meet", $options: "i" } })
```

#### Theory/performance notes
- Regex is not full-text search. It’s pattern matching.
- Only **left-anchored** regex (like `^prefix`) can effectively use a normal ascending index in many cases.
- For “search” features:
  - MongoDB text index (`$text`) is basic
  - Atlas Search is much more powerful (stemming, scoring, analyzers)

---

## 2) Projection & Filtering (More Theory)

### 2.1 What projection is (and why it matters)
Projection controls which fields are returned.
Benefits:
- Less network transfer
- Less memory/CPU to decode documents in the driver
- Better API security (don’t accidentally return secrets)

```javascript name=mongo_projection_deep.js
db.users.find(
  { status: "ACTIVE" },
  { _id: 0, email: 1, status: 1 }
)
```

### 2.2 Inclusion vs exclusion rules (theory)
- Inclusion projection: `{field1: 1, field2: 1}`
- Exclusion projection: `{secret: 0, tokens: 0}`
- You can’t mix inclusion and exclusion except for `_id`.

```javascript name=mongo_projection_rules.js
// OK
db.users.find({}, { email: 1, status: 1 })

// OK
db.users.find({}, { passwordHash: 0 })

// NOT OK (except _id)
db.users.find({}, { email: 1, passwordHash: 0 })
```

### 2.3 Filtering documents
MongoDB filters are document-shaped; they represent a boolean expression on fields.

**Problem (API card response)**
```javascript name=mongo_filter_projection_problem_deep.js
db.products.find(
  { isActive: true, stock: { $gt: 0 } },
  { _id: 0, name: 1, price: 1, thumbnailUrl: 1 }
)
```

---

## 3) Sorting, Limit & Skip (Pagination Theory)

### 3.1 Sorting
```javascript name=mongo_sort_deep.js
db.orders.find({ customerId: ObjectId("...") })
  .sort({ createdAt: -1 })
```

**Theory**
- Sorting without a supporting index may require an in-memory sort (slower and may hit memory limits).
- Ideal: your index matches filter + sort.

### 3.2 Limit/Skip pagination
```javascript name=mongo_skip_limit_deep.js
db.orders.find()
  .sort({ createdAt: -1 })
  .skip(2000)
  .limit(20)
```

**Theory**
- `skip()` is O(n) in practice for large offsets: the server still advances through skipped documents.
- Best for small datasets or small page numbers.

### 3.3 Real-world pagination strategies
**Page-based**: simple, but slow for deep pages  
**Cursor-based**: scalable and stable under inserts

Cursor-based keyset pagination theory:
- Choose a stable ordering key: `(createdAt, _id)` is common
- Use “less than last seen” to get next page

```javascript name=mongo_cursor_pagination_deep.js
db.orders.find({
  customerId: ObjectId("..."),
  $or: [
    { createdAt: { $lt: lastCreatedAt } },
    { createdAt: lastCreatedAt, _id: { $lt: lastId } }
  ]
})
.sort({ createdAt: -1, _id: -1 })
.limit(20)
```

---

## 4) Indexes in MongoDB (More Theory)

### 4.1 What an index is (theory)
An index is a separate structure (typically B-Tree-like) that stores:
- indexed field values
- pointers to documents
So MongoDB can locate documents without scanning the whole collection.

### 4.2 Why indexes are important
- Reduce disk reads
- Reduce documents examined
- Enable efficient sort
- Improve join-like operations with `$lookup` if keys are indexed

### 4.3 Types covered

#### Single-field index
```javascript name=mongo_index_single_deep.js
db.users.createIndex({ email: 1 })
```

#### Compound index (order matters)
```javascript name=mongo_index_compound_deep.js
db.orders.createIndex({ customerId: 1, createdAt: -1 })
```

**Theory**
- Supports queries filtering by `customerId` and sorting by `createdAt`.
- If your query filters only by `createdAt` but not `customerId`, this index may be less useful (depends on query).

#### Unique index
```javascript name=mongo_index_unique_deep.js
db.users.createIndex({ email: 1 }, { unique: true })
```

**Theory**
- Enforces data integrity at the database layer.
- Prevents race-condition duplicates that app-only checks cannot reliably prevent.

### 4.4 How to verify index usage
```javascript name=mongo_index_explain_deep.js
db.users.find({ email: "a@example.com" }).explain("executionStats")
```

Look for:
- `COLLSCAN` (bad for large collections)
- `IXSCAN` (good)
- `totalDocsExamined` vs returned count

### 4.5 When indexes slow down writes
Theory:
- On insert/update/delete, MongoDB must also update each index.
- More indexes = more work per write + more disk usage.
- Indexes also consume RAM (working set).

---

## 5) Aggregation Framework (Deeper Concepts)

### 5.1 What is aggregation? (theory)
Aggregation is MongoDB’s server-side data processing:
- filtering
- transforming documents
- grouping/summarizing
- joining across collections
- computing derived values

Compared to `find()`:
- Aggregation can reshape documents and compute metrics
- Typically heavier but much more powerful

### 5.2 Pipeline concept
Pipeline stages run in order, each stage outputs documents to next stage.

Key optimization idea:
- Put `$match` early to reduce data volume.
- Use `$project` to remove heavy fields early if needed.

### 5.3 Important stages

```javascript name=mongo_aggregation_stages_deep.js
db.orders.aggregate([
  { $match: { status: "PAID" } },
  { $unwind: "$items" },
  {
    $group: {
      _id: "$items.sku",
      revenue: { $sum: { $multiply: ["$items.qty", "$items.price"] } }
    }
  },
  { $sort: { revenue: -1 } },
  { $project: { _id: 0, sku: "$_id", revenue: 1 } }
])
```

### 5.4 `$lookup` (join) theory
`$lookup` performs a left outer join:
- it matches documents from “local” to “foreign”
- output is an array field `as: "..."`

Performance depends heavily on:
- index on foreign field (`foreignField`)
- cardinality (how many matches)
- pipeline complexity

---

## 6) Embedded vs Referenced Documents (Theory + Decision Rules)

### Embedding (denormalization)
**Pros**
- Fast reads (one document fetch)
- Atomic updates within a single document (single-document atomicity)
- Simple modeling when bounded

**Cons**
- Document can grow large
- Updating embedded data can rewrite large documents
- Duplicated data can become inconsistent if not managed

### Referencing (normalization)
**Pros**
- Avoid doc bloat
- Flexible relationships
- Update referenced data once, read everywhere

**Cons**
- More round trips or `$lookup`
- Consistency across docs may require transactions for strict correctness

### Decision rules
- If you always fetch together and bounded → embed
- If it grows unbounded or shared among many parents → reference

---

## 7) Schema Design Best Practices (More Theory)

### 7.1 Design for the query workload
MongoDB is optimized when:
- you can answer key queries with 1–2 reads
- you have indexes matching query filters and sorts

### 7.2 Avoid unnecessary nesting
Theory:
- Deep nesting complicates updates (`$set` paths), indexing, and can increase doc size.
- Keep a balance: embed where it’s natural and bounded.

### 7.3 Manage document growth
Use patterns like:
- Separate collection for “events/logs”
- Bucket pattern (group events by time window)
- Store large blobs externally

### 7.4 Handle relationships properly
- One-to-few: embed
- One-to-many (large): reference
- Many-to-many: linking collection

### 7.5 Plan for scalability
- Avoid “hot” counters updated in a single doc by many clients.
- Think about how you’ll shard later (if needed): pick shard keys carefully.

---

## 8) Transactions (Basics) — Replica Set required (More Theory)

### 8.1 What is a transaction?
A transaction groups multiple operations (across multiple documents/collections) into one atomic unit:
- all commit or all rollback

### 8.2 Why replica set is required
Transactions rely on replication machinery (oplog) and consistent snapshots; hence standalone servers don’t support multi-document transactions.

### 8.3 When transactions are required (vs not)
**Required**
- Money transfer-like operations
- Inventory decrement + order create where overselling is unacceptable
- Multi-collection invariants

**Not required**
- Simple logging/event ingestion
- Use cases tolerant to eventual consistency
- Single-document updates (already atomic)

### 8.4 ACID overview in MongoDB
- Atomicity: ensured within transaction
- Consistency: validation + constraints + your logic
- Isolation: snapshot-style; concurrent reads don’t see partial writes
- Durability: depends on write concern (e.g., majority)

---

## 9) Data Validation (More Theory)

### Why validation matters in schema-flexible DBs
Without validation:
- data types drift (string vs number)
- missing required fields breaks app assumptions
- indexes become less effective (mixed types)

MongoDB validation gives “schema discipline” where needed.

**Example (JSON schema validator)**
```javascript name=mongo_validation_deep.js
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["email", "status"],
      properties: {
        email: { bsonType: "string", pattern: "^.+@.+\\..+$" },
        status: { enum: ["ACTIVE", "SUSPENDED", "DELETED"] },
        createdAt: { bsonType: "date" }
      }
    }
  }
})
```

---

## 10) MongoDB Security Basics (More Theory)

### 10.1 Authentication
Ensures “who are you?”
- username/password (SCRAM)
- x.509 certs
- cloud IAM integrations (Atlas options)

### 10.2 Authorization (Roles)
Ensures “what are you allowed to do?”
Principle of least privilege:
- app user: readWrite on one DB
- admin user: separate, restricted access

### 10.3 Network controls (IP allowlist / firewall)
Even with auth, you still protect network access:
- only allow app servers
- use private networking if possible

### 10.4 Never expose DB publicly
Common breach pattern:
- DB exposed on public IP
- weak or no auth
- data exfiltration

### 10.5 Secrets management
- environment variables
- secret manager (AWS Secrets Manager / GCP Secret Manager / Vault)
- rotate credentials

---

## 11) Performance Optimization Basics (More Theory)

### 11.1 Index strategy: match your query shape
Best indexes match:
- equality filters first (e.g., `customerId`)
- then range/sort fields (e.g., `createdAt`)
Example:
```javascript name=mongo_perf_index_strategy.js
db.orders.createIndex({ customerId: 1, createdAt: -1 })
```

### 11.2 Avoid full collection scans
Use `explain()` and check:
- plan uses `IXSCAN`
- docs examined is near docs returned

### 11.3 Projection wisely
Returning fewer fields reduces:
- disk fetch (sometimes)
- network cost
- driver decoding time

### 11.4 Monitor slow queries
- profiler
- logs
- Atlas tools (Performance Advisor)

### 11.5 Document size optimization
- keep documents “tight”
- split large rarely-needed fields into separate collection if needed

---

## 12) PyMongo with Python (More Theory + Patterns)

### 12.1 Why PyMongo patterns matter
In production you care about:
- connection pooling
- timeouts
- error handling
- serialization

### 12.2 Connecting (with good defaults)
```python name=pymongo_connection_best_practice.py
import os
from pymongo import MongoClient

client = MongoClient(
    os.environ["MONGODB_URI"],
    serverSelectionTimeoutMS=5000,  # fail fast
    retryWrites=True
)
db = client["shop"]
```

### 12.3 CRUD recap + ObjectId handling
```python name=pymongo_objectid_theory.py
from bson import ObjectId

# ObjectId is a binary type; for API JSON you must convert it to str.
oid = ObjectId("65f000000000000000000000")
```

### 12.4 Error handling theory
Common cases:
- Duplicate unique fields → `DuplicateKeyError`
- network / failover issues → timeouts; retry policies matter
- validation errors → `WriteError` / `OperationFailure` depending on context

### 12.5 Flask integration theory
Keep:
- one global `MongoClient` per process (it manages pooling)
Avoid:
- creating a new client per request (slow, wasteful)

Minimal example:
```python name=flask_pymongo_minimal.py
import os
from flask import Flask, jsonify, request
from pymongo import MongoClient
from bson import ObjectId

app = Flask(__name__)
client = MongoClient(os.environ["MONGODB_URI"])
db = client["shop"]
users = db["users"]

def serialize(doc):
    doc = dict(doc)
    doc["_id"] = str(doc["_id"])
    return doc

@app.get("/users/<id>")
def get_user(id):
    doc = users.find_one({"_id": ObjectId(id)}, {"email": 1, "status": 1})
    if not doc:
        return jsonify({"error": "not found"}), 404
    return jsonify(serialize(doc))
```

---

# Next step (to make this even more “comprehensive” in the way you want)
If you confirm these 3 items, I can add *even more theory exactly where it matters* (storage internals, query planner, index prefixes, write concerns, read concerns, transaction caveats, etc.) and produce “exam-ready notes”.

1) Which database environment: **MongoDB Atlas** or **local**?  
2) Which DB flavor for deployment: **replica set** already or single node?  
3) Your goal: **interview prep**, **backend dev**, or **data engineering**?

