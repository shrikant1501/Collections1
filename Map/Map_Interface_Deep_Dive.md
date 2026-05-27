# Map Interface - Complete Deep Dive

## What is a Map?

A **Map** is an object that maps keys to values. It represents a collection of key-value pairs where:
- Each key maps to exactly one value
- Keys must be unique (no duplicate keys)
- Values can be duplicated
- A key can map to at most one value

**Note:** Map is NOT a subtype of Collection interface - it's a separate hierarchy.

## Map Interface Hierarchy

```
        Map<K,V>
            |
    +-------+-------+-------+
    |               |       |
SortedMap<K,V>  HashMap  LinkedHashMap
    |           Hashtable
NavigableMap<K,V>
    |
TreeMap<K,V>
```

## Map Interface Declaration

```java
public interface Map<K, V>
```

Where:
- **K** = Type of keys
- **V** = Type of values

## Core Characteristics

### 1. Key-Value Pairs
- Each entry contains a key and a value
- Key is used to retrieve the value
- One key → One value mapping

### 2. Unique Keys
- No duplicate keys allowed
- Adding same key replaces old value
- Keys form a Set

### 3. Duplicate Values Allowed
- Multiple keys can map to same value
- Values form a Collection (not Set)

### 4. Null Handling
- **HashMap**: Allows one null key, multiple null values
- **TreeMap**: No null keys (NullPointerException), allows null values
- **Hashtable**: No null keys or values
- **LinkedHashMap**: Allows one null key, multiple null values

## Map Interface Methods

### Basic Operations

```java
// Put key-value pair
V put(K key, V value)

// Get value for key
V get(Object key)

// Remove entry by key
V remove(Object key)

// Check if key exists
boolean containsKey(Object key)

// Check if value exists
boolean containsValue(Object value)

// Get size
int size()

// Check if empty
boolean isEmpty()

// Clear all entries
void clear()
```

### Bulk Operations

```java
// Put all entries from another map
void putAll(Map<? extends K, ? extends V> m)
```

### Collection Views

```java
// Get all keys as Set
Set<K> keySet()

// Get all values as Collection
Collection<V> values()

// Get all entries as Set
Set<Map.Entry<K, V>> entrySet()
```

### Java 8+ Methods

```java
// Get value or default if key not found
V getOrDefault(Object key, V defaultValue)

// Put if key is absent
V putIfAbsent(K key, V value)

// Remove only if key maps to specific value
boolean remove(Object key, Object value)

// Replace value for key
V replace(K key, V value)

// Replace only if key maps to specific value
boolean replace(K key, V oldValue, V newValue)

// Compute value for key
V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)

// Compute if key is absent
V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction)

// Compute if key is present
V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)

// Merge values
V merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remappingFunction)

// For each entry
void forEach(BiConsumer<? super K, ? super V> action)

// Replace all values
void replaceAll(BiFunction<? super K, ? super V, ? extends V> function)
```

## Map.Entry Interface

Represents a key-value pair in the map.

```java
interface Entry<K, V> {
    K getKey()
    V getValue()
    V setValue(V value)
    
    // Java 8+ static methods
    static <K extends Comparable<? super K>, V> Comparator<Map.Entry<K,V>> comparingByKey()
    static <K, V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue()
}
```

## Map Implementations Comparison

### 1. HashMap
- **Backed by**: Hash table (array + linked list/tree)
- **Order**: No guaranteed order
- **Performance**: O(1) for get/put (average)
- **Null**: One null key, multiple null values
- **Thread-safe**: No
- **Use case**: General-purpose, when order doesn't matter

### 2. LinkedHashMap
- **Backed by**: Hash table + doubly-linked list
- **Order**: Maintains insertion order (or access order)
- **Performance**: O(1) for get/put (slightly slower than HashMap)
- **Null**: One null key, multiple null values
- **Thread-safe**: No
- **Use case**: When insertion/access order matters

### 3. TreeMap
- **Backed by**: Red-Black Tree
- **Order**: Sorted by keys (natural or custom comparator)
- **Performance**: O(log n) for get/put
- **Null**: No null keys, allows null values
- **Thread-safe**: No
- **Use case**: When sorted order is needed

### 4. Hashtable
- **Backed by**: Hash table
- **Order**: No guaranteed order
- **Performance**: O(1) for get/put (synchronized overhead)
- **Null**: No null keys or values
- **Thread-safe**: Yes (synchronized)
- **Use case**: Legacy code (prefer ConcurrentHashMap)

### 5. ConcurrentHashMap
- **Backed by**: Segmented hash table
- **Order**: No guaranteed order
- **Performance**: O(1) for get/put (lock-free reads)
- **Null**: No null keys or values
- **Thread-safe**: Yes (lock striping)
- **Use case**: Concurrent access without external synchronization

### 6. WeakHashMap
- **Backed by**: Hash table with weak references
- **Order**: No guaranteed order
- **Performance**: O(1) for get/put
- **Null**: One null key, multiple null values
- **Thread-safe**: No
- **Use case**: Cache implementations, memory-sensitive applications

### 7. IdentityHashMap
- **Backed by**: Hash table (uses == instead of equals)
- **Order**: No guaranteed order
- **Performance**: O(1) for get/put
- **Null**: Allows null keys and values
- **Thread-safe**: No
- **Use case**: When reference equality is needed

### 8. EnumMap
- **Backed by**: Array
- **Order**: Natural order of enum constants
- **Performance**: Very fast (array access)
- **Null**: No null keys, allows null values
- **Thread-safe**: No
- **Use case**: Keys are enum types

## Performance Comparison Table

| Operation | HashMap | LinkedHashMap | TreeMap | Hashtable | ConcurrentHashMap |
|-----------|---------|---------------|---------|-----------|-------------------|
| get() | O(1) | O(1) | O(log n) | O(1) | O(1) |
| put() | O(1) | O(1) | O(log n) | O(1) | O(1) |
| remove() | O(1) | O(1) | O(log n) | O(1) | O(1) |
| containsKey() | O(1) | O(1) | O(log n) | O(1) | O(1) |
| Iteration | O(n) | O(n) | O(n) | O(n) | O(n) |
| Order | None | Insertion | Sorted | None | None |
| Null key | Yes | Yes | No | No | No |
| Thread-safe | No | No | No | Yes | Yes |

## Creating Maps

### Empty Maps

```java
// HashMap
Map<String, Integer> map1 = new HashMap<>();

// LinkedHashMap
Map<String, Integer> map2 = new LinkedHashMap<>();

// TreeMap
Map<String, Integer> map3 = new TreeMap<>();

// With initial capacity
Map<String, Integer> map4 = new HashMap<>(100);

// With initial capacity and load factor
Map<String, Integer> map5 = new HashMap<>(100, 0.75f);
```

### Maps with Initial Values

```java
// Using Map.of (Java 9+, immutable, up to 10 entries)
Map<String, Integer> map1 = Map.of(
    "Apple", 1,
    "Banana", 2,
    "Cherry", 3
);

// Using Map.ofEntries (Java 9+, immutable, any number of entries)
Map<String, Integer> map2 = Map.ofEntries(
    Map.entry("Apple", 1),
    Map.entry("Banana", 2),
    Map.entry("Cherry", 3)
);

// Using constructor with another map
Map<String, Integer> map3 = new HashMap<>(existingMap);

// Double brace initialization (not recommended)
Map<String, Integer> map4 = new HashMap<>() {{
    put("Apple", 1);
    put("Banana", 2);
    put("Cherry", 3);
}};
```

## Common Operations

### Adding Entries

```java
Map<String, Integer> map = new HashMap<>();

// Put key-value pair
map.put("Apple", 1);
map.put("Banana", 2);
map.put("Cherry", 3);

// Put if absent (Java 8+)
map.putIfAbsent("Date", 4);      // Added
map.putIfAbsent("Apple", 10);    // Not added (key exists)

// Put all from another map
Map<String, Integer> more = Map.of("Elderberry", 5, "Fig", 6);
map.putAll(more);

// Compute if absent (Java 8+)
map.computeIfAbsent("Grape", k -> k.length());  // Adds "Grape" -> 5
```

### Accessing Values

```java
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 1);
map.put("Banana", 2);

// Get value
Integer value = map.get("Apple");  // 1

// Get or default (Java 8+)
Integer value2 = map.getOrDefault("Mango", 0);  // 0 (not found)

// Check if key exists
boolean hasApple = map.containsKey("Apple");  // true

// Check if value exists
boolean hasOne = map.containsValue(1);  // true
```

### Updating Values

```java
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 1);

// Replace value
Integer oldValue = map.put("Apple", 10);  // Returns 1, updates to 10

// Replace (Java 8+)
map.replace("Apple", 20);  // Updates to 20

// Replace only if old value matches
boolean replaced = map.replace("Apple", 20, 30);  // true, updates to 30

// Compute new value (Java 8+)
map.compute("Apple", (k, v) -> v * 2);  // Doubles the value

// Compute if present (Java 8+)
map.computeIfPresent("Apple", (k, v) -> v + 1);  // Increments value

// Merge (Java 8+)
map.merge("Apple", 5, Integer::sum);  // Adds 5 to existing value
```

### Removing Entries

```java
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 1);
map.put("Banana", 2);
map.put("Cherry", 3);

// Remove by key
Integer removed = map.remove("Banana");  // Returns 2

// Remove only if value matches (Java 8+)
boolean removed2 = map.remove("Apple", 1);  // true

// Clear all
map.clear();
```

## Iterating Maps

### Using entrySet()

```java
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 1);
map.put("Banana", 2);
map.put("Cherry", 3);

// Iterate over entries
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    String key = entry.getKey();
    Integer value = entry.getValue();
    System.out.println(key + " = " + value);
}
```

### Using keySet()

```java
// Iterate over keys
for (String key : map.keySet()) {
    Integer value = map.get(key);
    System.out.println(key + " = " + value);
}
```

### Using values()

```java
// Iterate over values only
for (Integer value : map.values()) {
    System.out.println(value);
}
```

### Using forEach (Java 8+)

```java
// forEach with lambda
map.forEach((key, value) -> System.out.println(key + " = " + value));

// forEach with method reference
map.forEach((k, v) -> System.out.printf("%s: %d%n", k, v));
```

### Using Stream API (Java 8+)

```java
// Filter and collect
Map<String, Integer> filtered = map.entrySet().stream()
    .filter(entry -> entry.getValue() > 1)
    .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));

// Transform values
Map<String, Integer> doubled = map.entrySet().stream()
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        entry -> entry.getValue() * 2
    ));

// Sort by key
map.entrySet().stream()
    .sorted(Map.Entry.comparingByKey())
    .forEach(entry -> System.out.println(entry.getKey() + " = " + entry.getValue()));

// Sort by value
map.entrySet().stream()
    .sorted(Map.Entry.comparingByValue())
    .forEach(entry -> System.out.println(entry.getKey() + " = " + entry.getValue()));
```

## SortedMap Interface

### Additional Methods

```java
public interface SortedMap<K, V> extends Map<K, V> {
    // Get comparator
    Comparator<? super K> comparator();
    
    // Get submap [fromKey, toKey)
    SortedMap<K, V> subMap(K fromKey, K toKey);
    
    // Get head map [first, toKey)
    SortedMap<K, V> headMap(K toKey);
    
    // Get tail map [fromKey, last]
    SortedMap<K, V> tailMap(K fromKey);
    
    // Get first key
    K firstKey();
    
    // Get last key
    K lastKey();
}
```

### Example Usage

```java
SortedMap<String, Integer> sortedMap = new TreeMap<>();
sortedMap.put("Banana", 2);
sortedMap.put("Apple", 1);
sortedMap.put("Cherry", 3);
sortedMap.put("Date", 4);

System.out.println(sortedMap);  // {Apple=1, Banana=2, Cherry=3, Date=4}

// Get first and last keys
String first = sortedMap.firstKey();  // "Apple"
String last = sortedMap.lastKey();    // "Date"

// Get submap
SortedMap<String, Integer> subMap = sortedMap.subMap("Banana", "Date");
System.out.println(subMap);  // {Banana=2, Cherry=3}

// Get head map
SortedMap<String, Integer> headMap = sortedMap.headMap("Cherry");
System.out.println(headMap);  // {Apple=1, Banana=2}

// Get tail map
SortedMap<String, Integer> tailMap = sortedMap.tailMap("Cherry");
System.out.println(tailMap);  // {Cherry=3, Date=4}
```

## NavigableMap Interface

### Additional Methods

```java
public interface NavigableMap<K, V> extends SortedMap<K, V> {
    // Get and remove first entry
    Map.Entry<K, V> pollFirstEntry();
    
    // Get and remove last entry
    Map.Entry<K, V> pollLastEntry();
    
    // Get entry with greatest key <= given key
    Map.Entry<K, V> floorEntry(K key);
    K floorKey(K key);
    
    // Get entry with least key >= given key
    Map.Entry<K, V> ceilingEntry(K key);
    K ceilingKey(K key);
    
    // Get entry with greatest key < given key
    Map.Entry<K, V> lowerEntry(K key);
    K lowerKey(K key);
    
    // Get entry with least key > given key
    Map.Entry<K, V> higherEntry(K key);
    K higherKey(K key);
    
    // Get first entry
    Map.Entry<K, V> firstEntry();
    
    // Get last entry
    Map.Entry<K, V> lastEntry();
    
    // Get descending map view
    NavigableMap<K, V> descendingMap();
    
    // Get descending key set
    NavigableSet<K> descendingKeySet();
}
```

### Example Usage

```java
NavigableMap<Integer, String> navMap = new TreeMap<>();
navMap.put(1, "One");
navMap.put(3, "Three");
navMap.put(5, "Five");
navMap.put(7, "Seven");
navMap.put(9, "Nine");

// Floor and ceiling
System.out.println(navMap.floorKey(6));    // 5 (greatest <= 6)
System.out.println(navMap.ceilingKey(6));  // 7 (least >= 6)

// Lower and higher
System.out.println(navMap.lowerKey(5));    // 3 (greatest < 5)
System.out.println(navMap.higherKey(5));   // 7 (least > 5)

// First and last entries
Map.Entry<Integer, String> first = navMap.firstEntry();  // 1=One
Map.Entry<Integer, String> last = navMap.lastEntry();    // 9=Nine

// Poll entries
Map.Entry<Integer, String> polled = navMap.pollFirstEntry();  // Removes and returns 1=One

// Descending view
NavigableMap<Integer, String> descending = navMap.descendingMap();
System.out.println(descending);  // {9=Nine, 7=Seven, 5=Five, 3=Three}
```

## Important Concepts

### 1. HashMap Internal Working

HashMap uses an array of buckets with linked lists/trees for collision handling:

```
Key "Apple" → hashCode() → bucket index → store Entry(key, value)
```

**Load Factor**: Default 0.75
- When 75% full, capacity doubles
- All entries are rehashed

### 2. TreeMap Internal Working

TreeMap uses a Red-Black Tree:
- Keys must be comparable
- Maintains sorted order
- O(log n) operations

### 3. LinkedHashMap Order

Two ordering modes:
- **Insertion order** (default): Maintains order of insertion
- **Access order**: Moves accessed entries to end (useful for LRU cache)

```java
// Access-order LinkedHashMap
Map<String, Integer> lruMap = new LinkedHashMap<>(16, 0.75f, true);
```

### 4. Immutable Maps (Java 9+)

```java
// Immutable map
Map<String, Integer> immutable = Map.of("A", 1, "B", 2);

// Attempting to modify throws UnsupportedOperationException
immutable.put("C", 3);  // Exception!

// Copy to immutable
Map<String, Integer> immutableCopy = Map.copyOf(mutableMap);
```

### 5. Synchronized Maps

```java
// Make HashMap thread-safe
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// Must synchronize when iterating
synchronized(syncMap) {
    for (Map.Entry<String, Integer> entry : syncMap.entrySet()) {
        System.out.println(entry.getKey() + " = " + entry.getValue());
    }
}
```

## Best Practices

### 1. Choose the Right Implementation

```java
// Default choice
Map<String, Integer> map = new HashMap<>();

// When order matters
Map<String, Integer> orderedMap = new LinkedHashMap<>();

// When sorted order needed
Map<String, Integer> sortedMap = new TreeMap<>();

// For concurrent access
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();

// For enum keys
Map<DayOfWeek, String> enumMap = new EnumMap<>(DayOfWeek.class);
```

### 2. Use Interface Type

```java
// Good - flexible
Map<String, Integer> map = new HashMap<>();

// Bad - tied to implementation
HashMap<String, Integer> map = new HashMap<>();
```

### 3. Specify Initial Capacity

```java
// If you know approximate size
Map<String, Integer> map = new HashMap<>(1000);
```

### 4. Use computeIfAbsent for Complex Initialization

```java
Map<String, List<Integer>> map = new HashMap<>();

// Old way
if (!map.containsKey("key")) {
    map.put("key", new ArrayList<>());
}
map.get("key").add(1);

// Better way (Java 8+)
map.computeIfAbsent("key", k -> new ArrayList<>()).add(1);
```

### 5. Use merge for Aggregation

```java
Map<String, Integer> wordCount = new HashMap<>();

// Count word occurrences
for (String word : words) {
    wordCount.merge(word, 1, Integer::sum);
}
```

## Common Pitfalls

### 1. Modifying Keys After Adding

```java
class MutableKey {
    int value;
    
    @Override
    public int hashCode() {
        return value;
    }
}

Map<MutableKey, String> map = new HashMap<>();
MutableKey key = new MutableKey();
key.value = 1;
map.put(key, "Value");

// Modifying key breaks map
key.value = 2;
System.out.println(map.get(key));  // null (lost!)
```

### 2. Not Implementing equals/hashCode

```java
class BadKey {
    String name;
    // No equals/hashCode override
}

Map<BadKey, String> map = new HashMap<>();
map.put(new BadKey("test"), "value");
System.out.println(map.get(new BadKey("test")));  // null (different objects)
```

### 3. ConcurrentModificationException

```java
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);

// Wrong - modifying during iteration
for (String key : map.keySet()) {
    map.remove(key);  // ConcurrentModificationException!
}

// Correct - use iterator
Iterator<String> iterator = map.keySet().iterator();
while (iterator.hasNext()) {
    iterator.next();
    iterator.remove();  // Safe
}
```

## When to Use Map

### ✅ Use Map When:
- Need key-value associations
- Need fast lookup by key
- Keys are unique
- Need to count occurrences
- Need to group data

### ❌ Avoid Map When:
- Don't need key-value pairs (use List or Set)
- Keys are not unique (use Multimap from libraries)
- Need ordered collection by index (use List)

## Summary

**Map Interface provides:**
- Key-value pair storage
- Fast lookup by key
- Multiple implementations for different needs
- Rich API for manipulation

**Key Implementations:**
- **HashMap**: Fast, unordered
- **LinkedHashMap**: Maintains insertion/access order
- **TreeMap**: Sorted by keys
- **ConcurrentHashMap**: Thread-safe

**Key Takeaways:**
- Use **HashMap** as default choice
- Use **LinkedHashMap** when order matters
- Use **TreeMap** when sorted order needed
- Use **ConcurrentHashMap** for concurrent access
- Always implement equals() and hashCode() for custom keys
- Don't modify keys after adding to map

---

**Next Steps**: We can explore:
- HashMap implementation details
- TreeMap implementation details
- ConcurrentHashMap internals
- Map operations and algorithms
- Queue/Deque interfaces