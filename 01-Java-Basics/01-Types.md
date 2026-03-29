# Types in Java
*(Primitive Types & Reference Types)*

---
## 1. Overview

In Java, every variable belongs to **exactly one of two categories**:

- **Primitive types** → store actual values  
- **Reference types** → store references to objects  

This distinction affects:
- assignment behavior
- parameter passing
- equality comparison
- memory usage
- mutability

Understanding this distinction is foundational for **all Java behavior**.

---
## 2. Primitive Types

### 2.1 Definition

Primitive types store their **actual values directly**.  
They are **not objects** and do not have methods.

---
### 2.2 The 8 Primitive Types

| Type    | Size (bits)   | Description                                                                                                                       |
| ------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| byte    | 8             | signed integer                                                                                                                    |
| short   | 16            | signed integer                                                                                                                    |
| int     | 32            | signed integer (default integer type)                                                                                             |
| long    | 64            | signed integer                                                                                                                    |
| float   | 32            | single-precision floating point                                                                                                   |
| double  | 64            | double-precision floating point (<span style="color:rgb(255, 0, 0)">default</span>)                                               |
| char    | 16            | UTF-16 code unit (<span style="color:rgb(0, 176, 80)">'char'</span> char <span style="color:rgb(255, 0, 0)">"char"</span> String) |
| boolean | JVM-dependent | true / false                                                                                                                      |

---
### 2.3 Characteristics

- Cannot be `null`
- Always have a value
- Stored directly in variables
- Assignment copies the **value itself**
- Comparisons use `==` to compare values

```java
Integer i = null;  
  
try {  
    int y = i;  
    System.out.println(y);  
} catch (NullPointerException e) {  
    System.out.println("Oops Error");  
}
```

---
### 2.4 Cast

Java primitive type casting is divided into **two categories**:

- **Widening conversion** (implicit, safe)
- **Narrowing conversion** (explicit, potentially unsafe)

#### 2.4.1 Widening Conversion (Implicit Cast)

Definition: A **widening conversion** converts a value from a type with a **smaller range** to a type with a **larger range**.

- Happens **automatically**
- **No explicit cast required**
- **No data loss**

```java
byte b = 10;
int i = b;          // implicit widening
double d = i;       // implicit widening

char c = 'A';
int x = c;          // implicit widening (65)
```

##### Key Properties

- Always safe
- Guaranteed not to overflow
- Happens at **compile time**
- Common in expressions and assignments
#### 2.4.2 Narrowing Conversion (Explicit Cast)

Definition: A **narrowing conversion** converts a value from a type with a **larger range** to a type with a **smaller range**.

- **Requires explicit cast**
- **May lose data**
- Compiler forces you to acknowledge the risk

```java
int i = 130;
byte b = (byte) i;  // explicit narrowing

double d = 3.99;
int x = (int) d;    // x == 3 (fraction truncated)
```

---
## 3. Wrapper Types

### 3.1 Definition

Each primitive type has a corresponding **wrapper class**, which is a **reference type (object)**.

| Primitive | Wrapper |
|----------|---------|
| byte     | Byte |
| short    | Short |
| int      | Integer |
| long     | Long |
| float    | Float |
| double   | Double |
| char     | Character |
| boolean  | Boolean |

---
### 3.2 Properties of Wrapper Types

- Are objects (reference types)
- Can be `null`
- Stored on the heap
- Provide utility methods (e.g. `Integer.parseInt`)
- Required for generics and collections
- <span style="color: red;"><b>Immutable</b></span>
	- You **cannot modify** its internal value 
	- <span style="color:rgb(255, 255, 0)">Any “change” creates a <b>new object</b></span>
	- `+=` on wrappers is **not mutation**, it is **reassignment**

---
### 3.3 Integer Max and Min 

```java
System.out.println(Integer.MAX_VALUE); // 2147483647  
System.out.println(Integer.MIN_VALUE); // -2147483648  
System.out.println(Integer.MAX_VALUE + 1); // -2147483648  
System.out.println(Integer.MIN_VALUE - 1); // 2147483647
```

---
## 4. Autoboxing and Unboxing

### 4.1 Definitions

- **Autoboxing**: automatic conversion from primitive → wrapper  
- **Unboxing**: automatic conversion from wrapper → primitive  

```java
Integer x = 10;   // autoboxing
int y = x;        // unboxing0
```

---
### 4.2 <span style="color:rgb(255, 0, 0)">Important Rule</span>

1. Unboxing a `null` wrapper causes a **NullPointerException**.

```java
Integer n = null; 
int v = n; // throws NullPointerException`
```

2. Immutable: changes create a new object

```java
String s = "abc";
s.concat("def"); // s = s.concat("def");
System.out.println(s); // "abc"
```

---
## 5. Assignment Semantics

### 5.1 Primitive Assignment

Primitive assignment copies the **actual value**.

```java
int a = 10; int b = a; b = 20;
```

- `a` remains `10`
- `b` becomes `20`
- No relationship between `a` and `b`

---
### 5.2 Reference Assignment

Reference assignment copies the **reference**, not the object.

```java
Box x = new Box(10);  
Box y = x;  
y.a = 15;  

System.out.println(x.a);  // 15
System.out.println(y.a);  // 15
```

- `x` and `y` point to the **same object**
- Modifying the object via one reference affects the other

---
## 6. Reference Types

### 6.1 Definition

A reference type variable stores a **reference (address abstraction)** to an object.

Examples:

- Objects created via `new`
- Arrays
- Strings
	- <span style="color:rgb(255, 0, 0)">isBlank() : whitespaces?</span>
	- <span style="color:rgb(255, 0, 0)">isEmpty() : length = 0</span>
- Wrapper classes
- Custom classes

---
### 6.2 Characteristics

- Can be `null`
- Assignment copies the reference
- Equality via `==` compares references
- Object state can be mutable or immutable
-  <span style="color: red;"><b>Mutable</b></span>
	- You **can modify** its internal value 

---
## 7. Arrays

### 7.1 Arrays Are Reference Types

All arrays in Java are objects.

```java
int[] a = {1, 2, 3, 4, 5};  
int[] b = a;  
// a and b points to the same array
  
b[0] = 10;  
// System.out.println(a); [I@65ab7765 
System.out.println(Arrays.toString(a)); // [10, 2, 3, 4, 5]  
System.out.println(Arrays.toString(b)); // [10, 2, 3, 4, 5]
```

- `a` and `b` refer to the same array
    
- Modifying `b[0]` affects `a[0]`
    

---

### 7.2 Array Copying

To create <span style="color:rgb(255, 0, 0)">a new independent array</span>, a **copy** must be made.

Common approaches:

- `Arrays.copyOf`
- `System.arraycopy`
- Manual loop copy

```java
int[] a = {1, 2, 3, 4, 5};  
int[] b = Arrays.copyOf(a, a.length);  
  
b[0] = 10;  
System.out.println(Arrays.toString(a)); // [1, 2, 3, 4, 5]  
System.out.println(Arrays.toString(b)); // [10, 2, 3, 4, 5]
```

---

## 8. The `final` Keyword and References

### 8.1 `final` on Primitive Variables

```java 
final int x = 10;
```

- Value cannot be reassigned
    
- Variable is immutable
    

---

### 8.2 `final` on Reference Variables

```java
final Box b = new Box(10); 
b.value = 20;     // allowed 
b = new Box(30);  // compilation error`
```

Key rule:

> `final` prevents **reassignment of the reference**, not mutation of the object.

---
