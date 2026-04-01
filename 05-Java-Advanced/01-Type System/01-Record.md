# Record

## 1. Overview

`record` is a special type of class introduced in **Java 16** that represents immutable data carriers.

Records automatically generate common boilerplate code normally required for simple data objects:

- constructor
- accessors
- equals()
- hashCode()
- toString()

Typical usage scenarios:

- DTO objects
- API response models
- database projection models
- configuration objects
- immutable domain objects

Example comparison:

Traditional class:

```java
public class Person {

    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String name() {
        return name;
    }

    public int age() {
        return age;
    }
}
```

Record version:

```java
public record Person(String name, int age) {}
```

---

# 2. Record Syntax

General syntax:

```java
public record RecordName(Type field1, Type field2, ...) {}
```

Example:

```java
public record Car(String brand, int year, double price) {}
```

The compiler automatically generates:

- private final fields
    
- canonical constructor
    
- accessor methods
    
- equals()
    
- hashCode()
    
- toString()
    

Conceptual equivalent generated class:

```java
public final class Car {

    private final String brand;
    private final int year;
    private final double price;

    public Car(String brand, int year, double price) {
        this.brand = brand;
        this.year = year;
        this.price = price;
    }

    public String brand() { return brand; }

    public int year() { return year; }

    public double price() { return price; }
}
```

---

# 3. Accessor Methods

Record components automatically generate accessor methods.

Important difference from JavaBeans:

Record accessors **do not use `get` prefix**.

Example:

```java
Car car = new Car("BMW", 2023, 55000);

System.out.println(car.brand());
System.out.println(car.year());
System.out.println(car.price());
```

---

# 4. Canonical Constructor

The automatically generated constructor is called the **canonical constructor**.

Example:

```java
public record User(String username, int age) {}
```

Equivalent generated constructor:

```java
public User(String username, int age) {
    this.username = username;
    this.age = age;
}
```

---

## Custom Canonical Constructor

You can explicitly define it to add validation.

```java
public record User(String username, int age) {

    public User(String username, int age) {

        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }

        this.username = username;
        this.age = age;
    }
}
```

---

# 5. Compact Constructor

Java allows a **compact constructor** that avoids repeating assignments.

```java
public record User(String username, int age) {

    public User {

        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}
```

The compiler automatically inserts:

```
this.username = username
this.age = age
```

---

# 6. Records Are Immutable

All record components are implicitly:

```
private final
```

This means values cannot change after object creation.

Example:

```java
Car car = new Car("BMW", 2023, 50000);

car.price = 60000; // compilation error
```

Benefits:

- thread safety
    
- predictable behavior
    
- functional style compatibility
    

---

# 7. Methods Inside Records

Records can contain additional instance methods.

```java
public record Rectangle(double width, double height) {

    public double area() {
        return width * height;
    }
}
```

Usage:

```java
Rectangle r = new Rectangle(5, 3);

System.out.println(r.area());
```

---

# 8. Static Members

Records can define static fields and methods.

```java
public record Temperature(double value) {

    public static Temperature freezingPoint() {
        return new Temperature(0);
    }
}
```

---

# 9. Records and Interfaces

Records can implement interfaces.

```java
public interface Printable {
    void print();
}
```

```java
public record Book(String title, int pages) implements Printable {

    @Override
    public void print() {
        System.out.println(title + " : " + pages);
    }
}
```

---

# 10. Restrictions of Records

## Cannot extend other classes

```
record A extends B ❌
```

Records implicitly extend:

```
java.lang.Record
```

---

## Instance fields cannot be added

```
public record Person(String name) {

    private int age; ❌
}
```

Records must maintain **transparent data representation**.

---

## Components are always final

All record fields are immutable.

```
private final
```

---

# 11. Runnable Example

```java
public class RecordExample {

    public record Car(String brand, int year, double price) {

        public String description() {
            return brand + " - " + year + " : $" + price;
        }
    }

    public static void main(String[] args) {

        Car car1 = new Car("BMW", 2023, 60000);
        Car car2 = new Car("Tesla", 2024, 75000);

        System.out.println(car1.brand());
        System.out.println(car1.description());

        System.out.println(car2);
    }
}
```

Example output:

```
BMW
BMW - 2023 : $60000.0
Car[brand=Tesla, year=2024, price=75000.0]
```

---

# 12. When to Use Records

Records are appropriate when:

- the class represents pure data
    
- immutability is required
    
- behavior is minimal
    
- boilerplate code should be avoided
    

Typical engineering usage:

```
DTO objects
API response models
database projection models
configuration objects
```