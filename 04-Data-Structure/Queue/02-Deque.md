___
## 1. What Is a Deque?

A **deque** stands for **double-ended queue**.

This means elements can be:

- inserted at the front
    
- inserted at the rear
    
- removed from the front
    
- removed from the rear
    

So unlike a basic queue, which usually supports insertion at one end and removal at the other, a deque allows efficient operations at **both ends**.

---

## 2. Why Deque Matters in Java

Deque is important because it is a more general structure than both:

- a normal **queue**
    
- a traditional **stack**
    

Depending on how you use it, a deque can behave like either one.

### As a queue

If you:

- insert at the rear
    
- remove from the front
    

then it behaves like **FIFO (First In, First Out)**.

### As a stack

If you:

- insert at the front
    
- remove from the front
    

then it behaves like **LIFO (Last In, First Out)**.

This is why `Deque` is often the modern replacement for both queue-only and stack-only use cases.

---

## 3. Where Deque Fits in the Java Collections Framework

The inheritance relationship is:

```java
Iterable
   └── Collection
         └── Queue
               └── Deque
```

This means:

- `Deque` is a subinterface of `Queue`
    
- it supports all queue-like behavior
    
- it adds operations for both the front and the rear
    

So you can think of `Deque` as an extended and more flexible form of `Queue`.

---

## 4. Core Mental Model of Deque

A deque has two logical ends:

- **front**
    
- **rear**
    

Both ends are active.

That is the key idea.

A normal queue usually cares about one insertion end and one removal end.  
A deque allows both ends to participate in insertion, deletion, and inspection.

So the real defining feature of a deque is not FIFO or LIFO by itself.  
Its defining feature is:

> efficient operations on both ends.

FIFO or LIFO is simply the result of how you choose to use it.

---

## 5. Main Method Groups in the Deque Interface

Deque methods are easiest to understand by grouping them by:

- front vs rear
    
- insert vs remove vs inspect
    

---

### 5.1 Front-End Operations

#### Insert at the front

```java
offerFirst(e)
addFirst(e)
```

#### Remove from the front

```java
pollFirst()
removeFirst()
```

#### Inspect the front

```java
peekFirst()
getFirst()
```

---

### 5.2 Rear-End Operations

#### Insert at the rear

```java
offerLast(e)
addLast(e)
```

#### Remove from the rear

```java
pollLast()
removeLast()
```

#### Inspect the rear

```java
peekLast()
getLast()
```

---

### 5.3 Stack-Style Methods

Deque also provides stack-like methods:

```java
push(e)
pop()
peek()
```

These are especially important because in modern Java, `Deque` is usually preferred over the old `Stack` class.

---

## 6. Why There Are Two Versions of Similar Methods

Just like `Queue`, `Deque` provides two styles of methods.

### Exception-throwing methods

Examples:

- `addFirst()`
    
- `removeFirst()`
    
- `getFirst()`
    
- `addLast()`
    
- `removeLast()`
    
- `getLast()`
    

These throw an exception if the operation cannot be completed.

### Special-value methods

Examples:

- `offerFirst()`
    
- `pollFirst()`
    
- `peekFirst()`
    
- `offerLast()`
    
- `pollLast()`
    
- `peekLast()`
    

These return a special value instead:

- `false` for failed insertion
    
- `null` for failed removal or inspection
    

### In practice

In most real code, the safer methods are preferred:

```java
offerFirst()
pollFirst()
peekFirst()

offerLast()
pollLast()
peekLast()
```

because they are easier to use without unnecessary exceptions.

---

## 7. Basic Example of Deque Usage

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
    public static void main(String[] args) {
        Deque<Integer> deque = new ArrayDeque<>();

        deque.offerLast(10);   // [10]
        deque.offerLast(20);   // [10, 20]
        deque.offerFirst(5);   // [5, 10, 20]
        deque.offerLast(30);   // [5, 10, 20, 30]

        System.out.println(deque);              // [5, 10, 20, 30]
        System.out.println(deque.peekFirst());  // 5
        System.out.println(deque.peekLast());   // 30

        System.out.println(deque.pollFirst());  // 5
        System.out.println(deque.pollLast());   // 30

        System.out.println(deque);              // [10, 20]
    }
}
```

### What this example shows

- you can insert from both ends
    
- you can inspect both ends
    
- you can remove from both ends
    

This is the essence of a deque.

---

## 8. Using Deque as a Normal Queue

If you use:

- rear insertion
    
- front removal
    

then the deque behaves like a standard FIFO queue.

Typical method combination:

```java
deque.offerLast(e);
deque.pollFirst();
deque.peekFirst();
```

Example:

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
    public static void main(String[] args) {
        Deque<String> deque = new ArrayDeque<>();

        deque.offerLast("A");
        deque.offerLast("B");
        deque.offerLast("C");

        System.out.println(deque.pollFirst()); // A
        System.out.println(deque.pollFirst()); // B
        System.out.println(deque.pollFirst()); // C
    }
}
```

This is FIFO behavior even though the data structure is a deque.

---

## 9. Using Deque as a Stack

If you use:

- front insertion
    
- front removal
    

then the deque behaves like a stack.

Typical method combination:

```java
deque.offerFirst(e);
deque.pollFirst();
deque.peekFirst();
```

Or you can use the dedicated stack-style methods:

```java
deque.push(e);
deque.pop();
deque.peek();
```

Example:

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
    public static void main(String[] args) {
        Deque<Integer> stack = new ArrayDeque<>();

        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println(stack.pop());  // 30
        System.out.println(stack.pop());  // 20
        System.out.println(stack.peek()); // 10
    }
}
```

This is standard LIFO behavior.

---

## 10. Why Deque Is Preferred Over the Old Stack Class

Java has a legacy class called:

```java
java.util.Stack
```

However, modern Java code usually does **not** prefer it.

### Why?

#### 10.1 `Stack` is an old design

It inherits from `Vector`, which is a legacy class with historical design baggage.

#### 10.2 `Vector` uses old synchronized behavior

That is not usually necessary in ordinary single-threaded algorithm or application code.

#### 10.3 `Deque` is more flexible

A `Deque` can be used as:

- a queue
    
- a stack
    
- a true double-ended queue
    

So modern Java usually prefers:

```java
Deque<Integer> stack = new ArrayDeque<>();
```

instead of:

```java
Stack<Integer> stack = new Stack<>();
```

---

## 11. What Is ArrayDeque?

`ArrayDeque` is one of the main implementations of the `Deque` interface.

The name can be understood as:

- **Array** → array-based implementation
    
- **Deque** → double-ended queue behavior
    

So:

> `ArrayDeque` is a resizable array-based implementation of a deque.

It is one of the most important practical data structures in Java.

---

## 12. ArrayDeque vs LinkedList

Both `LinkedList` and `ArrayDeque` can be used as a deque, but they are fundamentally different internally.

### `LinkedList`

- based on linked nodes
    
- each element is stored inside a node object
    
- each node connects to previous and next nodes
    

### `ArrayDeque`

- based on an array
    
- uses index movement to manage both ends
    
- usually has lower overhead and better cache behavior
    

### Practical takeaway

For most normal queue, stack, or deque scenarios, `ArrayDeque` is usually preferred over `LinkedList`.

---

## 13. Core Internal Idea of ArrayDeque

You do not need to memorize source code, but you should understand the core design.

### 13.1 It is not a linked list

Even though its name contains `Deque`, `ArrayDeque` is array-based.

### 13.2 It uses a circular-array idea

It treats the underlying array like a ring.

So when an index reaches the end of the array, it can wrap around to the beginning.

This allows efficient use of space without shifting all elements for front-end operations.

### 13.3 It tracks both ends logically

Internally, the structure keeps track of where the front and rear are.

Front insertion, rear insertion, front removal, and rear removal can all be done by moving indices instead of physically shifting the entire structure.

That is why it can support efficient double-ended operations.

---

## 14. Why ArrayDeque Is So Important

`ArrayDeque` is heavily used because it combines:

- good performance
    
- flexible semantics
    
- a modern API
    

It is commonly used as:

- a queue implementation
    
- a stack implementation
    
- a deque implementation
    

So in modern Java, a very common pattern is:

```java
Deque<Integer> deque = new ArrayDeque<>();
```

or

```java
Queue<Integer> queue = new ArrayDeque<>();
```

depending on the abstraction you want to expose.

---

## 15. Using ArrayDeque as a Queue

```java
import java.util.ArrayDeque;
import java.util.Queue;

public class Main {
    public static void main(String[] args) {
        Queue<Integer> queue = new ArrayDeque<>();

        queue.offer(1);
        queue.offer(2);
        queue.offer(3);

        System.out.println(queue.poll()); // 1
        System.out.println(queue.poll()); // 2
        System.out.println(queue.peek()); // 3
    }
}
```

### Why this works

Although the concrete class is `ArrayDeque`, the reference type is `Queue`, which means the code exposes queue-only semantics.

This is often a good design choice when you only need FIFO behavior.

---

## 16. Using ArrayDeque as a Deque

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
    public static void main(String[] args) {
        Deque<Integer> deque = new ArrayDeque<>();

        deque.offerFirst(2);
        deque.offerLast(3);
        deque.offerFirst(1);

        System.out.println(deque); // [1, 2, 3]

        System.out.println(deque.pollLast());  // 3
        System.out.println(deque.pollFirst()); // 1
        System.out.println(deque);             // [2]
    }
}
```

This shows true double-ended behavior.

---

## 17. Using ArrayDeque as a Stack

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
    public static void main(String[] args) {
        Deque<String> stack = new ArrayDeque<>();

        stack.push("A");
        stack.push("B");
        stack.push("C");

        System.out.println(stack.pop());  // C
        System.out.println(stack.pop());  // B
        System.out.println(stack.peek()); // A
    }
}
```

This is one of the most common modern stack patterns in Java.

---

## 18. Important Restriction: ArrayDeque Does Not Allow null

This is a very important practical rule.

```java
Deque<String> deque = new ArrayDeque<>();
deque.offer(null); // exception
```

### Why?

Because methods like:

- `poll()`
    
- `pollFirst()`
    
- `pollLast()`
    
- `peek()`
    
- `peekFirst()`
    
- `peekLast()`
    

use `null` to indicate failure or emptiness.

If `null` were allowed as a real stored element, Java would not be able to distinguish:

- an actual `null` element
    
- an empty-result signal
    

So `ArrayDeque` rejects `null` by design.

---

## 19. Time Complexity of ArrayDeque Operations

For typical front/rear operations, `ArrayDeque` is usually very efficient.

Common operations are typically:

- `offerFirst()` → **O(1)** amortized
    
- `offerLast()` → **O(1)** amortized
    
- `pollFirst()` → **O(1)**
    
- `pollLast()` → **O(1)**
    
- `peekFirst()` → **O(1)**
    
- `peekLast()` → **O(1)**
    

The word **amortized** means that although resizing can occasionally cost more, the average cost over many operations remains constant.

---

## 20. Typical Use Cases of Deque and ArrayDeque

### 20.1 Queue behavior

Used for standard FIFO processing.

Examples:

- BFS
    
- task processing
    
- request ordering
    

### 20.2 Stack behavior

Used for LIFO processing.

Examples:

- bracket matching
    
- expression evaluation
    
- iterative DFS
    

### 20.3 True deque behavior

Used when both ends matter.

Examples:

- sliding window problems
    
- monotonic queue patterns
    
- two-ended simulations
    
- palindrome-style two-side comparisons
    

---

## 21. Common Pitfalls

### 21.1 Thinking Deque is just another name for Queue

It is not.

A deque is more general and more powerful because both ends are active.

### 21.2 Thinking ArrayDeque is linked-list-based

It is not.

It is array-based and relies on circular-array logic.

### 21.3 Forgetting that ArrayDeque rejects null

This is a very common mistake.

### 21.4 Using Stack when Deque is the modern recommendation

In most modern Java code, `Deque` + `ArrayDeque` is the better choice.

### 21.5 Exposing the wrong abstraction

If you only need queue behavior, use:

```java
Queue<Integer> queue = new ArrayDeque<>();
```

If you need double-ended behavior, use:

```java
Deque<Integer> deque = new ArrayDeque<>();
```

This makes the intent of your code clearer.

---

## 22. Recommended Mental Model

When learning `Deque` and `ArrayDeque`, keep this model in mind:

- `Deque` is an interface
    
- it represents efficient operations on both ends
    
- it can behave like a queue
    
- it can behave like a stack
    
- `ArrayDeque` is a practical, high-performance implementation of that interface
    
- in modern Java, `ArrayDeque` is often the default choice for queue and stack behavior
    

---

## 23. Interview-Level Summary

If asked what `Deque` is, a strong answer would be:

> `Deque` is a double-ended queue interface in the Java Collections Framework. It extends `Queue` and supports insertion, removal, and inspection at both the front and rear. Because of this, it can be used to model both FIFO queues and LIFO stacks.

If asked what `ArrayDeque` is, a strong answer would be:

> `ArrayDeque` is a resizable array-based implementation of `Deque`. It is usually preferred for normal queue and stack use cases because it supports efficient operations at both ends, avoids the legacy issues of `Stack`, and is often more efficient than `LinkedList`.

---

## 24. Final Summary

### What is `Deque`?

A double-ended queue interface that supports operations at both ends.

### What can it simulate?

- a queue
    
- a stack
    
- a true double-ended structure
    

### What is `ArrayDeque`?

A resizable array-based implementation of `Deque`.

### Why is it important?

Because it is one of the best general-purpose choices in Java for queue and stack behavior.

### Why is it often preferred?

Because it is modern, efficient, flexible, and usually better suited than `Stack` or `LinkedList` for ordinary use.

### Most important practical caution

`ArrayDeque` does not allow `null` elements.