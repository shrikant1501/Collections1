# List - Most Commonly Used Operations with Examples

## Table of Contents
1. [Creating Lists](#creating-lists)
2. [Adding Elements](#adding-elements)
3. [Accessing Elements](#accessing-elements)
4. [Modifying Elements](#modifying-elements)
5. [Removing Elements](#removing-elements)
6. [Searching Elements](#searching-elements)
7. [Iterating Lists](#iterating-lists)
8. [Sorting Lists](#sorting-lists)
9. [Converting Lists](#converting-lists)
10. [Bulk Operations](#bulk-operations)
11. [SubList Operations](#sublist-operations)
12. [Java 8+ Stream Operations](#java-8-stream-operations)

---

## 1. Creating Lists

### Empty List
```java
// Using ArrayList (most common)
List<String> list1 = new ArrayList<>();

// Using LinkedList
List<String> list2 = new LinkedList<>();

// With initial capacity (performance optimization)
List<String> list3 = new ArrayList<>(100);

// Immutable empty list (Java 9+)
List<String> list4 = List.of();
```

### List with Initial Values
```java
// Using Arrays.asList (fixed-size list)
List<String> list1 = Arrays.asList("Apple", "Banana", "Cherry");

// Using List.of (immutable, Java 9+)
List<String> list2 = List.of("Apple", "Banana", "Cherry");

// Using ArrayList constructor
List<String> list3 = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry"));

// Using double brace initialization (not recommended)
List<String> list4 = new ArrayList<>() {{
    add("Apple");
    add("Banana");
    add("Cherry");
}};

// Java 9+ - List.of with multiple elements
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
```

### Creating from Another Collection
```java
Set<String> set = new HashSet<>(Arrays.asList("A", "B", "C"));
List<String> list = new ArrayList<>(set);

// Copy constructor
List<String> copy = new ArrayList<>(originalList);

// Java 10+ - List.copyOf (immutable)
List<String> immutableCopy = List.copyOf(originalList);
```

---

## 2. Adding Elements

### Add at End
```java
List<String> fruits = new ArrayList<>();

// Add single element
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Cherry");
// Result: [Apple, Banana, Cherry]

// Add null (allowed in ArrayList)
fruits.add(null);
// Result: [Apple, Banana, Cherry, null]
```

### Add at Specific Position
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Cherry"));

// Insert at index 1
fruits.add(1, "Banana");
// Result: [Apple, Banana, Cherry]

// Insert at beginning
fruits.add(0, "Mango");
// Result: [Mango, Apple, Banana, Cherry]
```

### Add Multiple Elements
```java
List<String> fruits = new ArrayList<>();
fruits.add("Apple");

// Add all from another collection
List<String> moreFruits = Arrays.asList("Banana", "Cherry", "Date");
fruits.addAll(moreFruits);
// Result: [Apple, Banana, Cherry, Date]

// Add all at specific position
fruits.addAll(1, Arrays.asList("Mango", "Orange"));
// Result: [Apple, Mango, Orange, Banana, Cherry, Date]
```

---

## 3. Accessing Elements

### Get by Index
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Get first element
String first = fruits.get(0);  // "Apple"

// Get last element
String last = fruits.get(fruits.size() - 1);  // "Cherry"

// Get element at index
String second = fruits.get(1);  // "Banana"

// Safe access with bounds check
int index = 5;
if (index >= 0 && index < fruits.size()) {
    String fruit = fruits.get(index);
} else {
    System.out.println("Index out of bounds");
}
```

### Get First and Last (Java 21+)
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Java 21+ methods
String first = fruits.getFirst();  // "Apple"
String last = fruits.getLast();    // "Cherry"
```

### Check if Empty
```java
List<String> fruits = new ArrayList<>();

if (fruits.isEmpty()) {
    System.out.println("List is empty");
}

// Get size
int size = fruits.size();  // 0
```

---

## 4. Modifying Elements

### Replace Element at Index
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry"));

// Replace element at index 1
String oldValue = fruits.set(1, "Blueberry");
// oldValue: "Banana"
// Result: [Apple, Blueberry, Cherry]
```

### Replace All Elements (Java 8+)
```java
List<String> fruits = new ArrayList<>(Arrays.asList("apple", "banana", "cherry"));

// Convert all to uppercase
fruits.replaceAll(String::toUpperCase);
// Result: [APPLE, BANANA, CHERRY]

// Add prefix to all
fruits.replaceAll(s -> "Fruit: " + s);
// Result: [Fruit: APPLE, Fruit: BANANA, Fruit: CHERRY]
```

---

## 5. Removing Elements

### Remove by Index
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Date"));

// Remove element at index 1
String removed = fruits.remove(1);
// removed: "Banana"
// Result: [Apple, Cherry, Date]

// Remove first element
fruits.remove(0);
// Result: [Cherry, Date]

// Remove last element
fruits.remove(fruits.size() - 1);
// Result: [Cherry]
```

### Remove by Value
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Banana"));

// Remove first occurrence
boolean removed = fruits.remove("Banana");
// removed: true
// Result: [Apple, Cherry, Banana]

// Remove object that doesn't exist
boolean notRemoved = fruits.remove("Mango");
// notRemoved: false
```

### Remove All Occurrences
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Banana", "Date"));

// Remove all occurrences of "Banana"
while (fruits.remove("Banana")) {
    // Keep removing until none left
}
// Result: [Apple, Cherry, Date]

// Better way - removeAll
fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Banana", "Date"));
fruits.removeAll(Collections.singleton("Banana"));
// Result: [Apple, Cherry, Date]
```

### Remove with Condition (Java 8+)
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Date", "Elderberry"));

// Remove fruits with length > 5
fruits.removeIf(fruit -> fruit.length() > 5);
// Result: [Apple, Date]

// Remove null values
fruits.removeIf(Objects::isNull);

// Remove based on multiple conditions
fruits.removeIf(fruit -> fruit.startsWith("A") || fruit.length() < 4);
```

### Remove During Iteration (Safe Way)
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Date"));

// Using Iterator (safe)
Iterator<String> iterator = fruits.iterator();
while (iterator.hasNext()) {
    String fruit = iterator.next();
    if (fruit.startsWith("B")) {
        iterator.remove();  // Safe removal
    }
}
// Result: [Apple, Cherry, Date]

// Using removeIf (better)
fruits.removeIf(fruit -> fruit.startsWith("B"));
```

### Clear All Elements
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry"));

fruits.clear();
// Result: []
System.out.println(fruits.isEmpty());  // true
```

---

## 6. Searching Elements

### Check if Contains
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Check if contains element
boolean hasApple = fruits.contains("Apple");  // true
boolean hasMango = fruits.contains("Mango");  // false

// Check if contains null
fruits = new ArrayList<>(Arrays.asList("Apple", null, "Cherry"));
boolean hasNull = fruits.contains(null);  // true
```

### Find Index
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Banana");

// Find first occurrence
int firstIndex = fruits.indexOf("Banana");  // 1

// Find last occurrence
int lastIndex = fruits.lastIndexOf("Banana");  // 3

// Element not found
int notFound = fruits.indexOf("Mango");  // -1
```

### Check if Contains All
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Date");
List<String> subset = Arrays.asList("Apple", "Cherry");

boolean containsAll = fruits.containsAll(subset);  // true

List<String> notSubset = Arrays.asList("Apple", "Mango");
boolean notContainsAll = fruits.containsAll(notSubset);  // false
```

### Find Element with Condition (Java 8+)
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Date");

// Find first element starting with 'C'
Optional<String> found = fruits.stream()
    .filter(fruit -> fruit.startsWith("C"))
    .findFirst();

if (found.isPresent()) {
    System.out.println("Found: " + found.get());  // "Cherry"
}

// Find any element with length > 5
Optional<String> anyLong = fruits.stream()
    .filter(fruit -> fruit.length() > 5)
    .findAny();
```

---

## 7. Iterating Lists

### For-Each Loop
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Standard for-each
for (String fruit : fruits) {
    System.out.println(fruit);
}
```

### Traditional For Loop
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Forward iteration
for (int i = 0; i < fruits.size(); i++) {
    System.out.println(i + ": " + fruits.get(i));
}

// Backward iteration
for (int i = fruits.size() - 1; i >= 0; i--) {
    System.out.println(fruits.get(i));
}
```

### Iterator
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Using Iterator
Iterator<String> iterator = fruits.iterator();
while (iterator.hasNext()) {
    String fruit = iterator.next();
    System.out.println(fruit);
}
```

### ListIterator (Bidirectional)
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry"));

// Forward iteration
ListIterator<String> iterator = fruits.listIterator();
while (iterator.hasNext()) {
    System.out.println(iterator.nextIndex() + ": " + iterator.next());
}

// Backward iteration
while (iterator.hasPrevious()) {
    System.out.println(iterator.previousIndex() + ": " + iterator.previous());
}

// Modify during iteration
iterator = fruits.listIterator();
while (iterator.hasNext()) {
    String fruit = iterator.next();
    if (fruit.equals("Banana")) {
        iterator.set("Blueberry");  // Replace
    }
}
```

### forEach Method (Java 8+)
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Using method reference
fruits.forEach(System.out::println);

// Using lambda
fruits.forEach(fruit -> System.out.println("Fruit: " + fruit));

// With index (using custom approach)
IntStream.range(0, fruits.size())
    .forEach(i -> System.out.println(i + ": " + fruits.get(i)));
```

---

## 8. Sorting Lists

### Natural Order Sorting
```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(5, 2, 8, 1, 9));

// Sort in ascending order
Collections.sort(numbers);
// Result: [1, 2, 5, 8, 9]

// Or using List.sort (Java 8+)
numbers.sort(null);  // null means natural order
// Result: [1, 2, 5, 8, 9]

// String sorting
List<String> fruits = new ArrayList<>(Arrays.asList("Cherry", "Apple", "Banana"));
Collections.sort(fruits);
// Result: [Apple, Banana, Cherry]
```

### Reverse Order Sorting
```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(5, 2, 8, 1, 9));

// Sort in descending order
Collections.sort(numbers, Collections.reverseOrder());
// Result: [9, 8, 5, 2, 1]

// Or using List.sort
numbers.sort(Comparator.reverseOrder());
// Result: [9, 8, 5, 2, 1]

// Reverse existing list
Collections.reverse(numbers);
```

### Custom Sorting with Comparator
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Date"));

// Sort by length
fruits.sort(Comparator.comparingInt(String::length));
// Result: [Date, Apple, Banana, Cherry]

// Sort by length (descending)
fruits.sort(Comparator.comparingInt(String::length).reversed());
// Result: [Banana, Cherry, Apple, Date]

// Sort by multiple criteria
fruits.sort(Comparator.comparingInt(String::length)
    .thenComparing(String::compareTo));
```

### Sorting Custom Objects
```java
class Person {
    String name;
    int age;
    
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

List<Person> people = new ArrayList<>();
people.add(new Person("Alice", 30));
people.add(new Person("Bob", 25));
people.add(new Person("Charlie", 35));

// Sort by age
people.sort(Comparator.comparingInt(p -> p.age));

// Sort by name
people.sort(Comparator.comparing(p -> p.name));

// Sort by age, then by name
people.sort(Comparator.comparingInt((Person p) -> p.age)
    .thenComparing(p -> p.name));

// Sort by age (descending)
people.sort(Comparator.comparingInt((Person p) -> p.age).reversed());
```

---

## 9. Converting Lists

### List to Array
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// To Object array
Object[] array1 = fruits.toArray();

// To typed array (preferred)
String[] array2 = fruits.toArray(new String[0]);

// To typed array with size
String[] array3 = fruits.toArray(new String[fruits.size()]);

// Java 11+ - toArray with generator
String[] array4 = fruits.toArray(String[]::new);
```

### Array to List
```java
String[] array = {"Apple", "Banana", "Cherry"};

// Fixed-size list (backed by array)
List<String> list1 = Arrays.asList(array);

// Mutable list
List<String> list2 = new ArrayList<>(Arrays.asList(array));

// Java 9+ - immutable list
List<String> list3 = List.of(array);

// Java 16+ - Stream to List
List<String> list4 = Arrays.stream(array).toList();
```

### List to Set
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Apple");

// Convert to HashSet (removes duplicates)
Set<String> set = new HashSet<>(fruits);
// Result: [Apple, Banana, Cherry]

// Using Stream (Java 8+)
Set<String> set2 = fruits.stream().collect(Collectors.toSet());
```

### List to String
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Join with delimiter
String joined = String.join(", ", fruits);
// Result: "Apple, Banana, Cherry"

// Using Stream (Java 8+)
String joined2 = fruits.stream()
    .collect(Collectors.joining(", "));

// With prefix and suffix
String joined3 = fruits.stream()
    .collect(Collectors.joining(", ", "[", "]"));
// Result: "[Apple, Banana, Cherry]"

// Simple toString
String str = fruits.toString();
// Result: "[Apple, Banana, Cherry]"
```

---

## 10. Bulk Operations

### Add All
```java
List<String> list1 = new ArrayList<>(Arrays.asList("Apple", "Banana"));
List<String> list2 = Arrays.asList("Cherry", "Date");

// Add all elements from list2 to list1
list1.addAll(list2);
// list1: [Apple, Banana, Cherry, Date]

// Add all at specific position
list1.addAll(1, Arrays.asList("Mango", "Orange"));
// list1: [Apple, Mango, Orange, Banana, Cherry, Date]
```

### Remove All
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Date", "Elderberry"));
List<String> toRemove = Arrays.asList("Banana", "Date");

// Remove all elements in toRemove
fruits.removeAll(toRemove);
// Result: [Apple, Cherry, Elderberry]
```

### Retain All (Keep Only)
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Date"));
List<String> toKeep = Arrays.asList("Apple", "Cherry", "Mango");

// Keep only elements that are in toKeep
fruits.retainAll(toKeep);
// Result: [Apple, Cherry]
```

### Replace All (Java 8+)
```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));

// Multiply each element by 2
numbers.replaceAll(n -> n * 2);
// Result: [2, 4, 6, 8, 10]

// Square each element
numbers.replaceAll(n -> n * n);
// Result: [4, 16, 36, 64, 100]
```

---

## 11. SubList Operations

### Get SubList
```java
List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry", "Date", "Elderberry"));

// Get sublist [fromIndex, toIndex)
List<String> subList = fruits.subList(1, 4);
// subList: [Banana, Cherry, Date]

// SubList is a view, not a copy
subList.set(0, "Blueberry");
// fruits: [Apple, Blueberry, Cherry, Date, Elderberry]
```

### Modify Through SubList
```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10));

// Remove elements 3-6 (indices 2-5)
numbers.subList(2, 6).clear();
// numbers: [1, 2, 7, 8, 9, 10]

// Replace range
numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
List<Integer> replacement = Arrays.asList(10, 20, 30);
numbers.subList(1, 4).clear();
numbers.addAll(1, replacement);
// numbers: [1, 10, 20, 30, 5]
```

---

## 12. Java 8+ Stream Operations

### Filter
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Date", "Elderberry");

// Filter fruits starting with 'C'
List<String> filtered = fruits.stream()
    .filter(fruit -> fruit.startsWith("C"))
    .collect(Collectors.toList());
// Result: [Cherry]

// Filter by length
List<String> longFruits = fruits.stream()
    .filter(fruit -> fruit.length() > 5)
    .collect(Collectors.toList());
// Result: [Banana, Cherry, Elderberry]
```

### Map (Transform)
```java
List<String> fruits = Arrays.asList("apple", "banana", "cherry");

// Convert to uppercase
List<String> uppercase = fruits.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
// Result: [APPLE, BANANA, CHERRY]

// Get lengths
List<Integer> lengths = fruits.stream()
    .map(String::length)
    .collect(Collectors.toList());
// Result: [5, 6, 6]
```

### Reduce
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Sum all numbers
int sum = numbers.stream()
    .reduce(0, Integer::sum);
// Result: 15

// Find maximum
Optional<Integer> max = numbers.stream()
    .reduce(Integer::max);
// Result: Optional[5]

// Concatenate strings
List<String> words = Arrays.asList("Hello", "World", "Java");
String concatenated = words.stream()
    .reduce("", (a, b) -> a + " " + b).trim();
// Result: "Hello World Java"
```

### Collect
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Date");

// To List
List<String> list = fruits.stream()
    .filter(f -> f.length() > 5)
    .collect(Collectors.toList());

// To Set
Set<String> set = fruits.stream()
    .collect(Collectors.toSet());

// To Map
Map<String, Integer> map = fruits.stream()
    .collect(Collectors.toMap(
        fruit -> fruit,
        String::length
    ));
// Result: {Apple=5, Banana=6, Cherry=6, Date=4}

// Joining
String joined = fruits.stream()
    .collect(Collectors.joining(", "));
// Result: "Apple, Banana, Cherry, Date"
```

### Sorting with Streams
```java
List<String> fruits = Arrays.asList("Cherry", "Apple", "Date", "Banana");

// Sort and collect
List<String> sorted = fruits.stream()
    .sorted()
    .collect(Collectors.toList());
// Result: [Apple, Banana, Cherry, Date]

// Sort by length
List<String> sortedByLength = fruits.stream()
    .sorted(Comparator.comparingInt(String::length))
    .collect(Collectors.toList());
// Result: [Date, Apple, Cherry, Banana]
```

### Distinct
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Apple", "Cherry", "Banana");

// Remove duplicates
List<String> distinct = fruits.stream()
    .distinct()
    .collect(Collectors.toList());
// Result: [Apple, Banana, Cherry]
```

### Limit and Skip
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Get first 5 elements
List<Integer> first5 = numbers.stream()
    .limit(5)
    .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5]

// Skip first 5 elements
List<Integer> after5 = numbers.stream()
    .skip(5)
    .collect(Collectors.toList());
// Result: [6, 7, 8, 9, 10]

// Pagination: skip 5, take 3
List<Integer> page = numbers.stream()
    .skip(5)
    .limit(3)
    .collect(Collectors.toList());
// Result: [6, 7, 8]
```

### Matching
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");

// Check if any element starts with 'A'
boolean anyStartsWithA = fruits.stream()
    .anyMatch(fruit -> fruit.startsWith("A"));
// Result: true

// Check if all elements have length > 3
boolean allLongerThan3 = fruits.stream()
    .allMatch(fruit -> fruit.length() > 3);
// Result: true

// Check if none starts with 'Z'
boolean noneStartsWithZ = fruits.stream()
    .noneMatch(fruit -> fruit.startsWith("Z"));
// Result: true
```

### Count
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Date", "Elderberry");

// Count fruits with length > 5
long count = fruits.stream()
    .filter(fruit -> fruit.length() > 5)
    .count();
// Result: 3
```

### Grouping
```java
List<String> fruits = Arrays.asList("Apple", "Apricot", "Banana", "Blueberry", "Cherry");

// Group by first letter
Map<Character, List<String>> grouped = fruits.stream()
    .collect(Collectors.groupingBy(fruit -> fruit.charAt(0)));
// Result: {A=[Apple, Apricot], B=[Banana, Blueberry], C=[Cherry]}

// Group by length
Map<Integer, List<String>> byLength = fruits.stream()
    .collect(Collectors.groupingBy(String::length));
// Result: {5=[Apple], 7=[Apricot], 6=[Banana, Cherry], 9=[Blueberry]}
```

### Partitioning
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Partition into even and odd
Map<Boolean, List<Integer>> partitioned = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
// Result: {false=[1, 3, 5, 7, 9], true=[2, 4, 6, 8, 10]}
```

---

## Summary of Most Common Operations

### Top 10 Most Used Operations:
1. **add()** - Adding elements
2. **get()** - Accessing elements by index
3. **size()** - Getting list size
4. **remove()** - Removing elements
5. **contains()** - Checking existence
6. **isEmpty()** - Checking if empty
7. **clear()** - Removing all elements
8. **indexOf()** - Finding element position
9. **forEach()** - Iterating elements
10. **stream()** - Stream operations

### Performance Tips:
- Use **ArrayList** for most scenarios (fast access)
- Specify **initial capacity** if size is known
- Use **removeIf()** instead of manual iteration for removal
- Use **streams** for complex transformations
- Use **bulk operations** (addAll, removeAll) when possible

---

**Next Steps**: We can explore:
- LinkedList implementation and operations
- Set Interface operations
- Map Interface operations
- Advanced Stream API operations