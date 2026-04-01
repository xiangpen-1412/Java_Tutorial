___
## What mutable and immutable mean

In Java, **mutable** means an object's internal state can be changed after the object is created.

**Immutable** means an object's state cannot be changed after creation. If something looks like a modification, it actually produces a new value or a new object.

---

## Primitive types: are they mutable?

Java primitive types are:

- `byte`
    
- `short`
    
- `int`
    
- `long`
    
- `float`
    
- `double`
    
- `char`
    
- `boolean`
    

Strictly speaking, primitives are **not objects**, so they are usually **not described as mutable or immutable in the same sense as objects**.

But from a practical learning perspective, you can treat them as **value-like and effectively immutable**:

- a primitive variable stores a value directly
    
- there is no internal object state to mutate
    
- when you assign a new value, you are replacing the value in the variable, not mutating an object
    

Example:

```java
int a = 10;
a = 20;
```

This does **not** mean the value `10` was mutated into `20`.  
It means the variable `a` now stores a different value.

So:

- **primitive variable can be reassigned**
    
- **primitive value itself is not a mutable object**
    

---

## Simple way to remember it

### Primitive types

Treat them as **not mutable objects**.  
In beginner and review notes, it is fine to think of them as **effectively immutable values**.

### Objects

For objects, mutable vs immutable is a real design concept.

- if the object's state can change -> **mutable**
    
- if the object's state cannot change after construction -> **immutable**
    

---

## Mutable examples in Java

### Array

```java
int[] nums = {1, 2, 3};
nums[0] = 100;
```

Array contents can be changed, so arrays are **mutable**.

### `StringBuilder`

```java
StringBuilder sb = new StringBuilder("Java");
sb.append(" Basics");
```

`StringBuilder` changes its own internal content, so it is **mutable**.

### `ArrayList`

```java
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
names.remove("Alice");
```

`ArrayList` can add, remove, and replace elements, so it is **mutable**.

---

## Immutable examples in Java

### `String`

```java
String s = "java";
String upper = s.toUpperCase();
```

`String` is **immutable**.  
Methods such as `toUpperCase()` return a new `String` instead of changing the old one.

### Wrapper classes

- `Integer`
    
- `Double`
    
- `Long`
    
- `Boolean`
    

Example:

```java
Integer x = 10;
Integer y = x + 5;
```

`Integer` is immutable. A new value is produced instead of modifying the old object.

### Java time classes

- `LocalDate`
    
- `LocalTime`
    
- `LocalDateTime`
    
- `Instant`
    

Example:

```java
LocalDate date = LocalDate.of(2026, 3, 26);
LocalDate next = date.plusDays(1);
```

`date` itself is unchanged. `plusDays()` returns a new object.

---

## Important distinction: variable vs object

This is one of the most important points.

```java
String s = "hello";
s = "world";
```

This does **not** mean the original `String` object was modified.  
It means the variable `s` now refers to another value.

The same idea applies to primitives:

```java
int x = 10;
x = 20;
```

This does **not** mean `10` was mutated.  
It means `x` now stores another value.

So do not confuse:

- **reassigning a variable**
    
- **mutating an object**
    

They are not the same thing.

---

## Final summary

### Primitive types

- not objects
    
- usually not discussed as mutable or immutable in strict OOP terms
    
- practically treated as **value-like / effectively immutable**
    

### Mutable

- object state can change after creation
    
- examples: arrays, `ArrayList`, `HashMap`, `StringBuilder`
    

### Immutable

- object state cannot change after creation
    
- “modification” returns a new object
    
- examples: `String`, wrapper classes, most `java.time` classes
    

---

## Interview-level takeaway

When people ask about **mutable vs immutable in Java**, they are usually talking about **objects**, not primitives.

For primitives like `int` and `double`, the safest short answer is:

> Primitive types are not objects, so they are not usually classified as mutable or immutable. In practice, they behave like immutable values.