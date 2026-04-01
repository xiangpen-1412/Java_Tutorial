# Consumer and Predicate in Java

`Consumer` and `Predicate` are functional interfaces from the `java.util.function` package. They are commonly used with lambda expressions, collections, and streams.

---

# 1. Consumer

## Definition

`Consumer<T>` represents an operation that:

- takes one input
    
- returns nothing
    

It is mainly used to perform an action on data.

### Method

```java
void accept(T t)
```

---

## Example 1 — Print values

### Code

```java
import java.util.function.Consumer;

public class ConsumerExample {

    public static void main(String[] args) {

        Consumer<String> printer = s -> System.out.println(s);

        printer.accept("Hello");
        printer.accept("Java");

    }
}
```

### Output

```
Hello
Java
```

---

## Example 2 — Using Consumer with forEach

### Code

```java
import java.util.List;
import java.util.function.Consumer;

public class ConsumerExample {

    public static void main(String[] args) {

        List<Integer> numbers = List.of(1, 2, 3, 4);

        Consumer<Integer> squarePrinter = n -> System.out.println(n * n);

        numbers.forEach(squarePrinter);

    }
}
```

### Output

```
1
4
9
16
```

---

# 2. Predicate

## Definition

`Predicate<T>` represents a condition.

It:

- takes one input
    
- returns boolean
    

Used for testing conditions.

### Method

```java
boolean test(T t)
```

---

## Example 1 — Check a condition

### Code

```java
import java.util.function.Predicate;

public class PredicateExample {

    public static void main(String[] args) {

        Predicate<Integer> isAdult = age -> age >= 18;

        System.out.println(isAdult.test(20));
        System.out.println(isAdult.test(15));

    }
}
```

### Output

```
true
false
```

---

## Example 2 — Filtering data

### Code

```java
import java.util.List;
import java.util.function.Predicate;

public class PredicateExample {

    public static void main(String[] args) {

        List<Integer> numbers = List.of(1,2,3,4,5,6);

        Predicate<Integer> isEven = n -> n % 2 == 0;

        numbers.stream()
               .filter(isEven)
               .forEach(System.out::println);

    }
}
```

### Output

```
2
4
6
```

---

# 3. Combining Predicate Conditions

Predicate supports logical composition.

### Code

```java
import java.util.function.Predicate;

public class PredicateExample {

    public static void main(String[] args) {

        Predicate<Integer> greaterThan10 = n -> n > 10;
        Predicate<Integer> lessThan20 = n -> n < 20;

        Predicate<Integer> condition = greaterThan10.and(lessThan20);

        System.out.println(condition.test(15));
        System.out.println(condition.test(25));

    }
}
```

### Output

```
true
false
```

---

# 4. Quick Comparison

|Interface|Input|Output|Purpose|
|---|---|---|---|
|Consumer|T|void|perform an action|
|Predicate|T|boolean|test a condition|