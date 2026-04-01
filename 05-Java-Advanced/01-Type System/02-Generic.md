# Java Generics

---

# 1. Why Generics Exist

Before Java 5, collections stored `Object`. That caused:

- No compile-time type safety
    
- Frequent casting
    
- Runtime `ClassCastException`
    

Example (Pre-Generics style):

```java
ArrayList list = new ArrayList();
list.add("hello");
list.add(123);

String s = (String) list.get(0);  // Manual cast required
```

Problem: The compiler cannot prevent wrong types.

Generics solve this by enforcing **compile-time type safety**.

---

# 2. Basic Generic Class

## 2.1 Defining a Generic Class

```java
public class Box<T> {

    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}
```

`T` is a type parameter (Type Variable).

---

## 2.2 Using the Generic Class

```java
Box<String> box = new Box<>();
box.set("Hello");

String result = box.get();   // No casting required
```

Now the compiler guarantees that `Box<String>` only stores `String`.

---

# 3. Multiple Type Parameters

```java
public class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public K getKey() { return key; }
    public V getValue() { return value; }
}
```

Usage:

```java
Pair<String, Integer> pair = new Pair<>("age", 25);
```

---

# 4. Bounded Type Parameters

Sometimes we want to restrict the allowed types.

## 4.1 Upper Bound

```java
public class NumberBox<T extends Number> {

    private T value;

    public NumberBox(T value) {
        this.value = value;
    }

    public double doubleValue() {
        return value.doubleValue();
    }
}
```

Valid:

```java
NumberBox<Integer> box1 = new NumberBox<>(10);
NumberBox<Double> box2 = new NumberBox<>(3.14);
```

Invalid:

```java
// NumberBox<String>  ❌ Compile-time error
```

---

## 4.2 Multiple Bounds

```java
public class Test<T extends Number & Comparable<T>> {
}
```

Rules:

- Only one class allowed
    
- Multiple interfaces allowed
    
- Class must appear first
    

---

# 5. Generic Methods

Generic methods are independent of the class type parameter.

```java
public class Util {

    public static <T> T identity(T value) {
        return value;
    }
}
```

Usage:

```java
String s = Util.identity("hello");
Integer i = Util.identity(100);
```

Syntax rule:

`<T>` must appear before the return type.

---

# 6. Wildcards

Wildcards allow flexible type usage.

## 6.1 Unbounded Wildcard

```java
public void printList(List<?> list) {
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```

`?` means unknown type.

---

## 6.2 Upper Bounded Wildcard

```java
public double sum(List<? extends Number> list) {
    double total = 0;
    for (Number n : list) {
        total += n.doubleValue();
    }
    return total;
}
```

Allows reading but not adding.

---

## 6.3 Lower Bounded Wildcard

```java
public void addIntegers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}
```

Allows adding but reading only as Object.

---

# 7. Type Erasure

Generics exist only at compile time.

After compilation:

```java
public class Box {
    private Object value;
}
```

Implications:

- Cannot create `new T()`
    
- Cannot create `new T[]`
    
- Cannot use `instanceof List<String>`
    
- Static members cannot use `T`
    

---

# 8. Generics and Inheritance

Important rule:

```
List<String> is NOT a subtype of List<Object>
```

Generics are invariant.

Correct flexible usage:

```java
List<? extends Object> list = new ArrayList<String>();
```

---

# 9. Common Limitations

## 9.1 Cannot Instantiate Type Parameter

```java
// T obj = new T();  ❌
```

## 9.2 Cannot Create Generic Arrays

```java
// T[] arr = new T[10];  ❌
```

## 9.3 Cannot Use Primitive Types

```java
// Box<int>  ❌
Box<Integer> ✔
```

---

# 10. Industrial Example – Generic Repository

```java
public interface Repository<T, ID> {

    T findById(ID id);

    void save(T entity);

    void deleteById(ID id);
}
```

Implementation example:

```java
public class UserRepository implements Repository<User, Long> {

    @Override
    public User findById(Long id) {
        return null;
    }

    @Override
    public void save(User entity) {
    }

    @Override
    public void deleteById(Long id) {
    }
}
```

This pattern is widely used in enterprise frameworks.

---

# 11. Summary

Generics provide:

- Compile-time type safety
    
- Code reuse
    
- Cleaner APIs
    
- Stronger abstraction boundaries
    

Core principles:

- Type parameters are erased at runtime
    
- Generics are invariant
    
- Use bounds to restrict types
    
- Use wildcards for flexible APIs
    

---
