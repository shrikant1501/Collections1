# Immutability in Java - Complete Guide

## Simple Explanation

### What is Immutability?

**Immutable** = Cannot be changed after creation

**Real-World Analogy:**
Think of immutability like:
- **Mutable Object** = Whiteboard (can erase and rewrite)
- **Immutable Object** = Printed book (cannot change content, must print new book)

**Example:**
```java
String str = "Hello";
str = str + " World";  // Doesn't modify original, creates new String

// Original "Hello" still exists in memory
// str now points to new "Hello World"
```

---

## Why Immutability?

### Benefits

1. **Thread-Safety** - No synchronization needed
2. **Caching** - Can safely cache and reuse
3. **Security** - Cannot be modified by malicious code
4. **Simplicity** - Easier to understand and debug
5. **HashMap Keys** - Safe to use as keys (hashCode won't change)

### Example: Thread-Safety

```java
// Mutable - Not thread-safe
class MutablePerson {
    private String name;
    
    public void setName(String name) {
        this.name = name;  // Can be modified by multiple threads
    }
}

// Immutable - Thread-safe
class ImmutablePerson {
    private final String name;
    
    public ImmutablePerson(String name) {
        this.name = name;  // Cannot be modified
    }
    
    public String getName() {
        return name;
    }
}
```

---

## Built-in Immutable Classes

### 1. String

```java
String str = "Hello";
str.concat(" World");  // Creates new String, doesn't modify original

System.out.println(str);  // Still "Hello"

String newStr = str.concat(" World");
System.out.println(newStr);  // "Hello World"
```

### 2. Wrapper Classes

```java
Integer num = 10;
num = num + 5;  // Creates new Integer(15), doesn't modify original

// Original Integer(10) still exists (if referenced elsewhere)
```

### 3. BigInteger and BigDecimal

```java
BigInteger big1 = new BigInteger("100");
BigInteger big2 = big1.add(new BigInteger("50"));  // Creates new object

System.out.println(big1);  // Still 100
System.out.println(big2);  // 150
```

### 4. LocalDate, LocalTime, LocalDateTime (Java 8+)

```java
LocalDate date = LocalDate.of(2024, 1, 1);
LocalDate newDate = date.plusDays(10);  // Creates new LocalDate

System.out.println(date);     // 2024-01-01
System.out.println(newDate);  // 2024-01-11
```

---

## Creating Immutable Classes

### Rules for Immutable Class

1. **Declare class as final** - Prevent subclassing
2. **Make all fields private and final** - Cannot be modified
3. **No setter methods** - Only getters
4. **Initialize all fields in constructor** - One-time initialization
5. **Deep copy mutable objects** - Prevent external modification
6. **Return copies of mutable objects** - Don't expose internal state

### Example 1: Simple Immutable Class

```java
public final class ImmutablePerson {
    private final String name;
    private final int age;
    
    public ImmutablePerson(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    // No setters!
}

// Usage
ImmutablePerson person = new ImmutablePerson("Alice", 25);
System.out.println(person.getName());  // Alice

// person.setAge(26);  // Compilation error - no setter!

// To "modify", create new object
ImmutablePerson olderPerson = new ImmutablePerson(person.getName(), 26);
```

### Example 2: Immutable Class with Mutable Field

```java
import java.util.Date;

// WRONG - Not truly immutable
public final class WrongImmutablePerson {
    private final String name;
    private final Date birthDate;
    
    public WrongImmutablePerson(String name, Date birthDate) {
        this.name = name;
        this.birthDate = birthDate;  // Direct reference!
    }
    
    public Date getBirthDate() {
        return birthDate;  // Exposes internal state!
    }
}

// Problem:
Date date = new Date();
WrongImmutablePerson person = new WrongImmutablePerson("Alice", date);

// Can modify through external reference!
date.setTime(0);  // Modifies person's birthDate!

// Or through getter
person.getBirthDate().setTime(0);  // Modifies internal state!
```

```java
// CORRECT - Truly immutable
public final class CorrectImmutablePerson {
    private final String name;
    private final Date birthDate;
    
    public CorrectImmutablePerson(String name, Date birthDate) {
        this.name = name;
        // Deep copy - create new Date
        this.birthDate = new Date(birthDate.getTime());
    }
    
    public Date getBirthDate() {
        // Return copy, not original
        return new Date(birthDate.getTime());
    }
}

// Now safe:
Date date = new Date();
CorrectImmutablePerson person = new CorrectImmutablePerson("Alice", date);

date.setTime(0);  // Doesn't affect person's birthDate
person.getBirthDate().setTime(0);  // Doesn't affect internal state
```

### Example 3: Immutable Class with Collection

```java
import java.util.*;

// WRONG - Not truly immutable
public final class WrongImmutableClass {
    private final List<String> items;
    
    public WrongImmutableClass(List<String> items) {
        this.items = items;  // Direct reference!
    }
    
    public List<String> getItems() {
        return items;  // Exposes internal state!
    }
}

// Problem:
List<String> list = new ArrayList<>(Arrays.asList("A", "B"));
WrongImmutableClass obj = new WrongImmutableClass(list);

list.add("C");  // Modifies internal state!
obj.getItems().add("D");  // Modifies internal state!
```

```java
// CORRECT - Truly immutable
public final class CorrectImmutableClass {
    private final List<String> items;
    
    public CorrectImmutableClass(List<String> items) {
        // Deep copy - create new list
        this.items = new ArrayList<>(items);
    }
    
    public List<String> getItems() {
        // Return unmodifiable view
        return Collections.unmodifiableList(items);
        
        // Or return copy
        // return new ArrayList<>(items);
    }
}

// Now safe:
List<String> list = new ArrayList<>(Arrays.asList("A", "B"));
CorrectImmutableClass obj = new CorrectImmutableClass(list);

list.add("C");  // Doesn't affect obj
// obj.getItems().add("D");  // UnsupportedOperationException!
```

### Example 4: Complete Immutable Class

```java
import java.util.*;

public final class ImmutableEmployee {
    private final String name;
    private final int id;
    private final Date joinDate;
    private final List<String> skills;
    
    public ImmutableEmployee(String name, int id, Date joinDate, List<String> skills) {
        this.name = name;
        this.id = id;
        // Deep copy Date
        this.joinDate = new Date(joinDate.getTime());
        // Deep copy List
        this.skills = new ArrayList<>(skills);
    }
    
    public String getName() {
        return name;
    }
    
    public int getId() {
        return id;
    }
    
    public Date getJoinDate() {
        // Return copy
        return new Date(joinDate.getTime());
    }
    
    public List<String> getSkills() {
        // Return unmodifiable view
        return Collections.unmodifiableList(skills);
    }
    
    @Override
    public String toString() {
        return "Employee{name='" + name + "', id=" + id + 
               ", joinDate=" + joinDate + ", skills=" + skills + "}";
    }
}

// Usage
Date date = new Date();
List<String> skills = new ArrayList<>(Arrays.asList("Java", "Python"));

ImmutableEmployee emp = new ImmutableEmployee("Alice", 101, date, skills);

// Safe - cannot modify
date.setTime(0);  // Doesn't affect emp
skills.add("C++");  // Doesn't affect emp
// emp.getSkills().add("Go");  // UnsupportedOperationException!
```

---

## Immutable Collections (Java 9+)

### List.of(), Set.of(), Map.of()

```java
// Immutable List
List<String> list = List.of("A", "B", "C");
// list.add("D");  // UnsupportedOperationException!

// Immutable Set
Set<Integer> set = Set.of(1, 2, 3);
// set.add(4);  // UnsupportedOperationException!

// Immutable Map
Map<String, Integer> map = Map.of("A", 1, "B", 2, "C", 3);
// map.put("D", 4);  // UnsupportedOperationException!

// For more than 10 entries
Map<String, Integer> largeMap = Map.ofEntries(
    Map.entry("A", 1),
    Map.entry("B", 2),
    Map.entry("C", 3)
    // ... more entries
);
```

### Collections.unmodifiableXxx() (Before Java 9)

```java
List<String> mutableList = new ArrayList<>(Arrays.asList("A", "B", "C"));
List<String> immutableList = Collections.unmodifiableList(mutableList);

// immutableList.add("D");  // UnsupportedOperationException!

// But original list can still be modified
mutableList.add("D");
System.out.println(immutableList);  // [A, B, C, D]
// Changes to original are reflected!
```

### Difference: List.of() vs Collections.unmodifiableList()

```java
// List.of() - Truly immutable
List<String> list1 = List.of("A", "B", "C");
// No way to modify

// Collections.unmodifiableList() - View of mutable list
List<String> original = new ArrayList<>(Arrays.asList("A", "B", "C"));
List<String> list2 = Collections.unmodifiableList(original);

original.add("D");  // Can still modify original
System.out.println(list2);  // [A, B, C, D] - reflects changes!
```

---

## Immutability Patterns

### 1. Builder Pattern for Immutable Objects

```java
public final class Person {
    private final String firstName;
    private final String lastName;
    private final int age;
    private final String email;
    
    private Person(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.email = builder.email;
    }
    
    // Getters
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public int getAge() { return age; }
    public String getEmail() { return email; }
    
    // Builder
    public static class Builder {
        private String firstName;
        private String lastName;
        private int age;
        private String email;
        
        public Builder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }
        
        public Builder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }
        
        public Builder age(int age) {
            this.age = age;
            return this;
        }
        
        public Builder email(String email) {
            this.email = email;
            return this;
        }
        
        public Person build() {
            return new Person(this);
        }
    }
}

// Usage
Person person = new Person.Builder()
    .firstName("John")
    .lastName("Doe")
    .age(30)
    .email("john@example.com")
    .build();
```

### 2. Wither Methods (Copy with Modification)

```java
public final class Person {
    private final String name;
    private final int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    
    // Wither methods - return new instance with modified field
    public Person withName(String name) {
        return new Person(name, this.age);
    }
    
    public Person withAge(int age) {
        return new Person(this.name, age);
    }
}

// Usage
Person person1 = new Person("Alice", 25);
Person person2 = person1.withAge(26);  // New object with age 26

System.out.println(person1.getAge());  // 25 (unchanged)
System.out.println(person2.getAge());  // 26
```

### 3. Records (Java 14+)

```java
// Automatically immutable!
public record Person(String name, int age) {
    // Compact constructor for validation
    public Person {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}

// Usage
Person person = new Person("Alice", 25);
System.out.println(person.name());  // Alice
System.out.println(person.age());   // 25

// Automatically provides:
// - Constructor
// - Getters (name(), age())
// - equals(), hashCode(), toString()
// - All fields are final
// - Class is final
```

---

## String Immutability

### Why String is Immutable

1. **String Pool** - Allows string interning for memory efficiency
2. **Security** - Strings used in security contexts (passwords, URLs)
3. **Thread-Safety** - No synchronization needed
4. **Caching** - hashCode can be cached

### String Pool Example

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = new String("Hello");

System.out.println(s1 == s2);  // true (same object in pool)
System.out.println(s1 == s3);  // false (different objects)
System.out.println(s1.equals(s3));  // true (same content)

// Intern - add to pool
String s4 = s3.intern();
System.out.println(s1 == s4);  // true (now same object)
```

### String Operations Create New Objects

```java
String str = "Hello";
System.out.println(System.identityHashCode(str));  // e.g., 123456

str = str.concat(" World");
System.out.println(System.identityHashCode(str));  // e.g., 789012 (different!)

// Original "Hello" still exists in memory (if referenced)
```

### StringBuilder vs String

```java
// Inefficient - Creates many String objects
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // Creates new String each time!
}

// Efficient - Mutable, single object
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);  // Modifies same object
}
String result = sb.toString();
```

---

## Common Pitfalls

### Pitfall 1: Forgetting to Make Class Final

```java
// WRONG - Can be subclassed
public class ImmutablePerson {
    private final String name;
    
    public ImmutablePerson(String name) {
        this.name = name;
    }
    
    public String getName() {
        return name;
    }
}

// Subclass can add mutable state!
class MutablePerson extends ImmutablePerson {
    private int age;  // Mutable!
    
    public MutablePerson(String name, int age) {
        super(name);
        this.age = age;
    }
    
    public void setAge(int age) {
        this.age = age;
    }
}
```

### Pitfall 2: Not Deep Copying Mutable Objects

```java
// WRONG
public final class Person {
    private final Date birthDate;
    
    public Person(Date birthDate) {
        this.birthDate = birthDate;  // Direct reference!
    }
    
    public Date getBirthDate() {
        return birthDate;  // Exposes internal state!
    }
}

// Can be modified externally
Date date = new Date();
Person person = new Person(date);
date.setTime(0);  // Modifies person's birthDate!
```

### Pitfall 3: Exposing Mutable Collections

```java
// WRONG
public final class Team {
    private final List<String> members;
    
    public Team(List<String> members) {
        this.members = new ArrayList<>(members);  // Good - copy
    }
    
    public List<String> getMembers() {
        return members;  // BAD - exposes internal state!
    }
}

// Can be modified
Team team = new Team(Arrays.asList("Alice", "Bob"));
team.getMembers().add("Charlie");  // Modifies internal state!
```

### Pitfall 4: Using Mutable Static Fields

```java
// WRONG - Static field is mutable
public final class Config {
    private static List<String> settings = new ArrayList<>();  // Mutable!
    
    public static void addSetting(String setting) {
        settings.add(setting);  // Can be modified!
    }
}
```

---

## Real-World Examples

### Example 1: Money Class

```java
import java.math.BigDecimal;

public final class Money {
    private final BigDecimal amount;
    private final String currency;
    
    public Money(BigDecimal amount, String currency) {
        if (amount == null || currency == null) {
            throw new IllegalArgumentException("Amount and currency cannot be null");
        }
        this.amount = amount;
        this.currency = currency;
    }
    
    public BigDecimal getAmount() {
        return amount;
    }
    
    public String getCurrency() {
        return currency;
    }
    
    public Money add(Money other) {
        if (!this.currency.equals(other.currency)) {
            throw new IllegalArgumentException("Cannot add different currencies");
        }
        return new Money(this.amount.add(other.amount), this.currency);
    }
    
    public Money subtract(Money other) {
        if (!this.currency.equals(other.currency)) {
            throw new IllegalArgumentException("Cannot subtract different currencies");
        }
        return new Money(this.amount.subtract(other.amount), this.currency);
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Money)) return false;
        Money money = (Money) o;
        return amount.equals(money.amount) && currency.equals(money.currency);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(amount, currency);
    }
    
    @Override
    public String toString() {
        return amount + " " + currency;
    }
}

// Usage
Money price1 = new Money(new BigDecimal("100.00"), "USD");
Money price2 = new Money(new BigDecimal("50.00"), "USD");

Money total = price1.add(price2);
System.out.println(total);  // 150.00 USD

// Original objects unchanged
System.out.println(price1);  // 100.00 USD
System.out.println(price2);  // 50.00 USD
```

### Example 2: Address Class

```java
public final class Address {
    private final String street;
    private final String city;
    private final String state;
    private final String zipCode;
    
    public Address(String street, String city, String state, String zipCode) {
        this.street = street;
        this.city = city;
        this.state = state;
        this.zipCode = zipCode;
    }
    
    // Getters
    public String getStreet() { return street; }
    public String getCity() { return city; }
    public String getState() { return state; }
    public String getZipCode() { return zipCode; }
    
    // Wither methods
    public Address withStreet(String street) {
        return new Address(street, this.city, this.state, this.zipCode);
    }
    
    public Address withCity(String city) {
        return new Address(this.street, city, this.state, this.zipCode);
    }
    
    public Address withState(String state) {
        return new Address(this.street, this.city, state, this.zipCode);
    }
    
    public Address withZipCode(String zipCode) {
        return new Address(this.street, this.city, this.state, zipCode);
    }
    
    @Override
    public String toString() {
        return street + ", " + city + ", " + state + " " + zipCode;
    }
}

// Usage
Address address1 = new Address("123 Main St", "New York", "NY", "10001");
Address address2 = address1.withCity("Boston").withState("MA").withZipCode("02101");

System.out.println(address1);  // 123 Main St, New York, NY 10001
System.out.println(address2);  // 123 Main St, Boston, MA 02101
```

### Example 3: Configuration Class

```java
import java.util.*;

public final class Configuration {
    private final Map<String, String> properties;
    
    public Configuration(Map<String, String> properties) {
        // Deep copy
        this.properties = new HashMap<>(properties);
    }
    
    public String getProperty(String key) {
        return properties.get(key);
    }
    
    public String getProperty(String key, String defaultValue) {
        return properties.getOrDefault(key, defaultValue);
    }
    
    public Map<String, String> getAllProperties() {
        // Return unmodifiable view
        return Collections.unmodifiableMap(properties);
    }
    
    public Configuration withProperty(String key, String value) {
        Map<String, String> newProps = new HashMap<>(this.properties);
        newProps.put(key, value);
        return new Configuration(newProps);
    }
    
    public Configuration withoutProperty(String key) {
        Map<String, String> newProps = new HashMap<>(this.properties);
        newProps.remove(key);
        return new Configuration(newProps);
    }
}

// Usage
Map<String, String> props = new HashMap<>();
props.put("host", "localhost");
props.put("port", "8080");

Configuration config1 = new Configuration(props);
Configuration config2 = config1.withProperty("port", "9090");

System.out.println(config1.getProperty("port"));  // 8080
System.out.println(config2.getProperty("port"));  // 9090
```

---

## Common Interview Questions

### Q1: What is immutability and why is it important?

**Answer:**
"Immutability means an object's state cannot be changed after creation. It's important because:

1. **Thread-Safety**: Immutable objects are inherently thread-safe - no synchronization needed
2. **Caching**: Can safely cache and reuse immutable objects
3. **HashMap Keys**: Safe to use as keys because hashCode won't change
4. **Security**: Cannot be modified by malicious code
5. **Simplicity**: Easier to understand and debug

Example:
```java
String str = "Hello";
str.concat(" World");  // Creates new String, doesn't modify original
System.out.println(str);  // Still "Hello"
```

Immutable objects are particularly useful in multi-threaded environments and functional programming."

### Q2: How do you create an immutable class in Java?

**Answer:**
"To create an immutable class, follow these rules:

1. **Declare class as final** - Prevent subclassing
2. **Make all fields private and final**
3. **No setter methods**
4. **Initialize all fields in constructor**
5. **Deep copy mutable objects** in constructor
6. **Return copies of mutable objects** from getters

Example:
```java
public final class ImmutablePerson {
    private final String name;
    private final Date birthDate;
    private final List<String> hobbies;
    
    public ImmutablePerson(String name, Date birthDate, List<String> hobbies) {
        this.name = name;
        this.birthDate = new Date(birthDate.getTime());  // Deep copy
        this.hobbies = new ArrayList<>(hobbies);  // Deep copy
    }
    
    public String getName() {
        return name;
    }
    
    public Date getBirthDate() {
        return new Date(birthDate.getTime());  // Return copy
    }
    
    public List<String> getHobbies() {
        return Collections.unmodifiableList(hobbies);  // Unmodifiable view
    }
}
```"

### Q3: Why is String immutable in Java?

**Answer:**
"String is immutable for several important reasons:

1. **String Pool**: Allows string interning for memory efficiency. Multiple references can point to the same String object in the pool.

2. **Security**: Strings are used in security-sensitive contexts (passwords, file paths, URLs). Immutability prevents malicious modification.

3. **Thread-Safety**: No synchronization needed when sharing strings between threads.

4. **Caching**: hashCode can be cached since it won't change. Important for HashMap/HashSet performance.

5. **Class Loading**: Class names are strings. If mutable, could cause security issues.

Example of String Pool:
```java
String s1 = "Hello";
String s2 = "Hello";
System.out.println(s1 == s2);  // true (same object in pool)
```

If String were mutable:
```java
String s1 = "Hello";
String s2 = s1;  // Both point to same object
s1.setValue("World");  // If this were possible
System.out.println(s2);  // Would print "World" - unexpected!
```"

### Q4: What's the difference between immutable and unmodifiable?

**Answer:**
"**Immutable** means the object's state cannot be changed at all. **Unmodifiable** means you cannot modify through a particular reference, but the underlying object might still be mutable.

Example:
```java
// Immutable - Cannot be changed at all
List<String> immutable = List.of("A", "B", "C");
// No way to modify

// Unmodifiable - View of mutable list
List<String> original = new ArrayList<>(Arrays.asList("A", "B", "C"));
List<String> unmodifiable = Collections.unmodifiableList(original);

// Cannot modify through unmodifiable reference
// unmodifiable.add("D");  // UnsupportedOperationException

// But can still modify through original reference
original.add("D");
System.out.println(unmodifiable);  // [A, B, C, D] - reflects changes!
```

Key difference:
- Immutable: Truly unchangeable
- Unmodifiable: Just a read-only view"

### Q5: Can an immutable class have mutable fields?

**Answer:**
"Technically yes, but it defeats the purpose of immutability. If you must have mutable fields, you need to:

1. **Deep copy in constructor** - Don't store direct reference
2. **Return copies from getters** - Don't expose internal state

Example:
```java
public final class Person {
    private final Date birthDate;  // Date is mutable
    
    public Person(Date birthDate) {
        // Deep copy - create new Date
        this.birthDate = new Date(birthDate.getTime());
    }
    
    public Date getBirthDate() {
        // Return copy, not original
        return new Date(birthDate.getTime());
    }
}
```

However, it's better to use immutable types:
```java
public final class Person {
    private final LocalDate birthDate;  // LocalDate is immutable
    
    public Person(LocalDate birthDate) {
        this.birthDate = birthDate;  // No copy needed
    }
    
    public LocalDate getBirthDate() {
        return birthDate;  // Safe to return directly
    }
}
```"

### Q6: What are the disadvantages of immutability?

**Answer:**
"While immutability has many benefits, it also has some drawbacks:

1. **Memory Overhead**: Every modification creates a new object
```java
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // Creates 1000 String objects!
}
```

2. **Performance**: Object creation and garbage collection overhead
```java
// Inefficient
String s = "Hello";
s = s + " World";  // Creates new String

// Better for frequent modifications
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");  // Modifies same object
```

3. **Complexity**: More code to create and maintain immutable classes

4. **Not Always Necessary**: For objects with short lifecycle or single-threaded scenarios

**When to avoid immutability:**
- Frequent modifications needed
- Large objects (expensive to copy)
- Performance-critical code
- Single-threaded scenarios where thread-safety isn't needed"

### Q7: How does immutability help with thread-safety?

**Answer:**
"Immutable objects are inherently thread-safe because:

1. **No State Changes**: State cannot be modified, so no race conditions
2. **No Synchronization Needed**: Multiple threads can safely read without locks
3. **Safe Publication**: Can be safely shared between threads without defensive copying

Example:
```java
// Mutable - Not thread-safe
class MutableCounter {
    private int count = 0;
    
    public void increment() {
        count++;  // Race condition!
    }
    
    public int getCount() {
        return count;
    }
}

// Immutable - Thread-safe
class ImmutableCounter {
    private final int count;
    
    public ImmutableCounter(int count) {
        this.count = count;
    }
    
    public ImmutableCounter increment() {
        return new ImmutableCounter(count + 1);  // New object
    }
    
    public int getCount() {
        return count;
    }
}
```

With immutable objects:
- No need for synchronized blocks
- No need for volatile keyword
- No need for locks
- Simpler concurrent code"

### Q8: What is defensive copying and when is it needed?

**Answer:**
"Defensive copying means creating a copy of an object to prevent external modification of internal state. It's needed when:

1. **Constructor receives mutable object**
2. **Getter returns mutable object**

Example without defensive copying:
```java
// WRONG
public final class Person {
    private final Date birthDate;
    
    public Person(Date birthDate) {
        this.birthDate = birthDate;  // Direct reference!
    }
    
    public Date getBirthDate() {
        return birthDate;  // Exposes internal state!
    }
}

// Can be modified externally
Date date = new Date();
Person person = new Person(date);
date.setTime(0);  // Modifies person's birthDate!
person.getBirthDate().setTime(0);  // Modifies internal state!
```

Example with defensive copying:
```java
// CORRECT
public final class Person {
    private final Date birthDate;
    
    public Person(Date birthDate) {
        this.birthDate = new Date(birthDate.getTime());  // Copy
    }
    
    public Date getBirthDate() {
        return new Date(birthDate.getTime());  // Copy
    }
}

// Now safe
Date date = new Date();
Person person = new Person(date);
date.setTime(0);  // Doesn't affect person
person.getBirthDate().setTime(0);  // Doesn't affect internal state
```"

### Q9: What are Records in Java and how do they relate to immutability?

**Answer:**
"Records (Java 14+) are a special kind of class designed for immutable data carriers. They automatically provide:

1. **Final class** - Cannot be extended
2. **Private final fields** - Cannot be modified
3. **Constructor** - Initializes all fields
4. **Getters** - Named after fields (no 'get' prefix)
5. **equals(), hashCode(), toString()** - Based on all fields

Example:
```java
// Traditional immutable class
public final class PersonOld {
    private final String name;
    private final int age;
    
    public PersonOld(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    
    @Override
    public boolean equals(Object o) { /* ... */ }
    @Override
    public int hashCode() { /* ... */ }
    @Override
    public String toString() { /* ... */ }
}

// Record - Much simpler!
public record Person(String name, int age) {
    // Compact constructor for validation
    public Person {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}

// Usage
Person person = new Person("Alice", 25);
System.out.println(person.name());  // Alice (not getName())
System.out.println(person.age());   // 25
```

Records are perfect for DTOs, value objects, and any immutable data structure."

### Q10: How do you handle collections in immutable classes?

**Answer:**
"When dealing with collections in immutable classes, you must:

1. **Deep copy in constructor**
2. **Return unmodifiable view or copy from getter**

Example:
```java
public final class Team {
    private final List<String> members;
    
    public Team(List<String> members) {
        // Deep copy - create new list
        this.members = new ArrayList<>(members);
    }
    
    public List<String> getMembers() {
        // Option 1: Return unmodifiable view
        return Collections.unmodifiableList(members);
        
        // Option 2: Return copy
        // return new ArrayList<>(members);
    }
}

// Usage
List<String> list = new ArrayList<>(Arrays.asList("Alice", "Bob"));
Team team = new Team(list);

// Safe - cannot modify
list.add("Charlie");  // Doesn't affect team
// team.getMembers().add("David");  // UnsupportedOperationException!
```

For Java 9+, use immutable collections:
```java
public final class Team {
    private final List<String> members;
    
    public Team(List<String> members) {
        // Create immutable copy
        this.members = List.copyOf(members);
    }
    
    public List<String> getMembers() {
        return members;  // Already immutable, safe to return
    }
}
```"

---

## Best Practices

### 1. Prefer Immutability by Default

```java
// Good - Immutable by default
public final class Point {
    private final int x;
    private final int y;
    
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

// Only make mutable if necessary
```

### 2. Use Immutable Collections (Java 9+)

```java
// Good
List<String> list = List.of("A", "B", "C");
Set<Integer> set = Set.of(1, 2, 3);
Map<String, Integer> map = Map.of("A", 1, "B", 2);

// Avoid
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
```

### 3. Use Records for Simple Data Classes

```java
// Good - Concise and immutable
public record Person(String name, int age) {}

// Avoid - Too much boilerplate
public final class Person {
    private final String name;
    private final int age;
    // ... constructor, getters, equals, hashCode, toString
}
```

### 4. Use Builder Pattern for Complex Objects

```java
// Good - Readable and flexible
Person person = new Person.Builder()
    .name("John")
    .age(30)
    .email("john@example.com")
    .build();

// Avoid - Too many constructor parameters
Person person = new Person("John", "Doe", 30, "john@example.com", 
                          "123 Main St", "New York", "NY", "10001");
```

### 5. Use Wither Methods for Modifications

```java
// Good - Clear intent
Person olderPerson = person.withAge(31);

// Avoid - Creating new object manually
Person olderPerson = new Person(person.getName(), 31, person.getEmail());
```

---

## Summary

**Immutability:**
- ✅ Object state cannot be changed after creation
- ✅ Thread-safe without synchronization
- ✅ Safe to use as HashMap keys
- ✅ Can be safely cached and shared
- ✅ Simpler to understand and debug

**Creating Immutable Classes:**
1. Declare class as final
2. Make all fields private and final
3. No setter methods
4. Deep copy mutable objects
5. Return copies or unmodifiable views

**Built-in Immutable Classes:**
- String, Integer, Long, etc.
- BigInteger, BigDecimal
- LocalDate, LocalTime, LocalDateTime
- List.of(), Set.of(), Map.of() (Java 9+)

**Patterns:**
- Builder Pattern for complex objects
- Wither Methods for modifications
- Records for simple data classes (Java 14+)

**Trade-offs:**
- ✅ Thread-safety, simplicity, safety
- ❌ Memory overhead, performance cost

**Interview Tips:**
- Explain benefits of immutability
- Know how to create immutable classes
- Understand defensive copying
- Know difference between immutable and unmodifiable
- Understand String immutability
- Know when to use Records

Master these concepts and you'll handle any immutability question with confidence!