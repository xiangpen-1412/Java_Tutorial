
---
# 1. Abstract

`LinkedList` is a linear data structure implemented as a **doubly linked list**.  
It implements multiple behavioral contracts:

- List
    
- Deque
    
- Queue
    
- Cloneable
    
- Serializable
    

This multi-interface design is intentional: the class is not only a sequential container, but also a double-ended queue implementation.

---

# 2. Inheritance Hierarchy

```
Object
  └── AbstractCollection<E>
       └── AbstractList<E>
            └── AbstractSequentialList<E>
                 └── LinkedList<E>
```

Key engineering implication:

`AbstractSequentialList` indicates that index-based access is not optimized for random access.

---

# 3. Core Data Structure

## 3.1 Node Structure

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```

Each element stores:

- value reference
    
- pointer to next node
    
- pointer to previous node
    

This is a **non-contiguous memory structure**.

---

## 3.2 Internal Fields

```java
transient int size;
transient Node<E> first;
transient Node<E> last;
```

Properties:

- `first` → head node
    
- `last` → tail node
    
- `size` → number of elements
    

There is no backing array.

---

# 4. Structural Characteristics

## 4.1 Memory Layout

Unlike array-based structures, nodes are allocated independently on the heap.

Implications:

- Poor CPU cache locality
    
- Higher memory overhead
    
- Increased GC pressure
    

Each element requires:

- object header
    
- element reference
    
- two node references
    

---

## 4.2 Bidirectional Traversal Optimization

Index-based lookup internally uses:

```java
if (index < (size >> 1))
    traverse from head
else
    traverse from tail
```

Although this reduces traversal distance by half on average, time complexity remains O(n).

---

# 5. Time Complexity (Critical)

|Operation|Complexity|Explanation|
|---|---|---|
|get(index)|O(n)|sequential traversal|
|add(E)|O(1)|tail insertion|
|addFirst|O(1)|pointer update|
|addLast|O(1)|pointer update|
|removeFirst|O(1)|pointer update|
|removeLast|O(1)|pointer update|
|add(index)|O(n)|traversal required|
|remove(index)|O(n)|traversal required|
|contains|O(n)|linear scan|

Key distinction:

Insertion is O(1) only when node reference is already known.

### 5.1 Insert Complexity — Concrete Example

**Scenario A: insert at head / tail (no position lookup) → O(1)**

```java
LinkedList<Integer> list = new LinkedList<>();
list.addLast(10);   // O(1) tail insert
list.addFirst(5);   // O(1) head insert
```

Reason: `first` / `last` node references are already maintained by the list, so insertion is pure pointer rewiring.

---

**Scenario B: insert by index → O(n) because you must locate the node first**

```java
LinkedList<Integer> list = new LinkedList<>();
for (int i = 0; i < 1_000_000; i++) {
    list.addLast(i);
}

// Insert near the middle
list.add(500_000, 999); // Overall O(n)
```

What happens internally:

1. **Traversal**: `node(index)` walks from `first` or `last` (whichever is closer) to reach index `500_000` → O(n)
    
2. **Linking**: once the target node is found, the actual insertion is pointer updates (prev/next rewiring) → O(1)
    

So the total cost is dominated by traversal → **O(n)**.

---

**Scenario C: insert using Iterator (position already known) → O(1) per insert**

```java
LinkedList<Integer> list = new LinkedList<>();
list.add(1);
list.add(2);
list.add(3);

ListIterator<Integer> it = list.listIterator();
it.next();          // now iterator is positioned after the first node
it.add(99);          // O(1) insertion at iterator position
```

Reason: the iterator already holds a direct reference to the current node, so no additional traversal is needed.

---

# 6. Operational Semantics

## 6.1 Head Insertion

- newNode.next = first
    
- first.prev = newNode
    
- first = newNode
    

## 6.2 Tail Insertion

- newNode.prev = last
    
- last.next = newNode
    
- last = newNode
    

## 6.3 Node Removal

Removal rewires:

- prev.next = next
    
- next.prev = prev
    

Edge cases:

- removing head
    
- removing tail
    
- removing the only node
    

---

# 7. Behavioral Roles

## 7.1 As List

Sequential container without fast random access.

## 7.2 As Queue

FIFO semantics via:

- offer
    
- poll
    
- peek
    

## 7.3 As Deque

Double-ended queue semantics:

- addFirst
    
- addLast
    
- removeFirst
    
- removeLast
    

Engineering note:

Although LinkedList implements Deque, `ArrayDeque` is typically preferred in production.

---

# 8. Comparison with ArrayList

|Dimension|LinkedList|ArrayList|
|---|---|---|
|Backing Structure|Doubly linked list|Dynamic array|
|Random Access|O(n)|O(1)|
|Head Insertion|O(1)|O(n)|
|Memory Usage|High|Lower|
|Cache Locality|Poor|Good|
|Resizing Cost|None|Occasional resize|

In real-world high-performance systems, ArrayList often outperforms LinkedList even for many insertion cases due to cache behavior.

---

# 9. Iterator and ListIterator Behavior

## 9.1 What is Iterator vs ListIterator

- `Iterator<E>` is the base traversal interface (forward-only).
    
- `ListIterator<E>` extends `Iterator<E>` and is **bi-directional** and **mutation-capable at cursor position**.
    

`ListIterator` is **built-in** in the JDK (`java.util.ListIterator`). You do not implement it yourself; you obtain an instance from a `List`:

```java
ListIterator<Place> it = itinerary.listIterator();
```

Only `List` types provide `listIterator()`. Because `LinkedList` implements `List`, it supports `ListIterator`.

---

## 9.2 Cursor Semantics (Most Bug-Prone)

A `ListIterator` maintains a <b>cursor position between elements</b>, not “on” an element.

> <span style="color:rgb(255, 0, 0)">Return the element first, then move the cursor</span>

- `next()` returns the element **after** the cursor and moves the cursor forward.
    
- `previous()` returns the element **before** the cursor and moves the cursor backward.
    

This is why direction switching (forward → backward / backward → forward) can cause:

- printing the same element twice
    
- skipping an element
    

Typical industrial-grade control uses a flag:

- `boolean goingForward`
    
- when switching direction, do one compensating `next()` / `previous()` to realign the cursor
    

---

## 9.3 Mutations Through the Iterator

Supported operations:

- `it.add(x)` inserts at the current cursor position → O(1) insertion (position already known)
    
- `it.remove()` removes the last element returned by `next()` or `previous()`
    
- `it.set(x)` replaces the last element returned by `next()` or `previous()`
    

Rules (important for correctness):

- `remove()` / `set()` can only be called **after** a successful `next()` or `previous()`
    
- calling `remove()` twice in a row without moving throws `IllegalStateException`
    

---

## 9.4 Fail-Fast Behavior

LinkedList iterators are **fail-fast**:

- structural modifications outside the iterator typically trigger `ConcurrentModificationException`
    

This is not a synchronization guarantee; it is a best-effort detection mechanism.

---

# 10. Thread Safety

LinkedList is NOT thread-safe.

Options:

- Collections.synchronizedList
    
- ConcurrentLinkedDeque
    
- External synchronization
    

---

# 11. Engineering Trade-offs

Advantages:

- Stable insertion/deletion when node reference is available
    
- Natural double-ended operations
    

Disadvantages:

- Poor memory efficiency
    
- Poor cache locality
    
- High object overhead
    
- Rarely optimal in modern JVM workloads
    

---

# 12. Algorithmic Context

LinkedList is foundational for understanding:

- LRU cache implementation (with HashMap + doubly linked list)
    
- Graph adjacency list representation
    
- Deque-based BFS traversal
    

However, direct usage in large-scale backend systems is uncommon.

---

# 13. Edge Cases and Internal Details

- size must always reflect structural modifications
    
- first and last must be null when size == 0
    
- Removing last element resets both first and last
    
- Null elements are allowed
    

---

# 14. Practical Engineering Conclusion

LinkedList is primarily educational and conceptual in modern enterprise systems.

For production:

- Prefer ArrayList for sequential data
    
- Prefer ArrayDeque for queue/deque
    
- Prefer Concurrent collections for multi-threading
    

Understanding LinkedList deeply is essential for interview-level data structure reasoning, but its direct industrial usage is limited.

---

# 15. Conceptual Summary

LinkedList = Doubly Linked Structure + Sequential Access + Deque Semantics

Core identity:

- Non-contiguous memory
    
- Pointer rewiring instead of element shifting
    
- Linear traversal cost
    

Mastery of LinkedList strengthens understanding of pointer manipulation, memory structure, and algorithmic trade-offs.