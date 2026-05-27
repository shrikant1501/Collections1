# Set Interface - Complete Deep Dive

## What is a Set?

A **Set** is a collection that contains **no duplicate elements**. It models the mathematical set abstraction and is used when you need to ensure uniqueness of elements.

## Set Interface Hierarchy

```
        Iterable<E>
            |
        Collection<E>
            |
          Set<E>
            |
    +-------+-------+
    |               |
SortedSet<E>    (HashSet, LinkedHashSet)
    |
NavigableSet<E>
    |
 TreeSet<E>
```

## Set Interface Declaration

```java
public interface Set<E> extends Collection<E>
```

## Core Characteristics

### 1. No Duplicates
- Each element can appear at most once
- Adding duplicate element returns false (doesn't throw exception)
- Uniqueness determined by `equals()` method

### 2. Unordered (in most implementations)
- HashSet: No guaranteed order
- LinkedHashSet: Maintains insertion order
- TreeSet: Maintains sorted order

### 3. At Most One Null Element
- HashSet and LinkedHashSet allow one null
- TreeSet doesn't allow null (throws NullPointerException)

### 4. Mathematical Set Operations
- Union, Intersection, Difference
- Subset checking

## Set Interface Methods

### Basic Operations (Inherited from Collection)

```java
// Add element (returns false if already exists)
boolean add(E e)

// Remove element
boolean remove(Object o)

// Check if contains element
boolean contains(Object o)

// Get size
int size()

// Check if empty
boolean isEmpty()

// Clear all elements
void clear()

// Get iterator
Iterator<E> iterator()
```

### Bulk Operations

```java
// Add all elements from collection
boolean addAll(Collection<? extends E> c)

// Remove all elements in collection
boolean removeAll(Collection<?> c)

// Keep only elements in collection (intersection)
boolean retainAll(Collection<?> c)

// Check if contains all elements
boolean containsAll(Collection<?> c)
```

### Array Conversion

```java
// Convert to array
Object[] toArray()
<T> T[] toArray(T[] a)
```

### Java 8+ Methods

```java
// Remove elements matching predicate
default boolean removeIf(Predicate<? super E> filter)

// Create stream
Stream<E> stream()
Stream<E> parallelStream()

// For each operation
default void forEach(Consumer<? super E> action)
```

## Set Implementations Comparison

### 1. HashSet
- **Backed by**: HashMap
- **Order**: No guaranteed order
- **Performance**: O(1) for add, remove, contains (average)
- **Null**: Allows one null element
- **Thread-safe**: No
- **Use case**: When order doesn't matter, need fast operations

### 2. LinkedHashSet
- **Backed by**: LinkedHashMap (HashMap + LinkedList)
- **Order**: Maintains insertion order
- **Performance**: O(1) for add, remove, contains (slightly slower than HashSet)
- **Null**: Allows one null element
- **Thread-safe**: No
- **Use case**: When insertion order matters

### 3. TreeSet
- **Backed by**: TreeMap (Red-Black Tree)
- **Order**: Sorted order (natural or custom comparator)
- **Performance**: O(log n) for add, remove, contains
- **Null**: Not allowed (throws NullPointerException)
- **Thread-safe**: No
- **Use case**: When sorted order is needed

### 4. EnumSet
- **Backed by**: Bit vector
- **Order**: Natural order of enum constants
- **Performance**: Very fast (bit operations)
- **Null**: Not allowed
- **Thread-safe**: No
- **Use case**: Working with enum types

### 5. CopyOnWriteArraySet
- **Backed by**: CopyOnWriteArrayList
- **Order**: Insertion order
- **Performance**: O(n) for add, remove, contains
- **Null**: Allowed
- **Thread-safe**: Yes
- **Use case**: Concurrent read-heavy scenarios

## Performance Comparison Table

| Operation | HashSet | LinkedHashSet | TreeSet | EnumSet |
|-----------|---------|---------------|---------|---------|
| add() | O(1) | O(1) | O(log n) | O(1) |
| remove() | O(1) | O(1) | O(log n) | O(1) |
| contains() | O(1) | O(1) | O(log n) | O(1) |
| Iteration | O(n) | O(n) | O(n) | O(n) |
| Order | None | Insertion | Sorted | Natural |
| Null allowed | Yes | Yes | No | No |

## Creating Sets

### Empty Sets

```java
// HashSet
Set<String> set1 = new HashSet<>();

// LinkedHashSet
Set<String> set2 = new LinkedHashSet<>();

// TreeSet
Set<String> set3 = new TreeSet<>();

// With initial capacity
Set<String> set4 = new HashSet<>(100);

// With initial capacity and load factor
Set<String> set5 = new HashSet<>(100, 0.75f);
```

### Sets with Initial Values

```java
// Using Arrays.asList
Set<String> set1 = new HashSet<>(Arrays.asList("A", "B", "C"));

// Using Set.of (Java 9+, immutable)
Set<String> set2 = Set.of("A", "B", "C");

// Using Set.copyOf (Java 10+, immutable)
Set<String> set3 = Set.copyOf(existingCollection);

// Double brace initialization (not recommended)
Set<String> set4 = new HashSet<>() {{
    add("A");
    add("B");
    add("C");
}};
```

### From Another Collection

```java
List<String> list = Arrays.asList("A", "B", "C", "A", "B");

// Remove duplicates by converting to Set
Set<String> set = new HashSet<>(list);
// Result: [A, B, C]
```

## Common Operations

### Adding Elements

```java
Set<String> set = new HashSet<>();

// Add single element
boolean added1 = set.add("Apple");     // true
boolean added2 = set.add("Banana");    // true
boolean added3 = set.add("Apple");     // false (duplicate)

// Add multiple elements
set.addAll(Arrays.asList("Cherry", "Date"));
```

### Removing Elements

```java
Set<String> set = new HashSet<>(Arrays.asList("Apple", "Banana", "Cherry"));

// Remove single element
boolean removed = set.remove("Banana");  // true

// Remove multiple elements
set.removeAll(Arrays.asList("Apple", "Date"));

// Remove with condition (Java 8+)
set.removeIf(fruit -> fruit.startsWith("C"));

// Clear all
set.clear();
```

### Checking Elements

```java
Set<String> set = new HashSet<>(Arrays.asList("Apple", "Banana", "Cherry"));

// Check if contains
boolean hasApple = set.contains("Apple");  // true

// Check if contains all
boolean hasAll = set.containsAll(Arrays.asList("Apple", "Banana"));  // true

// Check if empty
boolean empty = set.isEmpty();  // false

// Get size
int size = set.size();  // 3
```

## Mathematical Set Operations

### Union (A ∪ B)

```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4));
Set<Integer> set2 = new HashSet<>(Arrays.asList(3, 4, 5, 6));

// Union - all elements from both sets
Set<Integer> union = new HashSet<>(set1);
union.addAll(set2);
// Result: [1, 2, 3, 4, 5, 6]

// Using Stream (Java 8+)
Set<Integer> union2 = Stream.concat(set1.stream(), set2.stream())
    .collect(Collectors.toSet());
```

### Intersection (A ∩ B)

```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4));
Set<Integer> set2 = new HashSet<>(Arrays.asList(3, 4, 5, 6));

// Intersection - common elements
Set<Integer> intersection = new HashSet<>(set1);
intersection.retainAll(set2);
// Result: [3, 4]

// Using Stream (Java 8+)
Set<Integer> intersection2 = set1.stream()
    .filter(set2::contains)
    .collect(Collectors.toSet());
```

### Difference (A - B)

```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4));
Set<Integer> set2 = new HashSet<>(Arrays.asList(3, 4, 5, 6));

// Difference - elements in set1 but not in set2
Set<Integer> difference = new HashSet<>(set1);
difference.removeAll(set2);
// Result: [1, 2]

// Using Stream (Java 8+)
Set<Integer> difference2 = set1.stream()
    .filter(e -> !set2.contains(e))
    .collect(Collectors.toSet());
```

### Symmetric Difference (A △ B)

```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4));
Set<Integer> set2 = new HashSet<>(Arrays.asList(3, 4, 5, 6));

// Symmetric difference - elements in either set but not in both
Set<Integer> symmetricDiff = new HashSet<>(set1);
symmetricDiff.addAll(set2);
Set<Integer> intersection = new HashSet<>(set1);
intersection.retainAll(set2);
symmetricDiff.removeAll(intersection);
// Result: [1, 2, 5, 6]
```

### Subset Check

```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4, 5));
Set<Integer> set2 = new HashSet<>(Arrays.asList(2, 3));

// Check if set2 is subset of set1
boolean isSubset = set1.containsAll(set2);  // true

// Check if proper subset (subset but not equal)
boolean isProperSubset = set1.containsAll(set2) && !set1.equals(set2);  // true
```

### Superset Check

```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3, 4, 5));
Set<Integer> set2 = new HashSet<>(Arrays.asList(2, 3));

// Check if set1 is superset of set2
boolean isSuperset = set1.containsAll(set2);  // true
```

### Disjoint Check

```java
Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3));
Set<Integer> set2 = new HashSet<>(Arrays.asList(4, 5, 6));

// Check if sets have no common elements
boolean disjoint = Collections.disjoint(set1, set2);  // true
```

## Iterating Sets

### For-Each Loop

```java
Set<String> set = new HashSet<>(Arrays.asList("Apple", "Banana", "Cherry"));

for (String fruit : set) {
    System.out.println(fruit);
}
```

### Iterator

```java
Set<String> set = new HashSet<>(Arrays.asList("Apple", "Banana", "Cherry"));

Iterator<String> iterator = set.iterator();
while (iterator.hasNext()) {
    String fruit = iterator.next();
    System.out.println(fruit);
    
    // Safe removal during iteration
    if (fruit.equals("Banana")) {
        iterator.remove();
    }
}
```

### forEach Method (Java 8+)

```java
Set<String> set = new HashSet<>(Arrays.asList("Apple", "Banana", "Cherry"));

// Using method reference
set.forEach(System.out::println);

// Using lambda
set.forEach(fruit -> System.out.println("Fruit: " + fruit));
```

### Stream API (Java 8+)

```java
Set<String> set = new HashSet<>(Arrays.asList("Apple", "Banana", "Cherry"));

// Filter and collect
Set<String> filtered = set.stream()
    .filter(fruit -> fruit.length() > 5)
    .collect(Collectors.toSet());

// Map and collect
Set<Integer> lengths = set.stream()
    .map(String::length)
    .collect(Collectors.toSet());

// Sorted iteration
set.stream()
    .sorted()
    .forEach(System.out::println);
```

## SortedSet Interface

### Additional Methods

```java
public interface SortedSet<E> extends Set<E> {
    // Get comparator (null if natural ordering)
    Comparator<? super E> comparator();
    
    // Get subset [fromElement, toElement)
    SortedSet<E> subSet(E fromElement, E toElement);
    
    // Get head set [first, toElement)
    SortedSet<E> headSet(E toElement);
    
    // Get tail set [fromElement, last]
    SortedSet<E> tailSet(E fromElement);
    
    // Get first element
    E first();
    
    // Get last element
    E last();
}
```

### Example Usage

```java
SortedSet<Integer> sortedSet = new TreeSet<>(Arrays.asList(5, 2, 8, 1, 9, 3));

System.out.println(sortedSet);           // [1, 2, 3, 5, 8, 9]
System.out.println(sortedSet.first());   // 1
System.out.println(sortedSet.last());    // 9

// Get subset [3, 8)
SortedSet<Integer> subset = sortedSet.subSet(3, 8);
System.out.println(subset);              // [3, 5]

// Get head set [first, 5)
SortedSet<Integer> headSet = sortedSet.headSet(5);
System.out.println(headSet);             // [1, 2, 3]

// Get tail set [5, last]
SortedSet<Integer> tailSet = sortedSet.tailSet(5);
System.out.println(tailSet);             // [5, 8, 9]
```

## NavigableSet Interface

### Additional Methods

```java
public interface NavigableSet<E> extends SortedSet<E> {
    // Get and remove first element
    E pollFirst();
    
    // Get and remove last element
    E pollLast();
    
    // Get greatest element less than or equal to e
    E floor(E e);
    
    // Get least element greater than or equal to e
    E ceiling(E e);
    
    // Get greatest element strictly less than e
    E lower(E e);
    
    // Get least element strictly greater than e
    E higher(E e);
    
    // Get descending set view
    NavigableSet<E> descendingSet();
    
    // Get descending iterator
    Iterator<E> descendingIterator();
}
```

### Example Usage

```java
NavigableSet<Integer> navSet = new TreeSet<>(Arrays.asList(1, 3, 5, 7, 9));

System.out.println(navSet.floor(6));     // 5 (greatest <= 6)
System.out.println(navSet.ceiling(6));   // 7 (least >= 6)
System.out.println(navSet.lower(5));     // 3 (greatest < 5)
System.out.println(navSet.higher(5));    // 7 (least > 5)

System.out.println(navSet.pollFirst());  // 1 (removes and returns)
System.out.println(navSet.pollLast());   // 9 (removes and returns)

// Descending view
NavigableSet<Integer> descending = navSet.descendingSet();
System.out.println(descending);          // [7, 5, 3]
```

## Important Concepts

### 1. Equals and HashCode Contract

For proper Set behavior, elements must correctly implement `equals()` and `hashCode()`:

```java
class Person {
    String name;
    int age;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

// Now Person objects work correctly in HashSet
Set<Person> people = new HashSet<>();
people.add(new Person("Alice", 30));
people.add(new Person("Alice", 30));  // Duplicate, not added
System.out.println(people.size());     // 1
```

### 2. Immutable Sets (Java 9+)

```java
// Create immutable set
Set<String> immutable = Set.of("A", "B", "C");

// Attempting to modify throws UnsupportedOperationException
immutable.add("D");  // Exception!

// Copy to immutable set
Set<String> immutableCopy = Set.copyOf(mutableSet);
```

### 3. Synchronized Sets

```java
// Make HashSet thread-safe
Set<String> syncSet = Collections.synchronizedSet(new HashSet<>());

// Must synchronize when iterating
synchronized(syncSet) {
    Iterator<String> iterator = syncSet.iterator();
    while (iterator.hasNext()) {
        System.out.println(iterator.next());
    }
}
```

### 4. Unmodifiable Sets

```java
Set<String> original = new HashSet<>(Arrays.asList("A", "B", "C"));

// Create unmodifiable view
Set<String> unmodifiable = Collections.unmodifiableSet(original);

// Cannot modify through unmodifiable view
unmodifiable.add("D");  // UnsupportedOperationException

// But can modify through original
original.add("D");  // OK, also reflects in unmodifiable view
```

## Best Practices

### 1. Choose the Right Implementation

```java
// Default choice - when order doesn't matter
Set<String> set = new HashSet<>();

// When insertion order matters
Set<String> orderedSet = new LinkedHashSet<>();

// When sorted order is needed
Set<String> sortedSet = new TreeSet<>();

// For enum types
Set<DayOfWeek> days = EnumSet.of(DayOfWeek.MONDAY, DayOfWeek.FRIDAY);
```

### 2. Specify Initial Capacity

```java
// If you know approximate size
Set<String> set = new HashSet<>(1000);

// Avoids resizing operations
```

### 3. Use Interface Type

```java
// Good - flexible
Set<String> set = new HashSet<>();

// Bad - tied to implementation
HashSet<String> set = new HashSet<>();
```

### 4. Remove Duplicates from List

```java
List<String> listWithDuplicates = Arrays.asList("A", "B", "A", "C", "B");

// Remove duplicates
Set<String> uniqueSet = new HashSet<>(listWithDuplicates);

// Convert back to list if needed
List<String> uniqueList = new ArrayList<>(uniqueSet);
```

### 5. Use EnumSet for Enums

```java
enum Day { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY }

// Very efficient for enum types
Set<Day> weekdays = EnumSet.range(Day.MONDAY, Day.FRIDAY);
Set<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);
```

## Common Pitfalls

### 1. Modifying Elements After Adding

```java
class MutablePerson {
    String name;
    
    @Override
    public int hashCode() {
        return name.hashCode();
    }
}

Set<MutablePerson> set = new HashSet<>();
MutablePerson person = new MutablePerson();
person.name = "Alice";
set.add(person);

// Modifying after adding breaks Set contract
person.name = "Bob";  // Now contains() may not work correctly
```

### 2. Not Implementing equals/hashCode

```java
class BadPerson {
    String name;
    // No equals/hashCode override
}

Set<BadPerson> set = new HashSet<>();
set.add(new BadPerson("Alice"));
set.add(new BadPerson("Alice"));  // Added as different objects!
System.out.println(set.size());    // 2 (should be 1)
```

### 3. Using Mutable Objects as Elements

```java
// Bad - using mutable list as set element
Set<List<String>> set = new HashSet<>();
List<String> list = new ArrayList<>(Arrays.asList("A", "B"));
set.add(list);

list.add("C");  // Modifying after adding - breaks Set contract
```

## When to Use Set

### ✅ Use Set When:
- Need to ensure uniqueness
- Need to perform set operations (union, intersection)
- Order doesn't matter (or use LinkedHashSet/TreeSet)
- Need fast contains() checks
- Removing duplicates from collection

### ❌ Avoid Set When:
- Need to maintain duplicates
- Need positional access (use List)
- Need key-value pairs (use Map)
- Elements don't have proper equals/hashCode

## Summary

**Set Interface provides:**
- No duplicate elements
- Fast membership testing
- Mathematical set operations
- Multiple implementations for different needs

**Key Implementations:**
- **HashSet**: Fast, unordered
- **LinkedHashSet**: Maintains insertion order
- **TreeSet**: Sorted order
- **EnumSet**: Optimized for enums

**Key Takeaways:**
- Use **HashSet** as default choice
- Use **LinkedHashSet** when order matters
- Use **TreeSet** when sorted order needed
- Use **EnumSet** for enum types
- Always implement equals() and hashCode() correctly
- Choose based on performance needs and ordering requirements

---

**Next Steps**: We can explore:
- HashSet implementation details
- TreeSet implementation details
- LinkedHashSet implementation details
- Set operations and algorithms
- Map Interface