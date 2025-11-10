# Java Collections Framework - Comprehensive Guide

## Table of Contents
1. [Collections Framework Overview](#collections-framework-overview)
2. [Core Interfaces](#core-interfaces)
3. [List Implementations](#list-implementations)
4. [Set Implementations](#set-implementations)
5. [Map Implementations](#map-implementations)
6. [Queue Implementations](#queue-implementations)
7. [Time & Space Complexity](#time--space-complexity)
8. [Thread Safety & Concurrency](#thread-safety--concurrency)
9. [Best Practices](#best-practices)
10. [Common Interview Questions](#common-interview-questions)

---

## Collections Framework Overview

### Architecture
The Java Collections Framework provides a unified architecture for representing and manipulating collections. It consists of:
- **Interfaces**: Define contracts (List, Set, Map, Queue)
- **Implementations**: Concrete classes (ArrayList, HashSet, HashMap, etc.)
- **Algorithms**: Methods for searching, sorting, etc.

### Hierarchy
```
Collection (Interface)
├── List
│   ├── ArrayList
│   ├── LinkedList
│   ├── Vector
│   └── Stack
├── Set
│   ├── HashSet
│   ├── LinkedHashSet
│   ├── TreeSet
│   └── EnumSet
└── Queue
    ├── PriorityQueue
    ├── ArrayDeque
    └── BlockingQueue implementations

Map (Separate Interface)
├── HashMap
├── LinkedHashMap
├── TreeMap
├── Hashtable
└── ConcurrentHashMap
```

---

## Core Interfaces

### Collection Interface
**Root interface** for all collection classes (except Map).

**Key Methods**:
- `add(E e)`, `remove(Object o)`
- `contains(Object o)`, `size()`, `isEmpty()`
- `iterator()`, `toArray()`
- `addAll(Collection<? extends E> c)`, `removeAll(Collection<?> c)`

### List Interface
**Ordered collection** that allows duplicate elements.

**Characteristics**:
- Maintains insertion order
- Allows null elements
- Indexed access (get, set, add at index)

### Set Interface
**Collection that contains no duplicate elements**.

**Characteristics**:
- No duplicates (equals() method)
- At most one null element
- No ordering guarantee (except LinkedHashSet, TreeSet)

### Map Interface
**Object that maps keys to values**. Not part of Collection hierarchy.

**Characteristics**:
- No duplicate keys
- Each key maps to at most one value
- Key-value pairs

### Queue Interface
**Collection designed for holding elements prior to processing**.

**Characteristics**:
- FIFO (First-In-First-Out) typically
- Priority queues use natural ordering
- Blocking queues for producer-consumer patterns

---

## List Implementations

### ArrayList
**Resizable array implementation** of the List interface.

**Key Characteristics**:
- **Dynamic sizing**: Grows automatically (typically 50% increase)
- **Random access**: O(1) for get/set operations
- **Insertion/Deletion**: O(n) for operations at beginning/middle
- **Not thread-safe**: Use Collections.synchronizedList() or CopyOnWriteArrayList
- **Default capacity**: 10

**When to Use**:
- Frequent random access
- Iteration over elements
- Less frequent insertion/deletion at beginning

**Internal Working**:
- Uses array internally
- When capacity exceeded, creates new array (1.5x size typically)
- Old array elements copied to new array

**Time Complexity**:
- Add at end: O(1) amortized
- Add at index: O(n)
- Remove: O(n)
- Get/Set: O(1)
- Contains: O(n)
- IndexOf: O(n)

---

### LinkedList
**Doubly-linked list implementation** of List and Deque interfaces.

**Key Characteristics**:
- **Node-based**: Each element is a node with references to next and previous
- **No random access**: Sequential access only
- **Insertion/Deletion**: O(1) if position known
- **Memory overhead**: Extra memory for node pointers
- **Not thread-safe**

**When to Use**:
- Frequent insertion/deletion at beginning or middle
- When order of insertion matters
- Less frequent random access

**Time Complexity**:
- Add at beginning: O(1)
- Add at end: O(1)
- Add at index: O(n) to find position, then O(1)
- Remove: O(1) if node reference known, O(n) if by value
- Get/Set: O(n)
- Contains: O(n)

**ArrayList vs LinkedList**:
- **ArrayList**: Better for random access, less memory overhead
- **LinkedList**: Better for frequent insertions/deletions, more memory overhead

---

### Vector
**Synchronized, resizable array** (legacy class, similar to ArrayList).

**Key Characteristics**:
- **Thread-safe**: All methods synchronized
- **Performance overhead**: Synchronization causes slower performance
- **Legacy class**: Prefer ArrayList with external synchronization
- **Default capacity**: 10

**When to Use**:
- Legacy code compatibility
- Rarely used in new code (prefer ArrayList + synchronization)

---

### Stack
**LIFO (Last-In-First-Out) stack** (extends Vector).

**Key Characteristics**:
- **LIFO structure**: push(), pop(), peek()
- **Extends Vector**: Inherits synchronization overhead
- **Legacy class**: Prefer ArrayDeque for stack operations

---

## Set Implementations

### HashSet
**Hash table implementation** of Set interface.

**Key Characteristics**:
- **No ordering**: Elements not in insertion order
- **Fast operations**: O(1) average for add, remove, contains
- **Uses HashMap internally**: Each element stored as key in HashMap
- **Null allowed**: One null element permitted
- **Not thread-safe**

**Internal Working**:
- Uses hash code to determine bucket
- Handles collisions using linked lists or tree structure (Java 8+)
- Load factor: 0.75 (default)
- When load factor exceeded, rehashing occurs (doubles capacity)

**Time Complexity**:
- Add: O(1) average, O(n) worst case
- Remove: O(1) average, O(n) worst case
- Contains: O(1) average, O(n) worst case
- Iteration: O(n)

**Hash Function Requirements**:
- Consistent: Same object always returns same hash code
- Efficient: Fast computation
- Well-distributed: Minimizes collisions

---

### LinkedHashSet
**Hash table + linked list** implementation maintaining insertion order.

**Key Characteristics**:
- **Maintains order**: Insertion order preserved
- **Performance**: Slightly slower than HashSet due to linked list overhead
- **Uses LinkedHashMap internally**
- **Not thread-safe**

**When to Use**:
- Need Set behavior with insertion order
- Iteration order matters

**Time Complexity**: Same as HashSet (O(1) average)

---

### TreeSet
**Red-Black tree implementation** of NavigableSet.

**Key Characteristics**:
- **Sorted order**: Elements sorted (natural ordering or Comparator)
- **No null elements**: Null not allowed
- **Slower operations**: O(log n) for add, remove, contains
- **Range operations**: Efficient for range queries (subSet, headSet, tailSet)
- **Not thread-safe**

**When to Use**:
- Need sorted set
- Range queries required
- Ordering matters

**Time Complexity**:
- Add: O(log n)
- Remove: O(log n)
- Contains: O(log n)
- First/Last: O(log n)
- Iteration: O(n)

---

### EnumSet
**Specialized Set implementation** for enum types.

**Key Characteristics**:
- **Highly efficient**: Bit vector implementation
- **Type-safe**: Only enum elements allowed
- **Fast operations**: O(1) for most operations
- **Not thread-safe**

---

## Map Implementations

### HashMap
**Hash table implementation** of Map interface.

**Key Characteristics**:
- **No ordering**: Key-value pairs not ordered
- **Fast operations**: O(1) average for get, put, remove
- **Null allowed**: One null key, multiple null values
- **Not thread-safe**: Use ConcurrentHashMap for thread safety
- **Load factor**: 0.75 (default)

**Internal Working**:
- **Java 7**: Array of buckets, each bucket is linked list
- **Java 8+**: Array of buckets, linked list converts to tree when threshold exceeded (8 elements)
- **Rehashing**: When load factor exceeded, capacity doubled and all entries rehashed

**Bucket Structure**:
- Hash code determines bucket index
- Collisions handled by chaining (linked list or tree)
- Tree structure used when bucket has >8 elements (improves worst-case performance)

**Time Complexity**:
- Put: O(1) average, O(log n) worst case (with tree)
- Get: O(1) average, O(log n) worst case
- Remove: O(1) average, O(log n) worst case
- ContainsKey: O(1) average
- Iteration: O(n)

**Key Requirements**:
- **equals() and hashCode()**: Must be properly implemented
- **Immutable keys**: Best practice (prevents hash code changes)
- **Consistent**: equals() and hashCode() must be consistent

---

### LinkedHashMap
**Hash table + linked list** maintaining insertion or access order.

**Key Characteristics**:
- **Ordering**: Insertion order or access order (LRU cache)
- **Performance**: Slightly slower than HashMap
- **LRU Cache**: Can be configured for access-order (useful for caches)
- **Not thread-safe**

**When to Use**:
- Need Map with predictable iteration order
- Implementing LRU cache

**Time Complexity**: Same as HashMap

---

### TreeMap
**Red-Black tree implementation** of NavigableMap.

**Key Characteristics**:
- **Sorted keys**: Keys sorted (natural ordering or Comparator)
- **Range operations**: Efficient for range queries
- **No null keys**: Null keys not allowed (if natural ordering used)
- **Slower operations**: O(log n) for put, get, remove
- **Not thread-safe**

**When to Use**:
- Need sorted map
- Range queries required
- Ordered iteration needed

**Time Complexity**:
- Put: O(log n)
- Get: O(log n)
- Remove: O(log n)
- First/Last key: O(log n)
- Iteration: O(n)

---

### Hashtable
**Synchronized hash table** (legacy class, similar to HashMap).

**Key Characteristics**:
- **Thread-safe**: All methods synchronized
- **No null keys/values**: Null not allowed
- **Legacy class**: Prefer ConcurrentHashMap
- **Performance overhead**: Synchronization causes slower performance

---

## Queue Implementations

### PriorityQueue
**Unbounded priority queue** based on priority heap.

**Key Characteristics**:
- **Priority ordering**: Elements ordered by natural ordering or Comparator
- **Heap structure**: Min-heap by default
- **Not thread-safe**
- **No null elements**: Null not allowed

**Time Complexity**:
- Offer/Add: O(log n)
- Poll/Remove: O(log n)
- Peek: O(1)

---

### ArrayDeque
**Resizable array implementation** of Deque interface.

**Key Characteristics**:
- **Double-ended queue**: Add/remove from both ends
- **Faster than Stack**: Better performance than Stack class
- **Faster than LinkedList**: For queue operations
- **No null elements**: Null not allowed
- **Not thread-safe**

**When to Use**:
- Stack operations (prefer over Stack class)
- Queue operations (prefer over LinkedList for queue)
- Deque operations

**Time Complexity**:
- Add/Remove at ends: O(1)
- Contains: O(n)

---

## Time & Space Complexity

### Summary Table

| Collection | Add | Remove | Get/Contains | Space |
|------------|-----|--------|--------------|-------|
| ArrayList | O(1) amortized | O(n) | O(1) | O(n) |
| LinkedList | O(1) | O(1) | O(n) | O(n) |
| HashSet | O(1) avg | O(1) avg | O(1) avg | O(n) |
| TreeSet | O(log n) | O(log n) | O(log n) | O(n) |
| HashMap | O(1) avg | O(1) avg | O(1) avg | O(n) |
| TreeMap | O(log n) | O(log n) | O(log n) | O(n) |
| PriorityQueue | O(log n) | O(log n) | O(1) | O(n) |

---

## Thread Safety & Concurrency

### Thread-Safe Collections

**Collections.synchronizedXXX()**:
- Wraps collection with synchronized methods
- Performance overhead
- Need external synchronization for iteration

**ConcurrentHashMap**:
- Thread-safe HashMap alternative
- Lock striping (multiple locks)
- Better performance than synchronized HashMap
- No locking for reads

**CopyOnWriteArrayList**:
- Thread-safe List
- Creates new array on modification
- Good for read-heavy scenarios
- Expensive writes

**BlockingQueue Implementations**:
- ArrayBlockingQueue
- LinkedBlockingQueue
- PriorityBlockingQueue
- SynchronousQueue

**Concurrent Collections**:
- ConcurrentHashMap
- ConcurrentSkipListMap
- ConcurrentSkipListSet
- CopyOnWriteArrayList
- CopyOnWriteArraySet

---

## Best Practices

### Choosing the Right Collection

**List Selection**:
- **ArrayList**: Random access, iteration
- **LinkedList**: Frequent insertions/deletions
- **Vector**: Legacy code only

**Set Selection**:
- **HashSet**: General purpose, no ordering needed
- **LinkedHashSet**: Need insertion order
- **TreeSet**: Need sorted order

**Map Selection**:
- **HashMap**: General purpose
- **LinkedHashMap**: Need insertion/access order
- **TreeMap**: Need sorted keys
- **ConcurrentHashMap**: Thread-safe requirements

### Common Pitfalls

1. **Modifying during iteration**: Use Iterator.remove() or CopyOnWriteArrayList
2. **Not implementing equals()/hashCode()**: Required for Set and Map keys
3. **Using Vector/Hashtable**: Prefer modern alternatives
4. **Not considering thread safety**: Use concurrent collections when needed
5. **Choosing wrong collection**: Understand use case before selecting

### Performance Tips

1. **Initial capacity**: Set appropriate initial capacity if size known
2. **Load factor**: Adjust for HashMap/HashSet if needed
3. **Iterator vs for-each**: Iterator allows removal during iteration
4. **Avoid unnecessary boxing**: Use primitive collections if available
5. **Pre-size collections**: Reduce rehashing/reallocation

---

## Common Interview Questions

### Conceptual Questions

1. **Difference between ArrayList and LinkedList?**
   - ArrayList: Array-based, random access O(1), insertion O(n)
   - LinkedList: Node-based, sequential access, insertion O(1)

2. **How does HashMap work internally?**
   - Hash code determines bucket
   - Collisions handled by chaining (linked list or tree)
   - Rehashing when load factor exceeded

3. **Why is HashMap not thread-safe?**
   - No synchronization
   - Concurrent modifications can cause issues
   - Use ConcurrentHashMap for thread safety

4. **Difference between HashSet and TreeSet?**
   - HashSet: Hash table, O(1) average, no ordering
   - TreeSet: Red-Black tree, O(log n), sorted order

5. **When to use ArrayList vs Vector?**
   - ArrayList: Modern, not thread-safe, better performance
   - Vector: Legacy, thread-safe, performance overhead

6. **How does TreeMap maintain sorted order?**
   - Red-Black tree data structure
   - Self-balancing binary search tree
   - Maintains O(log n) operations

7. **What is fail-fast iterator?**
   - Throws ConcurrentModificationException on modification during iteration
   - ArrayList, HashMap iterators are fail-fast
   - CopyOnWriteArrayList has fail-safe iterator

8. **Difference between Iterator and ListIterator?**
   - Iterator: Forward traversal only
   - ListIterator: Bidirectional, can modify list

9. **Why String and Integer are good HashMap keys?**
   - Immutable (hash code doesn't change)
   - Properly implement equals() and hashCode()
   - Thread-safe

10. **What is the default capacity of ArrayList?**
    - 10 (increases by 50% typically when capacity exceeded)

### Advanced Questions

1. **How does Java 8 improve HashMap performance?**
   - Tree structure for buckets with >8 elements
   - Reduces worst-case O(n) to O(log n)

2. **Explain ConcurrentHashMap internal working?**
   - Lock striping (multiple segments/locks)
   - Read operations don't require locking
   - Better concurrency than synchronized HashMap

3. **When does HashSet use tree structure?**
   - When bucket has >8 elements (Java 8+)
   - Improves worst-case performance

4. **How to make ArrayList thread-safe?**
   - Collections.synchronizedList()
   - CopyOnWriteArrayList
   - External synchronization

5. **Difference between Comparable and Comparator?**
   - Comparable: Natural ordering (implemented by class)
   - Comparator: External ordering (separate class)

---

## Advanced Interview Questions & Answers

### Q1: Explain the internal working of HashMap in detail. How does it handle collisions?

**Answer**:

**HashMap Internal Structure**:

1. **Array of Buckets**: HashMap uses an array (default size 16) where each element is a bucket
2. **Hash Function**: `hashCode()` of key is used to determine bucket index
3. **Bucket Index**: `index = hashCode(key) & (array.length - 1)` (bitwise AND for power-of-2 sizing)

**Collision Handling**:

**Java 7 and Earlier**:
- Each bucket is a **linked list**
- When collision occurs, new entry added to linked list
- Worst case: O(n) if all keys hash to same bucket

**Java 8 and Later**:
- Bucket starts as **linked list**
- When bucket size exceeds **8**, converts to **red-black tree**
- When bucket size reduces to **6**, converts back to linked list
- Worst case: O(log n) instead of O(n)

**Why Tree Structure?**:
- Prevents worst-case O(n) performance
- Better for high collision scenarios
- Threshold 8 chosen based on performance analysis

**Load Factor and Rehashing**:
- **Load Factor**: 0.75 (default) - when 75% of buckets filled
- **Rehashing**: When threshold exceeded, array size doubled
- All entries rehashed and redistributed
- Rehashing is expensive operation

**Example**:
```java
// Key: "John", Value: 25
// Step 1: Calculate hash
int hash = "John".hashCode(); // e.g., 2314539

// Step 2: Calculate index
int index = hash & (array.length - 1); // e.g., index = 3

// Step 3: Check bucket at index 3
// If collision: Add to linked list or tree
```

---

### Q2: How does ConcurrentHashMap achieve thread-safety without using synchronized on every operation?

**Answer**:

**ConcurrentHashMap Thread-Safety Mechanisms**:

**Java 7 Approach - Segment Locking**:
- HashMap divided into **segments** (default 16)
- Each segment has its own lock
- **Lock striping**: Multiple threads can access different segments simultaneously
- Read operations don't require locking (volatile reads)
- Write operations lock only relevant segment

**Java 8 Approach - Node-Level Locking**:
- Removed segment concept
- Uses **CAS (Compare-And-Swap)** operations for lock-free reads
- **Synchronized blocks** only for write operations on specific nodes
- Much finer-grained locking

**Key Techniques**:

1. **Volatile Variables**:
   - Node values are volatile
   - Ensures visibility across threads
   - No locking needed for reads

2. **CAS Operations**:
   - Atomic operations for updates
   - Compare current value, swap if matches
   - Lock-free for many operations

3. **Synchronized Blocks**:
   - Only for write operations
   - Lock specific node, not entire map
   - Minimal lock contention

**Performance Benefits**:
- **Reads**: No locking, very fast
- **Writes**: Lock only affected nodes
- **Concurrency**: Multiple threads can read/write simultaneously
- **Scalability**: Better than synchronized HashMap

**Example**:
```java
// Thread 1: Reading from bucket 0
// Thread 2: Reading from bucket 1
// Thread 3: Writing to bucket 2
// All can proceed concurrently - no blocking
```

**Comparison with Synchronized HashMap**:
- **Synchronized HashMap**: One lock for entire map, all operations serialized
- **ConcurrentHashMap**: Multiple locks, parallel operations possible
- **Performance**: ConcurrentHashMap much faster for concurrent access

---

### Q3: Explain the fail-fast vs fail-safe iterator concept with examples.

**Answer**:

**Fail-Fast Iterators**:

**Definition**: Throw `ConcurrentModificationException` if collection is modified during iteration.

**How It Works**:
- Maintains **modification count** (modCount)
- Checks modCount before each operation
- If modCount changed, throws exception

**Collections with Fail-Fast Iterators**:
- ArrayList
- HashMap
- HashSet
- Vector
- Most non-concurrent collections

**Example**:
```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");

Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String item = it.next();
    list.add("C"); // Throws ConcurrentModificationException
}
```

**Solution for Fail-Fast**:
```java
// Use iterator's remove method
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String item = it.next();
    if (item.equals("A")) {
        it.remove(); // Safe - uses iterator's remove
    }
}
```

**Fail-Safe Iterators**:

**Definition**: Don't throw exception, work on snapshot or copy of collection.

**How It Works**:
- Iterate over **snapshot** of collection
- Changes to original collection don't affect iteration
- No exception thrown

**Collections with Fail-Safe Iterators**:
- CopyOnWriteArrayList
- ConcurrentHashMap
- Concurrent collections

**Example**:
```java
List<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");

Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String item = it.next();
    list.add("C"); // No exception, but "C" won't appear in current iteration
}
```

**Trade-offs**:

| Aspect | Fail-Fast | Fail-Safe |
|--------|-----------|-----------|
| Exception | Throws on modification | No exception |
| Performance | Faster | Slower (snapshot overhead) |
| Memory | Lower | Higher (snapshot) |
| Consistency | Immediate detection | May miss recent changes |
| Use Case | Single-threaded | Multi-threaded |

---

### Q4: How does TreeMap maintain sorted order? Explain the Red-Black tree structure.

**Answer**:

**TreeMap Uses Red-Black Tree**:

**Red-Black Tree Properties**:
1. **Binary Search Tree**: Left < Node < Right
2. **Colored Nodes**: Each node is red or black
3. **Root is Black**: Root node always black
4. **Red Node Rules**: Red nodes can't have red children
5. **Black Height**: Same number of black nodes on all paths from root to leaves

**Why Red-Black Tree?**:
- **Self-balancing**: Maintains O(log n) operations
- **Better than AVL**: Fewer rotations, better for insertions/deletions
- **Guaranteed Height**: Height at most 2*log(n+1)

**Operations**:

**Insertion**:
1. Insert as in BST (red node)
2. If parent is red, fix violations:
   - **Recolor**: Change colors
   - **Rotate**: Left/right rotations
3. Ensure properties maintained

**Deletion**:
1. Delete as in BST
2. If deleted node was black, fix violations
3. Rotate and recolor to maintain properties

**Rotation Operations**:

**Left Rotation**:
```
    P                P
   / \              / \
  A   X    -->     A   Y
     / \              / \
    B   Y            X   C
       / \          / \
      D   C        B   D
```

**Right Rotation**: Mirror of left rotation

**Time Complexity**:
- **Search**: O(log n)
- **Insert**: O(log n)
- **Delete**: O(log n)
- **Range Query**: O(log n + k) where k is result size

**Example**:
```java
TreeMap<Integer, String> map = new TreeMap<>();
map.put(5, "Five");
map.put(3, "Three");
map.put(7, "Seven");
// Tree structure automatically maintained
// In-order traversal: 3, 5, 7 (sorted)
```

**Comparison with HashMap**:
- **HashMap**: O(1) average, no ordering
- **TreeMap**: O(log n), sorted order
- **Use TreeMap**: When sorted order needed
- **Use HashMap**: When performance critical, no ordering needed

---

### Q5: Explain the difference between ArrayList and LinkedList in terms of memory layout and performance.

**Answer**:

**Memory Layout**:

**ArrayList**:
- **Contiguous Memory**: Elements stored in contiguous array
- **Single Allocation**: One large memory block
- **Cache Friendly**: Better CPU cache utilization
- **Memory Overhead**: Only array overhead

**LinkedList**:
- **Non-Contiguous**: Elements stored as separate nodes
- **Multiple Allocations**: Each node separately allocated
- **Cache Unfriendly**: Poor CPU cache utilization
- **Memory Overhead**: Each node has 2 pointers (next, previous)

**Node Structure**:
```java
class Node {
    E data;           // Actual data
    Node next;        // Pointer to next node
    Node previous;    // Pointer to previous node
}
```

**Performance Comparison**:

| Operation | ArrayList | LinkedList |
|-----------|-----------|------------|
| Get by Index | O(1) | O(n) |
| Add at End | O(1) amortized | O(1) |
| Add at Index | O(n) | O(n) to find, O(1) to insert |
| Remove by Index | O(n) | O(n) to find, O(1) to remove |
| Remove by Value | O(n) | O(n) |
| Memory per Element | ~4-8 bytes | ~24-48 bytes (with pointers) |

**When to Use ArrayList**:
- Frequent random access
- Iteration over elements
- Less frequent insertions/deletions
- Memory efficiency important

**When to Use LinkedList**:
- Frequent insertions/deletions at beginning/middle
- Order of insertion matters
- Less frequent random access
- Memory overhead acceptable

**Real-World Example**:
```java
// Scenario: Remove all even numbers

// ArrayList: O(n²) - shifting elements
for (int i = 0; i < list.size(); i++) {
    if (list.get(i) % 2 == 0) {
        list.remove(i); // Shifts all elements after i
    }
}

// LinkedList: O(n) - just pointer updates
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    if (it.next() % 2 == 0) {
        it.remove(); // Just updates pointers
    }
}
```

**Modern Recommendation**:
- **Prefer ArrayList**: Better performance in most cases
- **Use ArrayDeque**: Instead of LinkedList for queue/stack
- **LinkedList**: Rarely needed in modern Java

---

### Q6: How does PriorityQueue work internally? Explain the heap data structure.

**Answer**:

**PriorityQueue Uses Binary Heap**:

**Binary Heap Properties**:
1. **Complete Binary Tree**: All levels filled except possibly last
2. **Heap Property**: Parent >= Children (max-heap) or Parent <= Children (min-heap)
3. **PriorityQueue**: Uses **min-heap** by default
4. **Array Representation**: Stored in array, no pointers needed

**Array Representation**:
```
Index:  0   1   2   3   4   5   6
Value:  1   3   2   7   4   5   6

Tree Structure:
        1
       / \
      3   2
     / \ / \
    7  4 5  6
```

**Parent-Child Relationship**:
- Parent at index `i`
- Left child at `2*i + 1`
- Right child at `2*i + 2`
- Parent of node at `i` is at `(i-1)/2`

**Operations**:

**1. Insert (Offer)**:
```java
// Add element to end
// Bubble up (heapify up) until heap property satisfied
// Time: O(log n)
```

**2. Remove (Poll)**:
```java
// Remove root (min element)
// Move last element to root
// Bubble down (heapify down) until heap property satisfied
// Time: O(log n)
```

**3. Peek**:
```java
// Return root without removing
// Time: O(1)
```

**Heapify Operations**:

**Bubble Up (Insert)**:
```java
void bubbleUp(int index) {
    while (index > 0) {
        int parent = (index - 1) / 2;
        if (heap[parent] <= heap[index]) break;
        swap(parent, index);
        index = parent;
    }
}
```

**Bubble Down (Remove)**:
```java
void bubbleDown(int index) {
    while (true) {
        int left = 2 * index + 1;
        int right = 2 * index + 2;
        int smallest = index;
        
        if (left < size && heap[left] < heap[smallest])
            smallest = left;
        if (right < size && heap[right] < heap[smallest])
            smallest = right;
        
        if (smallest == index) break;
        swap(index, smallest);
        index = smallest;
    }
}
```

**Time Complexity**:
- **Offer**: O(log n)
- **Poll**: O(log n)
- **Peek**: O(1)
- **Build Heap**: O(n) - can build from array in linear time

**Use Cases**:
- **Task Scheduling**: Priority-based task execution
- **Dijkstra's Algorithm**: Shortest path finding
- **Top K Elements**: Find K largest/smallest
- **Event Simulation**: Process events by priority

**Example**:
```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(5);
pq.offer(2);
pq.offer(8);
pq.offer(1);

// Always returns minimum
pq.poll(); // 1
pq.poll(); // 2
pq.poll(); // 5
pq.poll(); // 8
```

---

### Q7: Explain how equals() and hashCode() work together in HashMap. What happens if you override one but not the other?

**Answer**:

**The Contract**:

1. **If two objects are equal (equals() returns true), they must have same hashCode()**
2. **If two objects have same hashCode(), they may or may not be equal**
3. **hashCode() should be consistent**: Same object should return same hashCode

**How HashMap Uses Them**:

**Step 1: hashCode() for Bucket Selection**:
```java
int bucketIndex = key.hashCode() & (array.length - 1);
```

**Step 2: equals() for Key Matching**:
```java
// Within bucket, use equals() to find exact match
if (entry.key.equals(searchKey)) {
    return entry.value;
}
```

**Violation Consequences**:

**Scenario 1: Override equals() but not hashCode()**:
```java
class Person {
    String name;
    
    public boolean equals(Object o) {
        // Custom equals logic
        return name.equals(((Person)o).name);
    }
    // No hashCode() override - uses Object.hashCode()
}

// Problem:
Person p1 = new Person("John");
Person p2 = new Person("John");
p1.equals(p2); // true
p1.hashCode() != p2.hashCode(); // Different buckets!

// Result: HashMap can't find the object even though it exists
```

**Scenario 2: Override hashCode() but not equals()**:
```java
class Person {
    String name;
    
    public int hashCode() {
        return name.hashCode();
    }
    // No equals() override - uses Object.equals() (reference equality)
}

// Problem:
Person p1 = new Person("John");
Person p2 = new Person("John");
p1.hashCode() == p2.hashCode(); // Same bucket
p1.equals(p2); // false (reference equality)

// Result: HashMap treats them as different keys
// Both can exist in same bucket (collision)
```

**Correct Implementation**:
```java
class Person {
    String name;
    int age;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && name.equals(person.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

**Best Practices**:
1. **Always override both together**
2. **Use same fields** in both methods
3. **Make hashCode() fast** - used frequently
4. **Make equals() consistent** - reflexive, symmetric, transitive
5. **Consider immutability** - mutable objects as keys cause issues

**Why Immutable Keys?**:
```java
Person p = new Person("John");
map.put(p, "value");
p.setName("Jane"); // Changes hashCode!
// map.get(p) fails - wrong bucket
```

---

### Q8: What is the difference between Collections.synchronizedList() and CopyOnWriteArrayList?

**Answer**:

**Collections.synchronizedList()**:

**How It Works**:
- Wraps original list with synchronized methods
- All methods synchronized on wrapper object
- Original list still accessible (not thread-safe directly)

**Characteristics**:
- **Synchronization**: Every operation synchronized
- **Performance**: Overhead on every operation
- **Iteration**: Requires external synchronization
- **Write Performance**: Blocks all operations during write
- **Memory**: Low overhead

**Example**:
```java
List<String> list = Collections.synchronizedList(new ArrayList<>());

// Safe for individual operations
list.add("item"); // Synchronized

// NOT safe for iteration - need external sync
synchronized(list) {
    for (String item : list) {
        // Safe iteration
    }
}
```

**CopyOnWriteArrayList**:

**How It Works**:
- Creates new array on every modification
- Reads from current array (no locking)
- Writes create copy, then replace reference
- Snapshot-based iteration

**Characteristics**:
- **Read Operations**: No locking, very fast
- **Write Operations**: Expensive (copy entire array)
- **Iteration**: Safe, works on snapshot
- **Memory**: Higher overhead (multiple copies)
- **Consistency**: Iterators see snapshot, may miss recent writes

**Example**:
```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("item");

// Safe iteration - no synchronization needed
for (String item : list) {
    // Works on snapshot
    list.add("new"); // Won't appear in current iteration
}
```

**Comparison**:

| Aspect | synchronizedList | CopyOnWriteArrayList |
|--------|------------------|---------------------|
| Read Performance | Slower (synchronized) | Very fast (no lock) |
| Write Performance | Moderate (synchronized) | Slow (copy array) |
| Iteration | Needs external sync | Safe automatically |
| Memory | Low | High (multiple copies) |
| Use Case | Write-heavy, mixed | Read-heavy, rare writes |

**When to Use synchronizedList**:
- Write-heavy workloads
- Memory constrained
- Can handle external synchronization for iteration

**When to Use CopyOnWriteArrayList**:
- Read-heavy workloads
- Rare writes
- Iteration without synchronization needed
- Memory overhead acceptable

---

## Key Takeaways

1. **Understand use cases**: Choose collection based on operations needed
2. **Know complexity**: Time and space complexity for each operation
3. **Thread safety**: Understand when concurrent collections needed
4. **Internal working**: Know how HashMap, HashSet work internally
5. **Best practices**: Follow best practices for equals(), hashCode(), immutability

