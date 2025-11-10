# System Design - Comprehensive Guide

## Table of Contents
1. [System Design Fundamentals](#system-design-fundamentals)
2. [Scalability Concepts](#scalability-concepts)
3. [Distributed Systems Principles](#distributed-systems-principles)
4. [Caching Strategies](#caching-strategies)
5. [Load Balancing](#load-balancing)
6. [API Rate Limiting](#api-rate-limiting)
7. [Database Design](#database-design)
8. [E-commerce Application Design](#e-commerce-application-design)
9. [Video Streaming Application Design](#video-streaming-application-design)
10. [URL Shortener (TinyURL) Design](#url-shortener-tinyurl-design)
11. [Social Media Feed (Twitter) Design](#social-media-feed-twitter-design)
12. [Chat/Messaging System Design](#chatmessaging-system-design)
13. [Search Engine Design](#search-engine-design)
14. [Common Interview Questions](#common-interview-questions)

---

## System Design Fundamentals

### What is System Design?

**Definition**: System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements.

### Goals of System Design

1. **Scalability**: Handle growing amounts of work
2. **Reliability**: System works correctly and consistently
3. **Availability**: System is operational when needed
4. **Performance**: Fast response times
5. **Maintainability**: Easy to modify and extend
6. **Security**: Protect data and resources

### System Design Process

1. **Requirements Clarification**
   - Functional requirements
   - Non-functional requirements
   - Scale requirements
   - Constraints

2. **Capacity Estimation**
   - Traffic estimates
   - Storage estimates
   - Bandwidth estimates

3. **System API Design**
   - Define APIs
   - Request/Response formats
   - Error handling

4. **Database Design**
   - Data models
   - Database schema
   - Indexing strategy

5. **High-Level Design**
   - Component architecture
   - Data flow
   - Technology choices

6. **Detailed Design**
   - Component interactions
   - Algorithms
   - Data structures

7. **Identify Bottlenecks**
   - Performance bottlenecks
   - Scalability issues
   - Single points of failure

8. **Scale the System**
   - Horizontal scaling
   - Vertical scaling
   - Caching strategies
   - Load balancing

---

## Scalability Concepts

### Vertical Scaling (Scale Up)

**Definition**: Adding more power (CPU, RAM, storage) to existing machines.

**Pros**:
- Simple to implement
- No code changes needed
- No data distribution issues

**Cons**:
- Hardware limits
- Expensive
- Single point of failure
- Limited scalability

### Horizontal Scaling (Scale Out)

**Definition**: Adding more machines to handle increased load.

**Pros**:
- Virtually unlimited scalability
- Cost-effective
- Better fault tolerance
- No hardware limits

**Cons**:
- Complex to implement
- Data distribution challenges
- Network latency
- Load balancing needed

### Scalability Patterns

**Stateless Services**:
- No session data stored on server
- Any server can handle any request
- Easier horizontal scaling

**Stateful Services**:
- Session data stored on server
- Sticky sessions required
- Harder to scale

**Database Scaling**:
- **Read Replicas**: Scale read operations
- **Sharding**: Partition data across databases
- **Master-Slave**: Write to master, read from slaves

---

## Distributed Systems Principles

### CAP Theorem - Comprehensive Explanation

**Definition**: In a distributed system, you can only guarantee two out of three properties simultaneously.

**The Three Properties**:

**1. Consistency (C)**:
- **Definition**: All nodes in the system see the same data at the same time
- **Strong Consistency**: Every read receives the most recent write
- **Linearizability**: All operations appear to execute atomically
- **Example**: When you write data to Node A, all reads from Node B immediately see that data

**2. Availability (A)**:
- **Definition**: System remains operational and responds to requests
- **Every request gets a response**: No errors, no timeouts
- **No downtime**: System is always accessible
- **Example**: Even if some nodes fail, the system continues to serve requests

**3. Partition Tolerance (P)**:
- **Definition**: System continues to operate despite network partitions
- **Network Partition**: When network splits, nodes can't communicate
- **Must Handle**: System must handle network failures gracefully
- **Example**: If network cable is cut, system continues operating on both sides

**Why Only Two Out of Three?**

**The Fundamental Constraint**:
- In a distributed system, network partitions **will** occur
- When partition happens, you must choose:
  - **Cancel operation** (sacrifice Availability) → CP
  - **Continue with stale data** (sacrifice Consistency) → AP
  - **Can't have both** during partition

**Real-World Scenarios**:

**Scenario 1: Network Partition Occurs**

```
Before Partition:
Node A ──────── Node B
  │                │
  └────────────────┘

After Partition:
Node A          Node B
  │                │
  └                └
(Can't communicate)
```

**CP System (Consistency + Partition Tolerance)**:
- **Behavior**: Blocks writes during partition
- **Reason**: Can't ensure consistency across partition
- **Result**: System unavailable for writes
- **Example**: MongoDB replica set - only primary accepts writes

**AP System (Availability + Partition Tolerance)**:
- **Behavior**: Continues accepting writes on both sides
- **Reason**: Prioritizes availability
- **Result**: Data may be inconsistent temporarily
- **Example**: Cassandra - accepts writes on both sides, resolves later

**CA System (Consistency + Availability)**:
- **Not Possible**: In truly distributed systems
- **Why**: Network partitions are inevitable
- **Only Works**: Single-node systems (not distributed)

**System Classifications**:

**CP Systems** (Consistency + Partition Tolerance):
- **Examples**: MongoDB, HBase, Traditional RDBMS with replication
- **Characteristics**:
  - Strong consistency
  - Blocks writes during partition
  - Data always consistent
  - May be unavailable during partition
- **Use Cases**: Financial systems, critical transactions, banking

**AP Systems** (Availability + Partition Tolerance):
- **Examples**: Cassandra, DynamoDB, CouchDB
- **Characteristics**:
  - High availability
  - Eventual consistency
  - Continues operating during partition
  - May serve stale data temporarily
- **Use Cases**: Social media, content delivery, recommendations

**Visual Representation**:

```
        CAP Triangle
            /\
           /  \
          /    \
    (C)  /      \  (A)
        /        \
       /__________\
          (P)
```

**Practical Implications**:

**Choosing CP**:
- When data consistency is critical
- Can tolerate temporary unavailability
- Financial transactions, inventory systems
- **Trade-off**: May reject requests during partition

**Choosing AP**:
- When availability is critical
- Can tolerate temporary inconsistency
- Social feeds, content systems
- **Trade-off**: May serve stale data

**The Reality**:
- Most systems are **mostly consistent** or **mostly available**
- Perfect CP or AP is rare
- Systems make trade-offs based on use case
- Modern systems often provide tunable consistency

---

### Horizontal vs Vertical Scaling - Deep Dive

**Vertical Scaling (Scale Up)**:

**What It Means**:
- Add more resources to **existing machine**
- Increase CPU, RAM, storage on same server
- Make the server more powerful

**How It Works**:
```
Before:
┌─────────────────┐
│   Server        │
│   CPU: 4 cores  │
│   RAM: 16 GB    │
│   Storage: 500GB│
└─────────────────┘

After:
┌─────────────────┐
│   Server        │
│   CPU: 16 cores │ ← Upgraded
│   RAM: 64 GB    │ ← Upgraded
│   Storage: 2 TB │ ← Upgraded
└─────────────────┘
```

**Implementation**:
1. **Hardware Upgrade**: Replace CPU, add RAM, add storage
2. **Cloud Scaling**: Resize instance (AWS, Azure, GCP)
3. **No Code Changes**: Application doesn't need changes
4. **Single Machine**: All resources on one server

**Pros**:
- ✅ **Simple**: Easy to implement
- ✅ **No Code Changes**: Application works as-is
- ✅ **No Data Distribution**: All data on one machine
- ✅ **No Network Overhead**: No inter-server communication
- ✅ **Consistent Performance**: No network latency

**Cons**:
- ❌ **Hardware Limits**: Can't scale beyond physical limits
- ❌ **Expensive**: High-end hardware is costly
- ❌ **Single Point of Failure**: One machine fails, entire system down
- ❌ **Limited Scalability**: Eventually hit ceiling
- ❌ **Downtime**: May need downtime for upgrades

**When to Use**:
- Small to medium applications
- Single server sufficient
- Simple architecture
- Budget allows for high-end hardware

**Example**:
```
E-commerce site with 10K users:
Start: 4 CPU, 16GB RAM
Scale Up: 16 CPU, 64GB RAM
Still one server, just more powerful
```

---

**Horizontal Scaling (Scale Out)**:

**What It Means**:
- Add **more machines** to system
- Distribute load across multiple servers
- Each server handles portion of requests

**How It Works**:
```
Before:
┌─────────┐
│ Server1 │
│         │
└─────────┘

After:
┌─────────┐  ┌─────────┐  ┌─────────┐
│ Server1 │  │ Server2 │  │ Server3 │
│         │  │         │  │         │
└─────────┘  └─────────┘  └─────────┘
     │            │            │
     └────────────┴────────────┘
            Load Balancer
```

**Implementation**:
1. **Add Servers**: Provision new servers
2. **Load Balancing**: Distribute requests across servers
3. **Data Distribution**: May need to distribute data (sharding)
4. **Code Changes**: May need stateless design

**Pros**:
- ✅ **Unlimited Scalability**: Can add many servers
- ✅ **Cost-Effective**: Commodity hardware
- ✅ **Fault Tolerance**: One server fails, others continue
- ✅ **Better Performance**: Parallel processing
- ✅ **No Hardware Limits**: Scale as needed

**Cons**:
- ❌ **Complex**: More moving parts
- ❌ **Data Distribution**: Need to handle data across servers
- ❌ **Network Latency**: Inter-server communication
- ❌ **Load Balancing**: Need load balancer
- ❌ **Consistency**: Harder to maintain consistency

**When to Use**:
- Large scale applications
- Need high availability
- Cost-effective scaling
- Distributed architecture

**Example**:
```
E-commerce site with 10M users:
Start: 1 server
Scale Out: 10 servers
Each handles 1M users
```

**Comparison Table**:

| Aspect | Vertical Scaling | Horizontal Scaling |
|--------|----------------|-------------------|
| **Method** | Upgrade hardware | Add more servers |
| **Complexity** | Simple | Complex |
| **Cost** | Expensive (high-end) | Cost-effective (commodity) |
| **Scalability** | Limited | Unlimited |
| **Fault Tolerance** | Single point of failure | Distributed |
| **Data Distribution** | Not needed | May be needed |
| **Network** | No overhead | Network latency |
| **Code Changes** | Not needed | May be needed |

**Modern Approach - Hybrid**:
- Start with vertical scaling (simple)
- Move to horizontal scaling (when needed)
- Use both: Powerful servers + many servers

---

### Load Balancing - Complete Guide

**What is Load Balancing?**

**Definition**: Distribute incoming requests across multiple servers to optimize resource utilization, maximize throughput, and minimize response time.

**Why Needed**:
- Single server can't handle all traffic
- Distribute load evenly
- Improve availability
- Scale horizontally

**How It Works**:

```
Clients
   │
   ├─ Request 1 ──┐
   ├─ Request 2 ──┤
   ├─ Request 3 ──┼──> Load Balancer
   └─ Request 4 ──┘
                   │
        ┌──────────┼──────────┐
        │          │          │
     Server 1   Server 2   Server 3
```

**Load Balancing Algorithms**:

**1. Round Robin**:
- **How**: Distribute requests sequentially
- **Example**: Request 1 → Server 1, Request 2 → Server 2, Request 3 → Server 3, repeat
- **Pros**: Simple, fair distribution
- **Cons**: Doesn't consider server load or capacity

**2. Weighted Round Robin**:
- **How**: Round robin with weights
- **Example**: Server 1 (weight 3), Server 2 (weight 1), Server 3 (weight 1)
- **Result**: Server 1 gets 3 requests, then Server 2, then Server 3
- **Pros**: Handles different server capacities
- **Cons**: Still doesn't consider current load

**3. Least Connections**:
- **How**: Route to server with fewest active connections
- **Example**: Server 1 (10 connections), Server 2 (5 connections) → Route to Server 2
- **Pros**: Good for long-lived connections
- **Cons**: May not reflect actual load

**4. Least Response Time**:
- **How**: Route to server with lowest response time
- **Example**: Server 1 (50ms), Server 2 (20ms) → Route to Server 2
- **Pros**: Routes to fastest server
- **Cons**: Requires monitoring response times

**5. IP Hash**:
- **How**: Hash client IP to determine server
- **Example**: Hash(Client IP) % Number of Servers
- **Pros**: Sticky sessions, consistent routing
- **Cons**: Uneven if IPs not distributed

**6. Geographic**:
- **How**: Route based on geographic location
- **Example**: US clients → US servers, EU clients → EU servers
- **Pros**: Reduced latency
- **Cons**: May not balance load evenly

**Load Balancer Types**:

**Layer 4 (Transport Layer - TCP/UDP)**:
- **Works At**: IP and port level
- **Sees**: Source IP, destination IP, ports
- **Doesn't See**: HTTP headers, content
- **Pros**: Fast, simple, low overhead
- **Cons**: Limited intelligence
- **Use Case**: Simple load distribution

**Layer 7 (Application Layer - HTTP)**:
- **Works At**: Application level
- **Sees**: HTTP headers, URLs, content
- **Can Do**: Content-based routing, SSL termination
- **Pros**: Intelligent routing, more features
- **Cons**: More overhead, slower
- **Use Case**: Complex routing needs

**Load Balancer Architecture**:

**1. Single Load Balancer**:
```
Clients → Load Balancer → Servers
```
- **Pros**: Simple
- **Cons**: Single point of failure

**2. Load Balancer with Backup**:
```
Clients → Active LB ──┐
         Backup LB ──┘ → Servers
```
- **Pros**: High availability
- **Cons**: More complex

**3. Multiple Load Balancers (DNS)**:
```
Clients → DNS → LB1, LB2, LB3 → Servers
```
- **Pros**: Distributed, scalable
- **Cons**: Most complex

**Health Checks**:

**Purpose**: Remove unhealthy servers from rotation

**Types**:
- **Active**: LB sends requests to check health
- **Passive**: Monitor responses to actual requests

**Checks**:
- **HTTP**: Check if server responds to HTTP request
- **TCP**: Check if port is open
- **Custom**: Application-specific health checks

**Example**:
```
LB checks Server 1: GET /health → 200 OK ✓
LB checks Server 2: GET /health → Timeout ✗
LB removes Server 2 from rotation
```

**Session Persistence (Sticky Sessions)**:

**Problem**: User session stored on Server 1, but next request goes to Server 2

**Solution**: Route same user to same server

**Methods**:
- **Cookie-based**: Set cookie with server identifier
- **IP Hash**: Hash client IP (same IP → same server)
- **URL Rewriting**: Embed server ID in URL

**Example**:
```
User 1 → LB → Server 1 (session created)
User 1 → LB → Server 1 (same server, session available)
```

---

### Data Synchronization in Distributed Systems

**The Challenge**:

When you have multiple servers or databases, how do you keep data consistent?

**The Problem**:
```
Server 1: User updates profile
Server 2: Still has old profile
Server 3: Still has old profile
```

**Synchronization Methods**:

**1. Master-Slave Replication**:

**How It Works**:
```
Master (Write)
   │
   ├── Replicate ──> Slave 1 (Read)
   ├── Replicate ──> Slave 2 (Read)
   └── Replicate ──> Slave 3 (Read)
```

**Process**:
1. **Write to Master**: All writes go to master
2. **Replication**: Master replicates changes to slaves
3. **Read from Slaves**: Reads can go to any slave
4. **Async or Sync**: Replication can be asynchronous or synchronous

**Asynchronous Replication**:
- Master writes immediately
- Replicates to slaves in background
- **Pros**: Fast writes, master not blocked
- **Cons**: Slaves may have stale data (eventual consistency)

**Synchronous Replication**:
- Master waits for slaves to confirm
- All nodes have same data
- **Pros**: Strong consistency
- **Cons**: Slower writes, master blocked

**Example**:
```
Write: User updates email
1. Write to Master → Success
2. Master replicates to Slaves (async)
3. Slaves update (eventually)
4. Reads from Slaves see new email (after replication)
```

**2. Master-Master Replication**:

**How It Works**:
```
Master 1 ←── Replicate ──→ Master 2
   │                            │
   └── Replicate ───────────────┘
```

**Process**:
1. **Write to Any Master**: Can write to any master
2. **Bidirectional Replication**: Masters replicate to each other
3. **Conflict Resolution**: Handle conflicts when both masters update same data

**Conflict Resolution**:
- **Last Write Wins**: Most recent write wins
- **Vector Clocks**: Track causality
- **Application Logic**: Custom conflict resolution

**Example**:
```
Master 1: User updates name to "John"
Master 2: User updates name to "Jane" (same time)
Conflict! → Resolve (e.g., last write wins → "Jane")
```

**3. Multi-Master Replication**:

**How It Works**:
```
Master 1 ←──┐
Master 2 ←──┼── Replicate ──→ Master 3
Master 4 ←──┘
```

**Characteristics**:
- Multiple masters
- All can accept writes
- Replicate to all others
- Complex conflict resolution

**4. Sharding with Synchronization**:

**How It Works**:
```
Shard 1 (Users 1-1000)    Shard 2 (Users 1001-2000)
      │                            │
      └──────── Replicate ─────────┘
              (for backup)
```

**Process**:
1. **Data Partitioned**: Data split across shards
2. **Each Shard Independent**: Own master and replicas
3. **Synchronization Within Shard**: Master-slave within each shard
4. **No Cross-Shard Sync**: Shards don't sync with each other

**5. Event-Driven Synchronization**:

**How It Works**:
```
Service 1 → Event: UserUpdated → Message Queue
                                      │
Service 2 ←── Consume Event ──────────┘
Service 3 ←── Consume Event ──────────┘
```

**Process**:
1. **Event Published**: Service publishes event on change
2. **Event Queue**: Events stored in message queue (Kafka, RabbitMQ)
3. **Subscribers Consume**: Other services consume events
4. **Update Local Data**: Each service updates its own database

**Example**:
```
Order Service: Order created → Publish OrderCreated event
Payment Service: Consumes event → Update payment status
Inventory Service: Consumes event → Update inventory
```

**Synchronization Patterns**:

**1. Push Model**:
- **How**: Source pushes changes to destinations
- **Example**: Master pushes to slaves
- **Pros**: Real-time, controlled
- **Cons**: Source must know all destinations

**2. Pull Model**:
- **How**: Destinations pull changes from source
- **Example**: Slaves poll master for changes
- **Pros**: Destinations control timing
- **Cons**: May have delay, polling overhead

**3. Hybrid Model**:
- **How**: Combination of push and pull
- **Example**: Master notifies, slaves pull details
- **Pros**: Best of both
- **Cons**: More complex

**Consistency Models**:

**1. Strong Consistency**:
- **Definition**: All nodes see same data immediately
- **How**: Synchronous replication
- **Trade-off**: Slower, may block

**2. Eventual Consistency**:
- **Definition**: System becomes consistent over time
- **How**: Asynchronous replication
- **Trade-off**: Faster, may have temporary inconsistencies

**3. Weak Consistency**:
- **Definition**: No guarantee of consistency
- **How**: No synchronization
- **Trade-off**: Fastest, inconsistent

**How It All Works Together**:

**Complete System Example**:

```
                    Internet
                       │
                  ┌────┴────┐
                  │   DNS  │ (Geographic routing)
                  └────┬────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
   Load Balancer 1  Load Balancer 2  Load Balancer 3
   (US Region)      (EU Region)      (Asia Region)
        │              │              │
   ┌────┴────┐    ┌────┴────┐    ┌────┴────┐
   │ Server1 │    │ Server4 │    │ Server7 │
   │ Server2 │    │ Server5 │    │ Server8 │
   │ Server3 │    │ Server6 │    │ Server9 │
   └────┬────┘    └────┬────┘    └────┬────┘
        │              │              │
   ┌────┴────┐    ┌────┴────┐    ┌────┴────┐
   │ Master  │    │ Master  │    │ Master  │
   │  DB 1   │    │  DB 2   │    │  DB 3   │
   └────┬────┘    └────┬────┘    └────┬────┘
        │              │              │
        └────── Replicate ────────────┘
              (Cross-region sync)
```

**Flow of a Request**:

1. **Client Request**: User in US makes request
2. **DNS Routing**: DNS routes to US Load Balancer (geographic)
3. **Load Balancing**: LB routes to Server 2 (least connections)
4. **Application Processing**: Server 2 processes request
5. **Database Write**: Server 2 writes to Master DB 1
6. **Replication**: Master DB 1 replicates to:
   - Local Slaves (for reads)
   - Other Region Masters (for disaster recovery)
7. **Response**: Server 2 responds to client
8. **Subsequent Reads**: Can read from any slave (eventual consistency)

**Data Synchronization Timeline**:

```
Time 0: Write to Master DB 1 (US)
Time 1: Master DB 1 replicates to Slave DB 1a (US) - 10ms
Time 2: Master DB 1 replicates to Master DB 2 (EU) - 100ms
Time 3: Master DB 2 replicates to Slave DB 2a (EU) - 10ms
Time 4: All nodes consistent
```

**Key Takeaways**:

1. **CAP Theorem**: Choose consistency vs availability based on needs
2. **Scaling**: Start vertical, move to horizontal as needed
3. **Load Balancing**: Distribute load, improve availability
4. **Synchronization**: Keep data consistent across nodes
5. **Trade-offs**: Every choice has trade-offs
6. **Design for Scale**: Plan for growth from start

### ACID Properties

**Atomicity**: All or nothing - transaction completes fully or not at all

**Consistency**: Database remains in valid state after transaction

**Isolation**: Concurrent transactions don't interfere

**Durability**: Committed changes persist even after system failure

### BASE Properties

**Basically Available**: System remains available most of the time

**Soft State**: System state may change over time

**Eventual Consistency**: System will become consistent eventually

---

## Caching Strategies

### Caching Levels

**Application Cache**:
- In-memory cache in application
- Fast access
- Limited size

**Distributed Cache**:
- Shared cache across servers
- Redis, Memcached
- Larger capacity

**CDN (Content Delivery Network)**:
- Geographic distribution
- Static content caching
- Reduced latency

**Database Cache**:
- Query result caching
- Reduced database load

### Cache Patterns

**Cache-Aside (Lazy Loading)**:
- Application checks cache first
- If miss, fetches from database
- Stores in cache for future

**Write-Through**:
- Write to cache and database simultaneously
- Always consistent
- Higher write latency

**Write-Back (Write-Behind)**:
- Write to cache first
- Write to database asynchronously
- Better write performance
- Risk of data loss

**Refresh-Ahead**:
- Proactively refresh cache
- Before expiration
- Reduces cache misses

### Cache Eviction Policies

- **LRU (Least Recently Used)**: Evict least recently used
- **LFU (Least Frequently Used)**: Evict least frequently used
- **FIFO (First In First Out)**: Evict oldest
- **TTL (Time To Live)**: Evict after time expires

---

## Load Balancing

### Load Balancing Algorithms

**Round Robin**:
- Distribute requests sequentially
- Simple and fair
- Doesn't consider server load

**Weighted Round Robin**:
- Round robin with weights
- More powerful servers get more requests

**Least Connections**:
- Route to server with fewest connections
- Good for long-lived connections

**IP Hash**:
- Hash client IP to determine server
- Sticky sessions
- Consistent routing

**Geographic**:
- Route based on geographic location
- Reduced latency

### Load Balancer Types

**Layer 4 (Transport Layer)**:
- Routes based on IP and port
- Fast and simple
- No content inspection

**Layer 7 (Application Layer)**:
- Routes based on content
- More intelligent routing
- SSL termination
- Content-based routing

---

## API Rate Limiting

### Overview

**API Rate Limiting** is a technique to control the number of requests a client can make to an API within a specific time period.

**Purpose**:
- **Prevent abuse**: Stop malicious or excessive usage
- **Fair resource allocation**: Ensure fair usage among users
- **Protect backend**: Prevent overload of servers and databases
- **Cost control**: Manage infrastructure costs
- **Quality of service**: Maintain performance for all users

### Why Rate Limiting is Important

**1. DDoS Protection**:
- Prevent distributed denial-of-service attacks
- Limit impact of malicious traffic
- Protect infrastructure

**2. Resource Management**:
- Prevent single user from consuming all resources
- Ensure fair distribution
- Maintain system stability

**3. Cost Control**:
- Control API usage costs
- Prevent unexpected bills
- Manage infrastructure expenses

**4. Business Logic**:
- Enforce usage tiers (free, premium, enterprise)
- Implement pricing models
- Control feature access

**5. API Monetization**:
- Different limits for different plans
- Usage-based billing
- Tiered access levels

### Rate Limiting Strategies

**1. User-Based Rate Limiting**:
- Limit per user/API key
- Different limits for different user types
- Tracks usage per user

**2. IP-Based Rate Limiting**:
- Limit per IP address
- Simple to implement
- Can be bypassed with proxies/VPNs

**3. Endpoint-Based Rate Limiting**:
- Different limits for different endpoints
- Critical endpoints more restricted
- Flexible per-route limits

**4. Geographic Rate Limiting**:
- Different limits by region
- Compliance with local regulations
- Traffic management

**5. Time-Based Rate Limiting**:
- Per second, minute, hour, day
- Sliding window or fixed window
- Burst vs sustained limits

### Rate Limiting Algorithms

**1. Fixed Window (Counter)**:

**How it works**:
- Divide time into fixed windows (e.g., 1 minute)
- Count requests in current window
- Reset counter at window boundary
- Allow request if count < limit

**Example**:
- Limit: 100 requests per minute
- Window: 0:00-0:59, 1:00-1:59, etc.
- Count requests in current window

**Pros**:
- Simple to implement
- Low memory usage
- Easy to understand

**Cons**:
- Burst traffic at window boundary
- Uneven distribution
- Can allow 2x limit at boundary

**Implementation**:
```
Key: user_id:window_timestamp
Value: request_count
Increment on each request
Check if count < limit
```

**2. Sliding Window Log**:

**How it works**:
- Maintain log of request timestamps
- Remove timestamps outside window
- Count requests in sliding window
- Allow if count < limit

**Example**:
- Limit: 100 requests per minute
- Window: Last 60 seconds from current time
- Track all request timestamps

**Pros**:
- Smooth rate limiting
- No burst at boundaries
- Accurate

**Cons**:
- High memory usage
- Expensive to maintain
- Not scalable

**Implementation**:
```
Key: user_id
Value: Sorted set of timestamps
Add current timestamp
Remove timestamps outside window
Count remaining timestamps
```

**3. Sliding Window Counter**:

**How it works**:
- Combine fixed windows
- Weighted average of current and previous window
- Estimate requests in sliding window
- More accurate than fixed window

**Example**:
- Limit: 100 requests per minute
- Current window: 0:00-0:59
- Previous window: 23:00-23:59
- Estimate = current_count + (previous_count * overlap_ratio)

**Pros**:
- Better accuracy than fixed window
- Lower memory than sliding log
- Smooth distribution

**Cons**:
- More complex
- Still approximate
- Requires two windows

**4. Token Bucket**:

**How it works**:
- Bucket with tokens (capacity = limit)
- Tokens added at constant rate
- Request consumes token
- Allow if tokens available

**Example**:
- Capacity: 100 tokens
- Refill rate: 10 tokens/second
- Request consumes 1 token
- Block if bucket empty

**Pros**:
- Allows bursts
- Smooth average rate
- Flexible

**Cons**:
- More complex
- Requires token management
- Memory for bucket state

**Implementation**:
```
Key: user_id
Value: {tokens: count, last_refill: timestamp}
Refill tokens based on time elapsed
Consume token on request
Check if tokens > 0
```

**5. Leaky Bucket**:

**How it works**:
- Bucket with fixed capacity
- Requests added to bucket
- Requests processed at constant rate
- Reject if bucket full

**Example**:
- Capacity: 100 requests
- Processing rate: 10 requests/second
- Add request to bucket
- Process at constant rate

**Pros**:
- Smooth output rate
- Prevents bursts
- Predictable

**Cons**:
- Can reject valid requests
- Less flexible
- Requires queue management

### Rate Limiting Implementation

**1. In-Memory Rate Limiting**:

**Pros**:
- Fast (no network calls)
- Simple implementation
- Low latency

**Cons**:
- Not distributed (per server)
- Lost on server restart
- Inconsistent across servers

**Use When**:
- Single server
- Simple use case
- Low traffic

**2. Distributed Rate Limiting (Redis)**:

**Pros**:
- Consistent across servers
- Persistent (if Redis configured)
- Scalable
- High performance

**Cons**:
- Network latency
- Redis dependency
- More complex

**Use When**:
- Multiple servers
- Need consistency
- High traffic

**Redis Implementation Example**:

**Fixed Window with Redis**:
```
Key: rate_limit:user_id:window
INCR key
EXPIRE key window_duration
GET key (check if < limit)
```

**Sliding Window with Redis Sorted Set**:
```
Key: rate_limit:user_id
ZADD key timestamp current_timestamp
ZREMRANGEBYSCORE key 0 (current_timestamp - window)
ZCARD key (count remaining)
EXPIRE key window_duration
```

**Token Bucket with Redis**:
```
Key: rate_limit:user_id
GET key (get current tokens and last_refill)
Calculate tokens to add
SET key {tokens, last_refill}
DECR tokens
Check if tokens > 0
```

**3. API Gateway Rate Limiting**:

**Location**: At API Gateway layer

**Benefits**:
- Centralized enforcement
- Before reaching backend
- Consistent across services
- Easy to configure

**Examples**:
- AWS API Gateway
- Kong
- Nginx
- Envoy

**Configuration**:
- Per API key
- Per IP
- Per endpoint
- Global limits

### Rate Limiting Headers

**Standard HTTP Headers**:

**X-RateLimit-Limit**:
- Maximum requests allowed
- Example: `X-RateLimit-Limit: 100`

**X-RateLimit-Remaining**:
- Remaining requests in window
- Example: `X-RateLimit-Remaining: 45`

**X-RateLimit-Reset**:
- Time when limit resets (Unix timestamp)
- Example: `X-RateLimit-Reset: 1640995200`

**Retry-After**:
- Seconds to wait before retrying (when rate limited)
- Example: `Retry-After: 60`

**Response Codes**:
- **200 OK**: Request allowed
- **429 Too Many Requests**: Rate limit exceeded
- **503 Service Unavailable**: Temporary rate limiting

### Rate Limiting Best Practices

**1. Choose Appropriate Limits**:
- Based on use case
- Consider normal usage patterns
- Allow reasonable bursts
- Review and adjust

**2. Provide Clear Feedback**:
- Return rate limit headers
- Clear error messages
- Document limits in API docs
- Provide retry guidance

**3. Implement Graceful Degradation**:
- Soft limits with warnings
- Hard limits with blocking
- Tiered limits (warning → throttling → blocking)
- Allow burst within reason

**4. Handle Edge Cases**:
- Window boundary bursts
- Clock skew in distributed systems
- Race conditions
- Concurrent requests

**5. Monitor and Alert**:
- Track rate limit hits
- Monitor abuse patterns
- Alert on anomalies
- Adjust limits based on data

**6. Consider User Experience**:
- Don't be too restrictive
- Allow reasonable usage
- Provide upgrade paths
- Clear communication

**7. Security Considerations**:
- Don't rely solely on IP-based limiting
- Use API keys for authentication
- Implement CAPTCHA for suspicious activity
- Log and monitor abuse

### Distributed Rate Limiting Challenges

**1. Consistency**:
- Multiple servers need consistent view
- Use distributed cache (Redis)
- Synchronize state
- Handle network partitions

**2. Race Conditions**:
- Concurrent requests from same user
- Atomic operations (Redis INCR)
- Distributed locks if needed
- Idempotent operations

**3. Clock Skew**:
- Servers may have different times
- Use server time, not client time
- Synchronize clocks (NTP)
- Consider time drift

**4. Scalability**:
- High request volume
- Efficient data structures
- Shard by user if needed
- Cache frequently accessed data

### Rate Limiting Patterns

**1. Hard Rate Limiting**:
- Strict enforcement
- Reject requests over limit
- Simple to implement
- Can frustrate legitimate users

**2. Soft Rate Limiting**:
- Allow bursts
- Throttle instead of reject
- Queue requests
- Better user experience

**3. Adaptive Rate Limiting**:
- Adjust limits based on system load
- Increase limits when system healthy
- Decrease when under stress
- Dynamic thresholds

**4. Tiered Rate Limiting**:
- Different limits per tier
- Free: 100 requests/hour
- Premium: 10,000 requests/hour
- Enterprise: Unlimited
- Based on subscription

**5. Per-Endpoint Rate Limiting**:
- Different limits per endpoint
- Critical endpoints: stricter
- Public endpoints: more lenient
- Flexible configuration

### Rate Limiting in Microservices

**1. API Gateway Level**:
- Centralized rate limiting
- Before requests reach services
- Consistent enforcement
- Easy to manage

**2. Service Level**:
- Each service enforces limits
- Service-specific rules
- More granular control
- Distributed enforcement

**3. Hybrid Approach**:
- Gateway for global limits
- Services for specific limits
- Defense in depth
- Flexible

### Common Interview Questions

**1. How would you implement rate limiting?**
- Choose algorithm (fixed window, sliding window, token bucket)
- Choose storage (in-memory, Redis)
- Implement at appropriate layer (gateway, service)
- Handle edge cases

**2. Fixed Window vs Sliding Window?**
- Fixed: Simple, but allows bursts at boundary
- Sliding: More accurate, but more complex
- Choose based on requirements

**3. How to handle rate limiting in distributed systems?**
- Use distributed cache (Redis)
- Atomic operations
- Handle consistency
- Consider clock skew

**4. What happens when rate limit is exceeded?**
- Return 429 status code
- Include Retry-After header
- Log the event
- Optionally queue request

**5. How to implement different limits for different users?**
- Store limits per user/tier
- Lookup limit before checking
- Dynamic configuration
- Database or configuration store

---

## Database Design

### Database Types

**Relational Database (SQL)**:
- ACID properties
- Structured data
- Examples: MySQL, PostgreSQL, Oracle

**NoSQL Databases**:
- **Document**: MongoDB, CouchDB
- **Key-Value**: Redis, DynamoDB
- **Column**: Cassandra, HBase
- **Graph**: Neo4j

### Database Scaling

**Read Replicas**:
- Master for writes
- Replicas for reads
- Improved read performance
- Eventual consistency

**Sharding**:
- Partition data across databases
- Horizontal scaling
- Shard key selection critical

**Partitioning**:
- **Horizontal**: Split rows across tables
- **Vertical**: Split columns across tables

### Indexing

**Purpose**: Speed up queries

**Types**:
- **Primary Index**: Unique, clustered
- **Secondary Index**: Non-unique
- **Composite Index**: Multiple columns

**Trade-offs**:
- Faster reads
- Slower writes
- Additional storage

---

## E-commerce Application Design

### Requirements

**Functional Requirements**:
- User registration and authentication
- Product catalog browsing
- Search products
- Shopping cart
- Checkout and payment
- Order management
- Product reviews and ratings
- Recommendations

**Non-Functional Requirements**:
- High availability (99.9%)
- Low latency (<200ms)
- Handle 10M users
- Support 100K requests/second
- Secure payment processing

### High-Level Architecture

```
┌─────────────┐
│   Clients   │ (Web, Mobile)
└──────┬──────┘
       │
┌──────▼─────────────────────────────────────┐
│         Load Balancer / API Gateway         │
└──────┬──────────────────────────────────────┘
       │
       ├──────────────┬──────────────┐
       │              │              │
┌──────▼──────┐  ┌────▼──────┐  ┌───▼──────┐
│   Web       │  │  Mobile   │  │  Admin   │
│  Service    │  │  Service  │  │ Service  │
└──────┬──────┘  └────┬──────┘  └───┬──────┘
       │              │              │
       └──────────────┼──────────────┘
                      │
       ┌──────────────┼──────────────┐
       │              │              │
┌──────▼──────┐  ┌────▼──────┐  ┌───▼──────┐
│  Product    │  │   Order   │  │ Payment  │
│  Service    │  │  Service  │  │ Service  │
└──────┬──────┘  └────┬──────┘  └───┬──────┘
       │              │              │
       └──────────────┼──────────────┘
                      │
       ┌──────────────┼──────────────┐
       │              │              │
┌──────▼──────┐  ┌────▼──────┐  ┌───▼──────┐
│  Search     │  │  Cart     │  │  User    │
│  Service    │  │  Service  │  │ Service  │
└──────┬──────┘  └────┬──────┘  └───┬──────┘
       │              │              │
       └──────────────┼──────────────┘
                      │
       ┌──────────────┼──────────────┐
       │              │              │
┌──────▼──────┐  ┌────▼──────┐  ┌───▼──────┐
│  Database   │  │   Cache   │  │ Message  │
│  (Sharded)  │  │  (Redis)  │  │  Queue   │
└─────────────┘  └───────────┘  └──────────┘
```

### Component Details

**1. API Gateway**
- Single entry point
- Authentication/Authorization
- Rate limiting
- Request routing
- SSL termination

**2. Microservices**

**Product Service**:
- Product catalog management
- Product details
- Inventory management
- Product categories

**User Service**:
- User registration
- Authentication (JWT tokens)
- User profiles
- Session management

**Cart Service**:
- Shopping cart operations
- Add/remove items
- Cart persistence (Redis)
- Cart expiration

**Order Service**:
- Order creation
- Order status tracking
- Order history
- Order processing workflow

**Payment Service**:
- Payment processing
- Payment gateway integration
- Transaction management
- Refund processing

**Search Service**:
- Product search
- Full-text search (Elasticsearch)
- Filters and sorting
- Search suggestions

**Recommendation Service**:
- Product recommendations
- Collaborative filtering
- Machine learning models
- Personalized suggestions

**3. Database Design**

**User Database**:
```
Users Table:
- user_id (PK)
- email
- password_hash
- name
- created_at

Sessions Table:
- session_id (PK)
- user_id (FK)
- token
- expires_at
```

**Product Database**:
```
Products Table:
- product_id (PK)
- name
- description
- price
- category_id
- inventory_count
- created_at

Categories Table:
- category_id (PK)
- name
- parent_category_id

Inventory Table:
- inventory_id (PK)
- product_id (FK)
- quantity
- warehouse_id
```

**Order Database**:
```
Orders Table:
- order_id (PK)
- user_id (FK)
- total_amount
- status
- shipping_address
- created_at

OrderItems Table:
- order_item_id (PK)
- order_id (FK)
- product_id (FK)
- quantity
- price
```

**4. Caching Strategy**

**Product Catalog Cache**:
- Cache popular products (Redis)
- Cache product details
- TTL: 1 hour
- Cache-aside pattern

**Shopping Cart Cache**:
- Store cart in Redis
- Key: user_id
- TTL: 7 days
- Write-through pattern

**Session Cache**:
- JWT tokens in Redis
- User sessions
- TTL: 24 hours

**5. Search Implementation**

**Elasticsearch**:
- Full-text search
- Faceted search (filters)
- Autocomplete
- Search ranking

**Search Index**:
- Product name, description
- Categories, tags
- Price range
- Ratings

**6. Payment Processing**

**Payment Flow**:
1. User initiates checkout
2. Order Service creates order (pending)
3. Payment Service processes payment
4. Payment gateway integration
5. On success: Order status → confirmed
6. On failure: Order status → failed

**Idempotency**:
- Payment requests must be idempotent
- Prevent duplicate charges
- Use unique transaction IDs

**7. Scalability Considerations**

**Horizontal Scaling**:
- Stateless services
- Load balancers
- Auto-scaling groups

**Database Scaling**:
- Read replicas for reads
- Sharding by user_id or product_id
- Separate databases per service

**Caching**:
- CDN for static content (images)
- Redis for hot data
- Application-level caching

**8. Reliability & Availability**

**Circuit Breaker**:
- Prevent cascading failures
- Payment service failures
- External API failures

**Retry Logic**:
- Exponential backoff
- Payment retries
- Idempotent operations

**Monitoring**:
- Application metrics
- Error tracking
- Performance monitoring
- Alerting

**9. Security**

**Authentication**:
- JWT tokens
- OAuth 2.0
- Session management

**Payment Security**:
- PCI DSS compliance
- Tokenization
- Encryption
- Secure communication (HTTPS)

**Data Protection**:
- Encrypt sensitive data
- PII protection
- GDPR compliance

---

## Video Streaming Application Design

### Requirements

**Functional Requirements**:
- User registration and profiles
- Video upload
- Video encoding/transcoding
- Video streaming (adaptive bitrate)
- Video search and discovery
- Comments and likes
- Playlists
- Recommendations

**Non-Functional Requirements**:
- Support 1B users
- 500M hours of video watched daily
- Low latency streaming (<2s)
- High availability (99.9%)
- Handle 1M concurrent viewers

### High-Level Architecture

```
┌─────────────┐
│   Clients   │ (Web, Mobile, Smart TV)
└──────┬──────┘
       │
┌──────▼─────────────────────────────────────┐
│         CDN / Load Balancer                │
└──────┬──────────────────────────────────────┘
       │
       ├──────────────┬──────────────┐
       │              │              │
┌──────▼──────┐  ┌────▼──────┐  ┌───▼──────┐
│   Video     │  │   User    │  │  Search  │
│  Streaming  │  │  Service  │  │ Service  │
│  Service    │  │           │  │          │
└──────┬──────┘  └────┬──────┘  └───┬──────┘
       │              │              │
       │      ┌───────▼───────┐     │
       │      │  Upload       │     │
       │      │  Service      │     │
       │      └───────┬───────┘     │
       │              │              │
       │      ┌───────▼───────┐     │
       │      │  Encoding     │     │
       │      │  Service      │     │
       │      └───────┬───────┘     │
       │              │              │
       └──────────────┼──────────────┘
                      │
       ┌──────────────┼──────────────┐
       │              │              │
┌──────▼──────┐  ┌────▼──────┐  ┌───▼──────┐
│  Metadata   │  │  Video    │  │  Cache   │
│  Database   │  │  Storage  │  │ (Redis)  │
│             │  │  (S3)     │  │          │
└─────────────┘  └───────────┘  └──────────┘
```

### Component Details

**1. Video Upload Service**

**Upload Flow**:
1. User selects video file
2. Upload to temporary storage
3. Validate file (size, format)
4. Generate unique video ID
5. Queue for encoding
6. Return upload confirmation

**Chunked Upload**:
- Large files uploaded in chunks
- Resume capability
- Better reliability
- Parallel uploads

**2. Video Encoding Service**

**Transcoding**:
- Convert to multiple formats
- Different resolutions (480p, 720p, 1080p, 4K)
- Different bitrates
- Adaptive bitrate streaming (HLS, DASH)

**Encoding Pipeline**:
- Queue-based processing
- Multiple workers
- Priority queues (premium users)
- Progress tracking

**Formats**:
- **HLS (HTTP Live Streaming)**: Apple standard
- **DASH (Dynamic Adaptive Streaming)**: Open standard
- **MP4**: Progressive download

**3. Video Storage**

**Object Storage**:
- Amazon S3, Google Cloud Storage
- Highly durable
- Scalable
- Cost-effective

**Storage Strategy**:
- Original video: Long-term storage
- Encoded videos: Multiple copies (different qualities)
- Thumbnails: Separate storage
- CDN integration

**4. Video Streaming Service**

**Streaming Flow**:
1. Client requests video
2. Service checks user permissions
3. Returns manifest file (HLS/DASH)
4. Client requests video segments
5. CDN serves segments
6. Adaptive bitrate selection

**Adaptive Bitrate Streaming**:
- Client monitors network conditions
- Automatically switches quality
- Smooth playback experience
- Reduced buffering

**5. CDN (Content Delivery Network)**

**Purpose**:
- Geographic distribution
- Reduced latency
- Reduced origin server load
- Better user experience

**CDN Strategy**:
- Cache popular videos
- Edge locations worldwide
- Cache invalidation
- Origin pull vs. push

**6. Database Design**

**Videos Table**:
```
videos:
- video_id (PK)
- user_id (FK)
- title
- description
- duration
- status (uploading, processing, ready, failed)
- created_at
- views_count
- likes_count
```

**Video Metadata**:
```
video_metadata:
- video_id (PK)
- resolution
- bitrate
- format
- file_size
- storage_url
- cdn_url
```

**User Interactions**:
```
likes:
- like_id (PK)
- video_id (FK)
- user_id (FK)
- created_at

comments:
- comment_id (PK)
- video_id (FK)
- user_id (FK)
- text
- created_at
```

**7. Search & Discovery**

**Search Service**:
- Full-text search (Elasticsearch)
- Video metadata indexing
- Tags and categories
- Transcript search (if available)

**Recommendation Engine**:
- Collaborative filtering
- Content-based filtering
- Trending videos
- Personalized recommendations

**8. Caching Strategy**

**Hot Videos Cache**:
- Popular videos in Redis
- Video metadata
- TTL: 1 hour

**User Recommendations Cache**:
- Personalized recommendations
- User-specific cache
- TTL: 6 hours

**Thumbnails Cache**:
- CDN caching
- Static content
- Long TTL

**9. Scalability Considerations**

**Horizontal Scaling**:
- Stateless streaming service
- Multiple encoding workers
- Load balancers

**Storage Scaling**:
- Object storage (unlimited)
- CDN scaling (automatic)
- Database sharding

**Bandwidth**:
- CDN reduces origin bandwidth
- Adaptive bitrate reduces waste
- Compression

**10. Performance Optimizations**

**Pre-loading**:
- Pre-fetch next segments
- Predictive loading
- Reduced buffering

**Compression**:
- Video compression (H.264, H.265)
- Thumbnail optimization
- Metadata compression

**Caching**:
- Aggressive CDN caching
- Application-level caching
- Database query caching

---

## URL Shortener (TinyURL) Design

### Requirements

**Functional Requirements**:
- Shorten long URLs
- Redirect to original URL
- Custom short URLs (optional)
- Analytics (click tracking)
- URL expiration (optional)

**Non-Functional Requirements**:
- 100M URLs shortened per day
- 100:1 read/write ratio
- Redirect latency <100ms
- Short URL length: 7 characters
- 5 years data retention

### Capacity Estimation

**Traffic**:
- 100M URLs/day = ~1,160 URLs/second
- Read/write ratio: 100:1
- Reads: 116,000/second
- Writes: 1,160/second

**Storage**:
- 100M URLs/day × 365 days × 5 years = 182.5B URLs
- Average URL length: 100 bytes
- Total storage: ~18.25 TB

### System Design

**Components**:
1. **URL Shortening Service**: Generate short URLs
2. **Redirect Service**: Handle redirects
3. **Database**: Store URL mappings
4. **Cache**: Hot URLs caching
5. **Analytics Service**: Track clicks

**URL Generation**:
- **Base62 encoding**: 0-9, a-z, A-Z (62 characters)
- 7 characters = 62^7 = 3.5 trillion possibilities
- **Hash-based**: MD5/SHA256 hash, take first 7 chars
- **Counter-based**: Auto-increment counter, encode

**Database Schema**:
```
url_mappings:
- short_url (PK)
- long_url
- created_at
- expires_at
- user_id
- click_count
```

**Caching**:
- Cache popular URLs (Redis)
- 80-20 rule: 20% URLs get 80% traffic
- Cache-aside pattern

**Scalability**:
- Database sharding by short_url hash
- Read replicas for redirects
- CDN for static content
- Horizontal scaling

---

## Social Media Feed (Twitter) Design

### Requirements

**Functional Requirements**:
- User registration
- Post tweets (280 characters)
- Follow/unfollow users
- Timeline feed (home, user)
- Like, retweet, reply
- Search tweets
- Trending topics

**Non-Functional Requirements**:
- 500M users
- 200M daily active users
- 100M tweets per day
- Timeline generation <200ms
- Support 600K reads/second

### System Design

**Components**:
1. **User Service**: User management
2. **Tweet Service**: Create, read tweets
3. **Timeline Service**: Generate feeds
4. **Social Graph Service**: Follow relationships
5. **Search Service**: Full-text search

**Timeline Generation**:

**Fan-out Approaches**:

**Pull Model (On-demand)**:
- Fetch tweets from followed users on request
- Simple to implement
- High read load
- Slower for users with many followers

**Push Model (Fan-out on Write)**:
- Pre-compute timelines on tweet creation
- Write to all followers' timelines
- Fast reads
- High write load for celebrities

**Hybrid Model**:
- Push for regular users (<1000 followers)
- Pull for celebrities (>1000 followers)
- Best of both worlds

**Database Design**:
```
users:
- user_id (PK)
- username
- email
- created_at

tweets:
- tweet_id (PK)
- user_id (FK)
- content
- created_at
- likes_count
- retweets_count

follows:
- follower_id (FK)
- followee_id (FK)
- created_at

timelines:
- user_id (PK)
- tweet_id (FK)
- created_at
```

**Caching**:
- Cache user timelines (Redis)
- Cache social graph
- Cache trending topics
- TTL: 5 minutes

**Scalability**:
- Database sharding by user_id
- Read replicas
- Cache layer
- Message queue for fan-out

---

## Chat/Messaging System Design

### Requirements

**Functional Requirements**:
- One-on-one messaging
- Group messaging
- Online/offline status
- Message delivery status
- Push notifications
- Media sharing

**Non-Functional Requirements**:
- 1B users
- 50M concurrent users
- 100M messages per day
- Message delivery <100ms
- 99.9% availability

### System Design

**Components**:
1. **Message Service**: Send/receive messages
2. **Presence Service**: Online/offline status
3. **Notification Service**: Push notifications
4. **Media Service**: Handle media files

**Architecture**:

**Real-time Communication**:
- **WebSockets**: Persistent connections
- **Long Polling**: HTTP-based fallback
- **Server-Sent Events**: One-way communication

**Message Flow**:
1. User A sends message
2. Message Service stores message
3. Check User B online status
4. If online: Push via WebSocket
5. If offline: Store for later delivery
6. Send push notification

**Database Design**:
```
messages:
- message_id (PK)
- sender_id (FK)
- receiver_id (FK)
- content
- message_type (text, image, video)
- created_at
- delivered_at
- read_at

conversations:
- conversation_id (PK)
- participant_1 (FK)
- participant_2 (FK)
- last_message_id
- updated_at
```

**Message Queue**:
- Store messages in queue
- Asynchronous processing
- Retry failed deliveries
- Kafka or RabbitMQ

**Scalability**:
- WebSocket servers (horizontal scaling)
- Message queue for async processing
- Database sharding by user_id
- Caching for presence status

---

## Search Engine Design

### Requirements

**Functional Requirements**:
- Web crawling
- Indexing web pages
- Search queries
- Ranking results
- Autocomplete
- Spell correction

**Non-Functional Requirements**:
- 1B web pages indexed
- 100K queries per second
- Search results <100ms
- High availability

### System Design

**Components**:
1. **Crawler Service**: Fetch web pages
2. **Indexer Service**: Build search index
3. **Search Service**: Handle queries
4. **Ranking Service**: Rank results

**Crawling**:
- **BFS/DFS**: Traverse web
- **Robots.txt**: Respect crawling rules
- **Rate limiting**: Don't overwhelm servers
- **Distributed crawling**: Multiple crawlers

**Indexing**:
- **Inverted Index**: Word → List of documents
- **Forward Index**: Document → List of words
- **Distributed storage**: Shard by word hash

**Search Flow**:
1. Parse query
2. Lookup in inverted index
3. Find matching documents
4. Rank results
5. Return top N results

**Ranking**:
- **PageRank**: Link-based ranking
- **TF-IDF**: Term frequency-inverse document frequency
- **Machine Learning**: Learning to rank
- **Relevance**: Content matching

**Caching**:
- Cache popular queries
- Cache search results
- Autocomplete cache

---

## Common Interview Questions

### Fundamental Questions

1. **How do you approach system design?**
   - Clarify requirements
   - Estimate capacity
   - Design APIs
   - Database design
   - High-level architecture
   - Identify bottlenecks
   - Scale the system

2. **What is scalability?**
   - System's ability to handle growth
   - Vertical vs. horizontal
   - Stateless vs. stateful

3. **Explain CAP theorem?**
   - Consistency, Availability, Partition Tolerance
   - Can only guarantee two
   - Trade-offs in distributed systems

4. **What is load balancing?**
   - Distribute traffic across servers
   - Algorithms: Round robin, least connections
   - Layer 4 vs. Layer 7

5. **How does caching work?**
   - Store frequently accessed data
   - Reduce database load
   - Patterns: Cache-aside, write-through

6. **What is database sharding?**
   - Partition data across databases
   - Horizontal scaling
   - Shard key selection critical

7. **How to handle high traffic?**
   - Horizontal scaling
   - Caching
   - CDN
   - Database optimization
   - Load balancing

8. **What is microservices?**
   - Small, independent services
   - Own database
   - API communication

9. **How to ensure data consistency?**
   - ACID for transactions
   - Eventual consistency for distributed
   - Saga pattern

10. **What is API Gateway?**
    - Single entry point
    - Routing, security, rate limiting
    - Service abstraction

### Design-Specific Questions

1. **How would you design a URL shortener?**
   - URL generation algorithm
   - Database design
   - Caching strategy
   - Scalability considerations

2. **Design a social media feed?**
   - Timeline generation
   - Fan-out strategies
   - Database design
   - Caching

3. **How to design a chat system?**
   - Real-time communication
   - Message storage
   - Presence management
   - Scalability

4. **Design a video streaming service?**
   - Video storage
   - Encoding pipeline
   - CDN strategy
   - Adaptive bitrate

5. **How to design a search engine?**
   - Web crawling
   - Indexing
   - Ranking
   - Scalability

---

## Key Takeaways

1. **Start with Requirements**: Clarify functional and non-functional
2. **Estimate Capacity**: Traffic, storage, bandwidth
3. **Design APIs**: Define interfaces early
4. **Database Design**: Schema, indexing, sharding
5. **High-Level Architecture**: Component design
6. **Identify Bottlenecks**: Performance and scalability
7. **Scale the System**: Caching, load balancing, replication
8. **Consider Trade-offs**: CAP theorem, consistency vs. availability
9. **Think About Failure**: Fault tolerance, redundancy
10. **Monitor Everything**: Metrics, logging, alerting

