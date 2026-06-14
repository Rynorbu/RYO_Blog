---
title: "MongoDB, The No-Nonsense Guide to Document Databases Every Dev Needs"
published: 2026-04-21
description: "A simplified guide to MongoDB fundamentals."
# image: "../../assets/images/my_photo.jpg"
tags: ["DBMS"]
category: MongoDB
draft: false
---

# Unit III: Document Stores (MongoDB)

---

## Title

1. **"MongoDB Unlocked: The No-Nonsense Guide to Document Databases Every Dev Needs"**
2. **"Forget Rows & Columns: Here's Why MongoDB's Document Model Will Change How we Think About Data"**
3. **"From JSON to Genius: A Fun, Deep Dive into MongoDB and the World of Document Stores"**

---

## The Hook: Why Should You Care?

Imagine we're building the next big social media app. Every user has a profile, but some users have 2 phone numbers, others have 10. Some have a bio, others don't. Some link 5 social accounts, others link none.

In a traditional relational database (SQL), we'd be juggling a dozen tables, foreign keys, and JOIN queries just to display one user's profile. It's like trying to fit everyone's luggage into identical-sized boxes, frustrating, wasteful, and rigid.

**MongoDB says: "What if the box just… shaped itself to the luggage?"**

That's the magic of **document stores** flexible, powerful, and built for the messy, real-world data that modern applications actually produce. Whether you're building e-commerce platforms, content management systems, real-time analytics, or IoT applications, MongoDB is a tool worth knowing deeply.

Let's break it all down, from the ground up.

---

## 3.1 Introduction to Document Databases

### 3.1.1 Concept and Characteristics of Document Stores

A **document store** (or document-oriented database) is a type of NoSQL database that stores data as **semi-structured documents**, typically in **JSON**, **BSON**, or **XML** format.

Think of each document as a **self-contained folder** that holds everything you need to know about one "thing" (a user, a product, an order), all in one place, without needing to look elsewhere.

**Key Characteristics:**

- **Schema flexibility**: Documents in the same collection don't need identical fields
- **Nested/embedded data**: Related data lives together inside one document
- **Human-readable format**: JSON-like structure is intuitive for developers
- **Rich query support**: Query on any field, including deeply nested ones
- **Horizontal scalability**: Designed to scale out across many servers
- **High performance**: Optimized for read/write-heavy workloads

> **Analogy:** A relational database is like a spreadsheet, every row must have the same columns. A document store is like a filing cabinet of Word documents, each file can have its own structure, headings, and content.

---

### 3.1.2 Comparison with Relational and Key-Value Databases

| Feature | Relational (SQL) | Key-Value Store | Document Store (MongoDB) |
|---|---|---|---|
| **Data Format** | Tables (rows & columns) | Key → Blob/String | Key → JSON/BSON Document |
| **Schema** | Fixed, strict schema | Schema-less | Flexible schema |
| **Query Power** | Very rich (SQL) | Minimal (by key only) | Rich (by any field) |
| **Relationships** | JOINs across tables | None (manual) | Embedded or referenced |
| **Scalability** | Vertical (scale up) | Horizontal (scale out) | Horizontal (scale out) |
| **ACID Transactions** | Full support | Limited | Multi-document support |
| **Best For** | Structured, relational data | Caching, sessions, simple lookups | Hierarchical, varied data |
| **Examples** | MySQL, PostgreSQL | Redis, DynamoDB | MongoDB, CouchDB |

**When to choose what:**

- **Relational** → Banking, ERP systems, structured reporting
- **Key-Value** → Caching layers, session storage, leaderboards
- **Document Store** → User profiles, product catalogs, content management, event logging

---

### 3.1.3 Use Cases and Applications

MongoDB thrives in scenarios where data is **complex, variable, or rapidly evolving**:

1. **Content Management Systems (CMS)**: Articles, blogs, pages all have different fields
2. **E-commerce Product Catalogs**: A shirt has size/color; a laptop has RAM/CPU specs
3. **User Profile Management**: Social platforms with varying profile attributes
4. **Real-Time Analytics**: IoT sensor data, clickstream analysis
5. **Mobile Applications**: Offline-first apps needing flexible sync
6. **Gaming**: Player state, inventory, achievements stored per user
7. **Healthcare**: Patient records with heterogeneous data
8. **Logistics & Supply Chain**: Tracking packages with varied metadata

---

## 3.2 MongoDB Architecture

### 3.2.1 Core Components

MongoDB's architecture has three primary processes that work together in production deployments:

**1. `mongod` (MongoDB Daemon)**

- The **primary database process** — the workhorse of MongoDB
- Handles all data storage, retrieval, and management
- Listens for connections from clients (default port: **27017**)
- Each `mongod` instance manages its own data files on disk

**2. `mongos` (MongoDB Shard Router)**

- Acts as a **query router** in sharded cluster deployments
- Clients connect to `mongos`, which routes queries to the correct shard(s)
- Abstracts the complexity of sharding from application code
- Does **not** store data itself: it's a traffic controller

**3. Config Servers**

- Store the **cluster's metadata**: which data lives on which shard
- In production, run as a **replica set** (usually 3 config servers)
- `mongos` consults config servers to route queries correctly
- Critical component, losing all config servers = losing routing information

> **Analogy:** Think of a large library system. `mongod` instances are the individual branch libraries (storing books). `mongos` is the central catalog desk (telling you which branch has your book). Config servers are the master catalog records (the map of what's where).

---

### 3.2.2 Storage Engines

A **storage engine** is the component that manages how data is stored on disk and in memory.

**WiredTiger (Default since MongoDB 3.2)**

- **Document-level concurrency control**: multiple clients can modify different documents simultaneously
- **Compression**: Snappy (default) or zlib compression reduces disk usage by up to 80%
- **MVCC (Multi-Version Concurrency Control)**: Readers don't block writers; writers don't block readers
- **Journaling**: Write-ahead log (WAL) ensures crash recovery
- **Best for:** General-purpose production workloads

**In-Memory Storage Engine**

- Stores **all data in RAM**: no disk persistence
- Extremely low latency reads and writes
- Data is **lost on shutdown**: not suitable for durable storage
- **Best for:** Real-time analytics, caching, high-speed temporary data processing (e.g., leaderboards that reset)

---

### 3.2.3 Replication and Sharding Concepts

**Replication** = Copying data across multiple servers for **high availability**

- A **replica set** is a group of `mongod` instances that maintain the same dataset
- One **Primary** node handles all writes; **Secondary** nodes replicate the primary's data
- If the primary fails, an automatic **election** promotes a secondary to primary
- Provides **fault tolerance** and can serve reads from secondaries

**Sharding** = Distributing data across multiple servers for **horizontal scalability**

- Data is split into **chunks** based on a **shard key**
- Each chunk is stored on a different **shard** (which is itself a replica set)
- Allows MongoDB to handle datasets larger than what one server can hold
- `mongos` routes queries to the appropriate shard(s)

---

## 3.3 MongoDB Data Model

### 3.3.1 BSON (Binary JSON) Format

**BSON** stands for **Binary JSON**. It's the format MongoDB uses internally to store and transmit documents.

**Why not just use JSON?**

| Feature | JSON | BSON |
|---|---|---|
| **Format** | Text (human-readable) | Binary (machine-optimized) |
| **Data Types** | Limited (string, number, bool, null, array, object) | Extended (Date, Binary, ObjectId, Decimal128, etc.) |
| **Performance** | Slower to parse | Faster to encode/decode |
| **Size** | Smaller for simple data | Slightly larger, but traversal is faster |
| **Special Types** | None | ObjectId, ISODate, NumberLong, Regex, etc. |

**Key BSON Data Types:**

- `ObjectId`: 12-byte unique identifier (auto-generated `_id`)
- `Date`: 64-bit integer representing milliseconds since Unix epoch
- `Binary`: Raw binary data (images, files)
- `Decimal128`: High-precision decimal numbers (financial data)
- `Regular Expression`: Native regex support
- `Array`: Ordered list of values
- `Embedded Document`: A document nested inside another

---

### 3.3.2 Document Structure and Embedded Documents

A MongoDB **document** is a set of **field-value pairs** (like a JSON object):

```json
{
  "_id": ObjectId("64a7f3b2c1234567890abcde"),
  "name": "Tenzin Dorji",
  "email": "tenzin@example.bt",
  "age": 28,
  "address": {
    "street": "Norzin Lam",
    "city": "Thimphu",
    "country": "Bhutan"
  },
  "hobbies": ["hiking", "photography", "archery"],
  "orders": [
    { "item": "Kira", "price": 1200, "date": ISODate("2024-01-15") },
    { "item": "Gho", "price": 950,  "date": ISODate("2024-03-22") }
  ]
}
```

**Key concepts:**

- **`_id` field**: Every document must have one. MongoDB auto-generates an `ObjectId` if you don't provide it. It's the primary key.
- **Embedded documents**: The `address` field above is a nested document. Related data lives together.
- **Arrays**: The `hobbies` and `orders` fields are arrays. Arrays can hold primitives or full embedded documents.
- **Max document size**: **16 MB** per document (BSON limit)

**Embedding vs. Referencing:**

| Approach | When to Use | Example |
|---|---|---|
| **Embed** | Data is accessed together; one-to-few relationships | User + Address |
| **Reference** | Data is shared; one-to-many with large arrays | Blog Post + Comments (millions) |

---

### 3.3.3 Collections and Databases

**Hierarchy in MongoDB:**

```
MongoDB Server
  └── Database (e.g., "shopDB")
        └── Collection (e.g., "products")
              └── Document (e.g., one product record)
```

- **Database** A logical grouping of collections. One MongoDB instance can run multiple databases.
- **Collection** A grouping of documents (analogous to a SQL table, but schema-flexible)
- **Document** The individual data record (analogous to a SQL row)

**Important differences from SQL:**

- Collections do **not enforce a schema** by default (though you can add validation)
- No need to define columns before inserting data
- Collections are created **implicitly** when you first insert a document

---

### 3.3.4 Schema Design Patterns and Best Practices

Even though MongoDB is schema-flexible, **thoughtful schema design is critical** for performance.

**Common Design Patterns:**

**1. Embedded Document Pattern**

- Nest related data inside the parent document
- Best for: data always accessed together, one-to-one or one-to-few relationships

```json
{ "user": "Pema", "address": { "city": "Paro" } }
```

**2. Bucket Pattern**

- Group related time-series or streaming data into "buckets"
- Best for: IoT sensor readings, log data

```json
{ "sensor_id": "T01", "date": "2024-06-01", "readings": [22.1, 22.3, 22.0, ...] }
```

**3. Outlier Pattern**

- Handle documents with unusually large arrays (e.g., a celebrity with millions of followers)
- Add an `has_extras` flag and store overflow in a separate document

**4. Computed Pattern**

- Pre-compute expensive values (totals, averages) and store them
- Reduces read-time computation at the cost of write-time overhead

**5. Subset Pattern**

- Store a **subset** of related data in the main document (e.g., last 10 reviews)
- Store the full dataset in a separate collection

**Best Practices:**

- Model data for **how your application queries it**, not how it exists in the real world
- Avoid unbounded array growth, use references when arrays could grow infinitely
- Use meaningful, consistent field names (camelCase convention)
- Index fields that appear in query filters, sorts, and join conditions

---

## 3.4 CRUD Operations in MongoDB

> CRUD = **C**reate, **R**ead, **U**pdate, **D**elete, the four fundamental data operations.

### 3.4.1 Insert Operations

**`insertOne()`** Insert a single document:

```javascript
db.students.insertOne({
  name: "Karma Wangchuk",
  grade: "A",
  enrolled: true
});
// Returns: { acknowledged: true, insertedId: ObjectId("...") }
```

**`insertMany()`** Insert multiple documents at once:

```javascript
db.students.insertMany([
  { name: "Sonam", grade: "B" },
  { name: "Deki",  grade: "A+" },
  { name: "Rinzin", grade: "C" }
]);
// Returns: { acknowledged: true, insertedIds: { 0: ObjectId("..."), ... } }
```

**Key notes:**

- If `_id` is not provided, MongoDB generates an `ObjectId` automatically
- `insertMany` is **ordered by default** — stops on first error. Use `{ ordered: false }` to continue on error.

---

### 3.4.2 Read Operations

**`findOne()`** Returns the first matching document:

```javascript
db.students.findOne({ name: "Karma Wangchuk" });
```

**`find()`** Returns a **cursor** to all matching documents:

```javascript
db.students.find({ grade: "A" });
// Add .toArray() or .forEach() to iterate
```

**Projection** Specify which fields to return (1 = include, 0 = exclude):

```javascript
db.students.find(
  { grade: "A" },              // filter
  { name: 1, grade: 1, _id: 0 } // projection: show name & grade, hide _id
);
```

**Useful cursor methods:**

```javascript
db.students.find().limit(5)         // return max 5 documents
db.students.find().skip(10)         // skip first 10 documents
db.students.find().sort({ name: 1}) // sort by name ascending (−1 = descending)
db.students.find().count()          // count results
```

---

### 3.4.3 Update Operations

**`updateOne()`** Updates the **first** matching document:

```javascript
db.students.updateOne(
  { name: "Sonam" },           // filter
  { $set: { grade: "A" } }     // update operator
);
```

**`updateMany()`** Updates **all** matching documents:

```javascript
db.students.updateMany(
  { enrolled: true },
  { $set: { semester: "Spring 2025" } }
);
```

**`replaceOne()`** Replaces the **entire** document (except `_id`):

```javascript
db.students.replaceOne(
  { name: "Deki" },
  { name: "Deki Lhamo", grade: "A+", year: 2 }
);
```

**Common Update Operators:**

| Operator | Purpose | Example |
|---|---|---|
| `$set` | Set a field value | `{ $set: { age: 25 } }` |
| `$unset` | Remove a field | `{ $unset: { tempField: "" } }` |
| `$inc` | Increment a number | `{ $inc: { score: 10 } }` |
| `$push` | Add to an array | `{ $push: { tags: "mongodb" } }` |
| `$pull` | Remove from an array | `{ $pull: { tags: "old" } }` |
| `$addToSet` | Add to array (no duplicates) | `{ $addToSet: { roles: "admin" } }` |
| `$rename` | Rename a field | `{ $rename: { "nm": "name" } }` |

**Upsert** Insert if no match found:

```javascript
db.students.updateOne(
  { name: "NewStudent" },
  { $set: { grade: "B" } },
  { upsert: true }           // creates document if it doesn't exist
);
```

---

### 3.4.4 Delete Operations

**`deleteOne()`** Deletes the **first** matching document:

```javascript
db.students.deleteOne({ name: "Rinzin" });
```

**`deleteMany()`** Deletes **all** matching documents:

```javascript
db.students.deleteMany({ enrolled: false });
// Delete ALL documents in collection:
db.students.deleteMany({});
```

> **Warning:** `deleteMany({})` with an empty filter deletes **all documents** in the collection. Always double-check your filter!

---

## 3.5 MongoDB Query Language

### 3.5.1 Query Operators

MongoDB's query language uses **operators** (prefixed with `$`) to express conditions.

**Comparison Operators:**

| Operator | Meaning | Example |
|---|---|---|
| `$eq` | Equal to | `{ age: { $eq: 25 } }` or shorthand `{ age: 25 }` |
| `$ne` | Not equal to | `{ status: { $ne: "inactive" } }` |
| `$gt` | Greater than | `{ score: { $gt: 80 } }` |
| `$gte` | Greater than or equal | `{ score: { $gte: 80 } }` |
| `$lt` | Less than | `{ price: { $lt: 100 } }` |
| `$lte` | Less than or equal | `{ price: { $lte: 100 } }` |
| `$in` | Value in array | `{ status: { $in: ["active", "pending"] } }` |
| `$nin` | Value NOT in array | `{ role: { $nin: ["guest", "banned"] } }` |

**Logical Operators:**

| Operator | Meaning | Example |
|---|---|---|
| `$and` | All conditions true | `{ $and: [{ age: { $gt: 18 } }, { enrolled: true }] }` |
| `$or` | At least one condition true | `{ $or: [{ grade: "A" }, { grade: "A+" }] }` |
| `$not` | Negates a condition | `{ age: { $not: { $gt: 65 } } }` |
| `$nor` | None of the conditions true | `{ $nor: [{ status: "banned" }, { age: { $lt: 13 } }] }` |

**Element Operators:**

```javascript
{ field: { $exists: true } }   // document has this field
{ field: { $type: "string" } } // field is of type string
```

**Array Operators:**

```javascript
{ tags: { $all: ["mongodb", "nosql"] } }  // array contains ALL these values
{ tags: { $size: 3 } }                    // array has exactly 3 elements
{ scores: { $elemMatch: { $gt: 80, $lt: 90 } } } // element matching multiple conditions
```

---

### 3.5.2 Aggregation Framework

The **Aggregation Framework** is MongoDB's most powerful feature for data processing — think of it as the MongoDB equivalent of SQL's `GROUP BY`, `HAVING`, `JOIN`, and more, combined into a flexible pipeline.

**Core Concept: The Pipeline:**
Data flows through a series of **stages**, each transforming the documents:

```
Collection → [$match] → [$group] → [$sort] → [$limit] → Result
```

**Common Pipeline Stages:**

| Stage | Purpose | SQL Equivalent |
|---|---|---|
| `$match` | Filter documents | `WHERE` |
| `$group` | Group and aggregate | `GROUP BY` |
| `$sort` | Sort results | `ORDER BY` |
| `$limit` | Limit output count | `LIMIT` |
| `$skip` | Skip documents | `OFFSET` |
| `$project` | Shape output fields | `SELECT` |
| `$lookup` | Join with another collection | `JOIN` |
| `$unwind` | Deconstruct array into separate docs | (no direct equivalent) |
| `$addFields` | Add computed fields | computed columns |
| `$count` | Count documents | `COUNT(*)` |

**Example: Total sales by product category:**

```javascript
db.orders.aggregate([
  { $match: { status: "completed" } },           // filter completed orders
  { $group: {
      _id: "$category",                            // group by category
      totalRevenue: { $sum: "$price" },            // sum prices
      orderCount:   { $count: {} }                 // count orders
  }},
  { $sort: { totalRevenue: -1 } },               // sort descending
  { $limit: 5 }                                   // top 5 categories
]);
```

**Common Aggregation Expressions:**

| Expression | Purpose |
|---|---|
| `$sum` | Sum of values |
| `$avg` | Average of values |
| `$min` / `$max` | Min/max value |
| `$count` | Count of documents |
| `$push` | Collect values into array |
| `$first` / `$last` | First/last value in group |
| `$concat` | String concatenation |
| `$toUpper` / `$toLower` | String case conversion |

---

### 3.5.3 Text Search and Geospatial Queries

**Text Search:**

1. Create a text index on the field(s) to search:

```javascript
db.articles.createIndex({ title: "text", body: "text" });
```

1. Query using `$text`:

```javascript
db.articles.find({ $text: { $search: "MongoDB document database" } });
// Sort by relevance score:
db.articles.find(
  { $text: { $search: "MongoDB" } },
  { score: { $meta: "textScore" } }
).sort({ score: { $meta: "textScore" } });
```

**Geospatial Queries:**

MongoDB supports location-based queries using **GeoJSON** format.

1. Store location data in GeoJSON format:

```javascript
{
  name: "Tashichho Dzong",
  location: {
    type: "Point",
    coordinates: [89.6390, 27.4716]  // [longitude, latitude]
  }
}
```

1. Create a geospatial index:

```javascript
db.places.createIndex({ location: "2dsphere" });
```

1. Find places near a point:

```javascript
db.places.find({
  location: {
    $near: {
      $geometry: { type: "Point", coordinates: [89.64, 27.47] },
      $maxDistance: 5000  // within 5km
    }
  }
});
```

**Other geospatial operators:** `$geoWithin` (within a shape), `$geoIntersects` (intersects a shape), `$centerSphere` (spherical radius)

---

### 3.5.4 Indexes and Query Optimization

**What is an Index?**
An index is a **data structure** that holds a small portion of the collection's data in an easy-to-traverse form. Without indexes, MongoDB must do a **collection scan** (read every document), slow for large datasets.

> **Analogy:** An index is like a book's index at the back, instead of reading every page to find "sharding," you look it up alphabetically and go directly to the right page.

**Types of Indexes:**

| Index Type | Description | Use Case |
|---|---|---|
| **Single Field** | Index on one field | `{ age: 1 }` |
| **Compound** | Index on multiple fields | `{ lastName: 1, firstName: 1 }` |
| **Multikey** | Index on array field elements | Automatically created for arrays |
| **Text** | Full-text search index | Searching string content |
| **Geospatial (2dsphere)** | Location-based queries | Proximity searches |
| **Hashed** | Hash of field value | Used for sharding |
| **Partial** | Index only documents matching a filter | Saving space |
| **TTL (Time-To-Live)** | Auto-delete documents after a time | Sessions, logs, caches |
| **Unique** | Enforce unique field values | Email addresses |

**Creating Indexes:**

```javascript
db.users.createIndex({ email: 1 }, { unique: true });   // unique index
db.logs.createIndex({ createdAt: 1 }, { expireAfterSeconds: 86400 }); // TTL: 1 day
db.orders.createIndex({ customerId: 1, orderDate: -1 }); // compound index
```

**Query Optimization with `explain()`:**

```javascript
db.users.find({ age: { $gt: 25 } }).explain("executionStats");
// Look for: "COLLSCAN" (bad — no index) vs "IXSCAN" (good — uses index)
// Check: nReturned, totalDocsExamined, executionTimeMillis
```

**Index Best Practices:**

- Create indexes on fields used in **filters, sorts, and joins**
- Follow the **ESR rule** for compound indexes: **E**quality → **S**ort → **R**ange
- Avoid over-indexing, indexes consume memory and slow down writes
- Use **covered queries** (all needed fields are in the index, no document fetch needed)

---

## 3.6 MongoDB Transactions and Consistency

### 3.6.1 Multi-Document ACID Transactions

Before MongoDB 4.0, atomic operations were only guaranteed at the **single-document level**. Now, MongoDB supports full **multi-document ACID transactions** across multiple collections and databases.

**ACID Explained:**

| Property | Meaning | MongoDB Guarantee |
|---|---|---|
| **Atomicity** | All operations succeed or all fail together | Yes (multi-document) |
| **Consistency** | Data moves from one valid state to another | Yes |
| **Isolation** | Transactions don't interfere with each other | Snapshot isolation |
| **Durability** | Committed data survives crashes | With journaling |

**Using Transactions:**

```javascript
const session = client.startSession();
session.startTransaction();

try {
  // Debit account A
  db.accounts.updateOne(
    { _id: "accountA" },
    { $inc: { balance: -500 } },
    { session }
  );
  // Credit account B
  db.accounts.updateOne(
    { _id: "accountB" },
    { $inc: { balance: 500 } },
    { session }
  );

  await session.commitTransaction();
} catch (error) {
  await session.abortTransaction(); // rollback on error
} finally {
  session.endSession();
}
```

> **Note:** Transactions in MongoDB have a **60-second time limit** by default and require a **replica set** (minimum setup). They also carry a performance overhead, use them only when truly needed.

---

### 3.6.2 Read and Write Concerns

**Read Concern** Controls **how current** the data is when reading:

| Level | Description |
|---|---|
| `local` | Returns data from local node (may not be majority-committed) default |
| `majority` | Returns only data acknowledged by majority of replica set members |
| `linearizable` | Guarantees the most up-to-date data (slowest) |
| `available` | Fastest; may return stale data (useful in sharded clusters) |
| `snapshot` | Returns data from a consistent snapshot (used in transactions) |

**Write Concern** Controls **how many nodes must acknowledge** a write before it's considered successful:

| Level | Description |
|---|---|
| `{ w: 1 }` | Primary acknowledges (default) fastest |
| `{ w: "majority" }` | Majority of replica set members acknowledge safer |
| `{ w: 0 }` | Fire and forget — no acknowledgment (not recommended for critical data) |
| `{ j: true }` | Write must be committed to journal before acknowledgment |
| `{ wtimeout: 5000 }` | Max wait time (ms) for write concern acknowledgment |

```javascript
db.orders.insertOne(
  { item: "Widget", qty: 100 },
  { writeConcern: { w: "majority", j: true, wtimeout: 5000 } }
);
```

---

### 3.6.3 Consistency Models in Distributed Environments

In distributed systems, there's a fundamental trade-off defined by the **CAP Theorem**:

> A distributed system can only guarantee **two of three**:
>
> - **C**onsistency: All nodes see the same data at the same time
> - **A**vailability: Every request gets a response
> - **P**artition Tolerance: System works despite network partitions

**MongoDB's Position:**

- MongoDB is primarily a **CP system** (Consistency + Partition Tolerance)
- With `w: "majority"` + `readConcern: "majority"` → strong consistency
- With `w: 1` + `readConcern: "local"` → eventual consistency (higher availability)
- **Tunable consistency** — you control the trade-off via read/write concerns

**Eventual Consistency in Replica Sets:**

- When a write goes to the primary, secondaries replicate it **asynchronously**
- Reading from a secondary before replication completes = stale data
- This is **eventually consistent**: the secondary *will* catch up, just not instantly

---

## 3.7 Scaling MongoDB

### 3.7.1 Replication and Replica Sets

A **Replica Set** is a group of MongoDB instances (typically 3 or more) that maintain identical copies of the data.

**Roles in a Replica Set:**

| Role | Description |
|---|---|
| **Primary** | Receives all write operations; replicates to secondaries via **oplog** |
| **Secondary** | Maintains a copy of the primary's data; can serve reads (if configured) |
| **Arbiter** | Participates in elections but holds **no data**; used to break ties |

**The Oplog (Operations Log):**

- A special capped collection on the primary
- Records every write operation in order
- Secondaries continuously read and replay the oplog to stay in sync

**Automatic Failover:**

1. Primary becomes unavailable
2. Remaining members **detect the failure** (via heartbeats every 2 seconds)
3. An **election** occurs, member with most up-to-date oplog and most votes wins
4. New primary is elected, typically within **10-30 seconds**
5. Application reconnects automatically (with MongoDB drivers)

**Minimum Recommended Setup:** 3 members (2 data-bearing + 1 arbiter, or 3 data-bearing)

---

### 3.7.2 Sharding Strategies and Shard Keys

**Sharding** distributes data across multiple machines. The **shard key** determines how data is distributed.

**Three Sharding Strategies:**

**1. Ranged Sharding**

- Documents are grouped into chunks based on contiguous ranges of the shard key
- Example: orders by `date` → chunk 1 has Jan-Mar, chunk 2 has Apr-Jun, etc.
- Efficient for range queries
- Risk of **hotspots** if writes cluster around one range (e.g., always today's date)

**2. Hashed Sharding**

- MongoDB hashes the shard key value; documents are distributed based on hash
- Example: shard key is `userId` → hash distributes users evenly across shards
- Even distribution, avoids hotspots
- Range queries are inefficient (data is scattered)

**3. Zone Sharding (Tag-Aware)**

- Define geographic or logical zones; assign chunks to specific shards
- Example: European users → EU shard; Asian users → Asia shard
- Data locality, keep data near users for compliance or performance
- More complex to configure

**Choosing a Good Shard Key:**

- **High cardinality** many distinct values (avoid boolean fields)
- **Even distribution** prevents hotspots
- **Frequently used in queries** allows `mongos` to target specific shards
- **Immutable** once set, you cannot change a document's shard key value

---

### 3.7.3 Horizontal Scaling Techniques

**Vertical Scaling (Scale Up):**

- Add more RAM, CPU, or faster storage to one server
- Has a hard limit, we can only make one machine so big
- Expensive beyond a certain point

**Horizontal Scaling (Scale Out):**

- Add more servers (shards) to the cluster
- MongoDB handles data distribution automatically
- **Near-linear scalability** 2x shards ≈ 2x throughput
- Cost-effective using commodity hardware

**Scaling Reads:**

- Configure replica set members to serve reads (read preference: `secondary`)
- Use **read preference modes**: `primary`, `primaryPreferred`, `secondary`, `secondaryPreferred`, `nearest`

**Scaling Writes:**

- Only through **sharding** — writes always go to the primary of each shard
- More shards = more primaries = more write capacity

---

### 3.7.4 Load Balancing and Data Distribution

**How `mongos` Distributes Queries:**

1. **Targeted queries** — Filter includes the shard key → `mongos` sends query to ONE shard Fast
2. **Scatter-gather queries** — No shard key in filter → `mongos` sends to ALL shards, merges results Slow

**Chunk Balancing:**

- MongoDB divides each shard's data into **chunks** (default: 128 MB max size)
- A **balancer** process (runs on config servers) monitors chunk distribution
- If one shard has too many chunks, the balancer **migrates** chunks to less-loaded shards
- Migrations happen in the **background** and are largely transparent

**Zone Balancing:**

- Assign shards to zones (geographic regions or hardware tiers)
- The balancer respects zone assignments when distributing chunks

---

## 3.8 MongoDB Ecosystem and Tools

### 3.8.1 MongoDB Atlas (Cloud Database Service)

**MongoDB Atlas** is MongoDB's fully managed cloud database service, we get MongoDB without managing servers, backups, or networking.

**Key Features:**

- **Multi-cloud support** Deploy on AWS, Google Cloud, or Azure
- **Auto-scaling** Automatically scales compute and storage based on demand
- **Global clusters** Distribute data across multiple geographic regions
- **Automated backups** Point-in-time recovery with configurable retention
- **Atlas Search** Full-text search powered by Apache Lucene, integrated natively
- **Atlas Vector Search** AI/ML embedding search for semantic similarity
- **Atlas Data Federation** Query across MongoDB, S3, and other data sources
- **Atlas Charts** Built-in data visualization
- **Atlas Triggers** Event-driven functions (serverless)
- **Security** VPC peering, IP whitelisting, encryption at rest and in transit

**Tiers:**

- **M0 Free Tier** 512 MB storage, shared resources (great for learning)
- **M2/M5** Shared tiers for development
- **M10+** Dedicated tiers for production workloads

---

### 3.8.2 Compass (GUI for MongoDB)

**MongoDB Compass** is the official graphical user interface (GUI) for MongoDB — like pgAdmin for PostgreSQL, but for MongoDB.

**What you can do in Compass:**

- **Browse and explore** databases, collections, and documents visually
- **Build and run queries** without writing code (visual query builder)
- **Create and manage indexes** with performance impact estimates
- **Run aggregation pipelines** with a visual stage-by-stage builder
- **Schema analysis** Compass analyzes your collection and shows field types, value distributions
- **Explain plans** Visualize how queries execute
- **Real-time performance** Monitor server metrics (operations, memory, connections)
- **Import/Export** data (JSON, CSV)

**Editions:**

- **Compass** Full-featured (free)
- **Compass Readonly** Read-only access for analysts

---

### 3.8.3 Mongoose (ODM for Node.js)

**Mongoose** is an **Object Document Mapper (ODM)** for Node.js, it provides a schema-based layer on top of MongoDB's flexible model.

> **Analogy:** If MongoDB is a free-form filing cabinet, Mongoose is the colour-coded folder system you put inside it, adding structure, validation, and rules.

**Key Features:**

- **Schema definition** Define the shape of documents in your application layer
- **Validation** Automatically validate data before saving (required fields, min/max, regex, etc.)
- **Middleware (Hooks)* Run code before/after operations (pre-save, post-find, etc.)
- **Virtual properties** Computed fields not stored in the database
- **Populate** Reference-style joins between documents
- **Plugins** Reusable functionality across schemas

**Example — Defining and Using a Mongoose Model:**

```javascript
const mongoose = require('mongoose');

// 1. Define Schema
const studentSchema = new mongoose.Schema({
  name:     { type: String, required: true, trim: true },
  email:    { type: String, required: true, unique: true, lowercase: true },
  grade:    { type: String, enum: ['A', 'B', 'C', 'D', 'F'] },
  gpa:      { type: Number, min: 0, max: 4.0 },
  enrolled: { type: Boolean, default: true },
  createdAt:{ type: Date, default: Date.now }
});

// 2. Create Model
const Student = mongoose.model('Student', studentSchema);

// 3. Use Model
const newStudent = new Student({ name: 'Pema', email: 'pema@cst.bt', grade: 'A', gpa: 3.8 });
await newStudent.save();

// 4. Query
const topStudents = await Student.find({ gpa: { $gte: 3.5 } }).sort({ gpa: -1 });
```

---

### 3.8.4 MongoDB Charts and BI Connector

**MongoDB Charts:**

- Native **data visualization tool** for MongoDB data
- Available within MongoDB Atlas (no export needed)
- Create: bar charts, line charts, scatter plots, heat maps, geo maps, word clouds
- **Live data** Charts update in real-time as underlying data changes
- **Dashboards** Combine multiple charts into interactive dashboards
- **Embedding** Embed charts into your own applications
- **Filters** Users can filter charts interactively

**MongoDB BI Connector:**

- Translates **SQL queries** into MongoDB queries
- Allows **SQL-based BI tools** (Tableau, Power BI, Excel, Looker) to connect directly to MongoDB
- Uses a **MySQL-compatible interface** BI tools think they're talking to MySQL
- Ideal for organizations with existing BI infrastructure wanting to leverage MongoDB data

**When to use which:**

| Tool | Use Case |
|---|---|
| **MongoDB Charts** | Quick dashboards, Atlas-native, live data |
| **BI Connector** | Enterprise BI tools, SQL-familiar analysts, complex reporting |

---

## TL;DR: The Cheat Sheet Summary

**Document Stores:**

- Store data as flexible JSON-like documents (not rows/columns)
- Schema-flexible, hierarchical, developer-friendly
- MongoDB is the world's most popular document database

**Architecture:**

- `mongod` = data process | `mongos` = query router | Config Servers = metadata store
- WiredTiger = default engine (compression, MVCC) | In-Memory = speed, no durability
- Replica sets = high availability | Sharding = horizontal scalability

**Data Model:**

- BSON extends JSON with richer types (ObjectId, Date, Decimal128)
- Embed for "accessed together" data; reference for "shared" or "large" data
- Design schema around **query patterns**, not entity relationships

**CRUD:**

- Create: `insertOne()` / `insertMany()`
- Read: `find()` / `findOne()` + projection
- Update: `updateOne()` / `updateMany()` with `$set`, `$inc`, `$push`, etc.
- Delete: `deleteOne()` / `deleteMany()`

**Queries:**

- Operators: `$eq`, `$gt`, `$lt`, `$in`, `$and`, `$or`, `$exists`, `$elemMatch`
- Aggregation pipeline: `$match → $group → $sort → $project → $lookup`
- Indexes: critical for performance; use `explain()` to diagnose slow queries

**Transactions:**

- Multi-document ACID transactions supported (requires replica set)
- Write concern controls durability; read concern controls staleness
- CAP theorem: MongoDB is tunable between consistency and availability

**Scaling:**

- Replica Sets: 1 Primary + N Secondaries + optional Arbiter; auto-failover
- Sharding: Ranged (range queries) | Hashed (even distribution) | Zone (geo-locality)
- Choose shard keys with high cardinality, even distribution, and query alignment

**Ecosystem:**

- **Atlas** = managed cloud MongoDB (free tier available)
- **Compass** = GUI for exploring and managing MongoDB
- **Mongoose** = schema/validation layer for Node.js apps
- **Charts** = native dashboards | **BI Connector** = SQL BI tool integration

---

## References

- <https://www.mongodb.com/docs/manual/>
- <https://www.mongodb.com/docs/manual/core/document/>
- <https://www.mongodb.com/docs/manual/core/aggregation-pipeline/>
- <https://www.mongodb.com/docs/manual/indexes/>
- <https://www.mongodb.com/docs/manual/core/transactions/>
- <https://www.mongodb.com/docs/manual/sharding/>
- <https://www.mongodb.com/docs/manual/replication/>
- <https://www.mongodb.com/docs/atlas/>
- <https://mongoosejs.com/docs/guide.html>
- <https://www.mongodb.com/docs/charts/>
- <https://www.mongodb.com/docs/bi-connector/current/>
- <https://www.mongodb.com/docs/compass/current/>
- <https://www.mongodb.com/docs/manual/core/wiredtiger/>

---
