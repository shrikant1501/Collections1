# Comparable vs Comparator - Complete Guide

## Simple Explanation

### What is Sorting?

Imagine you have a list of students and you want to arrange them:
- By roll number (1, 2, 3...)
- By name (alphabetically)
- By marks (highest to lowest)

**Comparable and Comparator help you define HOW to sort objects.**

---

## Comparable Interface

### Simple Definition

**Comparable = "I know how to compare myself with others"**

The class itself defines its natural/default sorting order.

### Real-World Analogy

Think of students with roll numbers. Each student knows their roll number and can say:
- "I come before you" (my roll number is smaller)
- "I come after you" (my roll number is bigger)
- "We're equal" (same roll number)

### How It Works

```java
class Student implements Comparable<Student> {
    int rollNo;
    String name;
    int marks;
    
    // Constructor
    public Student(int rollNo, String name, int marks) {
        this.rollNo = rollNo;
        this.name = name;
        this.marks = marks;
    }
    
    // Natural ordering: by roll number
    @Override
    public int compareTo(Student other) {
        return this.rollNo - other.rollNo;
    }
    
    @Override
    public String toString() {
        return "Student{rollNo=" + rollNo + ", name='" + name + "', marks=" + marks + "}";
    }
}

// Usage
List<Student> students = new ArrayList<>();
students.add(new Student(3, "Alice", 85));
students.add(new Student(1, "Bob", 90));
students.add(new Student(2, "Charlie", 78));

Collections.sort(students);  // Sorts by roll number automatically
System.out.println(students);
// Output: [Student{rollNo=1, name='Bob', marks=90}, 
//          Student{rollNo=2, name='Charlie', marks=78}, 
//          Student{rollNo=3, name='Alice', marks=85}]
```

### Key Points

1. **One sorting logic** - Class defines only ONE natural order
2. **Modifies the class** - Must implement Comparable in the class
3. **Simple to use** - Just call `Collections.sort(list)`
4. **Return values:**
   - Negative number: this < other (this comes first)
   - Zero: this == other (equal)
   - Positive number: this > other (other comes first)

---

## Comparator Interface

### Simple Definition

**Comparator = "External judge who decides how to compare"**

You create separate comparison logic outside the class.

### Real-World Analogy

Think of a sports competition with multiple judges:
- Judge 1: Sorts by speed
- Judge 2: Sorts by technique
- Judge 3: Sorts by style

Each judge has their own criteria. The athlete doesn't change, but different judges rank them differently.

### How It Works

```java
class Student {
    int rollNo;
    String name;
    int marks;
    
    public Student(int rollNo, String name, int marks) {
        this.rollNo = rollNo;
        this.name = name;
        this.marks = marks;
    }
    
    @Override
    public String toString() {
        return "Student{rollNo=" + rollNo + ", name='" + name + "', marks=" + marks + "}";
    }
}

// Comparator 1: Sort by name
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name);
    }
}

// Comparator 2: Sort by marks (descending)
class MarksComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s2.marks - s1.marks;  // Reverse for descending
    }
}

// Usage
List<Student> students = new ArrayList<>();
students.add(new Student(3, "Alice", 85));
students.add(new Student(1, "Bob", 90));
students.add(new Student(2, "Charlie", 78));

// Sort by name
Collections.sort(students, new NameComparator());
System.out.println("By Name: " + students);
// Output: [Alice, Bob, Charlie]

// Sort by marks
Collections.sort(students, new MarksComparator());
System.out.println("By Marks: " + students);
// Output: [Bob(90), Alice(85), Charlie(78)]
```

### Key Points

1. **Multiple sorting logics** - Can have many different Comparators
2. **Doesn't modify class** - Comparison logic is external
3. **Flexible** - Can sort by any criteria
4. **Pass to sort method** - `Collections.sort(list, comparator)`

---

## Comparable vs Comparator - Quick Comparison

| Feature | Comparable | Comparator |
|---------|-----------|------------|
| **Location** | Inside the class | Outside the class |
| **Method** | `compareTo(T o)` | `compare(T o1, T o2)` |
| **Sorting Orders** | One (natural order) | Multiple (custom orders) |
| **Modify Class** | Yes (implement interface) | No (separate class) |
| **Usage** | `Collections.sort(list)` | `Collections.sort(list, comparator)` |
| **Package** | `java.lang` | `java.util` |
| **When to Use** | Default/natural sorting | Multiple sorting criteria |

---

## Real Project Examples

### Example 1: E-Commerce Product Sorting

```java
class Product {
    String name;
    double price;
    int rating;
    
    public Product(String name, double price, int rating) {
        this.name = name;
        this.price = price;
        this.rating = rating;
    }
}

// Sort by price (low to high)
Comparator<Product> priceComparator = (p1, p2) -> 
    Double.compare(p1.price, p2.price);

// Sort by rating (high to low)
Comparator<Product> ratingComparator = (p1, p2) -> 
    p2.rating - p1.rating;

// Sort by name
Comparator<Product> nameComparator = (p1, p2) -> 
    p1.name.compareTo(p2.name);

// Usage in real project
List<Product> products = getProductsFromDatabase();

// User selects "Sort by Price"
products.sort(priceComparator);

// User selects "Sort by Rating"
products.sort(ratingComparator);
```

### Example 2: Employee Management System

```java
class Employee implements Comparable<Employee> {
    int id;
    String name;
    double salary;
    LocalDate joiningDate;
    
    // Natural order: by employee ID
    @Override
    public int compareTo(Employee other) {
        return this.id - other.id;
    }
}

// Additional sorting options
Comparator<Employee> salaryComparator = 
    Comparator.comparingDouble(Employee::getSalary);

Comparator<Employee> joiningDateComparator = 
    Comparator.comparing(Employee::getJoiningDate);

Comparator<Employee> nameComparator = 
    Comparator.comparing(Employee::getName);

// Real usage
List<Employee> employees = getAllEmployees();

// Default sort (by ID)
Collections.sort(employees);

// Sort by salary for payroll
employees.sort(salaryComparator);

// Sort by joining date for seniority
employees.sort(joiningDateComparator);
```

### Example 3: Social Media Posts

```java
class Post {
    String content;
    LocalDateTime timestamp;
    int likes;
    int comments;
    
    // Natural order: newest first
    public int compareTo(Post other) {
        return other.timestamp.compareTo(this.timestamp);
    }
}

// Sort by popularity
Comparator<Post> popularityComparator = (p1, p2) -> {
    int score1 = p1.likes + p1.comments * 2;
    int score2 = p2.likes + p2.comments * 2;
    return score2 - score1;
};

// Sort by engagement rate
Comparator<Post> engagementComparator = 
    Comparator.comparingInt((Post p) -> p.likes + p.comments)
              .reversed();

// Real usage
List<Post> posts = getUserPosts();

// Default: Show newest first
Collections.sort(posts);

// User selects "Most Popular"
posts.sort(popularityComparator);
```

---

## Java 8+ Lambda Syntax (Modern Way)

### Old Way (Verbose)

```java
Collections.sort(students, new Comparator<Student>() {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name);
    }
});
```

### New Way (Lambda)

```java
// Simple comparison
students.sort((s1, s2) -> s1.name.compareTo(s2.name));

// Using Comparator.comparing
students.sort(Comparator.comparing(Student::getName));

// Reverse order
students.sort(Comparator.comparing(Student::getMarks).reversed());

// Multiple criteria
students.sort(Comparator.comparing(Student::getMarks)
                        .thenComparing(Student::getName));
```

---

## Common Interview Questions

### Q1: What's the difference between Comparable and Comparator?

**Answer:**
"Comparable is used for natural ordering and is implemented by the class itself. It has one method `compareTo()` and defines the default sorting logic. For example, String implements Comparable to sort alphabetically.

Comparator is used for custom ordering and is a separate class. It has a `compare()` method and allows multiple sorting criteria. For example, you can create different Comparators to sort employees by name, salary, or joining date without modifying the Employee class."

### Q2: Can a class implement both Comparable and have Comparators?

**Answer:**
"Yes, absolutely. A class can implement Comparable for its natural ordering, and you can still create multiple Comparators for different sorting needs. For example, Employee might implement Comparable to sort by ID (natural order), but you can create Comparators to sort by name, salary, or department."

**Example:**
```java
class Employee implements Comparable<Employee> {
    int id;
    String name;
    double salary;
    
    // Natural order: by ID
    @Override
    public int compareTo(Employee other) {
        return this.id - other.id;
    }
}

// Custom comparators
Comparator<Employee> nameComparator = Comparator.comparing(Employee::getName);
Comparator<Employee> salaryComparator = Comparator.comparingDouble(Employee::getSalary);

// Usage
Collections.sort(employees);  // By ID (natural order)
employees.sort(nameComparator);  // By name
employees.sort(salaryComparator);  // By salary
```

### Q3: How do you sort in descending order?

**Answer:**
"There are multiple ways:

1. **Reverse the comparison logic:**
```java
// Ascending
(s1, s2) -> s1.marks - s2.marks

// Descending
(s1, s2) -> s2.marks - s1.marks
```

2. **Use reversed():**
```java
students.sort(Comparator.comparing(Student::getMarks).reversed());
```

3. **Use Collections.reverseOrder():**
```java
Collections.sort(students, Collections.reverseOrder());
```

### Q4: How do you sort by multiple fields?

**Answer:**
"Use `thenComparing()` to chain multiple comparisons. The second comparison is used only when the first comparison returns equal."

**Example:**
```java
// Sort by marks (descending), then by name (ascending)
students.sort(Comparator.comparing(Student::getMarks).reversed()
                        .thenComparing(Student::getName));

// Sort by department, then by salary (descending), then by name
employees.sort(Comparator.comparing(Employee::getDepartment)
                         .thenComparing(Employee::getSalary, Comparator.reverseOrder())
                         .thenComparing(Employee::getName));
```

### Q5: What happens if compareTo() or compare() returns 0?

**Answer:**
"Returning 0 means the two objects are considered equal for sorting purposes. In a TreeSet or TreeMap, objects that compare as equal (return 0) are treated as duplicates, and only one is kept. This is important because TreeSet uses compareTo() or compare() for equality, not equals()."

**Example:**
```java
class Person implements Comparable<Person> {
    String name;
    int age;
    
    @Override
    public int compareTo(Person other) {
        return this.age - other.age;  // Only compares age
    }
}

TreeSet<Person> set = new TreeSet<>();
set.add(new Person("Alice", 25));
set.add(new Person("Bob", 25));  // Not added! Same age = equal

System.out.println(set.size());  // 1 (only one person with age 25)
```

### Q6: How do you handle null values in sorting?

**Answer:**
"Use `Comparator.nullsFirst()` or `Comparator.nullsLast()` to handle nulls safely."

**Example:**
```java
// Nulls at the beginning
students.sort(Comparator.nullsFirst(Comparator.comparing(Student::getName)));

// Nulls at the end
students.sort(Comparator.nullsLast(Comparator.comparing(Student::getName)));

// Nulls first, then sort by name
students.sort(Comparator.nullsFirst(
    Comparator.comparing(Student::getName, Comparator.nullsFirst(String::compareTo))
));
```

### Q7: Can you sort a List of Strings in case-insensitive order?

**Answer:**
"Yes, use `String.CASE_INSENSITIVE_ORDER` comparator or create a custom one."

**Example:**
```java
List<String> names = Arrays.asList("alice", "Bob", "CHARLIE", "david");

// Case-insensitive sort
names.sort(String.CASE_INSENSITIVE_ORDER);
// Result: [alice, Bob, CHARLIE, david]

// Or using lambda
names.sort((s1, s2) -> s1.compareToIgnoreCase(s2));

// Or using Comparator
names.sort(Comparator.comparing(String::toLowerCase));
```

### Q8: How do you sort a Map by values?

**Answer:**
"Convert the Map to a List of entries, sort the list, then collect back to a LinkedHashMap to maintain order."

**Example:**
```java
Map<String, Integer> scores = new HashMap<>();
scores.put("Alice", 85);
scores.put("Bob", 92);
scores.put("Charlie", 78);

// Sort by value (ascending)
Map<String, Integer> sortedMap = scores.entrySet()
    .stream()
    .sorted(Map.Entry.comparingByValue())
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        Map.Entry::getValue,
        (e1, e2) -> e1,
        LinkedHashMap::new
    ));

// Sort by value (descending)
Map<String, Integer> sortedDesc = scores.entrySet()
    .stream()
    .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        Map.Entry::getValue,
        (e1, e2) -> e1,
        LinkedHashMap::new
    ));
```

### Q9: What's the difference between Comparable and equals()?

**Answer:**
"Comparable is for ordering (sorting), while equals() is for equality checking. They serve different purposes:

- `compareTo()` returns negative, zero, or positive (ordering)
- `equals()` returns true or false (equality)

However, they should be consistent: if `a.equals(b)` is true, then `a.compareTo(b)` should return 0. Breaking this consistency can cause issues in sorted collections like TreeSet."

### Q10: How do you sort a collection of custom objects without modifying the class?

**Answer:**
"Use Comparator. This is the main advantage of Comparator - you can sort objects without changing their class definition."

**Example:**
```java
// Class you can't modify (from a library)
class Book {
    String title;
    String author;
    int year;
    // No Comparable implementation
}

// Create Comparators externally
Comparator<Book> titleComparator = Comparator.comparing(Book::getTitle);
Comparator<Book> authorComparator = Comparator.comparing(Book::getAuthor);
Comparator<Book> yearComparator = Comparator.comparingInt(Book::getYear);

// Sort without modifying Book class
List<Book> books = getBooks();
books.sort(titleComparator);
```

---

## Common Mistakes and How to Avoid Them

### Mistake 1: Inconsistent compareTo() and equals()

**Wrong:**
```java
class Person implements Comparable<Person> {
    String name;
    int age;
    
    @Override
    public int compareTo(Person other) {
        return this.age - other.age;  // Only compares age
    }
    
    @Override
    public boolean equals(Object obj) {
        Person other = (Person) obj;
        return this.name.equals(other.name) && this.age == other.age;  // Compares both
    }
}

// Problem: Two people with same age but different names
// compareTo() says equal, equals() says not equal
```

**Correct:**
```java
@Override
public int compareTo(Person other) {
    int ageCompare = this.age - other.age;
    if (ageCompare != 0) return ageCompare;
    return this.name.compareTo(other.name);  // Consistent with equals
}
```

### Mistake 2: Integer Overflow in Subtraction

**Wrong:**
```java
@Override
public int compareTo(Person other) {
    return this.age - other.age;  // Can overflow!
}

// Problem: If this.age = 2147483647 and other.age = -1
// Result: 2147483648 (overflow, becomes negative!)
```

**Correct:**
```java
@Override
public int compareTo(Person other) {
    return Integer.compare(this.age, other.age);  // Safe
}

// Or
@Override
public int compareTo(Person other) {
    if (this.age < other.age) return -1;
    if (this.age > other.age) return 1;
    return 0;
}
```

### Mistake 3: Not Handling Nulls

**Wrong:**
```java
students.sort(Comparator.comparing(Student::getName));
// NullPointerException if any student has null name!
```

**Correct:**
```java
students.sort(Comparator.comparing(
    Student::getName,
    Comparator.nullsLast(String::compareTo)
));
```

---

## Quick Reference

### Comparable Template

```java
class MyClass implements Comparable<MyClass> {
    private int field;
    
    @Override
    public int compareTo(MyClass other) {
        return Integer.compare(this.field, other.field);
    }
}
```

### Comparator Templates

```java
// Lambda
Comparator<MyClass> comp1 = (o1, o2) -> o1.field - o2.field;

// Method reference
Comparator<MyClass> comp2 = Comparator.comparing(MyClass::getField);

// Multiple fields
Comparator<MyClass> comp3 = Comparator
    .comparing(MyClass::getField1)
    .thenComparing(MyClass::getField2);

// Reverse
Comparator<MyClass> comp4 = Comparator
    .comparing(MyClass::getField)
    .reversed();
```

---

## Summary

**Use Comparable when:**
- ✅ There's ONE natural/obvious way to sort
- ✅ You control the class source code
- ✅ Sorting logic is fundamental to the class
- ✅ Example: Employee by ID, String alphabetically

**Use Comparator when:**
- ✅ Need MULTIPLE ways to sort
- ✅ Can't modify the class
- ✅ Sorting logic is external/contextual
- ✅ Example: Sort products by price, rating, or name

**Remember:**
- Comparable = `compareTo(other)` - one method, one order
- Comparator = `compare(o1, o2)` - many comparators, many orders
- Java 8+ makes Comparator much easier with lambdas
- Always handle nulls and avoid integer overflow
- Keep compareTo() consistent with equals()

**Interview Tip:**
Always mention that Comparable is for natural ordering (one way) and Comparator is for custom ordering (multiple ways). Give a real example like sorting employees by ID (Comparable) vs sorting by name, salary, or department (Comparator).