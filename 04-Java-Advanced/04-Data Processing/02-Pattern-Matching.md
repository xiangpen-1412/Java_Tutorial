___
## 1. Overview

Java provides powerful support for regular expressions (regex) through:

- `Pattern` → compiled regex rule
    
- `Matcher` → engine that applies the rule to text
    

Core idea:

> Pattern = rule  
> Matcher = rule + input + matching process

---

## 2. Basic Workflow

```java
Pattern pattern = Pattern.compile("\\d+");
Matcher matcher = pattern.matcher("abc123def456");

while (matcher.find()) {
    System.out.println(matcher.group());
}
```

Output:

```
123
456
```

### Steps Explained

1. Compile regex → `Pattern`
    
2. Apply to input → `Matcher`
    
3. Iterate matches → `find()`
    
4. Extract results → `group()`
    

---

## 3. Pattern

### Definition

```java
Pattern pattern = Pattern.compile(regex);
```

### Example

```java
Pattern pattern = Pattern.compile("\\d+");
```

Meaning:

- `\d` → digit
    
- `+` → one or more
    

So it matches: `123`, `456`, etc.

---

## 4. Matcher

### Definition

```java
Matcher matcher = pattern.matcher(input);
```

### Example

```java
Matcher matcher = pattern.matcher("abc123def456");
```

Matcher maintains:

- current scan position
    
- last match result
    
- group data
    

---

## 5. Core Matching Methods

### 5.1 matches()

```java
matcher.matches();
```

Meaning:

> Entire string must match regex

Example:

```java
Pattern p = Pattern.compile("\\d+");
Matcher m = p.matcher("123");
System.out.println(m.matches()); // true
```

---

### 5.2 find() ⭐ (MOST IMPORTANT)

```java
matcher.find();
```

Meaning:

> Find NEXT matching substring

Example:

```java
Pattern pattern = Pattern.compile("\\d+");
Matcher matcher = pattern.matcher("abc123def456");

while (matcher.find()) {
    System.out.println(matcher.group());
}
```

Output:

```
123
456
```

### Key Behavior

- NOT full match
    
- scans left → right
    
- continues from last match position
    

---

### 5.3 lookingAt()

```java
matcher.lookingAt();
```

Meaning:

> Match from beginning only

---

## 6. find() Deep Understanding

### Internal Behavior

String:

```
abc123def456
```

Regex:

```
\d+
```

Steps:

1st find():

- starts at index 0
    
- finds `123`
    

2nd find():

- continues from index 6
    
- finds `456`
    

3rd find():

- no more matches → false
    

---

## 7. group() – Extract Result

```java
matcher.group();
```

Meaning:

> Return last matched substring

Example:

```java
while (matcher.find()) {
    System.out.println(matcher.group());
}
```

---

## 8. start() and end()

```java
matcher.start();
matcher.end();
```

Example:

```java
while (matcher.find()) {
    System.out.println(matcher.group());
    System.out.println(matcher.start());
    System.out.println(matcher.end());
}
```

Meaning:

- start() → start index
    
- end() → end index (exclusive)
    

Range:

```
[start, end)
```

---

## 9. Capturing Groups

### Example: Date Extraction

```java
Pattern pattern = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
Matcher matcher = pattern.matcher("2026-03-27");

if (matcher.find()) {
    System.out.println(matcher.group());   // full
    System.out.println(matcher.group(1));  // year
    System.out.println(matcher.group(2));  // month
    System.out.println(matcher.group(3));  // day
}
```

---

### Group Rules

- `group()` = full match
    
- `group(1)` = first ()
    
- `group(2)` = second ()
    

Numbering:

> Left to right by opening parentheses

---

## 10. Nested Groups

```java
Pattern pattern = Pattern.compile("(name=(\\w+)), age=(\\d+)");
Matcher matcher = pattern.matcher("name=Tom, age=20");

if (matcher.find()) {
    System.out.println(matcher.group(1)); // name=Tom
    System.out.println(matcher.group(2)); // Tom
    System.out.println(matcher.group(3)); // 20
}
```

---

## 11. Matching Literal Parentheses

### Problem

`() is group syntax`

### Solution

Escape them

Regex:

```
\(\d+\)
```

Java:

```java
"\\(\\d+\\)"
```

---

### Example

```java
Pattern pattern = Pattern.compile("\\((\\d+)\\)");
Matcher matcher = pattern.matcher("(123)");

if (matcher.find()) {
    System.out.println(matcher.group());    // (123)
    System.out.println(matcher.group(1));   // 123
}
```

---

## 12. Replacement with Matcher

### Example: Date Format Change

```java
Pattern pattern = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
Matcher matcher = pattern.matcher("2026-03-27");

String result = matcher.replaceAll("$2/$3/$1");
System.out.println(result);
```

Output:

```
03/27/2026
```

---

### Group Reference in Replacement

- `$1` → group 1
    
- `$2` → group 2
    

---

## 13. Real Example: Phone Parsing

```java
Pattern pattern = Pattern.compile("\\((\\d{3})\\)\\s(\\d{3})-(\\d{4})");
Matcher matcher = pattern.matcher("(416) 555-1234");

if (matcher.find()) {
    System.out.println(matcher.group(1)); // 416
    System.out.println(matcher.group(2)); // 555
    System.out.println(matcher.group(3)); // 1234
}
```

---

## 14. matches vs find

|Method|Meaning|
|---|---|
|matches()|whole string|
|find()|partial match|

Example:

```java
Pattern p = Pattern.compile("\\d+");
Matcher m = p.matcher("abc123");

m.matches(); // false
m.find();    // true
```

---

## 15. Common Mistakes

### ❌ Using group() without find()

```java
matcher.group(); // ERROR
```

### ❌ Confusing matches with find

### ❌ Forgetting escape in Java

```
\d not \d
```

### ❌ Misunderstanding parentheses

- `( )` = group
    
- `\( \)` = literal
    

---

## 16. Key Takeaways

- Pattern = compiled regex
    
- Matcher = engine
    
- find() = scan text ⭐
    
- group() = extract result
    
- () = capture
    
- ( ) = literal parentheses
    
- replaceAll() + $1 = powerful transformation
    

---

## 17. Core Template (MUST REMEMBER)

```java
Pattern pattern = Pattern.compile(regex);
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.println(matcher.group());
}
```

This is the most important pattern in Java regex.