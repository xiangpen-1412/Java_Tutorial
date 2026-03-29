# OOP Core Concepts (Class to Method Overriding)

This note documents the foundational object-oriented programming constructs in Java.  
The scope intentionally excludes inheritance hierarchy design and polymorphism,  
focusing instead on object modeling, encapsulation, and method behavior definition.

---

## 1. Class and Object

### Definition

- A `class` defines a custom reference type composed of fields and methods.
    
- An `object` is a runtime instance of a class allocated on the heap.
    

### Engineering Role

- Primary unit of abstraction in Java
    
- Encapsulates state and behavior into a cohesive structure
    

### Example

```java
class User {
    String name;
    int age;

    void greet() {
        System.out.println("Hello " + name);
    }
}

User user = new User();
user.name = "Tom";
user.age = 20;
```

---

## 2. Constructor

### Definition

- A constructor initializes an object at creation time.
    
- It is executed exactly once per object instantiation.
    

### Properties

- Name must match the class name
    
- No return type
    
- Supports overloading
    

### Engineering Role

- Enforces valid object initialization
    
- Centralizes construction logic
    

### Example

```java
class User {
    String name;
    int age;

    User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

---

## 3. `this` Keyword

### Definition

- `this` refers to the current object instance.
    

### Typical Use Cases

- Distinguishing instance fields from method parameters
    
- Constructor chaining within the same class
    

### Engineering Role

- Eliminates ambiguity in object state assignment
    
- Improves code readability and correctness
    

### Example

```java
class User {
    String name;

    User(String name) {
        this.name = name;
    }
}
```

---

## 4. Encapsulation

### Definition

- Encapsulation restricts direct access to internal object state.
    
- Implemented via access modifiers and controlled method exposure.
    

### Common Access Levels

- `private`: class-internal access only
    
- `public`: unrestricted access
    

### Engineering Role

- Prevents invalid or inconsistent state
    
- Reduces coupling between components
    
- Preserves internal implementation flexibility
    

### Design Rules

- Fields should default to `private`
    
- External interaction via methods
    

### Example

```java
class User {
    private int age;

    public void setAge(int age) {
        if (age <= 0) {
            throw new IllegalArgumentException("Invalid age");
        }
        this.age = age;
    }

    public int getAge() {
        return age;
    }
}
```

---

## 5. Method Overloading

### Definition

- Multiple methods with the same name but different parameter lists.
    
- Resolved at compile time.
    

### Valid Overloading Criteria

- Different parameter count
    
- Different parameter types
    
- Different parameter order
    

Return type alone does not constitute overloading.

### Engineering Role

- Improves API expressiveness
    
- Supports semantic grouping of related operations
    

### Example

```java
class Calculator {

    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }
}
```

---

## 6. Method Overriding

### Definition

- A subclass provides a new implementation for a method declared in its superclass.
    
- Method dispatch occurs at runtime.
    

### Overriding Constraints

- Method signature must match exactly
    
- Access level cannot be more restrictive
    
- Return type must be compatible
    
- `@Override` annotation is recommended
    

### Engineering Role

- Allows behavioral specialization
    
- Serves as a prerequisite for polymorphic design
    

### Example

```java
class Animal {
    void sound() {
        System.out.println("animal sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("bark");
    }
}
```

---


## 7. Discrepancies: Overloading vs Overriding

This section gives a **concise but precise comparison** between **method overloading** and **method overriding**, with a **single classic example** that shows both behaviors side by side. This is mainly about **inheritance and method dispatch**.

---

### 7.1 Core Difference (One‑line intuition)

- **Overloading**: decided at **compile time**, based on **parameter types**
    
- **Overriding**: decided at **run time**, based on the **actual object type**
    

A very common summary:

> **Overloading cares about the reference type.**  
> **Overriding cares about the object type.**

---

### 7.2 Overloading (Same method name, different parameters)

#### Key rules

- Must be in the **same class** (or inherited, but resolved locally)
    
- Method name is the same
    
- **Parameter list must be different**
    
- Return type does **not** matter
    
- Binding happens at **compile time**
    

Example:

```java
static void greet(Parent p) {
    System.out.println("greet(Parent)");
}

static void greet(Child c) {
    System.out.println("greet(Child)");
}
```

The compiler decides **which method to call** purely from the **declared type** of the argument.

---

### 7.3 Overriding (Same method signature, child replaces parent behavior)

#### Key rules

- Requires **inheritance**
    
- Method signature must be **exactly the same**
    
- Access level cannot be more restrictive
    
- Binding happens at **run time** (dynamic dispatch)
    

Example:

```java
class Parent {
    void speak() {
        System.out.println("Parent.speak()");
    }
}

class Child extends Parent {
    @Override
    void speak() {
        System.out.println("Child.speak()");
    }
}
```

The JVM decides which implementation to call based on the **actual object**, not the reference.

---

### 7.4 Classic Side‑by‑Side Example (Overloading vs Overriding)

This example intentionally puts **both concepts into the same program**.

```java
public class OverloadVsOverrideDemo {

    static class Parent {
        void speak() {
            System.out.println("Parent.speak()");
        }
    }

    static class Child extends Parent {
        @Override
        void speak() {
            System.out.println("Child.speak()");
        }
    }

    // -------- Overloading --------
    static void greet(Parent p) {
        System.out.println("greet(Parent)");
    }

    static void greet(Child c) {
        System.out.println("greet(Child)");
    }

    public static void main(String[] args) {

        Parent p1 = new Parent();
        Parent p2 = new Child();
        Child  c1 = new Child();

        // ----- Overriding (runtime) -----
        p1.speak(); // Parent.speak()
        p2.speak(); // Child.speak()
        c1.speak(); // Child.speak()

        System.out.println("-----");

        // ----- Overloading (compile time) -----
        greet(p1);  // greet(Parent)
        greet(p2);  // greet(Parent)
        greet(c1);  // greet(Child)

        System.out.println("-----");

        // Force compiler to choose greet(Child)
        greet((Child) p2); // greet(Child)
    }
}
```

---

### 7.5 Why `greet(p2)` Calls `greet(Parent)`

```java
Parent p2 = new Child();
```

- **Reference type**: `Parent`
    
- **Object type**: `Child`
    

For **overloading**, the compiler only sees:

```java
greet(Parent)
```

So it chooses `greet(Parent)` **before the program even runs**.

---

### 7.6 Why `p2.speak()` Calls `Child.speak()`

`speak()` is **overridden**, not overloaded.

At run time, the JVM checks the **actual object**:

```java
new Child()
```

So it dispatches to:

```java
Child.speak()
```

This is **runtime polymorphism**.

---

### 7.7 Minimal Comparison Table

|Aspect|Overloading|Overriding|
|---|---|---|
|Requires inheritance|No|Yes|
|Method signature|Different parameters|Same parameters|
|Binding time|Compile time|Run time|
|Decided by|Reference type|Object type|
|Polymorphism|No|Yes|

---

### 7.8 One‑sentence Interview Summary

> Overloading is resolved at compile time based on parameter types, while overriding is resolved at runtime based on the actual object type.

---
## Scope Boundary

This note intentionally excludes:

- Inheritance hierarchy design
    
- Polymorphism and dynamic dispatch usage
    
- Interface-based abstraction
    

These topics are covered in separate notes.