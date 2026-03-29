___
# 1. What is HashSet

`HashSet` is a concrete implementation of the **Set interface**.

Characteristics:

- No duplicate elements
    
- Unordered
    
- Fast lookup
    
- Backed by **HashMap** internally
    

Declaration:

```java
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, Serializable
```

Internally:

```
HashSet
   ↓
HashMap
```

Each element in the HashSet is stored as a **key** in the HashMap.

---

# 2. Internal Structure

```
HashSet
   ↓
HashMap
   ↓
Hash Table (array)
   ↓
Bucket
   ↓
LinkedList / Red Black Tree
```

When collisions occur, elements are stored in the same bucket.

---

# 3. How HashSet Prevents Duplicates

HashSet relies on:

```
hashCode()
+
equals()
```

Insertion steps:

1. compute hash
    
2. find bucket index
    
3. compare with existing elements
    
4. use equals() to detect duplicates
    

---

# 4. Basic Example

```java
import java.util.HashSet;

public class HashSetExample {

    public static void main(String[] args) {

        HashSet<String> set = new HashSet<>();

        set.add("Apple");
        set.add("Banana");
        set.add("Apple");

        System.out.println(set);
    }
}
```

Output:

```
[Apple, Banana]
```

Duplicate elements are ignored.

---

# 5. Custom Object Example

```java
import java.util.HashSet;

class Student {

    String id;

    Student(String id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object o) {
        Student s = (Student) o;
        return this.id.equals(s.id);
    }

    @Override
    public int hashCode() {
        return id.hashCode();
    }
}

public class HashSetObjectExample {

    public static void main(String[] args) {

        HashSet<Student> set = new HashSet<>();

        set.add(new Student("S001"));
        set.add(new Student("S001"));

        System.out.println(set.size());
    }
}
```

Output:

```
1
```

Because two objects are considered equal.

---

# 6. Common HashSet Methods

|Method|Description|
|---|---|
|add(E e)|add element|
|remove(Object o)|remove element|
|contains(Object o)|check existence|
|size()|element count|
|clear()|remove all elements|

Example:

```java
import java.util.HashSet;

public class HashSetMethodsExample {

    public static void main(String[] args) {

        HashSet<Integer> set = new HashSet<>();

        set.add(10);
        set.add(20);
        set.add(30);

        System.out.println(set.contains(20));

        set.remove(20);

        System.out.println(set);
    }
}
```

---

# 7. Time Complexity

|Operation|Average Complexity|
|---|---|
|add|O(1)|
|remove|O(1)|
|contains|O(1)|

Worst case:

```
O(log n)
```

when buckets become trees.

---

# 8. When to Use HashSet

Use HashSet when:

- you need **unique elements**
    
- order does not matter
    
- fast membership checking is required