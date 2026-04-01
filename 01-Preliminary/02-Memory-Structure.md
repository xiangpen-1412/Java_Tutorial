___
## Purpose

This note summarizes the core runtime storage areas in Java and the JVM. The goal is to build a clear mental model of **where data lives during execution**, **what each region is responsible for**, and **which common runtime problems are associated with each region**.

This is a storage-focused note only. It does not cover data structure usage patterns such as stack/queue/heap as abstract ADTs.

---

## Big Picture

Java runtime memory can be understood by separating it into two groups:

### Thread-Private Areas

Each thread has its own copy:

- **PC Register**
    
- **JVM Stack**
    
- **Native Method Stack**
    

### Thread-Shared Areas

All threads share them:

- **Heap**
    
- **Method Area**
    
- **Runtime Constant Pool**
    

In HotSpot JVM, the Method Area is commonly associated with **Metaspace** for class metadata storage.

---

## 1. PC Register

### What it is

The **Program Counter Register** is a small thread-private storage area that records the current execution position of a thread.

### What it stores

- The address / position of the current bytecode instruction being executed
    
- The location the thread should continue from after scheduling resumes it
    

### Why it exists

Java threads can be paused and resumed. The JVM needs a way to know where each thread was executing before a context switch.

### Key properties

- Thread-private
    
- Very small
    
- Used for control flow, not for storing objects or method-local data
    

### Core idea

The PC register answers:

**“What instruction is this thread currently executing?”**

---

## 2. JVM Stack

### What it is

Each thread owns a **Java Virtual Machine Stack**. Every time a Java method is called, the JVM pushes a new **stack frame** onto that thread's stack.

So the JVM stack is the runtime structure responsible for **method invocation state**.

### What it stores

The JVM stack stores **stack frames**, and each frame represents one method invocation.

A stack frame typically contains:

- **Local Variable Table**
    
- **Operand Stack**
    
- **Return / frame metadata**
    

### Why it matters

The JVM stack is the reason method calls naturally behave in **last-in, first-out** order:

- last called method returns first
    
- earlier caller resumes later
    

### Common error

- **StackOverflowError**
    

This usually happens when:

- recursion is infinite
    
- recursion depth is too large
    
- method call nesting becomes too deep
    

### Core idea

The JVM stack answers:

**“What execution context is needed for the methods currently in progress?”**

---

## 3. Stack Frame

### What it is

A **stack frame** is the runtime execution context of one method call.

If method calls are:

```text
main -> a -> b
```

then the current thread's JVM stack conceptually looks like:

```text
top
[b frame]
[a frame]
[main frame]
bottom
```

### What a frame contains

#### Local Variable Table

Stores:

- method parameters
    
- local variables
    
- local reference variables
    

Example:

```java
public static void foo(int a) {
    int x = 10;
    String s = "hi";
}
```

The frame for `foo` contains entries for:

- `a`
    
- `x`
    
- `s` (reference value, not the full object body)
    

#### Operand Stack

This is a temporary working stack used by JVM bytecode execution.

For example, for an expression like:

```java
int c = a + b;
```

bytecode execution conceptually does this:

- load `a`
    
- load `b`
    
- push them onto the operand stack
    
- perform addition
    
- push the result
    
- store the result into local variable `c`
    

So the JVM is a **stack-based execution model** at the bytecode level.

#### Return / frame metadata

A frame also contains information needed for:

- returning to the caller
    
- method linkage
    
- restoring execution state
    

### Core idea

A stack frame answers:

**“What exactly does this single method call need in order to execute?”**

---

## 4. Native Method Stack

### What it is

The **Native Method Stack** supports the execution of native methods, meaning methods implemented outside normal Java bytecode, often in C/C++ through JVM internals or JNI.

### What it does

It provides runtime support for non-Java method invocation state.

### How to think about it

At this stage, the important understanding is simple:

- Java code is not always executed purely as Java bytecode
    
- when native code is involved, the JVM may need a separate stack support mechanism
    

### Core idea

The native method stack answers:

**“How is native method execution state managed?”**

---

## 5. Heap

### What it is

The **Heap** is the main runtime memory area used for **objects and arrays**.

Most of the time:

- objects created with `new`
    
- arrays created with `new`
    

are allocated in the heap.

### What it stores

- object instances
    
- array instances
    

Example:

```java
Person p = new Person();
int[] arr = new int[100];
```

Typical mental model:

- `p` is a local reference variable in the current stack frame
    
- the `Person` object lives in the heap
    
- `arr` is a local reference variable in the current stack frame
    
- the array object lives in the heap
    

### Why heap exists

Objects often outlive the method that created them.

Example:

```java
public static Person createPerson() {
    Person p = new Person();
    return p;
}
```

If objects were stored only inside the current method frame, they would disappear when the method returns. The heap exists so dynamically created data can survive beyond one method call.

### Key properties

- Thread-shared
    
- Main area managed by garbage collection
    
- Usually the largest runtime memory region
    

### Common error

- **OutOfMemoryError: Java heap space**
    

Common reasons:

- too many objects created
    
- large collections grow without bound
    
- objects remain reachable and cannot be collected
    
- large arrays or large object graphs consume too much space
    

### Core idea

The heap answers:

**“Where do dynamically created objects and arrays live?”**

---

## 6. Objects and Arrays

### Objects

An object instance stores its **instance fields**.

Example:

```java
class Person {
    int age;
    String name;
}
```

A `Person` object in the heap stores:

- `age` directly as field data
    
- `name` as a reference field value
    

Important distinction:

- a local reference variable may be stored in a stack frame
    
- a reference field inside an object is stored inside the object itself
    

So it is wrong to say:

- “all references are in stack”
    
- “all primitives are in stack”
    

Correct rule:

- **local variables** belong to stack frames
    
- **instance fields** belong to objects
    
- objects themselves live in the heap
    

### Arrays

Arrays are also objects.

Example:

```java
int[] nums = new int[5];
String[] names = new String[3];
```

The array object in the heap stores:

- length information
    
- element storage
    

For reference arrays like `String[]`, each slot stores a reference, not the full target object body.

---

## 7. Method Area

### What it is

The **Method Area** is a shared runtime area used for **class-level information**, not normal object instances.

### What it stores

It typically includes:

- class metadata
    
- method information
    
- field information
    
- bytecode-related method structures
    
- static fields
    
- runtime constant pool
    

### Why it matters

A class definition must exist independently of the objects created from it.

Example:

```java
class Person {
    private String name;
    public void sayHello() {}
}
```

The JVM must know:

- the class name
    
- its fields
    
- its methods
    
- modifiers
    
- parent class
    
- implemented interfaces
    

This information is not object instance data. It belongs to class-level storage.

### Core idea

The Method Area answers:

**“Where does the JVM keep the structural information of classes?”**

---

## 8. Metaspace

### What it is

In modern HotSpot JVM implementations, **Metaspace** is the main area used for class metadata storage.

### Relationship with Method Area

- **Method Area** is the JVM-specification-level concept
    
- **Metaspace** is the HotSpot implementation-level mechanism commonly used for that role
    

So in learning notes, it is useful to separate:

- abstract concept: **Method Area**
    
- concrete HotSpot term: **Metaspace**
    

### Why it matters

This distinction helps avoid confusion between specification language and JVM implementation language.

---

## 9. Class Metadata

### What it is

Class metadata is the structural description of a loaded class.

Example:

```java
class Person {
    private String name;
    public void sayHello() {}
}
```

Class metadata includes information such as:

- class name
    
- field definitions
    
- method definitions
    
- method signatures
    
- modifiers
    
- superclass
    
- implemented interfaces
    

### Where it belongs

Class metadata belongs to class-level runtime storage, typically associated with the Method Area / Metaspace.

### Important distinction

Example:

```java
Person p1 = new Person();
Person p2 = new Person();
```

- `p1` and `p2` are two separate heap objects
    
- but `Person` class metadata exists once as shared class information
    

---

## 10. Static Fields

### What they are

Static fields belong to the **class**, not to each instance.

Example:

```java
class Person {
    static int count = 0;
    String name;
}
```

Here:

- `count` is a static field
    
- `name` is an instance field
    

### Difference from instance fields

#### Instance field

Each object owns its own copy.

```java
Person a = new Person();
Person b = new Person();
```

Then `a.name` and `b.name` are distinct fields inside different heap objects.

#### Static field

`Person.count` is shared at the class level.

### Mental model

- instance field → belongs to object
    
- static field → belongs to class
    

So static storage should be understood together with class-level runtime storage rather than ordinary per-object heap state.

---

## 11. Runtime Constant Pool

### What it is

Each `.class` file contains a **constant pool** with symbolic information such as:

- literals
    
- class names
    
- field names
    
- method names
    
- symbolic references
    

After the class is loaded, this becomes runtime-usable structure called the **Runtime Constant Pool**.

### What it stores

Typical contents include:

- literal constants
    
- symbolic references to classes
    
- symbolic references to methods
    
- symbolic references to fields
    

### Why it matters

The JVM uses it during:

- linking
    
- resolution
    
- field access
    
- method invocation
    

### Core idea

The runtime constant pool answers:

**“Where does the JVM keep symbolic and constant information needed for runtime resolution?”**

---

## 12. String Pool

### What it is

The **String Pool** is a special string-sharing mechanism used by Java to avoid unnecessary duplication of identical string literals.

Example:

```java
String a = "hello";
String b = "hello";
```

In many cases, `a` and `b` refer to the same pooled string object.

### Why it exists

Strings are used extremely often. Reusing identical literals saves memory and reduces unnecessary object creation.

### Important contrast

```java
String a = "hi";
String b = "hi";
String c = new String("hi");
```

Typical mental model:

- `a` and `b` may share the pooled literal object
    
- `c` creates a new object through `new`
    

### Why it is often studied separately

Because strings behave differently from normal objects created blindly with `new`, and this frequently appears in interviews and bug analysis.

---

## 13. Most Important Distinctions

### Local variable vs instance field

- local variable → current stack frame
    
- instance field → inside heap object
    

### Reference variable vs referenced object

- reference variable stores a reference value
    
- referenced object body lives elsewhere, usually in heap
    

### Instance field vs static field

- instance field → one copy per object
    
- static field → one copy per class
    

### JVM Stack vs data structure stack

- JVM Stack → runtime method invocation mechanism
    
- stack data structure → abstract data structure used in programs
    

They share the same LIFO idea, but they are not the same thing.

### Heap vs Method Area

- Heap → object instances and arrays
    
- Method Area → class-level structures and metadata
    

---

## 14. Common Runtime Errors and Their Memory Meaning

### StackOverflowError

Usually means:

- method call depth is too large
    
- recursion never ends
    
- the JVM stack has run out of space
    

### OutOfMemoryError: Java heap space

Usually means:

- heap allocation pressure is too large
    
- too many live objects remain reachable
    
- object / array growth exceeds heap capacity
    

### Practical takeaway

When diagnosing memory-related runtime problems, the first question is:

**Which runtime area is being exhausted or misused?**

---

## 15. Minimal Mental Map

Use this as the compact review version:

- **PC Register** → current instruction position of a thread
    
- **JVM Stack** → method invocation state of a thread
    
- **Stack Frame** → one method call context
    
- **Native Method Stack** → native call support
    
- **Heap** → objects and arrays
    
- **Method Area** → class-level runtime structures
    
- **Metaspace** → HotSpot class metadata storage mechanism
    
- **Runtime Constant Pool** → constants and symbolic references
    
- **String Pool** → shared string literal mechanism
    
- **Static Fields** → class-level shared state
    

---

## Final Summary

The most important storage model in early Java runtime understanding is this:

- **Execution state** is managed through thread-private runtime structures like the PC register and JVM stack.
    
- **Object data** is mainly stored in the heap.
    
- **Class definitions and shared symbolic information** are stored in class-level shared areas such as the Method Area / Metaspace and runtime constant pool.
    

If this map is clear, later topics become much easier:

- stack frame behavior
    
- recursion and stack overflow
    
- object lifetime
    
- garbage collection
    
- static vs instance fields
    
- string pooling
    
- class loading and linking
    
- bytecode execution model