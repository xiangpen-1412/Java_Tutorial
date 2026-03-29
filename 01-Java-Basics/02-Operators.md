# Operators & Numeric Promotion in Java

---

## 1. Categories of Operators

Java operators can be grouped as:

- Arithmetic: `+ - * / %`
- Unary: `+ - ++ -- ! ~`
- Assignment: `= += -= *= /= %=`
- Comparison: `== != > < >= <=`
- Logical: `&& || !`
- Bitwise: `& | ^ ~ << >> >>>`
- Ternary: `?:`

This chapter focuses on **arithmetic behavior and type promotion**.

---

## 2. Numeric Promotion (Core Rule)

### Definition
Numeric promotion is the process where Java **automatically converts operands to a common type before evaluation**.

### Fundamental Rules
1. `byte`, `short`, and `char` are promoted to `int` before any arithmetic
2. If one operand is `double`, result is `double`
3. Else if one operand is `float`, result is `float`
4. Else if one operand is `long`, result is `long`
5. Otherwise, result is `int`

---