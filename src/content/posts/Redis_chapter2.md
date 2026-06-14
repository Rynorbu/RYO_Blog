---
title: Redis Fundamentals
published: 2026-04-21
description: "A simplified guide to Redis fundamentals."
# image: "../../assets/images/my_photo.jpg"
tags: ["DBMS"]
category: Redis
draft: false
---

# Unit II: Key-Value Databases: Redis

### DBS302 · NoSQL Database Systems · BE Software Engineering

## Table of Contents

| # | Topic |
|---|-------|
| 2.1 | [Introduction to Key-Value Databases](#21-introduction-to-key-value-databases) |
| 2.2 | [Redis Fundamentals](#22-redis-fundamentals) |
| 2.3 | [Redis Data Structures & Algorithms](#23-redis-data-structures-and-algorithms) |
| 2.4 | [Redis Persistence & Durability](#24-redis-persistence-and-durability) |
| 2.5 | [Redis Clustering & High Availability](#25-redis-clustering-and-high-availability) |
| 2.6 | [Redis Modules & Extensions](#26-redis-modules-and-extensions) |
| 2.7 | [Redis Performance Optimization](#27-redis-performance-optimization) |
| 2.8 | [Redis Security Considerations](#28-redis-security-considerations) |

---

## 2.1 Introduction to Key-Value Databases

### 2.1.1 Concept and Architecture

A **key-value database** stores data as pairs of a unique key and its associated value, exactly like a `dict` in Python or a `HashMap` in Java.

> **Analogy: Gym Locker Room:** Each locker has a unique number (the **KEY**) and whatever we put inside is the **VALUE**. If we know the locker number, we get our stuff instantly, no searching through every locker. That's O(1) lookup.

```
KEY                           VALUE
──────────────────────────    ─────────────────────────────────
user:1001:name           →    "Alice"
session:abc123           →    "{userId: 1001, role: admin}"
product:iphone15:price   →    "1299"
leaderboard              →    [sorted list of players]
```

**Redis Architecture:** Redis follows a **Client-Server model** where clients connect via TCP on port 6379. Internally, Redis is built on three architectural pillars:

| Pillar | What it means |
|--------|--------------|
| **In-Memory Storage** | All data lives in RAM, no disk reads, sub-millisecond access |
| **Non-Blocking I/O** | Uses the *Reactor Pattern* with `epoll`/`kqueue` to handle thousands of connections on a single thread |
| **Single-Threaded Execution** | No context switching, no mutex locks, no race conditions |

**The Reactor Pattern** is the heart of Redis: it's an event-driven design where an *I/O multiplexer* monitors all client connections and dispatches events one by one to a single worker thread. The CPU never waits for slow I/O; it only works when data is actually ready.

> **Why is single-threaded fast?** Because there are no context switches, no lock contention, and the CPU cache stays "warm." Redis processes 100,000+ requests/second on a single core.

**How RESP fits in:** Clients communicate with Redis using **RESP (REdis Serialization Protocol)** a binary-safe, text-based protocol. While the Reactor Pattern is the *engine*, RESP is the *language* spoken over connections.

---

### 2.1.2 Advantages and Limitations

**Advantages:**

| Advantage | Why It Matters |
|-----------|----------------|
| **Blazing Fast** | In-memory = no disk I/O. O(1) for most operations. 1M+ ops/sec |
| **Simple Model** | No schemas, no JOINs, store and retrieve with minimal complexity |
| **Horizontally Scalable** | Redis Cluster distributes data across nodes |
| **Flexible Data Types** | 10+ types: strings, lists, sets, hashes, sorted sets, geo, HLL, and more |
| **Atomic Operations** | `INCR` is atomic, safe for concurrent counters without locks |
| **TTL Support** | Keys auto-expire, perfect for sessions, caches, rate limits |

**Limitations:**

| Limitation | Explanation |
|------------|-------------|
| **Memory-Bound** | All data lives in RAM. RAM is expensive. You can't store 500 GB in Redis |
| **No Complex Queries** | No SQL JOINs, GROUP BY, or nested WHERE clauses |
| **Limited ACID** | `MULTI/EXEC` ≠ full SQL transactions. No rollback on runtime errors |
| **Key-Based Lookup Only** | Scanning all keys is O(N) and dangerous in production |
| **Persistence Complexity** | In-memory data risks loss without careful configuration |

> **Key Insight:** Redis is **not a replacement** for PostgreSQL or MySQL. It's a **complement** use it for caching, sessions, queues, and leaderboards *alongside* a persistent relational database.

---

### 2.1.3 Common Use Cases

| Use Case | How Redis Helps | Real Company |
|----------|----------------|--------------|
| **Session Management** | Store login sessions with TTL auto-expiry | GitHub, GitLab |
| **Caching** | Cache DB results and API responses | Twitter, Instagram |
| **Rate Limiting** | Count API calls per user per time window | Stripe, GitHub API |
| **Leaderboards** | Sorted Sets rank users by score in real-time | Stack Overflow, Games |
| **Real-time Analytics** | Count events and unique visitors | YouTube view counts |
| **Message Queues** | Lists as FIFO queues for background tasks | Celery (Python) |
| **Pub/Sub** | Event broadcasting between microservices | Slack notifications |
| **Geolocation** | Find nearby drivers, restaurants, stores | Uber, Swiggy, Zomato |
| **Distributed Locks** | Prevent race conditions across servers | Payment processing |

> **Real-World Example: Amazon Cart:** When you add an item to your Amazon cart, it's stored in Redis as `cart:user:9876`. It's faster than querying a database on every page load and expires automatically after 30 days of inactivity.

---

## 2.2 Redis Fundamentals

### 2.2.1 Redis Data Model

Redis stores everything in a **flat key-value namespace** no tables, no rows, no foreign keys. Since all keys share the same space, **structured naming is critical.**

**Key Naming Convention:** `object-type:id:attribute`

```
user:1001:profile           # User profile data
user:1001:sessions          # User's active sessions
order:ORD2024001:status     # Order status
cache:homepage:trending     # Cached content
ratelimit:user:1001:api     # Rate limiting counter
```

> **Tip:** Use colons (`:`) as separators consistently. This lets you safely iterate with `SCAN 0 MATCH user:*` in production without blocking.

**Key Rules:**

| Rule | Detail |
|------|--------|
| Format | Binary-safe strings (use readable UTF-8 names) |
| Max Size | 512 MB (keep keys short — they add to memory) |
| Case Sensitive | `User:1001` ≠ `user:1001` |
| TTL | Keys can auto-expire in seconds or milliseconds |

---

### 2.2.2 Redis Data Types

#### 1. Strings

The most fundamental type. Binary-safe. Can hold text, numbers, serialized JSON, even images. Max: **512 MB**.

```bash
SET username "Alice"
SET counter 0
SET user:1001:profile '{"name":"Alice","age":25}'   # Store JSON as string

GET username          # → "Alice"
INCR counter          # → 1  (ATOMIC increment)
INCRBY counter 5      # → 6
INCRBYFLOAT price 1.5 # Float increment

# Key with expiry:
SETEX session:tok123 3600 "user:1001"   # Expires in 1 hour
TTL session:tok123                       # Returns remaining seconds
PERSIST username                         # Remove expiry
```

> **Real-World: Twitter Rate Limiting:** `SET ratelimit:user:1001:tweets 0` → `INCR` on each tweet → `EXPIRE` for 3 hours → reject when value hits 300.

---

#### 2. Lists

An ordered list of strings, a doubly linked list under the hood. Perfect for **queues** (LPUSH + RPOP) and **stacks** (LPUSH + LPOP).

```bash
LPUSH tasks "send_email"        # ["send_email"]
RPUSH tasks "generate_pdf"      # ["send_email", "generate_pdf"]

LLEN tasks                      # 2
LRANGE tasks 0 -1               # Get ALL elements
LPOP tasks                      # Remove from left
RPOP tasks                      # Remove from right

BLPOP tasks 30     # BLOCKING pop — waits up to 30s for a new task
```

> **Real-World: Instagram Task Queue:** When a user uploads a photo, a job is pushed to a Redis list. Worker processes use `BLPOP` to pick up and process jobs (resize, notify) without polling.

---

#### 3. Sets

An **unordered collection of unique strings**. Duplicates are automatically ignored. Supports powerful set math.

```bash
SADD followers:user:1001 "user:2001" "user:3001"
SMEMBERS followers:user:1001         # Get all members
SISMEMBER followers:user:1001 "user:2001"  # → 1 (exists)
SCARD followers:user:1001            # Count = 2

# Set Math:
SADD tags:article:101 "redis" "nosql" "database"
SADD tags:article:102 "redis" "caching"

SINTER tags:article:101 tags:article:102   # → {"redis"}
SUNION tags:article:101 tags:article:102   # → all unique tags
SDIFF  tags:article:101 tags:article:102   # → {"nosql","database"}
```

> **Real-World: LinkedIn "People You May Know":** `SINTER connections:alice connections:bob` returns users known by both, perfect mutual connection suggestions.

---

#### 4. Hashes

A **field-value map** stored under a single key, like a mini dictionary, or a single database row. Best for storing objects.

```bash
HSET user:1001 name "Alice" email "alice@example.com" age 28 city "Thimphu"

HGET  user:1001 name          # → "Alice"
HGETALL user:1001             # → all fields and values
HMGET user:1001 name email    # → multiple specific fields
HINCRBY user:1001 age 1       # Atomically increment age
HDEL  user:1001 city          # Delete one field
```

**Hash vs String for objects:**

| Approach | Problem |
|----------|---------|
| `SET user:1001 '{"name":"Alice","age":28}'` | To update age: read → deserialize → update → serialize → write the entire object |
| `HSET user:1001 name "Alice" age 28` | Update just one field: `HSET user:1001 age 29` — no touching other fields |

> **Real-World: GitHub Repos:** `HSET repo:torvalds/linux stars 170000 forks 49000 language "C"`, fetched with `HGETALL` in microseconds on page load.

---

#### 5. Sorted Sets (ZSets)

The **most powerful Redis type**. Like a Set, but every member has a **floating-point score**. Members are always kept sorted by score. O(log N) for most operations.

```bash
ZADD leaderboard 9500 "player:alice"
ZADD leaderboard 9800 "player:charlie"
ZADD leaderboard 8200 "player:bob"

# Top 3 (highest scores first):
ZREVRANGE leaderboard 0 2 WITHSCORES
# → charlie 9800 | alice 9500 | bob 8200

ZREVRANK leaderboard "player:alice"   # → 1 (2nd place, 0-indexed)
ZSCORE leaderboard "player:alice"     # → 9500.0
ZINCRBY leaderboard 500 "player:alice"  # alice → 10000

# Range by score:
ZRANGEBYSCORE leaderboard 8000 +inf WITHSCORES
ZCOUNT leaderboard 4500 +inf          # Count with score >= 4500
ZPOPMIN leaderboard                   # Remove and return lowest scorer
```

> **Real-World: Stack Overflow:** `ZREVRANGE reputations 0 99 WITHSCORES`, fetches the top 100 users for the "Top Users" page in real-time. No SQL sorting needed.

---

### 2.2.3 Basic Redis Commands and Operations

**Global Key Commands (work on ALL types):**

```bash
EXISTS user:1001         # Does key exist? → 1 or 0
DEL    user:1001         # Delete key
TYPE   user:1001         # Returns: string | list | set | hash | zset
EXPIRE user:1001 3600    # Set TTL in seconds
TTL    user:1001         # Remaining TTL (-1 = no TTL, -2 = not found)
PERSIST user:1001        # Remove TTL (make permanent)

# Safe key iteration:
SCAN 0 MATCH user:* COUNT 100   # Use this
KEYS user:*                      # NEVER in production: O(N) blocking
```

> **NEVER use `KEYS *` in production!** It's O(N) and blocks ALL other Redis operations while scanning. Use `SCAN` instead, it iterates in small batches without blocking.

**Server Commands:**

```bash
PING              # → PONG (test connection)
INFO memory       # Memory-specific stats
DBSIZE            # Total key count
SLOWLOG GET 10    # Last 10 slow commands
CONFIG SET maxmemory 2gb   # Live config update (no restart!)
FLUSHDB           # Delete ALL keys in current DB
```

---

## 2.3 Redis Data Structures and Algorithms

> These are Redis's advanced, specialized structures for specific problem classes. Each trades something (exactness, simplicity) for enormous gains in memory efficiency or speed.

---

### 2.3.1 Bitmaps and Bitfields

#### Bitmaps

A Redis **Bitmap** is not a separate type — it's a set of bit manipulation operations on regular Redis Strings. Each string character = 8 bits = 8 boolean flags.

> **Analogy: Cinema Seating:** Imagine each seat = one user ID. A `1` bit means "occupied/active", `0` means "empty/inactive". You can represent the status of 8 users in a single byte.

```bash
# Track which users logged in on a specific date:
SETBIT login:2024-01-15 1001 1    # User 1001 logged in
SETBIT login:2024-01-15 2005 1    # User 2005 logged in

GETBIT login:2024-01-15 1001      # → 1 (logged in)
GETBIT login:2024-01-15 9999      # → 0 (did NOT log in)
BITCOUNT login:2024-01-15         # Total logins today

# Users active on BOTH Monday AND Tuesday:
BITOP AND active_both login:mon login:tue
BITCOUNT active_both              # Intersection count
```

**Memory comparison:**

| Method | Memory for 10M users/day | Approach |
|--------|--------------------------|----------|
| Redis Set | ~100 MB | Store each user ID as string |
| Bitmap | ~1.25 MB | 1 bit per user |
| **Savings** | **80x smaller** | |

> **Real-World: GitHub Contribution Graph:** Those green squares on GitHub profiles? Each bit = one day. `BITCOUNT contributions:user:torvalds` = total active days.

---

#### Bitfields

Extend bitmaps by storing **multi-bit integers** at arbitrary bit offsets. Pack multiple integer values compactly into a single key.

```bash
# Store player stats: level (u8), health (u8), gold (u16)
BITFIELD player:1001 SET u8  0  15     # Level = 15
BITFIELD player:1001 SET u8  8  87     # Health = 87
BITFIELD player:1001 SET u16 16 5000   # Gold = 5000

BITFIELD player:1001 GET u8 0          # → 15
BITFIELD player:1001 INCRBY u8 0 1     # Level up → 16

# Overflow protection (cap at max, no wrap-around):
BITFIELD player:1001 OVERFLOW SAT INCRBY u8 8 200   # Health stays at 255
```

---

### 2.3.2 HyperLogLog for Cardinality Estimation

**The Problem:** You need to count unique visitors. Storing every visitor ID in a Set costs ~48 GB for 1 billion users.

**HyperLogLog (HLL)** is a probabilistic algorithm that estimates the count of distinct elements using **constant, tiny memory, always ~12 KB**, regardless of input size. The trade-off: ~0.81% error rate (acceptable for analytics).

**How it works (intuition):** HLL hashes each input into a binary string and tracks the maximum number of leading zeros seen. Statistically, if the longest run of leading zeros is `k`, then approximately `2^k` distinct items have been seen. With 16,384 sub-registers averaging these observations, the estimate becomes remarkably accurate.

```bash
# Count unique website visitors:
PFADD visitors:2024-01-15 user1 user2 user3 user4 user5
PFADD visitors:2024-01-15 user2 user3 user6 user7   # Duplicates ignored

PFCOUNT visitors:2024-01-15    # → ~7 (not 9, duplicates excluded)

# Merge multiple HLLs (weekly report):
PFADD visitors:mon user1 user2 user3
PFADD visitors:tue user2 user4 user5
PFADD visitors:wed user1 user6 user7

PFMERGE visitors:week visitors:mon visitors:tue visitors:wed
PFCOUNT visitors:week   # → ~7 unique users across all 3 days
```

> PF in commands stands for **Philippe Flajolet**, the mathematician who invented the HyperLogLog algorithm.

**Comparison:**

| Method | Memory (1B unique items) | Accuracy |
|--------|--------------------------|----------|
| Redis Set | ~48 GB | 100% exact |
| HyperLogLog | ~12 KB | ~99.19% |

> **Real-World: YouTube:** "500 million unique views" is an approximation using HLL-like structures. At that scale, ±0.81% error is unnoticeable and saves enormous memory.

---

### 2.3.3 Bloom Filters for Membership Testing

**The Problem:** You have 5 billion URLs and need to check "Has this URL been seen before?" Exact lookup in a database is too slow; storing everything in memory costs hundreds of GB.

A **Bloom Filter** answers: *"Is this element possibly in the set?"*

- **"NO"** - **definitely not** in the set (100% accurate, zero false negatives)
- **"YES"** - **probably** in the set (small chance of false positive)

**How it works:** A bit array + multiple hash functions. Adding an item sets several bits to 1. Checking an item verifies all those bits, if any is 0, the item is definitely absent; if all are 1, it's probably present.

```bash
# Requires RedisBloom module (included in Redis Stack)

# Create a filter for 1M items with 0.1% false positive rate:
BF.RESERVE urls:shortened 0.001 1000000

# Add items:
BF.ADD urls:shortened "https://google.com"
BF.ADD urls:shortened "https://github.com"

# Check membership:
BF.EXISTS urls:shortened "https://google.com"   # 1 (probably in set)
BF.EXISTS urls:shortened "https://bing.com"     # 0 (DEFINITELY not)

# Bulk operations:
BF.MADD blacklist:emails "spam@evil.com" "bot@scam.net"
BF.MEXISTS blacklist:emails "spam@evil.com" "real@gmail.com"
# [1, 0]
```

**Two-tier lookup pattern:**

```
Request
   │
   ▼
[Bloom Filter Check]
   │                  │
   │ NO               │ YES (possibly)
   ▼                  ▼
Definitely        [Exact DB Lookup]
Not Present           │        │
(skip DB!)          Found   Not Found
                    (TP)     (FP → discard)
```

> **Real-World: Google Chrome Safe Browsing:** Chrome stores a local Bloom Filter of known malicious URLs. `0` = definitely safe (no network call needed). `1` = send to Google for exact verification. This reduces network calls by ~99%.

**Important Limitations:**

- Cannot **delete** items (only add). Use a **Cuckoo Filter** if deletion is needed.
- False positive rate **increases** as the filter fills beyond capacity.
- Not suitable when exact membership is required (billing, auth).

---

### 2.3.4 Geospatial Indexes

Redis Geospatial commands are built **on top of Sorted Sets**, coordinates are encoded as GeoHash integers (scores), enabling efficient proximity queries.

> **GeoHash:** An algorithm that encodes `(latitude, longitude)` into a single string or integer. Nearby coordinates share similar GeoHash prefixes, this "spatial locality" enables fast range queries.

```bash
# Add driver locations (longitude first, then latitude):
GEOADD drivers:online 77.2090 28.6139 "driver_A"
GEOADD drivers:online 77.2210 28.6250 "driver_B"
GEOADD drivers:online 77.3000 28.7000 "driver_D"

# Get stored coordinates:
GEOPOS drivers:online driver_A

# Distance between two points:
GEODIST drivers:online driver_A driver_B km   # → 1.78 km

# Find all drivers within 3 km of a passenger:
GEOSEARCH drivers:online
    FROMLONLAT 77.2090 28.6139
    BYRADIUS 3 km
    ASC
    COUNT 5
    WITHCOORD
    WITHDIST

# GeoHash encoding (nearby places share prefix):
GEOHASH drivers:online driver_A driver_B
# → "ttnfv2ub4k0"   "ttnfvdc6k50"  (share "ttnfv" prefix)
```

**Key Commands:**

| Command | Description |
|---------|-------------|
| `GEOADD key lon lat member` | Add a location |
| `GEOPOS key member` | Get coordinates |
| `GEODIST key m1 m2 unit` | Distance (m, km, mi, ft) |
| `GEOSEARCH key FROMLONLAT ...` | Radius/bounding box search |
| `GEOHASH key member` | Get GeoHash string |

> **Real-World — Swiggy/Zomato:** On app open: `GEOSEARCH restaurants FROMLONLAT <your_lat> <your_lon> BYRADIUS 5 km ASC`, returns all restaurants within 5 km, sorted by proximity, in sub-millisecond time.

**Performance:** ~16 bytes per member, ~0.6mm precision, O(log N) for GEOADD, O(N + log M) for radius search.

---

### Comparative Summary

| Structure | Problem Solved | Memory | Accuracy | Primary Commands |
|-----------|----------------|--------|----------|-----------------|
| Bitmaps | Binary flags per user/day | 1 bit/user/day | Exact | `SETBIT`, `GETBIT`, `BITCOUNT` |
| Bitfields | Packed integer arrays | Dense bit-packing | Exact | `BITFIELD GET/SET/INCRBY` |
| HyperLogLog | Count distinct items | Fixed 12 KB | ~0.81% error | `PFADD`, `PFCOUNT`, `PFMERGE` |
| Bloom Filter | Membership testing | ~10 bits/item | No false negatives | `BF.ADD`, `BF.EXISTS` |
| Geospatial | Location-based queries | ~16 bytes/point | ~0.6mm precision | `GEOADD`, `GEOSEARCH` |

---

## 2.4 Redis Persistence and Durability

> Redis is in-memory. A crash or restart clears all data, like RAM being wiped on shutdown. Persistence mechanisms save data to disk so it can be recovered.

---

### 2.4.1 RDB Snapshots (Redis Database Backup)

RDB creates **point-in-time binary snapshots** of the entire dataset at configured intervals. Uses OS `fork()` + Copy-On-Write, so the main process keeps serving clients while a child process writes the snapshot.

```bash
# redis.conf:
save 900 1        # Snapshot if ≥1 key changed in 15 min
save 300 10       # Snapshot if ≥10 keys changed in 5 min
save 60 10000     # Snapshot if ≥10000 keys changed in 1 min
dbfilename dump.rdb
dir /var/lib/redis/

# Manual commands:
BGSAVE      # Background save (non-blocking)
SAVE        # Synchronous save (BLOCKS Redis)
LASTSAVE    # Unix timestamp of last successful save
```

| Feature | Detail |
|---------|--------|
| File format | Compact binary `.rdb` (highly compressible) |
| Performance impact | Low — `fork()` is nearly instant |
| Recovery time | Fast — loads single binary file |
| Data loss risk | Up to X minutes (since last snapshot) |
| Best for | Backups, disaster recovery, warm cache restarts |

---

### 2.4.2 AOF (Append-Only File) Logs

AOF **logs every write operation** to a file in RESP format. On restart, Redis replays all commands to rebuild the dataset, like a transaction log in RDBMS.

```bash
# redis.conf:
appendonly yes
appendfilename "appendonly.aof"

# Fsync policy (when to flush buffer to disk):
appendfsync always    # Every write → MOST DURABLE, slowest
appendfsync everysec  # Every second → BALANCED recommended
appendfsync no        # Let OS decide → FASTEST, least durable

# Auto-rewrite (compress AOF when it grows too large):
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# Manual rewrite:
BGREWRITEAOF
```

What the AOF file looks like (human-readable RESP format):

```
*3          ← Command has 3 arguments
$3          ← Next arg: 3 bytes
SET
$8          ← Key: 8 bytes
username
$5          ← Value: 5 bytes
Alice
```

---

### 2.4.3 Hybrid Persistence Strategies

| Strategy | Data Loss Risk | Restart Speed | Use Case |
|----------|----------------|---------------|----------|
| No persistence | Total loss | Instant | Pure cache, data rebuildable |
| RDB only | Up to minutes | Fast | Tolerable loss, backups |
| AOF only | ≤1 second | Slower | High-durability requirement |
| **RDB + AOF** | **≤1 second** | **Fast (uses RDB)** | **Production recommended** |

```bash
# redis.conf, Production Hybrid Setup:
appendonly yes
appendfsync everysec
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
save 900 1
save 300 10
aof-use-rdb-preamble yes    # Redis 7.0+ hybrid mode
```

> **Real-World — Shopify on Black Friday:** AOF `everysec` ensures cart data loses at most 1 second during outages. Daily RDB snapshots go to AWS S3 for disaster recovery. During peak traffic (millions of transactions/minute), this balance is critical.

---

## 2.5 Redis Clustering and High Availability

> **Key Terms:**
>
> - **SPOF (Single Point of Failure):** If one server fails, the whole service goes down.
> - **High Availability (HA):** The system keeps working even if some nodes fail.
> - **Horizontal Scaling:** Adding more machines, not bigger machines.

---

### 2.5.1 Redis Sentinel for Automatic Failover

Sentinel is the **health check and automatic failover** layer. It monitors Redis instances and, if the primary (master) goes down, automatically promotes a replica to become the new primary.

```bash
# sentinel.conf:
sentinel monitor mymaster 192.168.1.100 6379 2
# Name: mymaster | Address | Quorum: 2 sentinels must agree

sentinel down-after-milliseconds mymaster 5000   # Down after 5s
sentinel failover-timeout mymaster 60000         # Failover must finish in 60s

# Start:
redis-sentinel /etc/redis/sentinel.conf
```

**Sentinel Failover Flow:**

1. Primary stops responding → Sentinels detect failure
2. Quorum reached (e.g., 2 of 3 sentinels agree)
3. Sentinel promotes a replica: `SLAVEOF NO ONE`
4. Sentinels notify clients about the new primary
5. Other replicas reconfigure to follow the new primary

> **Sentinel vs Cluster:** Sentinel solves **High Availability** (auto-failover) but NOT horizontal scaling. To scale writes beyond one machine, use Redis Cluster.

---

### 2.5.2 Redis Cluster for Horizontal Scaling

Redis Cluster automatically **shards data across multiple nodes** using hash slots.

**Hash Slot Sharding:**

```
Total: 16,384 hash slots
slot = CRC16(key) % 16384

Node A → Slots 0–5460
Node B → Slots 5461–10922
Node C → Slots 10923–16383
```

When a client connects to the wrong node, Redis returns a `MOVED` redirect to the correct node.

```bash
# redis.conf for each cluster node:
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000

# Create cluster (3 masters + 3 replicas):
redis-cli --cluster create \
  192.168.1.101:6379 192.168.1.102:6379 192.168.1.103:6379 \
  192.168.1.104:6379 192.168.1.105:6379 192.168.1.106:6379 \
  --cluster-replicas 1

# Hash Tags — force keys to same slot:
{user:1001}.name    # Both keys hashed on "user:1001" → same node
{user:1001}.email   # → multi-key operations work!
```

**Sentinel vs Cluster:**

| Feature | Redis Sentinel | Redis Cluster |
|---------|----------------|---------------|
| Primary purpose | HA (auto-failover) | Scaling + HA |
| Max data | One machine's RAM | Sum of all nodes' RAM |
| Write throughput | Single machine | Scales with nodes |
| Multi-key ops | Full support | Keys must share hash slot |
| Minimum nodes | 3 (1P + 2 Sentinels) | 6 (3M + 3R) |

> **Real-World: Instagram:** Uses Redis Cluster across thousands of nodes to store follower/following relationships and feed data for 1+ billion users. No single machine could hold all this in RAM.

---

### 2.5.3 Replication and Data Synchronization

```bash
# On replica node's redis.conf:
replicaof 192.168.1.100 6379   # Point to primary

# Or dynamically:
REPLICAOF 192.168.1.100 6379
REPLICAOF NO ONE               # Detach (promote to standalone)

# Check replication status on primary:
INFO replication
# → role:master
# → connected_slaves:2
# → slave0:ip=192.168.1.101,...,state=online,lag=0
```

**Full Sync Process:**

1. Replica sends `PSYNC ? -1` (full sync request)
2. Primary forks and creates an RDB snapshot (`BGSAVE`)
3. Primary sends RDB file to replica
4. Replica loads RDB (clears memory first)
5. Primary sends buffered commands from during transfer
6. Ongoing: Primary streams every write command to replica

---

## 2.6 Redis Modules and Extensions

Redis Modules (since Redis 4.0) extend Redis with new data types and commands. **Redis Stack** bundles the most popular ones.

| Module | Purpose |
|--------|---------|
| **RediSearch** | Full-text search + aggregations |
| **RedisJSON** | Native JSON storage and querying |
| **RedisTimeSeries** | Time-series data and analytics |
| **RedisAI** | ML model serving and inference |
| **RedisBloom** | Bloom/Cuckoo Filters + HyperLogLog |

---

### 2.6.1 RediSearch: Full-Text Search

RediSearch transforms Redis into a search engine with inverted indexes for O(1)/O(log N) queries instead of O(N) key scans.

```bash
# 1. Create a search index:
FT.CREATE idx:products
  ON HASH
  PREFIX 1 product:
  SCHEMA
    name        TEXT WEIGHT 5.0   # Higher weight = more relevant
    description TEXT
    price       NUMERIC SORTABLE
    category    TAG
    brand       TAG SORTABLE

# 2. Add products (normal HSET — index updates automatically!):
HSET product:1001 name "iPhone 15 Pro" price 1299 category "smartphone" brand "Apple"
HSET product:1002 name "Samsung Galaxy S24" price 1199 category "smartphone" brand "Samsung"

# 3. Search:
FT.SEARCH idx:products "iPhone"                       # Full-text
FT.SEARCH idx:products "@category:{smartphone}"       # Tag filter
FT.SEARCH idx:products "@price:[200 500]"             # Numeric range
FT.SEARCH idx:products "flagship @price:[1000 +inf]"  # Combined
FT.SEARCH idx:products "*" SORTBY price ASC LIMIT 0 10
```

**Field Types:**

| Field Type | Description | Use Case |
|------------|-------------|----------|
| `TEXT` | Full-text search with tokenization and stemming | Names, descriptions |
| `TAG` | Atomic literal, no tokenization | Categories, brands, IDs |
| `NUMERIC` | Range-based math queries | Prices, timestamps |

**Key Design Insights:**

- The index is **decoupled from the data**: deleting the index doesn't delete the Hash data.
- Data is added via standard `HSET`, RediSearch monitors the prefix and auto-indexes.
- Instead of O(N) key scans, RediSearch uses an **Inverted Index** for O(1)/O(log N) lookups.

---

### 2.6.2 RedisJSON: Native JSON Storage

Store, retrieve, and partially update JSON documents natively, no full serialization/deserialization required.

```bash
# Store JSON:
JSON.SET user:1001 $ '{"name":"Alice","age":28,"skills":["Python","Redis"]}'

# Read (JSONPath syntax):
JSON.GET user:1001 $              # Entire document
JSON.GET user:1001 $.name         # → "Alice"
JSON.GET user:1001 $.skills       # → ["Python","Redis"]

# Partial update (no need to read whole document!):
JSON.SET user:1001 $.age 29
JSON.SET user:1001 $.address.city "Paro"

# Array operations:
JSON.ARRAPPEND user:1001 $.skills '"Docker"'
JSON.ARRLEN    user:1001 $.skills      # → 3
```

**RedisJSON vs String JSON:**

| Operation | String JSON | RedisJSON |
|-----------|-------------|-----------|
| Partial update | Read → parse → update → write full blob | `JSON.SET $.field value` |
| Partial read | Get entire blob | `JSON.GET $.field` |
| Array push | Full rewrite | `JSON.ARRAPPEND` |
| Search | Not indexable | Index with RediSearch |

---

### 2.6.3 RedisTimeSeries: Time-Series Data

Built for storing and querying sequential timestamped data with automatic aggregation and downsampling.

```bash
# Create a time series:
TS.CREATE temperature:sensor:001
  RETENTION 86400000    # Keep 24 hours (ms)
  LABELS location Thimphu unit celsius

# Add data points (* = auto current timestamp):
TS.ADD temperature:sensor:001 * 22.5
TS.ADD temperature:sensor:001 * 23.1

# Query:
TS.RANGE temperature:sensor:001 - +                          # All data
TS.RANGE temperature:sensor:001 - + AGGREGATION avg 3600000  # Hourly avg

# Automatic downsampling rule:
TS.CREATERULE temperature:sensor:001 temp:hourly:001
  AGGREGATION avg 3600000   # Auto-compute hourly averages
```

> **Real-World: Tesla Telemetry:** Every Tesla sends battery level, temperature, speed, and GPS every second. RedisTimeSeries stores this with automatic aggregation (per-minute averages), enabling real-time dashboards and anomaly detection.

---

### 2.6.4 RedisAI, ML Model Serving

Serve machine learning models directly inside Redis, no data transfer to a separate inference server.

```bash
# Load a trained TensorFlow model:
AI.MODELSTORE sentiment:model TF CPU
  INPUTS  input_text
  OUTPUTS prediction
  BLOB    <model_binary_data>

# Set input tensor:
AI.TENSORSET input:review FLOAT 1 128 VALUES 0.1 0.5 0.3 ...

# Run inference:
AI.MODELEXECUTE sentiment:model
  INPUTS  1 input:review
  OUTPUTS 1 output:sentiment

# Get result:
AI.TENSORGET output:sentiment VALUES
# → [0.92]  (92% positive sentiment)
```

The key advantage: User data is already in Redis. The model is also in Redis. Inference happens in one hop, no network transfer to a separate ML server.

---

## 2.7 Redis Performance Optimization

### 2.7.1 Pipelining and Transactions

#### Pipelining

Every Redis command involves a **round-trip**: Client → Network → Redis → Network → Client. For 1000 commands, that's 1000 round-trips. **Pipelining** batches all commands into a single network trip.

```bash
# Python example (redis-py):

# Without pipelining — 10,000 round-trips (slow!)
for i in range(10000):
    r.set(f"key:{i}", f"value:{i}")

# With pipelining — ~1 round-trip (~100x faster!)
pipe = r.pipeline(transaction=False)
for i in range(10000):
    pipe.set(f"key:{i}", f"value:{i}")   # Just queues locally
results = pipe.execute()                  # Send ALL at once
```

> `transaction=False` disables `MULTI/EXEC` wrapping, commands are buffered and sent together but are **not atomic**. This is appropriate for bulk inserts where atomicity isn't needed.

---

#### Transactions (MULTI/EXEC)

```bash
MULTI                          # Start transaction (queue mode)
DECRBY user:1001:credits 100   # Queued
INCRBY user:2001:credits 100   # Queued
EXEC                           # Execute ALL atomically
DISCARD                        # Cancel transaction

# Optimistic Locking with WATCH:
WATCH user:1001:credits        # Monitor this key for changes
# ... read current value ...
MULTI
DECRBY user:1001:credits 100
EXEC   # Returns nil if key changed since WATCH → retry!
```

> **Redis ≠ SQL Transactions.** Redis does NOT roll back on runtime errors. If command 3 of 5 fails, commands 1, 2, 4, 5 still execute. `MULTI/EXEC` only guarantees no interleaving between other clients.

---

#### Lua Scripts (Atomic Complex Operations)

```bash
# Atomic check-then-deduct:
EVAL "
  local balance = redis.call('GET', KEYS[1])
  if tonumber(balance) >= tonumber(ARGV[1]) then
    redis.call('DECRBY', KEYS[1], ARGV[1])
    return 1   -- success
  else
    return 0   -- insufficient funds
  end
" 1 user:1001:credits 100
```

Lua scripts run atomically, no other commands can interleave during execution.

---

### 2.7.2 Memory Management and Eviction Policies

```bash
# redis.conf:
maxmemory 4gb
maxmemory-policy allkeys-lru
```

**Eviction Policies:**

| Policy | Behavior | Best For |
|--------|----------|----------|
| `noeviction` | Error when full (default) | Data that must never be lost |
| `allkeys-lru` | Evict Least Recently Used from all keys | General cache |
| `volatile-lru` | LRU from keys with TTL only | Mixed permanent + cached |
| `allkeys-lfu` | Evict Least Frequently Used from all keys | Frequency matters more than recency |
| `volatile-lfu` | LFU from TTL keys only | Frequently accessed cache |
| `volatile-ttl` | Evict soonest-expiring keys | Data with varying importance |
| `allkeys-random` | Random eviction | Rarely used |

```bash
# Check memory usage:
MEMORY USAGE user:1001     # Returns bytes
INFO memory                # Full memory stats
MEMORY DOCTOR              # Auto-diagnosis and suggestions

# Key fields to monitor:
# used_memory_human         → current allocated
# mem_fragmentation_ratio   → >1.5 = high fragmentation
# maxmemory_human           → configured limit
```

> **Pro Tip: Hash for Small Objects:** Redis uses compact `listpack` encoding for hashes with fewer than 128 fields and values under 64 bytes. Storing 1M user objects as hashes can be 10x more memory-efficient than using separate string keys.

---

### 2.7.3 Benchmarking and Monitoring Tools

```bash
# Built-in benchmark:
redis-benchmark -h localhost -p 6379 -n 100000 -c 50
# -n: total requests  -c: concurrent clients

redis-benchmark -t get,set -n 1000000 -q   # Quiet mode
# Typical output on modern hardware:
# SET: 185,185 requests/sec
# GET: 192,307 requests/sec

# Monitoring:
INFO stats         # hits, misses, ops/sec
INFO clients       # connected_clients, blocked_clients
SLOWLOG GET 25     # Last 25 slow commands
LATENCY HISTORY    # Latency spikes
MONITOR            # Real-time command stream (dev/debug only!)
```

**Key Metrics to Monitor:**

| Metric | Healthy Value | Alert If |
|--------|---------------|----------|
| Hit rate (keyspace_hits/total) | > 90% | < 80% |
| used_memory | < 80% of maxmemory | > 90% |
| connected_clients | Stable | Sudden spike |
| mem_fragmentation_ratio | 1.0–1.5 | > 1.5 |
| rdb_last_bgsave_status | ok | Not "ok" |

---

## 2.8 Redis Security Considerations

> **Critical Context:** Redis was originally designed for trusted internal networks. By default, **NO authentication**, listens on all interfaces. In 2016, tens of thousands of unprotected Redis instances were compromised. Security is not optional.

---

### 2.8.1 Authentication and Access Control

#### Legacy Password (Redis < 6.0)

```bash
# redis.conf:
requirepass YourStr0ngP@ssw0rd!

# Connect:
redis-cli -a YourStr0ngP@ssw0rd!
# Or after connecting:
AUTH YourStr0ngP@ssw0rd!
```

#### ACL: Access Control Lists (Redis 6.0+)

Modern, fine-grained access control — define what each user can do and on which keys.

```bash
# Create users with specific permissions:
ACL SETUSER alice on >alice_pass ~user:* +GET +HGET +HGETALL
# alice: enabled | password | key pattern | allowed commands

ACL SETUSER bob on >bob_pass ~order:* +@read
# bob: read-only on order:* keys

ACL SETUSER api_service on >svc_pass ~cache:* +GET +MGET +SETEX
ACL SETUSER admin on >admin_pass ~* +@all   # Full access

# Disable unauthenticated access:
ACL SETUSER default off

# Inspect:
ACL LIST             # Show all rules
ACL WHOAMI           # Current user
ACL LOG              # Security events (failed auths, denied commands)
```

**ACL Command Categories:**

| Category | Commands Included |
|----------|------------------|
| `+@read` | GET, LRANGE, HGETALL, SMEMBERS, ZRANGE… |
| `+@write` | SET, LPUSH, HSET, SADD, ZADD… |
| `+@admin` | CONFIG, INFO, MONITOR, DEBUG… |
| `~user:*` | Only keys starting with `user:` |
| `~*` | All keys |

---

### 2.8.2 SSL/TLS Encryption

```bash
# redis.conf (Redis 6.0+):
port 0                          # Disable plain TCP
tls-port 6380                   # Enable TLS
tls-cert-file /etc/redis/redis.crt
tls-key-file  /etc/redis/redis.key
tls-ca-cert-file /etc/redis/ca.crt
tls-auth-clients yes            # Require client cert
tls-protocols "TLSv1.2 TLSv1.3"

# Connect with TLS:
redis-cli -h localhost -p 6380 \
  --tls \
  --cert /path/to/client.crt \
  --key  /path/to/client.key \
  --cacert /path/to/ca.crt
```

---

### 2.8.3 Redis Security Best Practices

```bash
# 1. Bind to specific interfaces only:
bind 127.0.0.1 10.0.0.50    # NEVER: bind 0.0.0.0

# 2. Enable protected mode:
protected-mode yes

# 3. Disable or rename dangerous commands:
rename-command FLUSHDB  ""               # Disable completely
rename-command FLUSHALL ""
rename-command DEBUG    ""
rename-command CONFIG   "ADMIN_CFG_9k2m" # Rename to secret
rename-command KEYS     ""               # Force use of SCAN

# 4. Set maxmemory to prevent OOM:
maxmemory 4gb
maxmemory-policy allkeys-lru
```

**Security Layers Summary:**

| Layer | Tool | Protects Against |
|-------|------|-----------------|
| Network | Firewall, bind to internal IP | Unauthorized external access |
| Authentication | ACL + strong passwords | Unauthorized users |
| Authorization | ACL key patterns + commands | Lateral movement |
| Encryption (transit) | TLS/SSL | Eavesdropping, MITM |
| Audit | ACL LOG, slow log | Detecting attacks, compliance |
| Command hardening | Rename/disable dangerous commands | Accidental/malicious data wipes |

> **Real Security Incident: The Crackit Attack (2016):** Attackers found Redis instances exposed to the internet with no auth. They used `CONFIG SET dir /root/.ssh`, `CONFIG SET dbfilename authorized_keys`, then `SET` to write their SSH key, gaining full root server access. **Always firewall your Redis port. Always use ACL.**

---

## Quick Reference Cheat Sheet

### Commands by Data Type

```bash
# ── STRINGS ──────────────────────────────────────────
SET key value          GET key           MSET k1 v1 k2 v2
INCR key               INCRBY key n      DECRBY key n
SETEX key ttl value    SETNX key value   STRLEN key

# ── LISTS ────────────────────────────────────────────
LPUSH key v            RPUSH key v       LPOP key
RPOP key               LLEN key          LRANGE key 0 -1
BLPOP key timeout

# ── SETS ─────────────────────────────────────────────
SADD key m             SREM key m        SMEMBERS key
SISMEMBER key m        SCARD key         SPOP key
SUNION k1 k2           SINTER k1 k2      SDIFF k1 k2

# ── HASHES ───────────────────────────────────────────
HSET key f v           HGET key f        HGETALL key
HDEL key f             HEXISTS key f     HINCRBY key f n

# ── SORTED SETS ──────────────────────────────────────
ZADD key score member  ZREVRANGE key 0 -1 WITHSCORES
ZRANK key m            ZREVRANK key m     ZSCORE key m
ZINCRBY key n m        ZCOUNT key min max ZPOPMIN key

# ── ADVANCED ─────────────────────────────────────────
SETBIT key offset 1    GETBIT key offset  BITCOUNT key
PFADD key v            PFCOUNT key        PFMERGE dest k1 k2
GEOADD key lon lat m   GEOSEARCH key ...  GEODIST key m1 m2 km
BF.ADD key item        BF.EXISTS key item
```

### Big O Complexity Reference

| Operation | Complexity | Note |
|-----------|------------|------|
| GET / SET (String) | O(1) | Hash table lookup |
| LPUSH / RPUSH / LPOP / RPOP | O(1) | Doubly linked list ends |
| LRANGE | O(S+N) | S=start offset, N=returned |
| SADD / SISMEMBER | O(1) | Hash set |
| SUNION / SINTER / SDIFF | O(N) | N=total elements in all sets |
| HSET / HGET | O(1) | Hash table |
| ZADD / ZSCORE / ZRANK | O(log N) | Skip list |
| ZRANGE | O(log N + M) | M=elements returned |
| KEYS * | O(N) | Blocks Redis |
| SCAN | O(1) per call | Non-blocking |

### Data Type Decision Guide

| If you need to store... | Use |
|-------------------------|-----|
| A single value, counter, or JSON blob | **String** |
| An object with multiple fields | **Hash** |
| An ordered sequence or queue | **List** |
| A unique collection | **Set** |
| Ranked members | **Sorted Set** |
| Boolean flags for millions of IDs | **Bitmap** |
| Approximate unique item count | **HyperLogLog** |
| Fast membership testing | **Bloom Filter** |
| Geographic coordinates | **Geospatial** |

---

## Key Principles to Remember

| Principle | Rule |
|-----------|------|
| Key naming | Use `type:id:field` pattern with colons |
| Searching keys | Use `SCAN`, never `KEYS *` in production |
| TTL | Always set TTL on cache keys |
| Persistence | Use RDB + AOF hybrid in production |
| HA | Minimum 3 Sentinels for failover |
| Scaling | Redis Cluster for data > single machine RAM |
| Security | Bind to localhost + firewall + ACL = minimum |
| Performance | Pipeline batch commands to reduce round-trips |
| Memory | Use Hashes for small objects (compact encoding) |
| Anti-pattern | Never `KEYS *`, never store huge values in Redis |

---
