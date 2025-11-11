# Principal Engineer Interview Q&A - Advanced Topics

## Table of Contents
1. [Architecture & Design](#architecture--design)
2. [Performance & Optimization](#performance--optimization)
3. [Security & Compliance](#security--compliance)
4. [DevOps & Infrastructure](#devops--infrastructure)
5. [Team Leadership & Engineering Excellence](#team-leadership--engineering-excellence)
6. [Modern Java Features](#modern-java-features)
7. [Advanced React Patterns](#advanced-react-patterns)
8. [Distributed Systems Deep Dive](#distributed-systems-deep-dive)
9. [Data Engineering & Analytics](#data-engineering--analytics)
10. [Cloud Architecture](#cloud-architecture)

---

## Architecture & Design

### Q1: How do you approach technical debt in a large codebase? What's your strategy for balancing new features with refactoring?

**Answer**:

**Technical Debt Assessment**:

**1. Categorize Technical Debt**:
- **Critical Debt**: Blocks new features, security issues
- **High Priority**: Impacts performance, maintainability
- **Medium Priority**: Code quality issues, minor architectural problems
- **Low Priority**: Nice-to-have improvements

**2. Quantify Impact**:
```
Technical Debt Score = (Bug Count × Severity) + (Development Velocity Impact) + (Maintenance Cost)
```

**3. Create Technical Debt Backlog**:
- Track debt items like regular features
- Estimate effort and impact
- Prioritize based on ROI

**Strategy Framework**:

**The 80/20 Rule**:
- 80% feature development
- 20% technical debt/refactoring
- Adjust based on project phase

**Boy Scout Rule**:
- Leave code better than you found it
- Small improvements with each change
- Compounds over time

**Opportunistic Refactoring**:
- Refactor when touching code for features
- Don't separate refactoring from features
- Build improvement into daily work

**Scheduled Refactoring Sprints**:
- Dedicate specific sprints to debt
- Every 3-4 feature sprints
- Clear goals and metrics

**Example Implementation**:
```
Quarter Planning:
- Sprint 1-3: Features (80% velocity)
- Sprint 4: Technical Debt Sprint (100% debt focus)
- Repeat cycle

Metrics to Track:
- Code coverage: Improve from 60% to 80%
- Build time: Reduce from 20min to 10min
- Deployment frequency: Increase from weekly to daily
- Bug rate: Decrease by 30%
```

**Stakeholder Communication**:
```
Frame as Business Value:
❌ "We need to refactor the authentication system"
✅ "We can reduce login failures by 50% and speed up new user features by 40% by improving our authentication system"

Show Cost of Inaction:
- "Current bugs cost 20 hours/sprint in fixes"
- "Refactoring will reduce this to 5 hours/sprint"
- "ROI: 15 hours × 50 weeks = 750 hours saved/year"
```

**Best Practices**:
1. **Make Debt Visible**: Track in backlog, discuss in planning
2. **Link to Business Impact**: Show how debt affects users
3. **Celebrate Improvements**: Recognize debt reduction
4. **Prevent New Debt**: Code reviews, standards, automated checks
5. **Regular Assessments**: Quarterly tech debt reviews

---

### Q2: Explain Domain-Driven Design (DDD). How would you apply it to a complex e-commerce system?

**Answer**:

**Domain-Driven Design Core Concepts**:

**1. Ubiquitous Language**:
- Shared vocabulary between developers and domain experts
- Same terms in code and business discussions
- Reduces misunderstandings

**2. Bounded Contexts**:
- Clear boundaries around models
- Each context has own model
- Context map shows relationships

**3. Entities**:
- Objects with identity (ID)
- Identity persists through lifecycle
- Example: Order, Customer, Product

**4. Value Objects**:
- No identity, defined by attributes
- Immutable
- Example: Address, Money, DateRange

**5. Aggregates**:
- Cluster of entities/value objects
- One aggregate root
- Consistency boundary
- Transaction boundary

**6. Domain Events**:
- Something significant that happened
- Published by aggregates
- Triggers side effects

**E-commerce System DDD Design**:

**Identify Bounded Contexts**:
```
1. Catalog Context
   - Product browsing
   - Search
   - Categories

2. Ordering Context
   - Shopping cart
   - Checkout
   - Order management

3. Payment Context
   - Payment processing
   - Refunds
   - Payment methods

4. Inventory Context
   - Stock management
   - Reservations
   - Warehouse management

5. Shipping Context
   - Shipment tracking
   - Delivery management
   - Address validation
```

**Example Aggregate: Order**

```java
// Aggregate Root
public class Order {
    private OrderId orderId;
    private CustomerId customerId;
    private List items;
    private OrderStatus status;
    private Money totalAmount;
    private ShippingAddress shippingAddress;
    
    // Domain logic encapsulated
    public void addItem(Product product, int quantity) {
        if (status != OrderStatus.DRAFT) {
            throw new IllegalStateException("Cannot add items to non-draft order");
        }
        
        OrderItem item = new OrderItem(product, quantity);
        items.add(item);
        recalculateTotal();
        
        // Domain event
        DomainEvents.publish(new ItemAddedToOrder(orderId, item));
    }
    
    public void placeOrder() {
        if (items.isEmpty()) {
            throw new IllegalStateException("Cannot place empty order");
        }
        
        // Business rule: minimum order amount
        if (totalAmount.lessThan(Money.of(10.00))) {
            throw new IllegalStateException("Order must be at least $10");
        }
        
        status = OrderStatus.PLACED;
        
        // Domain event
        DomainEvents.publish(new OrderPlaced(orderId, customerId, totalAmount));
    }
    
    private void recalculateTotal() {
        totalAmount = items.stream()
            .map(OrderItem::getSubtotal)
            .reduce(Money.ZERO, Money::add);
    }
}

// Value Object
public class Money {
    private final BigDecimal amount;
    private final Currency currency;
    
    // Immutable, no setters
    public Money add(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("Cannot add different currencies");
        }
        return new Money(amount.add(other.amount), currency);
    }
    
    public boolean lessThan(Money other) {
        return amount.compareTo(other.amount) < 0;
    }
}

// Entity within Aggregate
public class OrderItem {
    private OrderItemId id;
    private ProductId productId;
    private String productName;
    private Money unitPrice;
    private int quantity;
    
    public Money getSubtotal() {
        return unitPrice.multiply(quantity);
    }
}
```

**Domain Events**:
```java
// Event published when order is placed
public class OrderPlaced implements DomainEvent {
    private final OrderId orderId;
    private final CustomerId customerId;
    private final Money totalAmount;
    private final Instant occurredAt;
    
    // Immutable event
}

// Event Handler in Inventory Context
@EventHandler
public void handleOrderPlaced(OrderPlaced event) {
    // Reserve inventory
    inventoryService.reserveItems(event.getOrderId());
}

// Event Handler in Payment Context
@EventHandler
public void handleOrderPlaced(OrderPlaced event) {
    // Process payment
    paymentService.processPayment(event.getCustomerId(), event.getTotalAmount());
}
```

**Context Mapping**:
```
Catalog Context <---> Ordering Context
- Shared Kernel: Product information
- Anti-Corruption Layer: Translate product models

Ordering Context ---> Payment Context
- Published Language: Domain events (OrderPlaced)
- Conformist: Payment follows order events

Ordering Context <---> Inventory Context
- Customer/Supplier: Ordering requests, Inventory confirms
- Domain Events: OrderPlaced, InventoryReserved
```

**Repository Pattern (DDD)**:
```java
// Repository interface in domain layer
public interface OrderRepository {
    Order findById(OrderId orderId);
    void save(Order order);
    List findByCustomer(CustomerId customerId);
}

// Implementation in infrastructure layer
@Repository
public class JpaOrderRepository implements OrderRepository {
    @PersistenceContext
    private EntityManager entityManager;
    
    @Override
    public Order findById(OrderId orderId) {
        OrderEntity entity = entityManager.find(OrderEntity.class, orderId.getValue());
        return OrderMapper.toDomain(entity);
    }
    
    @Override
    public void save(Order order) {
        OrderEntity entity = OrderMapper.toEntity(order);
        entityManager.persist(entity);
    }
}
```

**Benefits of DDD for E-commerce**:
1. **Clear Boundaries**: Each context independently deployable
2. **Business Focus**: Code reflects business logic
3. **Maintainability**: Changes localized to contexts
4. **Team Organization**: Teams own contexts
5. **Scalability**: Scale contexts independently

**Implementation Steps**:
1. **Event Storming**: Collaborate with domain experts
2. **Identify Contexts**: Find natural boundaries
3. **Define Ubiquitous Language**: Create shared vocabulary
4. **Model Aggregates**: Design consistency boundaries
5. **Implement Domain Events**: Enable loose coupling
6. **Iterate**: Refine based on insights

---

### Q3: How do you design APIs for scalability and maintainability? What are the best practices?

**Answer**:

**API Design Principles**:

**1. RESTful Design Best Practices**:

**Resource-Based URLs**:
```
✅ Good:
GET    /users                    # List users
GET    /users/{id}               # Get specific user
POST   /users                    # Create user
PUT    /users/{id}               # Update user
DELETE /users/{id}               # Delete user
GET    /users/{id}/orders        # User's orders

❌ Bad:
GET    /getUsers
POST   /createUser
GET    /getUserOrders?userId={id}
```

**HTTP Status Codes**:
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping("/{id}")
    public ResponseEntity getUser(@PathVariable Long id) {
        return userService.findById(id)
            .map(user -> ResponseEntity.ok(user))           // 200 OK
            .orElse(ResponseEntity.notFound().build());     // 404 Not Found
    }
    
    @PostMapping
    public ResponseEntity createUser(@Valid @RequestBody CreateUserRequest request) {
        UserDTO user = userService.create(request);
        URI location = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(user.getId())
            .toUri();
        return ResponseEntity.created(location).body(user); // 201 Created with Location header
    }
    
    @PutMapping("/{id}")
    public ResponseEntity updateUser(
            @PathVariable Long id, 
            @Valid @RequestBody UpdateUserRequest request) {
        try {
            UserDTO user = userService.update(id, request);
            return ResponseEntity.ok(user);                 // 200 OK
        } catch (NotFoundException e) {
            return ResponseEntity.notFound().build();       // 404 Not Found
        } catch (ValidationException e) {
            return ResponseEntity.badRequest().build();     // 400 Bad Request
        }
    }
}
```

**2. API Versioning**:

**URL Versioning (Recommended)**:
```java
@RestController
@RequestMapping("/api/v1/users")
public class UserControllerV1 {
    // Version 1 implementation
}

@RestController
@RequestMapping("/api/v2/users")
public class UserControllerV2 {
    // Version 2 implementation with breaking changes
}
```

**Header Versioning**:
```java
@GetMapping(value = "/users", headers = "API-Version=1")
public List getUsersV1() {
    // Version 1
}

@GetMapping(value = "/users", headers = "API-Version=2")
public List getUsersV2() {
    // Version 2
}
```

**3. Pagination and Filtering**:

```java
@GetMapping("/users")
public ResponseEntity<PagedResponse> getUsers(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "20") int size,
        @RequestParam(defaultValue = "id,asc") String sort,
        @RequestParam(required = false) String name,
        @RequestParam(required = false) String email) {
    
    Pageable pageable = PageRequest.of(page, size, Sort.by(parseSortParam(sort)));
    Page users = userService.findAll(name, email, pageable);
    
    PagedResponse response = PagedResponse.builder()
        .content(users.getContent())
        .page(users.getNumber())
        .size(users.getSize())
        .totalElements(users.getTotalElements())
        .totalPages(users.getTotalPages())
        .last(users.isLast())
        .build();
    
    return ResponseEntity.ok(response);
}

// Response structure
{
  "content": [ ... ],
  "page": 0,
  "size": 20,
  "totalElements": 100,
  "totalPages": 5,
  "last": false
}
```

**4. Error Handling**:

**Standardized Error Response**:
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(NotFoundException.class)
    public ResponseEntity handleNotFound(NotFoundException ex) {
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .status(404)
            .error("Not Found")
            .message(ex.getMessage())
            .path(getCurrentRequest().getRequestURI())
            .build();
        return ResponseEntity.status(404).body(error);
    }
    
    @ExceptionHandler(ValidationException.class)
    public ResponseEntity handleValidation(ValidationException ex) {
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .status(400)
            .error("Validation Error")
            .message(ex.getMessage())
            .errors(ex.getValidationErrors())
            .path(getCurrentRequest().getRequestURI())
            .build();
        return ResponseEntity.badRequest().body(error);
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity handleGeneric(Exception ex) {
        // Log error but don't expose internal details
        logger.error("Unexpected error", ex);
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .status(500)
            .error("Internal Server Error")
            .message("An unexpected error occurred")
            .path(getCurrentRequest().getRequestURI())
            .build();
        return ResponseEntity.status(500).body(error);
    }
}

// Error response structure
{
  "timestamp": "2024-01-15T10:30:00Z",
  "status": 400,
  "error": "Validation Error",
  "message": "Invalid input",
  "errors": [
    {
      "field": "email",
      "message": "Email is required"
    }
  ],
  "path": "/api/v1/users"
}
```

**5. Rate Limiting**:

```java
@Component
public class RateLimitInterceptor implements HandlerInterceptor {
    
    private final RateLimiter rateLimiter;
    
    @Override
    public boolean preHandle(HttpServletRequest request, 
                            HttpServletResponse response, 
                            Object handler) {
        String clientId = extractClientId(request);
        String endpoint = request.getRequestURI();
        String key = clientId + ":" + endpoint;
        
        if (!rateLimiter.tryAcquire(key, 100, Duration.ofMinutes(1))) {
            response.setStatus(429); // Too Many Requests
            response.setHeader("X-RateLimit-Limit", "100");
            response.setHeader("X-RateLimit-Remaining", "0");
            response.setHeader("X-RateLimit-Reset", 
                String.valueOf(System.currentTimeMillis() + 60000));
            return false;
        }
        
        return true;
    }
}
```

**6. Caching Headers**:

```java
@GetMapping("/users/{id}")
public ResponseEntity getUser(@PathVariable Long id) {
    UserDTO user = userService.findById(id)
        .orElseThrow(() -> new NotFoundException("User not found"));
    
    return ResponseEntity.ok()
        .cacheControl(CacheControl.maxAge(1, TimeUnit.HOURS))
        .eTag(calculateETag(user))
        .body(user);
}

@GetMapping("/users/{id}")
public ResponseEntity getUserWithETag(
        @PathVariable Long id,
        @RequestHeader(value = "If-None-Match", required = false) String ifNoneMatch) {
    
    UserDTO user = userService.findById(id)
        .orElseThrow(() -> new NotFoundException("User not found"));
    
    String etag = calculateETag(user);
    
    if (etag.equals(ifNoneMatch)) {
        return ResponseEntity.status(304).build(); // Not Modified
    }
    
    return ResponseEntity.ok()
        .eTag(etag)
        .body(user);
}
```

**7. API Documentation (OpenAPI/Swagger)**:

```java
@Configuration
@OpenAPIDefinition(
    info = @Info(
        title = "User Management API",
        version = "1.0",
        description = "API for managing users",
        contact = @Contact(name = "API Team", email = "api@example.com")
    )
)
public class OpenAPIConfig {
    
    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .components(new Components()
                .addSecuritySchemes("bearer-jwt",
                    new SecurityScheme()
                        .type(SecurityScheme.Type.HTTP)
                        .scheme("bearer")
                        .bearerFormat("JWT")));
    }
}

@RestController
@RequestMapping("/api/v1/users")
@Tag(name = "Users", description = "User management APIs")
public class UserController {
    
    @Operation(
        summary = "Get user by ID",
        description = "Returns a single user",
        responses = {
            @ApiResponse(responseCode = "200", description = "Success"),
            @ApiResponse(responseCode = "404", description = "User not found")
        }
    )
    @GetMapping("/{id}")
    public ResponseEntity getUser(@PathVariable Long id) {
        // Implementation
    }
}
```

**8. Idempotency**:

```java
@PostMapping("/orders")
public ResponseEntity createOrder(
        @RequestHeader("Idempotency-Key") String idempotencyKey,
        @Valid @RequestBody CreateOrderRequest request) {
    
    // Check if request with this key already processed
    Optional existing = orderService.findByIdempotencyKey(idempotencyKey);
    if (existing.isPresent()) {
        return ResponseEntity.ok(existing.get()); // Return existing result
    }
    
    // Process new request
    OrderDTO order = orderService.create(request, idempotencyKey);
    return ResponseEntity.status(201).body(order);
}
```

**9. HATEOAS (Hypermedia)**:

```java
@GetMapping("/users/{id}")
public ResponseEntity<EntityModel> getUser(@PathVariable Long id) {
    UserDTO user = userService.findById(id)
        .orElseThrow(() -> new NotFoundException("User not found"));
    
    EntityModel resource = EntityModel.of(user);
    
    // Add links
    resource.add(linkTo(methodOn(UserController.class).getUser(id)).withSelfRel());
    resource.add(linkTo(methodOn(OrderController.class).getUserOrders(id)).withRel("orders"));
    resource.add(linkTo(methodOn(UserController.class).updateUser(id, null)).withRel("update"));
    resource.add(linkTo(methodOn(UserController.class).deleteUser(id)).withRel("delete"));
    
    return ResponseEntity.ok(resource);
}

// Response
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "_links": {
    "self": { "href": "/api/v1/users/1" },
    "orders": { "href": "/api/v1/users/1/orders" },
    "update": { "href": "/api/v1/users/1" },
    "delete": { "href": "/api/v1/users/1" }
  }
}
```

**10. API Security**:

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable() // Disable for stateless REST APIs
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/v1/public/**").permitAll()
                .requestMatchers("/api/v1/admin/**").hasRole("ADMIN")
                .requestMatchers(HttpMethod.POST, "/api/v1/users").hasAnyRole("ADMIN", "USER")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer()
                .jwt();
        
        return http.build();
    }
}
```

**Best Practices Summary**:
1. **RESTful Principles**: Follow REST conventions
2. **Version APIs**: Use URL versioning from start
3. **Pagination**: Always paginate large lists
4. **Error Handling**: Standardized error responses
5. **Rate Limiting**: Protect against abuse
6. **Caching**: Use HTTP caching headers
7. **Documentation**: Always document APIs
8. **Idempotency**: Support idempotent operations
9. **Security**: Authentication, authorization, HTTPS
10. **Monitoring**: Log and track API usage

---

## Performance & Optimization

### Q4: How do you identify and resolve performance bottlenecks in a distributed system?

**Answer**:

**Performance Investigation Framework**:

**1. Establish Baseline Metrics**:
```
Application Metrics:
- Response time (p50, p95, p99)
- Throughput (requests/second)
- Error rate
- CPU/Memory usage

Infrastructure Metrics:
- Network latency
- Database query time
- Cache hit rate
- Queue depth
```

**2. Monitoring Stack**:
```
APM (Application Performance Monitoring):
- New Relic, Datadog, Dynatrace
- Track request flows across services
- Identify slow transactions

Distributed Tracing:
- Zipkin, Jaeger, AWS X-Ray
- End-to-end request tracking
- Visualize service dependencies

Logging:
- Structured logging (JSON)
- Centralized (ELK, Splunk)
- Correlation IDs across services

Metrics:
- Prometheus + Grafana
- Time-series data
- Real-time dashboards
```

**3. Bottleneck Identification Process**:

**Step 1: Top-Down Analysis**
```
1. Start with user-facing metrics
   - Which endpoints are slowest?
   - What's the p99 latency?

2. Trace slowest requests
   - Use distributed tracing
   - Identify bottleneck service

3. Drill down into service
   - Database queries?
   - External API calls?
   - CPU-intensive operations?
```

**Example Investigation**:
```
Symptom: Checkout endpoint takes 5 seconds (p99)

Step 1: Distributed Trace Analysis
Request Flow:
- API Gateway: 50ms
- Order Service: 200ms
- Payment Service: 4500ms ← BOTTLENECK
- Inventory Service: 150ms

Step 2: Payment Service Analysis
- Database query: 100ms
- External payment gateway call: 4300ms ← ISSUE
- Response processing: 100ms

Step 3: Root Cause
- No timeout on payment gateway
- Retry logic without backoff
- Sequential retries (5 attempts × 800ms)
```

**Common Bottlenecks and Solutions**:

**1. Database Performance**:

**Problem**: Slow queries
```sql
-- Slow query (no index)
EXPLAIN ANALYZE
SELECT * FROM orders 
WHERE customer_id = 123 
AND created_at > '2024-01-01'
ORDER BY created_at DESC;

-- Result: Seq Scan on orders (cost=0.00..50000.00 rows=1000000)
-- Execution time: 2000ms
```

**Solution**:
```sql
-- Add composite index
CREATE INDEX idx_orders_customer_date 
ON orders(customer_id, created_at DESC);

-- Optimized query
EXPLAIN ANALYZE
SELECT * FROM orders 
WHERE customer_id = 123 
AND created_at > '2024-01-01'
ORDER BY created_at DESC;

-- Result: Index Scan using idx_orders_customer_date (cost=0.00..100.00 rows=100)
-- Execution time: 10ms
```

**Problem**: N+1 queries
```java
// Bad: N+1 queries
List orders = orderRepository.findAll(); // 1 query
for (Order order : orders) {
    Customer customer = customerRepository.findById(order.getCustomerId()); // N queries
    order.setCustomer(customer);
}
// Total: 1 + N queries

// Good: Single query with join
@Query("SELECT o FROM Order o JOIN FETCH o.customer")
List findAllWithCustomers();
// Total: 1 query
```

**2. Network Latency**:

**Problem**: Sequential service calls
```java
// Bad: Sequential (600ms total)
User user = userService.getUser(userId);        // 200ms
Orders orders = orderService.getOrders(userId); // 200ms
Profile profile = profileService.getProfile(userId); // 200ms
```

**Solution**: Parallel calls
```java
// Good: Parallel (200ms total)
CompletableFuture userFuture = 
    CompletableFuture.supplyAsync(() -> userService.getUser(userId));
    
CompletableFuture ordersFuture = 
    CompletableFuture.supplyAsync(() -> orderService.getOrders(userId));
    
CompletableFuture profileFuture = 
    CompletableFuture.supplyAsync(() -> profileService.getProfile(userId));

CompletableFuture.allOf(userFuture, ordersFuture, profileFuture).join();

User user = userFuture.get();
Orders orders = ordersFuture.get();
Profile profile = profileFuture.get();
```

**3. Caching Issues**:

**Problem**: Cache misses
```
Metrics:
- Cache hit rate: 30%
- Database queries: 7000/minute
- Response time: 500ms avg
```

**Solution**: Optimize caching strategy
```java
@Configuration
@EnableCaching
public class CacheConfig {
    
    @Bean
    public CacheManager cacheManager() {
        RedisCacheManager.Builder builder = RedisCacheManager
            .builder(redisConnectionFactory())
            .cacheDefaults(RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofHours(1))
                .serializeValuesWith(SerializationPair.fromSerializer(
                    new GenericJackson2JsonRedisSerializer())));
        
        return builder.build();
    }
}

@Service
public class ProductService {
    
    @Cacheable(value = "products", key = "#id", unless = "#result == null")
    public Product getProduct(String id) {
        return productRepository.findById(id)
            .orElseThrow(() -> new NotFoundException("Product not found"));
    }
    
    // Cache warming on startup
    @PostConstruct
    public void warmCache() {
        List popularProducts = productRepository.findTop100ByOrderBySalesDesc();
        popularProducts.forEach(p -> {
            cacheManager.getCache("products").put(p.getId(), p);
        });
    }
    
    @CacheEvict(value = "products", key = "#id")
    public void updateProduct(String id, Product product) {
        productRepository.save(product);
    }
}
```

**4. Memory Issues**:

**Problem**: Memory leaks, GC pauses
```
Symptoms:
- GC pauses increasing
- Heap usage growing
- OutOfMemoryError

Metrics:
- Heap usage: 95% (consistently high)
- GC time: 30% (too high)
- GC pauses: 2 seconds (too long)
```

**Solution**: Profiling and optimization
```java
// Identify with profiler (JProfiler, YourKit, Java Flight Recorder)

// Bad: Creating many objects
public List processData(List dataList) {
    List results = new ArrayList<>();
    for (Data data : dataList) {
        // Creates new String objects
        results.add(new String("prefix" + data.getValue() + "suffix"));
    }
    return results;
}

// Good: Reuse StringBuilder, avoid unnecessary objects
public List processData(List dataList) {
    List results = new ArrayList<>(dataList.size());
    StringBuilder sb = new StringBuilder(50); // Pre-allocate capacity
    
    for (Data data : dataList) {
        sb.setLength(0); // Clear for reuse
        sb.append("prefix").append(data.getValue()).append("suffix");
        results.add(sb.toString());
    }
    return results;
}
```

**5. Thread Pool Exhaustion**:

**Problem**: Thread pool maxed out
```
Symptoms:
- Request queue growing
- Response time increasing
- Rejected requests

Metrics:
- Active threads: 100/100 (maxed out)
- Queue size: 5000 (growing)
- Rejected tasks: 500/minute
```

**Solution**: Tune thread pools
```java
@Configuration
public class AsyncConfig implements AsyncConfigurer {
    
    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        
        // Core threads (always alive)
        executor.setCorePoolSize(50);
        
        // Maximum threads
        executor.setMaxPoolSize(200);
        
        // Queue capacity
        executor.setQueueCapacity(1000);
        
        // Thread name prefix
        executor.setThreadNamePrefix("async-");
        
        // Rejection policy
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        
        // Graceful shutdown
        executor.setWaitForTasksToCompleteOnShutdown(true);
        executor.setAwaitTerminationSeconds(60);
        
        executor.initialize();
        return executor;
    }
}
```

**Performance Optimization Toolkit**:

**Profiling Tools**:
- **JProfiler, YourKit**: CPU and memory profiling
- **VisualVM**:
