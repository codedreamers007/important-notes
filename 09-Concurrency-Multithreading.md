# Concurrency & Multithreading - Comprehensive Guide

## Table of Contents
1. [Concurrency Fundamentals](#concurrency-fundamentals)
2. [Threads and Processes](#threads-and-processes)
3. [Synchronization Mechanisms](#synchronization-mechanisms)
4. [Java Concurrency API](#java-concurrency-api)
5. [Common Concurrency Problems](#common-concurrency-problems)
6. [Best Practices](#best-practices)
7. [Common Interview Questions](#common-interview-questions)

---

## Concurrency Fundamentals

### What is Concurrency?

**Definition**: Concurrency is the ability of a program to execute multiple tasks simultaneously or in overlapping time periods.

**Key Concepts**:
- **Parallelism**: Tasks executing at the same time (multiple CPUs)
- **Concurrency**: Tasks making progress in overlapping time (may not be simultaneous)
- **Thread Safety**: Code that works correctly when accessed by multiple threads

### Why Concurrency?

**Benefits**:
- **Better Resource Utilization**: Use multiple CPU cores
- **Improved Responsiveness**: UI remains responsive while background tasks run
- **Higher Throughput**: Process more requests simultaneously
- **Better Performance**: Take advantage of multi-core processors

**Challenges**:
- **Race Conditions**: Unpredictable behavior due to timing
- **Deadlocks**: Threads waiting for each other indefinitely
- **Data Corruption**: Shared data modified incorrectly
- **Complexity**: Harder to debug and test

---

## Threads and Processes

### Process vs Thread

**Process**:
- Independent execution unit
- Has its own memory space
- Isolated from other processes
- Heavyweight (more memory, slower creation)
- Inter-process communication (IPC) needed

**Thread**:
- Lightweight execution unit within process
- Shares memory space with other threads
- Faster to create and switch
- Direct access to shared memory
- Need synchronization for shared data

### Thread Lifecycle

**States**:
1. **NEW**: Thread created but not started
2. **RUNNABLE**: Thread ready to run (may be running or waiting for CPU)
3. **BLOCKED**: Thread waiting for monitor lock
4. **WAITING**: Thread waiting indefinitely for another thread
5. **TIMED_WAITING**: Thread waiting for specified time
6. **TERMINATED**: Thread completed execution

**State Transitions**:
```
NEW → start() → RUNNABLE → run() completes → TERMINATED
RUNNABLE → wait() → WAITING → notify() → RUNNABLE
RUNNABLE → sleep(time) → TIMED_WAITING → timeout → RUNNABLE
RUNNABLE → synchronized → BLOCKED → lock acquired → RUNNABLE
```

---

## Synchronization Mechanisms

### 1. Synchronized Keyword

**Purpose**: Ensure only one thread executes synchronized block at a time.

**Usage**:
```java
// Synchronized method
public synchronized void increment() {
    count++;
}

// Synchronized block
public void increment() {
    synchronized(this) {
        count++;
    }
}

// Synchronized on class
public static synchronized void method() {
    // Synchronized on class object
}
```

**How It Works**:
- Acquires lock on object/class
- Only one thread can hold lock
- Other threads wait until lock released
- Lock automatically released when block exits

**Lock Types**:
- **Object Lock**: `synchronized(this)` or `synchronized(obj)`
- **Class Lock**: `synchronized(ClassName.class)` or `static synchronized`

### 2. Volatile Keyword

**Purpose**: Ensure variable visibility across threads.

**Problem**: Without volatile, changes may not be visible to other threads due to CPU cache.

**Solution**:
```java
private volatile boolean flag = true;

public void stop() {
    flag = false; // Visible to all threads immediately
}
```

**What Volatile Does**:
- Ensures reads/writes go to main memory (not CPU cache)
- Prevents compiler optimizations
- Provides visibility guarantee (not atomicity)

**Limitations**:
- Doesn't provide atomicity for compound operations
- Not suitable for operations like `count++` (read-modify-write)

### 3. Locks (java.util.concurrent.locks)

**ReentrantLock**:
```java
private final ReentrantLock lock = new ReentrantLock();

public void method() {
    lock.lock();
    try {
        // Critical section
    } finally {
        lock.unlock(); // Always unlock in finally
    }
}
```

**Advantages over synchronized**:
- Can try to acquire lock (tryLock)
- Can interrupt while waiting (lockInterruptibly)
- Fair locking (FIFO)
- More flexible

**ReadWriteLock**:
```java
private final ReadWriteLock lock = new ReentrantReadWriteLock();
private final Lock readLock = lock.readLock();
private final Lock writeLock = lock.writeLock();

// Multiple readers allowed
public void read() {
    readLock.lock();
    try {
        // Read operation
    } finally {
        readLock.unlock();
    }
}

// Only one writer
public void write() {
    writeLock.lock();
    try {
        // Write operation
    } finally {
        writeLock.unlock();
    }
}
```

**Benefits**: Allows multiple readers simultaneously, better performance for read-heavy workloads.

### 4. Atomic Classes

**Purpose**: Thread-safe operations without synchronization.

**AtomicInteger**:
```java
private AtomicInteger count = new AtomicInteger(0);

public void increment() {
    count.incrementAndGet(); // Atomic operation
}

public int get() {
    return count.get();
}
```

**Available Atomic Classes**:
- `AtomicInteger`, `AtomicLong`, `AtomicBoolean`
- `AtomicReference<T>`: For object references
- `AtomicIntegerArray`, `AtomicLongArray`
- `AtomicReferenceArray<T>`

**How They Work**:
- Use Compare-And-Swap (CAS) operations
- Lock-free (no blocking)
- Better performance than synchronized
- Hardware-level atomic operations

**Example**:
```java
// Thread-safe counter without synchronization
public class Counter {
    private AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet();
    }
    
    public int get() {
        return count.get();
    }
}
```

### 5. Semaphore

**Purpose**: Control access to resource with limited capacity.

**Example**:
```java
// Allow 3 threads to access resource simultaneously
Semaphore semaphore = new Semaphore(3);

public void accessResource() {
    try {
        semaphore.acquire(); // Wait for permit
        // Access resource (max 3 threads)
    } finally {
        semaphore.release(); // Release permit
    }
}
```

**Use Cases**:
- Connection pooling
- Rate limiting
- Resource access control

### 6. CountDownLatch

**Purpose**: Wait for multiple threads to complete.

**Example**:
```java
CountDownLatch latch = new CountDownLatch(3);

// Thread 1, 2, 3
public void doWork() {
    // Do work
    latch.countDown(); // Decrement count
}

// Main thread
latch.await(); // Wait until count reaches 0
// All threads completed
```

**Use Cases**:
- Wait for initialization
- Wait for multiple tasks to complete

### 7. CyclicBarrier

**Purpose**: Synchronize threads at a barrier point.

**Example**:
```java
CyclicBarrier barrier = new CyclicBarrier(3, () -> {
    // Action when all threads reach barrier
    System.out.println("All threads reached barrier");
});

// Each thread
public void doWork() {
    // Phase 1
    barrier.await(); // Wait for all threads
    // Phase 2 (all threads start together)
}
```

**Use Cases**:
- Parallel algorithms
- Multi-phase processing

### 8. Exchanger

**Purpose**: Exchange data between two threads.

**Example**:
```java
Exchanger<String> exchanger = new Exchanger<>();

// Thread 1
String data1 = exchanger.exchange("Data from Thread 1");
// Receives "Data from Thread 2"

// Thread 2
String data2 = exchanger.exchange("Data from Thread 2");
// Receives "Data from Thread 1"
```

---

## Java Concurrency API

### Executor Framework

**Purpose**: Manage thread creation and execution.

**ExecutorService**:
```java
ExecutorService executor = Executors.newFixedThreadPool(10);

// Submit task
Future<String> future = executor.submit(() -> {
    return "Result";
});

// Get result
String result = future.get(); // Blocks until result available

// Shutdown
executor.shutdown();
executor.awaitTermination(60, TimeUnit.SECONDS);
```

**Thread Pool Types**:
- **FixedThreadPool**: Fixed number of threads
- **CachedThreadPool**: Creates threads as needed
- **SingleThreadExecutor**: Single worker thread
- **ScheduledThreadPool**: For scheduled tasks

**Custom Thread Pool**:
```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    5,  // Core pool size
    10, // Maximum pool size
    60L, TimeUnit.SECONDS, // Keep alive time
    new LinkedBlockingQueue<>() // Work queue
);
```

### Future and CompletableFuture

**Future**:
```java
ExecutorService executor = Executors.newFixedThreadPool(10);
Future<String> future = executor.submit(() -> {
    Thread.sleep(1000);
    return "Result";
});

// Check if done
if (future.isDone()) {
    String result = future.get();
}

// Get with timeout
String result = future.get(5, TimeUnit.SECONDS);
```

**CompletableFuture** (Java 8+):
```java
// Async execution
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Result";
});

// Chain operations
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenApply(String::toUpperCase);

// Combine futures
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");
CompletableFuture<String> combined = future1.thenCombine(future2, (a, b) -> a + " " + b);

// Handle errors
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> {
        if (error) throw new RuntimeException();
        return "Result";
    })
    .exceptionally(ex -> "Error: " + ex.getMessage());
```

### BlockingQueue

**Purpose**: Thread-safe queue for producer-consumer pattern.

**Types**:
- `ArrayBlockingQueue`: Bounded array-based queue
- `LinkedBlockingQueue`: Unbounded or bounded linked queue
- `PriorityBlockingQueue`: Priority-ordered queue
- `SynchronousQueue`: No capacity, direct handoff

**Example**:
```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>();

// Producer
queue.put("Item"); // Blocks if queue full

// Consumer
String item = queue.take(); // Blocks if queue empty
```

---

## Common Concurrency Problems

### 1. Race Condition

**Problem**: Outcome depends on timing of thread execution.

**Example**:
```java
// Unsafe
private int count = 0;

public void increment() {
    count++; // Not atomic: read, increment, write
}
```

**Solution**:
```java
// Safe with synchronization
private int count = 0;

public synchronized void increment() {
    count++;
}

// Or with AtomicInteger
private AtomicInteger count = new AtomicInteger(0);

public void increment() {
    count.incrementAndGet();
}
```

### 2. Deadlock

**Problem**: Two or more threads waiting for each other indefinitely.

**Example**:
```java
// Thread 1
synchronized(lockA) {
    synchronized(lockB) {
        // ...
    }
}

// Thread 2
synchronized(lockB) {
    synchronized(lockA) {
        // ...
    }
}
```

**Prevention**:
- **Lock Ordering**: Always acquire locks in same order
- **Timeout**: Use tryLock with timeout
- **Avoid Nested Locks**: Minimize lock nesting
- **Lock-Free**: Use atomic classes when possible

**Solution**:
```java
// Always acquire locks in same order
synchronized(lockA) {
    synchronized(lockB) {
        // ...
    }
}
```

### 3. Livelock

**Problem**: Threads keep responding to each other but make no progress.

**Example**: Two threads trying to pass through narrow corridor, both step aside.

**Solution**: Introduce randomness or backoff.

### 4. Starvation

**Problem**: Thread waits indefinitely for resource.

**Causes**:
- Low priority threads
- Unfair locking
- Greedy threads

**Solution**: Use fair locks, proper thread priorities.

### 5. Memory Visibility

**Problem**: Changes made by one thread not visible to others.

**Example**:
```java
// Without volatile
private boolean flag = true;

// Thread 1
flag = false;

// Thread 2 (may not see change due to CPU cache)
while (flag) {
    // Infinite loop
}
```

**Solution**:
```java
private volatile boolean flag = true;
```

---

## Best Practices

1. **Prefer Immutable Objects**: Immutable objects are thread-safe
2. **Use Thread-Safe Collections**: ConcurrentHashMap, CopyOnWriteArrayList
3. **Minimize Shared State**: Reduce shared mutable state
4. **Use Atomic Classes**: For simple operations
5. **Proper Lock Ordering**: Prevent deadlocks
6. **Always Unlock in Finally**: Prevent resource leaks
7. **Use Executor Framework**: Don't create threads manually
8. **Avoid Premature Optimization**: Profile before optimizing
9. **Test Concurrent Code**: Use stress testing
10. **Document Thread Safety**: Document thread-safety guarantees

---

## Common Interview Questions

### Q1: Explain the difference between synchronized and volatile keywords.

**Answer**:

**Synchronized**:
- Provides both **mutual exclusion** and **visibility**
- Only one thread can execute synchronized block
- Ensures all changes visible to other threads
- Can be used on methods or blocks
- Provides atomicity for compound operations

**Volatile**:
- Provides only **visibility** guarantee
- Doesn't provide mutual exclusion
- Ensures reads/writes go to main memory
- Doesn't provide atomicity
- Lighter weight than synchronized

**Key Differences**:

| Aspect | Synchronized | Volatile |
|--------|--------------|----------|
| **Mutual Exclusion** | Yes | No |
| **Visibility** | Yes | Yes |
| **Atomicity** | Yes (for block) | No |
| **Performance** | Slower (locking) | Faster (no locking) |
| **Use Case** | Critical sections | Simple flags, counters |

**Example - When to Use Each**:

**Synchronized** (for compound operations):
```java
private int count = 0;

public synchronized void increment() {
    count++; // Atomic read-modify-write
}
```

**Volatile** (for simple flags):
```java
private volatile boolean running = true;

public void stop() {
    running = false; // Simple write, volatile ensures visibility
}

public void run() {
    while (running) { // Volatile ensures sees latest value
        // Do work
    }
}
```

**When to Use Synchronized**:
- Need mutual exclusion
- Compound operations (read-modify-write)
- Multiple operations need to be atomic together

**When to Use Volatile**:
- Simple flag variables
- Single read/write operations
- Visibility is main concern
- Performance critical (no locking overhead)

---

### Q2: What is a deadlock? How do you prevent and detect it?

**Answer**:

**Deadlock Definition**:
A situation where two or more threads are blocked forever, waiting for each other to release locks.

**Deadlock Conditions** (All must be present):
1. **Mutual Exclusion**: Resources can't be shared
2. **Hold and Wait**: Thread holds one lock, waits for another
3. **No Preemption**: Locks can't be forcibly taken
4. **Circular Wait**: Circular chain of threads waiting for locks

**Example**:
```java
// Thread 1
synchronized(lockA) {
    Thread.sleep(100); // Simulate work
    synchronized(lockB) {
        // ...
    }
}

// Thread 2
synchronized(lockB) {
    Thread.sleep(100);
    synchronized(lockA) {
        // ...
    }
}
```

**Prevention Strategies**:

**1. Lock Ordering**:
Always acquire locks in same order.

```java
// Good: Same order
synchronized(lockA) {
    synchronized(lockB) {
        // ...
    }
}

// Also good: Same order
synchronized(lockA) {
    synchronized(lockB) {
        // ...
    }
}
```

**2. Timeout on Locks**:
Use tryLock with timeout.

```java
if (lock1.tryLock(5, TimeUnit.SECONDS)) {
    try {
        if (lock2.tryLock(5, TimeUnit.SECONDS)) {
            try {
                // Critical section
            } finally {
                lock2.unlock();
            }
        }
    } finally {
        lock1.unlock();
    }
}
```

**3. Avoid Nested Locks**:
Minimize lock nesting.

**4. Lock-Free Programming**:
Use atomic classes, immutable objects.

**5. Use Higher-Level Constructs**:
Use ConcurrentHashMap instead of synchronized HashMap.

**Detection**:

**1. Thread Dump Analysis**:
```bash
jstack <pid> | grep -A 10 "deadlock"
```

**2. Deadlock Detection Tools**:
- JConsole
- VisualVM
- Application monitoring tools

**3. Programmatic Detection**:
```java
ThreadMXBean threadBean = ManagementFactory.getThreadMXBean();
long[] deadlockedThreads = threadBean.findDeadlockedThreads();
if (deadlockedThreads != null) {
    // Deadlock detected
}
```

**Recovery**:
- **Prevention is Best**: Design to avoid deadlocks
- **Timeout and Retry**: Use tryLock with timeout
- **Restart**: As last resort, restart application

---

### Q3: Explain the producer-consumer problem and how to solve it.

**Answer**:

**Problem**:
- Producer produces items
- Consumer consumes items
- Need to coordinate between producer and consumer
- Buffer has limited capacity

**Challenges**:
- Producer must wait if buffer full
- Consumer must wait if buffer empty
- Need thread-safe access to buffer

**Solution 1: Using BlockingQueue** (Simplest):

```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>(10);

// Producer
class Producer implements Runnable {
    public void run() {
        try {
            while (true) {
                String item = produceItem();
                queue.put(item); // Blocks if queue full
                System.out.println("Produced: " + item);
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

// Consumer
class Consumer implements Runnable {
    public void run() {
        try {
            while (true) {
                String item = queue.take(); // Blocks if queue empty
                consumeItem(item);
                System.out.println("Consumed: " + item);
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

**Solution 2: Using wait() and notify()**:

```java
class Buffer {
    private Queue<String> queue = new LinkedList<>();
    private int capacity = 10;
    
    public synchronized void put(String item) throws InterruptedException {
        while (queue.size() == capacity) {
            wait(); // Wait if buffer full
        }
        queue.add(item);
        notifyAll(); // Notify consumers
    }
    
    public synchronized String take() throws InterruptedException {
        while (queue.isEmpty()) {
            wait(); // Wait if buffer empty
        }
        String item = queue.remove();
        notifyAll(); // Notify producers
        return item;
    }
}
```

**Key Points**:
- Use `while` not `if` for condition check (spurious wakeups)
- Always notify after state change
- Synchronize on same object

**Solution 3: Using Semaphore**:

```java
Semaphore emptySlots = new Semaphore(10); // Buffer capacity
Semaphore filledSlots = new Semaphore(0); // Initially empty
Mutex mutex = new Mutex(); // For mutual exclusion

// Producer
emptySlots.acquire(); // Wait for empty slot
mutex.lock();
try {
    buffer.add(item);
} finally {
    mutex.unlock();
}
filledSlots.release(); // Signal item available

// Consumer
filledSlots.acquire(); // Wait for item
mutex.lock();
try {
    item = buffer.remove();
} finally {
    mutex.unlock();
}
emptySlots.release(); // Signal slot available
```

**Best Practice**: Use BlockingQueue - it's thread-safe and handles all synchronization internally.

---

### Q4: What is the difference between ExecutorService and ForkJoinPool?

**Answer**:

**ExecutorService**:
- General-purpose thread pool
- Fixed number of threads
- Good for independent tasks
- Tasks submitted and executed
- No work-stealing

**ForkJoinPool**:
- Specialized for divide-and-conquer tasks
- Work-stealing algorithm
- Good for recursive tasks
- Tasks can fork (split) and join (combine)
- More efficient for CPU-intensive tasks

**Example - ExecutorService**:
```java
ExecutorService executor = Executors.newFixedThreadPool(10);

for (int i = 0; i < 100; i++) {
    executor.submit(() -> {
        // Independent task
        processItem(i);
    });
}
```

**Example - ForkJoinPool**:
```java
class SumTask extends RecursiveTask<Long> {
    private int[] array;
    private int start, end;
    
    protected Long compute() {
        if (end - start < 1000) {
            // Base case: compute directly
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += array[i];
            }
            return sum;
        } else {
            // Split task
            int mid = (start + end) / 2;
            SumTask left = new SumTask(array, start, mid);
            SumTask right = new SumTask(array, mid, end);
            left.fork(); // Execute in parallel
            long rightResult = right.compute();
            long leftResult = left.join(); // Wait for result
            return leftResult + rightResult;
        }
    }
}

ForkJoinPool pool = new ForkJoinPool();
long result = pool.invoke(new SumTask(array, 0, array.length));
```

**When to Use Each**:

**ExecutorService**:
- Independent tasks
- I/O-bound operations
- General-purpose threading
- Simple task submission

**ForkJoinPool**:
- Recursive/divide-and-conquer algorithms
- CPU-intensive tasks
- Tasks that can be split
- Parallel processing of large datasets

---

### Q5: Explain thread safety in Java collections. How do you make collections thread-safe?

**Answer**:

**Problem**: Most Java collections are not thread-safe.

**Non-Thread-Safe Collections**:
- ArrayList, LinkedList, HashMap, HashSet
- Multiple threads modifying can cause:
  - Data corruption
  - ConcurrentModificationException
  - Inconsistent state

**Solutions**:

**1. Synchronized Collections**:
```java
List<String> list = Collections.synchronizedList(new ArrayList<>());
Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
```

**Limitations**:
- Synchronization on every operation
- Need external synchronization for iteration
- Performance overhead

**2. Concurrent Collections** (Recommended):

**ConcurrentHashMap**:
```java
ConcurrentHashMap<String, String> map = new ConcurrentHashMap<>();
// Thread-safe, lock striping, better performance
```

**CopyOnWriteArrayList**:
```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
// Thread-safe, creates copy on write
// Good for read-heavy scenarios
```

**BlockingQueue**:
```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>();
// Thread-safe queue for producer-consumer
```

**3. Immutable Collections**:
```java
List<String> list = List.of("a", "b", "c"); // Immutable, thread-safe
Map<String, String> map = Map.of("key", "value"); // Immutable
```

**Comparison**:

| Collection | Thread-Safe | Performance | Use Case |
|------------|-------------|-------------|----------|
| ArrayList | No | Fast | Single-threaded |
| Collections.synchronizedList | Yes | Slower | Simple thread-safety |
| CopyOnWriteArrayList | Yes | Slower writes | Read-heavy |
| ConcurrentHashMap | Yes | Fast | Concurrent access |
| Collections.synchronizedMap | Yes | Slower | Simple thread-safety |

**Best Practices**:
1. Use ConcurrentHashMap for maps
2. Use CopyOnWriteArrayList for read-heavy lists
3. Use BlockingQueue for producer-consumer
4. Prefer immutable collections when possible
5. Avoid synchronized collections if possible

---

### Q6: What is the difference between CountDownLatch and CyclicBarrier?

**Answer**:

**CountDownLatch**:
- One-time use
- Countdown from N to 0
- One or more threads wait for countdown
- Countdown threads don't wait for each other
- Not reusable

**Example**:
```java
CountDownLatch latch = new CountDownLatch(3);

// Worker threads
for (int i = 0; i < 3; i++) {
    new Thread(() -> {
        // Do work
        latch.countDown(); // Decrement count
    }).start();
}

// Main thread waits
latch.await(); // Wait until count reaches 0
// All workers completed
```

**CyclicBarrier**:
- Reusable
- Multiple threads wait at barrier
- All threads must reach barrier before proceeding
- Can have barrier action (runs when all threads reach)
- Threads wait for each other

**Example**:
```java
CyclicBarrier barrier = new CyclicBarrier(3, () -> {
    System.out.println("All threads reached barrier");
});

// Three threads
for (int i = 0; i < 3; i++) {
    new Thread(() -> {
        // Phase 1
        barrier.await(); // Wait for all threads
        // Phase 2 (all start together)
    }).start();
}
```

**Key Differences**:

| Aspect | CountDownLatch | CyclicBarrier |
|--------|----------------|---------------|
| **Reusability** | One-time | Reusable |
| **Count Direction** | Count down | Count up |
| **Threads Wait** | Main thread waits | All threads wait |
| **Barrier Action** | No | Yes (optional) |
| **Use Case** | Wait for completion | Synchronize at barrier |

**When to Use CountDownLatch**:
- Wait for initialization
- Wait for multiple tasks to complete
- One-time coordination

**When to Use CyclicBarrier**:
- Parallel algorithms
- Multi-phase processing
- Threads need to synchronize at points

---

### Q7: Explain the happens-before relationship in Java concurrency.

**Answer**:

**Happens-Before Relationship**:
A guarantee that memory writes by one thread are visible to another thread.

**Rules** (If A happens-before B, then A's writes are visible to B):

**1. Program Order**:
- Actions in same thread happen in program order
- Compiler can reorder, but maintains happens-before

**2. Monitor Lock (synchronized)**:
- Unlock happens-before subsequent lock on same monitor
- All writes before unlock visible after lock

**3. Volatile**:
- Write to volatile variable happens-before subsequent read
- Ensures visibility across threads

**4. Thread Start**:
- Thread.start() happens-before any action in started thread

**5. Thread Join**:
- All actions in thread happen-before join() returns

**6. Transitivity**:
- If A happens-before B, and B happens-before C, then A happens-before C

**Example**:
```java
// Without proper happens-before
int x = 0;
boolean flag = false;

// Thread 1
x = 1;
flag = true; // May not be visible to Thread 2

// Thread 2
while (!flag) { } // May loop forever
System.out.println(x); // May print 0 (stale value)
```

**Solution**:
```java
volatile boolean flag = false; // Volatile ensures happens-before

// Thread 1
x = 1;
flag = true; // Happens-before write

// Thread 2
while (!flag) { } // Happens-before read
System.out.println(x); // Guaranteed to see x = 1
```

**Importance**:
- Ensures correct visibility
- Prevents stale data
- Critical for thread safety
- Without it, programs may have unpredictable behavior

---

### Q8: How do you implement a thread-safe singleton pattern?

**Answer**:

**Problem**: Need single instance, thread-safe creation.

**Solution 1: Eager Initialization** (Simplest):
```java
public class Singleton {
    private static final Singleton instance = new Singleton();
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        return instance;
    }
}
```
- Thread-safe (static initialization)
- Created at class loading
- Simple but created even if not used

**Solution 2: Lazy Initialization with Synchronization**:
```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {}
    
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
- Thread-safe
- Lazy initialization
- Synchronization overhead on every call

**Solution 3: Double-Checked Locking**:
```java
public class Singleton {
    private static volatile Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
- Thread-safe
- Lazy initialization
- Minimal synchronization (only on first call)
- Requires volatile keyword

**Solution 4: Bill Pugh (Inner Static Class)** (Recommended):
```java
public class Singleton {
    private Singleton() {}
    
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }
    
    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```
- Thread-safe (static initialization)
- Lazy initialization (created when getInstance() called)
- No synchronization needed
- Simple and efficient

**Solution 5: Enum Singleton** (Best for Java):
```java
public enum Singleton {
    INSTANCE;
    
    public void doSomething() {
        // Implementation
    }
}
```
- Thread-safe
- Serialization-safe
- Reflection-safe
- Simplest implementation
- Recommended by Joshua Bloch

**Recommendation**: Use Enum for simplicity, or Bill Pugh for class-based approach.

---

### Q9: Explain the producer-consumer pattern using BlockingQueue.

**Answer**:

**BlockingQueue Benefits**:
- Thread-safe
- Handles blocking automatically
- No manual synchronization needed
- Built-in capacity management

**Complete Example**:
```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

class Producer implements Runnable {
    private BlockingQueue<String> queue;
    
    public Producer(BlockingQueue<String> queue) {
        this.queue = queue;
    }
    
    public void run() {
        try {
            for (int i = 0; i < 100; i++) {
                String item = "Item " + i;
                queue.put(item); // Blocks if queue full
                System.out.println("Produced: " + item);
                Thread.sleep(100);
            }
            queue.put("POISON"); // Signal completion
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

class Consumer implements Runnable {
    private BlockingQueue<String> queue;
    
    public Consumer(BlockingQueue<String> queue) {
        this.queue = queue;
    }
    
    public void run() {
        try {
            while (true) {
                String item = queue.take(); // Blocks if queue empty
                if ("POISON".equals(item)) {
                    break; // Stop consuming
                }
                System.out.println("Consumed: " + item);
                Thread.sleep(200);
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

// Usage
BlockingQueue<String> queue = new LinkedBlockingQueue<>(10);
ExecutorService executor = Executors.newFixedThreadPool(2);

executor.submit(new Producer(queue));
executor.submit(new Consumer(queue));

executor.shutdown();
```

**Key Features**:
- `put()`: Blocks if queue full
- `take()`: Blocks if queue empty
- Thread-safe operations
- Automatic blocking/unblocking
- No manual wait/notify needed

**Multiple Producers/Consumers**:
```java
// Multiple producers
for (int i = 0; i < 3; i++) {
    executor.submit(new Producer(queue));
}

// Multiple consumers
for (int i = 0; i < 2; i++) {
    executor.submit(new Consumer(queue));
}
```

---

### Q10: What are the common pitfalls in multithreaded programming and how to avoid them?

**Answer**:

**1. Race Conditions**

**Problem**: Unpredictable behavior due to timing.

**Example**:
```java
// Unsafe
private int count = 0;
public void increment() {
    count++; // Not atomic
}
```

**Solution**:
```java
// Use synchronization or atomic
private AtomicInteger count = new AtomicInteger(0);
public void increment() {
    count.incrementAndGet();
}
```

**2. Deadlock**

**Problem**: Threads waiting for each other.

**Solution**: Lock ordering, timeouts, avoid nested locks.

**3. Livelock**

**Problem**: Threads keep responding but make no progress.

**Solution**: Introduce randomness, backoff strategies.

**4. Starvation**

**Problem**: Thread waits indefinitely.

**Solution**: Fair locks, proper thread priorities.

**5. Memory Visibility**

**Problem**: Changes not visible to other threads.

**Solution**: Use volatile, synchronized, or atomic classes.

**6. Incorrect use of volatile**

**Problem**: Using volatile for compound operations.

**Example**:
```java
// Wrong: volatile doesn't make this atomic
private volatile int count = 0;
public void increment() {
    count++; // Still not atomic!
}
```

**Solution**: Use AtomicInteger or synchronized.

**7. Forgetting to unlock**

**Problem**: Lock never released, deadlock.

**Solution**: Always unlock in finally block.

**8. Spurious Wakeups**

**Problem**: Thread wakes up without notify.

**Solution**: Use while loop, not if statement.

**9. Sharing mutable objects**

**Problem**: Sharing mutable objects without synchronization.

**Solution**: Use immutable objects, thread-safe collections, or proper synchronization.

**10. Testing Concurrent Code**

**Problem**: Hard to test, may pass tests but fail in production.

**Solution**: Stress testing, use tools like jcstress, proper test design.

**Best Practices Summary**:
1. Minimize shared mutable state
2. Use thread-safe collections
3. Prefer immutable objects
4. Use proper synchronization
5. Test thoroughly
6. Document thread-safety guarantees
7. Use higher-level concurrency utilities
8. Avoid premature optimization
9. Understand happens-before relationships
10. Use tools to detect issues (FindBugs, PMD)

---

## Key Takeaways

1. **Understand Thread Safety**: Know when code is thread-safe
2. **Choose Right Tool**: Synchronized, volatile, atomic, locks - each has purpose
3. **Avoid Common Pitfalls**: Deadlock, race conditions, visibility issues
4. **Use High-Level APIs**: Executor framework, concurrent collections
5. **Test Concurrent Code**: Stress testing is essential
6. **Document Thread Safety**: Document guarantees clearly
7. **Prefer Immutability**: Immutable objects are thread-safe
8. **Minimize Shared State**: Less shared state = fewer problems
9. **Understand Memory Model**: Happens-before relationships
10. **Profile Before Optimizing**: Measure, don't guess

