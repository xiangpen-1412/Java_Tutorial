# Lambda Expressions in Java

## 1. Overview

A **lambda expression** provides a concise way to implement the single abstract method of a **functional interface**. It allows behavior (code) to be treated as a value that can be assigned to variables or passed as arguments.

Lambda expressions were introduced in **Java 8** to reduce the verbosity of anonymous class implementations and to support functional-style programming patterns.

Key idea:

```
lambda expression = implementation of a single-method interface
```

---

## 2. Basic Syntax

General form:

```java
(parameters) -> expression
```

or

```java
(parameters) -> {
    statements
}
```

Structure:

```
(parameters) -> body
```

Example:

```java
(a, b) -> a + b
```

Meaning:

```
take two parameters and return their sum
```

---

## 3. Functional Interfaces

A lambda expression must target a **functional interface**.

A functional interface is an interface that contains **exactly one abstract method**.

Example interface:

```java
interface Printer {
    void print(String message);
}
```

Lambda implementation:

```java
Printer printer = message -> System.out.println(message);
```

Equivalent anonymous class implementation:

```java
Printer printer = new Printer() {
    public void print(String message) {
        System.out.println(message);
    }
};
```

Lambda removes the boilerplate code required by anonymous classes.

---

## 4. Parameter Rules

### Explicit parameter types

```java
(int a, int b) -> a + b
```

### Type inference

The compiler can infer parameter types from the functional interface.

```java
(a, b) -> a + b
```

### Single parameter shorthand

Parentheses can be omitted when there is only one parameter.

```java
x -> x * x
```

---

## 5. Lambda Body Forms

### Single expression

```java
(a, b) -> a + b
```

The return value is implicit.

Equivalent form:

```java
(a, b) -> {
    return a + b;
}
```

### Multiple statements

Braces are required when multiple statements are used.

```java
(a, b) -> {
    int result = a + b;
    System.out.println(result);
    return result;
}
```

---

## 6. Runnable Code Example

Complete example using built-in functional interfaces.

```java
import java.util.function.Consumer;

public class LambdaExample {

    public static void main(String[] args) {

        Consumer<String> greet = name -> System.out.println("Hello " + name);

        Consumer<String> shout = name -> System.out.println(name.toUpperCase());

        Consumer<String> whisper = name -> System.out.println("psst... " + name.toLowerCase());

        greet.accept("Alice");
        shout.accept("Bob");
        whisper.accept("Charlie");
    }
}
```

Output:

```
Hello Alice
BOB
psst... charlie
```

---

## 7. Variable Capture (Effectively Final Rule)

Lambda expressions can access variables from the surrounding scope. However, these variables must be **effectively final**.

Valid example:

```java
int base = 10;

Function<Integer, Integer> addBase = x -> x + base;
```

Invalid example:

```java
int base = 10;
base = 20;

Function<Integer, Integer> addBase = x -> x + base;
```

Local variables captured by lambdas must not be modified after initialization.

---

## 8. Built-in Functional Interfaces

Java provides common functional interfaces in the package:

```
java.util.function
```

Most frequently used ones:

|Interface|Method|Purpose|
|---|---|---|
|Consumer|accept(T t)|perform an action|
|Function<T,R>|apply(T t)|transform data|
|Predicate|test(T t)|return boolean condition|
|Supplier|get()|produce a value|

Example:

```java
Predicate<Integer> isEven = x -> x % 2 == 0;

System.out.println(isEven.test(10));
```

---

## 9. Method References

Some lambda expressions can be replaced by **method references**.

Lambda:

```java
x -> System.out.println(x)
```

Method reference:

```java
System.out::println
```

Method references provide an even more concise syntax when a lambda simply calls an existing method.

---

## 10. Conceptual Model

A lambda expression does not define a new method. Instead, it provides the implementation of the method defined by a functional interface.

Example:

Functional interface method:

```java
int compare(T a, T b);
```

Lambda implementation:

```java
(a, b) -> a.compareTo(b)
```

Meaning:

```
implement the compare method
```

---

## 11. When to Use Lambdas

Lambda expressions are suitable for:

- short behavior implementations
    
- passing behavior as parameters
    
- simplifying anonymous classes
    

They are not recommended for:

- complex multi-step logic
    
- long blocks of code
    
- stateful implementations
    

In such cases, a normal class or method implementation improves readability.