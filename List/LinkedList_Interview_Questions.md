# LinkedList - Interview Questions & Examples

## Table of Contents
1. [LinkedList Basics](#linkedlist-basics)
2. [Common Interview Problems](#common-interview-problems)
3. [LinkedList vs ArrayList](#linkedlist-vs-arraylist)
4. [Implementation Questions](#implementation-questions)
5. [Advanced Problems](#advanced-problems)

---

## 1. LinkedList Basics

### What is LinkedList?

LinkedList is a doubly-linked list implementation of List and Deque interfaces.

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, Serializable
```

### Internal Structure

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
    
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}

// LinkedList fields
transient int size = 0;
transient Node<E> first;
transient Node<E> last;
```

### Basic Operations

```java
LinkedList<String> list = new LinkedList<>();

// Add elements
list.add("A");              // Add at end
list.addFirst("Z");         // Add at beginning
list.addLast("B");          // Add at end
list.add(1, "M");           // Add at index

// Access elements
String first = list.getFirst();
String last = list.getLast();
String atIndex = list.get(2);

// Remove elements
list.removeFirst();
list.removeLast();
list.remove(1);

// Queue operations
list.offer("X");            // Add to end
String head = list.poll();  // Remove from beginning
String peek = list.peek();  // View first element

// Stack operations
list.push("Y");             // Add to beginning
String pop = list.pop();    // Remove from beginning
```

---

## 2. Common Interview Problems

### Problem 1: Reverse a LinkedList

**Question:** Reverse a LinkedList in-place.

```java
public class ReverseLinkedList {
    
    // Method 1: Using Java Collections
    public static void reverseUsingCollections(LinkedList<Integer> list) {
        Collections.reverse(list);
    }
    
    // Method 2: Manual reversal using Deque operations
    public static LinkedList<Integer> reverseManually(LinkedList<Integer> list) {
        LinkedList<Integer> reversed = new LinkedList<>();
        for (Integer item : list) {
            reversed.addFirst(item);  // Add each element at beginning
        }
        return reversed;
    }
    
    // Method 3: Using Stack
    public static LinkedList<Integer> reverseUsingStack(LinkedList<Integer> list) {
        Stack<Integer> stack = new Stack<>();
        
        // Push all elements to stack
        for (Integer item : list) {
            stack.push(item);
        }
        
        // Pop and create new list
        LinkedList<Integer> reversed = new LinkedList<>();
        while (!stack.isEmpty()) {
            reversed.add(stack.pop());
        }
        
        return reversed;
    }
    
    // Method 4: Using ListIterator
    public static void reverseUsingIterator(LinkedList<Integer> list) {
        ListIterator<Integer> iterator = list.listIterator();
        LinkedList<Integer> temp = new LinkedList<>();
        
        while (iterator.hasNext()) {
            temp.addFirst(iterator.next());
        }
        
        list.clear();
        list.addAll(temp);
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(Arrays.asList(1, 2, 3, 4, 5));
        System.out.println("Original: " + list);
        
        reverseUsingCollections(list);
        System.out.println("Reversed: " + list);
        // Output: [5, 4, 3, 2, 1]
    }
}
```

### Problem 2: Find Middle Element

**Question:** Find the middle element of a LinkedList.

```java
public class FindMiddleElement {
    
    // Method 1: Using size
    public static <T> T findMiddleUsingSize(LinkedList<T> list) {
        if (list.isEmpty()) {
            return null;
        }
        
        int middle = list.size() / 2;
        return list.get(middle);
    }
    
    // Method 2: Two pointer approach (slow and fast)
    public static <T> T findMiddleTwoPointer(LinkedList<T> list) {
        if (list.isEmpty()) {
            return null;
        }
        
        Iterator<T> slow = list.iterator();
        Iterator<T> fast = list.iterator();
        
        T middle = slow.next();
        
        while (fast.hasNext()) {
            fast.next();
            if (fast.hasNext()) {
                fast.next();
                middle = slow.next();
            }
        }
        
        return middle;
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(Arrays.asList(1, 2, 3, 4, 5));
        System.out.println("Middle element: " + findMiddleUsingSize(list));
        // Output: 3
        
        LinkedList<Integer> evenList = new LinkedList<>(Arrays.asList(1, 2, 3, 4, 5, 6));
        System.out.println("Middle element: " + findMiddleUsingSize(evenList));
        // Output: 4
    }
}
```

### Problem 3: Detect and Remove Duplicates

**Question:** Remove duplicate elements from a LinkedList.

```java
public class RemoveDuplicates {
    
    // Method 1: Using HashSet (preserves order)
    public static <T> void removeDuplicatesUsingSet(LinkedList<T> list) {
        Set<T> seen = new HashSet<>();
        Iterator<T> iterator = list.iterator();
        
        while (iterator.hasNext()) {
            T element = iterator.next();
            if (!seen.add(element)) {
                iterator.remove();  // Duplicate found, remove it
            }
        }
    }
    
    // Method 2: Using Stream (Java 8+)
    public static <T> LinkedList<T> removeDuplicatesUsingStream(LinkedList<T> list) {
        return list.stream()
                   .distinct()
                   .collect(Collectors.toCollection(LinkedList::new));
    }
    
    // Method 3: Nested loop (O(n²) - not recommended for large lists)
    public static <T> void removeDuplicatesNestedLoop(LinkedList<T> list) {
        for (int i = 0; i < list.size(); i++) {
            T current = list.get(i);
            for (int j = i + 1; j < list.size(); j++) {
                if (current.equals(list.get(j))) {
                    list.remove(j);
                    j--;  // Adjust index after removal
                }
            }
        }
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(
            Arrays.asList(1, 2, 3, 2, 4, 1, 5, 3)
        );
        System.out.println("Original: " + list);
        
        removeDuplicatesUsingSet(list);
        System.out.println("After removing duplicates: " + list);
        // Output: [1, 2, 3, 4, 5]
    }
}
```

### Problem 4: Check if LinkedList is Palindrome

**Question:** Check if a LinkedList is a palindrome.

```java
public class PalindromeCheck {
    
    // Method 1: Using Stack
    public static <T> boolean isPalindromeUsingStack(LinkedList<T> list) {
        Stack<T> stack = new Stack<>();
        
        // Push all elements to stack
        for (T item : list) {
            stack.push(item);
        }
        
        // Compare with original list
        for (T item : list) {
            if (!item.equals(stack.pop())) {
                return false;
            }
        }
        
        return true;
    }
    
    // Method 2: Using two pointers
    public static <T> boolean isPalindromeTwoPointers(LinkedList<T> list) {
        int left = 0;
        int right = list.size() - 1;
        
        while (left < right) {
            if (!list.get(left).equals(list.get(right))) {
                return false;
            }
            left++;
            right--;
        }
        
        return true;
    }
    
    // Method 3: Compare with reversed list
    public static <T> boolean isPalindromeReverse(LinkedList<T> list) {
        LinkedList<T> reversed = new LinkedList<>(list);
        Collections.reverse(reversed);
        return list.equals(reversed);
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> palindrome = new LinkedList<>(Arrays.asList(1, 2, 3, 2, 1));
        LinkedList<Integer> notPalindrome = new LinkedList<>(Arrays.asList(1, 2, 3, 4, 5));
        
        System.out.println("Is palindrome: " + isPalindromeUsingStack(palindrome));
        // Output: true
        
        System.out.println("Is palindrome: " + isPalindromeUsingStack(notPalindrome));
        // Output: false
    }
}
```

### Problem 5: Merge Two Sorted LinkedLists

**Question:** Merge two sorted LinkedLists into one sorted LinkedList.

```java
public class MergeSortedLists {
    
    public static LinkedList<Integer> mergeSorted(
        LinkedList<Integer> list1, 
        LinkedList<Integer> list2
    ) {
        LinkedList<Integer> merged = new LinkedList<>();
        
        Iterator<Integer> iter1 = list1.iterator();
        Iterator<Integer> iter2 = list2.iterator();
        
        Integer val1 = iter1.hasNext() ? iter1.next() : null;
        Integer val2 = iter2.hasNext() ? iter2.next() : null;
        
        while (val1 != null && val2 != null) {
            if (val1 <= val2) {
                merged.add(val1);
                val1 = iter1.hasNext() ? iter1.next() : null;
            } else {
                merged.add(val2);
                val2 = iter2.hasNext() ? iter2.next() : null;
            }
        }
        
        // Add remaining elements
        while (val1 != null) {
            merged.add(val1);
            val1 = iter1.hasNext() ? iter1.next() : null;
        }
        
        while (val2 != null) {
            merged.add(val2);
            val2 = iter2.hasNext() ? iter2.next() : null;
        }
        
        return merged;
    }
    
    // Alternative: Using addAll and sort
    public static LinkedList<Integer> mergeAndSort(
        LinkedList<Integer> list1, 
        LinkedList<Integer> list2
    ) {
        LinkedList<Integer> merged = new LinkedList<>(list1);
        merged.addAll(list2);
        Collections.sort(merged);
        return merged;
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> list1 = new LinkedList<>(Arrays.asList(1, 3, 5, 7));
        LinkedList<Integer> list2 = new LinkedList<>(Arrays.asList(2, 4, 6, 8));
        
        LinkedList<Integer> merged = mergeSorted(list1, list2);
        System.out.println("Merged: " + merged);
        // Output: [1, 2, 3, 4, 5, 6, 7, 8]
    }
}
```

### Problem 6: Find Nth Node from End

**Question:** Find the Nth node from the end of a LinkedList.

```java
public class NthFromEnd {
    
    // Method 1: Using size
    public static <T> T findNthFromEnd(LinkedList<T> list, int n) {
        if (n <= 0 || n > list.size()) {
            throw new IllegalArgumentException("Invalid position");
        }
        
        int index = list.size() - n;
        return list.get(index);
    }
    
    // Method 2: Two pointer approach (more efficient for custom LinkedList)
    public static <T> T findNthFromEndTwoPointer(LinkedList<T> list, int n) {
        if (n <= 0 || n > list.size()) {
            throw new IllegalArgumentException("Invalid position");
        }
        
        Iterator<T> fast = list.iterator();
        Iterator<T> slow = list.iterator();
        
        // Move fast pointer n steps ahead
        for (int i = 0; i < n; i++) {
            if (!fast.hasNext()) {
                throw new IllegalArgumentException("List too short");
            }
            fast.next();
        }
        
        // Move both pointers until fast reaches end
        T result = slow.next();
        while (fast.hasNext()) {
            fast.next();
            result = slow.next();
        }
        
        return result;
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(Arrays.asList(1, 2, 3, 4, 5, 6, 7));
        
        System.out.println("3rd from end: " + findNthFromEnd(list, 3));
        // Output: 5
        
        System.out.println("1st from end: " + findNthFromEnd(list, 1));
        // Output: 7
    }
}
```

### Problem 7: Rotate LinkedList

**Question:** Rotate a LinkedList by k positions.

```java
public class RotateLinkedList {
    
    // Rotate right by k positions
    public static <T> void rotateRight(LinkedList<T> list, int k) {
        if (list.isEmpty() || k == 0) {
            return;
        }
        
        k = k % list.size();  // Handle k > size
        
        for (int i = 0; i < k; i++) {
            T last = list.removeLast();
            list.addFirst(last);
        }
    }
    
    // Rotate left by k positions
    public static <T> void rotateLeft(LinkedList<T> list, int k) {
        if (list.isEmpty() || k == 0) {
            return;
        }
        
        k = k % list.size();  // Handle k > size
        
        for (int i = 0; i < k; i++) {
            T first = list.removeFirst();
            list.addLast(first);
        }
    }
    
    // Efficient rotation using subList
    public static <T> LinkedList<T> rotateRightEfficient(LinkedList<T> list, int k) {
        if (list.isEmpty() || k == 0) {
            return new LinkedList<>(list);
        }
        
        k = k % list.size();
        int splitPoint = list.size() - k;
        
        LinkedList<T> rotated = new LinkedList<>();
        rotated.addAll(list.subList(splitPoint, list.size()));
        rotated.addAll(list.subList(0, splitPoint));
        
        return rotated;
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(Arrays.asList(1, 2, 3, 4, 5));
        System.out.println("Original: " + list);
        
        rotateRight(list, 2);
        System.out.println("Rotated right by 2: " + list);
        // Output: [4, 5, 1, 2, 3]
        
        list = new LinkedList<>(Arrays.asList(1, 2, 3, 4, 5));
        rotateLeft(list, 2);
        System.out.println("Rotated left by 2: " + list);
        // Output: [3, 4, 5, 1, 2]
    }
}
```

### Problem 8: Partition LinkedList

**Question:** Partition a LinkedList around a value x, such that all nodes less than x come before nodes greater than or equal to x.

```java
public class PartitionLinkedList {
    
    public static LinkedList<Integer> partition(LinkedList<Integer> list, int x) {
        LinkedList<Integer> less = new LinkedList<>();
        LinkedList<Integer> greaterOrEqual = new LinkedList<>();
        
        for (Integer value : list) {
            if (value < x) {
                less.add(value);
            } else {
                greaterOrEqual.add(value);
            }
        }
        
        less.addAll(greaterOrEqual);
        return less;
    }
    
    // In-place partition
    public static void partitionInPlace(LinkedList<Integer> list, int x) {
        LinkedList<Integer> partitioned = partition(list, x);
        list.clear();
        list.addAll(partitioned);
    }
    
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(Arrays.asList(3, 5, 8, 5, 10, 2, 1));
        System.out.println("Original: " + list);
        
        LinkedList<Integer> partitioned = partition(list, 5);
        System.out.println("Partitioned around 5: " + partitioned);
        // Output: [3, 2, 1, 5, 8, 5, 10]
    }
}
```

---

## 3. LinkedList vs ArrayList

### Performance Comparison

```java
public class PerformanceComparison {
    
    public static void compareAddOperations() {
        int size = 100000;
        
        // ArrayList - add at end
        long start = System.nanoTime();
        List<Integer> arrayList = new ArrayList<>();
        for (int i = 0; i < size; i++) {
            arrayList.add(i);
        }
        long arrayListTime = System.nanoTime() - start;
        
        // LinkedList - add at end
        start = System.nanoTime();
        List<Integer> linkedList = new LinkedList<>();
        for (int i = 0; i < size; i++) {
            linkedList.add(i);
        }
        long linkedListTime = System.nanoTime() - start;
        
        System.out.println("Add at end:");
        System.out.println("ArrayList: " + arrayListTime / 1_000_000 + " ms");
        System.out.println("LinkedList: " + linkedListTime / 1_000_000 + " ms");
    }
    
    public static void compareAddAtBeginning() {
        int size = 10000;
        
        // ArrayList - add at beginning
        long start = System.nanoTime();
        List<Integer> arrayList = new ArrayList<>();
        for (int i = 0; i < size; i++) {
            arrayList.add(0, i);  // Add at beginning
        }
        long arrayListTime = System.nanoTime() - start;
        
        // LinkedList - add at beginning
        start = System.nanoTime();
        LinkedList<Integer> linkedList = new LinkedList<>();
        for (int i = 0; i < size; i++) {
            linkedList.addFirst(i);  // Add at beginning
        }
        long linkedListTime = System.nanoTime() - start;
        
        System.out.println("\nAdd at beginning:");
        System.out.println("ArrayList: " + arrayListTime / 1_000_000 + " ms");
        System.out.println("LinkedList: " + linkedListTime / 1_000_000 + " ms");
    }
    
    public static void compareRandomAccess() {
        int size = 100000;
        List<Integer> arrayList = new ArrayList<>();
        LinkedList<Integer> linkedList = new LinkedList<>();
        
        for (int i = 0; i < size; i++) {
            arrayList.add(i);
            linkedList.add(i);
        }
        
        // ArrayList - random access
        long start = System.nanoTime();
        for (int i = 0; i < 10000; i++) {
            int index = (int) (Math.random() * size);
            arrayList.get(index);
        }
        long arrayListTime = System.nanoTime() - start;
        
        // LinkedList - random access
        start = System.nanoTime();
        for (int i = 0; i < 10000; i++) {
            int index = (int) (Math.random() * size);
            linkedList.get(index);
        }
        long linkedListTime = System.nanoTime() - start;
        
        System.out.println("\nRandom access:");
        System.out.println("ArrayList: " + arrayListTime / 1_000_000 + " ms");
        System.out.println("LinkedList: " + linkedListTime / 1_000_000 + " ms");
    }
    
    public static void main(String[] args) {
        compareAddOperations();
        compareAddAtBeginning();
        compareRandomAccess();
    }
}
```

### When to Use LinkedList vs ArrayList

```java
public class WhenToUse {
    
    // Use LinkedList for Queue/Deque operations
    public static void queueExample() {
        Queue<String> queue = new LinkedList<>();
        
        // Efficient queue operations
        queue.offer("First");
        queue.offer("Second");
        queue.offer("Third");
        
        System.out.println(queue.poll());  // "First"
        System.out.println(queue.peek());  // "Second"
    }
    
    // Use LinkedList for Stack operations
    public static void stackExample() {
        Deque<String> stack = new LinkedList<>();
        
        // Efficient stack operations
        stack.push("First");
        stack.push("Second");
        stack.push("Third");
        
        System.out.println(stack.pop());   // "Third"
        System.out.println(stack.peek());  // "Second"
    }
    
    // Use ArrayList for random access
    public static void randomAccessExample() {
        List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D", "E"));
        
        // Efficient random access
        for (int i = 0; i < 1000; i++) {
            int index = (int) (Math.random() * list.size());
            String element = list.get(index);  // O(1)
        }
    }
    
    // Use LinkedList for frequent insertions/deletions at ends
    public static void insertionExample() {
        LinkedList<String> list = new LinkedList<>();
        
        // Efficient operations at ends
        list.addFirst("Start");    // O(1)
        list.addLast("End");       // O(1)
        list.removeFirst();        // O(1)
        list.removeLast();         // O(1)
    }
}
```

---

## 4. Implementation Questions

### Question 1: Implement a Simple Singly LinkedList

```java
class Node<T> {
    T data;
    Node<T> next;
    
    Node(T data) {
        this.data = data;
        this.next = null;
    }
}

class SinglyLinkedList<T> {
    private Node<T> head;
    private int size;
    
    public SinglyLinkedList() {
        this.head = null;
        this.size = 0;
    }
    
    // Add at beginning
    public void addFirst(T data) {
        Node<T> newNode = new Node<>(data);
        newNode.next = head;
        head = newNode;
        size++;
    }
    
    // Add at end
    public void addLast(T data) {
        Node<T> newNode = new Node<>(data);
        
        if (head == null) {
            head = newNode;
        } else {
            Node<T> current = head;
            while (current.next != null) {
                current = current.next;
            }
            current.next = newNode;
        }
        size++;
    }
    
    // Remove first
    public T removeFirst() {
        if (head == null) {
            throw new NoSuchElementException("List is empty");
        }
        
        T data = head.data;
        head = head.next;
        size--;
        return data;
    }
    
    // Get element at index
    public T get(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        
        Node<T> current = head;
        for (int i = 0; i < index; i++) {
            current = current.next;
        }
        return current.data;
    }
    
    // Reverse the list
    public void reverse() {
        Node<T> prev = null;
        Node<T> current = head;
        Node<T> next = null;
        
        while (current != null) {
            next = current.next;
            current.next = prev;
            prev = current;
            current = next;
        }
        
        head = prev;
    }
    
    // Print list
    public void print() {
        Node<T> current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }
    
    public int size() {
        return size;
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
}

// Usage
public class CustomLinkedListDemo {
    public static void main(String[] args) {
        SinglyLinkedList<Integer> list = new SinglyLinkedList<>();
        
        list.addLast(1);
        list.addLast(2);
        list.addLast(3);
        list.addFirst(0);
        
        list.print();  // 0 -> 1 -> 2 -> 3 -> null
        
        list.reverse();
        list.print();  // 3 -> 2 -> 1 -> 0 -> null
    }
}
```

### Question 2: Detect Cycle in LinkedList

```java
public class CycleDetection {
    
    static class Node {
        int data;
        Node next;
        
        Node(int data) {
            this.data = data;
        }
    }
    
    // Floyd's Cycle Detection Algorithm (Tortoise and Hare)
    public static boolean hasCycle(Node head) {
        if (head == null || head.next == null) {
            return false;
        }
        
        Node slow = head;
        Node fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            
            if (slow == fast) {
                return true;  // Cycle detected
            }
        }
        
        return false;  // No cycle
    }
    
    // Find the start of the cycle
    public static Node findCycleStart(Node head) {
        if (head == null || head.next == null) {
            return null;
        }
        
        Node slow = head;
        Node fast = head;
        
        // Detect cycle
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            
            if (slow == fast) {
                break;
            }
        }
        
        // No cycle
        if (fast == null || fast.next == null) {
            return null;
        }
        
        // Find start of cycle
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        
        return slow;
    }
    
    public static void main(String[] args) {
        // Create a linked list with cycle: 1 -> 2 -> 3 -> 4 -> 2
        Node head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        head.next.next.next = new Node(4);
        head.next.next.next.next = head.next;  // Create cycle
        
        System.out.println("Has cycle: " + hasCycle(head));  // true
        
        Node cycleStart = findCycleStart(head);
        System.out.println("Cycle starts at: " + cycleStart.data);  // 2
    }
}
```

---

## 5. Advanced Problems

### Problem 1: LRU Cache using LinkedHashMap

```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);  // accessOrder = true
        this.capacity = capacity;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
    
    public static void main(String[] args) {
        LRUCache<Integer, String> cache = new LRUCache<>(3);
        
        cache.put(1, "One");
        cache.put(2, "Two");
        cache.put(3, "Three");
        System.out.println(cache);  // {1=One, 2=Two, 3=Three}
        
        cache.get(1);  // Access 1
        cache.put(4, "Four");  // Evicts 2 (least recently used)
        System.out.println(cache);  // {3=Three, 1=One, 4=Four}
    }
}
```

### Problem 2: Clone LinkedList with Random Pointer

```java
class RandomNode {
    int data;
    RandomNode next;
    RandomNode random;
    
    RandomNode(int data) {
        this.data = data;
    }
}

public class CloneLinkedList {
    
    public static RandomNode clone(RandomNode head) {
        if (head == null) {
            return null;
        }
        
        Map<RandomNode, RandomNode> map = new HashMap<>();
        
        // First pass: create all nodes
        RandomNode current = head;
        while (current != null) {
            map.put(current, new RandomNode(current.data));
            current = current.next;
        }
        
        // Second pass: set next and random pointers
        current = head;
        while (current != null) {
            RandomNode cloned = map.get(current);
            cloned.next = map.get(current.next);
            cloned.random = map.get(current.random);
            current = current.next;
        }
        
        return map.get(head);
    }
}
```

### Problem 3: Flatten Multilevel LinkedList

```java
class MultilevelNode {
    int data;
    MultilevelNode next;
    MultilevelNode child;
    
    MultilevelNode(int data) {
        this.data = data;
    }
}

public class FlattenLinkedList {
    
    public static MultilevelNode flatten(MultilevelNode head) {
        if (head == null) {
            return null;
        }
        
        MultilevelNode current = head;
        
        while (current != null) {
            if (current.child != null) {
                // Find tail of child list
                MultilevelNode childTail = current.child;
                while (childTail.next != null) {
                    childTail = childTail.next;
                }
                
                // Connect child list
                childTail.next = current.next;
                current.next = current.child;
                current.child = null;
            }
            current = current.next;
        }
        
        return head;
    }
}
```

---

## Interview Tips

### Common Questions to Expect:

1. **Difference between ArrayList and LinkedList**
   - Time complexity of operations
   - Memory usage
   - When to use which

2. **LinkedList Internal Implementation**
   - Node structure
   - Doubly-linked vs singly-linked
   - Head and tail pointers

3. **Algorithm Problems**
   - Reverse LinkedList
   - Detect cycle
   - Find middle element
   - Merge sorted lists
   - Remove duplicates

4. **Performance Questions**
   - Why is get(index) slow in LinkedList?
   - Why is add(0, element) fast in LinkedList?
   - Memory overhead of LinkedList

5. **Design Questions**
   - Implement LRU Cache
   - Design a queue using LinkedList
   - Implement a stack using LinkedList

### Key Points to Remember:

- LinkedList is **doubly-linked** in Java
- Implements both **List** and **Deque** interfaces
- **O(1)** for add/remove at ends
- **O(n)** for random access
- **Not thread-safe**
- Uses more memory than ArrayList (node overhead)
- Good for **Queue/Deque** operations
- Bad for **random access**

---

## Summary

**LinkedList Strengths:**
- Fast insertions/deletions at ends: O(1)
- Implements Queue and Deque
- No resizing overhead
- Good for sequential access

**LinkedList Weaknesses:**
- Slow random access: O(n)
- More memory overhead (node pointers)
- Poor cache locality
- Not thread-safe

**Most Asked Interview Topics:**
1. Reverse LinkedList
2. Detect cycle
3. Find middle element
4. Merge sorted lists
5. LinkedList vs ArrayList
6. Remove duplicates
7. Palindrome check
8. Nth from end

**Practice these problems to ace LinkedList interviews!**