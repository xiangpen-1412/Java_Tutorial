# Java Enum — Engineering-Level Notes

---

# 1. What is an Enum?

In Java, an `enum` is **not just a group of constants**.

It is a **special kind of class** that:

- Implicitly extends `java.lang.Enum`
    
- Cannot extend another class
    
- Can implement interfaces
    
- Can define fields, constructors, and methods
    
- Creates a fixed number of singleton instances
    

Each enum constant is a **public static final instance** of the enum type.

---

# 2. Basic Syntax

```java
public enum Direction {
    NORTH,
    SOUTH,
    EAST,
    WEST
}
```

Conceptually compiled as:

```java
public final class Direction extends Enum<Direction> {
    public static final Direction NORTH = new Direction("NORTH", 0);
    public static final Direction SOUTH = new Direction("SOUTH", 1);
}
```

Key properties:

- Instances are created during class loading
    
- No external instantiation allowed
    
- Guaranteed singleton per constant
    

---

# 3. Why Use Enum?

Enums provide:

- Type safety
    
- Restricted value domain
    
- Clear semantic meaning
    
- Compile-time checking
    
- Safer alternative to magic strings or integer constants
    

---

# 4. Built-in Methods

All enums inherit the following methods:

```java
name()      // returns constant name
ordinal()   // returns declaration index (0-based)
values()    // returns all constants as array
valueOf()   // converts string to enum
```

Example:

```java
Direction d = Direction.NORTH;
System.out.println(d.name());     // "NORTH"
System.out.println(d.ordinal());  // 0
```

⚠ Avoid relying on `ordinal()` in business logic.

---

# 5. Enums with Fields and Constructors

```java
public enum Size {
    SMALL(1),
    MEDIUM(2),
    LARGE(3);

    private final int code;

    Size(int code) {
        this.code = code;
    }

    public int getCode() {
        return code;
    }
}
```

Notes:

- Constructors are implicitly private
    
- Fields are typically final
    
- Instances are immutable
    

---

# 6. Enums with Behavior (Polymorphism)

Enums can define abstract methods and override them per constant.

```java
public enum Operation {
    ADD {
        public int apply(int a, int b) {
            return a + b;
        }
    },
    SUBTRACT {
        public int apply(int a, int b) {
            return a - b;
        }
    };

    public abstract int apply(int a, int b);
}
```

This replaces switch-based logic with polymorphism.

---

# 7. Enum and Switch

Enums integrate naturally with switch:

```java
switch (direction) {
    case NORTH -> ...;
    case SOUTH -> ...;
}
```

Benefits:

- Compiler ensures valid cases
    
- Cleaner than string comparison
    

---

# 8. Memory Model and Thread Safety

- Enum instances are created at class loading time
    
- Class loading in JVM is thread-safe
    
- Enum constants are implicitly static final
    

Therefore, enums are inherently thread-safe.

---

# 9. EnumSet and EnumMap

## 9.1 EnumSet

```java
EnumSet<Day> days = EnumSet.of(Day.MONDAY, Day.FRIDAY);
```

Characteristics:

- Internally uses bit vectors
    
- Extremely memory efficient
    
- Faster than HashSet for enums
    

---

## 9.2 EnumMap

```java
EnumMap<Day, String> map = new EnumMap<>(Day.class);
```

Characteristics:

- Internally backed by array
    
- Uses ordinal() for indexing
    
- O(1) access
    
- More efficient than HashMap for enum keys
    

---

# 10. Common Engineering Use Cases

- State machines
    
- Strategy pattern
    
- Order status
    
- HTTP status codes
    
- Permission roles
    
- Log levels
    

---

# 11. Enum vs Constant Class

Traditional constants:

```java
public static final int NORTH = 1;
```

Problems:

- Not type-safe
    
- Accepts invalid values
    
- Poor readability
    

Enum provides:

- Strong typing
    
- Restricted domain
    
- Better maintainability
    

---

# 12. Limitations

- Cannot extend another class
    
- Cannot be subclassed
    
- Cannot instantiate with new
    
- Constructor must be private
    

---

# 13. Serialization Behavior

Enums are serialization-safe:

- Only name is serialized
    
- JVM ensures singleton preservation during deserialization
    

---

# 14. Interview-Level Key Points

- Enum is a special class
    
- Each constant is a singleton instance
    
- Constructor runs once per constant
    
- EnumMap uses array indexing via ordinal
    
- EnumSet uses bitwise representation
    
- Can replace switch with polymorphism
    

---

# 15. Conceptual Summary

Enum = Restricted Type + Singleton Instances + Object-Oriented Behavior

Core identity:

- Compile-time constant set
    
- Runtime singleton objects
    
- Supports fields, methods, and polymorphism
    
- Highly efficient when used with EnumSet/EnumMap
    

Enums are a fundamental tool for designing safe and expressive domain models in Java.