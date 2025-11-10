# Microservices Architecture - Comprehensive Guide

## Table of Contents
1. [Microservices Fundamentals](#microservices-fundamentals)
2. [Architecture Patterns](#architecture-patterns)
3. [Service Communication](#service-communication)
4. [Data Management](#data-management)
5. [Service Discovery & Configuration](#service-discovery--configuration)
6. [API Gateway Pattern](#api-gateway-pattern)
7. [Circuit Breaker Pattern](#circuit-breaker-pattern)
8. [Authentication & Security in Microservices](#authentication--security-in-microservices)
9. [Fault Isolation & Resilience Patterns](#fault-isolation--resilience-patterns)
10. [Microservices Design Patterns](#microservices-design-patterns)
11. [API Design Patterns & Best Practices](#api-design-patterns--best-practices)
12. [General Design Patterns for Microservices](#general-design-patterns-for-microservices)
13. [Deployment & DevOps](#deployment--devops)
14. [Monitoring & Observability](#monitoring--observability)
15. [Best Practices & Anti-patterns](#best-practices--anti-patterns)
16. [Common Interview Questions](#common-interview-questions)

---

## Microservices Fundamentals

### What are Microservices?

**Definition**: Microservices is an architectural approach where a single application is built as a suite of small services, each running in its own process and communicating with lightweight mechanisms, often HTTP/REST APIs.

### Key Characteristics

1. **Independently Deployable**: Each service can be deployed independently
2. **Loosely Coupled**: Services communicate through well-defined APIs
3. **Technology Diversity**: Each service can use different technology stack
4. **Organized Around Business Capabilities**: Services align with business domains
5. **Decentralized Governance**: Teams own their services end-to-end
6. **Fault Isolation**: Failure in one service doesn't bring down entire system
7. **Scalability**: Scale individual services based on demand

### Microservices vs Monolith

**Monolithic Architecture**:
- Single deployable unit
- Easier to develop initially
- Tightly coupled components
- Single technology stack
- Difficult to scale specific parts
- Deployment affects entire system

**Microservices Architecture**:
- Multiple deployable units
- Complex to develop initially
- Loosely coupled services
- Technology diversity allowed
- Independent scaling
- Isolated deployments

### When to Use Microservices

**Good Fit**:
- Large, complex applications
- Multiple teams working on different features
- Need for independent scaling
- Different performance requirements per component
- Need for technology diversity

**Not Good Fit**:
- Small applications
- Simple domain
- Small team
- Tight coupling requirements
- High transaction volume requiring ACID transactions

---

## Architecture Patterns

### Service Decomposition Patterns

**Decompose by Business Capability**:
- Organize services around business functions
- Example: Order Service, Payment Service, Inventory Service
- Each service owns its business logic

**Decompose by Subdomain (DDD)**:
- Based on Domain-Driven Design
- Bounded contexts become services
- Clear domain boundaries

**Decompose by Transaction**:
- Services handle specific transaction types
- Useful for complex transaction flows

### Database per Service Pattern

**Principle**: Each microservice has its own database.

**Benefits**:
- Loose coupling between services
- Independent scaling
- Technology diversity
- Data ownership

**Challenges**:
- Distributed transactions
- Data consistency
- Cross-service queries

**Solutions**:
- Saga pattern for distributed transactions
- Event-driven architecture
- API composition

### API Gateway Pattern

**Purpose**: Single entry point for all client requests.

**Responsibilities**:
- Request routing
- Authentication/Authorization
- Rate limiting
- Load balancing
- Request/Response transformation
- Protocol translation

**Benefits**:
- Centralized cross-cutting concerns
- Simplified client interaction
- Service abstraction

**Examples**: Kong, AWS API Gateway, Zuul, Spring Cloud Gateway

---

## Service Communication

### Synchronous Communication

**REST (Representational State Transfer)**:
- HTTP-based
- Stateless
- Resource-oriented
- Widely adopted
- Simple to implement

**gRPC**:
- High performance
- Protocol Buffers
- HTTP/2 based
- Type-safe
- Better for internal services

**GraphQL**:
- Flexible querying
- Single endpoint
- Client specifies data needs
- Reduces over-fetching

**Challenges with Synchronous**:
- Tight coupling
- Cascading failures
- Latency accumulation
- Availability dependency

### Asynchronous Communication

**Message Queues**:
- Decouples services
- Better fault tolerance
- Handles traffic spikes
- Examples: RabbitMQ, ActiveMQ

**Message Brokers**:
- Pub/Sub pattern
- Event streaming
- Examples: Kafka, Redis Pub/Sub

**Event-Driven Architecture**:
- Services communicate via events
- Loose coupling
- Eventual consistency
- Scalable

### Communication Patterns

**Request-Response**:
- Synchronous communication
- Direct service calls
- Immediate response needed

**Fire and Forget**:
- Asynchronous, no response expected
- Event publishing
- Notification sending

**Publish-Subscribe**:
- Event broadcasting
- Multiple subscribers
- Decoupled communication

**Request-Reply**:
- Asynchronous request with callback
- Non-blocking
- Better scalability

---

## Data Management

### Database Patterns

**Database per Service**:
- Each service owns its database
- No shared database
- Loose coupling

**Shared Database Anti-pattern**:
- Multiple services share database
- Creates tight coupling
- Should be avoided

**CQRS (Command Query Responsibility Segregation)**:
- Separate read and write models
- Optimized for each operation
- Event sourcing often combined

**Event Sourcing**:
- Store events instead of current state
- Rebuild state from events
- Audit trail built-in
- Time travel capability

### Distributed Data Management

**Saga Pattern**:
- Manages distributed transactions
- Sequence of local transactions
- Compensating transactions for rollback
- Two types: Choreography, Orchestration

**Saga Choreography**:
- Services coordinate through events
- No central coordinator
- Decentralized

**Saga Orchestration**:
- Central orchestrator coordinates
- Centralized control
- Easier to understand flow

**Two-Phase Commit (2PC)**:
- Distributed transaction protocol
- Coordinator manages transaction
- Blocking protocol
- Not recommended for microservices (tight coupling)

---

## Service Discovery & Configuration

### Service Discovery

**Problem**: Services need to find each other dynamically.

**Service Registry Pattern**:
- Central registry of services
- Services register on startup
- Clients query registry
- Examples: Eureka, Consul, etcd

**Client-Side Discovery**:
- Client queries service registry
- Client determines service location
- Load balancing at client

**Server-Side Discovery**:
- Load balancer queries registry
- Client doesn't know registry
- Simpler client

**Self-Registration**:
- Service registers itself
- Heartbeat to stay registered
- Deregisters on shutdown

**Third-Party Registration**:
- Service registrar handles registration
- Service doesn't know about registry
- Service mesh often provides this

### Configuration Management

**Externalized Configuration**:
- Configuration outside code
- Environment-specific configs
- Examples: Spring Cloud Config, Consul

**Configuration Server**:
- Centralized configuration
- Version control
- Dynamic updates
- Examples: Spring Cloud Config Server

**Configuration Patterns**:
- Push: Server pushes config to services
- Pull: Services pull config from server
- Hybrid: Combination of both

---

## API Gateway Pattern

### Responsibilities

**Routing**:
- Route requests to appropriate services
- Path-based routing
- Host-based routing

**Authentication & Authorization**:
- Centralized security
- Token validation
- Role-based access control

**Rate Limiting**:
- Prevent abuse
- Throttling
- Quota management

**Load Balancing**:
- Distribute requests
- Health check integration
- Circuit breaker integration

**Request/Response Transformation**:
- Protocol translation
- Data format conversion
- Request aggregation

**Monitoring & Logging**:
- Request logging
- Metrics collection
- Performance monitoring

### API Gateway Types

**Edge Gateway**:
- External-facing
- Internet-facing clients
- Public APIs

**Internal Gateway**:
- Internal services
- Service-to-service communication
- Backend for frontend (BFF) pattern

**Backend for Frontend (BFF)**:
- Different gateway per client type
- Mobile BFF, Web BFF
- Optimized for specific client needs

---

## Circuit Breaker Pattern

### Purpose

Prevent cascading failures by stopping calls to failing services.

### States

**Closed (Normal)**:
- Requests flow normally
- Monitors failure rate
- Transitions to Open on threshold

**Open (Failing)**:
- Requests fail immediately
- No calls to failing service
- After timeout, transitions to Half-Open

**Half-Open (Testing)**:
- Allows limited requests
- Tests if service recovered
- Transitions to Closed if successful
- Transitions to Open if still failing

### Benefits

- **Fault Isolation**: Prevents cascading failures
- **Fast Failure**: Immediate response instead of timeout
- **Automatic Recovery**: Tests service health periodically
- **Degraded Functionality**: System continues with reduced features

### Implementation

**Hystrix** (Netflix, now in maintenance):
- Circuit breaker implementation
- Thread pool isolation
- Fallback mechanisms

**Resilience4j**:
- Modern alternative to Hystrix
- Functional programming style
- Lightweight

**Spring Cloud Circuit Breaker**:
- Abstraction over implementations
- Supports Resilience4j, Hystrix

### Fallback Strategies

- **Default Value**: Return default response
- **Cached Value**: Return cached data
- **Error Response**: Return error gracefully
- **Alternative Service**: Call backup service

---

## Authentication & Security in Microservices

### Authentication Challenges

**Problem**: In microservices, services need to authenticate users and communicate securely with each other.

**Key Challenges**:
- **Stateless Authentication**: Services should not maintain session state
- **Service-to-Service Authentication**: Services need to trust each other
- **Token Validation**: Each service must validate tokens efficiently
- **Centralized vs Distributed**: Where to handle authentication
- **Token Propagation**: Passing authentication context between services

### Authentication Patterns

**1. API Gateway Authentication (Centralized)**:

**How It Works**:
- All external requests go through API Gateway
- Gateway authenticates user (OAuth, JWT, API keys)
- Gateway validates token and extracts user context
- Gateway forwards request to services with user context
- Services trust the gateway (or validate token again)

**Architecture**:
```
Client → API Gateway (Authenticates) → Microservices
         ↓
    Validates JWT/OAuth
    Extracts user info
    Adds headers (X-User-ID, X-Roles)
```

**Benefits**:
- Centralized authentication logic
- Services don't need auth logic
- Single point for security policies
- Easier to update auth mechanisms

**Drawbacks**:
- Gateway becomes single point of failure
- All traffic must go through gateway
- Potential bottleneck

**2. Service-Level Authentication (Distributed)**:

**How It Works**:
- Each service validates tokens independently
- Services can call each other directly
- Each service has auth logic
- Token validation at service level

**Architecture**:
```
Client → Service A (Validates Token) → Service B (Validates Token)
```

**Benefits**:
- No single point of failure
- Services can be accessed directly
- More flexible architecture

**Drawbacks**:
- Auth logic duplicated
- More complex to maintain
- Each service must validate tokens

**3. Hybrid Approach (Recommended)**:

**How It Works**:
- API Gateway authenticates external requests
- Services validate tokens for service-to-service calls
- Shared token validation library
- Centralized user service for user data

**Architecture**:
```
External Client → API Gateway (Auth) → Services (Validate)
Internal Service → Service (Validate) → Other Services
```

### Authentication Mechanisms

**1. JWT (JSON Web Tokens)**:

**Structure**:
- **Header**: Algorithm, token type
- **Payload**: Claims (user ID, roles, expiration)
- **Signature**: Verifies token integrity

**Flow**:
1. User authenticates with credentials
2. Auth service issues JWT
3. Client includes JWT in requests
4. Services validate JWT signature
5. Services extract claims from payload

**Benefits**:
- Stateless (no server-side storage)
- Self-contained (includes user info)
- Scalable (no session storage)
- Standard format

**Challenges**:
- Token revocation difficult
- Token size (can be large)
- Must secure secret key

**2. OAuth 2.0**:

**Flow**:
1. Client redirects to authorization server
2. User authenticates
3. Authorization server issues access token
4. Client uses token to access resources
5. Resource server validates token

**Grant Types**:
- **Authorization Code**: Web applications
- **Client Credentials**: Service-to-service
- **Implicit**: Mobile apps (deprecated)
- **Resource Owner Password**: Trusted clients

**3. API Keys**:

**How It Works**:
- Simple key-value pairs
- Included in request headers
- Validated against database
- Good for service-to-service

**Use Cases**:
- Service-to-service authentication
- Third-party API access
- Simple authentication needs

### Service-to-Service Authentication

**Problem**: Services need to authenticate each other without user context.

**Solutions**:

**1. mTLS (Mutual TLS)**:
- Both services have certificates
- Services authenticate each other
- Encrypted communication
- Used in service mesh (Istio, Linkerd)

**2. Service Accounts**:
- Each service has credentials
- Services authenticate with credentials
- OAuth client credentials flow
- Centralized credential management

**3. API Keys**:
- Services have API keys
- Keys included in requests
- Validated by receiving service
- Simple but less secure

### Token Validation Strategies

**1. Local Validation (JWT)**:
- Service validates JWT signature locally
- No network call needed
- Fast validation
- Requires shared secret/public key

**2. Remote Validation (OAuth)**:
- Service calls authorization server
- Server validates token
- More secure (can revoke)
- Network overhead

**3. Hybrid Validation**:
- Validate signature locally
- Check revocation list (optional)
- Cache validation results
- Balance security and performance

### Security Best Practices

1. **Use HTTPS**: Encrypt all communication
2. **Token Expiration**: Short-lived tokens
3. **Refresh Tokens**: Long-lived refresh, short-lived access
4. **Token Rotation**: Rotate tokens periodically
5. **Least Privilege**: Services have minimum required permissions
6. **Input Validation**: Validate all inputs
7. **Rate Limiting**: Prevent abuse
8. **Audit Logging**: Log all authentication events
9. **Secrets Management**: Secure storage of secrets
10. **Network Segmentation**: Isolate services

### Example: JWT-Based Authentication Flow

```
1. User Login:
   Client → Auth Service (credentials)
   Auth Service → Validates → Issues JWT
   Client ← JWT

2. API Request:
   Client → API Gateway (JWT in header)
   API Gateway → Validates JWT → Extracts user info
   API Gateway → Service (with user context)
   Service → Validates (optional) → Processes request
   Service → Response

3. Service-to-Service:
   Service A → Service B (JWT or service token)
   Service B → Validates → Processes request
```

---

## Fault Isolation & Resilience Patterns

### How One Service Failure Doesn't Bring Down the System

**Core Principle**: Design services to fail independently and gracefully.

### Key Patterns for Fault Isolation

**1. Circuit Breaker Pattern** (Already covered above)

**2. Bulkhead Pattern**:

**Purpose**: Isolate resources to prevent cascading failures.

**How It Works**:
- Separate thread pools for different operations
- Isolated connection pools
- Resource quotas per service
- Failure in one area doesn't affect others

**Example**:
```
Service A:
  - Thread Pool 1: Critical operations (10 threads)
  - Thread Pool 2: Non-critical operations (5 threads)
  - Thread Pool 3: External API calls (3 threads)

If external API fails, only Thread Pool 3 is affected.
Critical operations continue normally.
```

**Benefits**:
- Isolates failures
- Prevents resource exhaustion
- Maintains critical functionality
- Better resource management

**3. Timeout Pattern**:

**Purpose**: Prevent services from waiting indefinitely.

**How It Works**:
- Set timeouts for all external calls
- Fail fast if timeout exceeded
- Use different timeouts for different operations
- Return error or fallback on timeout

**Implementation**:
```java
@HystrixCommand(
    fallbackMethod = "fallbackMethod",
    commandProperties = {
        @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "2000")
    }
)
public String callService() {
    return externalService.getData();
}
```

**4. Retry Pattern with Exponential Backoff**:

**Purpose**: Handle transient failures automatically.

**How It Works**:
- Retry failed requests
- Exponential backoff (wait longer each retry)
- Maximum retry limit
- Don't retry on certain errors (4xx)

**Example**:
```
Attempt 1: Immediate
Attempt 2: Wait 100ms
Attempt 3: Wait 200ms
Attempt 4: Wait 400ms
Attempt 5: Wait 800ms
Give up after 5 attempts
```

**5. Fallback Pattern**:

**Purpose**: Provide alternative behavior when service fails.

**Types**:
- **Default Value**: Return default/cached value
- **Cached Response**: Return last known good response
- **Degraded Service**: Provide reduced functionality
- **Alternative Service**: Call backup service
- **Error Response**: Return graceful error

**6. Health Checks & Auto-Recovery**:

**Purpose**: Detect failures and recover automatically.

**How It Works**:
- Services expose health endpoints
- Load balancer checks health periodically
- Unhealthy instances removed from rotation
- Auto-restart failed services
- Auto-scaling based on health

**Health Check Types**:
- **Liveness**: Is service running?
- **Readiness**: Is service ready for traffic?
- **Startup**: Is service starting up?

**7. Graceful Degradation**:

**Purpose**: System continues with reduced functionality.

**Example - E-commerce**:
```
Normal: Product details + Reviews + Recommendations + Inventory
Degraded (Reviews service down): Product details + Recommendations + Inventory
Degraded (Recommendations down): Product details + Reviews + Inventory
Degraded (Multiple services down): Product details only (core functionality)
```

**8. Async Communication**:

**Purpose**: Decouple services to prevent blocking.

**How It Works**:
- Use message queues for non-critical operations
- Fire-and-forget for notifications
- Event-driven architecture
- Services don't wait for responses

**Example**:
```
Order Service → Creates Order
Order Service → Publishes OrderCreated event (async)
Payment Service → Consumes event (async)
Inventory Service → Consumes event (async)
Shipping Service → Consumes event (async)

If Payment Service is down, order is still created.
Payment will be processed when service recovers.
```

**9. Database Isolation**:

**Purpose**: Each service has its own database.

**Benefits**:
- Database failure affects only one service
- Independent scaling
- Technology diversity
- No shared database bottlenecks

**10. Rate Limiting & Throttling**:

**Purpose**: Prevent overload and protect services.

**How It Works**:
- Limit requests per service/user
- Throttle when limits exceeded
- Protect downstream services
- Prevent DDoS attacks

### Complete Fault Isolation Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Load Balancer                        │
│              (Health Checks, Timeouts)                  │
└────────────────────┬────────────────────────────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
   ┌────▼────┐  ┌────▼────┐  ┌────▼────┐
   │Service A│  │Service B│  │Service C│
   │Instance1│  │Instance1│  │Instance1│
   │Instance2│  │Instance2│  │Instance2│
   │Instance3│  │Instance3│  │Instance3│
   └────┬────┘  └────┬────┘  └────┬────┘
        │            │            │
   ┌────▼────┐  ┌────▼────┐  ┌────▼────┐
   │Database │  │Database │  │Database │
   │   A     │  │   B     │  │   C     │
   │(Replica)│  │(Replica)│  │(Replica)│
   └─────────┘  └─────────┘  └─────────┘

Each Service:
- Multiple instances (redundancy)
- Own database (isolation)
- Circuit breaker (fault tolerance)
- Health checks (auto-recovery)
- Timeouts (fail fast)
- Fallbacks (graceful degradation)
```

### Failure Scenarios and Mitigation

**Scenario 1: Service A Fails Completely**

**What Happens**:
- Health checks detect failure
- Load balancer removes failed instances
- Circuit breaker opens for Service A
- Other services continue normally
- Clients calling Service A get fallback or error

**Mitigation**:
- Multiple instances (redundancy)
- Auto-restart failed instances
- Fallback responses
- Cached data if available

**Scenario 2: Service A is Slow**

**What Happens**:
- Timeout triggers
- Circuit breaker may open
- Requests fail fast
- Fallback activated
- Other services not affected

**Mitigation**:
- Timeouts prevent waiting
- Circuit breaker stops calls
- Fallback provides alternative
- Monitoring alerts team

**Scenario 3: Database A Fails**

**What Happens**:
- Service A cannot access database
- Service A health check fails
- Load balancer removes Service A
- Other services continue (own databases)
- Service A uses read replica if available

**Mitigation**:
- Database replication
- Read replicas for reads
- Failover to backup database
- Cached data for reads

**Scenario 4: Network Partition**

**What Happens**:
- Services can't communicate
- Circuit breakers open
- Services use cached data
- Fallbacks activated
- System continues with degraded functionality

**Mitigation**:
- Async communication (message queues)
- Cached data
- Fallback responses
- Eventual consistency

### Best Practices for Fault Isolation

1. **Design for Failure**: Assume services will fail
2. **Multiple Instances**: Always run multiple instances
3. **Health Checks**: Implement comprehensive health checks
4. **Timeouts**: Set appropriate timeouts
5. **Circuit Breakers**: Use circuit breakers for external calls
6. **Fallbacks**: Always have fallback strategies
7. **Async When Possible**: Use async communication
8. **Database Isolation**: Each service owns its database
9. **Monitoring**: Monitor everything
10. **Testing**: Test failure scenarios

---

## Microservices Design Patterns

### Overview

Design patterns provide proven solutions to common problems in microservices architecture. Understanding these patterns helps design robust, scalable, and maintainable systems.

### Core Microservices Patterns

**1. Service Decomposition Patterns** (Already covered in Architecture Patterns)

**2. Database per Service Pattern** (Already covered)

**3. API Gateway Pattern** (Already covered)

**4. Circuit Breaker Pattern** (Already covered)

**5. Service Discovery Pattern**

**Purpose**: Enable services to find each other dynamically.

**Problem**: Services need to communicate but don't know each other's locations (IPs, ports change).

**Solution**: 
- Service registry maintains list of available services
- Services register on startup
- Services query registry to find other services
- Registry monitors health and removes failed services

**Types**:
- **Client-Side Discovery**: Client queries registry, determines service location
- **Server-Side Discovery**: Load balancer queries registry, routes requests

**Examples**: Eureka, Consul, etcd, Zookeeper

**6. Saga Pattern**

**Purpose**: Manage distributed transactions across multiple services.

**Problem**: Traditional ACID transactions don't work across services.

**Solution**: 
- Sequence of local transactions
- Each transaction publishes event
- Compensating transactions for rollback
- Two approaches: Choreography (event-driven) or Orchestration (centralized)

**Use Cases**: Order processing, payment workflows, multi-step processes

**7. CQRS (Command Query Responsibility Segregation)**

**Purpose**: Separate read and write operations.

**Problem**: Same data model may not be optimal for both reads and writes.

**Solution**:
- Separate read and write models
- Write model optimized for updates
- Read model optimized for queries
- Often combined with event sourcing

**Benefits**:
- Independent scaling
- Optimized for each operation
- Flexible query models

**8. Event Sourcing Pattern**

**Purpose**: Store all changes as sequence of events.

**Problem**: Need complete audit trail and ability to rebuild state.

**Solution**:
- Store events instead of current state
- Rebuild state by replaying events
- Complete history available
- Time travel capability

**Use Cases**: Audit requirements, complex domain models, event-driven systems

**9. Strangler Fig Pattern**

**Purpose**: Gradually migrate from monolith to microservices.

**Problem**: Need to migrate large monolith incrementally.

**Solution**:
- Gradually replace monolith functionality with microservices
- Route new features to microservices
- Migrate existing features over time
- Eventually strangle (replace) monolith

**Benefits**:
- Low risk migration
- Incremental approach
- No big bang rewrite

**10. Backend for Frontend (BFF) Pattern**

**Purpose**: Create separate backend for each client type.

**Problem**: Different clients (web, mobile, desktop) have different needs.

**Solution**:
- Separate API gateway/backend per client
- Each BFF optimized for specific client
- Reduces client complexity
- Better performance

**Example**: 
- Web BFF: Optimized for web browser
- Mobile BFF: Optimized for mobile app (smaller payloads)

**11. Bulkhead Pattern**

**Purpose**: Isolate resources to prevent cascading failures.

**Problem**: Failure in one area affects entire service.

**Solution**:
- Separate thread pools
- Isolated connection pools
- Resource quotas per operation
- Failure isolated to specific area

**Benefits**: Prevents resource exhaustion, maintains critical functionality

**12. Sidecar Pattern**

**Purpose**: Attach supporting functionality to main service.

**Problem**: Need cross-cutting concerns without modifying service.

**Solution**:
- Sidecar container runs alongside main service
- Handles cross-cutting concerns (logging, monitoring, security)
- Service mesh uses this pattern
- Main service focuses on business logic

**Example**: Service mesh sidecars (Envoy, Linkerd-proxy)

**13. Ambassador Pattern**

**Purpose**: Offload common client connectivity tasks.

**Problem**: Services need to handle service discovery, routing, retries.

**Solution**:
- Ambassador proxy handles client-side concerns
- Service discovery, load balancing, retries
- Can be implemented as sidecar
- Simplifies service code

**14. Anti-Corruption Layer Pattern**

**Purpose**: Isolate new system from legacy system.

**Problem**: Need to integrate with legacy system without being corrupted by its design.

**Solution**:
- Translation layer between new and legacy
- Converts between different data models
- Protects new system from legacy issues
- Gradual migration path

**15. Database Sharding Pattern**

**Purpose**: Partition data across multiple databases.

**Problem**: Single database can't handle scale.

**Solution**:
- Split data by shard key
- Each shard is separate database
- Distribute load across shards
- Shard key selection critical

**16. Shared Database Anti-Pattern**

**What NOT to Do**: Multiple services share same database.

**Problems**:
- Tight coupling
- Can't scale independently
- Technology lock-in
- Schema changes affect all services

**Solution**: Use Database per Service pattern instead.

**17. Service Mesh Pattern**

**Purpose**: Infrastructure layer for service-to-service communication.

**Problem**: Need consistent handling of cross-cutting concerns.

**Solution**:
- Sidecar proxies handle communication
- Control plane manages proxies
- Handles: service discovery, load balancing, security, observability
- Transparent to application code

**Examples**: Istio, Linkerd, Consul Connect

**18. Event-Driven Architecture Pattern**

**Purpose**: Services communicate via events.

**Problem**: Need loose coupling and scalability.

**Solution**:
- Services publish events
- Other services subscribe to events
- Asynchronous communication
- Eventual consistency

**Benefits**: Loose coupling, scalability, fault tolerance

**19. Choreography Pattern**

**Purpose**: Services coordinate through events without central orchestrator.

**Problem**: Need distributed coordination without central point.

**Solution**:
- Services publish events
- Services react to events
- No central coordinator
- Decentralized control

**Use Cases**: Saga pattern, workflow coordination

**20. Orchestration Pattern**

**Purpose**: Central orchestrator coordinates service interactions.

**Problem**: Need centralized control and visibility.

**Solution**:
- Orchestrator coordinates workflow
- Orchestrator calls services
- Centralized control
- Better visibility

**Use Cases**: Complex workflows, Saga pattern, business processes

**21. API Composition Pattern**

**Purpose**: Aggregate data from multiple services.

**Problem**: Client needs data from multiple services.

**Solution**:
- API composer calls multiple services
- Aggregates responses
- Returns combined result
- Can cache results

**Alternative**: Use GraphQL for flexible data aggregation

**22. Branch Pattern (Saga)**

**Purpose**: Execute saga steps in parallel.

**Problem**: Some saga steps can run in parallel.

**Solution**:
- Execute independent steps in parallel
- Wait for all to complete
- Continue with next steps
- Improves performance

**23. Aggregator Pattern**

**Purpose**: Collect data from multiple services and return aggregated response.

**Problem**: Client needs to call multiple services.

**Solution**:
- Aggregator service calls multiple services
- Combines responses
- Returns single response
- Reduces client complexity

**24. Proxy Pattern (Microservices)**

**Purpose**: Proxy service forwards requests to appropriate service.

**Problem**: Need to route requests without exposing internal structure.

**Solution**:
- Proxy receives requests
- Routes to appropriate service
- Can add cross-cutting concerns
- Hides internal architecture

**25. Chain Pattern**

**Purpose**: Services call each other in sequence.

**Problem**: Need to process request through multiple services.

**Solution**:
- Service A calls Service B
- Service B calls Service C
- Each service adds value
- Response flows back through chain

**Use Cases**: Request processing pipeline, data transformation chain

---

## API Design Patterns & Best Practices

### REST API Design Patterns

**1. Resource-Based URLs**

**Good**:
```
GET /users/123
GET /users/123/orders
POST /users/123/orders
```

**Bad**:
```
GET /getUser?id=123
POST /createOrder
GET /getUserOrders?userId=123
```

**Principles**:
- Use nouns, not verbs
- Represent resources, not actions
- Hierarchical structure
- Plural nouns for collections

**2. HTTP Methods Semantics**

**GET**: Retrieve resource (idempotent, safe)
```
GET /users/123
GET /users
```

**POST**: Create resource (not idempotent)
```
POST /users
Body: { "name": "John", "email": "john@example.com" }
```

**PUT**: Update/replace resource (idempotent)
```
PUT /users/123
Body: { "name": "John", "email": "john@example.com" }
```

**PATCH**: Partial update (idempotent)
```
PATCH /users/123
Body: { "email": "newemail@example.com" }
```

**DELETE**: Delete resource (idempotent)
```
DELETE /users/123
```

**3. Status Codes**

**2xx Success**:
- `200 OK`: Request succeeded
- `201 Created`: Resource created
- `202 Accepted`: Request accepted, processing async
- `204 No Content`: Success, no response body

**4xx Client Error**:
- `400 Bad Request`: Invalid request
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Not authorized
- `404 Not Found`: Resource not found
- `409 Conflict`: Resource conflict
- `422 Unprocessable Entity`: Validation error

**5xx Server Error**:
- `500 Internal Server Error`: Server error
- `502 Bad Gateway`: Gateway error
- `503 Service Unavailable`: Service unavailable
- `504 Gateway Timeout`: Gateway timeout

**4. Pagination Pattern**

**Offset-Based**:
```
GET /users?page=1&limit=20
GET /users?offset=0&limit=20
```

**Cursor-Based** (Better for large datasets):
```
GET /users?cursor=abc123&limit=20
Response: {
  "data": [...],
  "next_cursor": "xyz789",
  "has_more": true
}
```

**5. Filtering, Sorting, Searching**

**Filtering**:
```
GET /users?status=active&role=admin
GET /orders?startDate=2024-01-01&endDate=2024-12-31
```

**Sorting**:
```
GET /users?sort=name&order=asc
GET /users?sort=-created_at  (descending)
```

**Searching**:
```
GET /users?search=john
GET /products?q=laptop&category=electronics
```

**6. API Versioning**

**URL Versioning**:
```
GET /v1/users
GET /v2/users
```

**Header Versioning**:
```
GET /users
Headers: Accept: application/vnd.api.v1+json
```

**Query Parameter**:
```
GET /users?version=1
```

**Best Practice**: URL versioning is most common and explicit.

**7. HATEOAS (Hypermedia as the Engine of Application State)**

**Purpose**: Include links to related resources in responses.

**Example**:
```json
{
  "id": 123,
  "name": "John",
  "links": {
    "self": "/users/123",
    "orders": "/users/123/orders",
    "profile": "/users/123/profile"
  }
}
```

**Benefits**: Client discovers available actions dynamically.

**8. Error Response Format**

**Standard Format**:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": [
      {
        "field": "email",
        "message": "Email is required"
      }
    ],
    "timestamp": "2024-01-15T10:30:00Z",
    "request_id": "abc-123-def"
  }
}
```

**9. Request/Response Patterns**

**Request Validation**:
- Validate all inputs
- Return clear error messages
- Use appropriate status codes
- Validate data types and formats

**Response Consistency**:
- Consistent structure
- Include metadata (pagination, timestamps)
- Use standard formats (JSON)
- Include relevant links

**10. Idempotency**

**Purpose**: Make operations safe to retry.

**Implementation**:
- Use idempotency keys
- Store idempotency key with request
- Return same response for duplicate requests
- Important for POST, PUT, DELETE

**Example**:
```
POST /orders
Headers: Idempotency-Key: abc-123-def
Body: { ... }

If same key used again, return original response.
```

### GraphQL Patterns

**1. Query Pattern**

**Single Resource**:
```graphql
query {
  user(id: "123") {
    id
    name
    email
  }
}
```

**Multiple Resources**:
```graphql
query {
  user(id: "123") {
    name
    orders {
      id
      total
    }
  }
}
```

**2. Mutation Pattern**

```graphql
mutation {
  createUser(input: {
    name: "John"
    email: "john@example.com"
  }) {
    id
    name
  }
}
```

**3. Subscription Pattern** (Real-time)

```graphql
subscription {
  orderStatusChanged(orderId: "123") {
    id
    status
    updatedAt
  }
}
```

### API Best Practices

**1. Design Principles**

- **RESTful**: Follow REST principles
- **Stateless**: Don't store client state
- **Cacheable**: Use appropriate cache headers
- **Uniform Interface**: Consistent API design
- **Layered System**: Can have intermediaries

**2. Security**

- **HTTPS**: Always use HTTPS
- **Authentication**: JWT, OAuth 2.0
- **Authorization**: Role-based access control
- **Input Validation**: Validate all inputs
- **Rate Limiting**: Prevent abuse
- **CORS**: Configure properly
- **SQL Injection**: Use parameterized queries
- **XSS**: Sanitize inputs

**3. Performance**

- **Pagination**: Don't return all data
- **Caching**: Use appropriate cache headers
- **Compression**: Gzip responses
- **Field Selection**: Allow clients to select fields
- **Async Operations**: For long-running tasks
- **Connection Pooling**: Reuse connections

**4. Documentation**

- **OpenAPI/Swagger**: Standard API documentation
- **Examples**: Provide request/response examples
- **Error Codes**: Document all error codes
- **Authentication**: Document auth flow
- **Rate Limits**: Document rate limits
- **Versioning**: Document versioning strategy

**5. Monitoring & Observability**

- **Logging**: Log all requests/responses
- **Metrics**: Track API metrics (latency, errors)
- **Tracing**: Distributed tracing
- **Alerting**: Alert on errors, latency
- **Health Checks**: Expose health endpoints

**6. Testing**

- **Unit Tests**: Test business logic
- **Integration Tests**: Test API endpoints
- **Contract Tests**: Test API contracts
- **Load Tests**: Test under load
- **Security Tests**: Test security

### Common API Anti-Patterns

**1. Chatty APIs**

**Bad**: Multiple small requests
```
GET /user/123
GET /user/123/orders
GET /user/123/profile
GET /user/123/settings
```

**Good**: Single request with related data
```
GET /user/123?include=orders,profile,settings
```

**2. Over-fetching**

**Bad**: Return all fields always
```
GET /users → Returns 50 fields per user
```

**Good**: Allow field selection
```
GET /users?fields=id,name,email
```

**3. Under-fetching**

**Bad**: Client needs multiple requests
```
GET /user/123
GET /user/123/orders
GET /user/123/profile
```

**Good**: Include related data
```
GET /user/123?include=orders,profile
```

**4. Inconsistent Naming**

**Bad**: Mixed naming conventions
```
GET /getUsers
GET /user_list
GET /Users
```

**Good**: Consistent naming
```
GET /users
```

**5. Ignoring HTTP Semantics**

**Bad**: Using POST for everything
```
POST /getUser
POST /deleteUser
POST /updateUser
```

**Good**: Use appropriate HTTP methods
```
GET /users/123
DELETE /users/123
PUT /users/123
```

---

## General Design Patterns for Microservices

### Creational Patterns

**1. Factory Pattern for Service Creation**

**Purpose**: Create service instances dynamically.

**Use Case**: Service registry, dynamic service instantiation

**2. Builder Pattern for Configuration**

**Purpose**: Build complex service configurations.

**Use Case**: Service configuration, API client setup

**3. Singleton Pattern (Use Carefully)**

**Purpose**: Single instance of service component.

**Use Case**: Service registry client, configuration manager

**Note**: Be careful with singletons in distributed systems.

### Structural Patterns

**1. Adapter Pattern**

**Purpose**: Adapt incompatible interfaces.

**Use Case**: 
- Legacy system integration
- Protocol translation (REST to gRPC)
- Data format conversion

**2. Facade Pattern**

**Purpose**: Simplify complex subsystem interface.

**Use Case**: 
- API Gateway (facade for multiple services)
- Service aggregator
- Simplified client interface

**3. Proxy Pattern**

**Purpose**: Control access to service.

**Use Case**: 
- Service proxy
- Caching proxy
- Security proxy

**4. Decorator Pattern**

**Purpose**: Add behavior to services dynamically.

**Use Case**: 
- Cross-cutting concerns (logging, metrics)
- Service middleware
- Request/response transformation

### Behavioral Patterns

**1. Observer Pattern**

**Purpose**: Notify multiple services of events.

**Use Case**: 
- Event-driven architecture
- Pub/Sub messaging
- Service notifications

**2. Strategy Pattern**

**Purpose**: Encapsulate algorithms, make them interchangeable.

**Use Case**: 
- Different authentication strategies
- Different caching strategies
- Different retry strategies

**3. Chain of Responsibility**

**Purpose**: Pass requests through chain of handlers.

**Use Case**: 
- Request processing pipeline
- Middleware chain
- Validation chain

**4. Command Pattern**

**Purpose**: Encapsulate requests as objects.

**Use Case**: 
- Saga pattern (commands)
- Undo/redo functionality
- Request queuing

**5. Template Method Pattern**

**Purpose**: Define algorithm skeleton, let subclasses implement steps.

**Use Case**: 
- Service lifecycle management
- Request processing template
- Data transformation pipeline

### Integration Patterns

**1. Messaging Patterns**

**Point-to-Point**: One sender, one receiver
**Publish-Subscribe**: One sender, multiple receivers
**Request-Reply**: Request with response
**Fire-and-Forget**: Send without waiting

**2. Data Integration Patterns**

**Shared Database**: (Anti-pattern, avoid)
**Database per Service**: (Recommended)
**API Composition**: Aggregate from multiple services
**CQRS**: Separate read/write models

**3. Communication Patterns**

**Synchronous**: REST, gRPC (immediate response)
**Asynchronous**: Message queues, events (delayed response)
**Hybrid**: Both synchronous and asynchronous

### Resilience Patterns

**1. Retry Pattern**

**Purpose**: Retry failed operations.

**Implementation**: Exponential backoff, max retries, jitter

**2. Timeout Pattern**

**Purpose**: Fail fast if operation takes too long.

**Implementation**: Set timeouts for all external calls

**3. Circuit Breaker Pattern** (Already covered)

**4. Bulkhead Pattern** (Already covered)

**5. Fallback Pattern** (Already covered)

**6. Health Check Pattern**

**Purpose**: Monitor service health.

**Implementation**: Health endpoints, liveness/readiness probes

---

## Deployment & DevOps

### Containerization

**Docker**:
- Package service with dependencies
- Consistent environments
- Easy deployment

**Container Orchestration**:
- **Kubernetes**: Industry standard
- **Docker Swarm**: Simpler alternative
- **Amazon ECS**: AWS managed

### Kubernetes Concepts

**Pods**:
- Smallest deployable unit
- Contains one or more containers
- Shared network and storage

**Services**:
- Stable network endpoint
- Load balancing
- Service discovery

**Deployments**:
- Manages pod replicas
- Rolling updates
- Rollback capability

**ConfigMaps & Secrets**:
- Configuration management
- Sensitive data handling

**Ingress**:
- External access to services
- SSL termination
- Load balancing

### CI/CD Pipeline

**Continuous Integration**:
- Automated testing
- Code quality checks
- Build automation

**Continuous Deployment**:
- Automated deployment
- Environment promotion
- Rollback mechanisms

**Pipeline Stages**:
1. Source control
2. Build
3. Test (unit, integration)
4. Package (Docker image)
5. Deploy (staging, production)
6. Verify

### Deployment Strategies

**Blue-Green Deployment**:
- Two identical environments
- Switch traffic instantly
- Zero downtime
- Easy rollback

**Canary Deployment**:
- Gradual rollout
- Small percentage first
- Monitor and expand
- Risk mitigation

**Rolling Deployment**:
- Gradual replacement
- One instance at a time
- No downtime
- Kubernetes default

---

## Monitoring & Observability

### Three Pillars

**Metrics**:
- Quantitative measurements
- Performance indicators
- Examples: CPU, memory, request rate, error rate

**Logging**:
- Event records
- Debugging information
- Structured logging preferred

**Tracing**:
- Request flow across services
- Distributed tracing
- Examples: Zipkin, Jaeger

### Monitoring Tools

**Application Performance Monitoring (APM)**:
- New Relic, Datadog, AppDynamics
- End-to-end visibility
- Performance insights

**Log Aggregation**:
- ELK Stack (Elasticsearch, Logstash, Kibana)
- Splunk
- CloudWatch Logs

**Distributed Tracing**:
- Zipkin
- Jaeger
- AWS X-Ray

**Metrics Collection**:
- Prometheus
- Grafana
- CloudWatch

### Health Checks

**Liveness Probe**:
- Is service running?
- Restart if unhealthy

**Readiness Probe**:
- Is service ready for traffic?
- Remove from load balancer if not ready

**Startup Probe**:
- Is service starting?
- Prevents premature restarts

---

## Best Practices & Anti-patterns

### Best Practices

1. **Start with Monolith**: Don't start with microservices
2. **Domain-Driven Design**: Align services with domains
3. **API-First Design**: Design APIs before implementation
4. **Stateless Services**: Services should be stateless
5. **Idempotency**: Make operations idempotent
6. **Versioning**: Version your APIs
7. **Documentation**: Maintain API documentation
8. **Testing**: Comprehensive testing strategy
9. **Monitoring**: Monitor everything
10. **Security**: Security at every layer

### Anti-patterns

1. **Shared Database**: Creates tight coupling
2. **Distributed Monolith**: Services too tightly coupled
3. **No API Versioning**: Breaking changes affect clients
4. **Synchronous Everything**: Causes cascading failures
5. **No Circuit Breaker**: Cascading failures
6. **Inadequate Monitoring**: Can't diagnose issues
7. **Too Many Services**: Over-engineering
8. **Inconsistent Patterns**: Makes system hard to understand
9. **No Service Mesh**: Complex service-to-service communication
10. **Ignoring Data Consistency**: Data integrity issues

---

## Common Interview Questions

### Fundamental Questions

1. **What are microservices?**
   - Small, independent services
   - Own process, own database
   - Communicate via APIs

2. **Microservices vs Monolith?**
   - Monolith: Single deployable, tight coupling
   - Microservices: Multiple services, loose coupling

3. **When to use microservices?**
   - Large applications
   - Multiple teams
   - Independent scaling needs
   - Technology diversity

4. **What is API Gateway?**
   - Single entry point
   - Routing, security, rate limiting
   - Service abstraction

5. **How do services communicate?**
   - Synchronous: REST, gRPC
   - Asynchronous: Message queues, events

6. **What is Circuit Breaker?**
   - Prevents cascading failures
   - Three states: Closed, Open, Half-Open
   - Fast failure

7. **How to handle distributed transactions?**
   - Saga pattern
   - Event-driven architecture
   - Avoid 2PC

8. **What is Service Discovery?**
   - Dynamic service location
   - Service registry
   - Client-side or server-side

9. **Database per Service?**
   - Each service owns database
   - Loose coupling
   - Data ownership

10. **How to ensure data consistency?**
    - Eventual consistency
    - Saga pattern
    - Event sourcing

### Advanced Questions

1. **How to handle service versioning?**
   - URL versioning
   - Header versioning
   - Backward compatibility

2. **What is Service Mesh?**
   - Infrastructure layer
   - Handles service-to-service communication
   - Examples: Istio, Linkerd

3. **How to test microservices?**
   - Unit tests
   - Integration tests
   - Contract tests
   - End-to-end tests

4. **How to handle security?**
   - API Gateway authentication
   - Service-to-service authentication
   - OAuth, JWT tokens

5. **What is CQRS?**
   - Separate read/write models
   - Optimized for each operation
   - Often with event sourcing

6. **How to monitor microservices?**
   - Metrics, logs, tracing
   - Distributed tracing
   - Health checks

7. **Deployment strategies?**
   - Blue-Green
   - Canary
   - Rolling

8. **How to handle failures?**
   - Circuit breaker
   - Retry with backoff
   - Fallback mechanisms
   - Bulkhead pattern

9. **How to handle authentication in microservices?**
   - API Gateway authentication
   - JWT tokens
   - Service-to-service authentication (mTLS, API keys)
   - Token validation strategies

10. **How does one service failure not bring down the system?**
    - Circuit breakers prevent cascading failures
    - Multiple instances provide redundancy
    - Health checks and auto-recovery
    - Database isolation per service
    - Async communication decouples services
    - Fallback mechanisms provide alternatives

---

## Advanced Interview Questions & Answers

### Q1: How do you handle distributed transactions in microservices? Explain the Saga pattern in detail.

**Answer**:

**The Problem with Distributed Transactions**:
- Traditional 2PC (Two-Phase Commit) creates tight coupling
- Locks resources across services
- Poor performance and availability
- Not suitable for microservices

**Saga Pattern Solution**:

**Definition**: Saga is a sequence of local transactions where each transaction updates data and publishes an event. If a step fails, compensating transactions undo previous steps.

**Two Approaches**:

**1. Choreography (Event-Driven)**:
- Services coordinate through events
- No central coordinator
- Each service knows what to do next
- Decentralized

**Example - Order Processing**:
```
Order Service → Create Order → Publish OrderCreated
Payment Service → Process Payment → Publish PaymentProcessed
Inventory Service → Reserve Items → Publish ItemsReserved
Shipping Service → Create Shipment → Publish ShipmentCreated
```

**If Payment Fails**:
```
Payment Service → Publish PaymentFailed
Order Service → Cancel Order (compensating)
Inventory Service → Release Items (compensating)
```

**Pros**:
- Loose coupling
- No single point of failure
- Services are autonomous

**Cons**:
- Hard to understand flow
- Difficult to debug
- Eventual consistency

**2. Orchestration (Centralized)**:
- Central orchestrator coordinates
- Orchestrator tells services what to do
- Centralized control
- Easier to understand

**Example**:
```
Orchestrator:
  1. Call Order Service → Create Order
  2. Call Payment Service → Process Payment
  3. Call Inventory Service → Reserve Items
  4. Call Shipping Service → Create Shipment
```

**If Payment Fails**:
```
Orchestrator:
  1. Call Order Service → Cancel Order
  2. Stop process
```

**Pros**:
- Centralized control
- Easier to understand
- Better error handling
- Can add timeouts

**Cons**:
- Single point of failure (orchestrator)
- Additional service to maintain
- Can become bottleneck

**Compensating Transactions**:
- Each step has compensating action
- Undo previous work
- May not be perfect (e.g., can't undo email sent)
- Eventual consistency

**Best Practices**:
1. Design compensating transactions carefully
2. Use idempotent operations
3. Implement timeouts
4. Log all steps for debugging
5. Consider partial failures

---

### Q2: Explain Service Mesh and how it differs from API Gateway.

**Answer**:

**Service Mesh**:

**Definition**: Infrastructure layer that handles service-to-service communication in microservices architecture.

**Key Components**:
1. **Data Plane**: Sidecar proxies (Envoy, Linkerd-proxy) that handle traffic
2. **Control Plane**: Manages and configures proxies (Istio, Linkerd)

**Responsibilities**:
- Service discovery
- Load balancing
- Retry logic
- Circuit breaking
- Security (mTLS)
- Observability (metrics, tracing)
- Rate limiting
- Timeout management

**How It Works**:
- **Sidecar Pattern**: Each service has proxy sidecar
- All traffic goes through sidecar
- Sidecar handles cross-cutting concerns
- Application code doesn't need to implement these

**Example - Istio**:
```
Service A → Sidecar A → Sidecar B → Service B
           (Envoy)      (Envoy)
```

**API Gateway**:

**Definition**: Single entry point for external clients to access services.

**Responsibilities**:
- Request routing
- Authentication/Authorization
- Rate limiting
- API versioning
- Request/response transformation
- SSL termination

**Key Differences**:

| Aspect | Service Mesh | API Gateway |
|--------|--------------|-------------|
| **Scope** | Service-to-service (internal) | Client-to-service (external) |
| **Location** | Sidecar with each service | Single entry point |
| **Traffic** | East-west (internal) | North-south (external) |
| **Purpose** | Infrastructure concerns | API management |
| **Transparency** | Transparent to application | Explicit API |

**When to Use Service Mesh**:
- Complex service-to-service communication
- Need for observability across services
- Security requirements (mTLS)
- Multiple services need same cross-cutting concerns

**When to Use API Gateway**:
- External API access
- API versioning needed
- Client-specific transformations
- Centralized authentication

**Can Use Both**:
- API Gateway for external traffic
- Service Mesh for internal traffic
- Complementary, not competing

**Service Mesh Benefits**:
1. **Separation of Concerns**: Infrastructure logic separate from business logic
2. **Consistency**: Same policies across all services
3. **Observability**: Built-in metrics, tracing, logging
4. **Security**: mTLS, policy enforcement
5. **No Code Changes**: Works with existing services

**Service Mesh Challenges**:
1. **Complexity**: Additional infrastructure to manage
2. **Performance**: Latency overhead from sidecars
3. **Resource Usage**: Each sidecar consumes resources
4. **Learning Curve**: New technology to learn

---

### Q3: How do you implement service discovery in a microservices architecture? Compare different approaches.

**Answer**:

**Service Discovery Problem**:
- Services need to find each other
- Services may be on different hosts/ports
- Services scale dynamically
- IP addresses change

**Approaches**:

**1. Client-Side Discovery**:

**How It Works**:
- Client queries service registry
- Client determines service location
- Client directly calls service
- Client handles load balancing

**Example**:
```
Client → Service Registry (Eureka) → Get Service List
Client → Service Instance (direct call)
```

**Pros**:
- Simple architecture
- No additional network hop
- Client has control

**Cons**:
- Client complexity
- Client must implement discovery logic
- Tight coupling to registry

**2. Server-Side Discovery**:

**How It Works**:
- Client calls load balancer
- Load balancer queries registry
- Load balancer routes to service
- Client doesn't know about registry

**Example**:
```
Client → Load Balancer → Service Registry → Service Instance
```

**Pros**:
- Simple client
- Centralized routing
- Can add features (SSL, auth) at LB

**Cons**:
- Additional network hop
- Load balancer is single point of failure
- Less flexible

**3. Service Registry Pattern**:

**Components**:
- **Service Registry**: Database of available services
- **Service Registration**: Services register on startup
- **Service Discovery**: Services query registry

**Registration Methods**:

**Self-Registration**:
- Service registers itself
- Service sends heartbeat
- Service deregisters on shutdown

**Third-Party Registration**:
- Service registrar handles registration
- Service doesn't know about registry
- Service mesh often provides this

**Popular Tools**:
- **Eureka** (Netflix): Self-registration, client-side discovery
- **Consul** (HashiCorp): Health checking, service discovery
- **etcd**: Distributed key-value store
- **Zookeeper**: Coordination service

**4. DNS-Based Discovery**:

**How It Works**:
- Services registered in DNS
- Client resolves DNS name
- DNS returns service IPs
- Simple but limited

**Pros**:
- Simple, standard protocol
- No additional infrastructure
- Works with existing DNS

**Cons**:
- DNS caching issues
- Limited features
- Not real-time

**Best Practices**:
1. **Health Checks**: Registry should check service health
2. **Heartbeats**: Services should send periodic heartbeats
3. **Deregistration**: Services should deregister on shutdown
4. **Caching**: Clients should cache registry data
5. **Failover**: Handle registry failures gracefully

**Example - Eureka**:
```java
// Service Registration
@SpringBootApplication
@EnableEurekaClient
public class UserService {
    public static void main(String[] args) {
        SpringApplication.run(UserService.class, args);
    }
}

// Service Discovery
@Autowired
private DiscoveryClient discoveryClient;

public String getServiceUrl(String serviceName) {
    List<ServiceInstance> instances = 
        discoveryClient.getInstances(serviceName);
    // Load balance and return URL
}
```

---

### Q4: Explain the Circuit Breaker pattern in detail. How does it prevent cascading failures?

**Answer**:

**Circuit Breaker Pattern**:

**Purpose**: Prevent cascading failures by stopping calls to failing services.

**Analogy**: Like electrical circuit breaker - opens circuit when overloaded.

**States**:

**1. Closed (Normal)**:
- Requests flow normally
- Monitors failure rate
- Counts failures and successes
- Transitions to Open when threshold exceeded

**2. Open (Failing)**:
- Requests fail immediately
- No calls to failing service
- Fast failure (no timeout wait)
- After timeout, transitions to Half-Open

**3. Half-Open (Testing)**:
- Allows limited requests through
- Tests if service recovered
- If successful → Closed
- If fails → Open again

**How It Prevents Cascading Failures**:

**Without Circuit Breaker**:
```
Service A → Service B (slow/failing)
Service A waits for timeout (30s)
Service A resources tied up
More requests to Service A → More waiting
Cascading failure spreads
```

**With Circuit Breaker**:
```
Service A → Service B (failing)
Circuit opens after threshold
Service A fails fast (<1ms)
Service A resources freed quickly
Fallback mechanism activated
System continues with reduced functionality
```

**Implementation Example**:

**Hystrix (Netflix)**:
```java
@HystrixCommand(fallbackMethod = "fallbackMethod")
public String callServiceB() {
    return serviceB.getData();
}

public String fallbackMethod() {
    return "Default response";
}
```

**Resilience4j**:
```java
CircuitBreaker circuitBreaker = CircuitBreaker.ofDefaults("serviceB");

Supplier<String> decoratedSupplier = CircuitBreaker
    .decorateSupplier(circuitBreaker, () -> serviceB.getData());

String result = Try.ofSupplier(decoratedSupplier)
    .recover(throwable -> "Default response")
    .get();
```

**Configuration Parameters**:

1. **Failure Threshold**: Percentage of failures to open circuit (e.g., 50%)
2. **Request Threshold**: Minimum requests before opening (e.g., 20)
3. **Timeout Duration**: How long circuit stays open (e.g., 60 seconds)
4. **Half-Open Max Calls**: Requests allowed in half-open state (e.g., 3)
5. **Success Threshold**: Successes needed to close circuit (e.g., 5)

**Fallback Strategies**:

1. **Default Value**: Return default/cached value
2. **Cached Response**: Return last known good response
3. **Error Response**: Return graceful error message
4. **Alternative Service**: Call backup service
5. **Queue for Later**: Queue request for retry

**Benefits**:
1. **Fast Failure**: No waiting for timeouts
2. **Resource Protection**: Frees resources quickly
3. **Automatic Recovery**: Tests service health periodically
4. **Degraded Functionality**: System continues with reduced features
5. **Observability**: Metrics on circuit state

**Best Practices**:
1. **Tune Thresholds**: Based on service characteristics
2. **Monitor Metrics**: Track circuit state changes
3. **Alert on Opens**: Know when circuits open
4. **Test Fallbacks**: Ensure fallbacks work
5. **Gradual Recovery**: Don't overwhelm recovering service

---

### Q5: How do you handle data consistency in microservices? Explain Eventual Consistency and Event Sourcing.

**Answer**:

**The Challenge**:
- Each service has its own database
- No distributed transactions (2PC)
- Need to maintain consistency
- Services are independent

**Eventual Consistency**:

**Definition**: System will become consistent over time, not immediately.

**How It Works**:
- Services update their databases independently
- Changes propagated via events
- Eventually all services see consistent state
- Acceptable delay for consistency

**Example - Order Processing**:
```
Order Service: Order created (immediate)
Payment Service: Payment processed (eventual - via event)
Inventory Service: Items reserved (eventual - via event)
Shipping Service: Shipment created (eventual - via event)
```

**Trade-offs**:
- ✅ High availability
- ✅ Better performance
- ✅ Scalability
- ❌ Temporary inconsistencies
- ❌ Complex to handle

**Event Sourcing**:

**Definition**: Store all changes as sequence of events, not just current state.

**How It Works**:
- Store events as source of truth
- Rebuild current state by replaying events
- Complete audit trail
- Time travel capability

**Example**:
```
Events:
1. OrderCreated (orderId: 123, customerId: 456)
2. PaymentProcessed (orderId: 123, amount: 100)
3. OrderShipped (orderId: 123, tracking: "ABC123")

Current State (rebuilt from events):
Order {
  id: 123,
  customerId: 456,
  status: "shipped",
  amount: 100,
  tracking: "ABC123"
}
```

**Benefits**:
1. **Complete History**: All changes recorded
2. **Audit Trail**: Know what happened and when
3. **Time Travel**: Rebuild state at any point
4. **Debugging**: Understand what happened
5. **Flexibility**: Can create new views from events

**Challenges**:
1. **Event Store**: Need reliable event storage
2. **Replay Performance**: Can be slow for large history
3. **Event Versioning**: Events may change over time
4. **Complexity**: More complex than CRUD

**CQRS with Event Sourcing**:

**Command Query Responsibility Segregation**:
- **Write Model**: Event store (events)
- **Read Model**: Optimized views (projections)
- **Separation**: Different models for reads and writes

**How It Works**:
```
Command → Event Store → Events
Events → Projections → Read Models
Query → Read Model (fast, optimized)
```

**Benefits**:
- Optimized for each operation
- Scalable read and write independently
- Flexible query models

**Example**:
```
Write: CreateOrder → OrderCreated event
Read: Query order by customer → Optimized view
```

**Best Practices**:
1. **Idempotency**: Make operations idempotent
2. **Event Versioning**: Handle event schema changes
3. **Snapshot**: Periodically snapshot state for performance
4. **Event Ordering**: Ensure events processed in order
5. **Compensation**: Handle failures with compensating events

---

### Q6: How do you implement authentication in a microservices architecture? Explain different approaches and their trade-offs.

**Answer**:

**The Challenge**:
- Services need to authenticate users
- Services need to authenticate each other
- Stateless authentication required
- Token validation must be efficient

**Approach 1: API Gateway Authentication (Centralized)**

**How It Works**:
1. All external requests go through API Gateway
2. Gateway authenticates user (validates JWT/OAuth token)
3. Gateway extracts user context (user ID, roles)
4. Gateway forwards request to services with user context headers
5. Services trust gateway or validate token again

**Architecture**:
```
Client → API Gateway (Validates JWT) → Service A
                              ↓
                    Extracts: X-User-ID, X-Roles
                    Adds headers to request
```

**Pros**:
- Centralized authentication logic
- Services don't need auth code
- Single point for security policies
- Easier to update auth mechanisms
- Consistent authentication across services

**Cons**:
- Gateway becomes single point of failure
- All traffic must go through gateway
- Potential bottleneck
- Gateway must be highly available

**Approach 2: Service-Level Authentication (Distributed)**

**How It Works**:
1. Each service validates tokens independently
2. Services can be accessed directly
3. Each service has authentication logic
4. Token validation at service level

**Architecture**:
```
Client → Service A (Validates JWT) → Service B (Validates JWT)
```

**Pros**:
- No single point of failure
- Services can be accessed directly
- More flexible architecture
- Better for service-to-service calls

**Cons**:
- Auth logic duplicated across services
- More complex to maintain
- Each service must validate tokens
- Harder to update auth mechanisms

**Approach 3: Hybrid (Recommended)**

**How It Works**:
1. API Gateway authenticates external requests
2. Services validate tokens for service-to-service calls
3. Shared token validation library
4. Centralized user service for user data

**Architecture**:
```
External Client → API Gateway (Auth) → Services (Optional validation)
Internal Service → Service (Validates) → Other Services
```

**Pros**:
- Best of both worlds
- External requests secured at gateway
- Service-to-service calls secured independently
- Flexible and scalable

**Cons**:
- More complex to implement
- Need to manage both patterns

**JWT-Based Authentication Flow**:

**Step 1: User Login**:
```
Client → Auth Service (username, password)
Auth Service → Validates credentials
Auth Service → Issues JWT (with user ID, roles, expiration)
Client ← JWT
```

**Step 2: API Request**:
```
Client → API Gateway (JWT in Authorization header)
API Gateway → Validates JWT signature
API Gateway → Checks expiration
API Gateway → Extracts user context
API Gateway → Service (with X-User-ID, X-Roles headers)
Service → Processes request with user context
Service → Response
```

**Step 3: Service-to-Service**:
```
Service A → Service B (JWT or service token)
Service B → Validates token
Service B → Processes request
```

**Token Validation Strategies**:

**1. Local Validation (JWT)**:
- Service validates JWT signature locally
- No network call needed
- Fast (microseconds)
- Requires shared secret or public key
- Cannot revoke tokens easily

**2. Remote Validation (OAuth)**:
- Service calls authorization server
- Server validates token
- Can revoke tokens
- Network overhead (milliseconds)
- More secure

**3. Hybrid Validation**:
- Validate signature locally
- Check revocation list (optional, cached)
- Cache validation results
- Balance security and performance

**Service-to-Service Authentication**:

**Option 1: mTLS (Mutual TLS)**:
- Both services have certificates
- Services authenticate each other
- Encrypted communication
- Used in service mesh (Istio, Linkerd)
- Most secure

**Option 2: Service Accounts with OAuth**:
- Each service has credentials
- OAuth client credentials flow
- Centralized credential management
- Standard protocol

**Option 3: API Keys**:
- Services have API keys
- Keys in request headers
- Simple but less secure
- Good for internal services

**Recommendation**:
"For external requests, I'd use API Gateway authentication with JWT. The gateway validates tokens and adds user context. For service-to-service calls, I'd use mTLS in a service mesh for security, or OAuth client credentials for simpler setup. This hybrid approach provides security while maintaining flexibility and performance."

---

### Q7: How does one service failure not bring down the entire microservices system? Explain the mechanisms in detail.

**Answer**:

**Core Principle**: Design services to fail independently and gracefully.

**Key Mechanisms**:

**1. Multiple Instances (Redundancy)**

**How It Works**:
- Each service runs multiple instances
- Load balancer distributes traffic
- If one instance fails, others continue
- Auto-scaling adds instances as needed

**Example**:
```
Service A:
  - Instance 1 (healthy)
  - Instance 2 (healthy)
  - Instance 3 (failed) ← Removed from rotation
  
Load balancer routes to Instance 1 and 2 only.
System continues normally.
```

**Benefits**:
- No single point of failure
- Automatic failover
- High availability
- Can handle instance failures

**2. Circuit Breaker Pattern**

**How It Works**:
- Monitors service health
- Opens circuit when failures exceed threshold
- Stops calling failing service
- Returns fallback immediately
- Prevents cascading failures

**States**:
- **Closed**: Normal operation, requests flow
- **Open**: Service failing, requests fail fast with fallback
- **Half-Open**: Testing if service recovered

**Example**:
```
Service A → Service B (failing)
Circuit Breaker opens after 5 failures
Service A → Circuit Breaker → Returns cached data (fallback)
Service B failures don't affect Service A
Other services continue normally
```

**Benefits**:
- Prevents cascading failures
- Fast failure (no waiting)
- Automatic recovery testing
- Protects downstream services

**3. Health Checks & Auto-Recovery**

**How It Works**:
- Services expose health endpoints (/health)
- Load balancer checks health periodically
- Unhealthy instances removed from rotation
- Auto-restart failed services
- Auto-scaling based on health

**Health Check Types**:
- **Liveness**: Is service running? (restart if not)
- **Readiness**: Is service ready for traffic? (remove from LB if not)
- **Startup**: Is service starting? (prevent premature restarts)

**Example**:
```
Service A Instance 1: /health → 200 OK ✓ (in rotation)
Service A Instance 2: /health → 503 Service Unavailable ✗ (removed)
Service A Instance 3: /health → 200 OK ✓ (in rotation)

Load balancer routes to Instance 1 and 3 only.
Instance 2 auto-restarts.
```

**4. Database Isolation**

**How It Works**:
- Each service has its own database
- Database failure affects only one service
- No shared database bottlenecks
- Independent scaling

**Example**:
```
Service A → Database A (fails)
Service B → Database B (healthy) ← Continues normally
Service C → Database C (healthy) ← Continues normally

Only Service A is affected.
Other services continue with their own databases.
```

**Benefits**:
- Failure isolation
- Independent scaling
- Technology diversity
- No shared bottlenecks

**5. Async Communication**

**How It Works**:
- Use message queues for non-critical operations
- Fire-and-forget for notifications
- Event-driven architecture
- Services don't wait for responses

**Example**:
```
Order Service → Creates Order
Order Service → Publishes OrderCreated event (async to queue)
Payment Service → Consumes event (when available)
Inventory Service → Consumes event (when available)

If Payment Service is down:
- Order is still created
- Event stays in queue
- Payment processed when service recovers
- Other services not blocked
```

**Benefits**:
- Decouples services
- Handles service failures gracefully
- No blocking
- Better fault tolerance

**6. Timeout Pattern**

**How It Works**:
- Set timeouts for all external calls
- Fail fast if timeout exceeded
- Don't wait indefinitely
- Return error or fallback

**Example**:
```
Service A → Service B (timeout: 2 seconds)
Service B is slow (5 seconds)
Service A → Timeout after 2 seconds
Service A → Returns fallback/cached data
Service A continues normally
Other services not affected
```

**Benefits**:
- Prevents resource exhaustion
- Fast failure
- Better user experience
- Protects services

**7. Fallback Mechanisms**

**How It Works**:
- Provide alternative behavior when service fails
- Types: default value, cached data, degraded service, error response

**Example**:
```
Normal: Product details + Reviews + Recommendations
Reviews Service fails:
  → Fallback: Product details + Recommendations (cached reviews)
  → System continues with reduced functionality
```

**Benefits**:
- System continues operating
- Better user experience
- Graceful degradation
- Maintains core functionality

**8. Bulkhead Pattern**

**How It Works**:
- Isolate resources (thread pools, connection pools)
- Failure in one area doesn't affect others
- Separate resources for different operations

**Example**:
```
Service A:
  Thread Pool 1: Critical operations (10 threads)
  Thread Pool 2: Non-critical (5 threads)
  Thread Pool 3: External API calls (3 threads)

External API fails:
  → Only Thread Pool 3 affected
  → Critical operations continue normally
  → Service continues with reduced functionality
```

**Benefits**:
- Isolates failures
- Prevents resource exhaustion
- Maintains critical functionality

**Complete Failure Scenario Example**:

**Scenario**: Payment Service fails completely

**What Happens**:
1. Health checks detect Payment Service failure
2. Load balancer removes Payment Service instances
3. Circuit breakers open for Payment Service
4. Order Service continues (uses fallback)
5. Inventory Service continues normally
6. Shipping Service continues normally
7. System operates with degraded functionality

**Mitigation Applied**:
- Multiple instances (redundancy) - but all failed
- Health checks (detected failure)
- Circuit breakers (prevented cascading)
- Fallbacks (Order Service uses cached payment methods)
- Async communication (payment events queued)
- Database isolation (other services unaffected)

**Result**: System continues operating. Users can browse, add to cart, but checkout is temporarily unavailable. Payment events are queued and will be processed when service recovers.

**Best Practices**:
1. Always run multiple instances
2. Implement health checks
3. Use circuit breakers
4. Set appropriate timeouts
5. Have fallback strategies
6. Use async communication when possible
7. Isolate databases
8. Monitor everything
9. Test failure scenarios
10. Design for failure from the start

---

### Q8: Explain the Strangler Fig Pattern and how you would use it to migrate from a monolith to microservices.

**Answer**:

**The Problem**:
- Large monolithic application
- Need to migrate to microservices
- Can't do big bang rewrite
- Need low-risk approach

**Strangler Fig Pattern**:

**How It Works**:
1. **Identify Feature**: Select feature to migrate
2. **Create Microservice**: Build microservice for that feature
3. **Route New Traffic**: Route new feature requests to microservice
4. **Migrate Existing**: Gradually migrate existing functionality
5. **Replace**: Eventually replace monolith functionality
6. **Strangle**: Remove monolith code when fully replaced

**Architecture**:
```
Before:
Client → Monolith (all features)

During Migration:
Client → Router/Facade
         ├─→ Monolith (old features)
         └─→ Microservice A (new feature)
         └─→ Microservice B (migrated feature)

After:
Client → Router/Facade
         ├─→ Microservice A
         ├─→ Microservice B
         └─→ Microservice C
(Monolith removed)
```

**Migration Strategy**:

**Phase 1: Identify and Extract**:
- Identify bounded context
- Extract to microservice
- Keep data in monolith initially
- Microservice calls monolith for data

**Phase 2: Migrate Data**:
- Move data to microservice database
- Sync data between monolith and microservice
- Dual-write pattern

**Phase 3: Route Traffic**:
- Route new requests to microservice
- Gradually migrate existing traffic
- Use feature flags

**Phase 4: Complete Migration**:
- All traffic to microservice
- Remove monolith code
- Remove data sync

**Example - E-commerce Migration**:

**Step 1**: Extract Product Catalog
```
Monolith: User, Order, Payment, Product Catalog
Extract: Product Catalog → Product Service
Router: Routes /products/* to Product Service
         Routes everything else to Monolith
```

**Step 2**: Extract User Management
```
Extract: User Management → User Service
Router: Routes /users/* to User Service
         Routes /products/* to Product Service
         Routes everything else to Monolith
```

**Step 3**: Continue Migration
```
Continue extracting services
Eventually: All services extracted
Monolith: Removed
```

**Benefits**:
- **Low Risk**: Incremental migration
- **No Downtime**: System continues operating
- **Reversible**: Can rollback if needed
- **Testable**: Test each migration step
- **Gradual**: Team learns microservices gradually

**Challenges**:
- **Data Migration**: Complex data migration
- **Dual Maintenance**: Maintain both systems temporarily
- **Coordination**: Need coordination between teams
- **Testing**: Test both old and new systems

**Best Practices**:
1. Start with less critical features
2. Extract by bounded context
3. Use feature flags for gradual rollout
4. Maintain data consistency during migration
5. Monitor both systems
6. Have rollback plan
7. Document migration steps

---

### Q9: What are the key design patterns for REST API development? Explain with examples.

**Answer**:

**1. Resource-Based URLs**

**Principle**: URLs represent resources, not actions.

**Good Design**:
```
GET    /users              # List users
GET    /users/123          # Get user 123
POST   /users              # Create user
PUT    /users/123          # Update user 123
PATCH  /users/123          # Partial update
DELETE /users/123          # Delete user 123

GET    /users/123/orders   # Get orders for user 123
POST   /users/123/orders   # Create order for user 123
```

**Bad Design**:
```
GET    /getUsers
POST   /createUser
GET    /getUserOrders?userId=123
POST   /updateUser
```

**2. HTTP Methods Semantics**

**GET**: Retrieve (idempotent, safe, cacheable)
```
GET /users/123
Response: 200 OK with user data
```

**POST**: Create (not idempotent)
```
POST /users
Body: { "name": "John", "email": "john@example.com" }
Response: 201 Created with Location header
```

**PUT**: Replace (idempotent)
```
PUT /users/123
Body: { "name": "John", "email": "john@example.com" }
Response: 200 OK or 204 No Content
```

**PATCH**: Partial update (idempotent)
```
PATCH /users/123
Body: { "email": "newemail@example.com" }
Response: 200 OK
```

**DELETE**: Delete (idempotent)
```
DELETE /users/123
Response: 204 No Content
```

**3. Status Codes**

**Success (2xx)**:
- `200 OK`: Standard success
- `201 Created`: Resource created (include Location header)
- `202 Accepted`: Accepted for async processing
- `204 No Content`: Success, no response body

**Client Error (4xx)**:
- `400 Bad Request`: Invalid request syntax
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Authenticated but not authorized
- `404 Not Found`: Resource doesn't exist
- `409 Conflict`: Resource conflict (e.g., duplicate)
- `422 Unprocessable Entity`: Validation error

**Server Error (5xx)**:
- `500 Internal Server Error`: Generic server error
- `502 Bad Gateway`: Gateway received invalid response
- `503 Service Unavailable`: Service temporarily unavailable
- `504 Gateway Timeout`: Gateway timeout

**4. Pagination**

**Offset-Based** (Simple but less efficient):
```
GET /users?page=1&limit=20
Response: {
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1000,
    "total_pages": 50
  }
}
```

**Cursor-Based** (Better for large datasets):
```
GET /users?cursor=abc123&limit=20
Response: {
  "data": [...],
  "pagination": {
    "next_cursor": "xyz789",
    "has_more": true
  }
}
```

**5. Filtering and Sorting**

**Filtering**:
```
GET /users?status=active&role=admin
GET /orders?startDate=2024-01-01&endDate=2024-12-31
GET /products?category=electronics&minPrice=100&maxPrice=1000
```

**Sorting**:
```
GET /users?sort=name&order=asc
GET /users?sort=-created_at  # Descending
GET /users?sort=name,email   # Multiple fields
```

**6. API Versioning**

**URL Versioning** (Most Common):
```
GET /v1/users
GET /v2/users
```

**Header Versioning**:
```
GET /users
Headers: Accept: application/vnd.api.v1+json
```

**Query Parameter**:
```
GET /users?version=1
```

**Best Practice**: URL versioning is explicit and easy to understand.

**7. Error Response Format**

**Standard Format**:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": [
      {
        "field": "email",
        "message": "Email is required",
        "code": "REQUIRED"
      }
    ],
    "timestamp": "2024-01-15T10:30:00Z",
    "request_id": "abc-123-def",
    "path": "/users"
  }
}
```

**8. HATEOAS (Hypermedia)**

**Include Links in Responses**:
```json
{
  "id": 123,
  "name": "John",
  "email": "john@example.com",
  "_links": {
    "self": { "href": "/users/123" },
    "orders": { "href": "/users/123/orders" },
    "profile": { "href": "/users/123/profile" }
  }
}
```

**9. Idempotency**

**For POST Operations**:
```
POST /orders
Headers: Idempotency-Key: abc-123-def
Body: { ... }

First request: Creates order, returns 201
Duplicate request (same key): Returns original response, 200
```

**10. Request/Response Patterns**

**Consistent Response Structure**:
```json
{
  "data": { ... },
  "meta": {
    "timestamp": "2024-01-15T10:30:00Z",
    "version": "1.0"
  },
  "links": { ... }
}
```

**Best Practices Summary**:
1. Use resource-based URLs
2. Follow HTTP method semantics
3. Use appropriate status codes
4. Implement pagination
5. Support filtering and sorting
6. Version your APIs
7. Standardize error responses
8. Consider HATEOAS
9. Make operations idempotent
10. Maintain consistent structure

---

### Q10: Explain the difference between CQRS and Event Sourcing. When would you use each?

**Answer**:

**CQRS (Command Query Responsibility Segregation)**:

**Definition**: Separate read and write models.

**How It Works**:
- **Write Model**: Optimized for writes (normalized, ACID)
- **Read Model**: Optimized for reads (denormalized, indexed)
- **Synchronization**: Write model updates read model (via events)

**Architecture**:
```
Command → Write Model (Database)
              ↓
         Event Published
              ↓
         Read Model Updated (Projection)
              ↓
Query → Read Model (Optimized View)
```

**Example**:
```
Write: CreateOrder → Order Database (normalized)
Event: OrderCreated published
Read Model: OrderView updated (denormalized, includes customer name)
Query: GetOrders → OrderView (fast, no joins needed)
```

**Benefits**:
- Optimized for each operation
- Independent scaling
- Flexible read models
- Better performance

**Event Sourcing**:

**Definition**: Store all changes as sequence of events.

**How It Works**:
- Store events instead of current state
- Events are immutable
- Rebuild state by replaying events
- Current state is projection of events

**Architecture**:
```
Command → Event Store (Events)
              ↓
         Replay Events
              ↓
         Current State (Projection)
              ↓
Query → Current State or Events
```

**Example**:
```
Events:
1. OrderCreated (orderId: 123, customerId: 456)
2. PaymentProcessed (orderId: 123, amount: 100)
3. OrderShipped (orderId: 123, tracking: "ABC123")

Current State (rebuilt):
Order {
  id: 123,
  customerId: 456,
  status: "shipped",
  amount: 100,
  tracking: "ABC123"
}
```

**Benefits**:
- Complete audit trail
- Time travel (rebuild state at any point)
- Debugging (see what happened)
- Flexible (create new views from events)

**Key Differences**:

| Aspect | CQRS | Event Sourcing |
|--------|------|---------------|
| **Storage** | Current state | Events |
| **Read Model** | Denormalized view | Projection from events |
| **Write Model** | Normalized database | Event store |
| **History** | No history | Complete history |
| **Complexity** | Moderate | High |
| **Use Case** | Performance optimization | Audit, time travel |

**When to Use CQRS**:
- Read and write patterns differ significantly
- Need to optimize reads independently
- Complex queries on read side
- High read/write ratio
- Need flexible read models

**When to Use Event Sourcing**:
- Need complete audit trail
- Need to rebuild state at any point
- Complex domain with many events
- Need time travel capability
- Regulatory requirements for audit

**Using Both Together**:

**Common Combination**:
- Event Sourcing for write model (store events)
- CQRS for read models (projections from events)
- Best of both worlds

**Architecture**:
```
Command → Event Store (Events)
              ↓
         Events Published
              ↓
    ┌─────────┴─────────┐
    ↓                   ↓
OrderView          CustomerView
(Projection)       (Projection)
    ↓                   ↓
Query → Fast Read Models
```

**Example - E-commerce**:
```
Write: CreateOrder → OrderCreated event stored
Events: Replayed to create projections
Read Models:
  - OrderListView (for order list page)
  - OrderDetailView (for order detail page)
  - CustomerOrderHistoryView (for customer page)
Query: Fast reads from optimized views
```

**Recommendation**:
"Use CQRS when you need to optimize read and write operations independently. Use Event Sourcing when you need complete audit trail and ability to rebuild state. They work well together - Event Sourcing provides the event store, and CQRS provides optimized read models through projections."

---

## Key Takeaways

1. **Start Simple**: Begin with monolith, evolve to microservices
2. **Domain Alignment**: Services should align with business domains
3. **Loose Coupling**: Services should be independent
4. **Fault Tolerance**: Design for failures
5. **Observability**: Monitor everything
6. **API Design**: Well-designed APIs are crucial
7. **Data Management**: Each service owns its data
8. **Communication**: Prefer asynchronous when possible
9. **DevOps**: Essential for microservices success
10. **Team Structure**: Conway's Law - structure affects architecture

