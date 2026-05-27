# Performance and Big-O Analysis - Complete Guide

## What is Big-O Notation?

**Simple Definition:**
Big-O describes how the time or space required by an algorithm grows as the input size increases.

**Real-World Analogy:**
Imagine finding a book in a library:
- **O(1)** - You know the exact shelf and position (direct access)
- **O(log n)** - You use the catalog system (binary search)
- **O(n)** - You check every shelf one by one (linear search)
- **O(n²)** - You compare every book with every other book

---

## Common Big-O Complexities

### From Best to Worst

```
O(1)        - Constant      - Best
O(log n)    - Logarithmic   - Excellent
O(n)        - Linear        - Good
O(n log n)  - Linearithmic  - Fair
O(n²)       - Quadratic     - Bad
O(2^n)      - Exponential   - Terrible
O(n!)       - Factorial     - Worst
```

### Visual Comparison

```
For n = 100:
O(1)      = 1 operation
O(log n)  = 7 operations
O(n)      = 100 operations
O(n log n)= 700 operations
O(n²)     = 10,000 operations
O(2^n)    = 1,267,650,600,228,229,401,496,703,205,376 operations
```

---

## Complete Performance Comparison

### List Implementations

| Operation | ArrayList | LinkedList | Vector | CopyOnWriteArrayList |
|-----------|-----------|------------|--------|---------------------|
| **get(index)** | O(1) | O(n) | O(1) | O(1) |
| **add(element)** | O(1)* | O(1) | O(1)* | O(n) |
| **add(index, element)** | O(n) | O(n) | O(n) | O(n) |
| **remove(index)** | O(n) | O(n) | O(n) | O(n) |
| **remove(element)** | O(n) | O(n) | O(n) | O(n) |
| **contains(element)** | O(n) | O(n) | O(n) | O(n) |
| **iterator.remove()** | O(n) | O(1) | O(n) | N/A |
| **Memory** | Low | High | Low | High |

*Amortized O(1) - occasionally O(n) when resizing

### Set Implementations

| Operation | HashSet | LinkedHashSet | TreeSet | EnumSet |
|-----------|---------|---------------|---------|---------|
| **add(element)** | O(1) | O(1) | O(log n) | O(1) |
| **remove(element)** | O(1) | O(1) | O(log n) | O(1) |
| **contains(element)** | O(1) | O(1) | O(log n) | O(1) |
| **Iteration** | O(n) | O(n) | O(n) | O(n) |
| **Order** | None | Insertion | Sorted | Natural |
| **Memory** | Medium | High | High | Very Low |

### Map Implementations

| Operation | HashMap | LinkedHashMap | TreeMap | Hashtable | ConcurrentHashMap |
|-----------|---------|---------------|---------|-----------|-------------------|
| **get(key)** | O(1) | O(1) | O(log n) | O(1) | O(1) |
| **put(key, value)** | O(1) | O(1) | O(log n) | O(1) | O(1) |
| **remove(key)** | O(1) | O(1) | O(log n) | O(1) | O(1) |
| **containsKey(key)** | O(1) | O(1) | O(log n) | O(1) | O(1) |
| **Iteration** | O(n) | O(n) | O(n) | O(n) | O(n) |
| **Thread-Safe** | No | No | No | Yes | Yes |
| **Null Keys** | Yes | Yes | No | No | No |

### Queue/Deque Implementations

| Operation | ArrayDeque | LinkedList | PriorityQueue |
|-----------|------------|------------|---------------|
| **offer(element)** | O(1)* | O(1) | O(log n) |
| **poll()** | O(1) | O(1) | O(log n) |
| **peek()** | O(1) | O(1) | O(1) |
| **remove(element)** | O(n) | O(n) | O(n) |
| **Memory** | Low | High | Medium |

---

## Detailed Analysis by Collection

### ArrayList

**Internal Structure:** Dynamic array

**Time Complexity:**

```java
List<String> list = new ArrayList<>();

// O(1) - Direct array access
String element = list.get(5);

// O(1) amortized - Add at end
list.add("new");
// Occasionally O(n) when array needs resizing

// O(n) - Add at beginning (shift all elements)
list.add(0, "first");

// O(n) - Remove from middle (shift elements)
list.remove(5);

// O(n) - Search for element
boolean found = list.contains("search");

// O(n log n) - Sorting
Collections.sort(list);
```

**Why O(1) for get()?**
```
Array: [A, B, C, D, E]
Index:  0  1  2  3  4

get(2):
1. Calculate memory address: base + (2 * element_size)
2. Direct access to memory location
3. Return element

No loops, no searching - constant time!
```

**Why O(n) for add(0)?**
```
Before: [A, B, C, D, E]
Add X at index 0:

Step 1: Shift all elements right
[_, A, B, C, D, E]
 ↑  ↑  ↑  ↑  ↑  ↑
 5  4  3  2  1  0 shifts

Step 2: Insert X
[X, A, B, C, D, E]

Total operations: n (where n = size)
```

**Space Complexity:**
- Best case: O(n) - when size equals capacity
- Worst case: O(2n) - just after resizing (old + new array)
- Average: O(1.5n) - considering 50% growth factor

---

### LinkedList

**Internal Structure:** Doubly-linked list

**Time Complexity:**

```java
LinkedList<String> list = new LinkedList<>();

// O(n) - Must traverse from head or tail
String element = list.get(5);

// O(1) - Add at beginning
list.addFirst("first");

// O(1) - Add at end
list.addLast("last");

// O(n) - Add at middle (must traverse to position)
list.add(5, "middle");

// O(1) - Remove first
list.removeFirst();

// O(1) - Remove last
list.removeLast();

// O(n) - Remove from middle
list.remove(5);

// O(n) - Search
boolean found = list.contains("search");
```

**Why O(n) for get()?**
```
LinkedList: [A] <-> [B] <-> [C] <-> [D] <-> [E]
            head                            tail

get(3):
1. Start from head
2. Follow next pointers: A -> B -> C -> D
3. Return D

Must traverse 3 nodes - linear time!

Optimization: If index > size/2, start from tail
```

**Why O(1) for addFirst()?**
```
Before: [A] <-> [B] <-> [C]
        head

Add X at beginning:
1. Create new node X
2. X.next = head
3. head.prev = X
4. head = X

[X] <-> [A] <-> [B] <-> [C]
head

Only 3 operations - constant time!
```

**Space Complexity:**
- O(n) for n elements
- Each node has extra overhead (prev, next pointers)
- ~3x memory of ArrayList per element

---

### HashMap

**Internal Structure:** Array of buckets + Linked List/Tree

**Time Complexity:**

```java
Map<String, Integer> map = new HashMap<>();

// O(1) average - Direct bucket access + small chain
Integer value = map.get("key");

// O(1) average - Hash + bucket access
map.put("key", 100);

// O(1) average - Hash + bucket access
map.remove("key");

// O(1) average
boolean exists = map.containsKey("key");

// O(n) - Must check all values
boolean hasValue = map.containsValue(100);

// O(n) - Iterate all entries
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    // Process entry
}
```

**Why O(1) average?**
```
HashMap: [Bucket Array]
         [0] -> null
         [1] -> Entry("A", 1)
         [2] -> Entry("B", 2) -> Entry("C", 3)  [collision]
         [3] -> null
         ...

get("B"):
1. Calculate hash: "B".hashCode() = 66
2. Calculate bucket: 66 % capacity = 2
3. Go to bucket[2]
4. Walk chain: Entry("B") found (1-2 comparisons)

Total: O(1) hash + O(1) bucket access + O(1) chain walk = O(1)
```

**Worst Case: O(n) or O(log n)**
```
All keys in same bucket (bad hash function):

Java 7:
Bucket[5] -> [E1] -> [E2] -> ... -> [E1000]
Search: O(1000) = O(n)

Java 8+ (after 8 entries):
Bucket[5] ->      [E500]
                 /      \
            [E250]      [E750]
            /    \      /     \
          ...    ...  ...    ...

Search: O(log 1000) = O(10)
```

**Space Complexity:**
- O(n) for n entries
- Additional space for buckets (capacity)
- Load factor 0.75: ~1.33n space

---

### TreeSet/TreeMap

**Internal Structure:** Red-Black Tree (self-balancing BST)

**Time Complexity:**

```java
TreeSet<Integer> set = new TreeSet<>();

// O(log n) - Tree traversal
set.add(50);

// O(log n) - Tree traversal
boolean found = set.contains(50);

// O(log n) - Tree traversal
set.remove(50);

// O(1) - Direct access to tree root
Integer first = set.first();

// O(1) - Direct access to tree root
Integer last = set.last();

// O(log n) - Tree traversal
Integer floor = set.floor(45);

// O(n) - In-order traversal
for (Integer num : set) {
    // Process num
}
```

**Why O(log n)?**
```
TreeSet with 15 elements (height = 4):

              8
           /     \
          4       12
         / \     /  \
        2   6   10  14
       /|   |\  |\  |\
      1 3  5 7 9 11 13 15

Search for 13:
1. Start at root (8)
2. 13 > 8, go right (12)
3. 13 > 12, go right (14)
4. 13 < 14, go left (13)
5. Found!

Steps: 4 = log₂(15) ≈ 4
```

**Space Complexity:**
- O(n) for n elements
- Each node has: element, left, right, parent, color
- ~5x overhead per element

---

## When to Use Which Collection

### Decision Tree

```
Need key-value pairs?
├─ Yes → Map
│  ├─ Order doesn't matter? → HashMap
│  ├─ Insertion order? → LinkedHashMap
│  ├─ Sorted order? → TreeMap
│  └─ Thread-safe? → ConcurrentHashMap
│
└─ No → Need duplicates?
   ├─ Yes → List
   │  ├─ Random access? → ArrayList
   │  ├─ Frequent add/remove at ends? → LinkedList
   │  └─ Thread-safe? → CopyOnWriteArrayList
   │
   └─ No → Set
      ├─ Order doesn't matter? → HashSet
      ├─ Insertion order? → LinkedHashSet
      ├─ Sorted order? → TreeSet
      └─ Enum type? → EnumSet
```

### Performance-Based Selection

**Need O(1) access by index?**
→ ArrayList

**Need O(1) add/remove at ends?**
→ LinkedList or ArrayDeque

**Need O(1) lookup by key?**
→ HashMap

**Need O(1) uniqueness check?**
→ HashSet

**Need sorted order with O(log n) operations?**
→ TreeSet or TreeMap

**Need thread-safety with good performance?**
→ ConcurrentHashMap or CopyOnWriteArrayList

---

## Real-World Scenarios

### Scenario 1: User Session Management

**Requirement:** Store user sessions, fast lookup by session ID

**Analysis:**
- Lookup by ID: Need O(1)
- No ordering required
- Frequent reads and writes

**Best Choice:** HashMap
```java
Map<String, UserSession> sessions = new HashMap<>();

// O(1) - Fast lookup
UserSession session = sessions.get(sessionId);

// O(1) - Fast insert
sessions.put(newSessionId, newSession);

// O(1) - Fast remove
sessions.remove(expiredSessionId);
```

### Scenario 2: Product Catalog

**Requirement:** Store products, display in sorted order by price

**Analysis:**
- Need sorted order
- Frequent searches
- Occasional updates

**Best Choice:** TreeMap
```java
TreeMap<Double, Product> catalog = new TreeMap<>();

// O(log n) - Insert in sorted position
catalog.put(product.getPrice(), product);

// O(log n) - Find product by price
Product p = catalog.get(99.99);

// O(n) - Get all products in price order (already sorted)
for (Product product : catalog.values()) {
    display(product);
}

// O(log n) - Get products in price range
SortedMap<Double, Product> range = catalog.subMap(50.0, 100.0);
```

### Scenario 3: Recent Activity Feed

**Requirement:** Store recent activities, maintain insertion order

**Analysis:**
- Need insertion order
- Frequent additions
- Occasional removals
- No duplicates

**Best Choice:** LinkedHashSet
```java
LinkedHashSet<Activity> recentActivities = new LinkedHashSet<>();

// O(1) - Add activity (maintains order)
recentActivities.add(newActivity);

// O(1) - Check if activity exists
boolean exists = recentActivities.contains(activity);

// O(n) - Display in insertion order
for (Activity activity : recentActivities) {
    display(activity);
}

// Limit to last 100 activities
if (recentActivities.size() > 100) {
    Iterator<Activity> it = recentActivities.iterator();
    it.next();
    it.remove();  // Remove oldest
}
```

### Scenario 4: Leaderboard

**Requirement:** Store player scores, display top 10

**Analysis:**
- Need sorted order (by score)
- Frequent updates
- Need top N efficiently

**Best Choice:** TreeMap (score to player)
```java
TreeMap<Integer, String> leaderboard = new TreeMap<>(Collections.reverseOrder());

// O(log n) - Update score
leaderboard.put(player.getScore(), player.getName());

// O(1) - Get top player
Map.Entry<Integer, String> top = leaderboard.firstEntry();

// O(k) - Get top 10 (k=10)
List<String> top10 = leaderboard.values().stream()
    .limit(10)
    .collect(Collectors.toList());
```

### Scenario 5: Cache Implementation

**Requirement:** LRU cache with fixed size

**Analysis:**
- Need access order
- Fast lookup
- Automatic eviction

**Best Choice:** LinkedHashMap with access order
```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);  // accessOrder = true
        this.capacity = capacity;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}

// O(1) - Get (moves to end)
V value = cache.get(key);

// O(1) - Put (auto-evicts if full)
cache.put(key, value);
```

---

## Common Interview Questions

### Q1: Why is ArrayList get() O(1) but LinkedList get() O(n)?

**Answer:**
"ArrayList uses a dynamic array internally. Arrays provide direct access to any index by calculating the memory address: base_address + (index × element_size). This is a simple arithmetic operation, making it O(1).

LinkedList uses a doubly-linked list where each node points to the next and previous nodes. To access an element at index i, you must traverse from the head (or tail) following the pointers, which requires i steps, making it O(n).

However, LinkedList optimizes by starting from the tail if the index is greater than size/2, but it's still O(n/2) which simplifies to O(n)."

### Q2: When would you use LinkedList over ArrayList?

**Answer:**
"Use LinkedList when you need:
1. Frequent insertions/deletions at the beginning or end (O(1) vs O(n))
2. Implementation of Queue or Deque
3. No random access needed

Use ArrayList when you need:
1. Frequent random access by index (O(1) vs O(n))
2. Iteration over elements
3. Memory efficiency (LinkedList uses ~3x memory per element)

In practice, ArrayList is preferred in most cases because:
- Modern CPUs cache arrays better (cache locality)
- Random access is more common than insertions at ends
- Memory efficiency matters"

### Q3: Why is HashMap O(1) and not O(n)?

**Answer:**
"HashMap achieves O(1) average time through hashing:

1. **Direct bucket access**: Hash code is converted to bucket index using modulo operation, which is O(1)
2. **Good hash distribution**: With a good hash function and load factor 0.75, most buckets have 0-2 entries
3. **Small chain traversal**: Even with collisions, the chain is typically very short

The worst case is O(n) when all keys hash to the same bucket (poor hash function), but this is rare with proper implementation. Java 8+ improves worst case to O(log n) by converting long chains to trees.

The key is that the number of operations doesn't grow linearly with the total number of entries - it depends on the bucket size, which remains small with good hashing."

### Q4: What's the time complexity of sorting different collections?

**Answer:**
"Sorting time complexity depends on the algorithm and data structure:

**ArrayList/Array:**
- Collections.sort(): O(n log n) - Uses TimSort (hybrid of merge sort and insertion sort)
- Worst case: O(n log n)
- Best case: O(n) for nearly sorted data

**LinkedList:**
- Collections.sort(): O(n log n) - Converts to array, sorts, converts back
- Additional O(n) overhead for conversions

**TreeSet/TreeMap:**
- Already sorted: O(1) to iterate in order
- Building from unsorted: O(n log n) - each insertion is O(log n)

**Stream API:**
- stream().sorted(): O(n log n) - Uses same algorithm as Collections.sort()

**Recommendation:** For sorting, ArrayList is most efficient due to cache locality and no conversion overhead."

### Q5: How does load factor affect HashMap performance?

**Answer:**
"Load factor determines when HashMap resizes. Default is 0.75 (75% full).

**Lower load factor (e.g., 0.5):**
- Pros: Fewer collisions, faster lookups
- Cons: More memory usage, more frequent resizing

**Higher load factor (e.g., 0.9):**
- Pros: Better memory utilization, less frequent resizing
- Cons: More collisions, slower lookups

**0.75 is optimal because:**
- Balances memory and performance
- Keeps collision probability low
- Minimizes resize frequency
- Based on statistical analysis (Poisson distribution)

**Example:**
```
Capacity = 16, Load Factor = 0.75
Threshold = 12

After 12 entries: Resize to 32
After 24 entries: Resize to 64

With Load Factor = 0.5:
After 8 entries: Resize to 32
After 16 entries: Resize to 64
More resizing, more memory
```"

### Q6: What's the space complexity of different collections?

**Answer:**
"Space complexity for n elements:

**ArrayList:**
- O(n) to O(2n) depending on capacity
- Minimal overhead per element
- Best memory efficiency

**LinkedList:**
- O(n) but with high constant factor
- Each node: element + 2 pointers (prev, next)
- ~3x memory of ArrayList

**HashMap:**
- O(n) to O(1.33n) with load factor 0.75
- Each entry: key + value + hash + next pointer
- Additional bucket array

**TreeSet/TreeMap:**
- O(n) with high constant factor
- Each node: element + 3 pointers (left, right, parent) + color
- ~5x overhead per element

**CopyOnWriteArrayList:**
- O(n) but creates copy on write
- Temporarily O(2n) during modification

**Recommendation:** Use ArrayList for memory-constrained scenarios."

### Q7: How do you optimize collection performance?

**Answer:**
"Several optimization strategies:

**1. Choose right collection:**
```java
// Bad: LinkedList for random access
List<String> list = new LinkedList<>();
for (int i = 0; i < 1000; i++) {
    list.get(i);  // O(n) each time!
}

// Good: ArrayList for random access
List<String> list = new ArrayList<>();
for (int i = 0; i < 1000; i++) {
    list.get(i);  // O(1) each time
}
```

**2. Set initial capacity:**
```java
// Bad: Multiple resizing
List<String> list = new ArrayList<>();
for (int i = 0; i < 10000; i++) {
    list.add("item");  // Resizes multiple times
}

// Good: Pre-allocate
List<String> list = new ArrayList<>(10000);
for (int i = 0; i < 10000; i++) {
    list.add("item");  // No resizing
}
```

**3. Use appropriate methods:**
```java
// Bad: Remove during iteration
for (String item : list) {
    if (condition) {
        list.remove(item);  // ConcurrentModificationException
    }
}

// Good: Use removeIf
list.removeIf(item -> condition);  // Efficient single pass
```

**4. Batch operations:**
```java
// Bad: Multiple individual operations
for (String item : items) {
    list.add(item);
}

// Good: Batch operation
list.addAll(items);  // More efficient
```"

### Q8: What's the difference between O(1) and O(1) amortized?

**Answer:**
"**O(1)** means every single operation takes constant time.

**O(1) amortized** means operations take constant time on average, but some individual operations might take longer.

**Example: ArrayList.add()**
```
Capacity = 4, Size = 3

add("A"): O(1) - Just place in array
add("B"): O(1) - Just place in array  
add("C"): O(1) - Just place in array
add("D"): O(4) - Must resize! Copy 4 elements to new array

Average: (1 + 1 + 1 + 4) / 4 = 1.75 ≈ O(1) amortized
```

Over many operations, the expensive resize operations are rare enough that the average is still constant time.

**Real-world analogy:**
Like paying rent monthly. Most days you pay nothing (O(1)), but once a month you pay a large amount (O(n)). On average per day, it's still a small constant amount (O(1) amortized)."

### Q9: How do you analyze the time complexity of nested loops?

**Answer:**
"Multiply the complexities of each loop:

**Example 1: Simple nested loops**
```java
for (int i = 0; i < n; i++) {           // O(n)
    for (int j = 0; j < n; j++) {       // O(n)
        System.out.println(i + j);       // O(1)
    }
}
// Total: O(n) × O(n) × O(1) = O(n²)
```

**Example 2: Dependent loops**
```java
for (int i = 0; i < n; i++) {           // O(n)
    for (int j = i; j < n; j++) {       // O(n-i)
        System.out.println(i + j);
    }
}
// Iterations: n + (n-1) + (n-2) + ... + 1 = n(n+1)/2 = O(n²)
```

**Example 3: With collection operations**
```java
for (String item : list) {              // O(n)
    if (set.contains(item)) {           // O(1) for HashSet
        map.put(item, value);           // O(1) for HashMap
    }
}
// Total: O(n) × O(1) × O(1) = O(n)
```

**Example 4: Logarithmic inner loop**
```java
for (int i = 0; i < n; i++) {           // O(n)
    int j = 1;
    while (j < n) {                      // O(log n)
        j = j * 2;
    }
}
// Total: O(n) × O(log n) = O(n log n)
```"

### Q10: What's the time complexity of common Stream operations?

**Answer:**
"Stream operations have different complexities:

**Intermediate Operations (lazy):**
```java
stream.filter(predicate)     // O(n) when executed
stream.map(function)         // O(n) when executed
stream.distinct()            // O(n) when executed
stream.sorted()              // O(n log n) when executed
stream.limit(k)              // O(k) when executed
stream.skip(k)               // O(k) when executed
```

**Terminal Operations:**
```java
stream.forEach(action)       // O(n)
stream.count()               // O(n)
stream.collect()             // O(n)
stream.reduce()              // O(n)
stream.anyMatch()            // O(n) worst case, can short-circuit
stream.findFirst()           // O(1) to O(n), can short-circuit
```

**Combined Example:**
```java
list.stream()                           // O(1)
    .filter(x -> x > 10)                // O(n)
    .map(x -> x * 2)                    // O(n)
    .sorted()                           // O(n log n)
    .limit(10)                          // O(10)
    .collect(Collectors.toList());      // O(10)

// Total: O(n log n) - dominated by sorted()
```

**Parallel Streams:**
```java
list.parallelStream()
    .filter(predicate)
    .collect(Collectors.toList());

// Time: O(n/p) where p = number of processors
// But has overhead, only beneficial for large n
```"

---

## Performance Optimization Tips

### 1. Choose Right Collection

```java
// Scenario: Frequent random access
// Bad
List<String> list = new LinkedList<>();  // O(n) access
String item = list.get(1000);

// Good
List<String> list = new ArrayList<>();   // O(1) access
String item = list.get(1000);
```

### 2. Set Initial Capacity

```java
// Bad: Multiple resizing
Map<String, Integer> map = new HashMap<>();
for (int i = 0; i < 10000; i++) {
    map.put("key" + i, i);
}

// Good: Pre-allocate
Map<String, Integer> map = new HashMap<>(10000);
for (int i = 0; i < 10000; i++) {
    map.put("key" + i, i);
}
```

### 3. Use Bulk Operations

```java
// Bad: Individual operations
for (String item : items) {
    list.add(item);
}

// Good: Bulk operation
list.addAll(items);
```

### 4. Avoid Unnecessary Boxing

```java
// Bad: Boxing overhead
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 1000000; i++) {
    list.add(i);  // Boxing int to Integer
}

// Better: Use primitive streams
int[] array = IntStream.range(0, 1000000).toArray();
```

### 5. Use Right Iterator

```java
// Bad: Index-based iteration on LinkedList
for (int i = 0; i < list.size(); i++) {
    process(list.get(i));  // O(n) for each get!
}

// Good: Iterator-based
for (String item : list) {
    process(item);  // O(1) for each
}
```

---

## Summary

**Key Takeaways:**

**Time Complexity:**
- ArrayList: O(1) access, O(n) insert/delete
- LinkedList: O(n) access, O(1) insert/delete at ends
- HashMap: O(1) average for all operations
- TreeSet/TreeMap: O(log n) for all operations

**Space Complexity:**
- ArrayList: Most memory efficient
- LinkedList: ~3x overhead per element
- HashMap: ~1.33x with load factor 0.75
- TreeSet/TreeMap: ~5x overhead per element

**Selection Criteria:**
- Random access → ArrayList
- Frequent add/remove at ends → LinkedList
- Fast lookup → HashMap
- Sorted order → TreeSet/TreeMap
- Thread-safety → Concurrent collections

**Optimization:**
- Set initial capacity
- Choose right collection
- Use bulk operations
- Avoid unnecessary boxing
- Use appropriate iterators

**Interview Tips:**
- Always mention average vs worst case
- Explain why, not just what
- Give real-world examples
- Know trade-offs
- Understand amortized complexity

Master these concepts and you'll ace any performance-related interview question!