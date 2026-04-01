___
# 1. What is LinkedHashSet

`LinkedHashSet` is a concrete implementation of the **Set interface** that preserves the **insertion order** of elements while still guaranteeing **uniqueness**.

It combines the characteristics of:

```
HashSet  +  Linked List
```

Key properties:

- No duplicate elements
    
- Maintains insertion order
    
- Average O(1) time complexity
    
- Internally backed by **LinkedHashMap**
    

Declaration:

```java
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, Serializable
```

---

# 2. Internal Structure

Although `LinkedHashSet` extends `HashSet`, internally it uses:

```
LinkedHashMap
```

Structure:

```
LinkedHashSet
      ↓
LinkedHashMap
      ↓
Hash Table + Doubly Linked List
```

Each entry exists in two structures simultaneously:

1. **Hash table bucket** (for fast lookup)
    
2. **Doubly linked list** (for maintaining order)
    

Simplified node structure:

```
class LinkedHashMap.Entry<K,V> {

    int hash;
    K key;
    V value;

    Entry<K,V> next;   // bucket chain

    Entry<K,V> before; // linked list
    Entry<K,V> after;  // linked list
}
```

---

# 3. Order Preservation

Unlike `HashSet`, iteration order in `LinkedHashSet` follows **insertion order**.

Example:

```java
import java.util.LinkedHashSet;

public class LinkedHashSetExample {

    public static void main(String[] args) {

        LinkedHashSet<Integer> set = new LinkedHashSet<>();

        set.add(3);
        set.add(1);
        set.add(2);
        set.add(1);

        System.out.println(set);
    }
}
```

Output:

```
[3, 1, 2]
```

Duplicate elements are ignored and the original insertion order is preserved.

---

# 4. Basic Operations

Common methods inherited from `Set`:

|Method|Description|
|---|---|
|add(E e)|add element|
|remove(Object o)|remove element|
|contains(Object o)|check existence|
|size()|number of elements|
|clear()|remove all elements|

Example:

```java
import java.util.LinkedHashSet;

public class LinkedHashSetOperations {

    public static void main(String[] args) {

        LinkedHashSet<String> languages = new LinkedHashSet<>();

        languages.add("Java");
        languages.add("Python");
        languages.add("Go");

        System.out.println(languages.contains("Java"));

        languages.remove("Python");

        System.out.println(languages);
    }
}
```

---

# 5. Iteration Order

Traversal always follows **insertion order**.

Example:

```java
import java.util.LinkedHashSet;

public class LinkedHashSetIteration {

    public static void main(String[] args) {

        LinkedHashSet<String> set = new LinkedHashSet<>();

        set.add("A");
        set.add("B");
        set.add("C");

        for (String s : set) {
            System.out.println(s);
        }
    }
}
```

Output:

```
A
B
C
```

---

# 6. HashSet vs LinkedHashSet

|Feature|HashSet|LinkedHashSet|
|---|---|---|
|Ordering|Unordered|Insertion order|
|Internal Structure|HashMap|LinkedHashMap|
|Performance|O(1)|O(1)|
|Memory Usage|Lower|Slightly higher|

The additional linked list structure allows `LinkedHashSet` to maintain order.

---

# 7. Typical Use Case

`LinkedHashSet` is useful when you need:

- **Uniqueness of elements**
    
- **Stable iteration order**
    
- **Fast lookup performance**
    

Example: removing duplicates while preserving order.

```java
import java.util.LinkedHashSet;
import java.util.List;

public class RemoveDuplicatesExample {

    public static void main(String[] args) {

        List<Integer> list = List.of(5,2,3,2,5,1);

        LinkedHashSet<Integer> set = new LinkedHashSet<>(list);

        System.out.println(set);
    }
}
```

Output:

```
[5, 2, 3, 1]
```

---

# 8. Time Complexity

|Operation|Average Complexity|
|---|---|
|add|O(1)|
|remove|O(1)|
|contains|O(1)|

Worst case (rare):

```
O(log n)
```

when buckets become balanced trees.

---

# 9. Summary

`LinkedHashSet` provides a balance between **fast hash-based operations** and **predictable iteration order**.

```
LinkedHashSet = Hash Table + Doubly Linked List
```

It is commonly used when element uniqueness is required but iteration order must remain stable.