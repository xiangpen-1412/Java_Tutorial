___
## 1. Overview

`Map<K, V>` represents a key-value mapping structure.

Key properties:

- Keys are **unique**
    
- Values can be duplicated
    
- Lookup is performed via **key**
    

Conceptually:

```
K → V
```

---

## 2. Core Implementations

### HashMap (Most Common)

- Unordered
- Average **O(1)** for put/get
- Allows one `null` key

### LinkedHashMap

- Maintains insertion order
- Backed by HashMap + doubly linked list

### TreeMap

- Sorted by key
- Based on Red-Black Tree
- Operations: **O(log n)**

---

## 3. Basic Usage

```java
import java.util.*;

public class Demo {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();

        map.put("Alice", 90);
        map.put("Bob", 85);
        map.put("Alice", 100); // overwrite

        System.out.println(map.get("Alice")); // 100
    }
}
```

---

## 4. Key Operations

```java
map.put(key, value);      // insert or update
map.get(key);             // retrieve value
map.containsKey(key);     // check existence
map.remove(key);          // delete
```

---

## 5. Iteration Patterns

### Entry Set (Recommended)

```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " : " + entry.getValue());
}
```

### Key Set

```java
for (String key : map.keySet()) {
    System.out.println(key + " : " + map.get(key));
}
```

---

## 6. HashMap Internals (Important)

Structure:

```
Array + Linked List + Red-Black Tree (JDK 8+)
```

### put() logic (simplified)

```
1. Compute hash(key)
2. Locate bucket index
3. If empty → insert
4. If collision → traverse list/tree
5. If same key → overwrite value
```

### get() logic

```
1. Hash to find bucket
2. Traverse nodes
3. Use equals() to match key
```

---

## 7. hashCode & equals Contract

Rules:

```
1. Equal objects must have same hashCode
2. hashCode same does NOT guarantee equals
```

Why important:

- Determines bucket placement
    
- Affects performance and correctness
    

---

## 8. TreeMap Ordering

Ordering is based on:

- `Comparable`
    
- or `Comparator`
    

Rule:

```
compare(a, b) == 0 ⇒ treated as same key
```

---

## 9. Common Pitfalls

### 1. Mutable keys

```java
map.put(obj, value);
obj.field = newValue; // breaks lookup
```

### 2. Missing hashCode override

Leads to excessive collisions and incorrect behavior.

### 3. Using wrong implementation

- Need speed → HashMap
    
- Need order → LinkedHashMap
    
- Need sorting → TreeMap
    

---

## 10. Performance Summary

|Operation|HashMap|TreeMap|
|---|---|---|
|put|O(1)|O(log n)|
|get|O(1)|O(log n)|
|remove|O(1)|O(log n)|

---

## 11. When to Use

Use Map when:

- You need fast lookup by key
    
- You want to associate data pairs
    
- You need indexing behavior (like database keys)
    

---

## 12. Summary

Map is a key-value data structure where:

- Keys are unique
    
- HashMap provides fast access
    
- TreeMap provides ordered access
    
- Correct implementation of hashCode and equals is critical