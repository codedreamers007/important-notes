# Database Principles - Comprehensive Guide

## Table of Contents
1. [Database Fundamentals](#database-fundamentals)
2. [ACID Properties](#acid-properties)
3. [Database Normalization](#database-normalization)
4. [Indexing Strategies](#indexing-strategies)
5. [Transaction Management](#transaction-management)
6. [Concurrency Control](#concurrency-control)
7. [Database Scaling](#database-scaling)
8. [SQL vs NoSQL](#sql-vs-nosql)
9. [Database Design Patterns](#database-design-patterns)
10. [Query Optimization](#query-optimization)
11. [Common Interview Questions](#common-interview-questions)

---

## Database Fundamentals

### What is a Database?

**Definition**: A database is an organized collection of data stored and accessed electronically.

**Purpose**:
- Persistent storage
- Data organization
- Efficient retrieval
- Data integrity
- Concurrent access

### Database Types

**Relational Databases (RDBMS)**:
- Structured data in tables
- Relationships between tables
- SQL for queries
- ACID properties
- Examples: MySQL, PostgreSQL, Oracle, SQL Server

**NoSQL Databases**:
- **Document**: MongoDB, CouchDB
- **Key-Value**: Redis, DynamoDB
- **Column**: Cassandra, HBase
- **Graph**: Neo4j

**NewSQL Databases**:
- Combine SQL and NoSQL benefits
- Distributed SQL
- Examples: CockroachDB, Spanner

### Database Components

**1. Tables/Collections**:
- Store data in structured format
- Rows (documents) and columns (fields)
- Schema definition

**2. Indexes**:
- Improve query performance
- Fast data lookup
- Trade-off: Storage and write performance

**3. Transactions**:
- Group of operations
- All or nothing
- ACID properties

**4. Queries**:
- Retrieve and manipulate data
- SQL or query language
- Optimized execution

---

## ACID Properties

### Atomicity

**Definition**: All operations in a transaction succeed or all fail.

**Example**:
- Transfer money from Account A to Account B
- Debit from A and credit to B must both happen
- If either fails, both are rolled back

**Implementation**:
- Transaction log
- Rollback mechanism
- All-or-nothing execution

### Consistency

**Definition**: Database remains in valid state after transaction.

**Aspects**:
- **Referential integrity**: Foreign keys valid
- **Data constraints**: Rules enforced
- **Business rules**: Domain constraints
- **Schema constraints**: Data types, nulls

**Example**:
- Account balance cannot be negative
- Foreign key references must exist
- Data types must match schema

### Isolation

**Definition**: Concurrent transactions don't interfere with each other.

**Isolation Levels** (from weakest to strongest):

**1. Read Uncommitted**:
- Can read uncommitted data
- Dirty reads possible
- Lowest isolation
- Rarely used

**2. Read Committed**:
- Can only read committed data
- Prevents dirty reads
- Non-repeatable reads possible
- Default in many databases

**3. Repeatable Read**:
- Same query returns same result
- Prevents dirty and non-repeatable reads
- Phantom reads possible
- Locks rows read

**4. Serializable**:
- Highest isolation
- Transactions appear serial
- Prevents all anomalies
- Lowest concurrency

**Concurrency Issues**:

**Dirty Read**:
- Read uncommitted data
- Data may be rolled back
- Inconsistent state

**Non-Repeatable Read**:
- Same query returns different results
- Data changed between reads
- Within same transaction

**Phantom Read**:
- New rows appear
- Range query returns different rows
- Insertions during transaction

### Durability

**Definition**: Committed changes persist even after system failure.

**Mechanisms**:
- **Write-ahead logging (WAL)**: Log before writing
- **Transaction logs**: Record all changes
- **Disk persistence**: Write to non-volatile storage
- **Replication**: Multiple copies

**Example**:
- After commit, data written to disk
- System crash doesn't lose committed data
- Recovery from logs if needed

---

## Database Normalization

### Purpose

**Normalization**: Process of organizing data to reduce redundancy and improve integrity.

**Goals**:
- Eliminate data redundancy
- Prevent update anomalies
- Ensure data consistency
- Simplify database structure

### Normal Forms

**1NF (First Normal Form)**:
- Each column contains atomic values
- No repeating groups
- Each row is unique

**Example**:
```
❌ Not 1NF:
OrderID | Products
--------|----------
1       | Apple, Banana, Orange

✅ 1NF:
OrderID | Product
--------|--------
1       | Apple
1       | Banana
1       | Orange
```

**2NF (Second Normal Form)**:
- Must be in 1NF
- All non-key attributes fully dependent on primary key
- No partial dependencies

**Example**:
```
❌ Not 2NF:
OrderID | ProductID | ProductName | Quantity
--------|-----------|-------------|----------
1       | 101       | Apple       | 5

ProductName depends on ProductID, not OrderID

✅ 2NF:
Orders: OrderID, ProductID, Quantity
Products: ProductID, ProductName
```

**3NF (Third Normal Form)**:
- Must be in 2NF
- No transitive dependencies
- Non-key attributes depend only on primary key

**Example**:
```
❌ Not 3NF:
EmployeeID | Department | DepartmentLocation
-----------|------------|-------------------
1          | Sales      | New York

DepartmentLocation depends on Department, not EmployeeID

✅ 3NF:
Employees: EmployeeID, Department
Departments: Department, Location
```

**BCNF (Boyce-Codd Normal Form)**:
- Stricter than 3NF
- Every determinant is a candidate key
- Handles overlapping candidate keys

**4NF (Fourth Normal Form)**:
- Must be in BCNF
- No multi-valued dependencies
- Separate multi-valued attributes

**5NF (Fifth Normal Form)**:
- Must be in 4NF
- No join dependencies
- Further decomposition not beneficial

### Denormalization

**Purpose**: Intentionally introduce redundancy for performance.

**When to Denormalize**:
- Read performance critical
- Joins are expensive
- Data rarely changes
- Storage cost acceptable

**Trade-offs**:
- ✅ Faster reads
- ✅ Fewer joins
- ❌ Data redundancy
- ❌ Update anomalies
- ❌ More storage

---

## Indexing Strategies

### What is an Index?

**Definition**: Data structure that improves speed of data retrieval.

**Analogy**: Like book index - points to data location.

**How It Works**:
- Creates separate structure
- Sorted for fast lookup
- Points to actual data
- Trade-off: Storage and write performance

### Index Types

**1. Primary Index**:
- On primary key
- Unique and clustered
- Automatically created
- Fastest access

**2. Secondary Index**:
- On non-primary key columns
- Non-clustered
- Can be multiple
- Slower than primary

**3. Composite Index**:
- Multiple columns
- Order matters
- Left-prefix rule
- Example: (last_name, first_name)

**4. Unique Index**:
- Enforces uniqueness
- Can be on multiple columns
- Prevents duplicates

**5. Partial Index**:
- Index subset of rows
- Conditional index
- Smaller index size
- Example: Active users only

### Index Structures

**B-Tree Index**:
- Balanced tree structure
- Most common
- Good for range queries
- Logarithmic search time

**Hash Index**:
- Hash function
- O(1) lookup
- Only equality queries
- No range queries

**Bitmap Index**:
- Bit arrays
- Good for low-cardinality columns
- Space efficient
- Fast for AND/OR operations

### Index Design Principles

**When to Index**:
- Frequently queried columns
- Foreign keys
- Columns in WHERE clause
- Columns in JOIN conditions
- Columns in ORDER BY

**When NOT to Index**:
- Rarely queried columns
- Frequently updated columns
- Small tables
- Columns with low selectivity
- Too many indexes

**Index Maintenance**:
- Rebuild periodically
- Monitor index usage
- Remove unused indexes
- Update statistics

---

## Transaction Management

### What is a Transaction?

**Definition**: Sequence of database operations executed as single unit.

**Properties**: ACID (Atomicity, Consistency, Isolation, Durability)

### Transaction States

**1. Active**:
- Transaction executing
- Operations being performed

**2. Partially Committed**:
- All operations completed
- Not yet committed
- Can still abort

**3. Committed**:
- Changes made permanent
- Cannot be rolled back
- Transaction successful

**4. Failed**:
- Error occurred
- Cannot continue
- Must be aborted

**5. Aborted**:
- Rolled back
- Database restored to previous state
- Can restart transaction

### Transaction Control

**BEGIN/START TRANSACTION**:
- Start new transaction
- Begin isolation

**COMMIT**:
- Save changes permanently
- End transaction
- Release locks

**ROLLBACK**:
- Undo all changes
- Restore previous state
- End transaction

**SAVEPOINT**:
- Mark point in transaction
- Can rollback to savepoint
- Partial rollback

### Nested Transactions

**Concept**: Transactions within transactions.

**Behavior**:
- Outer transaction controls
- Inner transaction commits to outer
- Outer rollback rolls back all
- Implementation varies by database

---

## Concurrency Control

### Problem

**Concurrency**: Multiple transactions executing simultaneously.

**Issues**:
- Lost updates
- Dirty reads
- Inconsistent analysis
- Phantom reads

### Locking Mechanisms

**1. Shared Lock (Read Lock)**:
- Multiple transactions can hold
- Prevents writes
- Allows reads
- Compatible with other shared locks

**2. Exclusive Lock (Write Lock)**:
- Only one transaction can hold
- Prevents reads and writes
- Not compatible with any lock
- Required for writes

**Lock Granularity**:

**Row-Level Locks**:
- Lock individual rows
- Fine-grained
- High concurrency
- More overhead

**Table-Level Locks**:
- Lock entire table
- Coarse-grained
- Lower concurrency
- Less overhead

**Page-Level Locks**:
- Lock data pages
- Medium granularity
- Balance of concurrency and overhead

### Deadlocks

**Definition**: Two or more transactions waiting for each other.

**Example**:
- Transaction A locks Row 1, wants Row 2
- Transaction B locks Row 2, wants Row 1
- Both wait forever

**Prevention**:
- Lock ordering
- Timeout mechanisms
- Deadlock detection

**Resolution**:
- Detect deadlock
- Abort one transaction
- Release locks
- Retry transaction

### Optimistic Concurrency Control

**Concept**: Assume no conflicts, detect if they occur.

**Process**:
1. Read data (no locks)
2. Make changes
3. Check for conflicts (version numbers, timestamps)
4. Commit if no conflict, retry if conflict

**Use When**:
- Low conflict probability
- Read-heavy workloads
- Better performance
- More retries possible

### Pessimistic Concurrency Control

**Concept**: Assume conflicts, prevent them with locks.

**Process**:
1. Acquire locks
2. Read/write data
3. Release locks
4. Commit

**Use When**:
- High conflict probability
- Write-heavy workloads
- Consistency critical
- Lower performance

---

## Database Scaling

### Vertical Scaling (Scale Up)

**Definition**: Add more resources to existing server.

**Resources**:
- CPU
- RAM
- Storage
- Network

**Pros**:
- Simple
- No code changes
- No data distribution

**Cons**:
- Hardware limits
- Expensive
- Single point of failure
- Limited scalability

### Horizontal Scaling (Scale Out)

**Definition**: Add more servers.

**Approaches**:

**1. Read Replicas**:
- Master for writes
- Replicas for reads
- Asynchronous replication
- Improved read performance
- Eventual consistency

**2. Sharding**:
- Partition data across servers
- Each shard independent
- Horizontal scaling
- Shard key critical

**3. Partitioning**:
- Split table into partitions
- Can be on same or different servers
- Horizontal or vertical partitioning

### Sharding Strategies

**1. Range Sharding**:
- Partition by value ranges
- Example: UserID 1-1000 on Shard 1
- Simple to implement
- Potential hotspots

**2. Hash Sharding**:
- Hash function on shard key
- Even distribution
- No range queries across shards
- Better load distribution

**3. Directory Sharding**:
- Lookup table for shard mapping
- Flexible
- Single point of failure
- More complex

**Sharding Challenges**:
- Cross-shard queries
- Transactions across shards
- Rebalancing
- Joins difficult

### Replication

**Master-Slave Replication**:
- One master, multiple slaves
- Writes to master
- Reads from slaves
- Asynchronous or synchronous

**Master-Master Replication**:
- Multiple masters
- Writes to any master
- Conflict resolution needed
- More complex

**Use Cases**:
- High availability
- Read scaling
- Geographic distribution
- Backup and recovery

---

## SQL vs NoSQL

### SQL Databases

**Characteristics**:
- Structured data
- Schema required
- ACID properties
- Relational model
- SQL queries
- Vertical scaling preferred

**Use Cases**:
- Complex queries
- Transactions
- Structured data
- Data integrity critical
- Established applications

**Examples**: MySQL, PostgreSQL, Oracle, SQL Server

### NoSQL Databases

**Types**:

**1. Document Databases**:
- Store documents (JSON, BSON)
- Schema flexible
- Examples: MongoDB, CouchDB

**2. Key-Value Stores**:
- Simple key-value pairs
- Fast lookups
- Examples: Redis, DynamoDB

**3. Column Stores**:
- Store by columns
- Good for analytics
- Examples: Cassandra, HBase

**4. Graph Databases**:
- Nodes and relationships
- Graph queries
- Examples: Neo4j, Amazon Neptune

**Characteristics**:
- Schema flexible
- Horizontal scaling
- Eventual consistency
- High performance
- Simple data models

**Use Cases**:
- Large scale
- Unstructured data
- Fast writes
- Horizontal scaling needed
- Simple queries

### Choosing SQL vs NoSQL

**Choose SQL When**:
- Complex queries
- ACID transactions
- Structured data
- Data relationships important
- Established patterns

**Choose NoSQL When**:
- Large scale
- Unstructured data
- Simple queries
- Horizontal scaling
- Fast writes

**Hybrid Approach**:
- Use both
- SQL for transactional data
- NoSQL for analytics, caching
- Polyglot persistence

---

## Database Design Patterns

### 1. Repository Pattern

**Purpose**: Abstract data access layer.

**Benefits**:
- Decouple business logic from data access
- Easier testing
- Switch databases easily
- Centralized data access

### 2. Unit of Work Pattern

**Purpose**: Track changes and commit as unit.

**Benefits**:
- Transaction management
- Change tracking
- Batch operations
- Consistency

### 3. Active Record Pattern

**Purpose**: Object represents database row.

**Benefits**:
- Simple CRUD
- Direct mapping
- Less abstraction
- Quick development

### 4. Data Mapper Pattern

**Purpose**: Separate domain objects from database.

**Benefits**:
- Clean separation
- Flexible mapping
- Testable
- More complex

### 5. CQRS (Command Query Responsibility Segregation)

**Purpose**: Separate read and write models.

**Benefits**:
- Optimized for each operation
- Independent scaling
- Event sourcing compatible
- More complex

---

## Query Optimization

### Optimization Techniques

**1. Use Indexes**:
- Index frequently queried columns
- Composite indexes for multiple columns
- Monitor index usage

**2. Avoid SELECT ***:
- Select only needed columns
- Reduces data transfer
- Better index usage

**3. Use LIMIT**:
- Limit result set
- Reduce data processing
- Faster queries

**4. Avoid Functions in WHERE**:
- Prevents index usage
- Calculate before query
- Use indexed columns

**5. Use JOINs Efficiently**:
- Proper join order
- Index join columns
- Avoid cartesian products

**6. Avoid N+1 Queries**:
- Use JOINs or batch loading
- Reduce database round trips
- Eager loading

**7. Use EXPLAIN**:
- Analyze query execution
- Identify bottlenecks
- Optimize based on plan

**8. Normalize Appropriately**:
- Balance normalization and performance
- Denormalize when needed
- Consider read vs write patterns

### Query Execution Plan

**Purpose**: Understand how database executes query.

**Components**:
- Table scans vs index scans
- Join algorithms
- Sort operations
- Filter operations

**Analysis**:
- Identify full table scans
- Check index usage
- Find expensive operations
- Optimize accordingly

---

## Common Interview Questions

### Fundamental Questions

1. **What are ACID properties?**
   - Atomicity, Consistency, Isolation, Durability
   - Ensure reliable transactions
   - Critical for data integrity

2. **Explain database normalization?**
   - Organize data to reduce redundancy
   - Normal forms (1NF to 5NF)
   - Trade-offs with denormalization

3. **What is an index?**
   - Data structure for fast lookup
   - Improves query performance
   - Trade-off: Storage and writes

4. **SQL vs NoSQL?**
   - SQL: Structured, ACID, relational
   - NoSQL: Flexible, scalable, various types
   - Choose based on requirements

5. **What is a transaction?**
   - Group of operations as single unit
   - ACID properties
   - All or nothing execution

6. **Explain isolation levels?**
   - Read Uncommitted, Read Committed, Repeatable Read, Serializable
   - Trade-off between consistency and concurrency
   - Prevent different anomalies

7. **What is database sharding?**
   - Partition data across servers
   - Horizontal scaling
   - Challenges with cross-shard operations

8. **What is replication?**
   - Copy data to multiple servers
   - High availability
   - Read scaling
   - Master-slave or master-master

9. **How to optimize queries?**
   - Use indexes
   - Avoid SELECT *
   - Optimize JOINs
   - Use EXPLAIN
   - Denormalize when needed

10. **What is a deadlock?**
    - Transactions waiting for each other
    - Prevention and detection
    - Resolution strategies

---

## Advanced Interview Questions & Answers

### Q1: Explain the CAP theorem in the context of databases. How do different database types handle it?

**Answer**:

**CAP Theorem**:
- **Consistency (C)**: All nodes see same data simultaneously
- **Availability (A)**: System remains operational
- **Partition Tolerance (P)**: System continues despite network partitions

**The Constraint**: Can only guarantee two out of three in distributed systems.

**Database Classifications**:

**1. CP Systems (Consistency + Partition Tolerance)**:
- **Examples**: MongoDB, HBase, Traditional RDBMS with replication
- **Characteristics**:
  - Strong consistency
  - Sacrifices availability during partitions
  - Blocks writes during network partition
  - Data always consistent

**2. AP Systems (Availability + Partition Tolerance)**:
- **Examples**: Cassandra, DynamoDB, CouchDB
- **Characteristics**:
  - High availability
  - Eventual consistency
  - Continues operating during partitions
  - May serve stale data

**3. CA Systems (Consistency + Availability)**:
- **Examples**: Single-node databases, non-distributed systems
- **Characteristics**:
  - Not possible in truly distributed systems
  - Only works without network partitions
  - Single point of failure

**Real-World Examples**:

**MongoDB (CP)**:
- Replica sets provide consistency
- During partition, one side becomes primary
- Other side unavailable for writes
- Ensures data consistency

**Cassandra (AP)**:
- Always available
- Eventual consistency
- Multiple nodes can accept writes
- Conflict resolution on read (last write wins or vector clocks)

**PostgreSQL (CP)**:
- Master-slave replication
- During partition, only master accepts writes
- Slaves unavailable for writes
- Strong consistency maintained

**Trade-offs**:

| System Type | Consistency | Availability | Use Case |
|-------------|-------------|-------------|----------|
| CP | Strong | Sacrificed | Financial systems, critical data |
| AP | Eventual | High | Social media, content delivery |
| CA | Strong | High | Single-node, non-distributed |

**Choosing the Right System**:
- **CP**: When consistency is critical (banking, transactions)
- **AP**: When availability is critical (social feeds, recommendations)
- **Consider**: Business requirements, data criticality, user experience

---

### Q2: Explain how database indexing works internally. Detail B-tree and B+tree structures.

**Answer**:

**Why Indexes?**:
- Speed up data retrieval
- Avoid full table scans
- Enable efficient lookups
- Support range queries

**B-Tree Index**:

**Structure**:
- Balanced tree structure
- All nodes contain data
- Internal nodes have keys and pointers
- Leaf nodes have keys and data

**Properties**:
- **Balanced**: All leaf nodes at same level
- **Ordered**: Keys sorted within nodes
- **Fanout**: Each node has multiple children
- **Height**: Logarithmic height (log n)

**Example**:
```
        [50]
       /    \
   [20,30]  [70,80]
   /  |  \   /  |  \
[10][25][40][60][75][90]
```

**Operations**:

**Search**:
1. Start at root
2. Compare key with node keys
3. Follow appropriate pointer
4. Continue to leaf
5. Time: O(log n)

**Insert**:
1. Find appropriate leaf
2. Insert key in sorted order
3. If node full, split node
4. Propagate split upward if needed
5. Time: O(log n)

**Delete**:
1. Find key in leaf
2. Remove key
3. If underflow, merge with sibling
4. Propagate merge upward if needed
5. Time: O(log n)

**B+Tree Index** (Most databases use this):

**Differences from B-Tree**:
- **Data only in leaves**: Internal nodes only have keys
- **Leaf nodes linked**: Sequential access possible
- **Higher fanout**: More keys per node
- **Better for range queries**: Sequential scan of leaves

**Structure**:
```
        [50]
       /    \
   [20,30]  [70,80]
   /  |  \   /  |  \
[10→20→30][50→60→70][80→90]
(linked leaves)
```

**Advantages**:
- **Range Queries**: Efficient sequential access
- **Higher Fanout**: Fewer levels, faster searches
- **Cache Efficiency**: Internal nodes fit in cache
- **Sequential Access**: Linked leaves enable scanning

**Database Implementations**:
- **MySQL InnoDB**: B+Tree
- **PostgreSQL**: B-Tree (similar to B+Tree)
- **Oracle**: B-Tree indexes
- **SQL Server**: B-Tree indexes

**Index Types**:

**1. Primary Index**:
- On primary key
- Clustered (data sorted by key)
- One per table
- Fastest access

**2. Secondary Index**:
- On non-primary columns
- Non-clustered
- Multiple per table
- Points to data location

**3. Composite Index**:
- Multiple columns
- Order matters
- Left-prefix rule applies
- Example: (last_name, first_name)

**Index Maintenance**:
- **Insert**: Update index
- **Update**: Update index if indexed column changed
- **Delete**: Remove from index
- **Rebuild**: Periodically rebuild for optimization

**Best Practices**:
1. Index frequently queried columns
2. Index foreign keys
3. Consider composite indexes for multi-column queries
4. Don't over-index (slows writes)
5. Monitor index usage

---

### Q3: Explain database sharding strategies and how to handle cross-shard queries.

**Answer**:

**Sharding Definition**:
- Partition data across multiple databases
- Each shard is independent database
- Horizontal scaling
- Distribute load

**Sharding Strategies**:

**1. Range Sharding**:
- Partition by value ranges
- Example: UserID 1-1000 on Shard 1, 1001-2000 on Shard 2
- **Pros**: Simple, easy to understand
- **Cons**: Potential hotspots, uneven distribution

**2. Hash Sharding**:
- Hash function on shard key
- Even distribution
- Example: `shard = hash(user_id) % num_shards`
- **Pros**: Even distribution, no hotspots
- **Cons**: No range queries across shards, rebalancing difficult

**3. Directory Sharding**:
- Lookup table for shard mapping
- Flexible shard assignment
- **Pros**: Flexible, easy to rebalance
- **Cons**: Single point of failure, lookup overhead

**4. Geographic Sharding**:
- Partition by geographic location
- Example: US users on Shard 1, EU users on Shard 2
- **Pros**: Data locality, compliance
- **Cons**: Uneven distribution possible

**Shard Key Selection**:
- **Critical Decision**: Affects performance and scalability
- **Characteristics**:
  - High cardinality (many unique values)
  - Even distribution
  - Frequently used in queries
  - Immutable or rarely changed

**Cross-Shard Queries**:

**The Challenge**:
- Query needs data from multiple shards
- Expensive and slow
- Complex to implement

**Solutions**:

**1. Avoid Cross-Shard Queries**:
- Design schema to minimize cross-shard queries
- Denormalize data if needed
- Use application-level joins

**2. Fan-Out Queries**:
- Query all relevant shards
- Aggregate results in application
- **Example**: "Find all orders for user"
  - Query all shards
  - Combine results
  - Sort/limit in application

**3. Replication**:
- Replicate frequently accessed data
- Read from local replica
- **Example**: User profile replicated to all shards

**4. Materialized Views**:
- Pre-compute cross-shard data
- Store in separate table
- Update periodically

**5. Two-Phase Query**:
- Phase 1: Get IDs from all shards
- Phase 2: Fetch full data from specific shards
- Reduces data transfer

**Sharding Challenges**:

**1. Rebalancing**:
- Moving data between shards
- Complex and expensive
- Requires downtime or careful migration

**2. Joins**:
- Can't join across shards efficiently
- Must do in application
- Denormalize or replicate

**3. Transactions**:
- Can't have ACID transactions across shards
- Use eventual consistency
- Saga pattern for distributed transactions

**4. Schema Changes**:
- Must apply to all shards
- Coordination required
- Rolling updates

**Best Practices**:
1. Choose shard key carefully
2. Design to minimize cross-shard queries
3. Plan for rebalancing
4. Monitor shard sizes
5. Handle failures gracefully

---

### Q4: Explain the difference between optimistic and pessimistic concurrency control. When would you use each?

**Answer**:

**Pessimistic Concurrency Control**:

**How It Works**:
- Assume conflicts will occur
- Lock data before access
- Prevent other transactions from accessing
- Release lock after transaction

**Lock Types**:
- **Shared Lock (Read Lock)**: Multiple readers, no writers
- **Exclusive Lock (Write Lock)**: No other access

**Example**:
```sql
BEGIN TRANSACTION;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE; -- Lock row
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT; -- Release lock
```

**Characteristics**:
- **Locks Early**: Before accessing data
- **Blocks Others**: Other transactions wait
- **Consistency**: Strong consistency
- **Performance**: Lower concurrency

**Use When**:
- High conflict probability
- Data consistency critical
- Can't tolerate conflicts
- Write-heavy workloads

**Optimistic Concurrency Control**:

**How It Works**:
- Assume no conflicts
- Don't lock data
- Check for conflicts before commit
- Rollback if conflict detected

**Versioning**:
- Each row has version number/timestamp
- Read version when reading
- Check version when updating
- Update fails if version changed

**Example**:
```sql
-- Read
SELECT id, balance, version FROM accounts WHERE id = 1;
-- Balance = 1000, version = 5

-- Update (later)
UPDATE accounts 
SET balance = 900, version = version + 1 
WHERE id = 1 AND version = 5; -- Fails if version changed
```

**Characteristics**:
- **No Locks**: Don't lock during read
- **Check on Commit**: Verify no conflicts
- **Retry on Conflict**: Rollback and retry
- **Performance**: Higher concurrency

**Use When**:
- Low conflict probability
- Read-heavy workloads
- Can tolerate retries
- Better performance needed

**Comparison**:

| Aspect | Pessimistic | Optimistic |
|--------|-------------|------------|
| **Locks** | Yes, early | No, check late |
| **Blocking** | Blocks others | No blocking |
| **Conflicts** | Prevents | Detects |
| **Performance** | Lower concurrency | Higher concurrency |
| **Retries** | Not needed | May need retries |
| **Use Case** | High conflicts | Low conflicts |

**Hybrid Approach**:
- Use pessimistic for critical sections
- Use optimistic for less critical data
- Balance based on requirements

**Best Practices**:
1. **Pessimistic**: For financial transactions, inventory
2. **Optimistic**: For user profiles, content, low-conflict data
3. **Monitor**: Track conflict rates
4. **Tune**: Adjust based on actual conflicts
5. **Fallback**: Have retry logic for optimistic

---

### Q5: How do you optimize a slow database query? Walk through the process.

**Answer**:

**Step 1: Identify the Problem**:
- Use EXPLAIN or EXPLAIN ANALYZE
- Check execution plan
- Identify full table scans
- Find expensive operations

**Step 2: Analyze Execution Plan**:
```sql
EXPLAIN ANALYZE
SELECT * FROM orders o
JOIN customers c ON o.customer_id = c.id
WHERE c.email = 'user@example.com'
AND o.created_at > '2024-01-01';
```

**Key Metrics**:
- **Seq Scan**: Full table scan (bad)
- **Index Scan**: Using index (good)
- **Cost**: Estimated cost
- **Rows**: Rows examined
- **Time**: Actual execution time

**Step 3: Check Indexes**:
- Are indexes being used?
- Are indexes on right columns?
- Are composite indexes needed?

**Step 4: Optimization Techniques**:

**1. Add Missing Indexes**:
```sql
-- If email frequently queried
CREATE INDEX idx_customers_email ON customers(email);

-- Composite index for multi-column queries
CREATE INDEX idx_orders_customer_date 
ON orders(customer_id, created_at);
```

**2. Rewrite Query**:
```sql
-- Bad: SELECT *
SELECT * FROM orders;

-- Good: Select only needed columns
SELECT id, customer_id, total FROM orders;
```

**3. Avoid Functions in WHERE**:
```sql
-- Bad: Can't use index
WHERE UPPER(email) = 'USER@EXAMPLE.COM';

-- Good: Index can be used
WHERE email = 'user@example.com';
```

**4. Use LIMIT**:
```sql
-- If only need first N results
SELECT * FROM orders ORDER BY created_at DESC LIMIT 10;
```

**5. Optimize JOINs**:
- Ensure join columns are indexed
- Join order matters
- Use appropriate join type

**6. Denormalize**:
- If joins are expensive
- Add redundant data
- Trade storage for performance

**Step 5: Verify Improvements**:
- Run EXPLAIN again
- Compare execution plans
- Measure actual performance
- Monitor in production

**Common Issues and Solutions**:

**Issue 1: Full Table Scan**:
- **Solution**: Add index on WHERE clause column

**Issue 2: N+1 Query Problem**:
```sql
-- Bad: Multiple queries
SELECT * FROM orders;
-- Then for each order:
SELECT * FROM order_items WHERE order_id = ?;

-- Good: Single query with JOIN
SELECT o.*, oi.* 
FROM orders o
LEFT JOIN order_items oi ON o.id = oi.order_id;
```

**Issue 3: Missing Composite Index**:
- **Solution**: Create index on multiple columns used together

**Issue 4: Suboptimal Join Order**:
- **Solution**: Database usually optimizes, but can hint

**Step 6: Monitor and Iterate**:
- Monitor query performance
- Track slow queries
- Adjust indexes based on usage
- Review periodically

**Tools**:
- **EXPLAIN**: Query execution plan
- **Query Profiler**: Detailed timing
- **Slow Query Log**: Log slow queries
- **Monitoring Tools**: Track performance metrics

---

## Key Takeaways

1. **ACID Properties**: Foundation of reliable databases
2. **Normalization**: Balance between structure and performance
3. **Indexing**: Critical for performance, use wisely
4. **Transactions**: Ensure data consistency
5. **Concurrency**: Balance consistency and performance
6. **Scaling**: Horizontal scaling for large scale
7. **SQL vs NoSQL**: Choose based on requirements
8. **Query Optimization**: Multiple techniques for performance
9. **Design Patterns**: Proven solutions for common problems
10. **Trade-offs**: Every decision has trade-offs

