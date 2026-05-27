# Set - Internal Working, Collection Selection & Interview Traps

## Table of Contents
1. [Internal Working of Set Implementations](#internal-working-of-set-implementations)
2. [When to Use Which Collection](#when-to-use-which-collection)
3. [Common Interview Traps](#common-interview-traps)

---

## 1. Internal Working of Set Implementations

### HashSet Internal Working

#### Architecture

```
HashSet
    |
    └── HashMap (backing data structure)
            |
            └── Array of Node[] (buckets)
                    |
                    └── LinkedList/TreeNode (collision handling)
```

#### Core Implementation

```java
public class HashSet<E> extends AbstractSet<E>
    implements Set<E>, Cloneable, Serializable {
    
    // HashSet is backed by a HashMap
    private transient HashMap<E, Object> map;
    
    // Dummy value to associate with Object in backing Map
    private static final Object PRESENT = new Object();
    
    public HashSet() {
        map = new HashMap<>();
    }
    
    public boolean add(E e) {
        return map.put(e, PRESENT) == null;
    }
    
    public boolean remove(Object o) {
        return map.remove(o) == PRESENT;
    }
    
    public boolean contains(Object o) {
        return map.containsKey(o);
    }
    
    public int size() {
        return map.size();
    }
}
```

#### How HashSet Works

**1. Adding Element:**
```
Element "Apple"
    ↓
hashCode() → 63476538
    ↓
Hash Function → index = hash % capacity
    ↓
Bucket[index]
    ↓
If empty: Add new Node
If occupied: Check equals()
    - If equal: Don't add (duplicate)
    - If not equal: Add to linked list (collision)
```

**2. Hash Calculation:**
```java
// Simplified hash calculation
int hash = key.hashCode();
int index = (capacity - 1) & hash;  // Equivalent to hash % capacity
```

**3. Collision Handling:**
- **Java 7**: Linked List (O(n) worst case)
- **Java 8+**: Linked List → Red-Black Tree when threshold reached (O(log n))
  - Threshold: 8 elements in same bucket
  - Converts back to list when < 6 elements

**4. Load Factor and Resizing:**
```java
// Default values
int DEFAULT_INITIAL_CAPACITY = 16;
float DEFAULT_LOAD_FACTOR = 0.75f;

// Resize when: size > capacity * loadFactor
// New capacity = old capacity * 2
```

**Example:**
```java
HashSet<String> set = new HashSet<>();
// Initial capacity: 16
// Threshold: 16 * 0.75 = 12

// After adding 12 elements, next add triggers resize
// New capacity: 32
// New threshold: 32 * 0.75 = 24
```

#### Memory Structure

```
HashSet Object
├── HashMap reference (8 bytes)
└── HashMap Object
    ├── Node[] table (reference: 8 bytes)
    ├── size (4 bytes)
    ├── threshold (4 bytes)
    ├── loadFactor (4 bytes)
    └── modCount (4 bytes)
    
Node[] Array (capacity * 8 bytes for references)
└── Each Node
    ├── hash (4 bytes)
    ├── key (8 bytes reference)
    ├── value (8 bytes reference to PRESENT)
    └── next (8 bytes reference)
```

---

### TreeSet Internal Working

#### Architecture

```
TreeSet
    |
    └── TreeMap (backing data structure)
            |
            └── Red-Black Tree
                    |
                    └── TreeNode (Entry)
                            ├── key
                            ├── value (PRESENT)
                            ├── left child
                            ├── right child
                            ├── parent
                            └── color (RED/BLACK)
```

#### Core Implementation

```java
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, Serializable {
    
    // TreeSet is backed by a TreeMap
    private transient NavigableMap<E, Object> m;
    
    private static final Object PRESENT = new Object();
    
    public TreeSet() {
        this(new TreeMap<>());
    }
    
    public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
    }
    
    public boolean add(E e) {
        return m.put(e, PRESENT) == null;
    }
    
    public boolean contains(Object o) {
        return m.containsKey(o);
    }
}
```

#### Red-Black Tree Properties

1. **Every node is either RED or BLACK**
2. **Root is always BLACK**
3. **All leaves (null) are BLACK**
4. **RED node cannot have RED children**
5. **Every path from root to leaf has same number of BLACK nodes**

#### How TreeSet Works

**1. Adding Element:**
```
Element "Cherry"
    ↓
Compare with root using compareTo() or Comparator
    ↓
If less: Go left
If greater: Go right
If equal: Don't add (duplicate)
    ↓
Insert at appropriate position
    ↓
Rebalance tree (rotations and recoloring)
```

**2. Tree Balancing:**
```java
// After insertion, tree may need rebalancing
// Operations: Left Rotation, Right Rotation, Recoloring

Example Tree:
        5 (BLACK)
       / \
      3   7 (RED)
         / \
        6   9 (RED)  ← Violation! (RED-RED)
        
After Rebalancing:
        5 (BLACK)
       / \
      3   7 (BLACK)  ← Recolored
         / \
        6   9 (RED)
```

**3. Time Complexity:**
- Height of Red-Black Tree: O(log n)
- All operations: O(log n)

#### Memory Structure

```
TreeSet Object
├── TreeMap reference (8 bytes)
└── TreeMap Object
    ├── root (8 bytes reference)
    ├── size (4 bytes)
    ├── comparator (8 bytes reference)
    └── modCount (4 bytes)
    
Each TreeNode (Entry)
├── key (8 bytes reference)
├── value (8 bytes reference to PRESENT)
├── left (8 bytes reference)
├── right (8 bytes reference)
├── parent (8 bytes reference)
└── color (1 byte: boolean)
```

---

### LinkedHashSet Internal Working

#### Architecture

```
LinkedHashSet
    |
    └── LinkedHashMap (backing data structure)
            |
            ├── HashMap (hash table)
            └── Doubly Linked List (maintains order)
```

#### Core Implementation

```java
public class LinkedHashSet<E> extends HashSet<E>
    implements Set<E>, Cloneable, Serializable {
    
    public LinkedHashSet() {
        super(16, 0.75f, true);  // true = use LinkedHashMap
    }
    
    // Inherits all methods from HashSet
    // But uses LinkedHashMap internally
}
```

#### How LinkedHashSet Works

**1. Structure:**
```
Hash Table (for O(1) access)
    +
Doubly Linked List (for order)

Example:
Hash Table:
Bucket[0]: null
Bucket[1]: Node("B") ←→ Node("E")
Bucket[2]: Node("A")
Bucket[3]: Node("C") ←→ Node("D")

Linked List (insertion order):
head → A ↔ B ↔ C ↔ D ↔ E ← tail
```

**2. Node Structure:**
```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after;  // For linked list
    
    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}
```

**3. Memory Overhead:**
- HashSet: 1 reference per node (next)
- LinkedHashSet: 3 references per node (next, before, after)
- ~50% more memory than HashSet

---

## 2. When to Use Which Collection

### Complete Decision Tree

```
Need to store elements?
    |
    ├─ Need key-value pairs? → Use MAP
    |   ├─ Order doesn't matter? → HashMap
    |   ├─ Insertion order? → LinkedHashMap
    |   └─ Sorted order? → TreeMap
    |
    ├─ Need duplicates? → Use LIST
    |   ├─ Random access? → ArrayList
    |   ├─ Frequent insertions at ends? → LinkedList
    |   └─ Thread-safe? → CopyOnWriteArrayList
    |
    ├─ No duplicates? → Use SET
    |   ├─ Order doesn't matter? → HashSet
    |   ├─ Insertion order? → LinkedHashSet
    |   ├─ Sorted order? → TreeSet
    |   └─ Enum type? → EnumSet
    |
    └─ FIFO/LIFO? → Use QUEUE/DEQUE
        ├─ FIFO? → LinkedList, ArrayDeque
        ├─ Priority? → PriorityQueue
        └─ Thread-safe? → BlockingQueue implementations
```

### Detailed Comparison Matrix

| Requirement | Best Choice | Alternative | Avoid |
|-------------|-------------|-------------|-------|
| Fast random access | ArrayList | Vector | LinkedList |
| Frequent insertions at ends | LinkedList | ArrayDeque | ArrayList |
| No duplicates, fast lookup | HashSet | LinkedHashSet | TreeSet (if order not needed) |
| No duplicates, sorted | TreeSet | - | HashSet |
| No duplicates, insertion order | LinkedHashSet | - | HashSet |
| Key-value pairs | HashMap | LinkedHashMap | Hashtable |
| Sorted key-value | TreeMap | - | HashMap |
| Thread-safe list | CopyOnWriteArrayList | Collections.synchronizedList() | ArrayList |
| Thread-safe set | CopyOnWriteArraySet | Collections.synchronizedSet() | HashSet |
| Thread-safe map | ConcurrentHashMap | Collections.synchronizedMap() | HashMap |
| Enum elements | EnumSet | HashSet | TreeSet |
| FIFO queue | ArrayDeque | LinkedList | ArrayList |
| Priority queue | PriorityQueue | TreeSet | ArrayList |

### Performance Characteristics

```
Operation Speed (Best to Worst):

Random Access:
ArrayList > Vector > LinkedList

Add/Remove at ends:
LinkedList ≈ ArrayDeque > ArrayList

Add/Remove in middle:
LinkedList > ArrayList

Contains/Search:
HashSet > LinkedHashSet > TreeSet > ArrayList > LinkedList

Sorted iteration:
TreeSet > (sort ArrayList) > HashSet

Memory efficiency:
ArrayList > HashSet > LinkedHashSet > TreeSet > LinkedList
```

### Real-World Use Cases

#### 1. HashSet
```java
// Remove duplicates
List<String> listWithDuplicates = Arrays.asList("A", "B", "A", "C");
Set<String> unique = new HashSet<>(listWithDuplicates);

// Fast membership testing
Set<String> validUsers = new HashSet<>(Arrays.asList("user1", "user2", "user3"));
if (validUsers.contains(username)) {
    // O(1) lookup
}

// Finding common elements
Set<Integer> set1 = new HashSet<>(list1);
set1.retainAll(list2);  // Intersection
```

#### 2. TreeSet
```java
// Maintaining sorted unique elements
TreeSet<Integer> scores = new TreeSet<>();
scores.add(85);
scores.add(92);
scores.add(78);
System.out.println(scores);  // [78, 85, 92]

// Range queries
NavigableSet<Integer> highScores = scores.tailSet(80);  // Scores >= 80

// Leaderboard
TreeSet<Player> leaderboard = new TreeSet<>(
    Comparator.comparingInt(Player::getScore).reversed()
);
```

#### 3. LinkedHashSet
```java
// Maintaining insertion order without duplicates
LinkedHashSet<String> visitedPages = new LinkedHashSet<>();
visitedPages.add("home");
visitedPages.add("products");
visitedPages.add("home");  // Duplicate, not added
// Order: [home, products]

// LRU Cache (with manual management)
LinkedHashSet<String> cache = new LinkedHashSet<>();
```

#### 4. ArrayList
```java
// Dynamic array with frequent reads
List<Product> products = new ArrayList<>();
Product p = products.get(index);  // O(1)

// Building result lists
List<String> results = new ArrayList<>();
for (String item : source) {
    if (condition) {
        results.add(item);
    }
}
```

#### 5. LinkedList
```java
// Queue implementation
Queue<Task> taskQueue = new LinkedList<>();
taskQueue.offer(task);
Task next = taskQueue.poll();

// Deque implementation
Deque<String> history = new LinkedList<>();
history.addFirst(page);  // Add to front
history.removeLast();    // Remove from back
```

---

## 3. Common Interview Traps

### Trap 1: HashSet with Mutable Objects

**The Trap:**
```java
class Person {
    String name;
    
    Person(String name) {
        this.name = name;
    }
    
    @Override
    public int hashCode() {
        return name.hashCode();
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Person)) return false;
        Person person = (Person) o;
        return Objects.equals(name, person.name);
    }
}

Set<Person> set = new HashSet<>();
Person person = new Person("Alice");
set.add(person);

System.out.println(set.contains(person));  // true

// TRAP: Modifying after adding
person.name = "Bob";
System.out.println(set.contains(person));  // false! (Lost in HashSet)
```

**Why it happens:**
- HashCode changed after adding
- Element is in wrong bucket
- contains() looks in different bucket

**Solution:**
```java
// Make Person immutable
class Person {
    private final String name;  // final
    
    Person(String name) {
        this.name = name;
    }
    
    // No setters
    // Only getters
}
```

---

### Trap 2: TreeSet with Inconsistent Comparator

**The Trap:**
```java
class Person {
    String name;
    int age;
    
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

// Comparator only compares age
TreeSet<Person> set = new TreeSet<>(Comparator.comparingInt(p -> p.age));

set.add(new Person("Alice", 30));
set.add(new Person("Bob", 30));    // Not added! (same age)
set.add(new Person("Charlie", 30)); // Not added! (same age)

System.out.println(set.size());  // 1 (Expected 3!)
```

**Why it happens:**
- TreeSet uses comparator for equality
- compareTo() == 0 means duplicate
- Different objects with same age are considered equal

**Solution:**
```java
// Compare by age, then by name for tie-breaking
TreeSet<Person> set = new TreeSet<>(
    Comparator.comparingInt((Person p) -> p.age)
              .thenComparing(p -> p.name)
);

set.add(new Person("Alice", 30));
set.add(new Person("Bob", 30));
set.add(new Person("Charlie", 30));

System.out.println(set.size());  // 3 (Correct!)
```

---

### Trap 3: equals() and hashCode() Inconsistency

**The Trap:**
```java
class Person {
    String name;
    int age;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Person)) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }
    
    // TRAP: hashCode() only uses name, but equals() uses both
    @Override
    public int hashCode() {
        return Objects.hash(name);  // Missing age!
    }
}

Set<Person> set = new HashSet<>();
set.add(new Person("Alice", 30));
set.add(new Person("Alice", 25));  // Should be different, but...

// Unpredictable behavior!
```

**Why it happens:**
- equals() says they're different
- hashCode() says they're same
- Violates hashCode contract

**Solution:**
```java
@Override
public int hashCode() {
    return Objects.hash(name, age);  // Use same fields as equals()
}
```

**The Contract:**
```
If a.equals(b) == true, then a.hashCode() == b.hashCode()
If a.hashCode() == b.hashCode(), a.equals(b) may be true or false
```

---

### Trap 4: ConcurrentModificationException

**The Trap:**
```java
Set<String> set = new HashSet<>(Arrays.asList("A", "B", "C", "D"));

// TRAP: Modifying while iterating
for (String item : set) {
    if (item.equals("B")) {
        set.remove(item);  // ConcurrentModificationException!
    }
}
```

**Why it happens:**
- Iterator detects structural modification
- modCount != expectedModCount
- Fail-fast behavior

**Solutions:**
```java
// Solution 1: Use Iterator.remove()
Iterator<String> iterator = set.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if (item.equals("B")) {
        iterator.remove();  // Safe
    }
}

// Solution 2: Use removeIf (Java 8+)
set.removeIf(item -> item.equals("B"));

// Solution 3: Collect items to remove, then remove
Set<String> toRemove = new HashSet<>();
for (String item : set) {
    if (item.equals("B")) {
        toRemove.add(item);
    }
}
set.removeAll(toRemove);
```

---

### Trap 5: TreeSet with null Elements

**The Trap:**
```java
TreeSet<String> set = new TreeSet<>();
set.add("Apple");
set.add(null);  // NullPointerException!
```

**Why it happens:**
- TreeSet uses compareTo() or compare()
- Cannot compare null with non-null
- No natural ordering for null

**Solution:**
```java
// Use custom comparator that handles null
TreeSet<String> set = new TreeSet<>(Comparator.nullsFirst(Comparator.naturalOrder()));
set.add("Apple");
set.add(null);  // OK now
set.add("Banana");
System.out.println(set);  // [null, Apple, Banana]
```

---

### Trap 6: HashSet Performance Degradation

**The Trap:**
```java
class BadHashCode {
    String value;
    
    @Override
    public int hashCode() {
        return 1;  // TRAP: Always returns same hash!
    }
}

Set<BadHashCode> set = new HashSet<>();
for (int i = 0; i < 10000; i++) {
    set.add(new BadHashCode("value" + i));
}

// Performance degrades to O(n) for all operations!
```

**Why it happens:**
- All elements go to same bucket
- Bucket becomes long linked list
- O(1) becomes O(n)

**Solution:**
```java
class GoodHashCode {
    String value;
    
    @Override
    public int hashCode() {
        return Objects.hash(value);  // Proper distribution
    }
}
```

---

### Trap 7: Set.of() with Duplicates

**The Trap:**
```java
// TRAP: Throws IllegalArgumentException
Set<String> set = Set.of("A", "B", "C", "A");  // Exception!
```

**Why it happens:**
- Set.of() checks for duplicates at creation
- Throws exception if duplicate found

**Solution:**
```java
// Use constructor if duplicates possible
Set<String> set = new HashSet<>(Arrays.asList("A", "B", "C", "A"));
// Result: [A, B, C]
```

---

### Trap 8: Comparing Sets with Different Implementations

**The Trap:**
```java
Set<String> hashSet = new HashSet<>(Arrays.asList("A", "B", "C"));
Set<String> treeSet = new TreeSet<>(Arrays.asList("A", "B", "C"));

// Are they equal?
System.out.println(hashSet.equals(treeSet));  // true

// But iteration order is different!
System.out.println(hashSet);  // [A, B, C] (random order)
System.out.println(treeSet);  // [A, B, C] (sorted order)
```

**Why it happens:**
- Set.equals() only checks elements, not order
- Different implementations can be equal

**Key Point:**
```java
// Set equality: same elements, regardless of order
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3));
Set<Integer> set2 = new TreeSet<>(Arrays.asList(3, 2, 1));
System.out.println(set1.equals(set2));  // true
```

---

### Trap 9: Memory Leak with HashSet

**The Trap:**
```java
class Resource {
    private byte[] data = new byte[1024 * 1024];  // 1MB
    
    @Override
    public int hashCode() {
        return 1;
    }
    
    @Override
    public boolean equals(Object o) {
        return this == o;  // Only reference equality
    }
}

Set<Resource> cache = new HashSet<>();
for (int i = 0; i < 1000; i++) {
    cache.add(new Resource());  // All added (equals uses ==)
}
// Memory: 1000 MB!
```

**Why it happens:**
- equals() uses reference equality
- Each object is unique
- All objects retained in memory

**Solution:**
```java
// Implement proper equals() based on content
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Resource)) return false;
    Resource resource = (Resource) o;
    return Arrays.equals(data, resource.data);
}
```

---

### Trap 10: Subset Check Confusion

**The Trap:**
```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4, 5));
Set<Integer> set2 = new HashSet<>(Arrays.asList(2, 3));

// Is set2 subset of set1?
boolean isSubset = set2.containsAll(set1);  // WRONG! false

// Correct way
boolean isSubset = set1.containsAll(set2);  // true
```

**Why it happens:**
- Confusion about which set should contain which
- containsAll() checks if caller contains all elements of argument

**Remember:**
```
A.containsAll(B) → "Does A contain all elements of B?"
                 → "Is B a subset of A?"
```

---

## Interview Tips

### Most Asked Questions:

1. **How does HashSet ensure uniqueness?**
   - Uses HashMap internally
   - Elements as keys, dummy PRESENT as value
   - HashMap doesn't allow duplicate keys

2. **Why is HashSet faster than TreeSet?**
   - HashSet: O(1) average (hash table)
   - TreeSet: O(log n) (balanced tree)

3. **Can HashSet contain null?**
   - Yes, one null element
   - TreeSet: No (NullPointerException)

4. **What happens if you modify an object after adding to HashSet?**
   - HashCode changes
   - Element lost in wrong bucket
   - contains() returns false

5. **Difference between HashSet and LinkedHashSet?**
   - HashSet: No order
   - LinkedHashSet: Maintains insertion order
   - LinkedHashSet: ~50% more memory

6. **How to make HashSet thread-safe?**
   ```java
   Set<String> syncSet = Collections.synchronizedSet(new HashSet<>());
   // Or use CopyOnWriteArraySet
   ```

7. **When to use TreeSet over HashSet?**
   - Need sorted order
   - Need range queries (subSet, headSet, tailSet)
   - Need floor, ceiling operations

### Quick Reference Card

```
HashSet:
✓ Fast (O(1))
✓ Allows null
✗ No order
✗ Not thread-safe

LinkedHashSet:
✓ Fast (O(1))
✓ Insertion order
✓ Allows null
✗ More memory
✗ Not thread-safe

TreeSet:
✓ Sorted order
✓ Range queries
✗ Slower (O(log n))
✗ No null
✗ Not thread-safe

EnumSet:
✓ Very fast
✓ Memory efficient
✓ Type-safe
✗ Only for enums
```

---

## Summary

**Key Takeaways:**

1. **HashSet** uses HashMap internally with dummy values
2. **TreeSet** uses Red-Black Tree for sorted order
3. **LinkedHashSet** combines hash table with linked list
4. Always implement **equals() and hashCode()** correctly
5. Don't modify objects after adding to Set
6. Use **Iterator.remove()** or **removeIf()** for safe removal
7. TreeSet doesn't allow null elements
8. Choose implementation based on requirements:
   - Speed → HashSet
   - Order → LinkedHashSet/TreeSet
   - Enums → EnumSet

**Common Mistakes to Avoid:**
- Mutable objects in Set
- Inconsistent equals/hashCode
- Modifying during iteration
- Poor hashCode distribution
- Wrong comparator in TreeSet

This completes the internal working and interview guide for Set!