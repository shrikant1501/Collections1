# ArrayList - Complete Implementation Details

## Overview

**ArrayList** is a resizable array implementation of the List interface. It's one of the most commonly used collection classes in Java.

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, Serializable
```

## Key Characteristics

- **Backed by**: Dynamic array (Object[])
- **Resizable**: Automatically grows when needed
- **Random Access**: O(1) time for get/set operations
- **Ordered**: Maintains insertion order
- **Duplicates**: Allowed
- **Null values**: Allowed
- **Thread-safe**: No (not synchronized)
- **Fail-fast**: Iterator throws ConcurrentModificationException

## Internal Structure

### Core Fields

```java
public class ArrayList<E> {
    // Default initial capacity
    private static final int DEFAULT_CAPACITY = 10;
    
    // Shared empty array instance
    private static final Object[] EMPTY_ELEMENTDATA = {};
    
    // Shared empty array for default sized empty instances
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    
    // The array buffer storing elements
    transient Object[] elementData;
    
    // The size of the ArrayList (number of elements)
    private int size;
    
    // Modification count for fail-fast behavior
    protected transient int modCount = 0;
}
```

### Why Object[] and not E[]?

- Java doesn't allow creation of generic arrays directly
- Type erasure removes generic type information at runtime
- Elements are cast to E when retrieved

## Constructors

### 1. Default Constructor
```java
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```
- Creates empty list with initial capacity 0
- First add() triggers expansion to DEFAULT_CAPACITY (10)
- **Lazy initialization** - saves memory if list remains empty

### 2. Constructor with Initial Capacity
```java
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
    }
}
```
- Useful when you know approximate size
- Avoids multiple resizing operations
- Better performance for large collections

### 3. Constructor with Collection
```java
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // c.toArray might not return Object[]
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```
- Creates ArrayList from existing collection
- Copies all elements from the collection

## Core Operations

### 1. add(E element) - Add at End

```java
public boolean add(E e) {
    modCount++;
    add(e, elementData, size);
    return true;
}

private void add(E e, Object[] elementData, int s) {
    if (s == elementData.length)
        elementData = grow();
    elementData[s] = e;
    size = s + 1;
}
```

**Time Complexity:**
- **Amortized O(1)** - most operations are O(1)
- **Worst case O(n)** - when resizing is needed

**Steps:**
1. Increment modCount (for fail-fast)
2. Check if array is full
3. If full, grow the array
4. Add element at end
5. Increment size

### 2. add(int index, E element) - Add at Index

```java
public void add(int index, E element) {
    rangeCheckForAdd(index);
    modCount++;
    final int s;
    Object[] elementData;
    if ((s = size) == (elementData = this.elementData).length)
        elementData = grow();
    System.arraycopy(elementData, index,
                     elementData, index + 1,
                     s - index);
    elementData[index] = element;
    size = s + 1;
}
```

**Time Complexity:** O(n)
- Must shift all elements after index to the right

**Steps:**
1. Validate index
2. Grow array if needed
3. Shift elements from index to end by one position
4. Insert element at index
5. Increment size

### 3. get(int index) - Retrieve Element

```java
public E get(int index) {
    Objects.checkIndex(index, size);
    return elementData(index);
}

E elementData(int index) {
    return (E) elementData[index];
}
```

**Time Complexity:** O(1)
- Direct array access
- This is ArrayList's biggest advantage

**Steps:**
1. Check if index is valid
2. Return element at index (with cast to E)

### 4. set(int index, E element) - Replace Element

```java
public E set(int index, E element) {
    Objects.checkIndex(index, size);
    E oldValue = elementData(index);
    elementData[index] = element;
    return oldValue;
}
```

**Time Complexity:** O(1)
- Direct array access

**Steps:**
1. Validate index
2. Get old value
3. Replace with new element
4. Return old value

### 5. remove(int index) - Remove by Index

```java
public E remove(int index) {
    Objects.checkIndex(index, size);
    final Object[] es = elementData;
    
    @SuppressWarnings("unchecked") E oldValue = (E) es[index];
    fastRemove(es, index);
    
    return oldValue;
}

private void fastRemove(Object[] es, int i) {
    modCount++;
    final int newSize;
    if ((newSize = size - 1) > i)
        System.arraycopy(es, i + 1, es, i, newSize - i);
    es[size = newSize] = null; // Clear for GC
}
```

**Time Complexity:** O(n)
- Must shift elements after index to the left

**Steps:**
1. Validate index
2. Get element to return
3. Shift elements left to fill gap
4. Set last element to null (for GC)
5. Decrement size
6. Return removed element

### 6. remove(Object o) - Remove by Value

```java
public boolean remove(Object o) {
    final Object[] es = elementData;
    final int size = this.size;
    int i = 0;
    found: {
        if (o == null) {
            for (; i < size; i++)
                if (es[i] == null)
                    break found;
        } else {
            for (; i < size; i++)
                if (o.equals(es[i]))
                    break found;
        }
        return false;
    }
    fastRemove(es, i);
    return true;
}
```

**Time Complexity:** O(n)
- Must search for element (O(n))
- Then shift elements (O(n))

**Steps:**
1. Search for first occurrence of element
2. If found, remove using fastRemove
3. Return true if removed, false otherwise

### 7. contains(Object o) - Check Existence

```java
public boolean contains(Object o) {
    return indexOf(o) >= 0;
}

public int indexOf(Object o) {
    return indexOfRange(o, 0, size);
}

int indexOfRange(Object o, int start, int end) {
    Object[] es = elementData;
    if (o == null) {
        for (int i = start; i < end; i++) {
            if (es[i] == null) {
                return i;
            }
        }
    } else {
        for (int i = start; i < end; i++) {
            if (o.equals(es[i])) {
                return i;
            }
        }
    }
    return -1;
}
```

**Time Complexity:** O(n)
- Linear search through array

## Dynamic Resizing (Growth Strategy)

### grow() Method

```java
private Object[] grow() {
    return grow(size + 1);
}

private Object[] grow(int minCapacity) {
    int oldCapacity = elementData.length;
    if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        int newCapacity = ArraysSupport.newLength(oldCapacity,
                minCapacity - oldCapacity, /* minimum growth */
                oldCapacity >> 1           /* preferred growth (50%) */);
        return elementData = Arrays.copyOf(elementData, newCapacity);
    } else {
        return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];
    }
}
```

### Growth Formula

**New Capacity = Old Capacity + (Old Capacity / 2)**

Approximately **50% increase** each time

**Example:**
```
Initial: 10
After 1st growth: 15
After 2nd growth: 22
After 3rd growth: 33
After 4th growth: 49
After 5th growth: 73
```

### Why 50% Growth?

**Advantages:**
- Balances memory usage and performance
- Fewer reallocations than smaller growth
- Less memory waste than doubling (like Vector)

**Comparison:**
- **ArrayList (50%)**: Better memory efficiency
- **Vector (100%)**: Faster growth but more memory waste

### ensureCapacity() Method

```java
public void ensureCapacity(int minCapacity) {
    if (minCapacity > elementData.length
        && !(elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
             && minCapacity <= DEFAULT_CAPACITY)) {
        modCount++;
        grow(minCapacity);
    }
}
```

**Usage:**
```java
ArrayList<String> list = new ArrayList<>();
list.ensureCapacity(1000); // Pre-allocate space
// Now add 1000 elements without resizing
```

### trimToSize() Method

```java
public void trimToSize() {
    modCount++;
    if (size < elementData.length) {
        elementData = (size == 0)
          ? EMPTY_ELEMENTDATA
          : Arrays.copyOf(elementData, size);
    }
}
```

**Usage:**
```java
ArrayList<String> list = new ArrayList<>(1000);
// Add only 100 elements
list.trimToSize(); // Reduce capacity to 100
```

## Memory Considerations

### Memory Overhead

**Per ArrayList Object:**
- Object header: ~12-16 bytes
- elementData reference: 4-8 bytes
- size field: 4 bytes
- modCount field: 4 bytes
- **Total**: ~24-32 bytes + array overhead

**Per Array:**
- Array header: ~12-16 bytes
- Elements: capacity × (4-8 bytes per reference)

**Example:**
```java
ArrayList<String> list = new ArrayList<>(); // capacity 0
list.add("A"); // capacity becomes 10

// Memory used:
// ArrayList object: ~32 bytes
// Array: 16 bytes + (10 × 8) = 96 bytes
// Total: ~128 bytes (for 1 element!)
```

### Memory Waste

**Scenario:**
```java
ArrayList<String> list = new ArrayList<>(1000);
// Add only 10 elements
// Wasted space: 990 element slots
```

**Solution:**
```java
list.trimToSize(); // Reduce capacity to actual size
```

## Performance Characteristics

### Time Complexity Summary

| Operation | Average Case | Worst Case | Notes |
|-----------|-------------|------------|-------|
| get(index) | O(1) | O(1) | Direct array access |
| set(index, element) | O(1) | O(1) | Direct array access |
| add(element) | O(1) | O(n) | Amortized O(1), O(n) when resizing |
| add(index, element) | O(n) | O(n) | Must shift elements |
| remove(index) | O(n) | O(n) | Must shift elements |
| remove(object) | O(n) | O(n) | Search + shift |
| contains(object) | O(n) | O(n) | Linear search |
| indexOf(object) | O(n) | O(n) | Linear search |
| clear() | O(n) | O(n) | Must null out elements |
| size() | O(1) | O(1) | Field access |
| isEmpty() | O(1) | O(1) | Field comparison |

### Space Complexity

- **Best case**: O(n) - when size equals capacity
- **Worst case**: O(2n) - just after resizing (old + new array temporarily)
- **Average**: O(1.5n) - considering 50% growth factor

## Iterator Implementation

### Iterator

```java
private class Itr implements Iterator<E> {
    int cursor;       // index of next element
    int lastRet = -1; // index of last element returned; -1 if no such
    int expectedModCount = modCount;

    public boolean hasNext() {
        return cursor != size;
    }

    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        return (E) elementData[lastRet = i];
    }

    public void remove() {
        if (lastRet < 0)
            throw new IllegalStateException();
        checkForComodification();

        try {
            ArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;
            expectedModCount = modCount;
        } catch (IndexOutOfBoundsException ex) {
            throw new ConcurrentModificationException();
        }
    }

    final void checkForComodification() {
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
}
```

### Fail-Fast Behavior

**How it works:**
1. Iterator stores `expectedModCount` at creation
2. Before each operation, checks if `modCount == expectedModCount`
3. If not equal, throws `ConcurrentModificationException`

**Example:**
```java
ArrayList<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
Iterator<String> iterator = list.iterator();

while (iterator.hasNext()) {
    String item = iterator.next();
    list.add("D"); // Modifies modCount
    // Next iterator.next() will throw ConcurrentModificationException
}
```

**Safe Modification:**
```java
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if (condition) {
        iterator.remove(); // Safe - updates expectedModCount
    }
}
```

## Serialization

### Custom Serialization

```java
private void writeObject(java.io.ObjectOutputStream s)
    throws java.io.IOException {
    int expectedModCount = modCount;
    s.defaultWriteObject();

    // Write out size
    s.writeInt(size);

    // Write out all elements in proper order
    for (int i=0; i<size; i++) {
        s.writeObject(elementData[i]);
    }

    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}

private void readObject(java.io.ObjectInputStream s)
    throws java.io.IOException, ClassNotFoundException {
    s.defaultReadObject();

    // Read in size
    int size = s.readInt();

    // Allocate array
    SharedSecrets.getJavaObjectInputStreamAccess().checkArray(s, Object[].class, size);
    Object[] elements = new Object[size];

    // Read in all elements
    for (int i = 0; i < size; i++) {
        elements[i] = s.readObject();
    }

    elementData = elements;
    this.size = size;
}
```

**Why Custom Serialization?**
- `elementData` is marked `transient`
- Only serialize actual elements (size), not entire capacity
- Saves space when capacity > size

## Best Practices

### 1. Specify Initial Capacity When Known

```java
// Bad - multiple resizing operations
ArrayList<String> list = new ArrayList<>();
for (int i = 0; i < 10000; i++) {
    list.add("Item " + i);
}

// Good - single allocation
ArrayList<String> list = new ArrayList<>(10000);
for (int i = 0; i < 10000; i++) {
    list.add("Item " + i);
}
```

### 2. Use ensureCapacity for Bulk Operations

```java
ArrayList<String> list = new ArrayList<>();
list.ensureCapacity(10000); // Pre-allocate
for (int i = 0; i < 10000; i++) {
    list.add("Item " + i);
}
```

### 3. Trim Unused Capacity

```java
ArrayList<String> list = new ArrayList<>(10000);
// Add only 100 elements
list.trimToSize(); // Free unused memory
```

### 4. Use for-each or Iterator for Removal

```java
// Wrong
for (int i = 0; i < list.size(); i++) {
    if (condition) {
        list.remove(i); // Index shifts, elements skipped
    }
}

// Correct - iterate backwards
for (int i = list.size() - 1; i >= 0; i--) {
    if (condition) {
        list.remove(i);
    }
}

// Better - use iterator
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (condition) {
        it.remove();
    }
}

// Best - use removeIf (Java 8+)
list.removeIf(item -> condition);
```

### 5. Avoid Frequent Insertions at Beginning

```java
// Bad - O(n) for each insertion
ArrayList<String> list = new ArrayList<>();
for (int i = 0; i < 10000; i++) {
    list.add(0, "Item " + i); // Shifts all elements
}

// Good - use LinkedList or add at end then reverse
LinkedList<String> list = new LinkedList<>();
for (int i = 0; i < 10000; i++) {
    list.addFirst("Item " + i);
}
```

## Common Pitfalls

### 1. Concurrent Modification

```java
// Wrong
for (String item : list) {
    if (item.equals("remove")) {
        list.remove(item); // ConcurrentModificationException
    }
}

// Correct
list.removeIf(item -> item.equals("remove"));
```

### 2. Index Shifting During Removal

```java
// Wrong - skips elements
for (int i = 0; i < list.size(); i++) {
    if (condition) {
        list.remove(i); // Next element shifts to current index
    }
}

// Correct - iterate backwards
for (int i = list.size() - 1; i >= 0; i--) {
    if (condition) {
        list.remove(i);
    }
}
```

### 3. Unnecessary Capacity

```java
// Wastes memory
ArrayList<String> list = new ArrayList<>(1000000);
list.add("single item");
// 999,999 unused slots

// Better
ArrayList<String> list = new ArrayList<>();
list.add("single item");
```

### 4. Using == Instead of equals()

```java
ArrayList<String> list = new ArrayList<>(Arrays.asList("A", "B"));
String search = new String("A");

// Wrong
boolean found = (list.get(0) == search); // false (different objects)

// Correct
boolean found = list.get(0).equals(search); // true
boolean found = list.contains(search); // true
```

## Performance Optimization Tips

### 1. Batch Operations

```java
// Slower - multiple modCount increments
for (String item : items) {
    list.add(item);
}

// Faster - single modCount increment
list.addAll(items);
```

### 2. Avoid Autoboxing in Loops

```java
// Slower - boxing/unboxing overhead
ArrayList<Integer> list = new ArrayList<>();
for (int i = 0; i < 10000; i++) {
    list.add(i); // Autoboxing
}

// Consider primitive collections for better performance
// (from libraries like Trove, FastUtil, Eclipse Collections)
```

### 3. Use subList for Range Operations

```java
// Efficient - no copying
List<String> subList = list.subList(10, 20);
subList.clear(); // Removes elements 10-19 from original list
```

### 4. Clear vs New Instance

```java
// Reuses existing array
list.clear();

// Creates new object and array
list = new ArrayList<>();

// If you need to keep reference, use clear()
// If starting fresh, new instance might be better
```

## When to Use ArrayList

### ✅ Use ArrayList When:
- Random access is frequent (get/set operations)
- Iteration is the primary operation
- Adding elements at the end
- Size is relatively stable or known in advance
- Memory efficiency is important (compared to LinkedList)

### ❌ Avoid ArrayList When:
- Frequent insertions/deletions at beginning or middle
- Frequent size changes with unknown capacity
- Need thread-safe operations (use CopyOnWriteArrayList or synchronize)
- Working with primitives (consider specialized collections)

## Summary

**ArrayList Strengths:**
- Fast random access: O(1)
- Memory efficient (compared to LinkedList)
- Cache-friendly (contiguous memory)
- Simple and widely used

**ArrayList Weaknesses:**
- Slow insertions/deletions in middle: O(n)
- Resizing overhead
- Not thread-safe
- Wasted capacity if size << capacity

**Key Implementation Details:**
- Backed by Object[] array
- Grows by ~50% when full
- Fail-fast iterators
- Custom serialization for efficiency
- Lazy initialization for memory efficiency

---

**Next Steps**: We can explore:
- LinkedList implementation details
- ArrayList vs LinkedList performance comparison
- Set Interface and implementations
- Map Interface and implementations