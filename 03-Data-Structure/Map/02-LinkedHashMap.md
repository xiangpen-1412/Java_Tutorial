___
## 1. Overview

`LinkedHashMap<K, V>` is an ordered implementation of the `Map` interface.

It extends `HashMap` and adds a **doubly linked list** across all entries so that iteration follows a predictable order.

Core properties:

- Stores key-value pairs
    
- Keys are unique
    
- Values can be duplicated
    
- Maintains **insertion order** by default
    
- Can also maintain **access order**
    
- Average complexity for `put`, `get`, and `remove` is **O(1)**
    

Conceptually:

```text
LinkedHashMap = HashMap + Doubly Linked List
```

---

## 2. Constructor Parameters Explained

A common constructor is:

```java
new LinkedHashMap<>(16, 0.75f, true)
```

Parameters:

### 2.1 initialCapacity

```java
16
```

Initial bucket array capacity.

### 2.2 loadFactor

```java
0.75f
```

This controls **when resizing happens**.

Threshold formula:

```text
threshold = capacity × loadFactor
```

So if capacity is `16` and load factor is `0.75`:

```text
threshold = 16 × 0.75 = 12
```

That means when the number of entries grows beyond `12`, the internal table will resize.

Why `0.75` is commonly used:

- Good balance between memory usage and collision rate
    
- Too low → wastes space
    
- Too high → causes more collisions
    

### 2.3 accessOrder

```java
true
```

Controls the iteration order:

- `false` → insertion order
    
- `true` → access order
    

When `accessOrder = true`, operations such as `get()` or successful `put()` updates move the accessed entry to the tail.

This is the basis of **LRU cache** behavior.

---

## 3. Internal Structure

`LinkedHashMap` inherits the hash table structure of `HashMap` and adds a doubly linked list.

So it maintains **two structures at the same time**:

### 3.1 Hash structure

Used for fast lookup.

```text
table[index] -> node -> node
```

### 3.2 Global doubly linked list

Used for maintaining iteration order.

```text
head <-> A <-> B <-> C <-> tail
```

Each entry conceptually contains:

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before;
    Entry<K,V> after;
}
```

Fields:

- `next` → used inside the bucket chain
    
- `before` / `after` → used inside the global order chain
    

---

## 4. Ordering Modes

## 4.1 Insertion Order (default)

```java
Map<String, Integer> map = new LinkedHashMap<>();
```

Entries are iterated in the order they were inserted.

Example:

```java
map.put("A", 1);
map.put("B", 2);
map.put("C", 3);
```

Iteration result:

```text
A -> B -> C
```

---

## 4.2 Access Order

```java
Map<String, Integer> map = new LinkedHashMap<>(16, 0.75f, true);
```

Entries are reordered when accessed.

Example:

```java
map.put("A", 1);
map.put("B", 2);
map.put("C", 3);
map.get("A");
```

Iteration result:

```text
B -> C -> A
```

Because `A` was the most recently accessed entry.

---

## 5. Basic Usage

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class Demo {
    public static void main(String[] args) {
        Map<String, Integer> map = new LinkedHashMap<>();

        map.put("Alice", 90);
        map.put("Bob", 85);
        map.put("Charlie", 95);

        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " -> " + entry.getValue());
        }
    }
}
```

---

## 6. Common Operations

```java
Map<String, Integer> map = new LinkedHashMap<>();

map.put("A", 1);
map.get("A");
map.containsKey("A");
map.remove("A");
map.size();
map.clear();
```

Functionally, these operations are almost the same as `HashMap`.  
The key difference is that `LinkedHashMap` also maintains order.

---

## 7. put() Logic

`LinkedHashMap` uses the same core hash logic as `HashMap`:

```text
1. Compute hash
2. Compute bucket index
3. Search the target bucket
4. If same key exists -> overwrite value
5. If key does not exist -> insert new entry
```

Additional behavior:

```text
After insertion, link the new node to the tail of the doubly linked list
```

So insertion affects both:

- hash table placement
    
- global order chain
    

---

## 8. get() Logic

Lookup is still hash-based:

```text
1. Compute hash
2. Compute bucket index
3. Search the bucket
4. Match by hash + equals
```

Additional behavior depends on ordering mode:

- In insertion-order mode: `get()` does not change order
    
- In access-order mode: accessed node moves to the tail
    

This tail movement is the key reason `LinkedHashMap` can implement LRU.

---

## 9. Why a Doubly Linked List Is Needed

Suppose the order chain is:

```text
A <-> B <-> C
```

If `B` is accessed in access-order mode, it must be moved to the tail:

```text
A <-> C <-> B
```

This requires:

- unlinking `B` from the middle
    
- reconnecting neighbors
    
- attaching `B` to the tail
    

A doubly linked list supports this in **O(1)** time because each node knows both:

- previous node
    
- next node
    

If a singly linked list were used, removing a middle node would require finding its predecessor, which would be slower.

---

## 10. LRU Cache Explained

LRU means:

```text
Least Recently Used
```

Policy:

- When capacity is full, remove the entry that has not been used for the longest time
    

In access-order mode:

- head = least recently used
    
- tail = most recently used
    

This makes `LinkedHashMap` a natural foundation for LRU cache implementations.

---

## 11. LRU Example

### Step-by-step behavior

Assume capacity = 3.

Initial inserts:

```text
put(A) -> A
put(B) -> A <-> B
put(C) -> A <-> B <-> C
```

Now access `A`:

```text
get(A) -> B <-> C <-> A
```

Now insert `D` when full:

```text
remove eldest -> remove B
insert D -> C <-> A <-> D
```

`B` is removed because it is the least recently used entry.

---

## 12. Complete LRU Code Example

```java
import java.util.LinkedHashMap;
import java.util.Map;

class LRUCache<K, V> extends LinkedHashMap<K, V> {

    private final int capacity;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}

public class Demo {
    public static void main(String[] args) {
        LRUCache<String, Integer> cache = new LRUCache<>(3);

        cache.put("A", 1);
        cache.put("B", 2);
        cache.put("C", 3);
        System.out.println(cache); // {A=1, B=2, C=3}

        cache.get("A");
        System.out.println(cache); // {B=2, C=3, A=1}

        cache.put("D", 4);
        System.out.println(cache); // {C=3, A=1, D=4}
    }
}
```

---

## 13. Iteration Patterns

### entrySet()

```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " -> " + entry.getValue());
}
```

### keySet()

```java
for (String key : map.keySet()) {
    System.out.println(key + " -> " + map.get(key));
}
```

### values()

```java
for (Integer value : map.values()) {
    System.out.println(value);
}
```

Because `LinkedHashMap` maintains order, iteration results are predictable.

---

## 14. Performance Summary

|Operation|Average Time|
|---|---|
|put|O(1)|
|get|O(1)|
|remove|O(1)|
|iterate|O(n)|

Compared with `HashMap`:

- same average-time complexity
    
- slightly higher memory overhead
    
- slightly more maintenance work due to doubly linked list updates
    

---

## 15. LinkedHashMap vs HashMap vs TreeMap

|Implementation|Ordering|Lookup|Typical Use|
|---|---|---|---|
|HashMap|Unordered|O(1) average|General-purpose map|
|LinkedHashMap|Insertion/access order|O(1) average|Ordered map / LRU cache|
|TreeMap|Sorted by key|O(log n)|Sorted key access|

Key distinction:

- `HashMap` is fast but unordered
    
- `LinkedHashMap` is fast and ordered
    
- `TreeMap` is sorted, not insertion-ordered
    

---

## 16. Common Pitfalls

### 16.1 Confusing order with sorting

`LinkedHashMap` preserves order, but it does **not sort keys**.

### 16.2 Forgetting accessOrder = true

If you want LRU behavior, you must explicitly enable access order.

### 16.3 Assuming every get() changes order

Only true when access-order mode is enabled.

### 16.4 Ignoring extra memory cost

The doubly linked list requires additional references per entry.

---

## 17. Best Practices

- Use `LinkedHashMap` when deterministic iteration order matters
    
- Use insertion order for stable output
    
- Use access order for LRU-style caches
    
- Use `HashMap` instead when order is irrelevant
    
- Do not mistake it for a sorted map
    

---

## 18. Interview-Level Summary

`LinkedHashMap` extends `HashMap` by adding a doubly linked list across all entries.

Its main value is that it preserves predictable iteration order:

- insertion order by default
    
- access order when enabled
    

Because it combines:

- hash table lookup for O(1) average access
    
- doubly linked list for O(1) order maintenance
    

it is commonly used to implement LRU caches and other ordered map structures.