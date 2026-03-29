# How a Java Program Runs (Source → JVM)

Below is a clean, copy‑paste‑friendly explanation you can keep in your notes.

---

## 1) From `.java` to `.class` (Compile time)

### What you write

You write Java source code (text files):

- `HelloApp.java`

### What the compiler does (`javac`)

When you compile, the Java compiler:

1. **Parses** your code (checks syntax)
    
2. **Type-checks** it (e.g., `int` vs `String`)
    
3. Resolves <span style="color:rgb(255, 0, 0)"><b>overloading</b></span> (chooses which overloaded method _would_ be called based on argument types)
    
4. Produces <span style="color:rgb(255, 0, 0)"><b>bytecode</b></span>: `HelloApp.class`
    

**Bytecode** is not CPU machine code. It’s a platform‑independent instruction format designed for the JVM.

---

## 2) Starting the JVM (Run time)

When you run:

```bash
java HelloApp
```

the JVM process starts and sets up memory areas (high level):

- **Heap**: where objects live (e.g., `new User()`)
- **Stacks** (per thread): method call frames, local variables, objects references
- **Metaspace**: class metadata (class structure, method info, constant pools, etc.)
- Internal structures for **GC** (garbage collection)

---

## 3) Class Loading (Run time)

Before `main` can execute, the JVM must load the class that contains it.

### 3.1 Load

The JVM finds `HelloApp.class` (via the classpath/module path), reads it, and creates a `Class` object in memory.

### 3.2 Link

Linking usually includes:

- **Verify**: checks bytecode safety/integrity
    
- **Prepare**: allocates memory for `static` fields and sets **default values**
    
- **Resolve**: converts symbolic references (names) into direct references (addresses/handles)
    

### 3.3 Initialize

The JVM runs class initialization:

- Executes `static { ... }` blocks
    
- Executes explicit `static` field initializers
    

---

## 4) Entering `main` (Run time)

The JVM calls:

```java
public static void main(String[] args)
```

Every method call creates a **stack frame** on the current thread’s stack.  
A stack frame typically holds:

- local variables
    
- operand stack (for bytecode operations)
    
- return address
    

---

## 5) Object Creation (Run time)

When you do:

```java
User u = new User();
```

Conceptually:

1. JVM allocates memory for the `User` object on the **heap**
    
2. Fields get **default initialization** (0 / false / null)
    
3. The constructor runs (your initialization logic)
    
4. The reference (address/handle) is stored in `u` (a local variable on the stack)
    

Important: **`u` is not the object**. `u` is a reference; the object data is in the heap.

---

## 6) How Code Actually Executes: Interpreter + JIT (Run time)

JVM execution is typically a mix of:

### Interpreter

Executes bytecode directly. Fast startup, slower per instruction. <span style="color:rgb(255, 0, 0)">No need to turn to machine code but still can be executed.</span>

### JIT (Just‑In‑Time compilation)

When the JVM detects “hot” code (executed frequently), it compiles that bytecode into native machine code.  
After that, the JVM can run the machine code directly, making long‑running programs much faster.

A practical mental model:

- **Start**: mostly interpreted
    
- **After warming up**: hotspots get JIT‑compiled and speed up
    

---

## 7) Garbage Collection (Run time)

Objects become eligible for GC when they are no longer reachable from **GC roots**, such as:

- local variables on thread stacks
    
- static references
    
- JNI references
    

The GC periodically reclaims memory from unreachable objects.

---

## 8) A concrete example you can compile and run

### `HelloApp.java`

```java
public class HelloApp {

    static {
        System.out.println("[Class init] static block runs once");
    }

    static int staticValue = initStatic();

    private static int initStatic() {
        System.out.println("[Class init] static field initializer runs");
        return 42;
    }

    public static void main(String[] args) {
        System.out.println("[main] start");

        User u = new User("Alice");
        System.out.println("[main] created user: " + u.name);

        System.out.println("[main] end");
    }

    static class User {
        String name;

        User(String name) {
            System.out.println("[constructor] User(String) runs");
            this.name = name;
        }
    }
}
```

### What you’ll see (typical order)

1. static block
    
2. static field initializer
    
3. main start
    
4. constructor
    
5. main created user
    
6. main end
    

This demonstrates the **class initialization happens before `main`**, and object construction happens during `main`.