# Technologies Guide - Redis, Kafka, Memcache, Event Management

## Table of Contents
1. [Redis](#redis)
2. [Apache Kafka](#apache-kafka)
3. [Memcached](#memcached)
4. [Event Management & Event-Driven Architecture](#event-management--event-driven-architecture)
5. [Comparison & Use Cases](#comparison--use-cases)
6. [Common Interview Questions](#common-interview-questions)

---

## Redis

### Overview

**Redis (Remote Dictionary Server)** is an in-memory data structure store used as a database, cache, message broker, and streaming engine.

**Key Characteristics**:
- **In-memory**: Data stored in RAM for fast access
- **Data structures**: Strings, Lists, Sets, Sorted Sets, Hashes, Bitmaps, HyperLogLogs
- **Persistence**: Optional disk persistence (RDB snapshots, AOF)
- **Replication**: Master-slave replication
- **High performance**: Sub-millisecond latency
- **Atomic operations**: All operations are atomic

### Data Structures

**1. Strings**:
- Basic key-value storage
- Can store strings, integers, or binary data
- Max size: 512 MB
- Use cases: Caching, counters, session storage

**2. Lists**:
- Ordered collection of strings
- Can add/remove from both ends
- Use cases: Queues, stacks, activity feeds

**3. Sets**:
- Unordered collection of unique strings
- Set operations: union, intersection, difference
- Use cases: Tags, unique visitors, relationships

**4. Sorted Sets**:
- Set with score for ordering
- Range queries by score
- Use cases: Leaderboards, rankings, time-series data

**5. Hashes**:
- Field-value pairs
- Efficient for objects
- Use cases: User profiles, product information

**6. Bitmaps**:
- Bit-level operations
- Space-efficient
- Use cases: User activity tracking, analytics

**7. HyperLogLog**:
- Probabilistic data structure
- Approximate unique count
- Use cases: Unique visitor counting

### Use Cases

**1. Caching**:
- **Session caching**: Store user sessions
- **Page caching**: Cache rendered pages
- **Database query caching**: Cache frequent queries
- **CDN caching**: Edge caching

**Benefits**:
- Reduces database load
- Improves response time
- Handles traffic spikes

**2. Session Storage**:
- Store user session data
- Shared across multiple servers
- TTL for automatic expiration
- Fast access

**3. Real-time Analytics**:
- Counters and metrics
- Leaderboards
- Real-time dashboards
- Event tracking

**4. Message Queuing**:
- Pub/Sub messaging
- Simple message queues
- Task queues
- Event streaming

**5. Rate Limiting**:
- API rate limiting
- User action limiting
- DDoS protection
- Sliding window counters
- Fixed window counters
- Token bucket algorithm
- Distributed rate limiting across servers

**6. Distributed Locks**:
- Prevent race conditions
- Coordinate distributed systems
- Leader election
- Critical section protection

### Persistence Options

**RDB (Redis Database Backup)**:
- Point-in-time snapshots
- Fork-based snapshotting
- Compact file size
- Fast recovery
- May lose data between snapshots

**AOF (Append Only File)**:
- Logs every write operation
- More durable
- Larger file size
- Slower recovery
- Configurable fsync policies

**Both**:
- Can use both for maximum durability
- RDB for backups
- AOF for durability

### Replication & High Availability

**Master-Slave Replication**:
- One master, multiple replicas
- Asynchronous replication
- Read scaling
- Automatic failover with Sentinel

**Redis Sentinel**:
- High availability solution
- Monitors master and replicas
- Automatic failover
- Configuration provider

**Redis Cluster**:
- Sharding across multiple nodes
- Automatic partitioning
- High availability
- Horizontal scaling

### Performance Characteristics

**Latency**:
- Sub-millisecond for simple operations
- Network latency is main factor
- Pipelining reduces round trips

**Throughput**:
- 100K+ operations/second (single instance)
- Scales horizontally with cluster
- Depends on operation complexity

**Memory**:
- All data in RAM
- Memory is limiting factor
- Eviction policies when memory full

### Eviction Policies

When memory is full, Redis can:
- **noeviction**: Return errors on writes
- **allkeys-lru**: Evict least recently used
- **allkeys-lfu**: Evict least frequently used
- **volatile-lru**: Evict LRU among keys with TTL
- **volatile-lfu**: Evict LFU among keys with TTL
- **volatile-ttl**: Evict shortest TTL
- **volatile-random**: Random eviction with TTL
- **allkeys-random**: Random eviction

### Best Practices

1. **Key Naming**: Use consistent, descriptive names
2. **TTL**: Set expiration for temporary data
3. **Pipelining**: Batch operations when possible
4. **Connection Pooling**: Reuse connections
5. **Monitor Memory**: Set maxmemory and eviction policy
6. **Persistence**: Configure based on durability needs
7. **Security**: Use authentication, restrict network access
8. **Monitoring**: Track memory, latency, hit rate

---

## Apache Kafka

### Overview

**Apache Kafka** is a distributed event streaming platform capable of handling trillions of events per day.

**Key Characteristics**:
- **Distributed**: Runs as cluster of brokers
- **Fault-tolerant**: Replication and partitioning
- **High throughput**: Millions of messages per second
- **Durability**: Messages persisted to disk
- **Scalable**: Horizontal scaling
- **Real-time**: Low latency processing

### Core Concepts

**1. Topics**:
- Category or feed name for messages
- Divided into partitions
- Messages are appended to partitions
- Ordering guaranteed within partition

**2. Partitions**:
- Topics split into partitions for parallelism
- Each partition is ordered log
- Messages assigned to partition by key
- Enables horizontal scaling

**3. Producers**:
- Applications that publish messages
- Can specify partition or key
- Asynchronous or synchronous sends
- Batching for performance

**4. Consumers**:
- Applications that read messages
- Organized into consumer groups
- Each partition consumed by one consumer in group
- Offset tracking for position

**5. Brokers**:
- Kafka servers in cluster
- Store topics and partitions
- Handle read/write requests
- Replicate data

**6. Consumer Groups**:
- Group of consumers working together
- Load balancing across consumers
- Each partition consumed by one consumer
- Parallel processing

**7. Offsets**:
- Position in partition
- Consumer tracks offset
- Committed offsets for recovery
- Can reset to earlier offset

### Architecture

**Producer → Kafka Cluster → Consumer**

**Kafka Cluster**:
- Multiple brokers
- Topics with partitions
- Replication for fault tolerance
- Zookeeper for coordination (or KRaft)

**Replication**:
- Each partition replicated across brokers
- One leader, multiple followers
- Leader handles reads/writes
- Followers replicate from leader
- Automatic leader election on failure

### Use Cases

**1. Event Streaming**:
- Real-time event processing
- Event sourcing
- CQRS pattern
- Activity tracking

**2. Messaging**:
- Decouple producers and consumers
- Asynchronous communication
- Message queues
- Pub/Sub messaging

**3. Log Aggregation**:
- Centralized logging
- Application logs
- System logs
- Audit logs

**4. Stream Processing**:
- Real-time data processing
- ETL pipelines
- Data transformation
- Analytics

**5. Metrics & Monitoring**:
- Application metrics
- System metrics
- Time-series data
- Monitoring dashboards

**6. Commit Log**:
- Database change logs
- Event sourcing
- Replication logs
- Audit trails

### Key Features

**1. Durability**:
- Messages written to disk
- Configurable replication factor
- Survives broker failures
- Configurable retention

**2. Scalability**:
- Horizontal scaling
- Add more brokers
- Add more partitions
- Add more consumers

**3. Performance**:
- High throughput
- Low latency
- Batching
- Zero-copy transfers
- Sequential disk I/O

**4. Ordering**:
- Ordering within partition
- Key-based partitioning maintains order
- Global ordering not guaranteed

**5. Retention**:
- Configurable retention period
- Time-based or size-based
- Can be infinite
- Log compaction for keyed topics

### Kafka Streams

**Stream Processing Library**:
- Build real-time applications
- Process streams of data
- Stateful and stateless operations
- Joins, aggregations, transformations
- Exactly-once semantics

### Kafka Connect

**Data Integration**:
- Connect Kafka with external systems
- Source connectors (ingest data)
- Sink connectors (export data)
- Pre-built connectors available

### Best Practices

1. **Partitioning**: Choose partition count based on throughput needs
2. **Replication**: Use replication factor of 3 for production
3. **Retention**: Set appropriate retention based on use case
4. **Consumer Groups**: Design for parallel processing
5. **Idempotency**: Make consumers idempotent
6. **Monitoring**: Monitor lag, throughput, errors
7. **Security**: Enable authentication and encryption
8. **Compression**: Use compression for better throughput

---

## Memcached

### Overview

**Memcached** is a high-performance, distributed memory caching system.

**Key Characteristics**:
- **Simple**: Key-value store
- **In-memory**: All data in RAM
- **Distributed**: Can run on multiple servers
- **Fast**: Sub-millisecond access
- **Volatile**: Data can be evicted
- **No persistence**: Data lost on restart

### Architecture

**Client-Server Model**:
- Memcached servers store data
- Clients connect to servers
- Client hashes key to determine server
- No communication between servers

**Hashing**:
- Consistent hashing for key distribution
- Client determines which server
- Even distribution across servers
- Minimal rehashing on server addition/removal

### Data Model

**Key-Value**:
- Simple key-value pairs
- Keys are strings (max 250 bytes)
- Values are strings or binary (max 1 MB)
- No complex data structures

**Operations**:
- **set**: Store value
- **get**: Retrieve value
- **delete**: Remove key
- **add**: Store only if key doesn't exist
- **replace**: Store only if key exists
- **increment/decrement**: Atomic counter operations

### Use Cases

**1. Database Caching**:
- Cache database query results
- Reduce database load
- Improve response time
- Common pattern: Cache-aside

**2. Session Storage**:
- Store user sessions
- Shared across servers
- Fast access
- TTL for expiration

**3. HTML Caching**:
- Cache rendered HTML pages
- Reduce application load
- Fast page delivery
- TTL-based expiration

**4. API Response Caching**:
- Cache API responses
- Reduce backend load
- Improve latency
- Invalidate on updates

### Eviction

**LRU (Least Recently Used)**:
- Evicts least recently used items
- When memory is full
- Per slab class
- Automatic and transparent

**Slab Allocation**:
- Memory divided into slabs
- Each slab stores items of similar size
- Reduces fragmentation
- Efficient memory usage

### Limitations

**No Persistence**:
- Data lost on restart
- Not suitable for critical data
- Must repopulate on restart

**No Replication**:
- No built-in replication
- Single point of failure per key
- Must handle failures in application

**Simple Data Model**:
- Only key-value
- No complex structures
- No queries
- No relationships

### Memcached vs Redis

**Memcached**:
- Simpler, focused on caching
- Multi-threaded (better for simple operations)
- No persistence
- No complex data structures
- Better for simple caching needs

**Redis**:
- More features (data structures, persistence)
- Single-threaded (simpler, but can be bottleneck)
- Persistence options
- Rich data structures
- Better for complex use cases

**When to Use Memcached**:
- Simple caching needs
- High throughput, simple operations
- Don't need persistence
- Don't need complex data structures

**When to Use Redis**:
- Need persistence
- Need complex data structures
- Need pub/sub, streams
- More than just caching

---

## Event Management & Event-Driven Architecture

### Event-Driven Architecture (EDA)

**Definition**: Architectural pattern where components communicate through events.

**Key Characteristics**:
- **Loose coupling**: Producers and consumers decoupled
- **Asynchronous**: Non-blocking communication
- **Scalable**: Easy to add new consumers
- **Resilient**: Failures isolated
- **Real-time**: Events processed as they occur

### Event Concepts

**Event**:
- Something that happened
- Immutable record
- Contains data about occurrence
- Timestamp and source

**Event Producer**:
- Generates events
- Publishes to event stream
- Doesn't know about consumers
- Fire and forget

**Event Consumer**:
- Subscribes to events
- Processes events
- Can be multiple consumers
- Independent processing

**Event Broker**:
- Routes events
- Manages subscriptions
- Ensures delivery
- Examples: Kafka, RabbitMQ, AWS EventBridge

### Event Patterns

**1. Event Notification**:
- Notify that something happened
- Minimal data in event
- Consumers fetch details if needed
- Lightweight events

**2. Event Carried State Transfer**:
- Event contains all needed data
- Consumers don't need to fetch
- Self-contained events
- Larger event size

**3. Event Sourcing**:
- Store events as source of truth
- Rebuild state from events
- Complete audit trail
- Time travel capability

**4. CQRS with Events**:
- Separate read and write models
- Events update read models
- Eventual consistency
- Optimized for each operation

### Event Types

**Domain Events**:
- Business events
- Part of domain model
- Meaningful to business
- Examples: OrderPlaced, PaymentReceived

**Integration Events**:
- Events for service communication
- Cross-service boundaries
- Technical events
- Examples: UserCreated, OrderShipped

**System Events**:
- Infrastructure events
- Technical events
- Examples: ServiceStarted, ErrorOccurred

### Event Processing

**Simple Event Processing**:
- One event triggers one action
- Direct mapping
- Simple workflows

**Complex Event Processing (CEP)**:
- Pattern matching across events
- Correlation of multiple events
- Time windows
- Rule-based processing

**Stream Processing**:
- Continuous processing
- Real-time analytics
- Aggregations
- Transformations

### Event Ordering

**Challenges**:
- Events may arrive out of order
- Network delays
- Parallel processing
- Multiple producers

**Solutions**:
- **Partitioning**: Same partition for related events
- **Sequence numbers**: Order events by sequence
- **Vector clocks**: Track causality
- **Idempotency**: Handle duplicates

### Event Delivery Guarantees

**At-Most-Once**:
- Event may be lost
- No retries
- Fastest
- Use when loss acceptable

**At-Least-Once**:
- Event delivered at least once
- May have duplicates
- Retries on failure
- Most common

**Exactly-Once**:
- Event delivered exactly once
- No duplicates, no loss
- Most complex
- Requires idempotency

### Event Schema Evolution

**Challenge**: Events change over time

**Solutions**:
- **Versioning**: Include version in event
- **Backward compatibility**: Old consumers handle new events
- **Schema registry**: Centralized schema management
- **Evolution rules**: Guidelines for changes

### Best Practices

1. **Idempotency**: Make consumers idempotent
2. **Event Design**: Include all necessary data
3. **Schema Management**: Use schema registry
4. **Monitoring**: Track event flow and errors
5. **Error Handling**: Dead letter queues
6. **Ordering**: Consider ordering requirements
7. **Partitioning**: Design partitions carefully
8. **Testing**: Test event flows thoroughly

---

## Comparison & Use Cases

### Redis vs Memcached vs Kafka

**Redis**:
- **Best for**: Caching, sessions, real-time data, pub/sub
- **Data structures**: Rich (strings, lists, sets, etc.)
- **Persistence**: Optional
- **Use when**: Need complex data structures or persistence

**Memcached**:
- **Best for**: Simple caching, high throughput
- **Data structures**: Key-value only
- **Persistence**: None
- **Use when**: Simple caching, maximum performance

**Kafka**:
- **Best for**: Event streaming, messaging, log aggregation
- **Data structures**: Messages/events
- **Persistence**: Yes (disk)
- **Use when**: Event streaming, high throughput messaging

### When to Use What

**Caching**:
- **Simple caching**: Memcached
- **Complex caching**: Redis
- **Session storage**: Redis
- **Page caching**: Memcached or Redis

**Messaging**:
- **Simple queues**: Redis
- **Event streaming**: Kafka
- **Pub/Sub**: Redis or Kafka
- **High throughput**: Kafka

**Real-time Data**:
- **Counters**: Redis
- **Leaderboards**: Redis
- **Analytics**: Kafka + Stream processing
- **Time-series**: Redis or specialized DB

---

## Common Interview Questions

### Redis Questions

1. **What is Redis?**
   - In-memory data structure store
   - Used as cache, database, message broker
   - High performance, sub-millisecond latency

2. **Redis data structures?**
   - Strings, Lists, Sets, Sorted Sets, Hashes, Bitmaps, HyperLogLogs

3. **How does Redis persistence work?**
   - RDB: Point-in-time snapshots
   - AOF: Append-only file logging
   - Can use both

4. **Redis replication?**
   - Master-slave replication
   - Asynchronous
   - Read scaling
   - High availability with Sentinel

5. **When to use Redis vs database?**
   - Redis: Fast access, temporary data, caching
   - Database: Persistent, complex queries, transactions

### Kafka Questions

1. **What is Kafka?**
   - Distributed event streaming platform
   - High throughput, fault-tolerant
   - Pub/sub messaging

2. **Kafka topics and partitions?**
   - Topics: Categories for messages
   - Partitions: Topics split for parallelism
   - Ordering within partition

3. **How does Kafka ensure durability?**
   - Messages written to disk
   - Replication across brokers
   - Configurable replication factor

4. **Consumer groups?**
   - Group of consumers working together
   - Load balancing
   - Each partition consumed by one consumer

5. **Kafka use cases?**
   - Event streaming, messaging, log aggregation, stream processing

### Memcached Questions

1. **What is Memcached?**
   - Distributed memory caching system
   - Simple key-value store
   - High performance

2. **Memcached vs Redis?**
   - Memcached: Simpler, multi-threaded, no persistence
   - Redis: More features, persistence, complex structures

3. **How does Memcached distribute data?**
   - Consistent hashing
   - Client determines server
   - Even distribution

4. **Memcached eviction?**
   - LRU (Least Recently Used)
   - Per slab class
   - When memory full

### Event Management Questions

1. **What is event-driven architecture?**
   - Components communicate via events
   - Loose coupling, asynchronous
   - Scalable and resilient

2. **Event delivery guarantees?**
   - At-most-once, at-least-once, exactly-once
   - Trade-offs between guarantees

3. **How to handle event ordering?**
   - Partitioning, sequence numbers
   - Vector clocks, idempotency

4. **Event sourcing?**
   - Store events as source of truth
   - Rebuild state from events
   - Complete audit trail

---

## Key Takeaways

1. **Redis**: Rich data structures, persistence, pub/sub - versatile caching and data store
2. **Kafka**: High-throughput event streaming, durable, scalable - event-driven systems
3. **Memcached**: Simple, fast caching - when you just need caching
4. **Event-Driven**: Loose coupling, scalability, resilience - modern architecture pattern
5. **Choose wisely**: Understand requirements before selecting technology
6. **Combine**: Often use multiple technologies together
7. **Monitor**: Track performance, errors, and usage
8. **Plan for scale**: Design for growth from start

