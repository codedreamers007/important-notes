Here are several common coding problems in Java with sample solutions to help you practice key concepts in data structures, algorithms, and efficiency. These cover typical interview topics and are suitable for mid/senior-level preparation.

***

## 1. Reverse a Linked List

**Problem:** Reverse a singly linked list.

**Solution:**

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode current = head;
    while (current != null) {
        ListNode nextTemp = current.next;
        current.next = prev;
        prev = current;
        current = nextTemp;
    }
    return prev;
}
```

***

## 2. Detect Cycle in a Linked List

**Problem:** Detect if a linked list contains a cycle.

**Solution (Floyd’s Cycle Detection):**

```java
public boolean hasCycle(ListNode head) {
    if (head == null) return false;
    ListNode slow = head, fast = head.next;
    while (fast != null && fast.next != null) {
        if (slow == fast) return true;
        slow = slow.next;
        fast = fast.next.next;
    }
    return false;
}
```

***

## 3. Merge Two Sorted Arrays

**Problem:** Merge two sorted integer arrays nums1 and nums2, storing the result in nums1 (with enough space).

**Solution:**

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int i = m - 1, j = n - 1, k = m + n - 1;
    while (i >= 0 && j >= 0) {
        if (nums1[i] > nums2[j]) {
            nums1[k--] = nums1[i--];
        } else {
            nums1[k--] = nums2[j--];
        }
    }
    while (j >= 0) {
        nums1[k--] = nums2[j--];
    }
}
```

***

## 4. Validate Binary Search Tree

**Problem:** Check whether a binary tree is a valid binary search tree (BST).

**Solution:**

```java
public boolean isValidBST(TreeNode root) {
    return isValidBSTHelper(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

private boolean isValidBSTHelper(TreeNode node, long min, long max) {
    if (node == null) return true;
    if (node.val <= min || node.val >= max) return false;
    return isValidBSTHelper(node.left, min, node.val) &&
           isValidBSTHelper(node.right, node.val, max);
}
```

***

## 5. Find the First Non-Repeated Character in a String

**Problem:** Given a string, find the first non-repeating character.

**Solution:**

```java
public char firstNonRepeatingChar(String s) {
    Map<Character, Integer> charCount = new LinkedHashMap<>();
    for (char c : s.toCharArray()) {
        charCount.put(c, charCount.getOrDefault(c, 0) + 1);
    }
    for (Map.Entry<Character, Integer> entry : charCount.entrySet()) {
        if (entry.getValue() == 1) {
            return entry.getKey();
        }
    }
    return '\0'; // or throw exception if none found
}
```

***

## 6. Implement DFS on a Graph

**Problem:** Perform a Depth-First Search (DFS) traversal on a graph represented with adjacency list.

**Solution:**

```java
public void dfs(int node, Map<Integer, List<Integer>> graph, Set<Integer> visited) {
    if (visited.contains(node)) return;
    visited.add(node);
    System.out.print(node + " ");
    for (int neighbor : graph.getOrDefault(node, new ArrayList<>())) {
        dfs(neighbor, graph, visited);
    }
}
```

***

## 7. Maximum Subarray Sum (Kadane’s Algorithm)

**Problem:** Find the contiguous subarray with the maximum sum.

**Solution:**

```java
public int maxSubArray(int[] nums) {
    int maxSoFar = nums[0];
    int maxEndingHere = nums[0];
    for (int i = 1; i < nums.length; i++) {
        maxEndingHere = Math.max(nums[i], maxEndingHere + nums[i]);
        maxSoFar = Math.max(maxSoFar, maxEndingHere);
    }
    return maxSoFar;
}
```

***

## 8. Climbing Stairs (Dynamic Programming)

**Problem:** You can climb 1 or 2 steps at a time. How many distinct ways to climb n steps?

**Solution:**

```java
public int climbStairs(int n) {
    if (n <= 2) return n;
    int one = 1, two = 2;
    for (int i = 3; i <= n; i++) {
        int temp = one + two;
        one = two;
        two = temp;
    }
    return two;
}
``` 

Here is an extended set of Java coding problems with solutions, including the two examples you mentioned, plus additional related problems common in coding interviews. Each problem includes a brief description and a Java-coded solution.

***

## 1. FizzBuzz

**Problem:** Print numbers from 1 to 100; multiples of 3 print "Fizz", multiples of 5 print "Buzz", multiples of both print "FizzBuzz".

```java
for (int i = 1; i <= 100; i++) {
    if (i % 15 == 0) {
        System.out.println("FizzBuzz");
    } else if (i % 3 == 0) {
        System.out.println("Fizz");
    } else if (i % 5 == 0) {
        System.out.println("Buzz");
    } else {
        System.out.println(i);
    }
}
```

***

## 2. Lowest Common Ancestor (BST)

**Problem:** Given two node values, find their Lowest Common Ancestor in a BST assuming both exist.

```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

public TreeNode lowestCommonAncestor(TreeNode root, int n1, int n2) {
    if (root == null) return null;
    if (root.val > n1 && root.val > n2) 
        return lowestCommonAncestor(root.left, n1, n2);
    if (root.val < n1 && root.val < n2)
        return lowestCommonAncestor(root.right, n1, n2);
    return root;
}
```

***

## 3. Check if String is Palindrome

```java
public boolean isPalindrome(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) return false;
        left++;
        right--;
    }
    return true;
}
```

***

## 4. Merge Two Sorted Linked Lists

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null) return l2;
    if (l2 == null) return l1;
    if (l1.val < l2.val) {
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    } else {
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
}
```

***

## 5. Binary Search in Sorted Array

```java
public int binarySearch(int[] nums, int target) {
    int low = 0, high = nums.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) low = mid + 1;
        else high = mid - 1;
    }
    return -1;
}
```

***

## 6. Find the kth Largest Element in Array

```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();
    for (int num : nums) {
        minHeap.offer(num);
        if (minHeap.size() > k) minHeap.poll();
    }
    return minHeap.peek();
}
```

***

## 7. Invert Binary Tree

```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    TreeNode left = invertTree(root.left);
    TreeNode right = invertTree(root.right);
    root.left = right;
    root.right = left;
    return root;
}
```

***

## 8. Valid Parentheses

**Problem:** Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

```java
public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    for (char c : s.toCharArray()) {
        if (c == '(') stack.push(')');
        else if (c == '{') stack.push('}');
        else if (c == '[') stack.push(']');
        else if (stack.isEmpty() || stack.pop() != c) return false;
    }
    return stack.isEmpty();
}
```

***

## 9. Count Number of Islands (DFS)

```java
public int numIslands(char[][] grid) {
    int count = 0;
    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[0].length; j++) {
            if (grid[i][j] == '1') {
                dfs(grid, i, j);
                count++;
            }
        }
    }
    return count;
}

private void dfs(char[][] grid, int i, int j) {
    if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == '0') return;
    grid[i][j] = '0';
    dfs(grid, i+1, j);
    dfs(grid, i-1, j);
    dfs(grid, i, j+1);
    dfs(grid, i, j-1);
}
```

***

## 10. Implement Queue Using Stacks

```java
class MyQueue {
    Stack<Integer> inStack = new Stack<>();
    Stack<Integer> outStack = new Stack<>();

    public void push(int x) {
        inStack.push(x);
    }

    public int pop() {
        if (outStack.isEmpty()) {
            while (!inStack.isEmpty()) outStack.push(inStack.pop());
        }
        return outStack.pop();
    }

    public int peek() {
        if (outStack.isEmpty()) {
            while (!inStack.isEmpty()) outStack.push(inStack.pop());
        }
        return outStack.peek();
    }

    public boolean empty() {
        return inStack.isEmpty() && outStack.isEmpty();
    }
}
```
 Absolutely! Here are several popular HackerRank-style problems with concise Java solutions. Each is a classic that helps demonstrate practical data structures, algorithm design, and coding skills.

***

## 1. **Two Sum**

**Problem:** Given an array of integers, return indices of the two numbers that add up to a specific target.

```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] { map.get(complement), i };
        }
        map.put(nums[i], i);
    }
    return new int[] {-1, -1}; // No solution
}
```

***

## 2. **Counting Valleys**

**Problem:** Given a string containing steps (`'U'` for up and `'D'` for down) during a hike, count the number of valleys (steps below sea level).

```java
public int countingValleys(int n, String s) {
    int seaLevel = 0, valleys = 0;
    for (char step : s.toCharArray()) {
        if (step == 'U') seaLevel++;
        else seaLevel--;
        if (seaLevel == 0 && step == 'U') valleys++;
    }
    return valleys;
}
```

***

## 3. **Jumping on the Clouds**

**Problem:** Given a sequence of clouds (safe: `0`, thunderhead: `1`), find the minimum number of jumps needed to reach the end, only jumping to safe clouds (at most 2 steps at a time).

```java
public int jumpingOnClouds(int[] c) {
    int jumps = 0, i = 0, n = c.length;
    while (i < n - 1) {
        if (i + 2 < n && c[i+2] == 0) i += 2;
        else i += 1;
        jumps++;
    }
    return jumps;
}
```

***

## 4. **Left Rotation**

**Problem:** Rotate the elements of an array to the left `d` times.

```java
public int[] rotLeft(int[] a, int d) {
    int n = a.length;
    int[] rotated = new int[n];
    for (int i = 0; i < n; i++) {
        rotated[i] = a[(i + d) % n];
    }
    return rotated;
}
```

***

## 5. **String Anagram**

**Problem:** Determine if two strings are anagrams (contain the same characters in any order).

```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    int[] count = new int[26];
    for (char c : s.toCharArray()) count[c - 'a']++;
    for (char c : t.toCharArray()) count[c - 'a']--;
    for (int i : count) if (i != 0) return false;
    return true;
}
```

***

## 6. **Maximum Hourglass Sum**

**Problem:** Find the max hourglass sum in a 6x6 2D array (each hourglass is shaped like:  
```
a b c
  d
e f g
```
)

```java
public int hourglassSum(int[][] arr) {
    int maxSum = Integer.MIN_VALUE;
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            int sum = arr[i][j] + arr[i][j+1] + arr[i][j+2] +
                      arr[i+1][j+1] +
                      arr[i+2][j] + arr[i+2][j+1] + arr[i+2][j+2];
            maxSum = Math.max(maxSum, sum);
        }
    }
    return maxSum;
}
``` 
Here are some of the **most asked LeetCode questions** with concise Java solutions, covering arrays, strings, trees, linked lists, and dynamic programming. These problems frequently appear in interviews at major tech companies:

***

## 1. Two Sum

```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] {map.get(complement), i};
        }
        map.put(nums[i], i);
    }
    return new int[] {-1, -1};
}
```

***

## 2. Best Time to Buy and Sell Stock

```java
public int maxProfit(int[] prices) {
    int min = Integer.MAX_VALUE, profit = 0;
    for (int price : prices) {
        if (price < min) min = price;
        else profit = Math.max(profit, price - min);
    }
    return profit;
}
```

***

## 3. Valid Parentheses

```java
public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    for (char c : s.toCharArray()) {
        if (c == '(') stack.push(')');
        else if (c == '{') stack.push('}');
        else if (c == '[') stack.push(']');
        else if (stack.isEmpty() || stack.pop() != c) return false;
    }
    return stack.isEmpty();
}
```

***

## 4. Merge Intervals

```java
public int[][] merge(int[][] intervals) {
    Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
    List<int[]> result = new ArrayList<>();
    for (int[] interval : intervals) {
        if (result.isEmpty() || result.get(result.size()-1)[1] < interval[0])
            result.add(interval);
        else
            result.get(result.size()-1)[1] = Math.max(result.get(result.size()-1)[1], interval[1]);
    }
    return result.toArray(new int[result.size()][]);
}
```

***

## 5. Longest Substring Without Repeating Characters

```java
public int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> map = new HashMap<>();
    int left = 0, maxLen = 0;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (map.containsKey(c)) {
            left = Math.max(left, map.get(c) + 1);
        }
        map.put(c, right);
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

***

## 6. Reverse Linked List

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode nextTemp = head.next;
        head.next = prev;
        prev = head;
        head = nextTemp;
    }
    return prev;
}
```

***

## 7. Maximum Depth of Binary Tree

```java
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

***

## 8. Remove Duplicates from Sorted Array

```java
public int removeDuplicates(int[] nums) {
    if (nums.length == 0) return 0;
    int i = 0;
    for (int j = 1; j < nums.length; j++) {
        if (nums[j] != nums[i]) {
            i++;
            nums[i] = nums[j];
        }
    }
    return i + 1;
}
```

***

## 9. Merge Two Sorted Lists

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null) return l2;
    if (l2 == null) return l1;
    if (l1.val < l2.val) {
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    } else {
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
}
```

***

## 10. Climbing Stairs (DP)

```java
public int climbStairs(int n) {
    if (n <= 2) return n;
    int one = 1, two = 2;
    for (int i = 3; i <= n; i++) {
        int temp = one + two;
        one = two;
        two = temp;
    }
    return two;
}
```
 Certainly! Here are additional widely-asked coding interview questions from LeetCode-type sites, with Java solutions. These expand coverage across arrays, strings, stacks, recursion, trees, and dynamic programming.

***

## 11. **Find All Duplicates in an Array**

**Problem:** Given an integer array where 1 ≤ a[i] ≤ n, some elements appear twice. Find all elements that appear twice.

```java
public List<Integer> findDuplicates(int[] nums) {
    List<Integer> result = new ArrayList<>();
    for (int num : nums) {
        int idx = Math.abs(num) - 1;
        if (nums[idx] < 0) result.add(Math.abs(num));
        nums[idx] = -nums[idx];
    }
    return result;
}
```

***

## 12. **Majority Element**

**Problem:** Find the element that appears more than ⌊n/2⌋ times.

```java
public int majorityElement(int[] nums) {
    int count = 0, candidate = 0;
    for (int num : nums) {
        if (count == 0) {
            candidate = num;
        }
        count += (num == candidate) ? 1 : -1;
    }
    return candidate;
}
```

***

## 13. **Contains Duplicate**

**Problem:** Check if any value appears at least twice in the array.

```java
public boolean containsDuplicate(int[] nums) {
    Set<Integer> seen = new HashSet<>();
    for (int num : nums) {
        if (!seen.add(num)) return true;
    }
    return false;
}
```

***

## 14. **Product of Array Except Self**

**Problem:** Without using division, return an array where result[i] is the product of all elements except nums[i].

```java
public int[] productExceptSelf(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    int left = 1;
    for (int i = 0; i < n; i++) {
        result[i] = left;
        left *= nums[i];
    }
    int right = 1;
    for (int i = n - 1; i >= 0; i--) {
        result[i] *= right;
        right *= nums[i];
    }
    return result;
}
```

***

## 15. **Rotate Array**

**Problem:** Rotate array to the right by k steps.

```java
public void rotate(int[] nums, int k) {
    k = k % nums.length;
    reverse(nums, 0, nums.length - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, nums.length - 1);
}

private void reverse(int[] nums, int start, int end) {
    while (start < end) {
        int temp = nums[start];
        nums[start++] = nums[end];
        nums[end--] = temp;
    }
}
```

***

## 16. **Valid Palindrome**

**Problem:** Check if a string is a palindrome, ignoring non-alphanumeric characters and case.

```java
public boolean isPalindrome(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        while (left < right && !Character.isLetterOrDigit(s.charAt(left))) left++;
        while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;
        if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) return false;
        left++;
        right--;
    }
    return true;
}
```

***

## 17. **Minimum Depth of Binary Tree**

```java
public int minDepth(TreeNode root) {
    if (root == null) return 0;
    if (root.left == null) return 1 + minDepth(root.right);
    if (root.right == null) return 1 + minDepth(root.left);
    return 1 + Math.min(minDepth(root.left), minDepth(root.right));
}
```

***

## 18. **Symmetric Tree**

**Problem:** Check if a binary tree is a mirror of itself (symmetric).

```java
public boolean isSymmetric(TreeNode root) {
    return root == null || isMirror(root.left, root.right);
}

private boolean isMirror(TreeNode t1, TreeNode t2) {
    if (t1 == null || t2 == null) return t1 == t2;
    return t1.val == t2.val && isMirror(t1.left, t2.right) && isMirror(t1.right, t2.left);
}
```

***

## 19. **Merge Sorted Array**

**Problem:** Merge nums2 into nums1 as one sorted array (nums1 has extra space at end).

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int i = m - 1, j = n - 1, k = m + n - 1;
    while (i >= 0 && j >= 0) {
        nums1[k--] = nums1[i] > nums2[j] ? nums1[i--] : nums2[j--];
    }
    while (j >= 0) {
        nums1[k--] = nums2[j--];
    }
}
```

***

## 20. **Substring Search (strStr)**

**Problem:** Return the index of the first occurrence of needle in haystack, or -1 if not found.

```java
public int strStr(String haystack, String needle) {
    return haystack.indexOf(needle);
}
```

*** 
Here are some **Oracle programming questions and answers** commonly asked for Java developer roles, covering both conceptual and coding questions likely to appear in Oracle technical interviews:

***

### 1. **How do you connect to an Oracle database using JDBC?**

**Answer:**
```java
Connection connection = DriverManager.getConnection(
    "jdbc:oracle:thin:@localhost:1521:xe", "username", "password");
```
Steps:
- Load the Oracle JDBC driver.
- Use `DriverManager.getConnection()` with the Oracle connection string.
- Always close the connection, statement, and result set.

***

### 2. **What is the difference between ArrayList, LinkedList, and Vector in Java?**

**Answer:**
- **ArrayList:** Dynamic array, fast random access, not thread-safe.
- **LinkedList:** Doubly-linked list, fast insert/delete at ends/middle, slower random access.
- **Vector:** Synchronized version of ArrayList (thread-safe, legacy).

***

### 3. **How do you implement thread-safe collections in Java?**

**Answer:**  
Use concurrent collections like `ConcurrentHashMap`, `CopyOnWriteArrayList`, or wrap collections using `Collections.synchronizedList()`. Prefer concurrent collections for better scalability.

***

### 4. **Write a Java program to find duplicate elements in an array.**

```java
public void printDuplicates(int[] arr) {
    Set<Integer> seen = new HashSet<>();
    Set<Integer> duplicates = new HashSet<>();
    for (int num : arr) {
        if (!seen.add(num)) {
            duplicates.add(num);
        }
    }
    for (int dup : duplicates) {
        System.out.println(dup);
    }
}
```

***

### 5. **What is the difference between HashMap and TreeMap?**

**Answer:**
- **HashMap:** Unordered, constant-time ops, allows null keys, does not sort.
- **TreeMap:** Sorted by keys (red-black tree), log(n) operations, no null key, slower for basic ops.

***

### 6. **Explain Exception Hierarchy in Java and write code for a custom exception.**

**Answer:**
Exceptions are divided into checked (`Exception`), unchecked (`RuntimeException`), and errors (`Error`).

**Custom Exception Example:**
```java
public class InvalidInputException extends Exception {
    public InvalidInputException(String message) {
        super(message);
    }
}
```
Usage:
```java
if (input < 0) throw new InvalidInputException("Input must be positive");
```

***

### 7. **Describe how you would profile and optimize a Java application.**

**Answer:**
- Use profiling tools (JProfiler, VisualVM, YourKit).
- Monitor CPU, memory, GC, I/O, network.
- Identify bottlenecks; optimize data structures, reduce object creation, strengthen multithreading.

***

### 8. **Write a code snippet to reverse a singly linked list.**

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode nextTemp = head.next;
        head.next = prev;
        prev = head;
        head = nextTemp;
    }
    return prev;
}
```

***

### 9. **How do you implement unit testing in Java?**

**Answer:**
- Use frameworks like JUnit or TestNG.
- Annotate test methods with `@Test`.

Example:
```java
@Test
public void testAddition() {
    assertEquals(5, Math.addExact(2, 3));
}
```

***

### 10. **How do you handle SQL exceptions in JDBC?**

**Answer:**
Use try-catch blocks and always close connections in a finally block or using try-with-resources:
```java
try (Connection conn = ...; Statement stmt = ...;) {
    // SQL operations
} catch (SQLException e) {
    e.printStackTrace();
}
```

***
 