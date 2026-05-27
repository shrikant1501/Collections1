# Stream API with Collections - Complete Guide

## What is Stream API?

**Simple Definition:**
Stream API is a way to process collections of data in a functional programming style. Think of it as a pipeline where data flows through various operations.

**Real-World Analogy:**
Imagine a factory assembly line:
- Raw materials enter (source)
- Pass through various stations (operations)
- Final product comes out (result)

Stream API works the same way with data!

---

## Why Use Streams?

### Before Streams (Traditional Way)

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Find even numbers greater than 5
List<Integer> result = new ArrayList<>();
for (Integer num : numbers) {
    if (num % 2 == 0 && num > 5) {
        result.add(num);
    }
}
System.out.println(result);  // [6, 8, 10]
```

### With Streams (Modern Way)

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Find even numbers greater than 5
List<Integer> result = numbers.stream()
    .filter(num -> num % 2 == 0)
    .filter(num -> num > 5)
    .collect(Collectors.toList());
System.out.println(result);  // [6, 8, 10]
```

**Benefits:**
- ✅ More readable
- ✅ Less code
- ✅ Declarative (what, not how)
- ✅ Can be parallelized easily
- ✅ Functional programming style

---

## Stream Basics

### Creating Streams

```java
// From Collection
List<String> list = Arrays.asList("A", "B", "C");
Stream<String> stream1 = list.stream();

// From Array
String[] array = {"A", "B", "C"};
Stream<String> stream2 = Arrays.stream(array);

// Using Stream.of()
Stream<String> stream3 = Stream.of("A", "B", "C");

// Empty stream
Stream<String> stream4 = Stream.empty();

// Infinite stream
Stream<Integer> stream5 = Stream.iterate(0, n -> n + 1);  // 0, 1, 2, 3...

// Generate stream
Stream<Double> stream6 = Stream.generate(Math::random);
```

### Stream Operations

**Two Types:**

1. **Intermediate Operations** (return Stream)
   - filter(), map(), sorted(), distinct(), limit(), skip()
   - Lazy - don't execute until terminal operation

2. **Terminal Operations** (return result)
   - collect(), forEach(), count(), reduce(), anyMatch(), findFirst()
   - Trigger execution of the pipeline

---

## Essential Stream Operations

### 1. filter() - Select Elements

**Purpose:** Keep only elements that match a condition

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Get even numbers
List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());
// Result: [2, 4, 6, 8, 10]

// Get numbers greater than 5
List<Integer> greaterThan5 = numbers.stream()
    .filter(n -> n > 5)
    .collect(Collectors.toList());
// Result: [6, 7, 8, 9, 10]

// Multiple filters (chaining)
List<Integer> result = numbers.stream()
    .filter(n -> n % 2 == 0)
    .filter(n -> n > 5)
    .collect(Collectors.toList());
// Result: [6, 8, 10]
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Get employees with salary > 50000
List<Employee> highEarners = employees.stream()
    .filter(emp -> emp.getSalary() > 50000)
    .collect(Collectors.toList());

// Get employees in IT department
List<Employee> itEmployees = employees.stream()
    .filter(emp -> emp.getDepartment().equals("IT"))
    .collect(Collectors.toList());
```

---

### 2. map() - Transform Elements

**Purpose:** Convert each element to something else

```java
List<String> names = Arrays.asList("alice", "bob", "charlie");

// Convert to uppercase
List<String> upperNames = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
// Result: [ALICE, BOB, CHARLIE]

// Get length of each string
List<Integer> lengths = names.stream()
    .map(String::length)
    .collect(Collectors.toList());
// Result: [5, 3, 7]

// Add prefix
List<String> withPrefix = names.stream()
    .map(name -> "Mr. " + name)
    .collect(Collectors.toList());
// Result: [Mr. alice, Mr. bob, Mr. charlie]
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Get all employee names
List<String> names = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.toList());

// Get all salaries
List<Double> salaries = employees.stream()
    .map(Employee::getSalary)
    .collect(Collectors.toList());

// Create DTOs
List<EmployeeDTO> dtos = employees.stream()
    .map(emp -> new EmployeeDTO(emp.getName(), emp.getSalary()))
    .collect(Collectors.toList());
```

---

### 3. flatMap() - Flatten Nested Structures

**Purpose:** Convert each element to a stream and flatten all streams into one

```java
List<List<Integer>> nestedList = Arrays.asList(
    Arrays.asList(1, 2, 3),
    Arrays.asList(4, 5, 6),
    Arrays.asList(7, 8, 9)
);

// Flatten to single list
List<Integer> flatList = nestedList.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**Real Example:**
```java
class Department {
    String name;
    List<Employee> employees;
}

List<Department> departments = getAllDepartments();

// Get all employees from all departments
List<Employee> allEmployees = departments.stream()
    .flatMap(dept -> dept.getEmployees().stream())
    .collect(Collectors.toList());

// Get all employee names from all departments
List<String> allNames = departments.stream()
    .flatMap(dept -> dept.getEmployees().stream())
    .map(Employee::getName)
    .collect(Collectors.toList());
```

---

### 4. sorted() - Sort Elements

```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9, 3);

// Natural order (ascending)
List<Integer> sorted = numbers.stream()
    .sorted()
    .collect(Collectors.toList());
// Result: [1, 2, 3, 5, 8, 9]

// Reverse order (descending)
List<Integer> sortedDesc = numbers.stream()
    .sorted(Comparator.reverseOrder())
    .collect(Collectors.toList());
// Result: [9, 8, 5, 3, 2, 1]
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Sort by name
List<Employee> byName = employees.stream()
    .sorted(Comparator.comparing(Employee::getName))
    .collect(Collectors.toList());

// Sort by salary (descending)
List<Employee> bySalary = employees.stream()
    .sorted(Comparator.comparing(Employee::getSalary).reversed())
    .collect(Collectors.toList());

// Sort by multiple fields
List<Employee> sorted = employees.stream()
    .sorted(Comparator.comparing(Employee::getDepartment)
                      .thenComparing(Employee::getSalary).reversed())
    .collect(Collectors.toList());
```

---

### 5. distinct() - Remove Duplicates

```java
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3, 4, 5, 5);

List<Integer> unique = numbers.stream()
    .distinct()
    .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5]
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Get unique departments
List<String> departments = employees.stream()
    .map(Employee::getDepartment)
    .distinct()
    .collect(Collectors.toList());

// Get unique cities
List<String> cities = employees.stream()
    .map(Employee::getCity)
    .distinct()
    .sorted()
    .collect(Collectors.toList());
```

---

### 6. limit() and skip() - Pagination

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Get first 5 elements
List<Integer> first5 = numbers.stream()
    .limit(5)
    .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5]

// Skip first 5, get rest
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

**Real Example:**
```java
List<Product> products = getAllProducts();

// Get first 10 products
List<Product> firstPage = products.stream()
    .limit(10)
    .collect(Collectors.toList());

// Get page 3 (items 21-30)
int pageNumber = 3;
int pageSize = 10;
List<Product> page3 = products.stream()
    .skip((pageNumber - 1) * pageSize)
    .limit(pageSize)
    .collect(Collectors.toList());
```

---

### 7. reduce() - Combine Elements

**Purpose:** Reduce stream to a single value

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Sum
int sum = numbers.stream()
    .reduce(0, (a, b) -> a + b);
// Result: 15

// Or using Integer::sum
int sum2 = numbers.stream()
    .reduce(0, Integer::sum);

// Product
int product = numbers.stream()
    .reduce(1, (a, b) -> a * b);
// Result: 120

// Max
Optional<Integer> max = numbers.stream()
    .reduce(Integer::max);
// Result: Optional[5]

// Min
Optional<Integer> min = numbers.stream()
    .reduce(Integer::min);
// Result: Optional[1]
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Total salary
double totalSalary = employees.stream()
    .map(Employee::getSalary)
    .reduce(0.0, Double::sum);

// Concatenate all names
String allNames = employees.stream()
    .map(Employee::getName)
    .reduce("", (a, b) -> a + ", " + b);
```

---

### 8. collect() - Collect Results

**Purpose:** Convert stream back to collection or other data structure

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// To List
List<String> list = names.stream()
    .collect(Collectors.toList());

// To Set
Set<String> set = names.stream()
    .collect(Collectors.toSet());

// To specific collection
ArrayList<String> arrayList = names.stream()
    .collect(Collectors.toCollection(ArrayList::new));

// To Map
Map<String, Integer> map = names.stream()
    .collect(Collectors.toMap(
        name -> name,           // key
        String::length          // value
    ));
// Result: {Alice=5, Bob=3, Charlie=7}

// Joining strings
String joined = names.stream()
    .collect(Collectors.joining(", "));
// Result: "Alice, Bob, Charlie"

// With prefix and suffix
String formatted = names.stream()
    .collect(Collectors.joining(", ", "[", "]"));
// Result: "[Alice, Bob, Charlie]"
```

---

### 9. Matching Operations

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Check if any element matches
boolean hasEven = numbers.stream()
    .anyMatch(n -> n % 2 == 0);
// Result: true

// Check if all elements match
boolean allPositive = numbers.stream()
    .allMatch(n -> n > 0);
// Result: true

// Check if no element matches
boolean noneNegative = numbers.stream()
    .noneMatch(n -> n < 0);
// Result: true
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Check if any employee earns > 100000
boolean hasHighEarner = employees.stream()
    .anyMatch(emp -> emp.getSalary() > 100000);

// Check if all employees are adults
boolean allAdults = employees.stream()
    .allMatch(emp -> emp.getAge() >= 18);

// Check if no employee is from "HR"
boolean noHR = employees.stream()
    .noneMatch(emp -> emp.getDepartment().equals("HR"));
```

---

### 10. Finding Operations

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Find first element
Optional<Integer> first = numbers.stream()
    .findFirst();
// Result: Optional[1]

// Find any element (useful in parallel streams)
Optional<Integer> any = numbers.stream()
    .findAny();
// Result: Optional[1] (or any element)

// Find first even number
Optional<Integer> firstEven = numbers.stream()
    .filter(n -> n % 2 == 0)
    .findFirst();
// Result: Optional[2]
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Find first employee in IT
Optional<Employee> itEmployee = employees.stream()
    .filter(emp -> emp.getDepartment().equals("IT"))
    .findFirst();

// Find any employee with salary > 100000
Optional<Employee> highEarner = employees.stream()
    .filter(emp -> emp.getSalary() > 100000)
    .findAny();
```

---

## Advanced Stream Operations

### 1. groupingBy() - Group Elements

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David", "Eve");

// Group by length
Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));
// Result: {3=[Bob, Eve], 5=[Alice, David], 7=[Charlie]}

// Count by length
Map<Integer, Long> countByLength = names.stream()
    .collect(Collectors.groupingBy(
        String::length,
        Collectors.counting()
    ));
// Result: {3=2, 5=2, 7=1}
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Group by department
Map<String, List<Employee>> byDepartment = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment));

// Count employees by department
Map<String, Long> countByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.counting()
    ));

// Average salary by department
Map<String, Double> avgSalaryByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.averagingDouble(Employee::getSalary)
    ));

// Group by department, get names
Map<String, List<String>> namesByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.mapping(Employee::getName, Collectors.toList())
    ));
```

---

### 2. partitioningBy() - Split into Two Groups

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Partition into even and odd
Map<Boolean, List<Integer>> partitioned = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
// Result: {false=[1, 3, 5, 7, 9], true=[2, 4, 6, 8, 10]}
```

**Real Example:**
```java
List<Employee> employees = getAllEmployees();

// Partition by salary > 50000
Map<Boolean, List<Employee>> partitioned = employees.stream()
    .collect(Collectors.partitioningBy(emp -> emp.getSalary() > 50000));

List<Employee> highEarners = partitioned.get(true);
List<Employee> lowEarners = partitioned.get(false);

// Count in each partition
Map<Boolean, Long> counts = employees.stream()
    .collect(Collectors.partitioningBy(
        emp -> emp.getSalary() > 50000,
        Collectors.counting()
    ));
```

---

### 3. Parallel Streams

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Sequential stream
long sum1 = numbers.stream()
    .mapToLong(Integer::longValue)
    .sum();

// Parallel stream (uses multiple threads)
long sum2 = numbers.parallelStream()
    .mapToLong(Integer::longValue)
    .sum();

// Convert to parallel
long sum3 = numbers.stream()
    .parallel()
    .mapToLong(Integer::longValue)
    .sum();
```

**When to Use Parallel Streams:**
- ✅ Large datasets (> 10,000 elements)
- ✅ CPU-intensive operations
- ✅ Independent operations (no shared state)

**When NOT to Use:**
- ❌ Small datasets (overhead > benefit)
- ❌ I/O operations (network, database)
- ❌ Operations with side effects

---

## Real Project Examples

### Example 1: E-Commerce Product Filtering

```java
class Product {
    String name;
    String category;
    double price;
    int rating;
    boolean inStock;
}

List<Product> products = getAllProducts();

// Get available products in "Electronics" under $500, sorted by rating
List<Product> filtered = products.stream()
    .filter(Product::isInStock)
    .filter(p -> p.getCategory().equals("Electronics"))
    .filter(p -> p.getPrice() < 500)
    .sorted(Comparator.comparing(Product::getRating).reversed())
    .collect(Collectors.toList());

// Get average price by category
Map<String, Double> avgPriceByCategory = products.stream()
    .collect(Collectors.groupingBy(
        Product::getCategory,
        Collectors.averagingDouble(Product::getPrice)
    ));

// Get top 10 highest rated products
List<Product> top10 = products.stream()
    .sorted(Comparator.comparing(Product::getRating).reversed())
    .limit(10)
    .collect(Collectors.toList());
```

### Example 2: Employee Analytics

```java
class Employee {
    String name;
    String department;
    double salary;
    int age;
    LocalDate joiningDate;
}

List<Employee> employees = getAllEmployees();

// Get employees hired in last year with salary > 50000
LocalDate oneYearAgo = LocalDate.now().minusYears(1);
List<Employee> recentHires = employees.stream()
    .filter(emp -> emp.getJoiningDate().isAfter(oneYearAgo))
    .filter(emp -> emp.getSalary() > 50000)
    .collect(Collectors.toList());

// Department-wise statistics
Map<String, DoubleSummaryStatistics> deptStats = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.summarizingDouble(Employee::getSalary)
    ));

// Get highest paid employee in each department
Map<String, Optional<Employee>> highestPaidByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.maxBy(Comparator.comparing(Employee::getSalary))
    ));

// Total salary expense by department
Map<String, Double> totalSalaryByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.summingDouble(Employee::getSalary)
    ));
```

### Example 3: Order Processing

```java
class Order {
    String orderId;
    String customerId;
    List<OrderItem> items;
    LocalDateTime orderDate;
    String status;
}

class OrderItem {
    String productId;
    int quantity;
    double price;
}

List<Order> orders = getAllOrders();

// Get total revenue
double totalRevenue = orders.stream()
    .flatMap(order -> order.getItems().stream())
    .mapToDouble(item -> item.getQuantity() * item.getPrice())
    .sum();

// Get orders from last month
LocalDateTime lastMonth = LocalDateTime.now().minusMonths(1);
List<Order> recentOrders = orders.stream()
    .filter(order -> order.getOrderDate().isAfter(lastMonth))
    .collect(Collectors.toList());

// Get top 5 customers by order count
Map<String, Long> orderCountByCustomer = orders.stream()
    .collect(Collectors.groupingBy(
        Order::getCustomerId,
        Collectors.counting()
    ));

List<String> top5Customers = orderCountByCustomer.entrySet().stream()
    .sorted(Map.Entry.<String, Long>comparingByValue().reversed())
    .limit(5)
    .map(Map.Entry::getKey)
    .collect(Collectors.toList());

// Get most sold products
Map<String, Integer> productQuantities = orders.stream()
    .flatMap(order -> order.getItems().stream())
    .collect(Collectors.groupingBy(
        OrderItem::getProductId,
        Collectors.summingInt(OrderItem::getQuantity)
    ));
```

---

## Common Interview Questions

### Q1: What's the difference between map() and flatMap()?

**Answer:**
"map() transforms each element to another element (one-to-one), while flatMap() transforms each element to a stream and then flattens all streams into one (one-to-many).

For example, if you have a list of sentences and want to get all words:
- map() would give you List<String[]> (array of words for each sentence)
- flatMap() would give you List<String> (all words in one list)"

**Example:**
```java
List<String> sentences = Arrays.asList("Hello World", "Java Streams");

// map - returns List<String[]>
List<String[]> words1 = sentences.stream()
    .map(s -> s.split(" "))
    .collect(Collectors.toList());
// Result: [["Hello", "World"], ["Java", "Streams"]]

// flatMap - returns List<String>
List<String> words2 = sentences.stream()
    .flatMap(s -> Arrays.stream(s.split(" ")))
    .collect(Collectors.toList());
// Result: ["Hello", "World", "Java", "Streams"]
```

### Q2: What's the difference between intermediate and terminal operations?

**Answer:**
"Intermediate operations return a Stream and are lazy - they don't execute until a terminal operation is called. Examples: filter(), map(), sorted().

Terminal operations return a result (not a Stream) and trigger the execution of the entire pipeline. Examples: collect(), forEach(), count(), reduce().

You can chain multiple intermediate operations, but you can only have one terminal operation at the end."

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// This doesn't execute anything (no terminal operation)
Stream<Integer> stream = numbers.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * 2);

// This triggers execution
List<Integer> result = numbers.stream()
    .filter(n -> n % 2 == 0)  // Intermediate
    .map(n -> n * 2)           // Intermediate
    .collect(Collectors.toList());  // Terminal
```

### Q3: How do you convert a List to a Map using streams?

**Answer:**
"Use Collectors.toMap() with key and value mappers."

**Example:**
```java
List<Employee> employees = getAllEmployees();

// Map of ID to Employee
Map<Integer, Employee> idToEmployee = employees.stream()
    .collect(Collectors.toMap(
        Employee::getId,      // key
        emp -> emp            // value
    ));

// Map of ID to Name
Map<Integer, String> idToName = employees.stream()
    .collect(Collectors.toMap(
        Employee::getId,
        Employee::getName
    ));

// Handle duplicate keys
Map<String, Employee> deptToEmployee = employees.stream()
    .collect(Collectors.toMap(
        Employee::getDepartment,
        emp -> emp,
        (existing, replacement) -> existing  // Keep first
    ));
```

### Q4: How do you find duplicates in a list using streams?

**Answer:**
"Use groupingBy() with counting(), then filter entries with count > 1."

**Example:**
```java
List<String> names = Arrays.asList("Alice", "Bob", "Alice", "Charlie", "Bob", "Alice");

// Method 1: Using groupingBy
Set<String> duplicates = names.stream()
    .collect(Collectors.groupingBy(name -> name, Collectors.counting()))
    .entrySet().stream()
    .filter(entry -> entry.getValue() > 1)
    .map(Map.Entry::getKey)
    .collect(Collectors.toSet());
// Result: [Alice, Bob]

// Method 2: Using Set
Set<String> seen = new HashSet<>();
Set<String> duplicates2 = names.stream()
    .filter(name -> !seen.add(name))
    .collect(Collectors.toSet());
// Result: [Alice, Bob]
```

### Q5: How do you sort a Map by values using streams?

**Answer:**
"Convert to stream of entries, sort by value, collect to LinkedHashMap to maintain order."

**Example:**
```java
Map<String, Integer> scores = new HashMap<>();
scores.put("Alice", 85);
scores.put("Bob", 92);
scores.put("Charlie", 78);

// Sort by value (ascending)
Map<String, Integer> sortedAsc = scores.entrySet().stream()
    .sorted(Map.Entry.comparingByValue())
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        Map.Entry::getValue,
        (e1, e2) -> e1,
        LinkedHashMap::new
    ));

// Sort by value (descending)
Map<String, Integer> sortedDesc = scores.entrySet().stream()
    .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        Map.Entry::getValue,
        (e1, e2) -> e1,
        LinkedHashMap::new
    ));
```

### Q6: When should you use parallel streams?

**Answer:**
"Use parallel streams when:
1. You have a large dataset (typically > 10,000 elements)
2. Operations are CPU-intensive
3. Operations are independent (no shared state)
4. Order doesn't matter

Avoid parallel streams when:
1. Dataset is small (overhead > benefit)
2. Operations involve I/O (network, database)
3. Operations have side effects or shared state
4. Order matters and you need sequential processing"

### Q7: How do you handle null values in streams?

**Answer:**
"Filter out nulls or use Optional."

**Example:**
```java
List<String> names = Arrays.asList("Alice", null, "Bob", null, "Charlie");

// Method 1: Filter nulls
List<String> nonNull = names.stream()
    .filter(Objects::nonNull)
    .collect(Collectors.toList());
// Result: [Alice, Bob, Charlie]

// Method 2: Using Optional
List<String> nonNull2 = names.stream()
    .filter(name -> name != null)
    .collect(Collectors.toList());

// Method 3: Map to Optional
List<Optional<String>> optionals = names.stream()
    .map(Optional::ofNullable)
    .collect(Collectors.toList());
```

### Q8: How do you get the top N elements from a stream?

**Answer:**
"Use sorted() with limit()."

**Example:**
```java
List<Employee> employees = getAllEmployees();

// Top 5 highest paid employees
List<Employee> top5 = employees.stream()
    .sorted(Comparator.comparing(Employee::getSalary).reversed())
    .limit(5)
    .collect(Collectors.toList());

// Top 3 youngest employees
List<Employee> youngest3 = employees.stream()
    .sorted(Comparator.comparing(Employee::getAge))
    .limit(3)
    .collect(Collectors.toList());
```

### Q9: How do you combine multiple lists using streams?

**Answer:**
"Use Stream.concat() or flatMap()."

**Example:**
```java
List<Integer> list1 = Arrays.asList(1, 2, 3);
List<Integer> list2 = Arrays.asList(4, 5, 6);
List<Integer> list3 = Arrays.asList(7, 8, 9);

// Method 1: Stream.concat (for 2 lists)
List<Integer> combined = Stream.concat(list1.stream(), list2.stream())
    .collect(Collectors.toList());

// Method 2: flatMap (for multiple lists)
List<Integer> allCombined = Stream.of(list1, list2, list3)
    .flatMap(List::stream)
    .collect(Collectors.toList());
// Result: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Q10: How do you count occurrences of each element?

**Answer:**
"Use Collectors.groupingBy() with Collectors.counting()."

**Example:**
```java
List<String> words = Arrays.asList("apple", "banana", "apple", "cherry", "banana", "apple");

// Count occurrences
Map<String, Long> wordCount = words.stream()
    .collect(Collectors.groupingBy(
        word -> word,
        Collectors.counting()
    ));
// Result: {apple=3, banana=2, cherry=1}

// Get most frequent word
Optional<Map.Entry<String, Long>> mostFrequent = wordCount.entrySet().stream()
    .max(Map.Entry.comparingByValue());
```

---

## Common Mistakes and Best Practices

### Mistake 1: Reusing Streams

**Wrong:**
```java
Stream<String> stream = list.stream();
stream.forEach(System.out::println);
stream.forEach(System.out::println);  // IllegalStateException!
```

**Correct:**
```java
list.stream().forEach(System.out::println);
list.stream().forEach(System.out::println);  // Create new stream
```

### Mistake 2: Modifying Source During Stream Operations

**Wrong:**
```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
list.stream()
    .forEach(item -> list.add("D"));  // ConcurrentModificationException!
```

**Correct:**
```java
List<String> newList = list.stream()
    .collect(Collectors.toList());
newList.add("D");
```

### Mistake 3: Using Parallel Streams Incorrectly

**Wrong:**
```java
List<Integer> list = new ArrayList<>();
IntStream.range(0, 1000)
    .parallel()
    .forEach(list::add);  // Not thread-safe!
```

**Correct:**
```java
List<Integer> list = IntStream.range(0, 1000)
    .parallel()
    .boxed()
    .collect(Collectors.toList());  // Thread-safe
```

---

## Quick Reference

### Common Patterns

```java
// Filter and collect
list.stream()
    .filter(predicate)
    .collect(Collectors.toList());

// Map and collect
list.stream()
    .map(function)
    .collect(Collectors.toList());

// Filter, map, and collect
list.stream()
    .filter(predicate)
    .map(function)
    .collect(Collectors.toList());

// Group by
list.stream()
    .collect(Collectors.groupingBy(classifier));

// Count
long count = list.stream()
    .filter(predicate)
    .count();

// Sum
int sum = list.stream()
    .mapToInt(ToIntFunction)
    .sum();

// Average
double avg = list.stream()
    .mapToDouble(ToDoubleFunction)
    .average()
    .orElse(0.0);

// Max/Min
Optional<T> max = list.stream()
    .max(Comparator);
```

---

## Summary

**Key Takeaways:**

1. **Streams are pipelines** - Data flows through operations
2. **Lazy evaluation** - Intermediate operations don't execute until terminal operation
3. **Immutable** - Streams don't modify source
4. **One-time use** - Can't reuse a stream
5. **Functional style** - Declarative, not imperative

**Most Important Operations:**
- filter() - Select elements
- map() - Transform elements
- collect() - Gather results
- reduce() - Combine to single value
- sorted() - Order elements
- distinct() - Remove duplicates
- groupingBy() - Group elements

**Interview Tips:**
- Always mention lazy evaluation
- Know difference between map() and flatMap()
- Understand when to use parallel streams
- Practice common patterns (filter-map-collect)
- Know how to handle nulls

**You're now 95% Collections interview ready!** 🎉

Stream API is one of the most practical and frequently asked topics in modern Java interviews. Master these patterns and you'll ace any Stream-related question!