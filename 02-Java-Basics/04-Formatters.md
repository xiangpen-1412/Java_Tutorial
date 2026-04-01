# Java Formatters

---

## 1. Why `printf` Uses `%` Instead of `\n`

### Different Roles

- `%` **introduces a format specifier**
    
    - It is a placeholder for **runtime values** (arguments passed to `printf`)
        
- `\` **introduces an escape sequence**
    
    - It represents **literal characters** inside a string
        

They do **not** serve the same purpose.

### Comparison

```java
System.out.println("age=" + age + "\n");
System.out.printf("age=%d%n", age);
```

- `\n` is interpreted **by the Java compiler** while building the string
    
- `%d` is interpreted **by the Formatter at runtime**
    

### Key Distinction

|Symbol|Processed By|Purpose|
|---|---|---|
|`\n`|Java compiler|Embed special characters in string literals|
|`%`|Formatter runtime|Inject and format values|

### Why `%n` Instead of `\n`

- `%n` is a **platform-independent newline**
    
- `\n` is a literal line-feed character
    

```java
System.out.printf("line1%nline2%n");
```

---

## 2. `printf` Format Specifier Structure

Every formatter follows this structure:

```
%[argument_index$][flags][width][.precision]conversion
```

Example:

```java
System.out.printf("%2$-10.2f", 3.14159, 2.71828);
```

---

## 3. Conversion Characters (All `printf` Formatters)

### 3.1 General / Text

|Specifier|Meaning|
|---|---|
|`%s`|String (`toString()` result)|
|`%S`|Uppercase string|
|`%c`|Character|
|`%C`|Uppercase character|
|`%%`|Literal `%`|
|`%n`|Platform-independent newline|

```java
System.out.printf("%s %C %% %n", "java", 'a');
```

---

### 3.2 Integer Numbers

|Specifier|Meaning|
|---|---|
|`%d`|Decimal integer|
|`%o`|Octal|
|`%x`|Hexadecimal (lowercase)|
|`%X`|Hexadecimal (uppercase)|

```java
System.out.printf("%d %o %x %X%n", 26, 26, 26, 26);
```

---

### 3.3 Floating-Point Numbers

|Specifier|Meaning|
|---|---|
|`%f`|Fixed-point decimal|
|`%e`|Scientific notation (lowercase)|
|`%E`|Scientific notation (uppercase)|
|`%g`|General format (shorter of `%f` or `%e`)|
|`%a`|Hexadecimal floating-point|

```java
System.out.printf("%f %.2f%n", 3.14159, 3.14159);
System.out.printf("%e %g%n", 12345.0, 12345.0);
```

---

### 3.4 Boolean

|Specifier|Meaning|
|---|---|
|`%b`|Boolean value|
|`%B`|Uppercase boolean|

Rules:

- `null` → `false`
    
- non-null object → `true`
    

```java
System.out.printf("%b %b%n", true, null);
```

---

### 3.5 Hash Code

|Specifier|Meaning|
|---|---|
|`%h`|Hex hash code|
|`%H`|Uppercase hex hash code|

```java
System.out.printf("%h%n", "abc");
```

---

### 3.6 Date / Time (`%t` / `%T`)

Used with `java.util.Date` or `java.time` types.

|Specifier|Meaning|
|---|---|
|`%tF`|ISO date `YYYY-MM-DD`|
|`%tT`|Time `HH:MM:SS`|
|`%tR`|Time `HH:MM`|
|`%tY`|Year (4 digits)|
|`%tm`|Month (01-12)|
|`%td`|Day of month|
|`%tH`|Hour (00-23)|
|`%tM`|Minute|
|`%tS`|Second|

```java
LocalDateTime now = LocalDateTime.now();
System.out.printf("%tF %tT%n", now, now);
```

---

## 4. Flags (Used with `printf`)

|Flag|Effect|
|---|---|
|`-`|Left-justify|
|`+`|Always show sign|
|space|Leading space for positive numbers|
|`0`|Zero-padding|
|`,`|Grouping separator|
|`(`|Parentheses for negatives|

```java
System.out.printf("|%-10s|%05d|%,d|%(d%n", "id", 7, 1234567, -42);
```

---

## 5. Width and Precision

### Width

Minimum output width.

```java
System.out.printf("|%10s|%n", "cat"); // |       cat|
```

### Precision

- `%f` → digits after decimal point
    
- `%s` → maximum characters
    

```java
System.out.printf("%.2f %.4s%n", 3.14159, "abcdef"); // 3.14 abcd
```

---

## 6. Argument Indexing

Allows argument reordering.

```java
System.out.printf("%2$s %1$d%n", 404, "status"); // status 404
```

---

## 7. Type Safety (Common Runtime Errors)

- `%d` → integer types only
    
- `%f` → floating-point types only
    
- Mismatch throws `IllegalFormatConversionException`
    

```java
// System.out.printf("%d", 3.14); // runtime error
System.out.printf("%.2f%n", 3.14);
```

---

## Summary

- `%` introduces **format placeholders** processed at runtime
    
- `\` introduces **escape characters** processed at compile time
    
- `printf` combines formatting and output in a single step
    
- `%n` should be preferred over `\n` for portability