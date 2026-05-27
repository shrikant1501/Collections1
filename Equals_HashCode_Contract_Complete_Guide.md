# equals() and hashCode() Contract - Complete Guide

## Simple Explanation

### What are equals() and hashCode()?

**equals()** - Tells if two objects are "equal" (have same content)
**hashCode()** - Gives a number (hash code) representing the object

**Real-World Analogy:**
Think of a library with books:
- **equals()** = Checking if two books have the same content (title, author, ISBN)
- **hashCode()** = The shelf number where the book should be stored

---

## The Contract (Rules)

### Rule 1: Consistency with equals()

```
If a.equals(b) returns true
Then a.hashCode() MUST equal b.hashCode()
```

**Simple:** If two objects are equal, they MUST have the same hash code.

### Rule 2: Hash Code Doesn't Guarantee Equality

```
If a.hashCode() == b.hashCode()
Then a.equals(b) MAY be true or false
```

**Simple:** Same hash code doesn't mean objects are equal (collision is allowed).

### Rule 3: Consistency

```
Multiple calls to hashCode() on same object
MUST return same value (if object not modified)
```

**Simple:** Hash code shouldn't change unless object changes.

### Rule 4: equals() Must Be

- **Reflexive**: `a.equals(a)` must be true
- **Symmetric**: If `a.equals(b)` is true, then `b.equals(a)` must be true
- **Transitive**: If `a.equals(b)` and `b.equals(c)`, then `a.equals(c)` must be true
- **Consistent**: Multiple calls return same result
- **Null handling**: `a.equals(null)` must be false

---

## Why This Contract Matters

### Problem Without Proper Implementation

```java
class Person {
    String name;
    int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Only equals() overridden, hashCode() NOT overridden
    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof Person)) return false;
        Person other = (Person) obj;
        return this.name.equals(other.name) && this.age == other.age;
    }
    
    // hashCode() NOT overridden - uses Object.hashCode() (memory address)
}

// The Problem:
Person p1 = new Person("Alice", 25);
Person p2 = new Person("Alice", 25);

System.out.println(p1.equals(p2));  // true (same content)
System.out.println(p1.hashCode() == p2.hashCode());  // false! (different memory addresses)

// Using in HashMap
Map<Person, String> map = new HashMap<>();
map.put(p1, "Engineer");

System.out.println(map.get(p2));  // null! (Can't find it!)

// Why?
// p1 and p2 have different hash codes
// They go to different buckets in HashMap
// Even though equals() says they're equal!
```

**Result:** HashMap is broken! Can't retrieve values using "equal" keys.

---

## Correct Implementation

### Example 1: Simple Class

```java
class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Correct equals() implementation
    @Override
    public boolean equals(Object obj) {
        // 1. Check if same object (optimization)
        if (this == obj) return true;
        
        // 2. Check if null
        if (obj == null) return false;
        
        // 3. Check if same class
        if (getClass() != obj.getClass()) return false;
        
        // 4. Cast and compare fields
        Person other = (Person) obj;
        return age == other.age && 
               Objects.equals(name, other.name);
    }
    
    // Correct hashCode() implementation
    @Override
    public int hashCode() {
        return Objects.hash(name, age);  // Use same fields as equals()
    }
}

// Now it works!
Person p1 = new Person("Alice", 25);
Person p2 = new Person("Alice", 25);

System.out.println(p1.equals(p2));  // true
System.out.println(p1.hashCode() == p2.hashCode());  // true

Map<Person, String> map = new HashMap<>();
map.put(p1, "Engineer");
System.out.println(map.get(p2));  // "Engineer" (Works!)
```

### Example 2: Class with Multiple Fields

```java
class Employee {
    private int id;
    private String name;
    private String department;
    private double salary;
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        
        Employee employee = (Employee) obj;
        return id == employee.id &&
               Double.compare(employee.salary, salary) == 0 &&
               Objects.equals(name, employee.name) &&
               Objects.equals(department, employee.department);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id, name, department, salary);
    }
}
```

### Example 3: Using Only Key Fields

```java
class Student {
    private int rollNo;      // Key field
    private String name;     // Non-key field
    private int marks;       // Non-key field
    
    // Only compare rollNo (unique identifier)
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        
        Student student = (Student) obj;
        return rollNo == student.rollNo;
    }
    
    // Only use rollNo in hashCode
    @Override
    public int hashCode() {
        return Integer.hashCode(rollNo);
    }
}

// Two students with same rollNo are considered equal
// Even if name or marks are different
Student s1 = new Student(101, "Alice", 85);
Student s2 = new Student(101, "Bob", 90);

System.out.println(s1.equals(s2));  // true (same rollNo)
```

---

## Common Mistakes

### Mistake 1: Using Different Fields

**Wrong:**
```java
class Person {
    String name;
    int age;
    String city;
    
    @Override
    public boolean equals(Object obj) {
        Person other = (Person) obj;
        return this.name.equals(other.name) && 
               this.age == other.age;  // Uses name and age
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, city);  // Uses name and city - WRONG!
    }
}

// Problem:
Person p1 = new Person("Alice", 25, "NYC");
Person p2 = new Person("Alice", 25, "LA");

System.out.println(p1.equals(p2));  // true (same name and age)
System.out.println(p1.hashCode() == p2.hashCode());  // false! (different cities)

// Violates contract!
```

**Correct:**
```java
@Override
public boolean equals(Object obj) {
    Person other = (Person) obj;
    return this.name.equals(other.name) && this.age == other.age;
}

@Override
public int hashCode() {
    return Objects.hash(name, age);  // Same fields as equals()
}
```

### Mistake 2: Not Handling Null

**Wrong:**
```java
@Override
public boolean equals(Object obj) {
    Person other = (Person) obj;  // NullPointerException if obj is null!
    return this.name.equals(other.name);
}
```

**Correct:**
```java
@Override
public boolean equals(Object obj) {
    if (obj == null) return false;  // Handle null first
    if (getClass() != obj.getClass()) return false;
    
    Person other = (Person) obj;
    return Objects.equals(this.name, other.name);  // Objects.equals handles null
}
```

### Mistake 3: Not Checking Class Type

**Wrong:**
```java
@Override
public boolean equals(Object obj) {
    Person other = (Person) obj;  // ClassCastException if obj is not Person!
    return this.name.equals(other.name);
}
```

**Correct:**
```java
@Override
public boolean equals(Object obj) {
    if (obj == null || getClass() != obj.getClass()) return false;
    
    Person other = (Person) obj;
    return Objects.equals(this.name, other.name);
}
```

### Mistake 4: Mutable Fields in hashCode()

**Wrong:**
```java
class Person {
    String name;  // Mutable field
    
    @Override
    public int hashCode() {
        return name.hashCode();
    }
}

Person p = new Person("Alice");
Set<Person> set = new HashSet<>();
set.add(p);

p.name = "Bob";  // Modify after adding to set

System.out.println(set.contains(p));  // false! (Lost in wrong bucket)
```

**Solution:** Use immutable fields or make class immutable.

### Mistake 5: Always Returning Same hashCode()

**Wrong:**
```java
@Override
public int hashCode() {
    return 1;  // All objects have same hash code!
}

// Problem: All objects go to same bucket
// HashMap performance degrades to O(n)
```

**Correct:**
```java
@Override
public int hashCode() {
    return Objects.hash(field1, field2, field3);  // Good distribution
}
```

---

## Impact on Collections

### HashMap/HashSet

```java
class Book {
    String isbn;
    String title;
    
    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof Book)) return false;
        Book other = (Book) obj;
        return Objects.equals(isbn, other.isbn);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(isbn);
    }
}

// Works correctly in HashMap
Map<Book, Integer> inventory = new HashMap<>();
Book book1 = new Book("123", "Java");
Book book2 = new Book("123", "Java");

inventory.put(book1, 10);
System.out.println(inventory.get(book2));  // 10 (Found!)

// Works correctly in HashSet
Set<Book> books = new HashSet<>();
books.add(book1);
books.add(book2);  // Not added (duplicate)
System.out.println(books.size());  // 1
```

### TreeSet/TreeMap

```java
class Person implements Comparable<Person> {
    String name;
    int age;
    
    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age);
    }
    
    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof Person)) return false;
        Person other = (Person) obj;
        return this.age == other.age && 
               Objects.equals(this.name, other.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

// Important: compareTo() should be consistent with equals()
// If a.equals(b), then a.compareTo(b) should return 0
```

---

## Interview Questions

### Q1: What is the contract between equals() and hashCode()?

**Answer:**
"The contract states that if two objects are equal according to equals(), they must have the same hashCode(). However, two objects with the same hashCode() may or may not be equal.

This is crucial for hash-based collections like HashMap and HashSet. If you override equals() but not hashCode(), objects that are equal might have different hash codes, causing them to be stored in different buckets. This breaks the collection's behavior - you won't be able to find objects even though they're equal."

### Q2: Why must you override both equals() and hashCode() together?

**Answer:**
"You must override both together to maintain the contract. If you only override equals(), the default hashCode() from Object class uses memory address, so equal objects will have different hash codes. This breaks hash-based collections.

If you only override hashCode(), objects with same hash code won't be recognized as equal by equals(), causing duplicates in HashSet or multiple entries with 'equal' keys in HashMap."

**Example:**
```java
// Only equals() overridden
Person p1 = new Person("Alice", 25);
Person p2 = new Person("Alice", 25);

Set<Person> set = new HashSet<>();
set.add(p1);
set.add(p2);  // Added! (Should be duplicate but isn't)
System.out.println(set.size());  // 2 (Wrong! Should be 1)
```

### Q3: What happens if hashCode() always returns the same value?

**Answer:**
"If hashCode() always returns the same value, all objects will be stored in the same bucket in HashMap or HashSet. This degrades performance from O(1) to O(n) because the collection becomes essentially a linked list. While it doesn't violate the contract, it defeats the purpose of hashing."

**Example:**
```java
@Override
public int hashCode() {
    return 1;  // Bad! All objects in same bucket
}

// Performance impact:
// HashMap with 10,000 entries
// All in one bucket
// get() operation: O(10,000) instead of O(1)
```

### Q4: Can two unequal objects have the same hashCode()?

**Answer:**
"Yes, this is called a hash collision and is perfectly normal. The contract only requires that equal objects have the same hashCode(), not that unequal objects have different hash codes. Hash-based collections handle collisions using chaining (linked lists or trees in Java 8+)."

**Example:**
```java
String s1 = "Aa";
String s2 = "BB";

System.out.println(s1.hashCode());  // 2112
System.out.println(s2.hashCode());  // 2112 (same!)
System.out.println(s1.equals(s2));  // false (different strings)

// This is fine - HashMap handles it with collision resolution
```

### Q5: What fields should you use in equals() and hashCode()?

**Answer:**
"Use the same fields in both methods - typically the fields that define the object's identity or business key. For example:
- Employee: use employee ID
- Book: use ISBN
- Person: use name and date of birth

Don't use:
- Mutable fields (can cause objects to be lost in collections)
- Derived/calculated fields (unless they're cached)
- Fields that don't contribute to identity"

### Q6: How do you handle null fields in equals() and hashCode()?

**Answer:**
"Use Objects.equals() and Objects.hash() utility methods which handle nulls safely."

**Example:**
```java
@Override
public boolean equals(Object obj) {
    if (!(obj instanceof Person)) return false;
    Person other = (Person) obj;
    return Objects.equals(this.name, other.name) &&  // Handles null
           this.age == other.age;
}

@Override
public int hashCode() {
    return Objects.hash(name, age);  // Handles null
}

// Objects.equals() handles:
// - Both null: returns true
// - One null: returns false
// - Neither null: calls equals()
```

### Q7: What's the difference between == and equals()?

**Answer:**
"== compares references (memory addresses) - checks if two variables point to the same object.
equals() compares content - checks if two objects are logically equal.

For primitives, == compares values.
For objects, == checks if they're the same instance, while equals() checks if they have the same content (if properly overridden)."

**Example:**
```java
String s1 = new String("Hello");
String s2 = new String("Hello");
String s3 = s1;

System.out.println(s1 == s2);      // false (different objects)
System.out.println(s1.equals(s2)); // true (same content)
System.out.println(s1 == s3);      // true (same object)
```

### Q8: Why is String's hashCode() calculated the way it is?

**Answer:**
"String's hashCode() uses the formula: s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]

31 is used because:
1. It's a prime number (good distribution)
2. It's odd (avoids losing information with multiplication)
3. It's close to a power of 2 (31 = 32-1), allowing JVM optimization
4. It provides good hash distribution for typical strings

This formula ensures different strings usually have different hash codes."

### Q9: Can you use mutable objects as HashMap keys?

**Answer:**
"Technically yes, but it's dangerous. If you modify a key after adding it to HashMap, its hashCode() might change, causing the entry to be in the wrong bucket. The entry becomes unreachable.

Best practice: Use immutable objects as keys (String, Integer, or custom immutable classes)."

**Example:**
```java
class MutableKey {
    int value;
    
    @Override
    public int hashCode() {
        return value;
    }
}

MutableKey key = new MutableKey();
key.value = 1;

Map<MutableKey, String> map = new HashMap<>();
map.put(key, "data");

key.value = 2;  // Modify key!

System.out.println(map.get(key));  // null (Lost!)
```

### Q10: What's the relationship between equals(), hashCode(), and compareTo()?

**Answer:**
"While not strictly required, it's strongly recommended that compareTo() be consistent with equals():
- If a.equals(b) is true, then a.compareTo(b) should return 0
- If a.compareTo(b) returns 0, then a.equals(b) should be true

Inconsistency can cause problems in sorted collections like TreeSet, where compareTo() is used for equality checks instead of equals()."

**Example:**
```java
class Person implements Comparable<Person> {
    String name;
    int age;
    
    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age);  // Only compares age
    }
    
    @Override
    public boolean equals(Object obj) {
        Person other = (Person) obj;
        return this.age == other.age && 
               Objects.equals(this.name, other.name);  // Compares age AND name
    }
}

// Problem:
Person p1 = new Person("Alice", 25);
Person p2 = new Person("Bob", 25);

System.out.println(p1.compareTo(p2));  // 0 (equal age)
System.out.println(p1.equals(p2));     // false (different names)

// In TreeSet:
TreeSet<Person> set = new TreeSet<>();
set.add(p1);
set.add(p2);  // Not added! (compareTo says equal)
System.out.println(set.size());  // 1 (Lost Bob!)
```

---

## Best Practices

### 1. Always Override Both Together

```java
// Good
@Override
public boolean equals(Object obj) { ... }

@Override
public int hashCode() { ... }
```

### 2. Use Objects Utility Methods

```java
// Good - handles nulls
@Override
public boolean equals(Object obj) {
    Person other = (Person) obj;
    return Objects.equals(this.name, other.name);
}

@Override
public int hashCode() {
    return Objects.hash(name, age, city);
}
```

### 3. Use Same Fields

```java
// Good - consistent fields
@Override
public boolean equals(Object obj) {
    return Objects.equals(name, other.name) && age == other.age;
}

@Override
public int hashCode() {
    return Objects.hash(name, age);  // Same fields
}
```

### 4. Make Classes Immutable When Used as Keys

```java
// Good - immutable
public final class Person {
    private final String name;
    private final int age;
    
    // No setters
    
    @Override
    public boolean equals(Object obj) { ... }
    
    @Override
    public int hashCode() { ... }
}
```

### 5. Follow equals() Contract

```java
@Override
public boolean equals(Object obj) {
    // 1. Reflexive: a.equals(a) must be true
    if (this == obj) return true;
    
    // 2. Null handling: a.equals(null) must be false
    if (obj == null) return false;
    
    // 3. Type check
    if (getClass() != obj.getClass()) return false;
    
    // 4. Field comparison
    Person other = (Person) obj;
    return Objects.equals(name, other.name) && age == other.age;
}
```

---

## IDE Generation

Most IDEs can generate equals() and hashCode() for you:

**IntelliJ IDEA:**
- Right-click → Generate → equals() and hashCode()
- Select fields to include
- Choose template (Java 7+, Objects.equals, etc.)

**Eclipse:**
- Right-click → Source → Generate hashCode() and equals()
- Select fields
- Choose options

**Generated Code Example:**
```java
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
```

---

## Summary

**The Contract:**
1. Equal objects MUST have equal hash codes
2. Unequal objects MAY have equal hash codes (collision)
3. Hash code must be consistent
4. equals() must be reflexive, symmetric, transitive, consistent

**Why It Matters:**
- Critical for HashMap, HashSet, Hashtable
- Affects TreeSet, TreeMap (with compareTo)
- Determines object equality in collections

**Best Practices:**
- Always override both together
- Use same fields in both methods
- Use Objects.equals() and Objects.hash()
- Make key classes immutable
- Follow the contract strictly

**Common Mistakes:**
- Overriding only one method
- Using different fields
- Not handling nulls
- Using mutable fields
- Always returning same hashCode

**Interview Tips:**
- Explain the contract clearly
- Give examples of what breaks
- Mention impact on collections
- Know how to implement correctly
- Understand collision handling

**Remember:** If you override equals(), you MUST override hashCode()!

This is one of the most fundamental concepts in Java and is asked in almost every interview. Master this and you'll handle any equals/hashCode question with confidence!