___
## 1. Overview

`HashMap<K, V>` is the most widely used `Map` implementation in Java.

It stores data as **key-value pairs** and is optimized for **fast lookup by key**.

Core properties:

- Keys are **unique**
    
- Values can be duplicated
    
- Allows one `null` key and multiple `null` values
    
- Does **not** preserve insertion order
    
- Average complexity for `put`, `get`, and `remove` is **O(1)**
    

---

## 2. Where HashMap Fits in the Map Hierarchy

```text
Map
├── HashMap
├── LinkedHashMap
├── TreeMap
├── Hashtable
└── ConcurrentHashMap
```

### Typical positioning

- **HashMap** → default choice for general-purpose key-value storage
    
- **LinkedHashMap** → use when insertion/access order matters
    
- **TreeMap** → use when keys must remain sorted
    
- **ConcurrentHashMap** → use in concurrent environments
    

---

## 3. Core Use Cases

Use `HashMap` when you need:

- Fast lookup by identifier
    
- Index-like access by key
    
- Caching or lookup tables
    
- Frequency counting
    
- Grouping data by category
    

Common examples:

- `userId -> User`
    
- `word -> occurrence count`
    
- `productCode -> Product`
    
- `configKey -> configValue`
    

---

## 4. Basic Usage

```java
import java.util.HashMap;
import java.util.Map;

public class Demo {
    public static void main(String[] args) {
        Map<String, Integer> scores = new HashMap<>();

        scores.put("Alice", 90);
        scores.put("Bob", 85);
        scores.put("Alice", 100); // overwrite existing value

        System.out.println(scores.get("Alice")); // 100
        System.out.println(scores.containsKey("Bob")); // true
        System.out.println(scores.containsValue(85)); // true
        System.out.println(scores.size()); // 2
    }
}
```

---

## 5. Common Operations

```java
Map<String, Integer> map = new HashMap<>();

map.put("A", 1);              // insert or update
map.get("A");                 // retrieve value
map.getOrDefault("B", 0);     // retrieve with fallback
map.containsKey("A");         // key existence check
map.containsValue(1);          // value existence check
map.remove("A");              // remove by key
map.size();                    // number of entries
map.isEmpty();                 // empty check
map.clear();                   // remove all entries
```

---

## 6. Iteration Patterns

### 6.1 entrySet() — recommended

```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " -> " + entry.getValue());
}
```

Why recommended:

- Direct access to both key and value
    
- Matches the internal storage model of Map
    
- Avoids an extra lookup
    

### 6.2 keySet()

```java
for (String key : map.keySet()) {
    System.out.println(key + " -> " + map.get(key));
}
```

This works, but it is less direct because value retrieval requires another lookup.

### 6.3 values()

```java
for (Integer value : map.values()) {
    System.out.println(value);
}
```

Use this only when keys are irrelevant.

---

## 7. Entry Explained

A `Map` does not store isolated keys or isolated values. It stores **entries**.

```java
Map.Entry<K, V>
```

An entry represents exactly one key-value pair.

Example:

```text
"Alice" -> 90
```

That single mapping is one entry.

In `HashMap`, the internal node structure is conceptually similar to:

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
}
```

Fields:

- `hash` → cached hash of the key
    
- `key` → unique identifier
    
- `value` → associated data
    
- `next` → next node in the same bucket chain
    

👉 Each node is one entry.

---

## 8. Internal Structure

`HashMap` is implemented as:

```text
Array (table) + Linked List + Red-Black Tree (JDK 8+)
```

### Structure model

- The outer container is an array called `table`
    
- Each array slot is a **bucket**
    
- Each bucket may contain:
    
    - no node
        
    - one node
        
    - a linked list of nodes
        
    - a red-black tree of nodes
        

Example conceptual layout:

```text
table[0]
table[1]
table[2] -> [A,1]
table[3]
table[4] -> [B,2] -> [C,3]
```

---

## 9. Hash → Bucket Index Calculation (Critical)

HashMap does not use `hashCode()` directly as the array index.

### Step 1: hash spreading

```java
int h = key.hashCode();
int hash = h ^ (h >>> 16);
```

Purpose:

- Mix high bits into low bits
    
- Improve bucket distribution
    
- Reduce collision probability caused by poor low-bit distribution
    

### Step 2: calculate bucket index

```java
index = (n - 1) & hash;
```

Where:

- `n` = current table length
    
- `n` must be a power of 2
    

Why bitwise AND instead of `%`:

- Faster than modulo
    
- Works cleanly when capacity is a power of 2
    

---

## 10. Why Collisions Happen

A collision means:

```text
Different keys end up in the same bucket
```

This can happen even when their hash values are different.

Reason:

- The hash space is very large
    
- The number of buckets is limited
    
- The bucket index uses only part of the hash information
    

Example:

```text
hash1 = 0001 0011
hash2 = 1111 0011
index = 0011 for both
```

So both entries fall into the same bucket.

Collision is not a bug. It is an expected property of hash tables.

---

## 11. put() Logic — Full Flow

```java
map.put(key, value);
```

Conceptually, `put` means:

- If the key does not exist → insert a new entry
    
- If the key already exists → overwrite the old value
    

### Full flow

```text
1. Initialize table if needed
2. Compute hash
3. Compute bucket index
4. If bucket is empty → insert new node
5. If bucket is not empty:
   - check whether the key already exists
   - if yes → overwrite value
   - if no → append new node
6. If chain becomes too long → treeify when eligible
7. If size exceeds threshold → resize
```

### Key matching rule

```java
if (node.hash == hash && (node.key == key || key.equals(node.key)))
```

This is where identity is decided.

---

## 12. get() Logic — Full Flow

```java
map.get(key);
```

Conceptually, `get` means:

- Compute where the key should be stored
    
- Search only inside that bucket
    
- Return the value if found
    

### Full flow

```text
1. Compute hash
2. Compute bucket index
3. If bucket is empty → return null
4. Check the first node
5. If not matched → traverse linked list or tree
6. If matched → return value
7. If not found → return null
```

### Matching rule

```java
if (node.hash == hash && (node.key == key || key.equals(node.key)))
```

---

## 13. When equals() Is Actually Used

This is one of the most important HashMap rules.

### Matching pipeline

```text
1. hashCode() → compute raw hash source
2. hash spreading → final hash
3. bucket index → locate bucket
4. hash comparison → fast candidate filtering
5. equals() → final identity confirmation
```

Key conclusion:

```text
equals() is used only after HashMap has already narrowed the search to candidate nodes in the target bucket.
```

In practice:

- `put()` uses `equals()` to decide whether to overwrite or insert
    
- `get()` uses `equals()` to decide whether the current node is the requested key
    

---

## 14. hashCode() and equals() Contract

For custom key classes, `hashCode()` and `equals()` must be consistent.

Rules:

- If two objects are equal, they must have the same hashCode
    
- If two objects have the same hashCode, they are not necessarily equal
    

Why this matters:

- `hashCode()` decides candidate bucket placement
    
- `equals()` decides logical identity
    

If these methods are inconsistent, `HashMap` may fail to retrieve values correctly.

---

## 15. Example with a Custom Key

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

class Student {
    private final int id;
    private final String name;

    public Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Student)) return false;
        Student student = (Student) o;
        return id == student.id;
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }

    @Override
    public String toString() {
        return "Student{id=" + id + ", name='" + name + "'}";
    }
}

public class Demo {
    public static void main(String[] args) {
        Map<Student, String> map = new HashMap<>();

        Student s1 = new Student(101, "Alice");
        Student s2 = new Student(101, "Alicia");

        map.put(s1, "First Insert");
        map.put(s2, "Overwrite Because Same Logical Key");

        System.out.println(map.size()); // 1
        System.out.println(map.get(s1)); // Overwrite Because Same Logical Key
    }
}
```

Explanation:

- `s1` and `s2` are different objects in memory
    
- But `equals()` and `hashCode()` define them as the same logical key
    
- Therefore the second `put` overwrites the first entry
    

---

## 16. Example of Collision + Overwrite

```java
import java.util.HashMap;
import java.util.Map;

public class Demo {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();

        map.put("Alice", 90);
        map.put("Bob", 85);
        map.put("Alice", 100);

        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " -> " + entry.getValue());
        }
    }
}
```

Logical result:

```text
Alice -> 100
Bob -> 85
```

What happened:

- First `put` inserted `Alice`
    
- Second `put` inserted `Bob`
    
- Third `put` found the same logical key `Alice` and overwrote the value
    

---

## 17. Resize Mechanism

HashMap resizes automatically when the number of entries exceeds a threshold.

### Default values

```text
capacity = 16
loadFactor = 0.75
threshold = 12
```

Trigger condition:

```text
size > capacity * loadFactor
```

Action:

```text
capacity doubles
entries are redistributed into the new table
```

Why resizing is needed:

- Prevent buckets from becoming too crowded
    
- Keep average operation cost near O(1)
    

---

## 18. Treeification (JDK 8+)

If too many entries accumulate in the same bucket, HashMap may convert the linked list into a red-black tree.

Typical condition:

```text
bucket size >= 8 and table capacity >= 64
```

Why:

- Linked-list lookup is O(n)
    
- Tree lookup is O(log n)
    

This optimization protects performance under heavy collision scenarios.

---

## 19. Performance Summary

|Operation|Average|Worst Case|
|---|---|---|
|put|O(1)|O(n) / O(log n)|
|get|O(1)|O(n) / O(log n)|
|remove|O(1)|O(n) / O(log n)|

Notes:

- Worst case depends on collision structure
    
- JDK 8+ improves worst-case behavior by treeification
    

---

## 20. Common Pitfalls

### 20.1 Mutable keys

```java
map.put(user, value);
user.name = "new";
map.get(user); // may fail
```

Reason:

- Key state changed after insertion
    
- hashCode and/or equals behavior changed
    
- The key can no longer be found reliably
    

### 20.2 Overriding equals without hashCode

This breaks the contract required by HashMap.

### 20.3 Poor hashCode implementation

Leads to excessive collisions and degraded performance.

### 20.4 Assuming iteration order

`HashMap` does not guarantee insertion order or sorted order.

---

## 21. Best Practices

- Override **both** `equals()` and `hashCode()` for custom key classes
    
- Prefer immutable keys
    
- Use `HashMap` as the default `Map` unless ordering is required
    
- Use `entrySet()` for full key-value iteration
    
- Do not rely on iteration order
    

---

## 22. HashMap vs Other Map Implementations

|Implementation|Ordering|Performance|Typical Use|
|---|---|---|---|
|HashMap|Unordered|O(1) average|General-purpose map|
|LinkedHashMap|Insertion/access order|O(1) average|Ordered traversal / LRU-style logic|
|TreeMap|Sorted by key|O(log n)|Sorted key access|
|ConcurrentHashMap|Unordered|Concurrent optimized|Multi-threaded access|

---

## 23. Interview-Level Summary

HashMap is a hash table–based `Map` implementation that stores data as entries inside buckets.

Its core mechanism is:

- `hashCode()` produces the original hash source
    
- hash spreading improves distribution
    
- `(n - 1) & hash` selects the bucket
    
- collisions are handled by linked lists or red-black trees
    
- `equals()` performs the final key identity check
    

As a result, HashMap provides O(1) average-time access for put/get/remove and is the default choice for most key-value storage scenarios in Java.