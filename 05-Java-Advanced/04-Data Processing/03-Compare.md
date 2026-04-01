# Java Compare — Complete Engineering Notes

---

# 1. Overview

In Java, comparison logic revolves around three core mechanisms:

1. `Comparable<T>` → defines a class’s natural ordering
    
2. `Comparator<T>` → defines external/custom ordering strategies
    
3. Primitive/Wrapper `compare()` methods → safe value comparison utilities
    

Understanding their design differences is critical for sorting, ordered collections, and system correctness.

---

# 2. Comparable — Natural Ordering

## 2.1 Definition

```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

Defines a class's intrinsic (natural) order.

Return contract:

- `< 0` → this < other
    
- `= 0` → equal in ordering sense
    
- `> 0` → this > other
    

---

## 2.2 When to Use Comparable

Use Comparable when:

- The class has ONE stable natural ordering
    
- The ordering is intrinsic to the object
    
- The ordering rarely changes
    

Examples:

- Version
    
- Money
    
- LocalDate
    
- BigDecimal
    

---

## 2.3 Example

```java
public final class Version implements Comparable<Version> {
    private final int major, minor, patch;

    @Override
    public int compareTo(Version o) {
        int c = Integer.compare(this.major, o.major);
        if (c != 0) return c;
        c = Integer.compare(this.minor, o.minor);
        if (c != 0) return c;
        return Integer.compare(this.patch, o.patch);
    }
}
```

Usage:

```java
Collections.sort(list);  // uses compareTo
```

---

## 2.4 Engineering Limitation

Comparable violates Open-Closed Principle:

- You can only define ONE natural ordering
    
- Adding new sorting logic requires modifying the class
    

Therefore, modern systems prefer Comparator.

---

# 3. Comparator — Strategy-Based Ordering

## 3.1 Definition

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

Comparator defines external sorting rules.

It follows Strategy Pattern.

---

## 3.2 When to Use Comparator

Use Comparator when:

- Multiple sorting strategies exist
    
- Sorting belongs to business logic
    
- You cannot modify the class
    
- Sorting rules are dynamic
    
- Used in TreeSet / TreeMap / PriorityQueue
    

In real-world projects: 90% of sorting uses Comparator.

---

# 4. Basic Comparator Patterns

Assume:

```java
public class Person {
    private final long id;
    private final int score;
    private final String name;

    public Person(long id, int score, String name) {
        this.id = id;
        this.score = score;
        this.name = name;
    }

    public long getId() { return id; }
    public int getScore() { return score; }
    public String getName() { return name; }
}
```

---

## 4.1 Primary + Secondary Field Sorting

Score ascending → Name ascending → ID ascending (safe unique fallback)

```java
Comparator<Person> PERSON_ORDER =
    Comparator.comparingInt(Person::getScore)
              .thenComparing(Person::getName, Comparator.nullsLast(String::compareTo))
              .thenComparingLong(Person::getId);
```

Why ID fallback is important:

TreeSet/TreeMap treat compare == 0 as duplicate keys.

---

## 4.2 Descending Order

```java
Comparator<Person> SCORE_DESC =
    Comparator.comparingInt(Person::getScore).reversed();
```

Important:

- `.reversed()` applies to the comparator before it
    
- Reversing entire chain reverses all levels
    

---

## 4.3 Safe Numeric Comparison

Never use subtraction:

```java
// ❌ overflow risk
return a.score - b.score;
```

Always use:

```java
Integer.compare(a, b);
Long.compare(a, b);
Double.compare(a, b);
```

---

# 5. Null Handling

Explicit null policy is required.

```java
Comparator.nullsFirst(...)
Comparator.nullsLast(...)
```

Example:

```java
Comparator.comparing(Person::getName,
    Comparator.nullsLast(String::compareTo));
```

Without this → potential NullPointerException.

---

# 6. TreeSet / TreeMap Critical Rule

These collections use compare() to determine key equality.

If:

```java
compare(a, b) == 0
```

Then the collection treats them as the SAME key.

Therefore:

Always include a unique fallback field (e.g., ID) in comparator.

---

# 7. Comparator Contract (Must Not Violate)

## 7.1 Antisymmetry

sign(compare(a,b)) == -sign(compare(b,a))

## 7.2 Transitivity

If a > b and b > c → then a > c

## 7.3 Consistency

Comparison must not change unless object state changes.

Violating these may corrupt TreeMap/TreeSet internal Red-Black Tree.

---

# 8. Lambda vs Separate Comparator Class

## 8.1 Use Lambda When

- Simple sorting
    
- Used in one place
    
- No external dependency
    

```java
list.sort(Comparator.comparingInt(Person::getScore));
```

---

## 8.2 Extract to Constants When Reused

```java
public final class PersonComparators {
    public static final Comparator<Person> BY_SCORE =
        Comparator.comparingInt(Person::getScore);

    public static final Comparator<Person> BY_SCORE_THEN_NAME =
        Comparator.comparingInt(Person::getScore)
                  .thenComparing(Person::getName)
                  .thenComparingLong(Person::getId);
}
```

---

## 8.3 Separate Class When Logic Is Complex

Use separate class when:

- Complex business rules
    
- Multiple branches
    
- External parameters
    
- Requires dependency injection
    
- Needs independent testing
    

Example:

```java
public class RankingComparator implements Comparator<Person> {

    private final boolean vipFirst;

    public RankingComparator(boolean vipFirst) {
        this.vipFirst = vipFirst;
    }

    @Override
    public int compare(Person a, Person b) {
        if (vipFirst) {
            if (a.isVip() && !b.isVip()) return -1;
            if (!a.isVip() && b.isVip()) return 1;
        }

        int c = Integer.compare(b.getScore(), a.getScore());
        if (c != 0) return c;

        return Long.compare(a.getId(), b.getId());
    }
}
```

---

# 9. Usage Contexts

## 9.1 List Sorting

```java
list.sort(PERSON_ORDER);
```

## 9.2 Arrays

```java
Arrays.sort(array, PERSON_ORDER);
```

## 9.3 PriorityQueue

```java
PriorityQueue<Person> pq =
    new PriorityQueue<>(PERSON_ORDER);
```

## 9.4 TreeSet

```java
TreeSet<Person> set =
    new TreeSet<>(PERSON_ORDER);
```

---

# 10. Performance Considerations

Sorting is O(n log n).

Comparator.compare() may be called thousands or millions of times.

Avoid:

- Heavy computation inside compare
    
- I/O operations
    
- Object allocation
    
- Database access
    

Comparator must be lightweight.

---

# 11. Comparable vs Comparator — Final Engineering Summary

|Scenario|Use|
|---|---|
|Single natural ordering|Comparable|
|Multiple sort strategies|Comparator|
|Business-layer sorting|Comparator|
|Complex logic|Separate Comparator class|
|One-time simple sort|Lambda Comparator|
|TreeSet/TreeMap|Comparator with unique fallback|

---

# 12. Core Principles to Remember

1. compare == 0 does NOT mean equals()
    
2. TreeMap equality is based on compare == 0
    
3. Never use subtraction for comparison
    
4. Always consider null strategy
    
5. Always ensure transitivity
    
6. Prefer Comparator in modern architecture
    

---

# End of Java Compare Notes