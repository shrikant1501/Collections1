# List Interface - Complete Deep Dive

## What is a List?

A **List** is an ordered collection (also known as a sequence) that allows:
- **Duplicate elements**
- **Positional access** to elements (index-based)
- **Ordered insertion** - maintains insertion order
- **Null elements** (in most implementations)

## List Interface Hierarchy

```
        Iterable<E>
            |
        Collection<E>
            |
         List<E>
            |
    +-------+-------+-------+
    |       |       |       |
ArrayList LinkedList Vector CopyOnWriteArrayList
                     |
                   Stack
```

## List Interface Declaration

```java
public interface List<E> extends Collection<E>
```

## Core Characteristics

### 1. Ordered Collection
- Elements maintain their insertion order
- Each element has a specific position (index)
- Index starts from 0

### 2. Positional Access
- Access elements by integer index
- Insert elements at specific positions
- Remove elements from specific positions

### 3. Duplicates Allowed
- Same element can appear multiple times
- Each occurrence has its own index

### 4. Null Elements
- Most List implementations allow null elements
- Can have multiple null values

## List Interface Methods

### Positional Access Operations

```java
// Get element at index
E get(int index)

// Replace element at index
E set(int index, E element)

// Add element at index (shifts subsequent elements)
void add(int index, E element)

// Remove element at index (shifts subsequent elements)
E remove(int index)
```

### Search Operations

```java
// Returns index of first occurrence (-1 if not found)
int indexOf(Object o)

// Returns index of last occurrence (-1 if not found)
int lastIndexOf(Object o)
```

### List Iterators

```java
// Returns list iterator starting at beginning
ListIterator<E> listIterator()

// Returns list iterator starting at specified position
ListIterator<E> listIterator(int index)
```

### Range-View Operations

```java
// Returns view of portion of list [fromIndex, toIndex)
List<E> subList(int fromIndex, int toIndex)
```

### Inherited from Collection

```java
// Basic operations
boolean add(E e)
boolean remove(Object o)
boolean contains(Object o)
int size()
boolean isEmpty()
void clear()

// Bulk operations
boolean addAll(Collection<? extends E> c)
boolean removeAll(Collection<?> c)
boolean retainAll(Collection<?> c)

// Array conversion
Object[] toArray()
<T> T[] toArray(T[] a)

// Iteration
Iterator<E> iterator()

// Java 8+ operations
default void replaceAll(UnaryOperator<E> operator)
default void sort(Comparator<? super E> c)
Stream<E> stream()
```

## ListIterator Interface

ListIterator extends Iterator with bidirectional traversal:

```java
// Forward traversal
boolean hasNext()
E next()
int nextIndex()

// Backward traversal
boolean hasPrevious()
E previous()
int previousIndex()

// Modification during iteration
void remove()
void set(E e)
void add(E e)
```

**Key Differences from Iterator:**
- Bidirectional traversal (forward and backward)
- Can modify list during iteration
- Can obtain iterator position (index)

## List Implementations Comparison

### 1. ArrayList
- **Backed by**: Dynamic array
- **Access Time**: O(1)
- **Insertion/Deletion**: O(n) - requires shifting
- **Memory**: Contiguous memory allocation
- **Best for**: Random access, iteration
- **Thread-safe**: No
- **Null allowed**: Yes

### 2. LinkedList
- **Backed by**: Doubly-linked list
- **Access Time**: O(n)
- **Insertion/Deletion**: O(1) at ends, O(n) in middle
- **Memory**: Non-contiguous, extra memory for node pointers
- **Best for**: Frequent insertions/deletions
- **Thread-safe**: No
- **Null allowed**: Yes
- **Also implements**: Queue, Deque interfaces

### 3. Vector
- **Backed by**: Dynamic array (like ArrayList)
- **Access Time**: O(1)
- **Insertion/Deletion**: O(n)
- **Thread-safe**: Yes (synchronized methods)
- **Performance**: Slower than ArrayList due to synchronization
- **Legacy**: Pre-Collections Framework class
- **Growth**: Doubles in size (ArrayList grows by 50%)
- **Null allowed**: Yes

### 4. Stack
- **Extends**: Vector
- **Type**: LIFO (Last-In-First-Out)
- **Thread-safe**: Yes (inherits from Vector)
- **Legacy**: Use ArrayDeque instead
- **Special methods**: push(), pop(), peek()

### 5. CopyOnWriteArrayList
- **Backed by**: Array (creates new copy on modification)
- **Thread-safe**: Yes (without external synchronization)
- **Best for**: Read-heavy scenarios with rare modifications
- **Iterator**: Fail-safe (snapshot of array)
- **Performance**: Expensive writes, fast reads
- **Package**: java.util.concurrent

## Performance Comparison Table

| Operation | ArrayList | LinkedList | Vector | CopyOnWriteArrayList |
|-----------|-----------|------------|--------|---------------------|
| get(index) | O(1) | O(n) | O(1) | O(1) |
| add(element) | O(1)* | O(1) | O(1)* | O(n) |
| add(index, element) | O(n) | O(n) | O(n) | O(n) |
| remove(index) | O(n) | O(n) | O(n) | O(n) |
| contains(element) | O(n) | O(n) | O(n) | O(n) |
| iterator.remove() | O(n) | O(1) | O(n) | N/A |

*Amortized O(1) - occasionally O(n) when resizing

## Common Use Cases

### When to Use ArrayList
```java
// Random access to elements
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
String first = names.get(0); // Fast O(1)

// Iteration over elements
for (String name : names) {
    System.out.println(name);
}

// When you know approximate size
List<Integer> numbers = new ArrayList<>(1000);
```

### When to Use LinkedList
```java
// Frequent insertions/deletions at beginning/end
List<Task> taskQueue = new LinkedList<>();
taskQueue.add(0, newTask); // Efficient at start

// Implementing Queue/Deque
Queue<String> queue = new LinkedList<>();
Deque<String> deque = new LinkedList<>();

// When memory fragmentation is a concern
```

### When to Use Vector
```java
// Legacy code compatibility
// Thread-safe operations without external synchronization
// Generally prefer Collections.synchronizedList(new ArrayList<>())
```

### When to Use CopyOnWriteArrayList
```java
// Concurrent read-heavy scenarios
List<Observer> observers = new CopyOnWriteArrayList<>();

// Event listeners
// Configuration data that rarely changes
// Fail-safe iteration during concurrent modifications
```

## Important Concepts

### 1. Initial Capacity and Load Factor

**ArrayList:**
```java
// Default capacity: 10
List<String> list1 = new ArrayList<>();

// Custom initial capacity
List<String> list2 = new ArrayList<>(100);

// From existing collection
List<String> list3 = new ArrayList<>(existingCollection);
```

**Growth Strategy:**
- ArrayList: New capacity = (oldCapacity * 3)/2 + 1 (approximately 50% increase)
- Vector: New capacity = oldCapacity * 2 (100% increase)

### 2. Fail-Fast Iterators

```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    list.add("C"); // Throws ConcurrentModificationException
}
```

**Solution:**
```java
// Use iterator's remove method
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if (condition) {
        iterator.remove(); // Safe
    }
}

// Or use CopyOnWriteArrayList for concurrent modifications
```

### 3. SubList Views

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));

// SubList is a view, not a copy
List<Integer> subList = numbers.subList(1, 4); // [2, 3, 4]

// Changes to subList affect original list
subList.set(0, 20); // numbers becomes [1, 20, 3, 4, 5]

// Changes to original list affect subList
numbers.add(2, 100); // subList is now invalid (ConcurrentModificationException)
```

### 4. Sorting Lists

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(5, 2, 8, 1, 9));

// Natural ordering (Comparable)
Collections.sort(numbers);
// or
numbers.sort(null);

// Custom ordering (Comparator)
numbers.sort((a, b) -> b - a); // Descending order

// Java 8+ with Comparator
numbers.sort(Comparator.naturalOrder());
numbers.sort(Comparator.reverseOrder());
```

### 5. Synchronization

```java
// Making ArrayList thread-safe
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// Must synchronize on list when iterating
synchronized(syncList) {
    Iterator<String> iterator = syncList.iterator();
    while (iterator.hasNext()) {
        System.out.println(iterator.next());
    }
}
```

### 6. Immutable Lists

```java
// Java 9+ factory methods
List<String> immutable1 = List.of("A", "B", "C");

// Java 10+ copyOf
List<String> immutable2 = List.copyOf(mutableList);

// Pre-Java 9
List<String> immutable3 = Collections.unmodifiableList(new ArrayList<>(Arrays.asList("A", "B")));

// Attempting to modify throws UnsupportedOperationException
immutable1.add("D"); // Exception!
```

## Best Practices

### 1. Choose the Right Implementation
```java
// Default choice for most scenarios
List<String> list = new ArrayList<>();

// Frequent insertions/deletions at ends
List<String> list = new LinkedList<>();

// Thread-safe with high read frequency
List<String> list = new CopyOnWriteArrayList<>();
```

### 2. Specify Initial Capacity
```java
// If you know approximate size
List<String> list = new ArrayList<>(1000);
// Avoids multiple resizing operations
```

### 3. Use Interface Type
```java
// Good - flexible
List<String> list = new ArrayList<>();

// Bad - tied to implementation
ArrayList<String> list = new ArrayList<>();
```

### 4. Avoid Unnecessary Boxing
```java
// Bad - boxing overhead
List<Integer> numbers = new ArrayList<>();

// Better for primitives - use specialized collections
IntList numbers = new IntArrayList(); // From libraries like Trove, FastUtil
```

### 5. Use Enhanced For-Loop or Streams
```java
// Enhanced for-loop
for (String item : list) {
    System.out.println(item);
}

// Stream API (Java 8+)
list.stream()
    .filter(s -> s.length() > 5)
    .forEach(System.out::println);
```

### 6. Remove Elements Safely
```java
// Wrong - ConcurrentModificationException
for (String item : list) {
    if (condition) {
        list.remove(item); // Exception!
    }
}

// Correct - use iterator
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    if (condition) {
        iterator.remove();
    }
}

// Or use removeIf (Java 8+)
list.removeIf(item -> condition);
```

## Common Pitfalls

### 1. Index Out of Bounds
```java
List<String> list = new ArrayList<>();
list.add("A");
String item = list.get(1); // IndexOutOfBoundsException
```

### 2. Modifying During Iteration
```java
for (String item : list) {
    list.remove(item); // ConcurrentModificationException
}
```

### 3. Comparing Lists
```java
List<String> list1 = new ArrayList<>(Arrays.asList("A", "B"));
List<String> list2 = new ArrayList<>(Arrays.asList("A", "B"));

// Correct - uses equals()
boolean equal = list1.equals(list2); // true

// Wrong - compares references
boolean same = (list1 == list2); // false
```

### 4. SubList Invalidation
```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4));
List<Integer> subList = list.subList(1, 3);
list.add(5); // Invalidates subList
subList.get(0); // ConcurrentModificationException
```

## Java 8+ Features with Lists

### 1. forEach with Lambda
```java
list.forEach(item -> System.out.println(item));
list.forEach(System.out::println);
```

### 2. replaceAll
```java
List<String> list = new ArrayList<>(Arrays.asList("a", "b", "c"));
list.replaceAll(String::toUpperCase); // [A, B, C]
```

### 3. removeIf
```java
list.removeIf(s -> s.length() < 3);
```

### 4. Stream Operations
```java
List<String> filtered = list.stream()
    .filter(s -> s.startsWith("A"))
    .collect(Collectors.toList());

List<Integer> lengths = list.stream()
    .map(String::length)
    .collect(Collectors.toList());
```

## Summary

**List Interface provides:**
- Ordered collection with positional access
- Duplicate elements allowed
- Rich API for manipulation
- Multiple implementations for different use cases

**Key Takeaways:**
- Use **ArrayList** for general-purpose scenarios (default choice)
- Use **LinkedList** for frequent insertions/deletions at ends
- Use **CopyOnWriteArrayList** for concurrent read-heavy scenarios
- Avoid **Vector** and **Stack** (legacy classes)
- Always use interface type (List) in declarations
- Be careful with concurrent modifications during iteration
- Choose appropriate initial capacity for better performance

---

**Next Steps**: We can explore:
- ArrayList implementation details
- LinkedList implementation details
- Set Interface and implementations
- Map Interface and implementations
- Queue/Deque interfaces