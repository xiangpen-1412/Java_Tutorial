# Interface

## 1. Definition

An **interface** defines a behavioral contract. It specifies _what_ operations a type must provide without defining _how_ they are implemented.

Interface-driven design promotes:

- Loose coupling
    
- Behavioral abstraction
    
- Multiple capability composition
    
- Architectural boundary separation
    

A class may implement multiple interfaces, enabling horizontal capability composition without inheritance constraints.

---

## 2. Structural Rules

### 2.1 Fields

All interface fields are implicitly:

```
public static final
```

They are constants and cannot hold mutable state.

---

### 2.2 Methods

An interface may contain:

- Abstract methods (implicitly `public abstract`)
    
- `default` methods (with implementation)
    
- `static` methods (belong to the interface type)
    
- `private` methods (Java 9+, helper methods for default/static logic)
    

Example:

```java
public interface Retryable {

    int MAX_ATTEMPTS = 3; // constant

    void execute(); // abstract

    default void retry() {
        for (int i = 0; i < MAX_ATTEMPTS; i++) {
            try {
                execute();
                return;
            } catch (RuntimeException e) {
                if (i == MAX_ATTEMPTS - 1) throw e;
            }
        }
    }

    static boolean isTransient(Exception e) {
        return e instanceof IllegalStateException;
    }
}
```

---

## 3. Multiple Inheritance of Behavior

A class may implement multiple interfaces:

```java
interface Auditable {
    void audit(String message);
}

interface Identifiable {
    String id();
}

class OrderService implements Auditable, Identifiable {

    private final String id;

    OrderService(String id) {
        this.id = id;
    }

    @Override
    public void audit(String message) {
        System.out.println("AUDIT -> " + message);
    }

    @Override
    public String id() {
        return id;
    }
}
```

This models capability composition rather than inheritance hierarchy.

---

## 4. Default Method Conflict Resolution

If two interfaces provide the same default method signature, the implementing class must override it explicitly.

```java
interface A {
    default String name() { return "A"; }
}

interface B {
    default String name() { return "B"; }
}

class C implements A, B {

    @Override
    public String name() {
        return A.super.name() + "+" + B.super.name();
    }
}
```

Conflict resolution is mandatory. Java does not infer precedence.

---

## 5. Interface vs Abstract Class

|Aspect|Interface|Abstract Class|
|---|---|---|
|Multiple inheritance|Yes|No|
|State (fields)|Constants only|Instance fields allowed|
|Constructor|Not allowed|Allowed|
|Use case|Capability contract|Shared partial implementation|

Guideline:

- Use interface to define behavior.
    
- Use abstract class to share state or template logic.
    

---

## 6. Functional Interface

A functional interface contains exactly one abstract method.

```java
@FunctionalInterface
interface Validator<T> {
    boolean validate(T value);
}
```

Used with lambda expressions:

```java
Validator<String> notBlank = s -> s != null && !s.isBlank();
```

Default methods do not count toward the abstract method limit.

---

## 7. Industrial Pattern Example (Strategy + Decorator)

```java
interface PaymentStrategy {
    PaymentResult pay(double amount);
}

record PaymentResult(boolean success, String message) {}

class CardPayment implements PaymentStrategy {
    @Override
    public PaymentResult pay(double amount) {
        return new PaymentResult(true, "Card approved");
    }
}

class LoggingPaymentDecorator implements PaymentStrategy {

    private final PaymentStrategy delegate;

    LoggingPaymentDecorator(PaymentStrategy delegate) {
        this.delegate = delegate;
    }

    @Override
    public PaymentResult pay(double amount) {
        System.out.println("Processing payment: " + amount);
        PaymentResult result = delegate.pay(amount);
        System.out.println("Result: " + result);
        return result;
    }
}
```

Key architectural points:

- Strategy abstraction decouples business logic from implementation.
    
- Decorator composes behavior without modifying original implementation.
    
- Client depends only on the interface.
    

---

## 8. Design Principles Reinforced

Interfaces support:

- Dependency Inversion Principle
    
- Open/Closed Principle
    
- Behavioral decoupling
    
- Testability via mocking
    
- Plugin-based extensibility
    

---

## 9. Interface Inheritance and Restrictions

### 9.1 Interface Extends Interface

An interface may extend one or more interfaces.

```java
interface A {
    void methodA();
}

interface B {
    void methodB();
}

interface C extends A, B {
    void methodC();
}
```

Key properties:

- Uses `extends`, not `implements`
    
- Supports multiple inheritance
    
- Inherits all abstract and default methods
    

The resulting type is a union of method signatures.

---

### 9.2 Method Resolution Rules

If multiple parent interfaces declare the same default method, the conflict is resolved in the implementing class.

```java
interface A {
    default void test() {}
}

interface B {
    default void test() {}
}

interface C extends A, B {}

class D implements C {
    @Override
    public void test() {
        A.super.test();
    }
}
```

Conflict resolution is mandatory at implementation time.

---

### 9.3 Interface Restrictions

An interface cannot:

- Declare instance fields
    
- Declare constructors
    
- Extend a class
    
- Define non-public abstract methods
    

All fields are implicitly:

```
public static final
```

All abstract methods are implicitly:

```
public abstract
```

Nested interfaces are implicitly `static`.

---

### 9.4 Design Rationale

Interface inheritance aggregates behavior without introducing state. This avoids the diamond problem associated with class multiple inheritance.

Class inheritance combines state and behavior, which introduces ambiguity when multiple inheritance is attempted. Interface inheritance avoids this by restricting state entirely.

---

## 10. Engineering Summary

An interface represents a behavioral boundary. It defines externally observable capabilities without imposing inheritance structure or state constraints.

In large-scale systems:

- Public APIs are exposed as interfaces.
    
- Implementations remain replaceable.
    
- Cross-cutting concerns are composed via decorators.
    
- Architecture layers communicate via interface contracts.
    

Interface-driven design is foundational to scalable and maintainable Java systems.