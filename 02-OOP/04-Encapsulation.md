# Encapsulation

## 1. What Is Encapsulation (One-Line Engineering Definition)

**Encapsulation** means:

> Hiding an object’s internal state and implementation details, while exposing only controlled, stable ways to interact with it.

Engineering intuition:

- External code can only use what you explicitly allow
    
- The object itself is responsible for keeping its state valid
    

---

## 2. What Problem Encapsulation Solves (Brief)

Without encapsulation, systems suffer from:

- External code freely mutating internal state
    
- Business rules scattered across the codebase
    
- Small internal changes breaking many callers
    

Encapsulation is **not** about blocking access — it is about:

> **Preventing misuse**.

---

## 3. The Simplest Form: private Fields + Public Methods (Quick Pass)

```java
public class User {
    private int age;

    public void setAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("age must be non-negative");
        }
        this.age = age;
    }

    public int getAge() {
        return age;
    }
}
```

Key points:

- Fields are `private`
    
- All mutation goes through methods
    
- Validation logic lives inside the class
    

---

## 4. The Core of Encapsulation: Protecting Invariants (Key Section)

### 4.1 What Is an Invariant

An **invariant** is:

> A condition that must always hold true throughout an object’s lifetime.

Examples:

- Age must never be negative
    
- Account balance must never drop below zero
    
- A terminated employee must not receive pay
    

---

### 4.2 The Problem Without Encapsulation (Anti-Example)

```java
public class BankAccount {
    public double balance;
}
```

```java
BankAccount account = new BankAccount();
account.balance = -1_000_000; // No protection
```

The object is now in an illegal state, and the system has no way to detect or prevent it.

---

### 4.3 Encapsulated Version (Recommended)

```java
public class BankAccount {

    private double balance;

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("amount must be positive");
        }
        balance += amount;
    }

    public void withdraw(double amount) {
        if (amount <= 0 || amount > balance) {
            throw new IllegalArgumentException("invalid withdrawal");
        }
        balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

Design implications:

- External code cannot directly corrupt `balance`
    
- All business rules are centralized
    
- The object always remains valid
    

---

## 5. Encapsulation ≠ Getters and Setters (Critical Clarification)

A common misconception:

> Encapsulation = private fields + getters/setters

This is **incorrect**.

### ❌ Superficial Encapsulation

```java
public class User {
    private int age;

    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```

Problem:

- No constraints in the setter
    
- Illegal states are still possible
    

---

### ✅ Behavior-Driven Encapsulation

```java
public class User {
    private int age;

    public void growOlder() {
        age++;
    }

    public int getAge() {
        return age;
    }
}
```

Design idea:

- Do not expose arbitrary mutation
    
- Expose only **business-valid behaviors**
    

---

## 6. Encapsulation and Design Stability (Key Insight)

The biggest engineering benefit of encapsulation:

- Internal implementation can change freely
    
- External code remains unaffected
    

Examples of safe internal changes:

- Change field type (`int` → `LocalDate`)
    
- Modify validation logic
    
- Change internal storage strategy
    

As long as the **public API remains stable**, callers do not need to change.

---

## 7. Encapsulation with Composition and Inheritance

- **Composition**: encapsulation hides internal collaborators
    
- **Inheritance**: base classes must encapsulate state to protect invariants from subclasses
    

Rule of thumb:

> If even subclasses should not freely modify a field, that field must be `private`.

---

## 8. Engineering Takeaways (Must Remember)

- Encapsulation is about **protecting rules**, not hiding data
    
- Objects must be responsible for their own correctness
    
- Invariants belong inside the class
    
- `private` is a tool, not the goal
    
- Expose behavior, not state