# Java Generics: Type Parameter T vs. Wildcard ?

## 1. The Error Analysis

Your code failed because of a syntax violation:

```java
public static double sum(List<T extends Number> list) { ... } 
```

**Reason:** In Java, you cannot **declare** a named type parameter (like `T`) inside the parameter list. You can only **use** a previously declared type or use an anonymous **Wildcard** (`?`).

---
## 2. Solution A: Generic Method (Using `<T>`)
If you want to use a named variable `T`, you must declare it **before** the return type.

### Corrected Code:
```java
//               [Declaration]               [Usage]
public static <T extends Number> double sum(List<T> list) {
    double s = 0.0;
    for (T n : list) {
        s += n.doubleValue();
    }
    return s;
}