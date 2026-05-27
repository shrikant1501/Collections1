# Concurrent Collections - Complete Guide

## Simple Explanation

### What are Concurrent Collections?

**Concurrent Collections** = Thread-safe collections designed for high-performance multi-threaded environments

**Real-World Analogy:**
Think of a restaurant:
- **Regular Collection** = Single cashier handling all orders (one at a time)
- **Synchronized Collection** = Single cashier with a lock (still one at a time, but thread-safe)
- **Concurrent Collection** = Multiple cashiers handling orders simultaneously (true parallelism)

**Key Difference:**
- Synchronized Collections: Lock entire collection for each operation
- Concurrent Collections: Allow multiple threads to access different parts simultaneously

---

## Why Concurrent Collections?

### Problems with Regular Collections

```java
// NOT thread-safe - Can cause data corruption
Map<String, Integer> map = new HashMap<>();

// Thread 1
map.put("A", 1);

// Thread 2 (simultaneously)
map.put("B", 2);

// Result: Unpredictable behavior, data corruption, or infinite loop!
```

### Problems with Synchronized Collections

```java
// Thread-safe but slow
Map<String, Integer> map = Collections.synchronizedMap(new HashMap<>());

// Thread 1 - Locks entire map
map.put("A", 1);

// Thread 2 - Must wait even though accessing different key
map.put("B", 2);  // Blocked!

// Result: Thread-safe but poor performance
```

### Solution: Concurrent Collections

```java
// Thread-safe AND fast
Map<String, Integer> map = new ConcurrentHashMap<>();

// Thread 1 - Locks only segment
map.put("A", 1);

// Thread 2 - Can proceed if different segment
map.put("B", 2);  // Not blocked!

// Result: Thread-safe with good performance
```

---

## Types of Concurrent Collections

### 1. ConcurrentHashMap
### 2. CopyOnWriteArrayList
### 3. CopyOnWriteArraySet
### 4. ConcurrentLinkedQueue
### 5. ConcurrentLinkedDeque
### 6. BlockingQueue Implementations
### 7. ConcurrentSkipListMap
### 8. ConcurrentSkipListSet

---

## 1. ConcurrentHashMap

### Overview

Thread-safe HashMap with better performance than synchronized HashMap.

**Key Features:**
- Lock striping (segments)
- No locking for reads
- Atomic operations
- Null keys/values NOT allowed

### Basic Usage

```java
Map<String, Integer> map = new ConcurrentHashMap<>();

// Thread-safe operations
map.put("A", 1);
map.get("A");
map.remove("A");
map.putIfAbsent("B", 2);
```

### Internal Working

**Before Java 8:**
- Divided into 16 segments (by default)
- Each segment is independently locked
- Multiple threads can access different segments simultaneously

**Java 8+:**
- Uses CAS (Compare-And-Swap) operations
- Node-level locking instead of segment locking
- Better scalability

### Important Methods

#### putIfAbsent(K key, V value)

Adds entry only if key doesn't exist.

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

map.putIfAbsent("A", 1);  // Returns null, adds entry
System.out.println(map);  // {A=1}

map.putIfAbsent("A", 2);  // Returns 1, doesn't add
System.out.println(map);  // {A=1}
```

#### computeIfAbsent(K key, Function mappingFunction)

Computes value if key is absent.

```java
ConcurrentHashMap<String, List<String>> map = new ConcurrentHashMap<>();

// Add to list, create list if doesn't exist
map.computeIfAbsent("fruits", k -> new ArrayList<>()).add("Apple");
map.computeIfAbsent("fruits", k -> new ArrayList<>()).add("Banana");

System.out.println(map);
// {fruits=[Apple, Banana]}
```

#### computeIfPresent(K key, BiFunction remappingFunction)

Computes new value if key exists.

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);

// Increment if exists
map.computeIfPresent("A", (k, v) -> v + 1);
System.out.println(map);  // {A=2}

map.computeIfPresent("B", (k, v) -> v + 1);  // Does nothing
System.out.println(map);  // {A=2}
```

#### compute(K key, BiFunction remappingFunction)

Computes value regardless of whether key exists.

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);

// Always computes
map.compute("A", (k, v) -> v == null ? 1 : v + 1);
System.out.println(map);  // {A=2}

map.compute("B", (k, v) -> v == null ? 1 : v + 1);
System.out.println(map);  // {A=2, B=1}
```

#### merge(K key, V value, BiFunction remappingFunction)

Merges value with existing value.

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);

// Merge with existing value
map.merge("A", 5, (oldVal, newVal) -> oldVal + newVal);
System.out.println(map);  // {A=6}

// If key doesn't exist, just put the value
map.merge("B", 10, (oldVal, newVal) -> oldVal + newVal);
System.out.println(map);  // {A=6, B=10}
```

### Real-World Example: Word Counter

```java
class WordCounter {
    private ConcurrentHashMap<String, Integer> wordCount = new ConcurrentHashMap<>();
    
    public void countWord(String word) {
        // Thread-safe increment
        wordCount.merge(word, 1, Integer::sum);
    }
    
    public int getCount(String word) {
        return wordCount.getOrDefault(word, 0);
    }
    
    public Map<String, Integer> getTopWords(int n) {
        return wordCount.entrySet().stream()
            .sorted((e1, e2) -> e2.getValue() - e1.getValue())
            .limit(n)
            .collect(Collectors.toMap(
                Map.Entry::getKey,
                Map.Entry::getValue,
                (e1, e2) -> e1,
                LinkedHashMap::new
            ));
    }
}

// Usage with multiple threads
WordCounter counter = new WordCounter();

// Thread 1
new Thread(() -> {
    counter.countWord("hello");
    counter.countWord("world");
    counter.countWord("hello");
}).start();

// Thread 2
new Thread(() -> {
    counter.countWord("hello");
    counter.countWord("java");
}).start();

Thread.sleep(100);
System.out.println(counter.getTopWords(3));
// {hello=3, world=1, java=1}
```

### ConcurrentHashMap vs HashMap vs Hashtable

| Feature | HashMap | Hashtable | ConcurrentHashMap |
|---------|---------|-----------|-------------------|
| **Thread-Safe** | No | Yes | Yes |
| **Null Key** | Allowed (one) | Not allowed | Not allowed |
| **Null Value** | Allowed | Not allowed | Not allowed |
| **Performance** | Fast | Slow | Fast |
| **Locking** | No locking | Locks entire table | Lock striping |
| **Iteration** | Fail-fast | Fail-fast | Weakly consistent |
| **Since** | Java 1.2 | Java 1.0 | Java 1.5 |

---

## 2. CopyOnWriteArrayList

### Overview

Thread-safe ArrayList where all mutative operations create a new copy of the underlying array.

**Key Features:**
- No locking for reads
- Expensive writes (creates copy)
- Iterator never throws ConcurrentModificationException
- Best for read-heavy workloads

### Basic Usage

```java
List<String> list = new CopyOnWriteArrayList<>();

// Thread-safe operations
list.add("A");
list.add("B");
list.remove("A");
```

### Internal Working

```java
// When you add an element:
// 1. Lock the list
// 2. Create a new array (size + 1)
// 3. Copy all elements to new array
// 4. Add new element
// 5. Replace old array with new array
// 6. Unlock

// When you read:
// No locking needed - reads from current array
```

### Iterator Behavior

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");
list.add("C");

// Iterator sees snapshot at creation time
Iterator<String> iter = list.iterator();

// Modify list after creating iterator
list.add("D");
list.remove("A");

// Iterator still sees original state
while (iter.hasNext()) {
    System.out.println(iter.next());
}
// Output: A, B, C (not D, and A is still there)

// New iterator sees current state
Iterator<String> iter2 = list.iterator();
while (iter2.hasNext()) {
    System.out.println(iter2.next());
}
// Output: B, C, D
```

### Real-World Example: Event Listeners

```java
class EventManager {
    private CopyOnWriteArrayList<EventListener> listeners = new CopyOnWriteArrayList<>();
    
    public void addListener(EventListener listener) {
        listeners.add(listener);
    }
    
    public void removeListener(EventListener listener) {
        listeners.remove(listener);
    }
    
    public void fireEvent(String event) {
        // Safe to iterate even if listeners are added/removed during iteration
        for (EventListener listener : listeners) {
            listener.onEvent(event);
        }
    }
}

interface EventListener {
    void onEvent(String event);
}

// Usage
EventManager manager = new EventManager();

// Thread 1 - Adding listeners
new Thread(() -> {
    manager.addListener(e -> System.out.println("Listener 1: " + e));
    manager.addListener(e -> System.out.println("Listener 2: " + e));
}).start();

// Thread 2 - Firing events
new Thread(() -> {
    manager.fireEvent("Event A");
    manager.fireEvent("Event B");
}).start();
```

### When to Use CopyOnWriteArrayList

**Use when:**
- ✅ Reads are much more frequent than writes
- ✅ List is small to medium size
- ✅ You need thread-safety without explicit synchronization
- ✅ Iteration is more common than modification

**Don't use when:**
- ❌ Writes are frequent (too expensive)
- ❌ List is very large (copying is expensive)
- ❌ You need strong consistency during iteration

---

## 3. CopyOnWriteArraySet

### Overview

Thread-safe Set backed by CopyOnWriteArrayList.

**Key Features:**
- Same characteristics as CopyOnWriteArrayList
- No duplicates
- Best for small sets with read-heavy workloads

### Basic Usage

```java
Set<String> set = new CopyOnWriteArraySet<>();

set.add("A");
set.add("B");
set.add("A");  // Ignored (duplicate)

System.out.println(set);  // [A, B]
```

### Real-World Example: Active Users

```java
class UserSessionManager {
    private CopyOnWriteArraySet<String> activeUsers = new CopyOnWriteArraySet<>();
    
    public void userLogin(String username) {
        activeUsers.add(username);
        System.out.println(username + " logged in");
    }
    
    public void userLogout(String username) {
        activeUsers.remove(username);
        System.out.println(username + " logged out");
    }
    
    public Set<String> getActiveUsers() {
        return new HashSet<>(activeUsers);  // Return copy
    }
    
    public int getActiveUserCount() {
        return activeUsers.size();
    }
    
    public boolean isUserActive(String username) {
        return activeUsers.contains(username);
    }
}

// Usage
UserSessionManager manager = new UserSessionManager();

// Multiple threads can safely add/remove users
new Thread(() -> manager.userLogin("Alice")).start();
new Thread(() -> manager.userLogin("Bob")).start();
new Thread(() -> manager.userLogout("Alice")).start();

Thread.sleep(100);
System.out.println("Active users: " + manager.getActiveUserCount());
```

---

## 4. ConcurrentLinkedQueue

### Overview

Thread-safe unbounded queue based on linked nodes.

**Key Features:**
- Non-blocking (lock-free)
- FIFO ordering
- No null elements
- Uses CAS operations

### Basic Usage

```java
Queue<String> queue = new ConcurrentLinkedQueue<>();

// Thread-safe operations
queue.offer("A");  // Add to tail
queue.offer("B");
queue.offer("C");

String head = queue.poll();  // Remove from head
System.out.println(head);  // A

String peek = queue.peek();  // View head without removing
System.out.println(peek);  // B
```

### Real-World Example: Task Queue

```java
class TaskProcessor {
    private ConcurrentLinkedQueue<Runnable> taskQueue = new ConcurrentLinkedQueue<>();
    private volatile boolean running = true;
    
    public void submitTask(Runnable task) {
        taskQueue.offer(task);
    }
    
    public void start() {
        new Thread(() -> {
            while (running) {
                Runnable task = taskQueue.poll();
                if (task != null) {
                    try {
                        task.run();
                    } catch (Exception e) {
                        System.err.println("Task failed: " + e.getMessage());
                    }
                } else {
                    try {
                        Thread.sleep(10);  // Wait for tasks
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                    }
                }
            }
        }).start();
    }
    
    public void stop() {
        running = false;
    }
}

// Usage
TaskProcessor processor = new TaskProcessor();
processor.start();

// Multiple threads submitting tasks
new Thread(() -> {
    for (int i = 0; i < 5; i++) {
        int taskId = i;
        processor.submitTask(() -> 
            System.out.println("Task " + taskId + " executed")
        );
    }
}).start();

Thread.sleep(1000);
processor.stop();
```

---

## 5. BlockingQueue Implementations

### Overview

Queues that support blocking operations - wait when empty (take) or full (put).

**Common Implementations:**
1. **ArrayBlockingQueue** - Bounded queue backed by array
2. **LinkedBlockingQueue** - Optionally bounded queue backed by linked nodes
3. **PriorityBlockingQueue** - Unbounded priority queue
4. **DelayQueue** - Queue where elements can only be taken after delay expires
5. **SynchronousQueue** - Queue with no capacity (direct handoff)

### ArrayBlockingQueue

```java
// Bounded queue with capacity 3
BlockingQueue<String> queue = new ArrayBlockingQueue<>(3);

// Producer thread
new Thread(() -> {
    try {
        queue.put("A");  // Blocks if full
        queue.put("B");
        queue.put("C");
        queue.put("D");  // Blocks until space available
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();

// Consumer thread
new Thread(() -> {
    try {
        Thread.sleep(1000);  // Wait a bit
        System.out.println(queue.take());  // Blocks if empty
        System.out.println(queue.take());
        System.out.println(queue.take());
        System.out.println(queue.take());
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();
```

### LinkedBlockingQueue

```java
// Optionally bounded (unbounded if no capacity specified)
BlockingQueue<String> queue = new LinkedBlockingQueue<>(10);

queue.put("A");
queue.put("B");

String item = queue.take();  // Blocks if empty
```

### Real-World Example: Producer-Consumer

```java
class ProducerConsumer {
    private BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(10);
    
    class Producer implements Runnable {
        @Override
        public void run() {
            try {
                for (int i = 1; i <= 20; i++) {
                    queue.put(i);
                    System.out.println("Produced: " + i);
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
    
    class Consumer implements Runnable {
        @Override
        public void run() {
            try {
                while (true) {
                    Integer item = queue.take();
                    System.out.println("Consumed: " + item);
                    Thread.sleep(200);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
    
    public void start() {
        new Thread(new Producer()).start();
        new Thread(new Consumer()).start();
    }
}

// Usage
ProducerConsumer pc = new ProducerConsumer();
pc.start();
```

### PriorityBlockingQueue

```java
class Task implements Comparable<Task> {
    String name;
    int priority;
    
    Task(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }
    
    @Override
    public int compareTo(Task other) {
        return Integer.compare(other.priority, this.priority);  // Higher priority first
    }
    
    @Override
    public String toString() {
        return name + "(P" + priority + ")";
    }
}

BlockingQueue<Task> queue = new PriorityBlockingQueue<>();

queue.put(new Task("Task1", 1));
queue.put(new Task("Task2", 5));
queue.put(new Task("Task3", 3));

System.out.println(queue.take());  // Task2(P5)
System.out.println(queue.take());  // Task3(P3)
System.out.println(queue.take());  // Task1(P1)
```

---

## 6. ConcurrentSkipListMap

### Overview

Thread-safe sorted map based on skip list data structure.

**Key Features:**
- Sorted by keys
- O(log n) operations
- Alternative to TreeMap for concurrent scenarios
- No null keys/values

### Basic Usage

```java
ConcurrentNavigableMap<Integer, String> map = new ConcurrentSkipListMap<>();

map.put(3, "Three");
map.put(1, "One");
map.put(2, "Two");

System.out.println(map);  // {1=One, 2=Two, 3=Three} (sorted)

// Navigation methods
System.out.println(map.firstEntry());  // 1=One
System.out.println(map.lastEntry());   // 3=Three
System.out.println(map.higherEntry(1)); // 2=Two
System.out.println(map.lowerEntry(3));  // 2=Two
```

### Real-World Example: Leaderboard

```java
class Leaderboard {
    // Score -> Player name (higher score = better)
    private ConcurrentSkipListMap<Integer, String> scores = new ConcurrentSkipListMap<>(
        Collections.reverseOrder()
    );
    
    public void updateScore(String player, int score) {
        scores.put(score, player);
    }
    
    public Map<Integer, String> getTopN(int n) {
        return scores.entrySet().stream()
            .limit(n)
            .collect(Collectors.toMap(
                Map.Entry::getKey,
                Map.Entry::getValue,
                (e1, e2) -> e1,
                LinkedHashMap::new
            ));
    }
    
    public String getWinner() {
        Map.Entry<Integer, String> first = scores.firstEntry();
        return first != null ? first.getValue() : null;
    }
}

// Usage
Leaderboard board = new Leaderboard();

// Multiple threads updating scores
new Thread(() -> board.updateScore("Alice", 100)).start();
new Thread(() -> board.updateScore("Bob", 150)).start();
new Thread(() -> board.updateScore("Charlie", 120)).start();

Thread.sleep(100);
System.out.println("Top 3: " + board.getTopN(3));
System.out.println("Winner: " + board.getWinner());
```

---

## 7. ConcurrentSkipListSet

### Overview

Thread-safe sorted set based on ConcurrentSkipListMap.

**Key Features:**
- Sorted elements
- O(log n) operations
- No duplicates
- No null elements

### Basic Usage

```java
ConcurrentNavigableSet<Integer> set = new ConcurrentSkipListSet<>();

set.add(3);
set.add(1);
set.add(2);

System.out.println(set);  // [1, 2, 3] (sorted)

// Navigation methods
System.out.println(set.first());    // 1
System.out.println(set.last());     // 3
System.out.println(set.higher(1));  // 2
System.out.println(set.lower(3));   // 2
```

---

## Comparison Table

| Collection | Thread-Safe | Ordering | Null | Performance | Best For |
|------------|-------------|----------|------|-------------|----------|
| **ConcurrentHashMap** | Yes | Unordered | No | High | General purpose map |
| **CopyOnWriteArrayList** | Yes | Insertion order | Yes | Read: High, Write: Low | Read-heavy lists |
| **CopyOnWriteArraySet** | Yes | Insertion order | Yes | Read: High, Write: Low | Read-heavy sets |
| **ConcurrentLinkedQueue** | Yes | FIFO | No | High | Non-blocking queue |
| **ArrayBlockingQueue** | Yes | FIFO | No | Medium | Bounded producer-consumer |
| **LinkedBlockingQueue** | Yes | FIFO | No | Medium | Unbounded producer-consumer |
| **PriorityBlockingQueue** | Yes | Priority | No | Medium | Priority-based processing |
| **ConcurrentSkipListMap** | Yes | Sorted | No | Medium | Sorted concurrent map |
| **ConcurrentSkipListSet** | Yes | Sorted | No | Medium | Sorted concurrent set |

---

## Common Interview Questions

### Q1: What's the difference between synchronized collections and concurrent collections?

**Answer:**
"Synchronized collections (from Collections.synchronizedXxx()) lock the entire collection for each operation, allowing only one thread at a time. This is simple but can be a performance bottleneck.

Concurrent collections use more sophisticated techniques:
1. **Lock Striping**: ConcurrentHashMap divides data into segments, allowing multiple threads to access different segments simultaneously
2. **Lock-Free Algorithms**: ConcurrentLinkedQueue uses CAS (Compare-And-Swap) operations without locks
3. **Copy-On-Write**: CopyOnWriteArrayList creates a new copy for writes, allowing lock-free reads

Example:
```java
// Synchronized - Only one thread can access at a time
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// Concurrent - Multiple threads can access different segments
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
```

Concurrent collections provide better scalability in multi-threaded environments."

### Q2: Why doesn't ConcurrentHashMap allow null keys or values?

**Answer:**
"ConcurrentHashMap doesn't allow null keys or values to avoid ambiguity in concurrent scenarios.

Consider this problem:
```java
// If nulls were allowed
Integer value = map.get(key);
if (value == null) {
    // Does this mean:
    // 1. Key doesn't exist?
    // 2. Key exists with null value?
    // 3. Key was just removed by another thread?
}
```

In a concurrent environment, you can't distinguish between these cases without additional synchronization, which defeats the purpose of a concurrent collection.

Solution: Use special values or Optional:
```java
// Use special value
map.put(key, -1);  // Instead of null

// Or use Optional (Java 8+)
map.put(key, Optional.empty());
```"

### Q3: How does ConcurrentHashMap achieve thread-safety without locking the entire map?

**Answer:**
"ConcurrentHashMap uses different strategies in different Java versions:

**Before Java 8:**
- Divided into 16 segments (by default)
- Each segment is independently locked
- Multiple threads can access different segments simultaneously
- Concurrency level = number of segments

**Java 8+:**
- Uses CAS (Compare-And-Swap) operations
- Node-level locking instead of segment locking
- Reads don't require locks at all
- Writes lock only the specific bucket being modified

Example of CAS operation:
```java
// Pseudo-code for CAS
do {
    oldValue = map.get(key);
    newValue = oldValue + 1;
} while (!compareAndSwap(key, oldValue, newValue));
```

This provides better scalability than locking the entire map."

### Q4: When should you use CopyOnWriteArrayList?

**Answer:**
"Use CopyOnWriteArrayList when:

1. **Reads >> Writes**: Read operations are much more frequent than writes
2. **Small to medium size**: List is not too large (copying is expensive)
3. **Iteration is common**: You frequently iterate over the list
4. **Weak consistency is acceptable**: Iterators see snapshot at creation time

Example use cases:
- Event listener lists (rarely modified, frequently iterated)
- Configuration settings (read often, changed rarely)
- Observer patterns

Don't use when:
- Writes are frequent (too expensive to copy)
- List is very large (memory overhead)
- You need strong consistency

Example:
```java
// Good use case - Event listeners
CopyOnWriteArrayList<EventListener> listeners = new CopyOnWriteArrayList<>();

// Bad use case - Frequently modified list
CopyOnWriteArrayList<String> logs = new CopyOnWriteArrayList<>();
for (int i = 0; i < 10000; i++) {
    logs.add("Log " + i);  // Creates 10000 copies!
}
```"

### Q5: What's the difference between ArrayBlockingQueue and LinkedBlockingQueue?

**Answer:**
"Both are blocking queues, but they differ in implementation and characteristics:

**ArrayBlockingQueue:**
- Backed by array
- **Bounded**: Must specify capacity
- **Single lock**: Uses one lock for both put and take
- **Better throughput**: For bounded scenarios
- **Fixed memory**: Allocates array upfront

**LinkedBlockingQueue:**
- Backed by linked nodes
- **Optionally bounded**: Can be unbounded
- **Two locks**: Separate locks for put and take (better concurrency)
- **Better scalability**: For high-concurrency scenarios
- **Dynamic memory**: Allocates nodes as needed

Example:
```java
// ArrayBlockingQueue - Must specify capacity
BlockingQueue<String> arrayQueue = new ArrayBlockingQueue<>(100);

// LinkedBlockingQueue - Optional capacity
BlockingQueue<String> linkedQueue1 = new LinkedBlockingQueue<>();  // Unbounded
BlockingQueue<String> linkedQueue2 = new LinkedBlockingQueue<>(100);  // Bounded
```

Choose ArrayBlockingQueue for bounded scenarios with predictable load. Choose LinkedBlockingQueue for better concurrency or when you need unbounded capacity."

### Q6: What is the weakly consistent iterator in ConcurrentHashMap?

**Answer:**
"A weakly consistent iterator in ConcurrentHashMap:

1. **Never throws ConcurrentModificationException**: Unlike fail-fast iterators
2. **May reflect modifications**: Can see changes made after iterator creation
3. **Guaranteed to traverse elements**: That existed at creation time
4. **May or may not reflect**: Subsequent modifications

Example:
```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);
map.put("B", 2);
map.put("C", 3);

Iterator<String> iter = map.keySet().iterator();

// Modify map during iteration
map.put("D", 4);
map.remove("A");

// Iterator doesn't throw exception
while (iter.hasNext()) {
    System.out.println(iter.next());
    // May or may not see "D"
    // May or may not see "A"
}
```

This is different from fail-fast iterators (HashMap) that throw ConcurrentModificationException, and snapshot iterators (CopyOnWriteArrayList) that see only the state at creation time."

### Q7: How does ConcurrentLinkedQueue achieve lock-free operations?

**Answer:**
"ConcurrentLinkedQueue uses CAS (Compare-And-Swap) operations, which are atomic hardware instructions.

CAS operation:
```java
// Pseudo-code
boolean compareAndSwap(Node expected, Node newValue) {
    if (currentValue == expected) {
        currentValue = newValue;
        return true;
    }
    return false;
}
```

Example of lock-free enqueue:
```java
// Simplified pseudo-code
public boolean offer(E e) {
    Node<E> newNode = new Node<>(e);
    while (true) {
        Node<E> tail = getTail();
        Node<E> next = tail.next;
        
        if (next == null) {
            // Try to link new node
            if (tail.casNext(null, newNode)) {
                // Success! Update tail
                casTail(tail, newNode);
                return true;
            }
            // CAS failed, retry
        } else {
            // Help other thread by updating tail
            casTail(tail, next);
        }
    }
}
```

Benefits:
- No locks needed
- No thread blocking
- Better scalability
- No deadlock possibility

Drawback: More complex implementation"

### Q8: What's the difference between put() and offer() in BlockingQueue?

**Answer:**
"Both add elements to the queue, but they handle full queue differently:

**put(E e):**
- Blocks if queue is full
- Waits until space becomes available
- Throws InterruptedException if interrupted
- No return value (void)

**offer(E e):**
- Returns immediately
- Returns false if queue is full
- Doesn't block
- Returns boolean (success/failure)

**offer(E e, long timeout, TimeUnit unit):**
- Waits for specified time
- Returns false if timeout expires
- Throws InterruptedException if interrupted

Example:
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(2);
queue.put("A");
queue.put("B");

// put() - Blocks until space available
new Thread(() -> {
    try {
        queue.put("C");  // Blocks here
        System.out.println("Added C");
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();

// offer() - Returns immediately
boolean added = queue.offer("D");
System.out.println("Added D: " + added);  // false

// offer with timeout
boolean added2 = queue.offer("E", 1, TimeUnit.SECONDS);
System.out.println("Added E: " + added2);  // false (timeout)
```

Similarly for removal:
- take() - Blocks if empty
- poll() - Returns null if empty
- poll(timeout) - Waits for specified time"

### Q9: Can you use ConcurrentHashMap for caching?

**Answer:**
"Yes, ConcurrentHashMap is excellent for caching, but consider these points:

**Advantages:**
- Thread-safe without external synchronization
- High performance for concurrent access
- Atomic operations (putIfAbsent, computeIfAbsent)

**Considerations:**
- No automatic eviction (use Guava Cache or Caffeine for that)
- No size limit (can grow unbounded)
- No expiration policy

Example of simple cache:
```java
class SimpleCache<K, V> {
    private ConcurrentHashMap<K, V> cache = new ConcurrentHashMap<>();
    
    public V get(K key, Function<K, V> loader) {
        return cache.computeIfAbsent(key, loader);
    }
    
    public void put(K key, V value) {
        cache.put(key, value);
    }
    
    public void invalidate(K key) {
        cache.remove(key);
    }
    
    public void clear() {
        cache.clear();
    }
}

// Usage
SimpleCache<String, User> userCache = new SimpleCache<>();

User user = userCache.get("user123", userId -> {
    // Load from database if not in cache
    return database.loadUser(userId);
});
```

For production caching, consider:
- Guava Cache (Google)
- Caffeine (high-performance)
- Ehcache (enterprise features)

These provide eviction policies, expiration, size limits, etc."

### Q10: What's the difference between ConcurrentHashMap and Hashtable?

**Answer:**
"Both are thread-safe maps, but ConcurrentHashMap is superior in almost every way:

| Feature | Hashtable | ConcurrentHashMap |
|---------|-----------|-------------------|
| **Locking** | Locks entire table | Lock striping (segments) |
| **Performance** | Poor (one thread at a time) | Excellent (multiple threads) |
| **Null keys/values** | Not allowed | Not allowed |
| **Iterator** | Fail-fast | Weakly consistent |
| **Since** | Java 1.0 (legacy) | Java 1.5 (modern) |
| **Recommended** | No | Yes |

Example:
```java
// Hashtable - Legacy, slow
Hashtable<String, Integer> table = new Hashtable<>();
table.put("A", 1);  // Locks entire table

// ConcurrentHashMap - Modern, fast
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);  // Locks only segment

// Multiple threads
// Hashtable: Thread 2 must wait for Thread 1
// ConcurrentHashMap: Both can proceed if different segments
```

**Recommendation:** Always use ConcurrentHashMap instead of Hashtable. Hashtable is only kept for backward compatibility."

---

## Best Practices

### 1. Choose the Right Collection

```java
// Read-heavy, small list
List<String> listeners = new CopyOnWriteArrayList<>();

// General purpose concurrent map
Map<String, Integer> cache = new ConcurrentHashMap<>();

// Producer-consumer pattern
BlockingQueue<Task> tasks = new LinkedBlockingQueue<>();

// Sorted concurrent map
NavigableMap<Integer, String> scores = new ConcurrentSkipListMap<>();
```

### 2. Use Atomic Operations

```java
// Bad - Not atomic
if (!map.containsKey(key)) {
    map.put(key, value);
}

// Good - Atomic
map.putIfAbsent(key, value);

// Bad - Not atomic
Integer count = map.get(key);
map.put(key, count == null ? 1 : count + 1);

// Good - Atomic
map.merge(key, 1, Integer::sum);
```

### 3. Don't Mix Synchronized and Concurrent

```java
// Bad - Unnecessary synchronization
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
synchronized(map) {  // Don't do this!
    map.put(key, value);
}

// Good - Use concurrent collection's methods
map.put(key, value);  // Already thread-safe
```

### 4. Handle InterruptedException Properly

```java
// Bad
try {
    queue.put(item);
} catch (InterruptedException e) {
    e.printStackTrace();  // Don't ignore!
}

// Good
try {
    queue.put(item);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();  // Restore interrupt status
    // Handle appropriately
}
```

### 5. Use Bulk Operations When Possible

```java
// Bad - Multiple operations
for (Entry<String, Integer> entry : entries) {
    map.put(entry.getKey(), entry.getValue());
}

// Good - Single bulk operation
map.putAll(entries);
```

---

## Summary

**Concurrent Collections:**
- ✅ Thread-safe without external synchronization
- ✅ Better performance than synchronized collections
- ✅ Use lock striping, CAS, or copy-on-write
- ✅ Designed for high-concurrency scenarios
- ✅ No ConcurrentModificationException

**Key Collections:**
1. **ConcurrentHashMap** - General purpose concurrent map
2. **CopyOnWriteArrayList** - Read-heavy list
3. **CopyOnWriteArraySet** - Read-heavy set
4. **ConcurrentLinkedQueue** - Lock-free queue
5. **BlockingQueue** - Producer-consumer pattern
6. **ConcurrentSkipListMap** - Sorted concurrent map

**When to Use:**
- Use concurrent collections for multi-threaded scenarios
- Choose based on read/write ratio and ordering requirements
- Consider memory and performance trade-offs

**Interview Tips:**
- Understand lock striping vs full locking
- Know when to use each collection type
- Understand CAS operations
- Know the difference between fail-fast and weakly consistent
- Understand copy-on-write trade-offs

Master these concepts and you'll handle any concurrent collections question with confidence!