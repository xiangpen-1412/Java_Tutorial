___
## 1. Definition

`TreeMap` is a `NavigableMap` implementation based on a **Red-Black Tree**.

It stores entries in **sorted key order**, not insertion order and not hash bucket order.

Key ordering is determined by:

- the key's **natural ordering** (`Comparable`)
    
- or a custom **`Comparator`** provided at construction time
    

---

## 2. Core Characteristics

- Stores key-value pairs
    
- Keys are kept **sorted**
    
- Backed by a **Red-Black Tree**
    
- `put`, `get`, `remove`, `containsKey` are typically `O(log n)`
    
- Does **not** use hashing for lookup
    
- Duplicate keys are determined by **comparison result == 0**
    
- Supports range queries and nearest-key navigation
    
- Not thread-safe
    

---

## 3. Internal Data Structure

Unlike `HashMap`, `TreeMap` does **not** use buckets or hashing.

Its entries are stored as nodes in a balanced binary search tree.

Each node conceptually contains:

- `key`
    
- `value`
    
- `left`
    
- `right`
    
- `parent`
    
- node color (`red` or `black`)
    

Because the tree is balanced, the height remains approximately `O(log n)`, which keeps search, insertion, and deletion efficient.

---

## 4. Ordering Rules

### 4.1 Natural Ordering

If no comparator is provided, keys must implement `Comparable`.

Example:

```java
TreeMap<Integer, String> map = new TreeMap<>();
```

`Integer` already implements `Comparable<Integer>`, so the keys can be sorted naturally.

---

### 4.2 Custom Comparator

A custom comparator can define a different ordering rule.

```java
TreeMap<String, Integer> map = new TreeMap<>(Comparator.reverseOrder());
```

This map sorts keys in descending order.

---

## 5. How TreeMap Determines Key Uniqueness

This is one of the most important points.

`TreeMap` does **not** primarily use `equals()` to determine whether two keys are the same.

Instead, two keys are considered the same key if:

```java
compareTo(...) == 0
```

or

```java
comparator.compare(...) == 0
```

That means:

- comparison result `0` -> same logical key in the map
    
- comparison result `< 0` -> go left
    
- comparison result `> 0` -> go right
    

So in `TreeMap`, the comparison rule determines both:

- **ordering**
    
- **key uniqueness**
    

---

## 6. Basic Example

```java
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();

        map.put(30, "C");
        map.put(10, "A");
        map.put(20, "B");

        System.out.println(map);
    }
}
```

Output:

```java
{10=A, 20=B, 30=C}
```

The insertion order was:

```java
30 -> 10 -> 20
```

But the stored order is:

```java
10 -> 20 -> 30
```

because `TreeMap` always maintains sorted key order.

---

## 7. Comparison with HashMap

### HashMap

- Uses `hashCode()` and `equals()`
    
- Average `O(1)` lookup
    
- No sorted order
    
- Best for fast general-purpose lookup
    

### TreeMap

- Uses `compareTo()` or `Comparator`
    
- `O(log n)` lookup
    
- Maintains sorted key order
    
- Best for ordered data, range queries, and nearest-key queries
    

---

## 8. Common Operations

### 8.1 put

```java
map.put(key, value);
```

Process:

1. Start from the root
    
2. Compare `key` with current node key
    
3. If smaller, go left
    
4. If larger, go right
    
5. If comparison result is `0`, replace the existing value
    
6. Insert a new node if a null position is reached
    
7. Rebalance the Red-Black Tree if necessary
    

Time complexity: `O(log n)`

---

### 8.2 get

```java
map.get(key);
```

Process:

1. Start from the root
    
2. Compare target key with current node key
    
3. Move left or right accordingly
    
4. Return the value when comparison result is `0`
    

Time complexity: `O(log n)`

---

### 8.3 remove

```java
map.remove(key);
```

Process:

1. Locate the node
    
2. Remove it according to tree deletion rules
    
3. Rebalance the Red-Black Tree if necessary
    

Time complexity: `O(log n)`

---

## 9. Example with Custom Key Type

```java
import java.util.TreeMap;

class Student implements Comparable<Student> {
    private String studentId;
    private String name;

    public Student(String studentId, String name) {
        this.studentId = studentId;
        this.name = name;
    }

    @Override
    public int compareTo(Student other) {
        return this.studentId.compareTo(other.studentId);
    }

    @Override
    public String toString() {
        return "Student{id='" + studentId + "', name='" + name + "'}";
    }
}

public class Main {
    public static void main(String[] args) {
        TreeMap<Student, String> map = new TreeMap<>();

        map.put(new Student("S002", "Bob"), "Value-B");
        map.put(new Student("S001", "Alice"), "Value-A");
        map.put(new Student("S003", "Carol"), "Value-C");

        System.out.println(map);
    }
}
```

The keys will be sorted by `studentId`.

---

## 10. Very Important Pitfall: `compareTo()` Defines Equality in TreeMap

Example:

```java
import java.util.TreeMap;

class Student implements Comparable<Student> {
    private String studentId;
    private String name;

    public Student(String studentId, String name) {
        this.studentId = studentId;
        this.name = name;
    }

    @Override
    public int compareTo(Student other) {
        return this.studentId.compareTo(other.studentId);
    }

    @Override
    public String toString() {
        return "Student{id='" + studentId + "', name='" + name + "'}";
    }
}

public class Main {
    public static void main(String[] args) {
        TreeMap<Student, String> map = new TreeMap<>();

        map.put(new Student("S001", "Alice"), "First");
        map.put(new Student("S001", "Bob"), "Second");

        System.out.println(map.size());
        System.out.println(map);
    }
}
```

Output:

```java
1
```

Why?

Because the two keys compare as `0`, so `TreeMap` treats them as the same key.

This is very different from the mental model many people build from `HashMap`.

---

## 11. Comparator Pitfall

A comparator controls both order and uniqueness.

Example:

```java
import java.util.Comparator;
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) {
        TreeMap<String, Integer> map =
                new TreeMap<>(Comparator.comparingInt(String::length));

        map.put("cat", 1);
        map.put("dog", 2);
        map.put("elephant", 3);

        System.out.println(map);
    }
}
```

`"cat"` and `"dog"` both have length `3`.

So:

```java
Comparator.comparingInt(String::length).compare("cat", "dog") == 0
```

That means one entry overwrites the other.

So a comparator must be designed carefully.

---

## 12. NavigableMap Features

`TreeMap` is powerful because it supports navigation by key order.

### 12.1 firstKey / lastKey

```java
map.firstKey();
map.lastKey();
```

- `firstKey()` -> smallest key
    
- `lastKey()` -> largest key
    

---

### 12.2 higherKey / lowerKey

```java
map.higherKey(k);
map.lowerKey(k);
```

- `higherKey(k)` -> smallest key strictly greater than `k`
    
- `lowerKey(k)` -> greatest key strictly less than `k`
    

---

### 12.3 ceilingKey / floorKey

```java
map.ceilingKey(k);
map.floorKey(k);
```

- `ceilingKey(k)` -> smallest key greater than or equal to `k`
    
- `floorKey(k)` -> greatest key less than or equal to `k`
    

---

## 13. Navigation Example

```java
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();

        map.put(10, "A");
        map.put(20, "B");
        map.put(30, "C");
        map.put(40, "D");

        System.out.println(map.firstKey());     // 10
        System.out.println(map.lastKey());      // 40
        System.out.println(map.higherKey(20));  // 30
        System.out.println(map.lowerKey(20));   // 10
        System.out.println(map.ceilingKey(25)); // 30
        System.out.println(map.floorKey(25));   // 20
    }
}
```

---

## 14. Range Views

### 14.1 subMap

```java
map.subMap(fromKey, true, toKey, true);
```

Returns a view of the specified key range.

---

### 14.2 headMap

```java
map.headMap(toKey, inclusive);
```

Returns a view of keys less than (or optionally equal to) `toKey`.

---

### 14.3 tailMap

```java
map.tailMap(fromKey, inclusive);
```

Returns a view of keys greater than (or optionally equal to) `fromKey`.

---

## 15. Range View Example

```java
import java.util.NavigableMap;
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();

        map.put(10, "A");
        map.put(20, "B");
        map.put(30, "C");
        map.put(40, "D");
        map.put(50, "E");

        NavigableMap<Integer, String> sub = map.subMap(20, true, 40, true);

        System.out.println(sub); // {20=B, 30=C, 40=D}

        sub.put(35, "X");

        System.out.println(map); // original map is also affected
    }
}
```

Important:

`subMap`, `headMap`, and `tailMap` return **views**, not independent copies.

That means modifications are reflected in the original map.

---

## 16. Descending Order View

```java
map.descendingMap();
```

Returns a reverse-order view of the same map.

Example:

```java
import java.util.NavigableMap;
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();

        map.put(10, "A");
        map.put(20, "B");
        map.put(30, "C");

        NavigableMap<Integer, String> descending = map.descendingMap();

        System.out.println(map);        // {10=A, 20=B, 30=C}
        System.out.println(descending); // {30=C, 20=B, 10=A}
    }
}
```

Again, this is a **view**, not a copy.

---

## 17. Null Handling

### 17.1 Null Keys

`TreeMap` generally does **not** allow `null` keys when natural ordering is used, because keys must be compared.

A `null` key usually causes `NullPointerException`.

### 17.2 Null Values

`null` values are generally allowed, but in real engineering code, relying on `null` values often makes the API semantics less clear.

---

## 18. Iteration Order

Iteration over `TreeMap` entries follows **sorted key order**.

Example:

```java
import java.util.Map;
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();

        map.put(50, "E");
        map.put(10, "A");
        map.put(30, "C");

        for (Map.Entry<Integer, String> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " -> " + entry.getValue());
        }
    }
}
```

Output:

```java
10 -> A
30 -> C
50 -> E
```

---

## 19. Time Complexity

|Operation|Time Complexity|
|---|---|
|`put`|`O(log n)`|
|`get`|`O(log n)`|
|`remove`|`O(log n)`|
|`containsKey`|`O(log n)`|
|`firstKey`|`O(log n)`|
|`lastKey`|`O(log n)`|

Because the tree remains balanced, the height is bounded by `O(log n)`.

---

## 20. When to Use TreeMap

Use `TreeMap` when you need:

- sorted keys
    
- nearest-key lookup
    
- range queries
    
- first / last key retrieval
    
- ordered traversal
    
- boundary-based search
    

Typical scenarios:

- score ranking
    
- timeline indexing
    
- interval lookup
    
- ordered configuration tables
    
- scheduling boundaries
    
- prefix/range style key navigation
    

---

## 21. When Not to Use TreeMap

Do not use `TreeMap` when:

- you only need fast general lookup with no ordering requirement
    
- you do not have a stable comparison rule
    
- the key is not naturally comparable and no proper comparator is available
    
- you need maximum average lookup speed and ordering is irrelevant
    

In those cases, `HashMap` is usually a better choice.

---

## 22. TreeMap vs LinkedHashMap vs HashMap

|Feature|HashMap|LinkedHashMap|TreeMap|
|---|---|---|---|
|Ordering|No guaranteed order|Insertion order or access order|Sorted key order|
|Core mechanism|Hash table|Hash table + doubly linked list|Red-Black Tree|
|Main equality rule for key replacement|`hashCode` + `equals`|`hashCode` + `equals`|`compareTo == 0` or `Comparator.compare == 0`|
|`put/get/remove` average complexity|`O(1)`|`O(1)`|`O(log n)`|
|Supports nearest/range queries|No|No|Yes|

---

## 23. Engineering Pitfalls

### 23.1 Inconsistent comparison logic

If `compareTo()` or `Comparator` is inconsistent with logical equality, the map may overwrite entries unexpectedly.

### 23.2 Comparator that ignores important fields

If the comparator only compares one field, objects differing in other fields may still collapse into a single key.

### 23.3 Mutable key fields

If key fields used in comparison are modified after insertion, the tree ordering semantics become invalid.

This is a severe bug pattern.

Keys used in ordered maps should be effectively immutable with respect to comparison fields.

---

## 24. Best Practices

- Prefer immutable keys
    
- Ensure comparison logic is stable
    
- Ensure comparison logic is consistent with your intended key uniqueness semantics
    
- Use `TreeMap` only when ordering or navigation is actually needed
    
- Do not use `TreeMap` as a default replacement for `HashMap`
    

---

## 25. Summary

`TreeMap` is a sorted map implementation backed by a Red-Black Tree.

Its key properties are:

- keys are always sorted
    
- it uses `compareTo()` or `Comparator` instead of hashing
    
- key uniqueness is determined by comparison result `0`
    
- major operations are `O(log n)`
    
- it is especially useful for ordered lookup, nearest-key navigation, and range queries
    

In practice:

- choose `HashMap` for fast unordered lookup
    
- choose `LinkedHashMap` when order must follow insertion or access
    
- choose `TreeMap` when key order and navigational operations matter