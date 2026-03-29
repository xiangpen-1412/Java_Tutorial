# Hashing

## 1. Overview

Hashing is a fundamental mechanism used in Java to enable **fast data lookup**. It is primarily used by hash-based collections such as:

- `HashMap`
    
- `HashSet`
    
- `Hashtable`
    
- `LinkedHashMap`
    

The hashing process converts an object into an integer value called a **hash code**, which is then used to determine the storage location of that object inside a hash table.

General idea:

```
object → hashCode() → hash → bucket index
```

The goal of hashing is to achieve **constant time average complexity** for lookup operations.

```
O(1) average
```

---

# 2. hashCode()

`hashCode()` is a method defined in the `Object` class:

```java
public int hashCode()
```

Its purpose is to produce an integer representation of an object that can be used by hash-based collections.

Example:

```java
String a = "Hello";

int hash = a.hashCode();

System.out.println(hash);
```

Different objects may produce the same hash code, which is known as a **hash collision**.

---

# 3. Relationship Between equals() and hashCode()

Java defines strict rules governing these two methods.

### Rule 1

If two objects are equal according to `equals()`, they **must produce the same hashCode()**.

```
a.equals(b) == true
→ a.hashCode() == b.hashCode()
```

### Rule 2

If two objects have different hash codes, they must be unequal.

```
a.hashCode() != b.hashCode()
→ a.equals(b) == false
```

### Rule 3

Two objects may share the same hash code but still be unequal.

```
hash collision
```

---

# 4. Why equals() and hashCode() Must Be Overridden Together

If `equals()` is overridden but `hashCode()` is not, hash-based collections may behave incorrectly.

Example:

```java
import java.util.HashSet;

class Person {

    String name;

    Person(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        Person p = (Person) o;
        return name.equals(p.name);
    }
}

public class Demo {

    public static void main(String[] args) {

        HashSet<Person> set = new HashSet<>();

        set.add(new Person("Alice"));
        set.add(new Person("Alice"));

        System.out.println(set.size());
    }
}
```

This may produce:

```
2
```

Because the two objects may end up in different hash buckets.

---

# 5. Implementing hashCode()

A common implementation pattern combines the hash codes of multiple fields.

Example:

```java
class Person {

    String name;
    int age;

    @Override
    public int hashCode() {

        int result = name.hashCode();
        result = 31 * result + age;

        return result;
    }
}
```

The multiplier `31` is commonly used because it is a prime number and produces a good distribution of hash values.

---

# 6. Recommended Implementation (Objects.hash)

Java provides a helper method:

```java
import java.util.Objects;

@Override
public int hashCode() {
    return Objects.hash(name, age);
}
```

This method simplifies implementation while maintaining correctness.

---

# 7. Hash Collision

A **hash collision** occurs when two different objects produce the same hash code.

Example:

```
objectA → hashCode = 15
objectB → hashCode = 15
```

In hash-based collections, collisions are handled by storing multiple elements in the same bucket.

Typical structure:

```
bucket
 ├─ objectA
 ├─ objectB
 └─ objectC
```

The collection then uses `equals()` to determine the correct element.

---

# 8. Hashing in HashMap / HashSet

Insertion into a hash-based collection typically follows this process:

```
1 compute hashCode
2 transform hash
3 locate bucket index
4 check existing elements
5 use equals() to resolve collisions
```

This design enables efficient lookup.

---

# 9. Performance Considerations

Hash-based collections rely heavily on the quality of `hashCode()`.

A good hash function should:

- distribute values evenly
    
- minimize collisions
    
- be fast to compute
    

Poor implementations may degrade performance to:

```
O(n)
```

---

# 10. Summary

Hashing is the core mechanism that enables efficient data retrieval in Java collections.

Key points:

```
hashCode converts objects into integers
hash values determine storage buckets
collisions are resolved using equals()
equals and hashCode must be consistent
```

Understanding hashing is essential for working with:

- `HashMap`
    
- `HashSet`
    
- `ConcurrentHashMap`
    

and other hash-based data structures.