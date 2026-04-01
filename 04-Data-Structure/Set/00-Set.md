___
## 1. What is a Set

In Java, **Set** is an interface in the **Java Collections Framework** that represents a collection of **unique elements**.

Key characteristics:

- No duplicate elements
    
- Not index-based (unlike List)
    
- Usually optimized for membership checking
    

```java
import java.util.Set;
import java.util.HashSet;

public class SetExample {

    public static void main(String[] args) {
        Set<String> set = new HashSet<>();

        set.add("Alice");
        set.add("Bob");
        set.add("Alice");

        System.out.println(set);
    }
}
```

Output:

```
[Alice, Bob]
```

The second "Alice" is ignored because a Set does not allow duplicate elements.

---

# 2. Set Interface Hierarchy

```
Collection
   │
   └── Set
         │
         ├── HashSet
         ├── LinkedHashSet
         └── TreeSet
```

All Set implementations guarantee **element uniqueness**, but they differ in ordering and internal structure.

---

# 3. Common Set Implementations

## 3.1 HashSet

- Backed by a **Hash Table** (actually a HashMap internally)
    
- Does **not maintain order**
    
- Fast operations
    

Average complexity:

|Operation|Complexity|
|---|---|
|add|O(1)|
|remove|O(1)|
|contains|O(1)|

---

## 3.2 LinkedHashSet

- Maintains **insertion order**
    
- Internally uses
    

```
Hash Table + Linked List
```

Example:

```java
import java.util.LinkedHashSet;

public class LinkedHashSetExample {

    public static void main(String[] args) {
        LinkedHashSet<String> set = new LinkedHashSet<>();

        set.add("C");
        set.add("A");
        set.add("B");

        System.out.println(set);
    }
}
```

Output:

```
[C, A, B]
```

Insertion order is preserved.

---

## 3.3 TreeSet

- Stores elements in **sorted order**
    
- Implemented using a **Red-Black Tree**
    

Time complexity:

|Operation|Complexity|
|---|---|
|add|O(log n)|
|remove|O(log n)|
|contains|O(log n)|

Example:

```java
import java.util.TreeSet;

public class TreeSetExample {

    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>();

        set.add(5);
        set.add(1);
        set.add(3);

        System.out.println(set);
    }
}
```

Output:

```
[1, 3, 5]
```

Elements are automatically sorted.

---

# 4. Common Set Methods

|Method|Description|
|---|---|
|add(E e)|add element|
|remove(Object o)|remove element|
|contains(Object o)|check if element exists|
|size()|number of elements|
|clear()|remove all elements|

Example:

```java
import java.util.HashSet;

public class SetMethodsExample {

    public static void main(String[] args) {

        HashSet<String> set = new HashSet<>();

        set.add("Java");
        set.add("Python");

        System.out.println(set.contains("Java"));

        set.remove("Python");

        System.out.println(set);
    }
}
```

