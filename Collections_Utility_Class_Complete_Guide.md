# Collections Utility Class - Complete Guide

## Simple Explanation

### What is Collections Class?

**Collections** = A utility class that provides static methods to operate on collections (like sorting, searching, reversing, etc.)

**Real-World Analogy:**
Think of Collections class as a **Swiss Army Knife** for collections:
- Just like a Swiss Army Knife has multiple tools (knife, scissors, screwdriver)
- Collections class has multiple utility methods (sort, reverse, shuffle, etc.)
- You don't create an instance; you just use the tools directly

**Important:** Don't confuse `Collections` (utility class) with `Collection` (interface)!

---

## Key Characteristics

1. **Utility Class** - Contains only static methods
2. **Cannot be Instantiated** - Constructor is private
3. **Works with Collections** - Operates on List, Set, Map, etc.
4. **Thread-Safe Wrappers** - Provides synchronized collections
5. **Immutable Wrappers** - Provides unmodifiable collections

---

## Categories of Methods

### 1. Sorting Methods
### 2. Searching Methods
### 3. Reversing and Shuffling
### 4. Min/Max Methods
### 5. Frequency and Disjoint
### 6. Replacing and Filling
### 7. Copying and Swapping
### 8. Rotating and Indexing
### 9. Synchronized Collections
### 10. Unmodifiable Collections
### 11. Empty Collections
### 12. Singleton Collections
### 13. Checked Collections

---

## 1. Sorting Methods

### sort(List<T> list)

Sorts a list in natural order (ascending).

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(5, 2, 8, 1, 9));
Collections.sort(numbers);
System.out.println(numbers);
// Output: [1, 2, 5, 8, 9]

List<String> names = new ArrayList<>(Arrays.asList("Charlie", "Alice", "Bob"));
Collections.sort(names);
System.out.println(names);
// Output: [Alice, Bob, Charlie]
```

### sort(List<T> list, Comparator<? super T> c)

Sorts a list using a custom comparator.

```java
List<String> names = new ArrayList<>(Arrays.asList("Alice", "Bob", "Charlie"));

// Sort by length
Collections.sort(names, (a, b) -> a.length() - b.length());
System.out.println(names);
// Output: [Bob, Alice, Charlie]

// Sort in reverse order
Collections.sort(names, Collections.reverseOrder());
System.out.println(names);
// Output: [Charlie, Bob, Alice]
```

### Real-World Example: Sorting Students

```java
class Student {
    String name;
    int marks;
    
    Student(String name, int marks) {
        this.name = name;
        this.marks = marks;
    }
    
    @Override
    public String toString() {
        return name + "(" + marks + ")";
    }
}

List<Student> students = new ArrayList<>();
students.add(new Student("Alice", 85));
students.add(new Student("Bob", 92));
students.add(new Student("Charlie", 78));

// Sort by marks (descending)
Collections.sort(students, (s1, s2) -> s2.marks - s1.marks);
System.out.println(students);
// Output: [Bob(92), Alice(85), Charlie(78)]

// Sort by name
Collections.sort(students, (s1, s2) -> s1.name.compareTo(s2.name));
System.out.println(students);
// Output: [Alice(85), Bob(92), Charlie(78)]
```

---

## 2. Searching Methods

### binarySearch(List<? extends Comparable<? super T>> list, T key)

Searches for an element using binary search. **List must be sorted first!**

```java
List<Integer> numbers = Arrays.asList(1, 3, 5, 7, 9, 11, 13);

int index = Collections.binarySearch(numbers, 7);
System.out.println("Index of 7: " + index);  // 3

int notFound = Collections.binarySearch(numbers, 6);
System.out.println("Index of 6: " + notFound);  // -4 (insertion point)
```

### binarySearch(List<? extends T> list, T key, Comparator<? super T> c)

Binary search with custom comparator.

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

// Search with case-insensitive comparison
int index = Collections.binarySearch(names, "bob", String.CASE_INSENSITIVE_ORDER);
System.out.println("Index: " + index);  // 1
```

**Important:** If element not found, returns `-(insertion point) - 1`

```java
List<Integer> numbers = Arrays.asList(1, 3, 5, 7, 9);
int result = Collections.binarySearch(numbers, 6);
System.out.println("Result: " + result);  // -4

// To get insertion point:
int insertionPoint = -(result + 1);
System.out.println("Insertion point: " + insertionPoint);  // 3
```

---

## 3. Reversing and Shuffling

### reverse(List<?> list)

Reverses the order of elements.

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
Collections.reverse(numbers);
System.out.println(numbers);
// Output: [5, 4, 3, 2, 1]
```

### shuffle(List<?> list)

Randomly shuffles elements.

```java
List<String> cards = new ArrayList<>(Arrays.asList("A", "K", "Q", "J", "10"));
Collections.shuffle(cards);
System.out.println(cards);
// Output: Random order, e.g., [Q, 10, A, J, K]
```

### shuffle(List<?> list, Random rnd)

Shuffle with custom random generator.

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
Random random = new Random(42);  // Fixed seed for reproducibility
Collections.shuffle(numbers, random);
System.out.println(numbers);
// Output: Same random order every time with seed 42
```

---

## 4. Min/Max Methods

### min(Collection<? extends T> coll)

Returns the minimum element.

```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);
int min = Collections.min(numbers);
System.out.println("Min: " + min);  // 1

List<String> names = Arrays.asList("Charlie", "Alice", "Bob");
String minName = Collections.min(names);
System.out.println("Min name: " + minName);  // Alice
```

### max(Collection<? extends T> coll)

Returns the maximum element.

```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);
int max = Collections.max(numbers);
System.out.println("Max: " + max);  // 9
```

### min/max with Comparator

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Min by length
String shortest = Collections.min(names, (a, b) -> a.length() - b.length());
System.out.println("Shortest: " + shortest);  // Bob

// Max by length
String longest = Collections.max(names, (a, b) -> a.length() - b.length());
System.out.println("Longest: " + longest);  // Charlie
```

---

## 5. Frequency and Disjoint

### frequency(Collection<?> c, Object o)

Counts occurrences of an element.

```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Apple", "Cherry", "Apple");
int count = Collections.frequency(fruits, "Apple");
System.out.println("Apple count: " + count);  // 3
```

### disjoint(Collection<?> c1, Collection<?> c2)

Checks if two collections have no common elements.

```java
List<Integer> list1 = Arrays.asList(1, 2, 3);
List<Integer> list2 = Arrays.asList(4, 5, 6);
List<Integer> list3 = Arrays.asList(3, 4, 5);

System.out.println(Collections.disjoint(list1, list2));  // true (no common)
System.out.println(Collections.disjoint(list1, list3));  // false (3 is common)
```

---

## 6. Replacing and Filling

### replaceAll(List<T> list, T oldVal, T newVal)

Replaces all occurrences of one value with another.

```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Apple", "Cherry"));
Collections.replaceAll(fruits, "Apple", "Orange");
System.out.println(fruits);
// Output: [Orange, Banana, Orange, Cherry]
```

### fill(List<? super T> list, T obj)

Replaces all elements with the specified value.

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));
Collections.fill(list, "X");
System.out.println(list);
// Output: [X, X, X, X]
```

---

## 7. Copying and Swapping

### copy(List<? super T> dest, List<? extends T> src)

Copies elements from source to destination. **Destination must be at least as large as source!**

```java
List<String> source = Arrays.asList("A", "B", "C");
List<String> dest = new ArrayList<>(Arrays.asList("X", "Y", "Z", "W"));

Collections.copy(dest, source);
System.out.println(dest);
// Output: [A, B, C, W]
```

### swap(List<?> list, int i, int j)

Swaps elements at two positions.

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));
Collections.swap(list, 0, 3);
System.out.println(list);
// Output: [D, B, C, A]
```

---

## 8. Rotating and Indexing

### rotate(List<?> list, int distance)

Rotates elements by the specified distance.

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));

// Rotate right by 2
Collections.rotate(numbers, 2);
System.out.println(numbers);
// Output: [4, 5, 1, 2, 3]

// Rotate left by 2 (negative distance)
Collections.rotate(numbers, -2);
System.out.println(numbers);
// Output: [1, 2, 3, 4, 5]
```

### indexOfSubList(List<?> source, List<?> target)

Finds the starting index of a sublist.

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
List<Integer> sublist = Arrays.asList(3, 4, 5);

int index = Collections.indexOfSubList(list, sublist);
System.out.println("Index: " + index);  // 2
```

### lastIndexOfSubList(List<?> source, List<?> target)

Finds the last occurrence of a sublist.

```java
List<Integer> list = Arrays.asList(1, 2, 3, 1, 2, 3, 4);
List<Integer> sublist = Arrays.asList(1, 2, 3);

int lastIndex = Collections.lastIndexOfSubList(list, sublist);
System.out.println("Last index: " + lastIndex);  // 3
```

---

## 9. Synchronized Collections

Makes collections thread-safe by wrapping them.

### synchronizedList(List<T> list)

```java
List<String> list = new ArrayList<>();
List<String> syncList = Collections.synchronizedList(list);

// Now safe for concurrent access
syncList.add("A");
syncList.add("B");
```

### synchronizedSet(Set<T> s)

```java
Set<String> set = new HashSet<>();
Set<String> syncSet = Collections.synchronizedSet(set);
```

### synchronizedMap(Map<K,V> m)

```java
Map<String, Integer> map = new HashMap<>();
Map<String, Integer> syncMap = Collections.synchronizedMap(map);
```

### Important: Manual Synchronization for Iteration

```java
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// Must synchronize manually when iterating
synchronized(syncList) {
    for (String item : syncList) {
        System.out.println(item);
    }
}
```

---

## 10. Unmodifiable Collections

Creates read-only views of collections.

### unmodifiableList(List<? extends T> list)

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
List<String> unmodList = Collections.unmodifiableList(list);

System.out.println(unmodList.get(0));  // OK - "A"
// unmodList.add("D");  // UnsupportedOperationException!
// unmodList.remove(0);  // UnsupportedOperationException!
```

### unmodifiableSet(Set<? extends T> s)

```java
Set<String> set = new HashSet<>(Arrays.asList("A", "B", "C"));
Set<String> unmodSet = Collections.unmodifiableSet(set);
```

### unmodifiableMap(Map<? extends K, ? extends V> m)

```java
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);

Map<String, Integer> unmodMap = Collections.unmodifiableMap(map);
// unmodMap.put("C", 3);  // UnsupportedOperationException!
```

### Use Case: Protecting Internal State

```java
class Library {
    private List<String> books = new ArrayList<>();
    
    public void addBook(String book) {
        books.add(book);
    }
    
    // Return unmodifiable view to prevent external modification
    public List<String> getBooks() {
        return Collections.unmodifiableList(books);
    }
}

Library library = new Library();
library.addBook("Book1");
library.addBook("Book2");

List<String> books = library.getBooks();
System.out.println(books);  // [Book1, Book2]
// books.add("Book3");  // UnsupportedOperationException!
```

---

## 11. Empty Collections

Returns immutable empty collections.

### emptyList()

```java
List<String> emptyList = Collections.emptyList();
System.out.println(emptyList.size());  // 0
// emptyList.add("A");  // UnsupportedOperationException!
```

### emptySet()

```java
Set<String> emptySet = Collections.emptySet();
```

### emptyMap()

```java
Map<String, Integer> emptyMap = Collections.emptyMap();
```

### Use Case: Avoiding Null Returns

```java
class UserService {
    public List<String> getUserNames(int userId) {
        if (userId < 0) {
            return Collections.emptyList();  // Better than returning null
        }
        // ... fetch and return user names
        return Arrays.asList("Alice", "Bob");
    }
}

// Client code doesn't need null check
List<String> names = service.getUserNames(-1);
for (String name : names) {  // No NullPointerException
    System.out.println(name);
}
```

---

## 12. Singleton Collections

Returns immutable collections with a single element.

### singletonList(T o)

```java
List<String> singleList = Collections.singletonList("Only");
System.out.println(singleList);  // [Only]
// singleList.add("Another");  // UnsupportedOperationException!
```

### singleton(T o)

```java
Set<String> singleSet = Collections.singleton("Only");
```

### singletonMap(K key, V value)

```java
Map<String, Integer> singleMap = Collections.singletonMap("Key", 1);
```

### Use Case: Method Parameters

```java
// When you need to pass a single-element collection
list.removeAll(Collections.singleton("ItemToRemove"));

// Instead of creating a new list
// List<String> temp = new ArrayList<>();
// temp.add("ItemToRemove");
// list.removeAll(temp);
```

---

## 13. Checked Collections

Provides runtime type checking.

### checkedList(List<E> list, Class<E> type)

```java
List<String> list = new ArrayList<>();
List<String> checkedList = Collections.checkedList(list, String.class);

checkedList.add("Valid");  // OK

// If someone tries to add wrong type via raw reference
List rawList = checkedList;
// rawList.add(123);  // ClassCastException at runtime!
```

### Use Case: Preventing Type Pollution

```java
class Container {
    private List<String> items = new ArrayList<>();
    
    public List<String> getItems() {
        // Protect against type pollution
        return Collections.checkedList(items, String.class);
    }
}
```

---

## 14. Other Useful Methods

### addAll(Collection<? super T> c, T... elements)

Adds multiple elements to a collection.

```java
List<String> list = new ArrayList<>();
Collections.addAll(list, "A", "B", "C", "D");
System.out.println(list);
// Output: [A, B, C, D]
```

### nCopies(int n, T o)

Returns an immutable list with n copies of an element.

```java
List<String> copies = Collections.nCopies(5, "X");
System.out.println(copies);
// Output: [X, X, X, X, X]
```

### reverseOrder()

Returns a comparator for reverse natural ordering.

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 5, 3, 9, 2));
Collections.sort(numbers, Collections.reverseOrder());
System.out.println(numbers);
// Output: [9, 5, 3, 2, 1]
```

### reverseOrder(Comparator<T> cmp)

Returns a comparator that reverses the given comparator.

```java
List<String> names = new ArrayList<>(Arrays.asList("Alice", "Bob", "Charlie"));

// Sort by length in reverse
Comparator<String> byLength = (a, b) -> a.length() - b.length();
Collections.sort(names, Collections.reverseOrder(byLength));
System.out.println(names);
// Output: [Charlie, Alice, Bob]
```

---

## Real-World Examples

### Example 1: Card Deck

```java
class CardDeck {
    private List<String> cards = new ArrayList<>();
    
    public CardDeck() {
        String[] suits = {"♠", "♥", "♦", "♣"};
        String[] ranks = {"A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"};
        
        for (String suit : suits) {
            for (String rank : ranks) {
                cards.add(rank + suit);
            }
        }
    }
    
    public void shuffle() {
        Collections.shuffle(cards);
    }
    
    public void sort() {
        Collections.sort(cards);
    }
    
    public List<String> getCards() {
        return Collections.unmodifiableList(cards);
    }
}

CardDeck deck = new CardDeck();
deck.shuffle();
System.out.println("Shuffled: " + deck.getCards().subList(0, 5));
```

### Example 2: Leaderboard

```java
class Player {
    String name;
    int score;
    
    Player(String name, int score) {
        this.name = name;
        this.score = score;
    }
    
    @Override
    public String toString() {
        return name + ": " + score;
    }
}

class Leaderboard {
    private List<Player> players = new ArrayList<>();
    
    public void addPlayer(Player player) {
        players.add(player);
    }
    
    public List<Player> getTopPlayers(int n) {
        // Sort by score (descending)
        Collections.sort(players, (p1, p2) -> p2.score - p1.score);
        
        // Return top n players
        int count = Math.min(n, players.size());
        return Collections.unmodifiableList(players.subList(0, count));
    }
    
    public Player getTopPlayer() {
        if (players.isEmpty()) {
            return null;
        }
        return Collections.max(players, (p1, p2) -> p1.score - p2.score);
    }
}

Leaderboard board = new Leaderboard();
board.addPlayer(new Player("Alice", 100));
board.addPlayer(new Player("Bob", 150));
board.addPlayer(new Player("Charlie", 120));

System.out.println("Top 2: " + board.getTopPlayers(2));
System.out.println("Winner: " + board.getTopPlayer());
```

### Example 3: Inventory Management

```java
class Inventory {
    private Map<String, Integer> items = new HashMap<>();
    
    public void addItem(String item, int quantity) {
        items.put(item, items.getOrDefault(item, 0) + quantity);
    }
    
    public void removeItem(String item, int quantity) {
        int current = items.getOrDefault(item, 0);
        if (current >= quantity) {
            items.put(item, current - quantity);
        }
    }
    
    public Map<String, Integer> getItems() {
        return Collections.unmodifiableMap(items);
    }
    
    public String getMostStockedItem() {
        if (items.isEmpty()) {
            return null;
        }
        return Collections.max(items.entrySet(), 
            (e1, e2) -> e1.getValue() - e2.getValue()).getKey();
    }
    
    public int getTotalItems() {
        return items.values().stream().mapToInt(Integer::intValue).sum();
    }
}

Inventory inv = new Inventory();
inv.addItem("Apple", 50);
inv.addItem("Banana", 30);
inv.addItem("Orange", 70);

System.out.println("Most stocked: " + inv.getMostStockedItem());
System.out.println("Total items: " + inv.getTotalItems());
```

---

## Common Interview Questions

### Q1: What's the difference between Collections and Collection?

**Answer:**
"`Collection` (singular) is an interface that represents a group of objects. It's the root interface of the collection hierarchy and is implemented by List, Set, and Queue.

`Collections` (plural) is a utility class that provides static methods to operate on collections, like sorting, searching, reversing, etc. It cannot be instantiated and all its methods are static.

Think of it like this:
- Collection = The container (interface)
- Collections = The toolbox for containers (utility class)"

### Q2: How does Collections.sort() work internally?

**Answer:**
"Collections.sort() uses a modified merge sort algorithm called TimSort (since Java 7). TimSort is a hybrid sorting algorithm derived from merge sort and insertion sort.

Key points:
1. Time Complexity: O(n log n) in worst case
2. Space Complexity: O(n)
3. Stable: Maintains relative order of equal elements
4. Adaptive: Performs better on partially sorted data

For primitive arrays, Arrays.sort() uses Dual-Pivot Quicksort, but Collections.sort() (for objects) uses TimSort because stability is important for objects."

### Q3: What's the difference between synchronized collections and concurrent collections?

**Answer:**
"Synchronized collections (from Collections.synchronizedXxx()) provide thread-safety by locking the entire collection for each operation. This can lead to poor performance in highly concurrent scenarios.

Concurrent collections (like ConcurrentHashMap) use more sophisticated locking mechanisms:
- Lock striping: Different parts of the collection can be locked independently
- Lock-free algorithms: Some operations don't require locks at all
- Better scalability: Multiple threads can access different parts simultaneously

Example:
```java
// Synchronized - Locks entire map for each operation
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// Concurrent - Better performance with multiple threads
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
```

Use synchronized collections for simple thread-safety needs. Use concurrent collections for high-concurrency scenarios."

### Q4: Why does Collections.binarySearch() require a sorted list?

**Answer:**
"Binary search works by repeatedly dividing the search space in half. It compares the target with the middle element and decides whether to search in the left or right half.

This only works if the list is sorted because:
1. If target < middle, we know target must be in the left half (only true if sorted)
2. If target > middle, we know target must be in the right half (only true if sorted)

If the list is not sorted, we can't make these assumptions, and binary search will give incorrect results.

Time complexity: O(log n) for sorted list vs O(n) for linear search on unsorted list."

### Q5: What's the purpose of unmodifiable collections?

**Answer:**
"Unmodifiable collections provide a read-only view of a collection. They're useful for:

1. **Protecting internal state**: Prevent external code from modifying internal data
2. **API design**: Return collections that clients can read but not modify
3. **Thread-safety**: Read-only collections are inherently thread-safe
4. **Defensive copying**: Avoid expensive copying when you just need read access

Example:
```java
class Library {
    private List<String> books = new ArrayList<>();
    
    public List<String> getBooks() {
        return Collections.unmodifiableList(books);  // Safe
        // return books;  // Unsafe - external code can modify
    }
}
```

Important: Unmodifiable is a view, not a copy. Changes to the original collection are reflected in the unmodifiable view."

### Q6: What happens if you try to modify an unmodifiable collection?

**Answer:**
"It throws `UnsupportedOperationException` at runtime. This is an unchecked exception, so it's not caught at compile time.

Example:
```java
List<String> list = Arrays.asList("A", "B", "C");
List<String> unmodList = Collections.unmodifiableList(list);

unmodList.add("D");  // UnsupportedOperationException
unmodList.remove(0);  // UnsupportedOperationException
unmodList.set(0, "X");  // UnsupportedOperationException
```

However, you can still read from it:
```java
System.out.println(unmodList.get(0));  // OK - "A"
System.out.println(unmodList.size());  // OK - 3
```"

### Q7: What's the difference between Collections.emptyList() and new ArrayList()?

**Answer:**
"Collections.emptyList() returns an immutable empty list, while new ArrayList() creates a mutable empty list.

Key differences:

1. **Mutability**:
   - emptyList(): Cannot add/remove elements
   - new ArrayList(): Can add/remove elements

2. **Memory**:
   - emptyList(): Returns a singleton instance (same object every time)
   - new ArrayList(): Creates a new object each time

3. **Performance**:
   - emptyList(): More efficient (no object creation)
   - new ArrayList(): Less efficient (creates new object)

Example:
```java
List<String> empty1 = Collections.emptyList();
List<String> empty2 = Collections.emptyList();
System.out.println(empty1 == empty2);  // true (same instance)

List<String> list1 = new ArrayList<>();
List<String> list2 = new ArrayList<>();
System.out.println(list1 == list2);  // false (different instances)
```

Use emptyList() when you need to return an empty collection that won't be modified."

### Q8: How does Collections.shuffle() work?

**Answer:**
"Collections.shuffle() uses the Fisher-Yates shuffle algorithm (also known as Knuth shuffle):

1. Start from the last element
2. Pick a random element from the remaining unshuffled portion
3. Swap it with the current element
4. Move to the previous element and repeat

Time Complexity: O(n)
Space Complexity: O(1)

Example:
```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
Collections.shuffle(numbers);
// Each permutation has equal probability
```

You can also provide a custom Random instance for reproducibility:
```java
Random random = new Random(42);  // Fixed seed
Collections.shuffle(numbers, random);
// Same shuffle every time with seed 42
```"

### Q9: What's the difference between Collections.copy() and List.addAll()?

**Answer:**
"Collections.copy() replaces elements in the destination list, while addAll() appends elements.

Key differences:

1. **Destination size**:
   - copy(): Destination must be at least as large as source
   - addAll(): Destination can be any size

2. **Behavior**:
   - copy(): Replaces existing elements
   - addAll(): Adds new elements

3. **Use case**:
   - copy(): When you want to overwrite existing data
   - addAll(): When you want to append data

Example:
```java
List<String> source = Arrays.asList("A", "B", "C");

// Collections.copy()
List<String> dest1 = new ArrayList<>(Arrays.asList("X", "Y", "Z", "W"));
Collections.copy(dest1, source);
System.out.println(dest1);  // [A, B, C, W]

// List.addAll()
List<String> dest2 = new ArrayList<>(Arrays.asList("X", "Y", "Z"));
dest2.addAll(source);
System.out.println(dest2);  // [X, Y, Z, A, B, C]
```"

### Q10: When should you use Collections.synchronizedList() vs CopyOnWriteArrayList?

**Answer:**
"Use Collections.synchronizedList() when:
- You have more writes than reads
- You need strong consistency
- You can afford locking overhead

Use CopyOnWriteArrayList when:
- You have more reads than writes
- You can tolerate eventual consistency
- Writes are infrequent

Example:
```java
// Synchronized - Good for balanced read/write
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// Copy-on-write - Good for read-heavy workloads
List<String> cowList = new CopyOnWriteArrayList<>();
```

CopyOnWriteArrayList creates a new copy of the array on every write, so it's expensive for frequent writes but very efficient for reads (no locking needed)."

---

## Best Practices

### 1. Use Appropriate Method for Task

```java
// Good - Use specific method
Collections.sort(list);

// Bad - Manual sorting
for (int i = 0; i < list.size(); i++) {
    for (int j = i + 1; j < list.size(); j++) {
        if (list.get(i).compareTo(list.get(j)) > 0) {
            Collections.swap(list, i, j);
        }
    }
}
```

### 2. Return Unmodifiable Collections from APIs

```java
// Good - Protect internal state
public List<String> getItems() {
    return Collections.unmodifiableList(items);
}

// Bad - Exposes internal state
public List<String> getItems() {
    return items;
}
```

### 3. Use Empty Collections Instead of Null

```java
// Good
public List<String> getResults() {
    if (noResults) {
        return Collections.emptyList();
    }
    return results;
}

// Bad
public List<String> getResults() {
    if (noResults) {
        return null;  // Client needs null check
    }
    return results;
}
```

### 4. Use Singleton Collections for Single Elements

```java
// Good - Memory efficient
list.removeAll(Collections.singleton("item"));

// Bad - Creates unnecessary list
List<String> temp = new ArrayList<>();
temp.add("item");
list.removeAll(temp);
```

### 5. Sort Before Binary Search

```java
// Good
Collections.sort(list);
int index = Collections.binarySearch(list, target);

// Bad - Incorrect results
int index = Collections.binarySearch(list, target);  // List not sorted!
```

---

## Summary

**Collections Utility Class:**
- ✅ Provides 50+ static utility methods
- ✅ Cannot be instantiated (private constructor)
- ✅ Works with all collection types
- ✅ Includes sorting, searching, reversing, shuffling
- ✅ Provides thread-safe wrappers
- ✅ Provides immutable wrappers
- ✅ Provides empty and singleton collections

**Key Categories:**
1. **Sorting**: sort(), reverseOrder()
2. **Searching**: binarySearch()
3. **Manipulation**: reverse(), shuffle(), rotate(), swap()
4. **Querying**: min(), max(), frequency(), disjoint()
5. **Modification**: replaceAll(), fill(), copy()
6. **Thread-Safety**: synchronizedXxx()
7. **Immutability**: unmodifiableXxx()
8. **Factory**: emptyXxx(), singletonXxx()

**When to Use:**
- Use Collections methods instead of manual implementation
- Use unmodifiable collections to protect internal state
- Use synchronized collections for simple thread-safety
- Use empty collections instead of null returns
- Use singleton collections for single-element operations

**Interview Tips:**
- Know the difference between Collection and Collections
- Understand sorting algorithms (TimSort)
- Know when to use synchronized vs concurrent collections
- Understand unmodifiable vs immutable
- Know binary search requirements (sorted list)

Master these concepts and you'll handle any Collections utility class question with confidence!