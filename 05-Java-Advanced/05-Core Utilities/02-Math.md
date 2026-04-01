___
## 1. Purpose of `Math`

`Math` is Java's standard utility class for common mathematical operations. It provides **static methods** only, so it is used directly through the class name rather than through object instantiation.

Typical responsibilities include:

- absolute value
    
- rounding
    
- min/max selection
    
- exponentiation and roots
    
- random number generation
    
- trigonometric functions
    
- logarithmic and exponential functions
    
- exact arithmetic helpers for overflow-sensitive integer operations
    

`Math` is mainly used when business logic needs **numeric transformation**, **boundary control**, **rounding policy**, or **safe arithmetic behavior**.

---

## 2. Basic Characteristics

### 2.1 Static Utility Class

`Math` methods are called like this:

```java
Math.abs(-10)
Math.max(3, 8)
Math.sqrt(25)
```

This means:

- no `new Math()`
    
- methods are globally accessible
    
- usage cost is low and syntax is simple
    

### 2.2 Numeric Type Support

Different methods support different primitive numeric types:

- `int`
    
- `long`
    
- `float`
    
- `double`
    

In practice, many `Math` operations return `double`, especially for:

- `sqrt`
    
- `pow`
    
- `sin`
    
- `cos`
    
- `log`
    

So type conversion awareness is important.

---

## 3. Frequently Used Methods

## 3.1 `abs()`

Returns the absolute value.

```java
Math.abs(-15);    // 15
Math.abs(15);     // 15
Math.abs(-3.5);   // 3.5
```

### Notes

- Often used in distance calculations, difference comparisons, and sign normalization.
    
- For most values, behavior is straightforward.
    
- There is one important edge case for integers:
    

```java
System.out.println(Math.abs(Integer.MIN_VALUE));
```

Output:

```java
-2147483648
```

Why this happens:

- `Integer.MIN_VALUE` is `-2147483648`
    
- `int` range is asymmetric: `-2147483648` to `2147483647`
    
- its positive counterpart cannot be represented in `int`
    

So `Math.abs(Integer.MIN_VALUE)` still returns a negative value.

This is a classic interview and bug-prevention detail.

---

## 3.2 `max()` and `min()`

Used to select the larger or smaller value.

```java
Math.max(10, 20);   // 20
Math.min(10, 20);   // 10
```

### Common Use Cases

- boundary enforcement
    
- score clamping
    
- range normalization
    
- choosing safer limits
    

Example:

```java
int value = 120;
int capped = Math.min(value, 100);
System.out.println(capped); // 100
```

---

## 3.3 `round()`, `floor()`, `ceil()`

These are all related to rounding, but their semantics are different.

### `Math.round()`

Rounds to the nearest integer.

```java
Math.round(3.2);   // 3
Math.round(3.5);   // 4
Math.round(3.8);   // 4
```

Return type:

- `long` for `double`
    
- `int` for `float`
    

### `Math.floor()`

Always rounds down toward negative infinity.

```java
Math.floor(3.9);    // 3.0
Math.floor(-3.1);   // -4.0
```

### `Math.ceil()`

Always rounds up toward positive infinity.

```java
Math.ceil(3.1);    // 4.0
Math.ceil(-3.9);   // -3.0
```

### Important Difference

For negative numbers:

```java
System.out.println(Math.floor(-3.1)); // -4.0
System.out.println(Math.ceil(-3.1));  // -3.0
```

This is a common source of mistakes.

---

## 3.4 `pow()` and `sqrt()`

### `Math.pow(a, b)`

Computes `a` raised to the power of `b`.

```java
Math.pow(2, 3);   // 8.0
Math.pow(5, 2);   // 25.0
```

### `Math.sqrt(x)`

Computes the square root.

```java
Math.sqrt(25);    // 5.0
Math.sqrt(2);     // 1.414213562...
```

### Notes

- both return `double`
    
- even when mathematical output looks like an integer, return type is still `double`
    

```java
double result = Math.pow(2, 10);
System.out.println(result); // 1024.0
```

### Engineering Note

For simple integer squaring, multiplication is often better than `pow()`:

```java
int x = 5;
int square = x * x;
```

Reason:

- clearer intent
    
- avoids floating-point conversion
    
- usually cheaper than `Math.pow()`
    

---

## 3.5 `random()`

Returns a `double` in the range:

```java
[0.0, 1.0)
```

That means:

- includes `0.0`
    
- excludes `1.0`
    

```java
double r = Math.random();
System.out.println(r);
```

### Generating Integer Ranges

Generate a value from `0` to `9`:

```java
int value = (int) (Math.random() * 10);
```

Generate a value from `1` to `10`:

```java
int value = (int) (Math.random() * 10) + 1;
```

Generate a value from `min` to `max`:

```java
int min = 5;
int max = 12;
int value = (int) (Math.random() * (max - min + 1)) + min;
```

### Engineering Note

`Math.random()` is acceptable for:

- simple demos
    
- practice code
    
- basic simulations
    

For more structured code, `Random` or `ThreadLocalRandom` is usually preferred.

---

## 3.6 Trigonometric Methods

Common methods:

- `Math.sin()`
    
- `Math.cos()`
    
- `Math.tan()`
    
- `Math.asin()`
    
- `Math.acos()`
    
- `Math.atan()`
    

These use **radians**, not degrees.

```java
double angle = Math.PI / 2;
System.out.println(Math.sin(angle)); // 1.0
```

### Degree Conversion

Use:

- `Math.toRadians()`
    
- `Math.toDegrees()`
    

```java
double degrees = 90;
double radians = Math.toRadians(degrees);
System.out.println(Math.sin(radians)); // 1.0
```

### Important Note

If you pass `90` directly into `Math.sin(90)`, Java interprets it as 90 radians, not 90 degrees.

---

## 3.7 Logarithmic and Exponential Methods

### `Math.log(x)`

Natural logarithm, base `e`.

### `Math.log10(x)`

Base-10 logarithm.

### `Math.exp(x)`

Computes `e^x`.

```java
System.out.println(Math.log(Math.E));   // 1.0
System.out.println(Math.log10(100));    // 2.0
System.out.println(Math.exp(1));        // 2.7182818...
```

These are more common in scientific, financial, statistical, or algorithmic contexts than in standard CRUD application logic.

---

## 4. `Math` Constants

Two important constants:

```java
Math.PI
Math.E
```

Example:

```java
double area = Math.PI * 5 * 5;
System.out.println(area);
```

---

## 5. Overflow-Aware Exact Arithmetic

For integer arithmetic, Java normally allows overflow silently.

Example:

```java
int a = Integer.MAX_VALUE;
int b = a + 1;
System.out.println(b); // overflowed result
```

This does **not** throw an exception. It wraps around.

To detect overflow explicitly, Java provides exact arithmetic methods.

### Common Methods

- `Math.addExact()`
    
- `Math.subtractExact()`
    
- `Math.multiplyExact()`
    
- `Math.incrementExact()`
    
- `Math.decrementExact()`
    
- `Math.negateExact()`
    
- `Math.toIntExact()`
    

### Example: `addExact()`

```java
int a = Integer.MAX_VALUE;
int b = 1;
int result = Math.addExact(a, b); // throws ArithmeticException
```

### Why This Matters

These methods are useful when:

- silent overflow would corrupt business logic
    
- numeric correctness matters
    
- data size may grow unexpectedly
    
- you want fail-fast behavior
    

---

## 6. Floating-Point Precision Caveat

`double` and `float` use binary floating-point representation.  
That means some decimal values cannot be represented exactly.

Example:

```java
System.out.println(0.1 + 0.2);
```

Output is not exactly:

```java
0.3
```

It may be:

```java
0.30000000000000004
```

### Engineering Implication

Do not use floating-point types for precision-critical money calculations.  
For that, `BigDecimal` is preferred.

---

## 7. Type Conversion Pitfalls

Many `Math` methods return `double`, even if the logical result looks integral.

```java
int x = (int) Math.sqrt(25);
```

This works, but the cast is explicit.

### Risk

Casting from `double` to `int` truncates the decimal part.

```java
int x = (int) 3.99;
System.out.println(x); // 3
```

So when using `Math` methods, always distinguish between:

- rounding
    
- truncation
    
- exact conversion
    

---

## 8. Common Practical Patterns

## 8.1 Clamp a Value into a Range

```java
int value = 150;
int clamped = Math.max(0, Math.min(value, 100));
System.out.println(clamped); // 100
```

This is a common pattern for:

- percentages
    
- ratings
    
- UI ranges
    
- game stats
    
- normalized scores
    

---

## 8.2 Distance Between Two Numbers

```java
int a = 15;
int b = 42;
int distance = Math.abs(a - b);
System.out.println(distance); // 27
```

---

## 8.3 Random Integer in a Range

```java
int min = 20;
int max = 30;
int value = (int) (Math.random() * (max - min + 1)) + min;
System.out.println(value);
```

---

## 8.4 Round to Two Decimal Places

```java
double value = 12.34567;
double rounded = Math.round(value * 100.0) / 100.0;
System.out.println(rounded); // 12.35
```

### Note

This is acceptable for simple display-oriented formatting.  
For financial-grade precision, use `BigDecimal`.

---

## 9. Full Example

```java
public class MathDemo {

    public static void main(String[] args) {
        System.out.println("abs(-9) = " + Math.abs(-9));
        System.out.println("max(12, 7) = " + Math.max(12, 7));
        System.out.println("min(12, 7) = " + Math.min(12, 7));

        System.out.println("round(3.6) = " + Math.round(3.6));
        System.out.println("floor(3.9) = " + Math.floor(3.9));
        System.out.println("ceil(3.1) = " + Math.ceil(3.1));

        System.out.println("pow(2, 5) = " + Math.pow(2, 5));
        System.out.println("sqrt(81) = " + Math.sqrt(81));

        double random = Math.random();
        System.out.println("random = " + random);

        int randomInt = (int) (Math.random() * 6) + 1;
        System.out.println("dice roll = " + randomInt);

        double radians = Math.toRadians(90);
        System.out.println("sin(90 degrees) = " + Math.sin(radians));

        System.out.println("PI = " + Math.PI);
        System.out.println("E = " + Math.E);

        int score = 125;
        int normalized = Math.max(0, Math.min(score, 100));
        System.out.println("normalized score = " + normalized);

        double price = 19.9876;
        double roundedPrice = Math.round(price * 100.0) / 100.0;
        System.out.println("rounded price = " + roundedPrice);
    }
}
```

### Sample Output

```java
abs(-9) = 9
max(12, 7) = 12
min(12, 7) = 7
round(3.6) = 4
floor(3.9) = 3.0
ceil(3.1) = 4.0
pow(2, 5) = 32.0
sqrt(81) = 9.0
random = 0.418...
dice roll = 4
sin(90 degrees) = 1.0
PI = 3.141592653589793
E = 2.718281828459045
normalized score = 100
rounded price = 19.99
```

---

## 10. Interview and Review Pitfalls

### 10.1 `abs(Integer.MIN_VALUE)`

Still negative because positive counterpart cannot be represented.

### 10.2 `pow()` Returns `double`

Even if result is mathematically integral.

### 10.3 `floor()` vs `ceil()` with Negative Values

Direction is based on number line, not on “remove decimal digits”.

### 10.4 `Math.random()` Upper Bound Is Exclusive

`1.0` is never returned.

### 10.5 Trigonometric Functions Use Radians

Passing degrees directly gives incorrect results.

### 10.6 Integer Overflow Is Silent by Default

Use `addExact()` and related methods when overflow must be detected.

### 10.7 Floating-Point Precision Is Not Exact Decimal Precision

Avoid `double` for money-sensitive logic.

---

## 11. When to Use `Math` vs Other Alternatives

Use `Math` when:

- you need standard numeric utilities
    
- the logic is primitive-type based
    
- performance and simplicity matter
    
- the operation is general-purpose
    

Use `BigDecimal` when:

- decimal precision must be controlled strictly
    
- monetary values are involved
    
- rounding rules must be explicit and deterministic
    

Use `Random` / `ThreadLocalRandom` when:

- random generation is frequent
    
- you want clearer API semantics
    
- concurrency or performance matters
    

---

## 12. Summary

`Math` is one of the most commonly used utility classes in Java. Its API looks simple, but correct usage requires attention to:

- return types
    
- integer overflow
    
- floating-point precision
    
- negative-number rounding behavior
    
- radians vs degrees
    

At a review level, the most important parts are not just memorizing the methods, but understanding:

- what kind of numeric model each method uses
    
- which methods can silently lose information
    
- which edge cases can break production logic
    

For interview preparation and industrial coding, the most valuable habits are:

- knowing when a result is `double` rather than `int`
    
- treating overflow as a real risk
    
- distinguishing truncation from rounding
    
- avoiding precision mistakes in decimal-sensitive code
    

---

## 13. Optional Practice Tasks

1. Write a method that clamps any integer into the range `0` to `100`.
    
2. Generate a random integer from `50` to `100`.
    
3. Compute the hypotenuse of a right triangle using `Math.sqrt()`.
    
4. Show the difference between `round`, `floor`, and `ceil` for positive and negative values.
    
5. Use `Math.addExact()` to demonstrate overflow detection.
    
6. Show why `0.1 + 0.2` is not exactly `0.3`.