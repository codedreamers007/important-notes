
Here are concise question-and-answer pairs to help you review coding principles typically tested in interviews for mid/senior software engineering roles at Oracle, covering all the essential data structures, algorithms, and Big-O analysis.

***

## Data Structures

### Lists
**Q:** What is the difference between an array and a linked list?  
**A:** Arrays offer constant-time access by index but are not efficient for insertions or deletions in the middle. Linked lists provide efficient insertions and deletions (O(1) if at head/tail), but no constant-time random access (O(n)).

### Stacks
**Q:** How is a stack typically implemented, and where is it used?  
**A:** A stack can be implemented with an array or linked list. It's used in function call management (call stack), expression evaluation, and depth-first search (DFS).

### Queues
**Q:** What’s the difference between a queue and a dequeue?  
**A:** A queue supports FIFO operations: enqueue (addition) at the rear and dequeue (removal) at the front. A dequeue (double-ended queue) allows insertion and removal from both ends.

### Trees
**Q:** What is a binary search tree (BST)?  
**A:** A BST is a binary tree where each node has a key greater than all nodes in the left subtree and less than all nodes in the right subtree. This property enables efficient searching, insertion, and deletion (average O(log n) time).

### Hash Tables
**Q:** How do hash tables handle collisions?  
**A:** Collisions can be handled by chaining (using linked lists at each bucket) or open addressing (probing for the next available slot).

### Sets
**Q:** What data structure commonly powers a set implementation?  
**A:** Sets are usually backed by hash tables for O(1) average case operations, or by balanced trees (like TreeSet in Java) for O(log n) operations with sorted order.

### Tries
**Q:** What is a trie and where is it used?  
**A:** A trie is a tree-like structure used for storing strings, where each node represents a single character. It is efficient for prefix-based searching (like auto-completion).

### Graphs
**Q:** What are the two main ways to represent a graph in code?  
**A:** Adjacency list (efficient for sparse graphs), and adjacency matrix (good for dense or fully connected graphs).

***

## Algorithms

### Sorting
**Q:** Compare quicksort and mergesort.  
**A:** Quicksort is usually faster on average (O(n log n)) but has O(n^2) worst-case. Mergesort always runs in O(n log n) time and is stable but requires extra memory for merging.

### Recursion
**Q:** What’s tail recursion, and why is it useful?  
**A:** Tail recursion means the recursive call is the last statement in the function. It can be optimized by compilers into iteration, using less stack space.

### Searching
**Q:** When is binary search applicable?  
**A:** Binary search works only on sorted arrays/lists, providing O(log n) search time.

### Tree Algorithms
**Q:** How do you traverse a binary tree?  
**A:** Using pre-order, in-order, or post-order (DFS), or level-order (BFS with a queue).

### Graph Algorithms
**Q:** What’s the difference between DFS and BFS?  
**A:** DFS uses a stack (or recursion), explores as far as possible, good for pathfinding in deep trees. BFS uses a queue, explores neighbors first, finds shortest path in unweighted graphs.

### Dynamic Programming
**Q:** How does dynamic programming improve naive recursion?  
**A:** DP stores results of subproblems (memoization/tabulation), eliminating duplicate work, and reducing exponential time to polynomial.

***

## Time and Space Efficiency (Big-O Analysis)

**Q:** What is Big-O notation?  
**A:** Big-O describes the upper bound of an algorithm’s running time or space usage in terms of input size n, abstracting machine or constant factors.

**Q:** What are common complexities for key operations?  
- Array access: O(1)  
- Linked list access: O(n)  
- Hash table lookup: O(1) average, O(n) worst-case  
- BST lookup: O(log n) average, O(n) worst-case (unbalanced)  
- Sorting: O(n log n) (quicksort/mergesort/heap sort)

**Q:** Why strive for optimal Big-O?  
**A:** Lower time and space complexities are critical for performance on large datasets and scalable systems.

***

Would you like detailed coding examples for any topic above, or mock problems with sample solutions for coding practice?