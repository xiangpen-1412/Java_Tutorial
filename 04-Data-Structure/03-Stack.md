___
## What Is a Stack?

A **stack** is a linear data structure that follows the **LIFO** rule: **Last In, First Out**.

This means the most recently added element is the first one removed.

You can think of a stack like a pile of plates:

- you put a new plate on the top
    
- you remove a plate from the top
    
- you do not normally remove a plate from the middle
    

So the key idea of a stack is not just how the data is stored, but **how access is restricted**.

In a stack, insertion and removal both happen at the same end, called the **top**.

---

## Core Characteristics

A stack has the following properties:

- It is a **linear** data structure.
    
- It follows the **LIFO** rule.
    
- It only allows operations at one end.
    
- It is usually used when the problem involves:
    
    - recent elements
        
    - nested structures
        
    - backtracking
        
    - undo operations
        
    - expression evaluation
        

---

## Basic Operations

### 1. `push`

Adds an element to the top of the stack.

Example:

```text
Stack: [1, 2]
push(3)
Result: [1, 2, 3]
```

### 2. `pop`

Removes and returns the top element.

Example:

```text
Stack: [1, 2, 3]
pop() -> 3
Result: [1, 2]
```

### 3. `peek`

Returns the top element without removing it.

Example:

```text
Stack: [1, 2, 3]
peek() -> 3
Result: [1, 2, 3]
```

### 4. `isEmpty`

Checks whether the stack contains no elements.

### 5. `size`

Returns the number of elements currently in the stack.

---

## Why Does a Stack Behave Like LIFO?

A stack behaves like **Last In, First Out** because you can only access the **top**.

For example:

```text
Bottom [A, B, C, D] Top
```

If you want to remove `B`, you cannot remove it directly. You must first remove:

- `D`
    
- `C`
    

Only then can you reach `B`.

So LIFO is not an arbitrary rule. It is the natural result of the stack's restricted access model.

---

## Stack vs Queue

These two data structures are often compared.

### Stack

- LIFO
    
- insert and remove at the same end
    
- useful for recent-first processing
    

### Queue

- FIFO
    
- insert at one end and remove from the other
    
- useful for arrival-order processing
    

### Example

#### Stack

```text
push 1
push 2
push 3

pop -> 3
pop -> 2
pop -> 1
```

#### Queue

```text
offer 1
offer 2
offer 3

poll -> 1
poll -> 2
poll -> 3
```

---

## Stack vs List

A stack is more restricted than a list.

### List / Array / ArrayList

You can usually:

- access by index
    
- insert in the middle
    
- remove from the middle
    
- iterate freely
    

### Stack

You should only perform stack operations such as:

- `push`
    
- `pop`
    
- `peek`
    

So the essence of a stack is not its appearance, but its **access rule**.

---

## Internal Implementations of a Stack

A stack is an **abstract data structure**, not one fixed implementation.

The two most common underlying implementations are:

- **array-based stack**
    
- **linked-list-based stack**
    

---

## Array-Based Stack

An array-based stack usually stores elements in an array and keeps track of the top position with an index.

### Core Idea

```java
int[] data = new int[10];
int top = -1;
```

- `top = -1` means the stack is empty
    
- `push` increments `top` and stores the new value
    
- `pop` returns `data[top]` and then decrements `top`
    

### Example Process

Initial state:

```text
data = [_, _, _, _, _]
top = -1
```

After `push(10)`:

```text
data = [10, _, _, _, _]
top = 0
```

After `push(20)`:

```text
data = [10, 20, _, _, _]
top = 1
```

After `pop()`:

- return `20`
    
- `top = 0`
    

Logical stack content:

```text
[10]
```

### Advantages

- simple
    
- fast
    
- good memory locality
    
- push/pop at the end are efficient
    

### Disadvantages

- fixed capacity unless resized
    
- resizing requires copying
    
- overflow must be handled if capacity is limited
    

---

## Linked-List-Based Stack

A linked-list-based stack usually uses the head node as the top.

Example:

```text
Top -> 30 -> 20 -> 10 -> null
```

After `push(40)`:

```text
Top -> 40 -> 30 -> 20 -> 10 -> null
```

After `pop()`:

- return `40`
    

Result:

```text
Top -> 30 -> 20 -> 10 -> null
```

### Advantages

- no fixed capacity in advance
    
- push/pop at the head are easy
    
- flexible growth
    

### Disadvantages

- extra pointer memory
    
- weaker cache locality
    
- often less efficient in practice than arrays
    

---

## Time Complexity

For a properly implemented stack, the main operations are usually:

- `push` -> **O(1)**
    
- `pop` -> **O(1)**
    
- `peek` -> **O(1)**
    
- `isEmpty` -> **O(1)**
    
- `size` -> **O(1)**
    

This is one of the main strengths of a stack.

---

## Stack in Java

In Java, there are two levels to understand:

1. the **stack data structure** as an abstract concept
    
2. the **Java API implementation** used to express stack behavior
    

---

## The Legacy `Stack` Class

Java provides a class called:

```java
java.util.Stack
```

Example:

```java
import java.util.Stack;

public class Main {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();

        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println(stack.peek()); // 30
        System.out.println(stack.pop());  // 30
        System.out.println(stack.pop());  // 20
    }
}
```

### Common Methods

```java
stack.push(x);
stack.pop();
stack.peek();
stack.isEmpty();
stack.size();
```

This class works, and you must be able to read it.

---

## Why `Stack` Is Usually Not Recommended Today

Although `Stack` works, modern Java usually prefers another approach.

Main reasons:

### 1. It is a legacy class

`Stack` extends `Vector`, which is part of older Java collection design.

### 2. It carries synchronization overhead

This is often unnecessary in normal single-threaded code.

### 3. It is not abstractly clean

Because it inherits list-style behavior, it exposes operations that do not fit the pure stack abstraction well.

---

## Recommended Java Stack: `Deque` + `ArrayDeque`

Modern Java code usually represents a stack like this:

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
    public static void main(String[] args) {
        Deque<Integer> stack = new ArrayDeque<>();

        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println(stack.peek()); // 30
        System.out.println(stack.pop());  // 30
        System.out.println(stack.pop());  // 20
    }
}
```

### Why this is preferred

- modern API design
    
- better practical performance
    
- no legacy `Vector` baggage
    
- suitable for both stack and queue behavior
    

### Important note

When using `Deque` as a stack, stay consistent with:

- `push`
    
- `pop`
    
- `peek`
    

Do not mix stack-style methods with queue-style methods unless that is your intention.

---

## Handwritten Array-Based Stack in Java

Writing one yourself is the best way to fully understand the data structure.

```java
public class IntStack {
    private int[] data;
    private int top;

    public IntStack(int capacity) {
        data = new int[capacity];
        top = -1;
    }

    public void push(int value) {
        if (top == data.length - 1) {
            throw new RuntimeException("Stack is full");
        }
        data[++top] = value;
    }

    public int pop() {
        if (isEmpty()) {
            throw new RuntimeException("Stack is empty");
        }
        return data[top--];
    }

    public int peek() {
        if (isEmpty()) {
            throw new RuntimeException("Stack is empty");
        }
        return data[top];
    }

    public boolean isEmpty() {
        return top == -1;
    }

    public int size() {
        return top + 1;
    }
}
```

### Example Usage

```java
public class Main {
    public static void main(String[] args) {
        IntStack stack = new IntStack(5);

        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println(stack.peek()); // 30
        System.out.println(stack.pop());  // 30
        System.out.println(stack.pop());  // 20
        System.out.println(stack.size()); // 1
    }
}
```

### Key Details

#### `top = -1`

This means the stack is empty.

#### `data[++top] = value`

This is the push logic:

- move the top forward
    
- place the new value there
    

#### `return data[top--]`

This is the pop logic:

- read the current top value
    
- move the top backward
    

---

## Generic Stack Version

A generic version allows the stack to store any reference type.

```java
public class MyStack<T> {
    private Object[] data;
    private int top;

    public MyStack(int capacity) {
        data = new Object[capacity];
        top = -1;
    }

    public void push(T value) {
        if (top == data.length - 1) {
            throw new RuntimeException("Stack is full");
        }
        data[++top] = value;
    }

    @SuppressWarnings("unchecked")
    public T pop() {
        if (isEmpty()) {
            throw new RuntimeException("Stack is empty");
        }
        T value = (T) data[top];
        data[top] = null;
        top--;
        return value;
    }

    @SuppressWarnings("unchecked")
    public T peek() {
        if (isEmpty()) {
            throw new RuntimeException("Stack is empty");
        }
        return (T) data[top];
    }

    public boolean isEmpty() {
        return top == -1;
    }

    public int size() {
        return top + 1;
    }
}
```

The line below is important:

```java
data[top] = null;
```

It helps avoid keeping unnecessary references after popping an element.

---

## Common Use Cases of Stack

A stack is especially useful when the problem has one of the following patterns.

### 1. Parentheses Matching

Example:

- `()[]{} -> valid`
    
- `([)] -> invalid`
    

Why stack?

Because the most recently opened bracket must be closed first.

### 2. Undo / Redo

The most recent operation is undone first.

### 3. Depth-First Search (DFS)

DFS naturally follows a “go deeper first, backtrack later” process.

### 4. Expression Evaluation

Stacks are widely used in:

- postfix expression evaluation
    
- infix-to-postfix conversion
    
- calculator-style parsing
    

### 5. Monotonic Stack Problems

Very common in interviews:

- Next Greater Element
    
- Daily Temperatures
    
- Largest Rectangle in Histogram
    

---

## How to Recognize a Stack Problem

You should suspect a stack when the problem involves:

- the **most recent unprocessed element**
    
- **backtracking**
    
- **nested structure matching**
    
- **reverse order of completion**
    
- going deep first, then returning step by step
    

Good trigger words include:

- recent
    
- nearest previous
    
- undo
    
- nested
    
- match
    
- backtrack
    

---

## Common Mistakes

### 1. Using `Stack` as the default modern answer

It works, but `Deque` with `ArrayDeque` is usually the better recommendation.

### 2. Calling `pop()` or `peek()` on an empty stack

Always check when necessary:

```java
if (stack.isEmpty()) {
    // handle empty case
}
```

### 3. Mixing stack and queue semantics

If a `Deque` is being used as a stack, use stack-style methods consistently.

### 4. Forgetting that stack is about restricted access

The essence of stack is not “a list of values”.  
It is “a structure where you are only allowed to work at the top”.

---

## Interview-Level Summary

A stack is a linear data structure that follows the LIFO rule. Its core operations are `push`, `pop`, and `peek`, and they are usually O(1). A stack is useful when the problem involves recent-first processing, nested structures, backtracking, or expression handling. In Java, although `java.util.Stack` exists, the modern preferred implementation is usually `Deque<E>` with `ArrayDeque<E>`.

---

## Final Summary

The most important things to remember are:

- A stack follows **LIFO**.
    
- It only allows insertion and removal at the **top**.
    
- Its main operations are **push**, **pop**, and **peek**.
    
- It can be implemented with an **array** or a **linked list**.
    
- In Java, the recommended implementation is usually **`Deque` + `ArrayDeque`**.
    
- Stack is commonly used in **parentheses matching**, **DFS**, **undo systems**, **expression processing**, and **monotonic stack problems**.
    

If you understand the access rule, the LIFO behavior, the implementation options, and the common problem patterns, then you truly understand the stack data structure.