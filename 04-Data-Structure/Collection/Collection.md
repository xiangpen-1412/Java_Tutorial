___
## 1. Overview

`Collection` is the **root interface of the Java Collection Framework** (excluding `Map`). It represents a group of objects known as **elements**.

The `Collection` interface defines the **fundamental operations** that every collection data structure should support, such as:

- adding elements
    
- removing elements
    
- querying elements
    
- iteration
    
- bulk operations
    

It provides a **minimal common abstraction** that allows algorithms to operate on different data structures in a uniform way.

```
Collection<E>
```

Typical implementations include:

- `List`
    
- `Set`
    
- `Queue`
    

These interfaces extend `Collection` and add more specialized behavior.

---

# 2. Collection Hierarchy

The simplified hierarchy of the Java Collection Framework is:

```
Iterable
   │
Collection
   ├── List
   │     ├── ArrayList
   │     └── LinkedList
   │
   ├── Set
   │     ├── HashSet
   │     ├── LinkedHashSet
   │     └── TreeSet
   │
   └── Queue
         ├── PriorityQueue
         └── ArrayDeque
```

`Map` is **not part of the `Collection` hierarchy** because it stores key-value pairs instead of individual elements.

---

# 3. Core Responsibilities of Collection

The `Collection` interface defines five major categories of operations:

1. Element insertion
    
2. Element removal
    
3. Membership testing
    
4. Iteration
    
5. Bulk operations
    

These operations provide a **unified API** across different implementations.

---

# 4. Element Insertion

## add(E e)

Adds an element to the collection.

```java
Collection<String> names = new ArrayList<>();

names.add("Alice");
names.add("Bob");
```

Return value:

```
true  → collection changed
false → element not added
```

Example where insertion may fail:

```
Set does not allow duplicate elements
```

---

## addAll

Adds all elements from another collection.

```java
```
```java
Collection<Integer> a = new ArrayList<>();
a.add(1);
a.add(2);

Collection<Integer> b = List.of(3,4);

a.addAll(b);
```

Result:

```
[1, 2, 3, 4]
```

---

# 5. Element Removal

## remove(Object o)

Removes a single element equal to the specified object.

```java
Collection<String> names = new ArrayList<>();

names.add("Alice");
names.add("Bob");

names.remove("Alice");
```

Equality is determined by:

```
equals()
```

---

## removeAll

Removes all elements contained in another collection.

```java
Collection<Integer> nums = new ArrayList<>(List.of(1,2,3,4));

nums.removeAll(List.of(2,4));

// result: [1,3]
```

Conceptually:

```
current = current − c
```

---

## retainAll

Keeps only elements that are also present in the specified collection.

```java
Collection<Integer> nums = new ArrayList<>(List.of(1,2,3,4));

nums.retainAll(List.of(2,4,6));

// result: [2,4]
```

Conceptually:

```
intersection
```

---

## clear()

Removes all elements from the collection.

```java
Collection<String> names = new ArrayList<>();

names.add("A");
names.add("B");

names.clear();
```

---

# 6. Query Operations

## contains(Object o)

Checks whether the collection contains the specified element.

```java
Collection<String> names = List.of("Alice","Bob");

names.contains("Alice");
```

Complexity depends on implementation:

|Implementation|Complexity|
|---|---|
|ArrayList|O(n)|
|LinkedList|O(n)|
|HashSet|O(1) average|
|TreeSet|O(log n)|

---

## containsAll

Checks whether the collection contains every element of another collection.

```java
Collection<Integer> nums = List.of(1,2,3,4);

nums.containsAll(List.of(2,3));
```

---

## size()

Returns the number of elements in the collection.

```java
collection.size();
```

---

## isEmpty()

Checks whether the collection contains no elements.

```java
collection.isEmpty();
```

---

# 7. Iteration

## iterator()

Returns an `Iterator` used to traverse elements sequentially.

```java
Collection<String> names = new ArrayList<>(List.of("A","B","C"));

Iterator<String> it = names.iterator();

while(it.hasNext()) {
    System.out.println(it.next());
}
```

Enhanced for-loop internally uses an iterator.

```java
for(String name : names) {
    System.out.println(name);
}
```

---

## Iterator Removal

Correct way to remove elements during iteration:

```java
Iterator<String> it = names.iterator();

while(it.hasNext()) {

    String name = it.next();

    if(name.equals("Bob")) {
        it.remove();
    }
}
```

Direct modification during iteration may trigger:

```
ConcurrentModificationException
```

---

# 8. Array Conversion

## toArray()

Converts the collection into an array.

```java
Object[] arr = collection.toArray();
```

---

## toArray(T[] a)

Type-safe version.

```java
String[] arr = names.toArray(new String[0]);
```

---

# 9. Functional Operations (Java 8+)

## removeIf(Predicate)

Removes elements matching a condition.

```java
Collection<Integer> nums = new ArrayList<>(List.of(10,20,30));

nums.removeIf(n -> n > 15);
```

---

## forEach(Consumer)

Applies an action to each element.

```java
names.forEach(System.out::println);
```

---

## stream()

Creates a sequential stream from the collection.

```java
names.stream()
     .filter(n -> n.length() > 3)
     .forEach(System.out::println);
```

---

# 10. Equality and Hashing

Many collection operations depend on:

```
equals()
hashCode()
```

For example:

- contains
    
- remove
    
- HashSet membership
    

Custom objects stored in collections should normally override both methods.

---

# 11. Key Design Principles

### 1. Interface-based design

Programming should typically depend on interfaces rather than concrete implementations.

```java
Collection<String> names = new ArrayList<>();
```

This improves flexibility and decoupling.

---

### 2. Implementation determines performance

Different implementations provide different complexity characteristics.

Example:

```
ArrayList → fast random access
LinkedList → efficient insertion/removal
HashSet → fast membership check
TreeSet → sorted data
```

---

### 3. Correct collection choice matters

Selecting the proper collection type is a major design decision.

Examples:

```
Use Set for uniqueness
Use List for ordering
Use Queue for FIFO processing
```

---

# 12. Summary

`Collection` provides a unified abstraction for handling groups of objects.

The most important methods include:

```
add
addAll
remove
removeAll
retainAll
contains
containsAll
size
isEmpty
clear
iterator
toArray
removeIf
forEach
stream
```

Understanding `Collection` is essential for mastering the **Java Collection Framework**, because all higher-level data structures build upon this abstraction.