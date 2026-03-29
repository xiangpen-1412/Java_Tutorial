# Java Runtime Memory & Structure

This document gives a **complete, structured view** of how a normal Java program runs in memory, with a **special focus on call frames (stack frames)**. It is written as a long‑term reference note.

---

## 1. High‑Level JVM Memory Layout

When a Java program runs, the JVM process manages several distinct memory areas:

- **Stack (per thread)** – method execution and call frames
- **Heap (shared)** – all objects and arrays
- **Metaspace (shared)** – class metadata (Java 8+)
- **Code Cache (shared)** – JIT‑compiled machine code
- **Native Memory** – JVM internals and native libraries

Each area has a different lifetime, responsibility, and failure mode.

---

## 2. Stack (Thread Stack)

### Purpose

The stack stores **method execution state** for a single thread.  
Each Java thread has its **own independent stack**.

### What the stack contains

- A sequence of **call frames (stack frames)**
- One frame per active method call
- Local variables and primitive types values

### Key properties

- Fast allocation and deallocation
    
- Memory is reclaimed automatically when methods return
    
- Limited size → deep recursion may cause `StackOverflowError`
    

---

## 3. Call Frame (Stack Frame)

### Definition

A **call frame** is a block of memory created on the thread stack **every time a method is invoked**. It represents the complete execution context of that method.

### One sentence summary

> A call frame stores a method’s local variables, parameters, and execution state, and is destroyed immediately when the method exits.

---

## 4. Internal Structure of a Call Frame

A conceptual call frame consists of the following parts:

### 4.1 Local Variable Table

Stores:

- Method parameters
    
- Local variables declared inside the method
    
- The `this` reference (for instance methods)
    

Notes:

- Primitive types store their actual values
    
- Reference types store **references to heap objects**, not the objects themselves
    

---

### 4.2 Operand Stack

Used internally by the JVM bytecode engine to perform calculations.

Example:

```java
int z = x + y;
```

Execution conceptually:

1. Push `x` onto operand stack
    
2. Push `y` onto operand stack
    
3. Execute addition instruction
    
4. Push result
    
5. Store result in `z`
    

This stack is **not visible in Java source code**, but crucial to JVM execution.

---

### 4.3 Return Address

Stores where execution should resume **after the method finishes**.

When a method returns:

- The current frame is popped
    
- Control resumes at the return address in the caller’s frame
    

---

### 4.4 Frame Metadata

Internal JVM bookkeeping information such as:

- Method reference
    
- Constant pool pointer
    
- Access and verification data
    

---

## 5. Call Stack (Stack of Call Frames)

The **call stack** is the ordered stack of call frames for a thread.

Example call sequence:

```text
main() → methodA() → methodB()
```

Stack layout during execution:

```text
| methodB frame |  ← currently executing
| methodA frame |
| main frame    |
```

Frames are popped in **last‑in, first‑out (LIFO)** order.

---

## 6. Heap

### Purpose

The heap stores **all Java objects and arrays** created at runtime.

### What lives on the heap

- Objects created via `new`
    
- Instance fields
    
- Array elements
    
- String objects
    

### Properties

- Shared across all threads
    
- Managed by the garbage collector (GC)
    
- Larger but slower than stack memory
    

---

## 7. Relationship Between Stack and Heap

Example:

```java
User u = new User("Alice");
```

Memory view:

```text
Stack frame:   u ─────────────┐
                               ↓
Heap:        User{name="Alice"}
```

Key rule:

- **References live in stack frames**
    
- **Objects live in the heap**
    

---

## 8. Metaspace (Method Area)

### Purpose

Stores **class‑level metadata**, not object instances.

### Contents

- Class names and hierarchy
    
- Method and field descriptors
    
- Annotations
    
- Runtime constant pool structures
    

### Characteristics

- Shared across threads
    
- Allocated from native memory (Java 8+)
    
- Can throw `OutOfMemoryError: Metaspace`
    

---

## 9. Code Cache

### Purpose

Stores native machine code produced by the JIT compiler.

### Behavior

- Hot bytecode is compiled into native instructions
    
- Native code is cached for fast execution
    

If full, JVM may stop compiling new methods, affecting performance.

---

## 10. Complete Execution Example

```java
public class MemoryExample {

    static class User {
        String name;
        int age;

        User(String name, int age) {
            this.name = name;
            this.age = age;
        }
    }

    static void update(User u) {
        u.age = 30;
        User temp = new User("Bob", 20);
    }

    public static void main(String[] args) {
        User user = new User("Alice", 25);
        update(user);
        System.out.println(user.age);
    }
}
```

### Execution reasoning

1. `main` frame is created on the stack
    
2. `user` reference is stored in `main` frame
    
3. `User("Alice", 25)` object is created on the heap
    
4. `update` frame is created
    
5. `u` points to the same heap object as `user`
    
6. `u.age = 30` modifies the heap object
    
7. `temp` points to a new heap object (`Bob`)
    
8. `update` returns → its frame is popped
    
9. `temp` becomes unreachable and eligible for GC
    
10. `user.age` prints `30`
    

---

## 11. StackOverflowError Explained

```java
void recurse() {
    recurse();
}
```

Each call creates a new call frame.  
No frame is ever popped.  
The thread stack fills up, resulting in:

```text
java.lang.StackOverflowError
```

This error is **entirely stack‑related** and unrelated to heap or GC.

---

## 12. Summary (Mental Model)

- **Stack**: method calls and local execution state
    
- **Call frame**: one method’s execution context
    
- **Heap**: all objects and arrays
    
- **Metaspace**: class definitions
    
- **Code Cache**: JIT‑compiled native code
    

Mastering call frames means you understand:

- Method calls
    
- Parameter passing
    
- Recursion limits
    
- Exception propagation
    
- Performance behavior