# Inheritance and Polymorphism

This note focuses on inheritance and polymorphism in Java from an engineering perspective.  
Simple syntax-level concepts are summarized briefly, while subtle rules, edge cases,  and commonly overlooked details are emphasized.

---

## 1. Inheritance (`extends`)

### Definition

- Inheritance allows a class (subclass) to reuse and specialize the behavior of another class (superclass).
- <span style="color:rgb(255, 0, 0)">Java supports <b>single inheritance</b> for classes.</span>

```java
class Animal {
    void move() {}
}

class Dog extends Animal {}
```

### Engineering Intent

- Expresses an **is-a** relationship
    
- Enables code reuse and behavioral specialization
    

### Key Constraints (Easy to Forget)

- <span style="color:rgb(255, 0, 0)">Constructors are <b>not inherited</b></span>
- <span style="color:rgb(255, 0, 0)">Private members are <b>not accessible</b> in subclasses</span>
- <span style="color:rgb(255, 0, 0)">Subclass initialization <b>always starts with the superclass constructor</b></span>

```java
class Dog extends Animal {
    Dog() {
        super(); // implicit if omitted
    }
}
```

---

## 2. `super` Keyword

### Definition

- `super` refers to the immediate superclass part of the current object.

### Typical Uses

- Access superclass methods or fields
- Explicitly invoke a superclass constructor

```java
class Dog extends Animal {
    @Override
    void move() {
        super.move();
    }
}
```

### Critical Rules

- `super()` must be the **first statement** in a constructor
    
- If neither `this()` nor `super()` is written, `super()` is inserted implicitly
    

---

## 3. Method Overriding

### Core Rule Recap

- Method signature must match exactly
    
- Runtime dispatch based on **object type**, not reference type
    

```java
Animal a = new Dog();
a.move();
```

### Frequently Overlooked Details

#### 3.1 Access Level Restrictions

- <span style="color:rgb(255, 255, 0)">Overriding method <b>cannot reduce visibility</b></span>

```java
// Invalid
protected void move() {}
// overriding a public method
```

#### 3.2 Return Type Covariance

- Overriding methods may return a **subtype** of the original return type
    

```java
class Animal {
    Animal copy() { return new Animal(); }
}

class Dog extends Animal {
    @Override
    Dog copy() { return new Dog(); }
}
```

#### 3.3 `final` Methods

- <span style="color:rgb(255, 255, 0)">`final` methods <b>cannot be overridden</b></span>

```java
class Animal {
    final void breathe() {}
}
```

---

## 4. Polymorphism

### Definition

- Polymorphism allows a superclass reference to point to a subclass object.

```java
Animal animal = new Dog();
```

### Engineering Role

- Decouples callers from concrete implementations
    
- Foundation for extensibility and framework design
    

---

## 5. Compile-Time vs Runtime Behavior 

### Methods

- Instance method calls are resolved **at runtime** based on object type

### Fields

- Field access is resolved **at compile time** based on reference type

```java
class Animal {
    String type = "Animal";
}

class Dog extends Animal {
    String type = "Dog";
}

Animal a = new Dog();
System.out.println(a.type); // Animal
```

---

## 6. Method Overloading vs Overriding 

### Overloading

- Compile-time binding
    
- Same method name, different parameters
    

### Overriding

- Runtime binding
    
- Same signature, subclass implementation
    

```java
class Dog extends Animal {
    void move(int speed) {} // overloading, NOT overriding
}
```

---

## 7. Casting and `instanceof`

### Downcasting Risk

```java
Animal a = new Dog();
Dog d = (Dog) a; // valid
```

```java
Animal a = new Animal();
Dog d = (Dog) a; // ClassCastException
```

### Safe Pattern

```java
if (a instanceof Dog) {
    Dog d = (Dog) a;
}
```

---

## 8. Constructor Execution Order 

### Rule

- Superclass constructor executes **before** subclass constructor
    
- Order is strictly top-down in the inheritance chain
    

```java
class A {
    A() { System.out.println("A"); }
}

class B extends A {
    B() { System.out.println("B"); }
}
```

Output:

```
A
B
```

---

## 9. Design Warnings (Engineering Perspective)

- Inheritance increases coupling
    
- Prefer composition when behavior reuse is not strictly is-a
    
- Excessive overriding often indicates poor abstraction
    

---

## Summary

- Inheritance defines structural relationships
    
- Overriding enables runtime behavior replacement
    
- Polymorphism depends on reference vs object type separation
    
- Many bugs originate from misunderstanding dispatch rules