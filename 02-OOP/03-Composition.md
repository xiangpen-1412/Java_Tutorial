# Composition

## 1. What Is Composition

**Composition** means a class _has_ another object and delegates part of its responsibility to that object, instead of inheriting behavior from a parent class.

- Inheritance: **is-a** relationship
    
- Composition: **has-a** relationship
    

> Design principle: **Favor composition over inheritance**

---

## 2. Why Composition Exists

Problems with inheritance:

- Strong coupling (parent changes ripple to all children)
    
- Behavior fixed at compile time
    
- Subclass explosion as requirements grow
    

Benefits of composition:

- Loose coupling
    
- Behavior can be replaced at runtime
    
- Easier to follow the Open–Closed Principle
    
- Easier to test (mock or stub collaborators)
    

---

## 3. Basic Example: Engine + Car

```java
class Engine {
    void start() {
        System.out.println("Engine starts");
    }
}

class Car {
    private Engine engine = new Engine();

    void start() {
        engine.start(); // delegation
    }
}
```

Key points:

- A Car **is not** an Engine
    
- A Car **has** an Engine
    

---

## 4. Engineering-Level Example: Modeling a Variation Point (Core Section)

### 4.1 Extract the Changing Behavior (Interface)

```java
public interface PayStrategy {
    double calculatePay();
}
```

- Interface represents a _behavior_, not a type
    
- The user of the interface does not care about the implementation
    

---

### 4.2 Concrete Implementations = Different Behaviors

```java
public class SalariedPayStrategy implements PayStrategy {

    private final double annualSalary;

    public SalariedPayStrategy(double annualSalary) {
        this.annualSalary = annualSalary;
    }

    @Override
    public double calculatePay() {
        return annualSalary / 26;
    }
}
```

```java
public class HourlyPayStrategy implements PayStrategy {

    private final double hourlyRate;

    public HourlyPayStrategy(double hourlyRate) {
        this.hourlyRate = hourlyRate;
    }

    @Override
    public double calculatePay() {
        return hourlyRate * 40;
    }

    public double calculateDoublePay() {
        return hourlyRate * 2;
    }
```