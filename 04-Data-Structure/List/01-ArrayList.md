
---
## 1. Overview

`ArrayList<E>` is a resizable array implementation of the `List` interface in the Java Collections Framework.

It provides:

- Indexed access (random access)
    
- Dynamic resizing
    
- Ordered storage
    
- Duplicate elements allowed
    
- Null elements allowed
    

Underlying Data Structure:

- Backed by a contiguous array (`Object[] elementData`)
    

Implements:

- List
    
- RandomAccess
    
- Cloneable
    
- Serializable
    

---

## 2. Internal Structure

Core Fields (simplified):

```java
transient Object[] elementData;
private int size;
```

Key Properties:

- `size` = number of actual stored elements
    
- `elementData.length` = current capacity
    

Default initial capacity (after first add): 10

---

## 3. Resizing Mechanism

When capacity is insufficient:

New capacity = oldCapacity + (oldCapacity >> 1)

Which equals:

1.5x growth strategy

Example:

- 10 → 15
    
- 15 → 22
    
- 22 → 33
    

Reason:

- Reduces number of reallocations
    
- Maintains amortized O(1) insertion
    

Resize Cost:

- O(n) due to array copy
    

But amortized add complexity remains O(1)

---

## 4. Time Complexity (Critical for Interviews)

|Operation|Time Complexity|Reason|
|---|---|---|
|get(index)|O(1)|Direct index access|
|set(index, element)|O(1)|Direct overwrite|
|add(element)|O(1) amortized|Occasional resize|
|add(index, element)|O(n)|Shift required|
|remove(index)|O(n)|Shift required|
|remove(object)|O(n)|Search + shift|
|contains()|O(n)|Linear scan|
|size()|O(1)|Stored field|
|clear()|O(n)|Null assignments|

Key Insight:  
All operations requiring shifting are O(n).

---

## 5. Memory Behavior

ArrayList stores references (not primitive values).

For primitive types:

```java
ArrayList<Integer>
```

This introduces:

- Autoboxing
    
- Extra heap allocation
    
- More GC pressure
    

For high-performance scenarios:

- Prefer primitive arrays
    
- Or specialized libraries
    

---

## 6. Capacity vs Size

Important Distinction:

- size() = number of stored elements
    
- capacity = internal array length
    

You can manually control capacity:

```java
ArrayList<String> list = new ArrayList<>(100);
list.ensureCapacity(200);
list.trimToSize();
```

---

## 7. Structural Modification & Fail-Fast Behavior

ArrayList uses `modCount` to detect concurrent modification.

If collection is structurally modified outside iterator:

```java
Iterator<String> it = list.iterator();
list.add("X");
it.next(); // throws ConcurrentModificationException
```

This is fail-fast behavior.

Important:

- Not thread-safe
    
- Must use synchronization or CopyOnWriteArrayList for concurrency
    

---

## 8. Iteration Performance

Best performance:

```java
for (int i = 0; i < list.size(); i++) {
    list.get(i);
}
```

Or:

```java
for (String s : list) {
    // use s
}
```

Avoid frequent remove during iteration (costly shifting).

---

## 9. ArrayList vs LinkedList

| Feature         | ArrayList | LinkedList          |
| --------------- | --------- | ------------------- |
| Random Access   | O(1)      | O(n)                |
| Insert at Head  | O(n)      | O(1)                |
| Memory Overhead | Low       | High (node objects) |
| Cache Friendly  | Yes       | No                  |

Industrial Reality:  
ArrayList is preferred in most production systems.

---

## 10. Common Pitfalls

1. Frequent middle insertions → performance degradation
    
2. Misunderstanding amortized O(1)
    
3. Confusing capacity with size
    
4. Using ArrayList in multi-threaded environment without synchronization
    
5. Heavy primitive boxing overhead
    

---

## 11. Binary Search Usage (Sorted List Required)

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(5);
list.add(1);
list.add(3);

Collections.sort(list);

int index = Collections.binarySearch(list, 3);
```

Precondition:

- List must be sorted
    

Time Complexity:

- O(log n)
    

---

## 12. When To Use ArrayList

Use when:

- Frequent read operations
    
- Random index access required
    
- Append-heavy workload
    

Avoid when:

- Heavy head insert/delete
    
- Real-time strict memory constraints
    
- High-concurrency mutation scenario
    

---

## 13. Interview-Level Key Points

1. Amortized O(1) proof reasoning
    
2. Resize growth strategy explanation
    
3. Difference between capacity and size
    
4. Fail-fast iterator principle
    
5. Internal array copying mechanism
    
6. Comparison vs LinkedList in CPU cache behavior
    

---

# Summary

ArrayList is a dynamic array optimized for random access and append-heavy workloads. It trades off insertion/removal efficiency for contiguous memory and cache performance. In real-world industrial systems, it is the default List implementation unless specific constraints require otherwise.