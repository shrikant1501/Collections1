# Queue and Deque - Complete Guide

## Simple Explanation

### What is a Queue?

**Queue** = A collection that follows FIFO (First-In-First-Out) principle

**Real-World Analogy:**
Think of a queue like:
- **Queue at a ticket counter** - First person in line gets served first
- **Print queue** - Documents print in the order they were sent
- **Call center queue** - Calls answered in the order received

**Example:**
```java
Queue<String> queue = new LinkedList<>();
queue.offer("First");   // Add to tail
queue.offer("Second");
queue.offer("Third");

queue.poll();  // Remove from head - "First"
queue.poll();  // Remove from head - "Second"
```

### What is a Deque?

**Deque** = Double-Ended Queue - Can add/remove from both ends

**Real-World Analogy:**
Think of a deque like:
- **Train** - Can add/remove cars from both front and back
- **Browser history** - Can go forward and backward
- **Undo/Redo** - Can add/remove from both ends

**Example:**
```java
Deque<String> deque = new ArrayDeque<>();
deque.offerFirst("Middle");   // Add to front
deque.offerLast("End");       // Add to back
deque.offerFirst("Start");    // Add to front

// Now: Start -> Middle -> End
```

---

## Queue Interface

### Overview

Queue is an interface that represents a collection designed for holding elements prior to processing.

**Key Characteristics:**
- FIFO (First-In-First-Out) ordering
- Elements added at tail, removed from head
- Extends Collection interface
- Two forms of methods: throwing exceptions vs returning special values

### Queue Hierarchy

```
Collection (interface)
    ↓
Queue (interface)
    ↓
├── LinkedList (class)
├── PriorityQueue (class)
├── ArrayDeque (class)
└── BlockingQueue (interface)
    ├── ArrayBlockingQueue
    ├── LinkedBlockingQueue
    └── PriorityBlockingQueue
```

### Queue Methods

Queue provides two forms of methods:

| Operation | Throws Exception | Returns Special Value |
|-----------|------------------|----------------------|
| **Insert** | add(e) | offer(e) |
| **Remove** | remove() | poll() |
| **Examine** | element() | peek() |

#### 1. Insertion Methods

```java
Queue<String> queue = new LinkedList<>();

// add(e) - Throws IllegalStateException if no space
queue.add("A");
queue.add("B");

// offer(e) - Returns false if no space (preferred)
boolean added = queue.offer("C");
System.out.println("Added: " + added);  // true
```

#### 2. Removal Methods

```java
Queue<String> queue = new LinkedList<>(Arrays.asList("A", "B", "C"));

// remove() - Throws NoSuchElementException if empty
String removed = queue.remove();
System.out.println(removed);  // "A"

// poll() - Returns null if empty (preferred)
String polled = queue.poll();
System.out.println(polled);  // "B"

// Empty queue
queue.clear();
String nullValue = queue.poll();
System.out.println(nullValue);  // null
```

#### 3. Examination Methods

```java
Queue<String> queue = new LinkedList<>(Arrays.asList("A", "B", "C"));

// element() - Throws NoSuchElementException if empty
String head = queue.element();
System.out.println(head);  // "A" (not removed)

// peek() - Returns null if empty (preferred)
String peeked = queue.peek();
System.out.println(peeked);  // "A" (not removed)

System.out.println(queue);  // [A, B, C] (unchanged)
```

### Basic Queue Operations Example

```java
Queue<Integer> queue = new LinkedList<>();

// Add elements
queue.offer(1);
queue.offer(2);
queue.offer(3);
queue.offer(4);
queue.offer(5);

System.out.println("Queue: " + queue);  // [1, 2, 3, 4, 5]

// Peek at head
System.out.println("Head: " + queue.peek());  // 1

// Remove elements (FIFO)
while (!queue.isEmpty()) {
    System.out.println("Removed: " + queue.poll());
}
// Output:
// Removed: 1
// Removed: 2
// Removed: 3
// Removed: 4
// Removed: 5
```

---

## Deque Interface

### Overview

Deque (Double-Ended Queue) is an interface that supports element insertion and removal at both ends.

**Key Characteristics:**
- Can function as both Queue (FIFO) and Stack (LIFO)
- Elements can be added/removed from both ends
- More flexible than Queue
- Extends Queue interface

### Deque Methods

Deque provides methods for both ends:

| Operation | First Element (Head) | Last Element (Tail) |
|-----------|---------------------|---------------------|
| **Insert** | addFirst(e) / offerFirst(e) | addLast(e) / offerLast(e) |
| **Remove** | removeFirst() / pollFirst() | removeLast() / pollLast() |
| **Examine** | getFirst() / peekFirst() | getLast() / peekLast() |

#### Methods Comparison

```java
Deque<String> deque = new ArrayDeque<>();

// Insert at head
deque.addFirst("A");      // Throws exception if no space
deque.offerFirst("B");    // Returns false if no space (preferred)

// Insert at tail
deque.addLast("C");       // Throws exception if no space
deque.offerLast("D");     // Returns false if no space (preferred)

// Remove from head
deque.removeFirst();      // Throws exception if empty
deque.pollFirst();        // Returns null if empty (preferred)

// Remove from tail
deque.removeLast();       // Throws exception if empty
deque.pollLast();         // Returns null if empty (preferred)

// Examine head
deque.getFirst();         // Throws exception if empty
deque.peekFirst();        // Returns null if empty (preferred)

// Examine tail
deque.getLast();          // Throws exception if empty
deque.peekLast();         // Returns null if empty (preferred)
```

### Deque as Queue (FIFO)

```java
Deque<String> queue = new ArrayDeque<>();

// Add to tail (enqueue)
queue.offerLast("First");
queue.offerLast("Second");
queue.offerLast("Third");

// Remove from head (dequeue)
System.out.println(queue.pollFirst());  // "First"
System.out.println(queue.pollFirst());  // "Second"
System.out.println(queue.pollFirst());  // "Third"
```

### Deque as Stack (LIFO)

```java
Deque<String> stack = new ArrayDeque<>();

// Push (add to head)
stack.offerFirst("First");
stack.offerFirst("Second");
stack.offerFirst("Third");

// Pop (remove from head)
System.out.println(stack.pollFirst());  // "Third"
System.out.println(stack.pollFirst());  // "Second"
System.out.println(stack.pollFirst());  // "First"

// Or use stack methods
stack.push("A");  // Same as offerFirst
stack.push("B");
String top = stack.pop();  // Same as pollFirst
```

### Deque Operations Example

```java
Deque<Integer> deque = new ArrayDeque<>();

// Add to both ends
deque.offerFirst(1);   // [1]
deque.offerLast(2);    // [1, 2]
deque.offerFirst(0);   // [0, 1, 2]
deque.offerLast(3);    // [0, 1, 2, 3]

System.out.println("Deque: " + deque);  // [0, 1, 2, 3]

// Peek at both ends
System.out.println("First: " + deque.peekFirst());  // 0
System.out.println("Last: " + deque.peekLast());    // 3

// Remove from both ends
System.out.println("Removed first: " + deque.pollFirst());  // 0
System.out.println("Removed last: " + deque.pollLast());    // 3

System.out.println("Deque: " + deque);  // [1, 2]
```

---

## PriorityQueue

### Overview

PriorityQueue is a heap-based implementation where elements are ordered by priority (natural ordering or custom comparator).

**Key Characteristics:**
- Elements ordered by priority, not insertion order
- Head is always the smallest element (min-heap by default)
- Not thread-safe
- Does not allow null elements
- O(log n) for insertion and removal

### Internal Working

PriorityQueue uses a **binary heap** data structure:
- Complete binary tree stored in an array
- Parent node is smaller than children (min-heap)
- Insertion: Add at end, bubble up
- Removal: Remove root, move last to root, bubble down

### Basic Usage

```java
// Natural ordering (min-heap)
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(5);
pq.offer(2);
pq.offer(8);
pq.offer(1);
pq.offer(9);

System.out.println("Priority Queue: " + pq);  // [1, 2, 8, 5, 9] (heap order)

// Remove elements (always smallest first)
while (!pq.isEmpty()) {
    System.out.println(pq.poll());
}
// Output: 1, 2, 5, 8, 9 (sorted order)
```

### Custom Comparator (Max-Heap)

```java
// Max-heap (largest element first)
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

maxHeap.offer(5);
maxHeap.offer(2);
maxHeap.offer(8);
maxHeap.offer(1);

while (!maxHeap.isEmpty()) {
    System.out.println(maxHeap.poll());
}
// Output: 8, 5, 2, 1 (descending order)
```

### Custom Objects with Priority

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
        // Higher priority value = higher priority
        return Integer.compare(other.priority, this.priority);
    }
    
    @Override
    public String toString() {
        return name + "(P" + priority + ")";
    }
}

PriorityQueue<Task> taskQueue = new PriorityQueue<>();

taskQueue.offer(new Task("Low Priority", 1));
taskQueue.offer(new Task("High Priority", 5));
taskQueue.offer(new Task("Medium Priority", 3));

while (!taskQueue.isEmpty()) {
    System.out.println(taskQueue.poll());
}
// Output:
// High Priority(P5)
// Medium Priority(P3)
// Low Priority(P1)
```

### Real-World Example: Hospital Emergency Room

```java
class Patient implements Comparable<Patient> {
    String name;
    int severity;  // 1-10, higher = more severe
    
    Patient(String name, int severity) {
        this.name = name;
        this.severity = severity;
    }
    
    @Override
    public int compareTo(Patient other) {
        // Higher severity = higher priority
        return Integer.compare(other.severity, this.severity);
    }
    
    @Override
    public String toString() {
        return name + " (Severity: " + severity + ")";
    }
}

class EmergencyRoom {
    private PriorityQueue<Patient> waitingRoom = new PriorityQueue<>();
    
    public void admitPatient(Patient patient) {
        waitingRoom.offer(patient);
        System.out.println("Admitted: " + patient);
    }
    
    public Patient treatNextPatient() {
        Patient patient = waitingRoom.poll();
        if (patient != null) {
            System.out.println("Treating: " + patient);
        }
        return patient;
    }
    
    public int getWaitingCount() {
        return waitingRoom.size();
    }
}

// Usage
EmergencyRoom er = new EmergencyRoom();

er.admitPatient(new Patient("John", 3));
er.admitPatient(new Patient("Alice", 8));
er.admitPatient(new Patient("Bob", 5));
er.admitPatient(new Patient("Charlie", 2));

System.out.println("\nTreating patients:");
while (er.getWaitingCount() > 0) {
    er.treatNextPatient();
}
// Output (by severity):
// Alice (8)
// Bob (5)
// John (3)
// Charlie (2)
```

---

## ArrayDeque vs LinkedList

### Comparison

| Feature | ArrayDeque | LinkedList |
|---------|-----------|------------|
| **Implementation** | Circular array | Doubly-linked list |
| **Memory** | More efficient | More overhead (node objects) |
| **Access** | O(1) at both ends | O(1) at both ends |
| **Insertion** | O(1) amortized | O(1) |
| **Removal** | O(1) | O(1) |
| **Random Access** | Not supported | O(n) |
| **Null Elements** | Not allowed | Allowed |
| **Thread-Safe** | No | No |
| **Recommended** | Yes (for Queue/Deque) | No (use ArrayDeque) |

### Performance Comparison

```java
// ArrayDeque - Better performance
Deque<Integer> arrayDeque = new ArrayDeque<>();
long start = System.nanoTime();
for (int i = 0; i < 100000; i++) {
    arrayDeque.offerLast(i);
}
long end = System.nanoTime();
System.out.println("ArrayDeque: " + (end - start) / 1000000 + " ms");

// LinkedList - Slower
Deque<Integer> linkedList = new LinkedList<>();
start = System.nanoTime();
for (int i = 0; i < 100000; i++) {
    linkedList.offerLast(i);
}
end = System.nanoTime();
System.out.println("LinkedList: " + (end - start) / 1000000 + " ms");

// ArrayDeque is typically 2-3x faster
```

### When to Use Each

**Use ArrayDeque when:**
- ✅ You need a Queue or Deque
- ✅ You need a Stack (better than Stack class)
- ✅ Performance is important
- ✅ You don't need null elements

**Use LinkedList when:**
- ✅ You need List operations (get, set by index)
- ✅ You need to allow null elements
- ✅ You need frequent insertions/deletions in the middle

**Example:**
```java
// Good - Use ArrayDeque for Queue/Stack
Deque<String> queue = new ArrayDeque<>();
Deque<String> stack = new ArrayDeque<>();

// Good - Use LinkedList for List operations
List<String> list = new LinkedList<>();
list.add(0, "Insert at index");

// Bad - Don't use LinkedList as Queue
Queue<String> badQueue = new LinkedList<>();  // Use ArrayDeque instead

// Bad - Don't use Stack class
Stack<String> badStack = new Stack<>();  // Use ArrayDeque instead
```

---

## BlockingQueue

### Overview

BlockingQueue is a thread-safe queue that supports blocking operations.

**Key Characteristics:**
- Thread-safe without external synchronization
- Blocks when queue is full (put) or empty (take)
- Used in producer-consumer patterns
- Part of java.util.concurrent package

### Common Implementations

1. **ArrayBlockingQueue** - Bounded queue backed by array
2. **LinkedBlockingQueue** - Optionally bounded queue backed by linked nodes
3. **PriorityBlockingQueue** - Unbounded priority queue
4. **DelayQueue** - Elements can only be taken after delay expires
5. **SynchronousQueue** - Queue with no capacity (direct handoff)

### BlockingQueue Methods

| Operation | Throws Exception | Special Value | Blocks | Times Out |
|-----------|------------------|---------------|--------|-----------|
| **Insert** | add(e) | offer(e) | put(e) | offer(e, time, unit) |
| **Remove** | remove() | poll() | take() | poll(time, unit) |
| **Examine** | element() | peek() | N/A | N/A |

### Basic Usage

```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(3);

// Producer thread
new Thread(() -> {
    try {
        queue.put("Item 1");  // Blocks if full
        queue.put("Item 2");
        queue.put("Item 3");
        System.out.println("Producer: Added 3 items");
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();

// Consumer thread
new Thread(() -> {
    try {
        Thread.sleep(1000);  // Wait a bit
        System.out.println("Consumer: " + queue.take());  // Blocks if empty
        System.out.println("Consumer: " + queue.take());
        System.out.println("Consumer: " + queue.take());
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();
```

### Producer-Consumer Example

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

---

## Common Interview Questions

### Q1: What's the difference between Queue and Deque?

**Answer:**
"Queue is a single-ended collection that follows FIFO (First-In-First-Out) principle. Elements are added at the tail and removed from the head.

Deque (Double-Ended Queue) is a more flexible collection that allows insertion and removal at both ends. It can function as both a Queue (FIFO) and a Stack (LIFO).

Key differences:

| Feature | Queue | Deque |
|---------|-------|-------|
| **Ends** | Single-ended | Double-ended |
| **Operations** | Add at tail, remove from head | Add/remove from both ends |
| **Use as Stack** | No | Yes |
| **Flexibility** | Less | More |

Example:
```java
// Queue - FIFO only
Queue<String> queue = new LinkedList<>();
queue.offer("A");  // Add to tail
queue.poll();      // Remove from head

// Deque - Both ends
Deque<String> deque = new ArrayDeque<>();
deque.offerFirst("A");  // Add to head
deque.offerLast("B");   // Add to tail
deque.pollFirst();      // Remove from head
deque.pollLast();       // Remove from tail
```

Deque extends Queue, so it can do everything Queue can do, plus more."

### Q2: When should you use PriorityQueue?

**Answer:**
"Use PriorityQueue when you need elements to be processed in priority order rather than insertion order.

**Common use cases:**

1. **Task Scheduling**: Process high-priority tasks first
2. **Dijkstra's Algorithm**: Find shortest path in graphs
3. **Huffman Coding**: Build optimal prefix codes
4. **Event-Driven Simulation**: Process events by time
5. **Top K Elements**: Find K largest/smallest elements

Example:
```java
// Task scheduling
class Task implements Comparable<Task> {
    String name;
    int priority;
    
    public int compareTo(Task other) {
        return Integer.compare(other.priority, this.priority);
    }
}

PriorityQueue<Task> tasks = new PriorityQueue<>();
tasks.offer(new Task("Low", 1));
tasks.offer(new Task("High", 5));
tasks.offer(new Task("Medium", 3));

// Always processes highest priority first
while (!tasks.isEmpty()) {
    Task task = tasks.poll();  // High -> Medium -> Low
    process(task);
}
```

**Don't use PriorityQueue when:**
- You need FIFO ordering (use Queue)
- You need to access elements by index (use List)
- You need thread-safety (use PriorityBlockingQueue)

**Time Complexity:**
- Insertion: O(log n)
- Removal: O(log n)
- Peek: O(1)"

### Q3: Implement a queue using two stacks

**Answer:**
"Here's how to implement a queue using two stacks:

**Approach 1: Expensive enqueue**
```java
class QueueUsingStacks<T> {
    private Stack<T> stack1 = new Stack<>();  // For enqueue
    private Stack<T> stack2 = new Stack<>();  // For dequeue
    
    // O(n) - Move all elements to stack2, push, move back
    public void enqueue(T item) {
        // Move all to stack2
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        
        // Push new item
        stack1.push(item);
        
        // Move all back to stack1
        while (!stack2.isEmpty()) {
            stack1.push(stack2.pop());
        }
    }
    
    // O(1)
    public T dequeue() {
        if (stack1.isEmpty()) {
            throw new NoSuchElementException();
        }
        return stack1.pop();
    }
    
    // O(1)
    public T peek() {
        if (stack1.isEmpty()) {
            throw new NoSuchElementException();
        }
        return stack1.peek();
    }
    
    public boolean isEmpty() {
        return stack1.isEmpty();
    }
}
```

**Approach 2: Expensive dequeue (Better)**
```java
class QueueUsingStacks<T> {
    private Stack<T> inbox = new Stack<>();   // For enqueue
    private Stack<T> outbox = new Stack<>();  // For dequeue
    
    // O(1)
    public void enqueue(T item) {
        inbox.push(item);
    }
    
    // Amortized O(1)
    public T dequeue() {
        if (outbox.isEmpty()) {
            // Move all from inbox to outbox
            while (!inbox.isEmpty()) {
                outbox.push(inbox.pop());
            }
        }
        
        if (outbox.isEmpty()) {
            throw new NoSuchElementException();
        }
        
        return outbox.pop();
    }
    
    // Amortized O(1)
    public T peek() {
        if (outbox.isEmpty()) {
            while (!inbox.isEmpty()) {
                outbox.push(inbox.pop());
            }
        }
        
        if (outbox.isEmpty()) {
            throw new NoSuchElementException();
        }
        
        return outbox.peek();
    }
    
    public boolean isEmpty() {
        return inbox.isEmpty() && outbox.isEmpty();
    }
}

// Usage
QueueUsingStacks<Integer> queue = new QueueUsingStacks<>();
queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);

System.out.println(queue.dequeue());  // 1
System.out.println(queue.dequeue());  // 2
System.out.println(queue.dequeue());  // 3
```

**Explanation:**
- Inbox stack: For enqueue operations
- Outbox stack: For dequeue operations
- When dequeue is called and outbox is empty, move all elements from inbox to outbox
- This reverses the order, making FIFO possible
- Amortized O(1) because each element is moved at most once"

### Q4: What's the difference between offer() and add() in Queue?

**Answer:**
"Both methods add elements to the queue, but they handle capacity restrictions differently:

**add(e):**
- Throws IllegalStateException if queue is full
- Inherited from Collection interface
- Use when you want to know immediately if addition failed

**offer(e):**
- Returns false if queue is full
- Specific to Queue interface
- Use when you want to handle failure gracefully

Example:
```java
// Bounded queue
Queue<String> queue = new ArrayBlockingQueue<>(2);
queue.add("A");
queue.add("B");

// add() - Throws exception
try {
    queue.add("C");  // IllegalStateException!
} catch (IllegalStateException e) {
    System.out.println("Queue is full");
}

// offer() - Returns false
boolean added = queue.offer("C");
if (!added) {
    System.out.println("Could not add element");
}
```

**Recommendation:** Use offer() for better error handling, especially with bounded queues."

### Q5: What's the difference between poll() and remove() in Queue?

**Answer:**
"Both methods remove and return the head element, but they handle empty queue differently:

**remove():**
- Throws NoSuchElementException if queue is empty
- Inherited from Collection interface
- Use when you expect queue to have elements

**poll():**
- Returns null if queue is empty
- Specific to Queue interface
- Use when queue might be empty

Example:
```java
Queue<String> queue = new LinkedList<>();

// remove() - Throws exception
try {
    String item = queue.remove();  // NoSuchElementException!
} catch (NoSuchElementException e) {
    System.out.println("Queue is empty");
}

// poll() - Returns null
String item = queue.poll();
if (item == null) {
    System.out.println("Queue is empty");
}
```

**Recommendation:** Use poll() for safer code, especially when queue might be empty."

### Q6: Why is ArrayDeque preferred over LinkedList for Queue/Stack?

**Answer:**
"ArrayDeque is preferred over LinkedList for Queue and Stack operations because:

**Performance:**
- ArrayDeque is faster for most operations (2-3x)
- Better cache locality (contiguous memory)
- Less memory overhead (no node objects)

**Memory:**
- ArrayDeque: Only stores elements
- LinkedList: Stores elements + node objects (prev/next pointers)

**Benchmarks:**
```java
// ArrayDeque - Faster
Deque<Integer> arrayDeque = new ArrayDeque<>();
// 100,000 operations: ~10ms

// LinkedList - Slower
Deque<Integer> linkedList = new LinkedList<>();
// 100,000 operations: ~25ms
```

**When to use LinkedList:**
- Need List operations (get/set by index)
- Need to allow null elements
- Need frequent insertions/deletions in the middle

**Example:**
```java
// Good - Use ArrayDeque for Queue
Queue<String> queue = new ArrayDeque<>();

// Good - Use ArrayDeque for Stack
Deque<String> stack = new ArrayDeque<>();

// Bad - Don't use LinkedList for Queue/Stack
Queue<String> badQueue = new LinkedList<>();  // Slower!
```

**Recommendation:** Always use ArrayDeque for Queue and Stack unless you specifically need LinkedList features."

### Q7: How does PriorityQueue maintain order?

**Answer:**
"PriorityQueue maintains order using a **binary heap** data structure:

**Binary Heap Properties:**
1. Complete binary tree (all levels filled except possibly last)
2. Min-heap: Parent ≤ children (or max-heap: Parent ≥ children)
3. Stored in an array for efficiency

**Operations:**

**Insertion (offer):**
1. Add element at end of array
2. Bubble up: Compare with parent, swap if needed
3. Repeat until heap property satisfied
4. Time: O(log n)

**Removal (poll):**
1. Remove root (smallest element)
2. Move last element to root
3. Bubble down: Compare with children, swap with smaller child
4. Repeat until heap property satisfied
5. Time: O(log n)

**Example:**
```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

// Insert 5
pq.offer(5);  // [5]

// Insert 2
pq.offer(2);  // [2, 5] (2 bubbles up)

// Insert 8
pq.offer(8);  // [2, 5, 8]

// Insert 1
pq.offer(1);  // [1, 2, 8, 5] (1 bubbles up to root)

// Remove (always smallest)
pq.poll();  // Returns 1, heap restructures
```

**Internal Array Representation:**
```
     1
   /   \
  2     8
 /
5

Array: [1, 2, 8, 5]
```

**Key Points:**
- Not fully sorted, just heap-ordered
- peek() is O(1) - just return root
- Iteration order is NOT sorted
- Only poll() guarantees sorted order"

### Q8: What's the difference between BlockingQueue and regular Queue?

**Answer:**
"BlockingQueue is a thread-safe queue that supports blocking operations, while regular Queue is not thread-safe and doesn't block.

**Key Differences:**

| Feature | Queue | BlockingQueue |
|---------|-------|---------------|
| **Thread-Safe** | No | Yes |
| **Blocking** | No | Yes |
| **Capacity** | Usually unbounded | Can be bounded |
| **Use Case** | Single-threaded | Multi-threaded |
| **Package** | java.util | java.util.concurrent |

**Blocking Behavior:**

**put()** - Blocks if queue is full:
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(2);
queue.put("A");
queue.put("B");
queue.put("C");  // Blocks until space available
```

**take()** - Blocks if queue is empty:
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(10);
String item = queue.take();  // Blocks until element available
```

**Producer-Consumer Pattern:**
```java
BlockingQueue<Task> queue = new LinkedBlockingQueue<>();

// Producer
new Thread(() -> {
    while (true) {
        Task task = createTask();
        queue.put(task);  // Blocks if full
    }
}).start();

// Consumer
new Thread(() -> {
    while (true) {
        Task task = queue.take();  // Blocks if empty
        process(task);
    }
}).start();
```

**When to use:**
- Use BlockingQueue for producer-consumer patterns
- Use regular Queue for single-threaded scenarios

**Common Implementations:**
- ArrayBlockingQueue (bounded)
- LinkedBlockingQueue (optionally bounded)
- PriorityBlockingQueue (unbounded, priority-based)"

### Q9: Can you use null in Queue implementations?

**Answer:**
"It depends on the implementation:

**Allow null:**
- LinkedList ✅

**Don't allow null:**
- ArrayDeque ❌
- PriorityQueue ❌
- All BlockingQueue implementations ❌

**Why some don't allow null:**

1. **Ambiguity**: poll() returns null when empty
```java
Queue<String> queue = new LinkedList<>();
queue.offer(null);  // Allowed in LinkedList

String item = queue.poll();
if (item == null) {
    // Does this mean:
    // 1. Queue was empty?
    // 2. Element was null?
    // Can't tell!
}
```

2. **BlockingQueue**: Uses null as sentinel value
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(10);
// queue.offer(null);  // NullPointerException!

String item = queue.poll();
if (item == null) {
    // Definitely means queue is empty
}
```

**Recommendation:**
- Avoid using null in queues
- Use Optional or special sentinel objects if needed
- Prefer implementations that don't allow null (ArrayDeque, PriorityQueue)"

### Q10: How do you implement a circular queue?

**Answer:**
"A circular queue uses a fixed-size array with wrap-around behavior:

```java
class CircularQueue<T> {
    private Object[] array;
    private int front;
    private int rear;
    private int size;
    private int capacity;
    
    public CircularQueue(int capacity) {
        this.capacity = capacity;
        this.array = new Object[capacity];
        this.front = 0;
        this.rear = -1;
        this.size = 0;
    }
    
    public boolean offer(T item) {
        if (isFull()) {
            return false;
        }
        
        rear = (rear + 1) % capacity;  // Wrap around
        array[rear] = item;
        size++;
        return true;
    }
    
    @SuppressWarnings("unchecked")
    public T poll() {
        if (isEmpty()) {
            return null;
        }
        
        T item = (T) array[front];
        array[front] = null;  // Help GC
        front = (front + 1) % capacity;  // Wrap around
        size--;
        return item;
    }
    
    @SuppressWarnings("unchecked")
    public T peek() {
        if (isEmpty()) {
            return null;
        }
        return (T) array[front];
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    public boolean isFull() {
        return size == capacity;
    }
    
    public int size() {
        return size;
    }
}

// Usage
CircularQueue<Integer> queue = new CircularQueue<>(5);

queue.offer(1);
queue.offer(2);
queue.offer(3);

System.out.println(queue.poll());  // 1
System.out.println(queue.poll());  // 2

queue.offer(4);
queue.offer(5);
queue.offer(6);

System.out.println(queue.size());  // 3
```

**Key Points:**
- Use modulo (%) for wrap-around
- Track front, rear, and size
- Check for full/empty conditions
- O(1) for all operations"

---

## Real-World Examples

### Example 1: Print Queue

```java
class PrintJob {
    String documentName;
    int pages;
    
    PrintJob(String documentName, int pages) {
        this.documentName = documentName;
        this.pages = pages;
    }
    
    @Override
    public String toString() {
        return documentName + " (" + pages + " pages)";
    }
}

class PrintQueue {
    private Queue<PrintJob> queue = new LinkedList<>();
    
    public void addJob(PrintJob job) {
        queue.offer(job);
        System.out.println("Added to queue: " + job);
    }
    
    public void printNext() {
        PrintJob job = queue.poll();
        if (job != null) {
            System.out.println("Printing: " + job);
            // Simulate printing
            try {
                Thread.sleep(job.pages * 100);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            System.out.println("Finished: " + job);
        } else {
            System.out.println("No jobs in queue");
        }
    }
    
    public int getQueueSize() {
        return queue.size();
    }
}

// Usage
PrintQueue printer = new PrintQueue();
printer.addJob(new PrintJob("Document1.pdf", 5));
printer.addJob(new PrintJob("Document2.pdf", 3));
printer.addJob(new PrintJob("Document3.pdf", 10));

while (printer.getQueueSize() > 0) {
    printer.printNext();
}
```

### Example 2: Browser History (Deque)

```java
class BrowserHistory {
    private Deque<String> backStack = new ArrayDeque<>();
    private Deque<String> forwardStack = new ArrayDeque<>();
    private String currentPage;
    
    public BrowserHistory(String homepage) {
        this.currentPage = homepage;
    }
    
    public void visit(String url) {
        if (currentPage != null) {
            backStack.push(currentPage);
        }
        currentPage = url;
        forwardStack.clear();  // Clear forward history
        System.out.println("Visited: " + url);
    }
    
    public String back() {
        if (backStack.isEmpty()) {
            System.out.println("No back history");
            return currentPage;
        }
        
        forwardStack.push(currentPage);
        currentPage = backStack.pop();
        System.out.println("Back to: " + currentPage);
        return currentPage;
    }
    
    public String forward() {
        if (forwardStack.isEmpty()) {
            System.out.println("No forward history");
            return currentPage;
        }
        
        backStack.push(currentPage);
        currentPage = forwardStack.pop();
        System.out.println("Forward to: " + currentPage);
        return currentPage;
    }
    
    public String getCurrentPage() {
        return currentPage;
    }
}

// Usage
BrowserHistory browser = new BrowserHistory("google.com");
browser.visit("youtube.com");
browser.visit("facebook.com");
browser.visit("twitter.com");

browser.back();     // facebook.com
browser.back();     // youtube.com
browser.forward();  // facebook.com
browser.visit("instagram.com");  // Clears forward history
```

### Example 3: Task Scheduler (PriorityQueue)

```java
class ScheduledTask implements Comparable<ScheduledTask> {
    String name;
    int priority;
    long scheduledTime;
    
    ScheduledTask(String name, int priority, long scheduledTime) {
        this.name = name;
        this.priority = priority;
        this.scheduledTime = scheduledTime;
    }
    
    @Override
    public int compareTo(ScheduledTask other) {
        // First by priority (higher first)
        int priorityCompare = Integer.compare(other.priority, this.priority);
        if (priorityCompare != 0) {
            return priorityCompare;
        }
        // Then by time (earlier first)
        return Long.compare(this.scheduledTime, other.scheduledTime);
    }
    
    @Override
    public String toString() {
        return name + " (P" + priority + ")";
    }
}

class TaskScheduler {
    private PriorityQueue<ScheduledTask> taskQueue = new PriorityQueue<>();
    
    public void scheduleTask(ScheduledTask task) {
        taskQueue.offer(task);
        System.out.println("Scheduled: " + task);
    }
    
    public void executeTasks() {
        while (!taskQueue.isEmpty()) {
            ScheduledTask task = taskQueue.poll();
            
            // Wait until scheduled time
            long currentTime = System.currentTimeMillis();
            if (task.scheduledTime > currentTime) {
                try {
                    Thread.sleep(task.scheduledTime - currentTime);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
            
            System.out.println("Executing: " + task);
            // Execute task
        }
    }
}

// Usage
TaskScheduler scheduler = new TaskScheduler();

long now = System.currentTimeMillis();
scheduler.scheduleTask(new ScheduledTask("Low Priority", 1, now + 1000));
scheduler.scheduleTask(new ScheduledTask("High Priority", 5, now + 500));
scheduler.scheduleTask(new ScheduledTask("Medium Priority", 3, now + 750));

scheduler.executeTasks();
// Executes in priority order: High -> Medium -> Low
```

---

## Best Practices

### 1. Choose the Right Implementation

```java
// Good - Use ArrayDeque for Queue
Queue<String> queue = new ArrayDeque<>();

// Good - Use ArrayDeque for Stack
Deque<String> stack = new ArrayDeque<>();

// Good - Use PriorityQueue for priority-based processing
Queue<Task> tasks = new PriorityQueue<>();

// Bad - Don't use LinkedList for Queue
Queue<String> badQueue = new LinkedList<>();

// Bad - Don't use Stack class
Stack<String> badStack = new Stack<>();
```

### 2. Use offer/poll/peek Instead of add/remove/element

```java
// Good - Graceful error handling
if (queue.offer(item)) {
    System.out.println("Added successfully");
}

String item = queue.poll();
if (item != null) {
    process(item);
}

// Bad - Exception handling
try {
    queue.add(item);
    String item = queue.remove();
} catch (Exception e) {
    // Handle exception
}
```

### 3. Check isEmpty() Before Operations

```java
// Good
if (!queue.isEmpty()) {
    String item = queue.poll();
    process(item);
}

// Bad
String item = queue.poll();
if (item != null) {  // Works but less clear
    process(item);
}
```

### 4. Use BlockingQueue for Producer-Consumer

```java
// Good - Thread-safe, blocking
BlockingQueue<Task> queue = new LinkedBlockingQueue<>();

// Producer
queue.put(task);  // Blocks if full

// Consumer
Task task = queue.take();  // Blocks if empty

// Bad - Manual synchronization
Queue<Task> queue = new LinkedList<>();
synchronized(queue) {
    queue.offer(task);
}
```

### 5. Don't Use null in Queues

```java
// Good - Use Optional or sentinel
queue.offer(Optional.of(value));
queue.offer(EMPTY_TASK);  // Sentinel object

// Bad - Ambiguous
queue.offer(null);  // Is queue empty or element is null?
```

---

## Summary

**Queue:**
- ✅ FIFO (First-In-First-Out) ordering
- ✅ Single-ended (add at tail, remove from head)
- ✅ Methods: offer(), poll(), peek()
- ✅ Implementations: LinkedList, ArrayDeque, PriorityQueue

**Deque:**
- ✅ Double-ended (add/remove from both ends)
- ✅ Can function as Queue (FIFO) or Stack (LIFO)
- ✅ Methods: offerFirst/Last(), pollFirst/Last(), peekFirst/Last()
- ✅ Preferred implementation: ArrayDeque

**PriorityQueue:**
- ✅ Heap-based (binary heap)
- ✅ Elements ordered by priority
- ✅ O(log n) insertion and removal
- ✅ Use for priority-based processing

**BlockingQueue:**
- ✅ Thread-safe
- ✅ Blocking operations (put/take)
- ✅ Use for producer-consumer patterns
- ✅ Implementations: ArrayBlockingQueue, LinkedBlockingQueue

**Key Points:**
- Use ArrayDeque instead of LinkedList for Queue/Stack
- Use offer/poll/peek instead of add/remove/element
- Use PriorityQueue for priority-based processing
- Use BlockingQueue for multi-threaded scenarios
- Avoid null in queues

**Interview Tips:**
- Know Queue vs Deque differences
- Understand PriorityQueue internal working (heap)
- Know when to use each implementation
- Can implement queue using stacks
- Understand blocking operations

Master these concepts and you'll handle any Queue/Deque question with confidence!