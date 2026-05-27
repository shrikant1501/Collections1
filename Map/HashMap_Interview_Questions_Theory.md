# HashMap Interview Questions - Complete Theory Guide

This comprehensive guide covers the 10 most important HashMap interview questions with detailed explanations, examples, and interview answer templates.

## Table of Contents
1. [How does HashMap work internally?](#1-how-does-hashmap-work-internally)
2. [Why are equals() and hashCode() important?](#2-why-are-equals-and-hashcode-important)
3. [What happens if two keys have same hashCode?](#3-what-happens-if-two-keys-have-same-hashcode)
4. [Why is HashMap O(1)?](#4-why-is-hashmap-o1)
5. [Difference between HashMap and Hashtable](#5-difference-between-hashmap-and-hashtable)
6. [Difference between HashMap and ConcurrentHashMap](#6-difference-between-hashmap-and-concurrenthashmap)
7. [Why are keys immutable in HashMap?](#7-why-are-keys-immutable-in-hashmap)
8. [Can mutable objects be keys?](#8-can-mutable-objects-be-keys)
9. [What is load factor?](#9-what-is-load-factor)
10. [What is rehashing?](#10-what-is-rehashing)
11. [Java 8 Treeification](#11-java-8-treeification)
12. [Why does HashMap allow one null key?](#12-why-does-hashmap-allow-one-null-key)

---

## Quick Reference Summary

### Most Critical Points to Remember

**HashMap Internals:**
- Uses Node<K,V>[] array of buckets
- hashCode() determines bucket using (n-1) & hash
- equals() finds exact key
- Java 8: Linked list → Tree after 8 entries (when capacity ≥ 64)

**equals() and hashCode():**
- Both must be overridden together
- Contract: if a.equals(b) is true, then a.hashCode() == b.hashCode()
- hashCode() finds bucket (fast)
- equals() finds exact key (accurate)
- Breaking contract causes bugs

**Collision:**
- Same bucket, different keys
- Handled by chaining (list/tree)
- Java 8 converts to tree at threshold 8

**Performance:**
- Average: O(1) with good hash distribution
- Worst: O(n) in Java 7, O(log n) in Java 8+
- Load factor 0.75 balances memory and speed

**Thread Safety:**
- HashMap: Not thread-safe
- Hashtable: Synchronized (legacy, slow)
- ConcurrentHashMap: Lock striping (modern, fast)

**Keys:**
- Should be immutable
- Mutable keys cause lost entries
- String, Integer are perfect keys

**Load Factor & Rehashing:**
- Default 0.75 (75% full triggers resize)
- Threshold = Capacity × Load Factor (e.g., 16 × 0.75 = 12)
- Rehashing redistributes all entries
- Capacity doubles on resize

**Null Key Handling:**
- Null key stored in bucket 0 (no hashCode calculation)
- Only one null key allowed
- Multiple null values allowed

---

For detailed explanations of each topic, please refer to the individual sections below. Each section includes:
- Detailed explanation
- Visual examples
- Common pitfalls
- Interview answer template

This guide is designed to help you ace HashMap-related interview questions with confidence.

## Detailed Explanations

Due to the comprehensive nature of this guide, detailed explanations for each of the 10 questions are provided in separate focused documents:

1. **HashMap Internal Working** - See `HashMap_Internal_Working.md`
2. **equals() and hashCode()** - See `HashMap_Equals_HashCode.md`
3. **Collision Handling** - See `../Set/Collision_Explanation_For_Interviews.md`
4. **Performance Analysis** - See `HashMap_Performance.md`
5. **Thread Safety Comparison** - See `HashMap_Thread_Safety.md`
6. **Immutability Requirements** - See `HashMap_Immutability.md`
7. **Load Factor and Rehashing** - See `HashMap_Load_Factor_Rehashing.md`

### Quick Interview Answers

**Q1: How does HashMap work internally?**
"HashMap internally uses a Node<K,V>[] array of buckets. When you put a key-value pair, it calculates the key's hashCode and converts it to a bucket index using (n-1) & hash, where n is the capacity. If multiple keys map to the same bucket (collision), it uses a linked list or tree (Java 8+) to store them. When retrieving, it uses hashCode to find the bucket, then equals() to find the exact key."

**Q2: Why are equals() and hashCode() important?**
"HashMap uses hashCode() to quickly find the bucket (O(1)), and equals() to find the exact key within that bucket. You need both because hashCode() alone can't guarantee uniqueness due to collisions, and equals() alone would be too slow (O(n)). They must be overridden together to maintain the contract: if two objects are equal according to equals(), they must return the same hashCode()."

**Q3: What happens if two keys have same hashCode?**
"It's called a collision. Both keys go to the same bucket. HashMap stores them in a linked list (or tree in Java 8+ after 8 entries). When searching, it uses equals() to differentiate between keys in the same bucket."

**Q4: Why is HashMap O(1)?**
"HashMap achieves O(1) through direct array access using hashing. The hashCode is converted to a bucket index, and arrays provide O(1) access. Good hash distribution minimizes collisions, helping maintain near O(1) average complexity. The load factor of 0.75 ensures the map resizes before becoming too dense, keeping performance optimal."

**Q5: Difference between HashMap and Hashtable?**
"Hashtable is synchronized (thread-safe but slow), doesn't allow null keys/values, and is legacy. HashMap is not synchronized (faster), allows one null key and multiple null values, and is modern. For thread-safety, use ConcurrentHashMap instead of Hashtable."

**Q6: Difference between HashMap and ConcurrentHashMap?**
"ConcurrentHashMap provides thread safety with fine-grained synchronization and CAS (Compare-And-Swap) operations, allowing high concurrency and better performance than Hashtable. HashMap is not thread-safe. ConcurrentHashMap doesn't allow null keys or values, has lock-free reads, and provides atomic operations like putIfAbsent, making it ideal for multi-threaded applications."

**Q7: Why are keys immutable in HashMap?**
"If a key changes after insertion, its hashCode changes, causing the entry to be in the wrong bucket. The entry becomes unreachable because get() looks in a different bucket. Immutable keys ensure hashCode never changes, keeping entries retrievable."

**Q8: Can mutable objects be keys?**
"Technically yes, but practically dangerous. If you modify a key after insertion, its hashCode changes, making the entry unreachable. This leads to lost entries and bugs. Best practice is to use immutable classes like String or Integer as keys."

**Q9: What is load factor?**
"Load factor (default 0.75) determines when HashMap resizes. It's the ratio of entries to capacity. The threshold is calculated as: Threshold = Capacity × Load Factor. For example, with capacity 16 and load factor 0.75, threshold is 12. When size reaches 12, HashMap doubles capacity and rehashes all entries. 0.75 balances memory usage and performance - lower means fewer collisions but more memory, higher means more collisions but less memory."

**Q10: What is rehashing?**
"Rehashing occurs when HashMap resizes. All existing entries are recalculated and redistributed across the new, larger bucket array. This maintains O(1) performance by keeping entries per bucket low. It's O(n) but happens infrequently, making it O(1) amortized."

---

## Interview Tips

### What Interviewers Look For

1. **Understanding of internals** - Not just API usage
2. **Trade-offs** - Memory vs performance, thread-safety vs speed
3. **Real-world implications** - Why immutability matters
4. **Problem-solving** - What causes O(n) performance
5. **Best practices** - When to use which implementation

### Common Follow-up Questions

- "What if all keys have the same hashCode?" → O(n) or O(log n) in Java 8+
- "How would you implement a thread-safe HashMap?" → ConcurrentHashMap or Collections.synchronizedMap()
- "What's the difference between capacity and size?" → Capacity is bucket array size, size is number of entries
- "Can you have duplicate values?" → Yes, only keys must be unique
- "What happens if you don't override hashCode()?" → Uses Object.hashCode() (memory address), breaks equality

### Red Flags to Avoid

❌ "HashMap is always O(1)" - Mention worst case
❌ "Hashtable is better for thread-safety" - ConcurrentHashMap is better
❌ "You can't use mutable objects as keys" - You can, but shouldn't
❌ "Load factor doesn't matter" - It's crucial for performance
❌ "Collision is a bug" - It's normal and expected

### Green Flags to Show

✅ Mention Java 8 treeification improvement
✅ Explain equals/hashCode contract
✅ Discuss trade-offs (memory vs performance)
✅ Know when to use ConcurrentHashMap
✅ Understand immutability importance

---

## Practice Questions

Test your understanding:

1. If HashMap capacity is 16 and load factor is 0.75, at what size does it resize?
   - Answer: 12 (16 × 0.75)

2. What's the time complexity of get() in worst case for Java 8?
   - Answer: O(log n) due to treeification

3. Can HashMap have multiple null values?
   - Answer: Yes, but only one null key

4. What happens if you modify a key after adding to HashMap?
   - Answer: Entry becomes unreachable, lost in wrong bucket

5. Why is ConcurrentHashMap better than Hashtable?
   - Answer: Fine-grained synchronization and CAS operations allow better concurrency, lock-free reads

6. What are the conditions for treeification in Java 8?
   - Answer: Bucket size > 8 AND table capacity ≥ 64

7. Where is the null key stored in HashMap?
   - Answer: Bucket 0 (index 0), since null has no hashCode

---

## Summary

**Master these concepts:**
- HashMap uses Node<K,V>[] array + linked list/tree
- Index calculation: (n-1) & hash
- hashCode() + equals() work together (contract: equals → same hashCode)
- Collisions are normal, handled efficiently
- O(1) average, O(log n) worst case (Java 8+)
- Treeification: bucket size > 8 AND capacity ≥ 64
- Use immutable keys
- Load factor 0.75 is optimal (Threshold = Capacity × Load Factor)
- Null key stored in bucket 0
- ConcurrentHashMap for thread-safety (fine-grained sync + CAS)

**Remember:**
- Explain step-by-step for "how it works"
- Mention Java 8 improvements
- Discuss trade-offs
- Give real-world examples
- Show understanding of internals

Good luck with your interviews!
**Q11: Java 8 Treeification (Advanced Topic)**
"In Java 8, when collisions increase in a bucket, HashMap converts the linked list to a balanced tree (Red-Black Tree) for better performance. This happens when: (1) bucket size exceeds 8 entries, AND (2) table capacity is at least 64. This improves worst-case search time from O(n) to O(log n). The tree converts back to a linked list when bucket size reduces to 6 or fewer entries. This is a significant performance improvement over Java 7."

**Q12: Why does HashMap allow one null key?**
"HashMap allows one null key because null is treated as a special case. Since null has no hashCode(), HashMap stores the null key in bucket 0 (index 0) by default. When you put or get with a null key, HashMap directly checks bucket 0 without calculating hashCode. This is why only one null key is allowed - multiple null keys would be duplicates. However, multiple null values are allowed because values don't need to be unique."
