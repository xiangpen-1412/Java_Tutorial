# Abstract Class

---

# 1. Conceptual Foundation

## 1.1 What Is an Abstract Class?

An abstract class is a partially implemented type that:

- Cannot be instantiated
    
- May contain abstract methods (no body)
    
- May contain fully implemented methods
    
- May define fields and constructors
    

It represents a **shared structural template** for related subclasses.

Core idea:

> An abstract class models a "base type with partial implementation".

---

# 2. Why Abstract Classes Exist

Abstract classes solve two major engineering problems:

1. Code reuse with enforced specialization
    
2. Controlled inheritance hierarchy
    

They allow:

- Shared state
    
- Shared logic
    
- Required behavior definition via abstract methods
    

This differs from interfaces, which primarily define contracts without shared state.

---

# 3. Syntax and Structure

```java
public abstract class PaymentProcessor {

    protected final String merchantId;

    public PaymentProcessor(String merchantId) {
        if (merchantId == null || merchantId.isBlank()) {
            throw new IllegalArgumentException("Merchant ID cannot be empty");
        }
        this.merchantId = merchantId;
    }

    public final void process(double amount) {
        validate(amount);
        authorize(amount);
        execute(amount);
        logTransaction(amount);
    }

    protected void validate(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Invalid amount");
        }
    }

    protected abstract void authorize(double amount);

    protected abstract void execute(double amount);

    private void logTransaction(double amount) {
        System.out.println("Transaction logged for " + amount);
    }
}
```

This demonstrates:

- Shared state (merchantId)
    
- Constructor logic
    
- Template method pattern (process)
    
- Abstract specialization points (authorize, execute)
    

---

# 4. Template Method Pattern 

The above `process()` method is a Template Method.

Definition:

> A final method that defines the execution algorithm while delegating specific steps to subclasses.

Key interview insight:

- Template method ensures algorithm consistency
    
- Subclasses customize only defined extension points
    

---

# 5. Subclass Implementation

```java
public class CreditCardProcessor extends PaymentProcessor {

    public CreditCardProcessor(String merchantId) {
        super(merchantId);
    }

    @Override
    protected void authorize(double amount) {
        System.out.println("Authorizing credit card for " + amount);
    }

    @Override
    protected void execute(double amount) {
        System.out.println("Charging credit card " + amount);
    }
}
```

This demonstrates:

- super() constructor chaining
    
- Overriding abstract methods
    
- Enforced specialization
    

---

# 6. Abstract Class vs Concrete Class

Abstract class:

- May contain abstract methods
    
- Cannot instantiate
    

Concrete subclass:

- Must implement all abstract methods
    
- Can be instantiated
    

If a subclass does not implement all abstract methods, it must also be declared abstract.

---

# 7. Abstract Methods — Deep Rules

Rules:

- Cannot be private
    
- Cannot be final
    
- Cannot have a body
    
- Must be implemented by first concrete subclass
    

You cannot combine:

```java
abstract final void test(); // invalid
```

Reason:

- abstract requires overriding
    
- final prevents overriding
    

---

# 8. Constructor Behavior

Abstract classes can define constructors.

Important:

- Abstract classes cannot be instantiated
    
- But their constructors are invoked during subclass instantiation
    

Execution order:

1. Parent constructor executes
    
2. Child constructor executes
    

Example:

```java
abstract class Base {
    Base() {
        System.out.println("Base constructor");
    }
}

class Derived extends Base {
    Derived() {
        System.out.println("Derived constructor");
    }
}
```

Output:

Base constructor  
Derived constructor

---

# 9. Field Initialization Order (Advanced Detail)

Order of execution when creating subclass:

1. Static initializers (parent)
    
2. Static initializers (child)
    
3. Instance fields (parent)
    
4. Parent constructor
    
5. Instance fields (child)
    
6. Child constructor
    

This is frequently tested in interviews.

---

# 10. Abstract Class and Polymorphism

```java
PaymentProcessor processor = new CreditCardProcessor("M-100");
processor.process(100.0);
```

Runtime dispatch applies to overridden abstract methods.

Key concept:

- Method resolution is dynamic
    
- Field access is static (based on reference type)
    

---

# 11. When to Use Abstract Class

Use abstract class when:

- You need shared state
    
- You need shared implementation
    
- You want controlled inheritance
    
- You want template methods
    

Avoid abstract class when:

- You need multiple inheritance of behavior
    
- You want pure contracts
    

---

# 12. Abstract Class and Interfaces Together

An abstract class can implement interfaces.

```java
public abstract class FileProcessor implements AutoCloseable {
    public abstract void process();
}
```

Concrete subclass must:

- Implement abstract methods
    
- Implement interface methods (if not already implemented)
    

---

# 13. Design Principles

## 13.1 Liskov Substitution Principle (LSP)

Subtypes must be substitutable for base types.

If subclass violates expected behavior, design is broken.

## 13.2 Open/Closed Principle (OCP)

Abstract classes enable extension without modification.

---

# 14. Common Interview Questions

1. Can abstract class have constructor? → Yes
    
2. Can abstract class have static methods? → Yes
    
3. Can abstract class have final methods? → Yes
    
4. Can abstract class be instantiated via reflection? → No (InstantiationException)
    
5. Can abstract class implement interface? → Yes
    
6. Can abstract class extend another abstract class? → Yes
    

---

# 15. Abstract Class Limitations

- Single inheritance only
    
- Tight coupling in hierarchy
    
- Not suitable for capability modeling
    

---

# 16. Performance Considerations

- No runtime overhead for abstract methods
    
- Dynamic dispatch cost identical to normal overridden methods
    
- JIT can inline concrete implementations
    

---

# 17. Real-World Architecture Example

Framework-level base classes:

- Spring AbstractController
    
- JUnit AbstractTestCase
    
- Java AbstractList
    

These provide:

- Partial implementation
    
- Hook methods
    
- Extension points
    

---

# 18. Conceptual Summary

Abstract class = Shared Structure + Partial Implementation + Enforced Specialization

Core identity:

- Represents "is-a" hierarchy
    
- Supports template method pattern
    
- Provides controlled extensibility
    
- Enables runtime polymorphism
    

Mastery of abstract classes is foundational for advanced design patterns and framework-level architecture.