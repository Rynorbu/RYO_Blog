---
title: "Cassandra, The Ultimate Guide to Distributed NoSQL Databases"
published: 2026-05-21
description: "A simplified guide to Cassandra fundamentals."
# image: "../../assets/images/my_photo.jpg"
tags: ["DBMS"]
category: Cassandra
draft: false
---

# Column Family Stores & Apache Cassandra: Unit IV Study Notes

---

> **Three Catchy Title Options:**
> 1. *"Cassandra Knew Everything - Now So Will You: The Ultimate NoSQL Column Store Guide"*
> 2. *"Never Go Down Again: How Apache Cassandra Makes Your Data Bulletproof"*
> 3. *"Rows Are So Last Decade: A Student's Survival Guide to Apache Cassandra"*

---

## The Hook: Why Should You Even Care About Cassandra?

Imagine we run the world's busiest messaging app, billions of messages flying around every second, users on every continent, and our database can **never, ever go down**. What do we use?

Facebook had this exact problem. Their solution? They built **Apache Cassandra**, a database so distributed, so fault-tolerant, and so fast that even if half our servers catch fire, our app keeps running without skipping a beat.

Cassandra isn't just a database. It's a *philosophy*, one that says: *"Scale wide, fail gracefully, and write fast."* Whether you're a student, a developer, or just a curious human, understanding Cassandra means understanding how the world's biggest tech companies keep their data alive at ridiculous scale.

Let's dive in.

---

## Unit IV: Column Family Stores (Apache Cassandra)

---

## 4.1 Introduction to Apache Cassandra

### 4.1.1 The Cassandra Elevator Pitch

#### 4.1.1.1 Cassandra in 50 Words or Less

> **Apache Cassandra** is an open-source, distributed, **NoSQL column-family database** designed for high availability, elastic scalability, and fault tolerance, with no single point of failure. It excels at handling massive write-heavy workloads across multiple data centers and cloud environments at global scale.

Think of it as a database that's been to the gym, studied abroad, and has a backup plan for *every* backup plan.

---

#### 4.1.1.2 Distributed and Decentralized Architecture

One of Cassandra's most powerful traits: **there is no master node**.

- In traditional RDBMS systems, one node is "in charge", if it dies, everything crashes.
- In Cassandra, **every node is equal** (a "peer-to-peer" architecture).
- Data is distributed across all nodes in a **ring topology**.

**Analogy:** Traditional databases are like a monarchy, one king rules all. Cassandra is more like a republic, every node has a voice, and no single node dying brings down the whole country.

**Key characteristics:**
| Feature | Description |
|---|---|
| **No Master Node** | All nodes are equal peers |
| **Ring Topology** | Nodes form a logical ring for data distribution |
| **Data Partitioning** | Data is distributed via consistent hashing |
| **Replication** | Data is automatically copied across multiple nodes |

---

#### 4.1.1.3 Elastic Scalability and High Performance

- **Horizontal scaling**: Add more nodes → get more capacity. It's that simple.
- No need to shut down or reconfigure existing nodes.
- Performance scales **linearly** double the nodes, roughly double the throughput.
- Optimized for **write-heavy workloads**: Cassandra can handle hundreds of thousands of writes per second.

**Analogy:** Scaling Cassandra is like adding more lanes to a highway, traffic keeps flowing while construction happens. Traditional databases are like resurfacing the only road in town: everything stops.

---

#### 4.1.1.4 High Availability and Fault Tolerance

- **No single point of failure (SPOF)**: the death of one (or many) nodes doesn't kill the cluster.
- Data is **replicated** across multiple nodes and data centers.
- Even during node failures, reads and writes can continue.
- Supports **multi-data-center replication** out of the box.

> **Key Term:** **Replication Factor (RF)** the number of copies of each piece of data stored across the cluster. RF=3 means your data lives on 3 different nodes.

---

#### 4.1.1.5 Tuneable Consistency

Cassandra gives us a **dial**, not a binary switch, for consistency vs. availability.

- we choose how many nodes must acknowledge a read or write before it's considered successful.
- This is called the **Consistency Level (CL)**.
- More nodes required = stronger consistency, but slower performance.
- Fewer nodes required = faster performance, but data might be slightly stale.

> **Key Term:** **Tuneable Consistency** the ability to configure the trade-off between data consistency and read/write availability on a per-operation basis.

---

### 4.1.2 Theoretical Foundations

#### 4.1.2.1 Brewer's CAP Theorem

**CAP Theorem** states that any distributed data store can only guarantee **two of the three** following properties simultaneously:

```
C — Consistency     (every read gets the most recent write)
A — Availability    (every request gets a response)
P — Partition Tolerance (system works even if nodes can't talk to each other)
```

| System Type | Guarantees | Trade-off |
|---|---|---|
| **CP** (e.g., HBase) | Consistency + Partition Tolerance | May be unavailable during partition |
| **AP** (e.g., Cassandra) | Availability + Partition Tolerance | May return stale data |
| **CA** (e.g., Traditional RDBMS) | Consistency + Availability | Cannot handle network partitions |

> **Cassandra is an AP system** it prioritizes **Availability** and **Partition Tolerance** over strict consistency. But (and this is key), with tuneable consistency, you can *lean* toward consistency when needed.

**Analogy:** Imagine a group chat with friends in different countries. CAP Theorem says we can have messages that are: (1) always the same for everyone, (2) always delivered, or (3) delivered even when the internet is patchy, but never all three perfectly at once.

---

#### 4.1.2.2 Row-Oriented Data Model

Despite being a "column-family" store, Cassandra organizes data in a **wide-row** model:

- Data is stored in **tables** (like SQL), but rows can have many, many columns.
- Each row is uniquely identified by a **primary key**.
- Columns are grouped into **column families** (now called tables in modern Cassandra).
- Unlike RDBMS, rows don't need to share the same columns (sparse model).

> **Key Term:** **Column Family** a container for rows that share a similar structure, analogous to a table in RDBMS, but far more flexible in column structure.

---

### 4.1.3 Cassandra's Origins and Evolution

| Year | Milestone |
|---|---|
| **2007** | Developed at **Facebook** to power the Inbox Search feature |
| **2008** | Open-sourced by Facebook |
| **2009** | Became an **Apache Incubator** project |
| **2010** | Graduated to a **top-level Apache project** |
| **2011+** | DataStax founded; enterprise adoption surged |
| **2020+** | Cassandra 4.x released with major stability and performance improvements |

> Fun fact: Cassandra is named after the prophet from Greek mythology who could foresee the future but was cursed so no one would believe her. The engineers thought it was fitting, their database could "predict" failures before they happened.

---

### 4.1.4 Use Cases and Applications

#### 4.1.4.1 Large Deployments

- **Netflix**: Tracks viewing history and personalization for 200M+ subscribers.
- **Apple**: Runs over 75,000 Cassandra nodes to manage billions of devices.
- **Instagram**: Uses Cassandra for media metadata storage.

Best fit when:
- You have **terabytes to petabytes** of data.
- You need **always-on availability** with zero downtime tolerance.

---

#### 4.1.4.2 Write-Heavy Workloads and Analytics

Cassandra is **built for writes** — inserts and updates are extremely fast because data is written to an in-memory structure first (no read-before-write required in most cases).

Ideal for:
- **IoT sensor data** (millions of writes per second)
- **Time-series data** (logs, metrics, financial ticks)
- **Event tracking** (clickstreams, user activity)

---

#### 4.1.4.3 Geographical Distribution

- Cassandra supports **multi-data-center replication** natively.
- Data can be replicated to nodes in New York, London, and Tokyo simultaneously.
- Users are automatically served by the nearest data center.
- Compliant with **data sovereignty** regulations (keep EU data in EU).

---

#### 4.1.4.4 Hybrid Cloud and Multicloud Deployment

- Cassandra runs on **on-premises servers, public clouds, and in containers**.
- A single cluster can span **AWS + Azure + bare metal** simultaneously.
- This makes it ideal for organizations transitioning to the cloud or avoiding vendor lock-in.

---

## 4.2 Cassandra Architecture and Data Model

### 4.2.1 Cassandra's Distributed Architecture

#### 4.2.1.1 Data Centers and Racks

Cassandra uses a **hierarchical topology**:

```
Cluster
  └── Data Center (DC)
        └── Rack
              └── Node
```

- **Cluster**: The top-level container, all nodes that work together.
- **Data Center**: A logical or physical grouping of nodes (often one per geographic region).
- **Rack**: A grouping within a data center (often represents physical server racks).
- **Node**: A single Cassandra instance on a machine.

> This hierarchy helps Cassandra make smart **replication decisions** spreading replicas across different racks and DCs to survive hardware failures.

---

#### 4.2.1.2 Rings and Tokens

Cassandra maps all nodes into a logical **ring**:

- Each node is assigned one or more **tokens**, values on a numeric range (0 to 2^127).
- When data is written, its **partition key** is hashed to produce a token value.
- The node responsible for that token range handles (and replicates) that data.

```
Token Ring (simplified):
Node A: tokens 0–33
Node B: tokens 34–66
Node C: tokens 67–100
```

> **Key Term:** **Consistent Hashing**, a technique that maps both data and nodes to the same numeric space, so adding/removing nodes only redistributes a small portion of the data.

---

#### 4.2.1.3 Virtual Nodes (vnodes)

- Traditionally, each node owned one large token range, uneven distribution when adding nodes.
- **Virtual nodes** assign **many small token ranges** to each physical node (default: 256 vnodes/node).
- Benefits:
  - **Better load balancing** across nodes
  - **Faster cluster resizing** (adding or removing nodes)
  - **Automatic data redistribution** without manual token assignment

**Analogy:** Instead of each delivery driver covering one huge zone, vnodes split the city into hundreds of tiny zones and distribute them evenly. Add a new driver? They take a few zones from everyone.

---

### 4.2.2 Core Components

#### 4.2.2.1 Gossip Protocol and Failure Detection

- Cassandra nodes communicate using a **Gossip Protocol**, they periodically share state information with random neighbors.
- Within seconds, **every node knows the state of every other node**.
- Failure detection is handled by **Phi Accrual Failure Detector**, instead of a binary "alive/dead" signal, it calculates a *suspicion score* that rises the longer a node goes silent.

> **Key Term:** **Gossip Protocol**, a peer-to-peer communication protocol where nodes exchange state information in a manner similar to how rumors spread in a social network.

---

#### 4.2.2.2 Snitches and Partitioners

**Snitches** tell Cassandra about the network topology, which nodes are in which rack and data center.

| Snitch Type | Description |
|---|---|
| `SimpleSnitch` | For single DC, development use only |
| `GossipingPropertyFileSnitch` | Production standard; reads DC/rack from config file |
| `Ec2Snitch` | Auto-detects topology on AWS |
| `GoogleCloudSnitch` | Auto-detects topology on GCP |

**Partitioners** determine how data is distributed across nodes:
- **Murmur3Partitioner** *(default)*: Uses Murmur3 hash, fast, even distribution.
- **RandomPartitioner**: Uses MD5, legacy option.
- **ByteOrderedPartitioner**: Preserves key order, generally avoided (causes hotspots).

---

#### 4.2.2.3 Replication Strategies

When writing data, Cassandra places replicas on multiple nodes based on the **Replication Strategy**:

| Strategy | Use Case |
|---|---|
| **SimpleStrategy** | Single data center only (dev/test) |
| **NetworkTopologyStrategy** | Multi-DC production deployments |

```sql
-- Example: NetworkTopologyStrategy with RF=3 in each DC
CREATE KEYSPACE my_app
WITH replication = {
  'class': 'NetworkTopologyStrategy',
  'DC1': 3,
  'DC2': 3
};
```

---

### 4.2.3 Cassandra's Data Model

#### 4.2.3.1 Clusters and Keyspaces

- **Cluster**: The entire Cassandra deployment (all DCs + nodes).
- **Keyspace**: The outermost data container, equivalent to a *database* in RDBMS.
  - Defines the replication strategy and factor.
  - A cluster can contain multiple keyspaces.

```sql
CREATE KEYSPACE ecommerce
WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 3};
```

---

#### 4.2.3.2 Tables and Columns

Cassandra tables look SQL-like but work very differently:

```sql
CREATE TABLE users (
  user_id   UUID,
  email     TEXT,
  username  TEXT,
  created   TIMESTAMP,
  PRIMARY KEY (user_id)
);
```

**Primary Key Anatomy:**

```
PRIMARY KEY (partition_key, clustering_column_1, clustering_column_2)
```

- **Partition Key**: Determines *which node* stores the data (via hashing).
- **Clustering Columns**: Determine the *sort order* of rows within a partition.
- **Regular Columns**: The actual data payload.

> **Critical Cassandra Truth**: You design tables around your **queries**, not around your entities. This is the single biggest mindset shift from RDBMS!

---

#### 4.2.3.3 CQL Types: Simple, Collection, and User-Defined

**Simple Types:**

| Type | Description |
|---|---|
| `UUID` / `TIMEUUID` | Unique identifiers |
| `TEXT` / `VARCHAR` | String data |
| `INT`, `BIGINT`, `FLOAT`, `DOUBLE` | Numeric types |
| `BOOLEAN` | True/False |
| `TIMESTAMP` / `DATE` / `TIME` | Date & time |
| `BLOB` | Binary data |

**Collection Types:**

| Type | Description | Example |
|---|---|---|
| `LIST<T>` | Ordered list of values | `['a', 'b', 'c']` |
| `SET<T>` | Unordered unique values | `{'red', 'blue'}` |
| `MAP<K,V>` | Key-value pairs | `{'name': 'Alice'}` |

**User-Defined Types (UDTs):**

```sql
CREATE TYPE address (
  street TEXT,
  city   TEXT,
  zip    TEXT
);

CREATE TABLE customers (
  id      UUID PRIMARY KEY,
  name    TEXT,
  home    FROZEN<address>
);
```

---

## 4.3 Installing and Configuring Cassandra

### 4.3.1 Installation Methods

#### 4.3.1.1 Apache Distribution

Download directly from the Apache Cassandra project:

```bash
# Download and extract
wget https://downloads.apache.org/cassandra/4.1.x/apache-cassandra-4.1.x-bin.tar.gz
tar -xvzf apache-cassandra-4.1.x-bin.tar.gz

# Start Cassandra
cd apache-cassandra-4.1.x
bin/cassandra
```

Prerequisites: **Java 11+** must be installed.

---

#### 4.3.1.2 Building from Source

```bash
git clone https://github.com/apache/cassandra.git
cd cassandra
ant
```

Used by contributors and those needing custom builds. Not recommended for production.

---

#### 4.3.1.3 Docker Deployment

The fastest way to get started locally:

```bash
# Pull official image
docker pull cassandra:latest

# Start a single node
docker run --name cassandra-node -d cassandra:latest

# Connect with cqlsh
docker exec -it cassandra-node cqlsh
```

---

### 4.3.2 Basic Server Operations

#### 4.3.2.1 Starting and Stopping Cassandra

```bash
# Start (foreground)
bin/cassandra -f

# Start (background)
bin/cassandra

# Stop
kill $(cat cassandra.pid)
# OR using nodetool
bin/nodetool stopdaemon

# Check status
bin/nodetool status
```

#### 4.3.2.2 Environment Configuration

Key configuration files:

| File | Purpose |
|---|---|
| `conf/cassandra.yaml` | Main config: cluster name, seeds, directories, ports |
| `conf/cassandra-env.sh` | JVM settings (heap size, GC options) |
| `conf/jvm.options` | Fine-grained JVM tuning |
| `conf/logback.xml` | Logging configuration |

**Important `cassandra.yaml` settings:**

```yaml
cluster_name: 'MyCluster'
seeds: "192.168.1.10,192.168.1.11"
listen_address: 192.168.1.12
native_transport_port: 9042
data_file_directories:
  - /var/lib/cassandra/data
commitlog_directory: /var/lib/cassandra/commitlog
```

---

### 4.3.3 CQL Shell (cqlsh)

**cqlsh** is the interactive command-line interface for Cassandra, think of it like `psql` for PostgreSQL or `mysql` CLI.

```bash
# Connect to local instance
bin/cqlsh

# Connect to remote
bin/cqlsh 192.168.1.10 9042

# Connect with credentials
bin/cqlsh -u username -p password
```

#### 4.3.3.1 Basic cqlsh Commands

```sql
-- Show all keyspaces
DESCRIBE KEYSPACES;

-- Show all tables in current keyspace
DESCRIBE TABLES;

-- Show table schema
DESCRIBE TABLE users;

-- Check cluster info
SELECT * FROM system.local;

-- Exit
EXIT;
```

#### 4.3.3.2 Creating Keyspaces and Tables

```sql
-- Create keyspace
CREATE KEYSPACE blog
WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};

-- Use keyspace
USE blog;

-- Create table
CREATE TABLE posts (
  author_id   UUID,
  created_at  TIMESTAMP,
  post_id     UUID,
  title       TEXT,
  body        TEXT,
  tags        SET<TEXT>,
  PRIMARY KEY ((author_id), created_at, post_id)
) WITH CLUSTERING ORDER BY (created_at DESC);
```

#### 4.3.3.3 Writing and Reading Data

```sql
-- Insert data
INSERT INTO posts (author_id, created_at, post_id, title, body)
VALUES (
  uuid(),
  toTimestamp(now()),
  uuid(),
  'Hello Cassandra',
  'This is my first post!'
);

-- Read data
SELECT * FROM posts WHERE author_id = <some-uuid>;

-- Update data
UPDATE posts SET title = 'Updated Title'
WHERE author_id = <uuid> AND created_at = <ts> AND post_id = <uuid>;

-- Delete data
DELETE FROM posts
WHERE author_id = <uuid> AND created_at = <ts> AND post_id = <uuid>;
```

---

## 4.4 Data Modeling in Cassandra

> **This is the hardest part to unlearn if you know SQL.** Read carefully.

### 4.4.1 Conceptual Data Modeling

At this stage, you identify:
- **Entities** (what objects exist — Users, Orders, Products)
- **Relationships** (how they connect)
- **Attributes** (what data they hold)

This phase looks similar to RDBMS ER modeling — but it's just the *starting point*.

---

### 4.4.2 Logical Data Modeling

#### 4.4.2.1 Differences from RDBMS Design

| RDBMS Approach | Cassandra Approach |
|---|---|
| Normalize data — eliminate redundancy | **Denormalize** — redundancy is OK, even encouraged |
| Design around entities | **Design around queries** |
| Joins allowed | **No joins** — ever |
| Ad hoc queries supported | **Queries must be predefined** |
| Foreign keys enforce relationships | **Application handles relationships** |

#### 4.4.2.2 Query-Driven Modeling Approach

**The golden rule of Cassandra modeling:**

> *"Know your queries first. Design your tables around them."*

**Workflow:**
1. Identify all application queries (e.g., "Get all posts by author, ordered by date")
2. For each query, design a table that satisfies it directly
3. Accept that you may need **multiple tables for the same data** (that's normal!)

**Example:**
```
Query 1: "Get user by email"
  → Table: users_by_email (partition key: email)

Query 2: "Get user by user_id"
  → Table: users_by_id (partition key: user_id)
```

Yes — two tables, same data, different access patterns. This is correct Cassandra modeling.

---

### 4.4.3 Physical Data Modeling

#### 4.4.3.1 Partition Design

The **partition key** is the most important design decision in Cassandra.

**Rules for a good partition key:**
- Should distribute data **evenly** across all nodes (high cardinality)
- Should be used in **every query** that accesses this table
- Avoid **low-cardinality** keys (e.g., `status = 'active'/'inactive'` → only 2 nodes get all the data)
- Avoid **monotonically increasing** keys with ByteOrderedPartitioner (causes hotspots)

```sql
-- BAD: Low cardinality partition key
PRIMARY KEY (status)   -- only 2–3 values, huge hotspots

-- GOOD: High cardinality
PRIMARY KEY (user_id)  -- millions of unique values, even distribution

-- COMPOSITE partition key (when needed for distribution)
PRIMARY KEY ((region, user_id), created_at)
```

---

#### 4.4.3.2 Clustering Columns

Clustering columns define the **sort order of rows within a partition**:

```sql
CREATE TABLE sensor_readings (
  sensor_id  UUID,
  recorded   TIMESTAMP,
  value      DOUBLE,
  PRIMARY KEY (sensor_id, recorded)
) WITH CLUSTERING ORDER BY (recorded DESC);
```

- Data within the `sensor_id` partition is stored sorted by `recorded` (newest first).
- You can query ranges: `WHERE sensor_id = X AND recorded > Y AND recorded < Z`
- Clustering columns **must be used in order** in WHERE clauses.

---

### 4.4.4 Schema Optimization Techniques

#### 4.4.4.1 Calculating Partition Size

Large partitions cause performance problems. Use this formula:

```
Nv = Nr * (Nc - Npk - Nck) + Nr * Nsc

Where:
  Nv  = number of values (cells) in the partition
  Nr  = number of rows
  Nc  = number of columns in table
  Npk = number of partition key columns
  Nck = number of clustering columns
  Nsc = number of static columns
```

> **Target:** Keep partitions under **100MB** in size and under **100,000 rows**.

---

#### 4.4.4.2 Breaking Up Large Partitions

**Technique 1: Add a bucket to the partition key**

```sql
-- BEFORE: One huge partition per sensor
PRIMARY KEY (sensor_id, recorded)

-- AFTER: Bucket by month to limit partition size
PRIMARY KEY ((sensor_id, month), recorded)
-- Now query: WHERE sensor_id = X AND month = '2024-01'
```

**Technique 2: Time-based bucketing**
- Break `user_id` + `year_month` so each partition only holds one month of data.

---

### 4.4.5 Data Modeling Tools for Cassandra

| Tool | Description |
|---|---|
| **DataStax Studio** | Visual query and data modeling IDE |
| **Hackolade** | Entity-relationship modeling for NoSQL databases |
| **Chebotko Diagrams** | Notation system for visually representing Cassandra table designs |
| **NoSQLBench** | Benchmarking and load testing tool |

> Reference: [DataStax Data Modeling Guide](https://docs.datastax.com/en/dse/6.8/dse-dev/datastax_enterprise/dbDesign/dbDesignIntro.html)

---

## 4.5 Advanced Cassandra Concepts

### 4.5.1 Consistency Models

#### 4.5.1.1 Consistency Levels

**Consistency Level (CL)** = how many replica nodes must respond before a read/write is considered successful.

| Consistency Level | Description | RF=3 Example |
|---|---|---|
| `ONE` | Only 1 replica must respond | Fast, weakest consistency |
| `TWO` | 2 replicas must respond | Moderate |
| `THREE` | 3 replicas must respond | All replicas |
| `QUORUM` | Majority must respond | `(3/2)+1 = 2` nodes |
| `LOCAL_QUORUM` | Majority in local DC | Best for multi-DC |
| `EACH_QUORUM` | Majority in each DC | Strongest multi-DC |
| `ALL` | All replicas must respond | Strongest, least available |
| `ANY` | At least 1 node (even hinted) | Fastest write |

> **Strong Consistency Formula:**  
> `Read CL + Write CL > Replication Factor` = **Strong Consistency**  
> Example: `QUORUM + QUORUM > 3`

**Analogy:** Think of CL as needing signatures on a document. `ONE` = just one co-signer. `QUORUM` = majority of the board. `ALL` = everyone must sign, or it's invalid.

---

#### 4.5.1.2 Lightweight Transactions and Paxos

Cassandra supports **compare-and-swap (CAS)** operations using the **Paxos consensus protocol**:

```sql
-- Only insert if the user doesn't already exist (IF NOT EXISTS)
INSERT INTO users (user_id, email)
VALUES (uuid(), 'alice@example.com')
IF NOT EXISTS;

-- Only update if current value matches (optimistic locking)
UPDATE users SET email = 'new@example.com'
WHERE user_id = <uuid>
IF email = 'old@example.com';
```

> **Warning:** Lightweight Transactions (LWT) are **significantly slower** (4x round trips using Paxos). Use sparingly, only when true compare-and-swap is required.

---

### 4.5.2 Read and Write Path

#### 4.5.2.1 Memtables, SSTables, and Commit Logs

**Write Path:**

```
Write Request
    │
    ├──→ Commit Log (WAL — durability on disk, sequential write)
    │
    └──→ Memtable (in-memory table, fast write)
              │
              │  [when full or timeout]
              ▼
           SSTable (immutable, sorted file on disk)
```

- **Commit Log**: Write-Ahead Log (WAL) ensures durability. If Cassandra crashes, this is replayed on restart.
- **Memtable**: In-memory buffer, writes are lightning fast here.
- **SSTable (Sorted String Table)**: Immutable on-disk file, once written, never modified. New versions of rows create new SSTables.

**Read Path:**

```
Read Request
    │
    ├──→ Row Cache (if enabled and hit → return immediately)
    │
    ├──→ Bloom Filter (is this key probably in this SSTable?)
    │
    ├──→ Key Cache (is the offset of this key cached?)
    │
    ├──→ Partition Summary → Partition Index
    │
    └──→ SSTable data file → return result
```

---

#### 4.5.2.2 Bloom Filters and Caching

**Bloom Filter:**
- A **probabilistic data structure** that quickly answers: *"Is this row definitely NOT in this SSTable?"*
- If the Bloom Filter says NO → skip the SSTable entirely (huge I/O savings).
- Can have **false positives** (says "maybe yes" when actually no) — but **never false negatives**.

**Caching Options:**

| Cache | What It Stores | Use Case |
|---|---|---|
| **Row Cache** | Entire rows from SSTables | Frequently read, rarely updated rows |
| **Key Cache** | SSTable offset for a partition key | General purpose; enabled by default |
| **Chunk Cache** | Compressed SSTable chunks (off-heap) | High-throughput read workloads |
| **Counter Cache** | Counter column values | Counter-heavy workloads |

---

### 4.5.3 Background Processes

#### 4.5.3.1 Hinted Handoff

**Problem:** Node B goes down. A write meant for Node B arrives.  
**Solution:** Another node (the coordinator) stores a **hint**, a temporary record of the write.

- When Node B comes back online, the coordinator **replays the hint** to bring it up to date.
- Hints are stored for a configurable duration (`max_hint_window`: default 3 hours).
- After the window expires, the data is no longer hinted → **read repair** or **manual repair** needed.

> **Key Term:** **Hinted Handoff** — a mechanism ensuring writes are not lost when a replica node is temporarily unavailable, by storing the write as a "hint" on another node.

---

#### 4.5.3.2 Anti-Entropy and Repair

Over time, replicas can drift out of sync. **Anti-Entropy Repair** fixes this:

- Uses **Merkle Trees** (hash trees) to efficiently compare data between replicas.
- Only rows that actually differ are synchronized, not entire datasets.
- Run with **nodetool repair**.

```bash
# Full repair of a node
nodetool repair

# Repair specific keyspace
nodetool repair my_keyspace

# Incremental repair (faster, modern approach)
nodetool repair --incremental
```

> **Merkle Tree Analogy:** Think of it like a file system checksum tree, if the top-level hash matches, nothing inside changed. If it doesn't, drill down until you find exactly which files differ. Efficient!

---

#### 4.5.3.3 Compaction

**Problem:** Every update/delete creates a new SSTable. Over time → thousands of SSTable files → slow reads.  
**Solution:** **Compaction** merging SSTables into fewer, larger, optimized files.

During compaction:
- Old versions of updated rows are discarded.
- **Tombstones** (deletion markers) are removed (after `gc_grace_seconds` window).
- Remaining data is merged into a new, cleaner SSTable.

**Compaction Strategies:**

| Strategy | Best For | How It Works |
|---|---|---|
| **STCS** (Size-Tiered) | Write-heavy workloads (default) | Merges SSTables of similar size |
| **LCS** (Leveled) | Read-heavy workloads | Organizes SSTables into levels; more predictable reads |
| **TWCS** (Time-Window) | Time-series data | Groups SSTables by time window; perfect for expiring data |

```sql
-- Set compaction strategy on a table
ALTER TABLE sensor_readings
WITH compaction = {'class': 'TimeWindowCompactionStrategy',
                   'compaction_window_unit': 'HOURS',
                   'compaction_window_size': 1};
```

> **Key Term:** **Tombstone**, a special marker written to disk when a row or column is deleted. It tells Cassandra "this data was deleted" until compaction cleans it up.

---

### 4.5.4 System Management

#### 4.5.4.1 Managers and Services Overview

Cassandra's internal architecture includes several key managers:

| Manager / Service | Responsibility |
|---|---|
| **StorageService** | Coordinates ring operations, token assignment |
| **StorageProxy** | Routes read/write requests to correct replicas |
| **MessagingService** | Handles inter-node communication |
| **GossipStage** | Manages gossip protocol execution |
| **CommitLogService** | Manages WAL writes and fsync |
| **CompactionManager** | Schedules and executes compaction tasks |
| **HintedHandoffManager** | Stores and replays hints for unavailable nodes |
| **RepairService** | Coordinates anti-entropy repair operations |
| **StreamManager** | Manages data streaming during topology changes |

---

#### 4.5.4.2 System Keyspaces

Cassandra maintains internal **system keyspaces** that store cluster metadata. Never delete or modify these!

| Keyspace | Contents |
|---|---|
| `system` | Local node state, schema, compaction history |
| `system_schema` | All keyspace, table, and type definitions |
| `system_auth` | User credentials and permissions |
| `system_distributed` | Distributed metadata: repair history, views |
| `system_traces` | Query trace data (for debugging) |

```sql
-- Inspect system keyspace
SELECT * FROM system.local;
SELECT * FROM system_schema.keyspaces;
SELECT * FROM system_schema.tables WHERE keyspace_name = 'my_app';
```

---

## References

- Apache Cassandra Documentation — [https://cassandra.apache.org/doc/latest/](https://cassandra.apache.org/doc/latest/)
- DataStax Documentation — [https://docs.datastax.com/](https://docs.datastax.com/)
- Eben Hewitt & Jeff Carpenter — *Cassandra: The Definitive Guide* (O'Reilly) — [https://www.oreilly.com/library/view/cassandra-the-definitive/9781491933657/](https://www.oreilly.com/library/view/cassandra-the-definitive/9781491933657/)
- CAP Theorem — Brewer, E. (2000) — [https://dl.acm.org/doi/10.1145/343477.343502](https://dl.acm.org/doi/10.1145/343477.343502)
- DataStax Data Modeling Guide — [https://docs.datastax.com/en/dse/6.8/dse-dev/datastax_enterprise/dbDesign/dbDesignIntro.html](https://docs.datastax.com/en/dse/6.8/dse-dev/datastax_enterprise/dbDesign/dbDesignIntro.html)
- Apache Cassandra GitHub — [https://github.com/apache/cassandra](https://github.com/apache/cassandra)
- Paxos / Lightweight Transactions — [https://cassandra.apache.org/doc/latest/cassandra/cql/dml.html#conditions](https://cassandra.apache.org/doc/latest/cassandra/cql/dml.html#conditions)

---

## TL;DR, The Cheat Sheet 

> Too long? Fine. Here's everything squeezed into one power block:

**Architecture:**
- Cassandra = **peer-to-peer ring**, no master node, **no single point of failure**
- Data is distributed via **consistent hashing** of the partition key
- **Virtual nodes (vnodes)** = even load distribution across the ring
- **Gossip Protocol** = how nodes know each other's state

**📐 Data Model:**
- **Keyspace** → **Table** → **Row** → **Column**
- Primary Key = **Partition Key** (which node?) + **Clustering Columns** (sort order within partition)
- **Design tables around queries**, NOT around data entities — this is the #1 rule

**Read & Write Path:**
- Writes go to **Commit Log** (durability) + **Memtable** (speed) → flush to **SSTable** (disk)
- Reads use **Bloom Filters** → **Caches** → **SSTables**
- **Compaction** merges SSTables and removes stale data/tombstones

**Consistency:**
- Cassandra is **AP** (CAP Theorem) — favors Availability + Partition Tolerance
- Use **tuneable consistency levels** (`ONE`, `QUORUM`, `ALL`) to balance speed vs. accuracy
- `QUORUM + QUORUM > RF` = strong consistency guarantee

**Operations:**
- `nodetool status` — check node health
- `nodetool repair` — sync out-of-sync replicas
- `cqlsh` — your SQL-like interface into Cassandra
- **Hinted Handoff** = writes survive temporary node failures
- **Anti-Entropy Repair** using Merkle Trees = long-term replica synchronization

**What Cassandra Can't Do (and you shouldn't try):**
- No JOINs
- No ad hoc queries across arbitrary columns
- No foreign keys
- Avoid `ALLOW FILTERING` (it's a table scan — very slow)

---