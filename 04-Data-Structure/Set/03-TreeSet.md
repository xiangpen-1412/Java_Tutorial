___
## 1. Overview

`TreeSet` is a sorted implementation of the `Set` interface backed by a Red-Black Tree.

Key properties:

- Elements are **automatically sorted**
    
- **No duplicates allowed**
    
- Operations run in **O(log n)** time
    

Internally:

```
TreeSet<E> -> TreeMap<E, Object>
```

---

## 2. Ordering Mechanism

TreeSet determines both **order and uniqueness** using:

- `Comparable` (natural ordering)
    
- or `Comparator` (custom ordering)
    

Rule:

```
compare(a, b) == 0  => treated as duplicate
```

---

## 3. Basic Usage

```java
import java.util.TreeSet;

public class Demo {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>();

        set.add(5);
        set.add(1);
        set.add(3);

        System.out.println(set); // [1, 3, 5]
    }
}
```

---

## 4. Custom Object Example (Comparator)

```java
import java.util.TreeSet;

class Student {
    int id;
    String name;

    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return id + "-" + name;
    }
}

public class Demo {
    public static void main(String[] args) {

        TreeSet<Student> set = new TreeSet<>(
            (a, b) -> a.id - b.id
        );

        set.add(new Student(2, "Bob"));
        set.add(new Student(1, "Alice"));
        set.add(new Student(2, "Charlie")); // duplicate id

        System.out.println(set);
        // Output: [1-Alice, 2-Bob]
    }
}
```

---

## 5. Key Operations

```java
TreeSet<Integer> set = new TreeSet<>();

set.add(10);
set.add(20);
set.add(30);

set.first();    // 10
set.last();     // 30

set.higher(20); // 30
set.lower(20);  // 10

set.ceiling(25); // 30
set.floor(25);   // 20
```

---

## 6. Range Queries

```java
TreeSet<Integer> set = new TreeSet<>();
set.add(10);
set.add(20);
set.add(30);
set.add(40);

System.out.println(set.subSet(15, 35)); // [20, 30]
System.out.println(set.headSet(30));    // [10, 20]
System.out.println(set.tailSet(20));    // [20, 30, 40]
```

---

## 7. Common Pitfalls

### 1. Not using equals for uniqueness

TreeSet relies on comparison, NOT equals/hashCode.

### 2. Inconsistent comparison logic

```java
(a, b) -> 0
```

All elements will be treated as duplicates.

### 3. Mutating key fields

```java
Student s = new Student(10, "A");
set.add(s);

s.id = 1; // breaks tree ordering
```

### 4. Null elements

```java
set.add(null); // throws NullPointerException
```

---

## 8. When to Use TreeSet

Use TreeSet when you need:

- Sorted unique elements
    
- Range queries (subSet, headSet, tailSet)
    
- Ordered data structure with predictable performance
    

Avoid when:

- You need O(1) lookup → use `HashSet`
    

---

## 9. Summary

TreeSet is a sorted set backed by a Red-Black Tree.  
It guarantees:

- Ordering via compare logic
    
- Uniqueness via comparison
    
- O(log n) performance for all core operations