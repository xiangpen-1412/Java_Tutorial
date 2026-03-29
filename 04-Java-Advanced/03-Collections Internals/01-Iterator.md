# Iterators

## Scope

This note covers **`Iterator<E>`**, **`Iterable<E>`**, and practical iteration patterns in the Java Collections Framework, including **fail-fast** behavior and safe structural modification during traversal.

---

## Core Types

### `Iterator<E>`

- A **cursor** over a sequence of elements.
    
- Primary operations:
    
    - `boolean hasNext()` — check if a next element exists
        
    - `E next()` — return next element **and advance**
        
    - `void remove()` — remove the **last element returned by `next()`** (optional operation)
        

```java
import java.util.Iterator;

Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String v = it.next();
    // use v
}
```

### `Iterable<E>`

- A type that can **produce** an `Iterator<E>`.
    
- Contract:
    
    - `Iterator<E> iterator()`
        
- Enables the **enhanced for-loop** (`for-each`).
    

```java
public interface Iterable<T> {
    Iterator<T> iterator();
}
```

**Key relationship**:

- `Iterable` = “can be iterated” (factory for iterators)
    
- `Iterator` = “does the iteration” (cursor)
    

---

## Enhanced For-Loop Desugaring

The compiler rewrites:

```java
for (String s : list) {
    System.out.println(s);
}
```

into logic equivalent to:

```java
for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
    String s = it.next();
    System.out.println(s);
}
```

So **for-each works only if the target is `Iterable` (or an array)**.

---

## Safe Removal During Iteration

### Correct: `Iterator.remove()`

When you must remove elements while traversing, use the iterator’s `remove()`.

```java
import java.util.*;

List<Integer> xs = new ArrayList<>(List.of(1, 2, 3, 4, 5));

for (Iterator<Integer> it = xs.iterator(); it.hasNext(); ) {
    int v = it.next();
    if (v % 2 == 0) {
        it.remove(); // removes the last element returned by next()
    }
}

System.out.println(xs); // [1, 3, 5]
```

### Wrong: modifying the collection directly inside for-each

```java
for (Integer v : xs) {
    if (v % 2 == 0) {
        xs.remove(v); // ❌ typically triggers ConcurrentModificationException
    }
}
```

Reason: most JCF iterators are **fail-fast** (detect unexpected structural changes).

---

## Fail-Fast Iterators (Practical Mental Model)

Many JCF collections track a structural modification counter (commonly referred to as `modCount`).

- The iterator captures an **expected** value at creation.
    
- Each `next()` / `remove()` compares expected vs actual.
    
- If they differ (meaning “someone modified the collection behind the iterator”), it throws:
    
    - `ConcurrentModificationException`
        

Notes:

- Fail-fast is **best-effort**, not a concurrency guarantee.
    
- For concurrent traversal/modification, prefer concurrent collections (e.g., `CopyOnWriteArrayList`, `ConcurrentHashMap`) depending on the use case.
    

---

## `ListIterator<E>` (List-only)

`ListIterator` extends `Iterator` and is available for `List` implementations.

Capabilities:

- Bidirectional traversal: `hasPrevious()` / `previous()`
    
- Index info: `nextIndex()` / `previousIndex()`
    
- In-place update and insertion:
    
    - `set(E e)` — replace last returned element
        
    - `add(E e)` — insert at cursor position
        

```java
import java.util.*;

List<String> xs = new ArrayList<>(List.of("A", "B", "C"));
ListIterator<String> it = xs.listIterator();

while (it.hasNext()) {
    String v = it.next();
    if (v.equals("B")) {
        it.set("B*");      // replace
        it.add("B+");      // insert right after B*
    }
}

System.out.println(xs); // [A, B*, B+, C]
```

---

## When to Implement `Iterable` Yourself

You implement `Iterable<E>` when your type is a **container/aggregate** that owns multiple elements.

### Recommended: container is `Iterable<Car>`, domain object is not

A single `Car` is a domain entity (not a sequence). The container (inventory/garage) is the sequence.

```java
public final class Car {
    private final String brand;
    private final int manufactureYear;
    private final long price;

    public Car(String brand, int manufactureYear, long price) {
        this.brand = brand;
        this.manufactureYear = manufactureYear;
        this.price = price;
    }

    public String getBrand() { return brand; }
    public int getManufactureYear() { return manufactureYear; }
    public long getPrice() { return price; }
}
```

```java
import java.util.*;

public class CarInventory implements Iterable<Car> {
    private final List<Car> cars = new ArrayList<>();

    public void add(Car car) {
        cars.add(Objects.requireNonNull(car));
    }

    @Override
    public Iterator<Car> iterator() {
        return cars.iterator(); // delegate
    }
}
```

Usage:

```java
CarInventory inv = new CarInventory();
inv.add(new Car("Toyota", 2020, 25000));
inv.add(new Car("BMW", 2022, 45000));

for (Car c : inv) {
    System.out.println(c.getBrand());
}
```

### Custom iterator (no delegation)

Useful when you store data in a non-standard structure or you want a specific traversal order.

```java
import java.util.*;

public class IntRange implements Iterable<Integer> {
    private final int start;
    private final int endExclusive;

    public IntRange(int start, int endExclusive) {
        if (endExclusive < start) throw new IllegalArgumentException("end < start");
        this.start = start;
        this.endExclusive = endExclusive;
    }

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<>() {
            private int cur = start;

            @Override
            public boolean hasNext() {
                return cur < endExclusive;
            }

            @Override
            public Integer next() {
                if (!hasNext()) throw new NoSuchElementException();
                return cur++;
            }
        };
    }
}
```

---

## Iterator Contracts and Pitfalls

### `next()` contract

- If no next element exists, `next()` must throw `NoSuchElementException`.
    

### `remove()` contract

- Removes the element returned by the most recent `next()`.
    
- Calling `remove()` before any `next()` or calling it twice for the same returned element should throw `IllegalStateException`.
    
- Some iterators do not support `remove()` and may throw `UnsupportedOperationException`.
    

### Complexity expectations

- For most JCF iterators:
    
    - `hasNext()` is O(1)
        
    - `next()` is typically O(1)
        
    - `remove()` depends on the data structure (often O(1) on linked structures; may be O(n) on array-backed lists due to shifting)
        

---

## Quick Decision Rules

- Need to traverse elements? Use `Iterator` / for-each.
    
- Need to remove while iterating? Use **`Iterator.remove()`**.
    
- Need forward + backward edits in a `List`? Use **`ListIterator`**.
    
- Designing your own type to be loopable? Implement **`Iterable<E>`** on the **container**, not the entity.