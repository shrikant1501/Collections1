# Custom LinkedList Implementation - Complete Guide

## Table of Contents
1. [Singly LinkedList Implementation](#singly-linkedlist-implementation)
2. [Doubly LinkedList Implementation](#doubly-linkedlist-implementation)
3. [Circular LinkedList Implementation](#circular-linkedlist-implementation)
4. [Testing and Usage Examples](#testing-and-usage-examples)

---

## 1. Singly LinkedList Implementation

### Node Class

```java
/**
 * Node class for Singly LinkedList
 * Each node contains data and reference to next node
 */
class Node<T> {
    T data;
    Node<T> next;
    
    // Constructor
    public Node(T data) {
        this.data = data;
        this.next = null;
    }
    
    // Constructor with next node
    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }
    
    @Override
    public String toString() {
        return data.toString();
    }
}
```

### Complete Singly LinkedList Implementation

```java
/**
 * Custom Singly LinkedList Implementation
 * @param <T> Type of elements in the list
 */
public class SinglyLinkedList<T> {
    
    private Node<T> head;
    private Node<T> tail;
    private int size;
    
    // Constructor
    public SinglyLinkedList() {
        this.head = null;
        this.tail = null;
        this.size = 0;
    }
    
    // ==================== ADD OPERATIONS ====================
    
    /**
     * Add element at the beginning
     * Time Complexity: O(1)
     */
    public void addFirst(T data) {
        Node<T> newNode = new Node<>(data);
        
        if (isEmpty()) {
            head = tail = newNode;
        } else {
            newNode.next = head;
            head = newNode;
        }
        size++;
    }
    
    /**
     * Add element at the end
     * Time Complexity: O(1) - because we maintain tail reference
     */
    public void addLast(T data) {
        Node<T> newNode = new Node<>(data);
        
        if (isEmpty()) {
            head = tail = newNode;
        } else {
            tail.next = newNode;
            tail = newNode;
        }
        size++;
    }
    
    /**
     * Add element at specific index
     * Time Complexity: O(n)
     */
    public void add(int index, T data) {
        if (index < 0 || index > size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        if (index == 0) {
            addFirst(data);
            return;
        }
        
        if (index == size) {
            addLast(data);
            return;
        }
        
        Node<T> newNode = new Node<>(data);
        Node<T> current = head;
        
        // Traverse to node before insertion point
        for (int i = 0; i < index - 1; i++) {
            current = current.next;
        }
        
        newNode.next = current.next;
        current.next = newNode;
        size++;
    }
    
    // ==================== REMOVE OPERATIONS ====================
    
    /**
     * Remove first element
     * Time Complexity: O(1)
     */
    public T removeFirst() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        
        T data = head.data;
        head = head.next;
        size--;
        
        if (isEmpty()) {
            tail = null;
        }
        
        return data;
    }
    
    /**
     * Remove last element
     * Time Complexity: O(n) - need to traverse to second-last node
     */
    public T removeLast() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        
        if (size == 1) {
            return removeFirst();
        }
        
        Node<T> current = head;
        
        // Traverse to second-last node
        while (current.next != tail) {
            current = current.next;
        }
        
        T data = tail.data;
        current.next = null;
        tail = current;
        size--;
        
        return data;
    }
    
    /**
     * Remove element at specific index
     * Time Complexity: O(n)
     */
    public T remove(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        if (index == 0) {
            return removeFirst();
        }
        
        if (index == size - 1) {
            return removeLast();
        }
        
        Node<T> current = head;
        
        // Traverse to node before removal point
        for (int i = 0; i < index - 1; i++) {
            current = current.next;
        }
        
        T data = current.next.data;
        current.next = current.next.next;
        size--;
        
        return data;
    }
    
    /**
     * Remove first occurrence of element
     * Time Complexity: O(n)
     */
    public boolean remove(T data) {
        if (isEmpty()) {
            return false;
        }
        
        // If head contains the data
        if (head.data.equals(data)) {
            removeFirst();
            return true;
        }
        
        Node<T> current = head;
        
        while (current.next != null) {
            if (current.next.data.equals(data)) {
                // Update tail if removing last element
                if (current.next == tail) {
                    tail = current;
                }
                current.next = current.next.next;
                size--;
                return true;
            }
            current = current.next;
        }
        
        return false;
    }
    
    // ==================== ACCESS OPERATIONS ====================
    
    /**
     * Get element at specific index
     * Time Complexity: O(n)
     */
    public T get(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        Node<T> current = head;
        for (int i = 0; i < index; i++) {
            current = current.next;
        }
        
        return current.data;
    }
    
    /**
     * Get first element
     * Time Complexity: O(1)
     */
    public T getFirst() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        return head.data;
    }
    
    /**
     * Get last element
     * Time Complexity: O(1)
     */
    public T getLast() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        return tail.data;
    }
    
    /**
     * Set element at specific index
     * Time Complexity: O(n)
     */
    public T set(int index, T data) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        Node<T> current = head;
        for (int i = 0; i < index; i++) {
            current = current.next;
        }
        
        T oldData = current.data;
        current.data = data;
        return oldData;
    }
    
    // ==================== SEARCH OPERATIONS ====================
    
    /**
     * Check if list contains element
     * Time Complexity: O(n)
     */
    public boolean contains(T data) {
        return indexOf(data) != -1;
    }
    
    /**
     * Find index of first occurrence
     * Time Complexity: O(n)
     */
    public int indexOf(T data) {
        Node<T> current = head;
        int index = 0;
        
        while (current != null) {
            if (current.data.equals(data)) {
                return index;
            }
            current = current.next;
            index++;
        }
        
        return -1;
    }
    
    /**
     * Find index of last occurrence
     * Time Complexity: O(n)
     */
    public int lastIndexOf(T data) {
        Node<T> current = head;
        int index = 0;
        int lastIndex = -1;
        
        while (current != null) {
            if (current.data.equals(data)) {
                lastIndex = index;
            }
            current = current.next;
            index++;
        }
        
        return lastIndex;
    }
    
    // ==================== UTILITY OPERATIONS ====================
    
    /**
     * Get size of list
     * Time Complexity: O(1)
     */
    public int size() {
        return size;
    }
    
    /**
     * Check if list is empty
     * Time Complexity: O(1)
     */
    public boolean isEmpty() {
        return size == 0;
    }
    
    /**
     * Clear all elements
     * Time Complexity: O(1)
     */
    public void clear() {
        head = null;
        tail = null;
        size = 0;
    }
    
    /**
     * Reverse the list
     * Time Complexity: O(n)
     */
    public void reverse() {
        if (isEmpty() || size == 1) {
            return;
        }
        
        Node<T> prev = null;
        Node<T> current = head;
        Node<T> next = null;
        tail = head;
        
        while (current != null) {
            next = current.next;
            current.next = prev;
            prev = current;
            current = next;
        }
        
        head = prev;
    }
    
    /**
     * Convert to array
     * Time Complexity: O(n)
     */
    public Object[] toArray() {
        Object[] array = new Object[size];
        Node<T> current = head;
        int index = 0;
        
        while (current != null) {
            array[index++] = current.data;
            current = current.next;
        }
        
        return array;
    }
    
    /**
     * Print list
     * Time Complexity: O(n)
     */
    public void print() {
        Node<T> current = head;
        System.out.print("[");
        
        while (current != null) {
            System.out.print(current.data);
            if (current.next != null) {
                System.out.print(" -> ");
            }
            current = current.next;
        }
        
        System.out.println("]");
    }
    
    /**
     * Print with null at end
     */
    public void printWithNull() {
        Node<T> current = head;
        
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        
        System.out.println("null");
    }
    
    @Override
    public String toString() {
        if (isEmpty()) {
            return "[]";
        }
        
        StringBuilder sb = new StringBuilder("[");
        Node<T> current = head;
        
        while (current != null) {
            sb.append(current.data);
            if (current.next != null) {
                sb.append(", ");
            }
            current = current.next;
        }
        
        sb.append("]");
        return sb.toString();
    }
}
```

---

## 2. Doubly LinkedList Implementation

### Node Class for Doubly LinkedList

```java
/**
 * Node class for Doubly LinkedList
 * Each node contains data and references to both next and previous nodes
 */
class DoublyNode<T> {
    T data;
    DoublyNode<T> next;
    DoublyNode<T> prev;
    
    // Constructor
    public DoublyNode(T data) {
        this.data = data;
        this.next = null;
        this.prev = null;
    }
    
    // Constructor with next and prev
    public DoublyNode(T data, DoublyNode<T> prev, DoublyNode<T> next) {
        this.data = data;
        this.next = next;
        this.prev = prev;
    }
    
    @Override
    public String toString() {
        return data.toString();
    }
}
```

### Complete Doubly LinkedList Implementation

```java
/**
 * Custom Doubly LinkedList Implementation
 * @param <T> Type of elements in the list
 */
public class DoublyLinkedList<T> {
    
    private DoublyNode<T> head;
    private DoublyNode<T> tail;
    private int size;
    
    // Constructor
    public DoublyLinkedList() {
        this.head = null;
        this.tail = null;
        this.size = 0;
    }
    
    // ==================== ADD OPERATIONS ====================
    
    /**
     * Add element at the beginning
     * Time Complexity: O(1)
     */
    public void addFirst(T data) {
        DoublyNode<T> newNode = new DoublyNode<>(data);
        
        if (isEmpty()) {
            head = tail = newNode;
        } else {
            newNode.next = head;
            head.prev = newNode;
            head = newNode;
        }
        size++;
    }
    
    /**
     * Add element at the end
     * Time Complexity: O(1)
     */
    public void addLast(T data) {
        DoublyNode<T> newNode = new DoublyNode<>(data);
        
        if (isEmpty()) {
            head = tail = newNode;
        } else {
            tail.next = newNode;
            newNode.prev = tail;
            tail = newNode;
        }
        size++;
    }
    
    /**
     * Add element at specific index
     * Time Complexity: O(n)
     */
    public void add(int index, T data) {
        if (index < 0 || index > size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        if (index == 0) {
            addFirst(data);
            return;
        }
        
        if (index == size) {
            addLast(data);
            return;
        }
        
        DoublyNode<T> newNode = new DoublyNode<>(data);
        DoublyNode<T> current = getNode(index);
        
        newNode.next = current;
        newNode.prev = current.prev;
        current.prev.next = newNode;
        current.prev = newNode;
        
        size++;
    }
    
    // ==================== REMOVE OPERATIONS ====================
    
    /**
     * Remove first element
     * Time Complexity: O(1)
     */
    public T removeFirst() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        
        T data = head.data;
        
        if (size == 1) {
            head = tail = null;
        } else {
            head = head.next;
            head.prev = null;
        }
        
        size--;
        return data;
    }
    
    /**
     * Remove last element
     * Time Complexity: O(1) - advantage of doubly linked list
     */
    public T removeLast() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        
        T data = tail.data;
        
        if (size == 1) {
            head = tail = null;
        } else {
            tail = tail.prev;
            tail.next = null;
        }
        
        size--;
        return data;
    }
    
    /**
     * Remove element at specific index
     * Time Complexity: O(n)
     */
    public T remove(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        if (index == 0) {
            return removeFirst();
        }
        
        if (index == size - 1) {
            return removeLast();
        }
        
        DoublyNode<T> current = getNode(index);
        T data = current.data;
        
        current.prev.next = current.next;
        current.next.prev = current.prev;
        
        size--;
        return data;
    }
    
    /**
     * Remove first occurrence of element
     * Time Complexity: O(n)
     */
    public boolean remove(T data) {
        DoublyNode<T> current = head;
        
        while (current != null) {
            if (current.data.equals(data)) {
                if (current == head) {
                    removeFirst();
                } else if (current == tail) {
                    removeLast();
                } else {
                    current.prev.next = current.next;
                    current.next.prev = current.prev;
                    size--;
                }
                return true;
            }
            current = current.next;
        }
        
        return false;
    }
    
    // ==================== ACCESS OPERATIONS ====================
    
    /**
     * Get node at specific index (optimized for doubly linked list)
     * Time Complexity: O(n/2) - can traverse from either end
     */
    private DoublyNode<T> getNode(int index) {
        DoublyNode<T> current;
        
        // Optimize by choosing direction based on index
        if (index < size / 2) {
            // Traverse from head
            current = head;
            for (int i = 0; i < index; i++) {
                current = current.next;
            }
        } else {
            // Traverse from tail
            current = tail;
            for (int i = size - 1; i > index; i--) {
                current = current.prev;
            }
        }
        
        return current;
    }
    
    /**
     * Get element at specific index
     * Time Complexity: O(n/2)
     */
    public T get(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        return getNode(index).data;
    }
    
    /**
     * Get first element
     * Time Complexity: O(1)
     */
    public T getFirst() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        return head.data;
    }
    
    /**
     * Get last element
     * Time Complexity: O(1)
     */
    public T getLast() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        return tail.data;
    }
    
    /**
     * Set element at specific index
     * Time Complexity: O(n/2)
     */
    public T set(int index, T data) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
        
        DoublyNode<T> node = getNode(index);
        T oldData = node.data;
        node.data = data;
        return oldData;
    }
    
    // ==================== SEARCH OPERATIONS ====================
    
    /**
     * Check if list contains element
     * Time Complexity: O(n)
     */
    public boolean contains(T data) {
        return indexOf(data) != -1;
    }
    
    /**
     * Find index of first occurrence
     * Time Complexity: O(n)
     */
    public int indexOf(T data) {
        DoublyNode<T> current = head;
        int index = 0;
        
        while (current != null) {
            if (current.data.equals(data)) {
                return index;
            }
            current = current.next;
            index++;
        }
        
        return -1;
    }
    
    /**
     * Find index of last occurrence (optimized - traverse from tail)
     * Time Complexity: O(n)
     */
    public int lastIndexOf(T data) {
        DoublyNode<T> current = tail;
        int index = size - 1;
        
        while (current != null) {
            if (current.data.equals(data)) {
                return index;
            }
            current = current.prev;
            index--;
        }
        
        return -1;
    }
    
    // ==================== UTILITY OPERATIONS ====================
    
    /**
     * Get size of list
     * Time Complexity: O(1)
     */
    public int size() {
        return size;
    }
    
    /**
     * Check if list is empty
     * Time Complexity: O(1)
     */
    public boolean isEmpty() {
        return size == 0;
    }
    
    /**
     * Clear all elements
     * Time Complexity: O(1)
     */
    public void clear() {
        head = null;
        tail = null;
        size = 0;
    }
    
    /**
     * Reverse the list
     * Time Complexity: O(n)
     */
    public void reverse() {
        if (isEmpty() || size == 1) {
            return;
        }
        
        DoublyNode<T> current = head;
        DoublyNode<T> temp = null;
        
        while (current != null) {
            // Swap next and prev
            temp = current.prev;
            current.prev = current.next;
            current.next = temp;
            current = current.prev;
        }
        
        // Swap head and tail
        temp = head;
        head = tail;
        tail = temp;
    }
    
    /**
     * Print list forward
     * Time Complexity: O(n)
     */
    public void printForward() {
        DoublyNode<T> current = head;
        System.out.print("[");
        
        while (current != null) {
            System.out.print(current.data);
            if (current.next != null) {
                System.out.print(" <-> ");
            }
            current = current.next;
        }
        
        System.out.println("]");
    }
    
    /**
     * Print list backward
     * Time Complexity: O(n)
     */
    public void printBackward() {
        DoublyNode<T> current = tail;
        System.out.print("[");
        
        while (current != null) {
            System.out.print(current.data);
            if (current.prev != null) {
                System.out.print(" <-> ");
            }
            current = current.prev;
        }
        
        System.out.println("]");
    }
    
    @Override
    public String toString() {
        if (isEmpty()) {
            return "[]";
        }
        
        StringBuilder sb = new StringBuilder("[");
        DoublyNode<T> current = head;
        
        while (current != null) {
            sb.append(current.data);
            if (current.next != null) {
                sb.append(", ");
            }
            current = current.next;
        }
        
        sb.append("]");
        return sb.toString();
    }
}
```

---

## 3. Circular LinkedList Implementation

```java
/**
 * Custom Circular LinkedList Implementation
 * Last node points back to first node
 */
public class CircularLinkedList<T> {
    
    private Node<T> tail;  // Only maintain tail, head is tail.next
    private int size;
    
    public CircularLinkedList() {
        this.tail = null;
        this.size = 0;
    }
    
    /**
     * Add element at the beginning
     * Time Complexity: O(1)
     */
    public void addFirst(T data) {
        Node<T> newNode = new Node<>(data);
        
        if (isEmpty()) {
            tail = newNode;
            tail.next = tail;  // Point to itself
        } else {
            newNode.next = tail.next;
            tail.next = newNode;
        }
        size++;
    }
    
    /**
     * Add element at the end
     * Time Complexity: O(1)
     */
    public void addLast(T data) {
        addFirst(data);
        tail = tail.next;  // Move tail to new node
    }
    
    /**
     * Remove first element
     * Time Complexity: O(1)
     */
    public T removeFirst() {
        if (isEmpty()) {
            throw new NoSuchElementException("List is empty");
        }
        
        Node<T> head = tail.next;
        T data = head.data;
        
        if (size == 1) {
            tail = null;
        } else {
            tail.next = head.next;
        }
        
        size--;
        return data;
    }
    
    /**
     * Print circular list
     */
    public void print() {
        if (isEmpty()) {
            System.out.println("[]");
            return;
        }
        
        Node<T> current = tail.next;
        System.out.print("[");
        
        do {
            System.out.print(current.data);
            if (current != tail) {
                System.out.print(" -> ");
            }
            current = current.next;
        } while (current != tail.next);
        
        System.out.println(" -> (circular)]");
    }
    
    public int size() {
        return size;
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
}
```

---

## 4. Testing and Usage Examples

### Testing Singly LinkedList

```java
public class SinglyLinkedListTest {
    
    public static void main(String[] args) {
        System.out.println("=== Singly LinkedList Test ===\n");
        
        SinglyLinkedList<Integer> list = new SinglyLinkedList<>();
        
        // Test add operations
        System.out.println("1. Testing Add Operations:");
        list.addLast(10);
        list.addLast(20);
        list.addLast(30);
        System.out.println("After addLast(10, 20, 30): " + list);
        
        list.addFirst(5);
        System.out.println("After addFirst(5): " + list);
        
        list.add(2, 15);
        System.out.println("After add(2, 15): " + list);
        
        // Test access operations
        System.out.println("\n2. Testing Access Operations:");
        System.out.println("get(0): " + list.get(0));
        System.out.println("get(2): " + list.get(2));
        System.out.println("getFirst(): " + list.getFirst());
        System.out.println("getLast(): " + list.getLast());
        System.out.println("size(): " + list.size());
        
        // Test search operations
        System.out.println("\n3. Testing Search Operations:");
        System.out.println("contains(15): " + list.contains(15));
        System.out.println("contains(100): " + list.contains(100));
        System.out.println("indexOf(20): " + list.indexOf(20));
        
        // Test set operation
        System.out.println("\n4. Testing Set Operation:");
        System.out.println("set(2, 25): " + list.set(2, 25));
        System.out.println("After set: " + list);
        
        // Test remove operations
        System.out.println("\n5. Testing Remove Operations:");
        System.out.println("removeFirst(): " + list.removeFirst());
        System.out.println("After removeFirst: " + list);
        
        System.out.println("removeLast(): " + list.removeLast());
        System.out.println("After removeLast: " + list);
        
        System.out.println("remove(1): " + list.remove(1));
        System.out.println("After remove(1): " + list);
        
        // Test reverse
        System.out.println("\n6. Testing Reverse:");
        list.addLast(30);
        list.addLast(40);
        list.addLast(50);
        System.out.println("Before reverse: " + list);
        list.reverse();
        System.out.println("After reverse: " + list);
        
        // Test clear
        System.out.println("\n7. Testing Clear:");
        list.clear();
        System.out.println("After clear: " + list);
        System.out.println("isEmpty(): " + list.isEmpty());
    }
}
```

### Testing Doubly LinkedList

```java
public class DoublyLinkedListTest {
    
    public static void main(String[] args) {
        System.out.println("=== Doubly LinkedList Test ===\n");
        
        DoublyLinkedList<String> list = new DoublyLinkedList<>();
        
        // Test add operations
        System.out.println("1. Testing Add Operations:");
        list.addLast("Apple");
        list.addLast("Banana");
        list.addLast("Cherry");
        System.out.println("After adding fruits: " + list);
        
        list.addFirst("Mango");
        System.out.println("After addFirst(Mango): " + list);
        
        list.add(2, "Orange");
        System.out.println("After add(2, Orange): " + list);
        
        // Test bidirectional printing
        System.out.println("\n2. Testing Bidirectional Printing:");
        System.out.print("Forward: ");
        list.printForward();
        System.out.print("Backward: ");
        list.printBackward();
        
        // Test remove operations
        System.out.println("\n3. Testing Remove Operations:");
        System.out.println("removeFirst(): " + list.removeFirst());
        System.out.println("removeLast(): " + list.removeLast());
        System.out.println("After removes: " + list);
        
        // Test reverse
        System.out.println("\n4. Testing Reverse:");
        list.reverse();
        System.out.println("After reverse: " + list);
        
        // Test optimized access
        System.out.println("\n5. Testing Optimized Access:");
        list.addLast("Date");
        list.addLast("Elderberry");
        list.addLast("Fig");
        System.out.println("List: " + list);
        System.out.println("get(0) - from head: " + list.get(0));
        System.out.println("get(5) - from tail: " + list.get(5));
    }
}
```

### Testing Circular LinkedList

```java
public class CircularLinkedListTest {
    
    public static void main(String[] args) {
        System.out.println("=== Circular LinkedList Test ===\n");
        
        CircularLinkedList<Integer> list = new CircularLinkedList<>();
        
        list.addLast(10);
        list.addLast(20);
        list.addLast(30);
        list.addFirst(5);
        
        System.out.println("Circular List:");
        list.print();
        
        System.out.println("\nAfter removeFirst():");
        list.removeFirst();
        list.print();
    }
}
```

### Performance Comparison Example

```java
public class PerformanceTest {
    
    public static void main(String[] args) {
        int size = 100000;
        
        // Test Singly LinkedList
        System.out.println("=== Singly LinkedList Performance ===");
        SinglyLinkedList<Integer> singlyList = new SinglyLinkedList<>();
        
        long start = System.nanoTime();
        for (int i = 0; i < size; i++) {
            singlyList.addLast(i);
        }
        long end = System.nanoTime();
        System.out.println("addLast " + size + " elements: " + (end - start) / 1_000_000 + " ms");
        
        start = System.nanoTime();
        for (int i = 0; i < 1000; i++) {
            singlyList.get(i);
        }
        end = System.nanoTime();
        System.out.println("get 1000 elements: " + (end - start) / 1_000_000 + " ms");
        
        // Test Doubly LinkedList
        System.out.println("\n=== Doubly LinkedList Performance ===");
        DoublyLinkedList<Integer> doublyList = new DoublyLinkedList<>();
        
        start = System.nanoTime();
        for (int i = 0; i < size; i++) {
            doublyList.addLast(i);
        }
        end = System.nanoTime();
        System.out.println("addLast " + size + " elements: " + (end - start) / 1_000_000 + " ms");
        
        start = System.nanoTime();
        for (int i = 0; i < 1000; i++) {
            doublyList.get(i);
        }
        end = System.nanoTime();
        System.out.println("get 1000 elements (optimized): " + (end - start) / 1_000_000 + " ms");
    }
}
```

---

## Summary

### Singly LinkedList
**Advantages:**
- Simple implementation
- Less memory per node (only one pointer)
- Efficient addFirst() - O(1)

**Disadvantages:**
- removeLast() is O(n)
- Can only traverse forward
- No optimization for accessing elements near end

### Doubly LinkedList
**Advantages:**
- removeLast() is O(1)
- Can traverse both directions
- Optimized access (can start from either end)
- Easier to implement certain operations

**Disadvantages:**
- More memory per node (two pointers)
- Slightly more complex implementation
- More pointer updates needed

### Circular LinkedList
**Advantages:**
- No null pointers
- Can traverse entire list from any node
- Useful for round-robin scheduling

**Disadvantages:**
- Need to be careful to avoid infinite loops
- Slightly more complex logic

### Time Complexity Comparison

| Operation | Singly | Doubly | Circular |
|-----------|--------|--------|----------|
| addFirst() | O(1) | O(1) | O(1) |
| addLast() | O(1) | O(1) | O(1) |
| removeFirst() | O(1) | O(1) | O(1) |
| removeLast() | O(n) | O(1) | O(n) |
| get(index) | O(n) | O(n/2) | O(n) |
| search | O(n) | O(n) | O(n) |

**Key Takeaways:**
- Use **Singly LinkedList** when memory is a concern and you don't need backward traversal
- Use **Doubly LinkedList** when you need efficient operations at both ends
- Use **Circular LinkedList** for applications like round-robin scheduling or circular buffers
- Java's LinkedList uses **Doubly LinkedList** implementation

This completes the custom LinkedList implementation guide!