## Java Thread Basics

### Core Foundations for Early Multithreading Study

## 1. What a Thread Is

A thread is the smallest unit of execution inside a process.  
A Java program does not run as one indivisible block of code. Execution happens through threads.

A process is the running program as a whole.  
A thread is one execution path inside that process.

In Java:

- the JVM runs inside an OS process
    
- the `main()` method runs on the main thread
    
- JVM may create GC threads, compiler threads, and service threads
    

So even a simple Java program already involves threads.

### Process vs Thread

#### Process

- heavier unit
    
- owns resources and memory space
    
- isolated from other processes
    

#### Thread

- lighter unit
    
- lives inside a process
    
- shares memory with sibling threads
    
- easier communication, higher concurrency risk
    

### Interview Answer

A process is an independent running program with its own resources, while a thread is a lightweight execution unit inside a process. Threads in the same process share memory and resources, which improves communication efficiency but introduces thread-safety problems.

---

## 2. Why Threads Exist

Threads allow a program to perform multiple tasks in overlapping time periods.

Typical reasons:

- improve responsiveness
    
- handle many requests
    
- use multi-core CPUs
    
- run background jobs
    
- perform asynchronous work
    
- separate long tasks from main flow
    

Examples:

- web server handling multiple clients
    
- GUI app loading data without freezing UI
    
- backend sending emails in background
    
- batch processing tasks concurrently
    

### Important Engineering Mindset

More threads do not automatically mean more speed.

Too many threads may cause:

- context switching overhead
    
- memory pressure
    
- lock contention
    
- scheduler overhead
    
- worse throughput
    

---

## 3. Java Threads and OS Threads

In standard modern JVM implementations, Java threads usually map to real operating system threads.

```java
Thread t = new Thread(...);
t.start();
```

The JVM cooperates with the OS scheduler to create and run a real thread.

### Why This Matters

Thread creation is not free.  
It costs:

- memory (thread stack)
    
- scheduling cost
    
- context switch cost
    
- management overhead
    

This is one reason production systems prefer thread pools instead of creating unlimited threads.

---

## 4. Creating Threads in Java

## 4.1 Extending Thread

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```

## 4.2 Implementing Runnable

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}

Thread t = new Thread(new MyTask());
```

### Why Runnable Is Preferred

It separates:

- task logic
    
- thread mechanism
    

This leads to better design and avoids single inheritance limitations.

## 4.3 Callable

Used when you need:

- return value
    
- checked exception support
    

```java
Callable<Integer> task = () -> 42;
```

---

## 5. start() vs run()

One of the highest-frequency interview topics.

Many beginners think both methods "run a thread".  
They do not.

## 5.1 run()

`run()` is only a normal method call.  
It does NOT create a new thread.

```java
Thread t = new Thread(() -> {
    System.out.println(Thread.currentThread().getName());
});

t.run();
```

Output:

```text
main
```

Why?  
Because the current thread (`main`) directly executed the method body.

## 5.2 start()

`start()` creates/starts a new thread.  
That new thread then executes `run()`.

```java
t.start();
```

Possible output:

```text
Thread-0
```

## 5.3 Correct Mental Model

### run()

- ordinary method call
    
- no new thread
    
- current thread executes logic
    

### start()

- starts a new execution path
    
- JVM schedules new thread
    
- new thread invokes `run()`
    

## 5.4 Example Comparison

```java
class Demo extends Thread {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Demo t = new Demo();
        t.run();

        t = new Demo();
        t.start();
    }
}
```

Possible output:

```text
main
Thread-0
```

## 5.5 Common Mistakes

### Mistake 1

```java
new Thread(task).run();
```

No concurrency happened.

### Mistake 2

```java
t.start();
t.start();
```

Throws:

```text
IllegalThreadStateException
```

A Thread object can only be started once.

### Interview Answer

`run()` is just a normal method call. `start()` actually starts a new thread, and the new thread executes `run()`.

---

## 6. Thread Lifecycle

Main states:

- NEW
    
- RUNNABLE
    
- BLOCKED
    
- WAITING
    
- TIMED_WAITING
    
- TERMINATED
    

## NEW

Created but not started.

## RUNNABLE

Ready to run or currently running.  
In Java, this usually includes both ready + running states.

## BLOCKED

Waiting to acquire a monitor lock (often entering synchronized code).

## WAITING

Waiting indefinitely for another action.  
Examples:

- `wait()`
    
- `join()`
    

## TIMED_WAITING

Waiting with timeout.  
Examples:

- `sleep(1000)`
    
- `join(1000)`
    

## TERMINATED

Execution finished.

### Interview Distinction

- `BLOCKED` = waiting for lock
    
- `WAITING` = waiting for signal/event
    

---

## 7. Common Thread Methods

## start()

Starts a new thread.

## run()

Task body.

## sleep(ms)

Pauses current thread.

Important:  
`sleep()` does NOT release locks.

This is a common interview trap.

## join()

Wait for another thread to finish.

```java
t.join();
```

## interrupt()

Sends interruption signal.  
Does not forcibly kill the thread.

Blocked operations like `sleep()` may throw `InterruptedException`.

## currentThread()

Returns current executing thread.

## isAlive()

Checks whether a thread has started and not yet terminated.

---

## 8. Daemon Thread

A daemon thread is a background support thread.

When all user threads finish, JVM may exit even if daemon threads are still alive.

Typical examples:

- GC related threads
    
- service helper threads
    

```java
Thread t = new Thread(() -> {
    while (true) {}
});

t.setDaemon(true);
t.start();
```

Important:  
Call `setDaemon(true)` before `start()`.

---

## 9. Shared Data Problem

Threads in the same process share memory.  
That creates concurrency risk.

Example:

```java
int count = 0;
count++;
```

Many beginners think this is one simple operation.  
It is not.

Roughly:

1. read count
    
2. add 1
    
3. write result back
    

Two threads can interleave these steps.

## Race Condition Example

Initial value = 0

- Thread A reads 0
    
- Thread B reads 0
    
- Thread A writes 1
    
- Thread B writes 1
    

Final result = 1 (wrong)  
Expected = 2

### Interview Answer

`count++` is not thread-safe because it is a read-modify-write sequence, not one atomic action.

---

## 10. Concurrency vs Parallelism

## Concurrency

Multiple tasks make progress during same overall period.  
May be interleaved.  
Single-core CPUs can still support concurrency.

## Parallelism

Multiple tasks truly run at same moment.  
Usually requires multiple cores.

### Summary

Concurrency = overlapping progress  
Parallelism = simultaneous execution

---

## 11. Critical Section

A critical section is code that accesses shared mutable state and must be protected.

Example:

```java
count++;
```

Core multithreading question:  
How do we protect critical sections correctly?

---

## 12. synchronized Basics

`synchronized` is Java's built-in mutual exclusion mechanism.

At beginner level, understand this first:

Threads using the same lock cannot execute the protected section at the same time.

It also provides memory visibility guarantees, but deep JMM details come later.

---

## 13. synchronized on Instance Method

```java
public synchronized void increment() {
    count++;
}
```

Equivalent to:

```java
public void increment() {
    synchronized (this) {
        count++;
    }
}
```

### Meaning

To enter this method, a thread must acquire the current object's monitor lock.

Lock = `this`

### Same Object Example

If two threads call the method on the same object, they must take turns.

### Very Important Note

This does NOT lock the whole class.  
It locks only the current instance.

```java
Counter c1 = new Counter();
Counter c2 = new Counter();
```

These are different locks.  
So:

- `c1.increment()`
    
- `c2.increment()`
    

can run concurrently.

### Interview Answer

An instance synchronized method locks the current object instance (`this`).

---

## 14. synchronized on Static Method

```java
public static synchronized void test() {}
```

Equivalent to:

```java
synchronized (ClassName.class) {}
```

### Meaning

Static methods do not use `this`.  
The lock is the class object.

All callers compete for the same class-level lock.

### Interview Answer

A static synchronized method locks the corresponding `Class` object, such as `MyClass.class`.

---

## 15. Method Synchronization vs Block Synchronization

## Method Level

```java
public synchronized void process() {
    // whole method locked
}
```

Easy to write, readable.

## Block Level

```java
public void process() {
    // non-critical code

    synchronized (this) {
        // critical section
    }

    // non-critical code
}
```

More flexible.  
Smaller lock scope.  
Often better for performance.

### Engineering Tradeoff

If only 5 lines need protection, locking 50 lines is unnecessary.  
Longer lock holding time can reduce throughput.

---

## 19. Java Memory Model (JMM) Basics

After basic thread creation and locking, the next important idea is understanding why threads may see stale data.

The Java Memory Model (JMM) is not a physical memory layout. It is a specification that defines how shared variables behave between threads.

At a simplified level, you can think in terms of:

- **main memory**: shared data storage
    
- **working memory**: thread-local cached view of shared data
    

This simplified model helps explain why one thread may update a value while another thread still sees an older value.

### The Three Core Concurrency Problems

#### 1. Visibility

One thread changes a shared variable, but another thread does not immediately see the updated value.

#### 2. Atomicity

An operation that appears simple may actually consist of multiple steps.

Example:

```java
count++;
```

This is roughly:

1. read
    
2. add
    
3. write
    

So it is not atomic.

#### 3. Ordering

The actual execution order may differ from source-code order because of compiler, JVM, or CPU optimizations.

### Why JMM Matters

Without this mental model, multithreading errors look random. With it, you can reason about why `volatile`, `synchronized`, locks, and atomic classes exist.

---

## 20. volatile

`volatile` is one of the most important Java concurrency keywords.

It mainly helps with:

- visibility
    
- part of ordering guarantees
    

It does **not** provide full mutual exclusion.

### Classic Example: Stop Flag

#### Wrong Version

```java
class Demo {
    static boolean running = true;

    public static void main(String[] args) throws Exception {
        Thread t = new Thread(() -> {
            while (running) {
            }
            System.out.println("Stopped");
        });

        t.start();
        Thread.sleep(1000);
        running = false;
    }
}
```

This may never stop, because the worker thread may keep reading an outdated cached value.

#### Correct Version

```java
class Demo {
    static volatile boolean running = true;

    public static void main(String[] args) throws Exception {
        Thread t = new Thread(() -> {
            while (running) {
            }
            System.out.println("Stopped");
        });

        t.start();
        Thread.sleep(1000);
        running = false;
    }
}
```

Now the latest value becomes visible across threads.

### What volatile Guarantees

When a thread writes a `volatile` variable:

- the new value is flushed so other threads can observe it
    

When a thread reads a `volatile` variable:

- it reads the latest visible value instead of reusing an older cached copy
    

### What volatile Does Not Guarantee

It does not make compound actions atomic.

Example:

```java
class Counter {
    volatile int count = 0;

    public void add() {
        count++;
    }
}
```

This is still unsafe, because `count++` is read-modify-write.

### When volatile Is Appropriate

- state flags
    
- shutdown flags
    
- simple one-writer-many-reader configuration values
    
- simple status publication
    

### When volatile Is Not Enough

- counters using `count++`
    
- check-then-act logic
    
- multi-step business operations
    
- multi-variable consistency
    

### Interview Summary

`volatile` solves visibility problems, but it does not solve general race conditions involving compound operations.

---

## 21. synchronized vs volatile

This is a high-frequency interview comparison.

### volatile

Provides:

- visibility
    
- partial ordering guarantees
    

Does not provide:

- mutual exclusion
    
- full atomicity for compound operations
    

### synchronized

Provides:

- mutual exclusion
    
- visibility
    
- a protected critical section for compound operations
    

### Best Memory Hook

`volatile` helps with **seeing the latest value**.  
`synchronized` helps with **preventing concurrent interference while modifying shared state**.

---

## 22. ReentrantLock

`ReentrantLock` is one of the most important explicit lock implementations in Java.

You can think of it as a more flexible and more feature-rich locking mechanism than `synchronized`.

### Why ReentrantLock Exists

`synchronized` is simple and useful, but it has limitations:

- cannot try once and give up immediately
    
- cannot easily wait with timeout for lock acquisition
    
- cannot easily interrupt lock waiting in the same flexible style
    
- less flexible condition waiting model
    

`ReentrantLock` solves these problems.

### Basic Usage Pattern

```java
import java.util.concurrent.locks.ReentrantLock;

ReentrantLock lock = new ReentrantLock();

lock.lock();
try {
    // critical section
} finally {
    lock.unlock();
}
```

### Why unlock() Must Be in finally

If an exception happens before unlock, the lock may never be released.  
That can permanently block other threads.

### What Reentrant Means

The same thread can acquire the same lock multiple times without deadlocking itself.

```java
class Demo {
    private final ReentrantLock lock = new ReentrantLock();

    public void outer() {
        lock.lock();
        try {
            inner();
        } finally {
            lock.unlock();
        }
    }

    public void inner() {
        lock.lock();
        try {
            System.out.println("inner");
        } finally {
            lock.unlock();
        }
    }
}
```

This works because the lock is reentrant.

### Important Features

#### tryLock()

Try once. If lock is unavailable, do not block forever.

```java
if (lock.tryLock()) {
    try {
        // got lock
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("failed to get lock");
}
```

#### tryLock(timeout)

Wait up to a limited amount of time.

```java
if (lock.tryLock(3, TimeUnit.SECONDS)) {
    try {
        // got lock
    } finally {
        lock.unlock();
    }
}
```

#### lockInterruptibly()

Allow interruption while waiting for the lock.

### Fair vs Unfair Lock

```java
new ReentrantLock();      // unfair by default
new ReentrantLock(true);  // fair lock
```

- **unfair lock**: higher throughput, allows barging
    
- **fair lock**: closer to first-come-first-served, usually lower throughput
    

### When ReentrantLock Is Better Than synchronized

- need `tryLock`
    
- need timeout lock acquisition
    
- need interruptible lock waiting
    
- need richer condition handling
    
- need more explicit control
    

### Interview Summary

`synchronized` is simpler and built-in.  
`ReentrantLock` is more flexible and better for advanced locking scenarios.

---

## 23. Condition

A `Condition` works with `ReentrantLock` and plays a role similar to `wait()/notify()`, but in a cleaner and more flexible way.

### Basic Creation

```java
Condition condition = lock.newCondition();
```

### Core Methods

- `await()`
    
- `signal()`
    
- `signalAll()`
    

### Example

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

class Buffer {
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition notEmpty = lock.newCondition();
    private int data = 0;

    public void produce() {
        lock.lock();
        try {
            data = 1;
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public void consume() throws InterruptedException {
        lock.lock();
        try {
            while (data == 0) {
                notEmpty.await();
            }
            data = 0;
        } finally {
            lock.unlock();
        }
    }
}
```

### Why while Instead of if

Because of:

- spurious wakeups
    
- another thread consuming the condition first
    
- condition changes before the current thread resumes meaningful work
    

So the condition must always be rechecked.

### Why Condition Is Better Than Basic wait/notify in Some Designs

You can create multiple condition queues for one lock, for example:

- `notEmpty`
    
- `notFull`
    

This makes producer-consumer designs clearer than forcing all waiters onto one monitor queue.

---

## 24. Atomic Classes and CAS

Java provides atomic classes in `java.util.concurrent.atomic`.

Common examples:

- `AtomicInteger`
    
- `AtomicLong`
    
- `AtomicBoolean`
    
- `AtomicReference`
    

These support lock-free thread-safe updates for certain single-variable operations.

### Basic Example

```java
import java.util.concurrent.atomic.AtomicInteger;

AtomicInteger count = new AtomicInteger(0);
count.incrementAndGet();
System.out.println(count.get());
```

### Why AtomicInteger Is Thread-Safe

Because it does not use a plain `count++`.  
It uses CAS.

### CAS = Compare And Set / Compare And Swap

CAS logic is conceptually:

1. read current value
    
2. check whether current value still equals expected old value
    
3. if yes, update to new value
    
4. if no, retry
    

### Conceptual Example

```java
while (true) {
    int oldValue = count.get();
    int newValue = oldValue + 1;

    if (count.compareAndSet(oldValue, newValue)) {
        break;
    }
}
```

### Why CAS Is Different From Locking

- locks may block threads
    
- CAS usually retries instead of blocking
    

### When Atomic Classes Are Good

- counters
    
- simple state transitions
    
- single-variable atomic updates
    
- lock-free lightweight coordination
    

### Limitations of Atomic Classes

They are not a universal replacement for locks.

Example:

```java
if (balance >= 100) {
    balance -= 100;
}
```

This is a multi-step business action, not just one simple atomic variable update.

### ABA Problem

CAS can miss intermediate changes if a value goes:

- A -> B -> A
    

A thread may think nothing changed because the final observed value is still A.

For this reason, Java also provides stamped/versioned references such as `AtomicStampedReference`.

### LongAdder

For high-contention counters, `LongAdder` is often better than `AtomicInteger` because it reduces contention by spreading updates across multiple cells and summing later.

### Interview Summary

Atomic classes are excellent for simple lock-free updates, but they do not solve all concurrency problems.

---

## 25. Thread Pool Basics

In real applications, tasks are usually not handled by repeatedly creating raw threads manually.  
Instead, thread pools are used.

### Why Thread Pools Exist

Creating and destroying threads repeatedly is expensive.  
Too many threads also create:

- memory overhead
    
- scheduling cost
    
- context switching cost
    

A thread pool reuses worker threads.

### Core Interface

The most important abstraction is:

- `ExecutorService`
    

### Basic Example

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

ExecutorService pool = Executors.newFixedThreadPool(3);

pool.execute(() -> {
    System.out.println(Thread.currentThread().getName());
});

pool.shutdown();
```

### execute() vs submit()

#### execute()

- no return value
    

#### submit()

- returns `Future`
    
- used when task result matters
    

```java
Future<Integer> future = pool.submit(() -> 42);
System.out.println(future.get());
```

### Common Pool Types

- `newFixedThreadPool`
    
- `newCachedThreadPool`
    
- `newSingleThreadExecutor`
    
- `newScheduledThreadPool`
    

### Production Note

In real projects, `ThreadPoolExecutor` is often preferred over directly relying on `Executors` factory methods, because it makes capacity and rejection behavior explicit.

---

## 26. ThreadPoolExecutor Core Parameters

`ThreadPoolExecutor` is the main JDK thread pool implementation.

```java
new ThreadPoolExecutor(
    corePoolSize,
    maximumPoolSize,
    keepAliveTime,
    unit,
    workQueue,
    threadFactory,
    handler
)
```

### Important Parameters

#### corePoolSize

Base worker count kept available.

#### maximumPoolSize

Upper bound of worker count.

#### keepAliveTime

How long extra non-core threads may stay idle before removal.

#### workQueue

Task queue used when all core workers are busy.

#### threadFactory

Controls how worker threads are created and named.

#### handler

Rejected execution policy when both worker capacity and queue capacity are exhausted.

### Execution Flow

1. if core threads are not full -> create worker
    
2. else put task into queue
    
3. if queue is full and max not reached -> create extra worker
    
4. else reject task
    

This execution order is a high-frequency interview topic.

---

## 27. ThreadPoolTaskExecutor (Spring)

In Spring projects, a very common thread pool wrapper is:

- `ThreadPoolTaskExecutor`
    

It is essentially Spring’s higher-level wrapper around JDK thread-pool infrastructure.

### Why It Is Popular in Spring Projects

- bean lifecycle management
    
- easier configuration
    
- thread naming support
    
- integration with `@Async`
    
- better Spring ecosystem fit
    

### Typical Example

```java
@Bean("taskExecutor")
public ThreadPoolTaskExecutor taskExecutor() {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(5);
    executor.setMaxPoolSize(10);
    executor.setQueueCapacity(100);
    executor.setKeepAliveSeconds(60);
    executor.setThreadNamePrefix("async-");
    executor.initialize();
    return executor;
}
```

### Main Understanding

`ThreadPoolExecutor` is the low-level JDK implementation.  
`ThreadPoolTaskExecutor` is the Spring-style project wrapper around thread-pool execution management.

---

## 28. Coordination Utilities

Three classic coordination tools are:

- `CountDownLatch`
    
- `Semaphore`
    
- `CyclicBarrier`
    

They solve different problems.

---

## 29. CountDownLatch

A `CountDownLatch` lets one or more threads wait until a set of operations finish.

### Core Methods

- `countDown()`
    
- `await()`
    

### Example

```java
import java.util.concurrent.CountDownLatch;

CountDownLatch latch = new CountDownLatch(3);

Runnable worker = () -> {
    try {
        Thread.sleep(1000);
    } catch (Exception ignored) {
    }
    latch.countDown();
};

new Thread(worker).start();
new Thread(worker).start();
new Thread(worker).start();

latch.await();
System.out.println("All finished");
```

### Main Use Case

One thread waits for multiple subtasks to complete.

### Important Limitation

A `CountDownLatch` is one-shot. Once it reaches zero, it cannot be reset.

---

## 30. Semaphore

A `Semaphore` controls how many threads may access a limited resource at the same time.

### Core Methods

- `acquire()`
    
- `release()`
    

### Example

```java
import java.util.concurrent.Semaphore;

Semaphore semaphore = new Semaphore(3);

for (int i = 0; i < 6; i++) {
    new Thread(() -> {
        try {
            semaphore.acquire();
            try {
                Thread.sleep(1000);
            } finally {
                semaphore.release();
            }
        } catch (Exception ignored) {
        }
    }).start();
}
```

### Main Use Cases

- limiting concurrent requests to an external system
    
- restricting access to scarce resources
    
- implementing bounded concurrency
    

### Important Distinction

A semaphore is not the same thing as a mutual-exclusion lock, although `Semaphore(1)` can act similarly.

---

## 31. CyclicBarrier

A `CyclicBarrier` lets multiple threads wait until all participants reach the same barrier point, and then they continue together.

### Example

```java
import java.util.concurrent.CyclicBarrier;

CyclicBarrier barrier = new CyclicBarrier(3, () -> {
    System.out.println("All ready, go!");
});

Runnable runner = () -> {
    try {
        Thread.sleep((long) (Math.random() * 1000));
        barrier.await();
        System.out.println(Thread.currentThread().getName() + " running");
    } catch (Exception ignored) {
    }
};

new Thread(runner, "A").start();
new Thread(runner, "B").start();
new Thread(runner, "C").start();
```

### Main Difference from CountDownLatch

- `CountDownLatch`: one thread waits for others to finish
    
- `CyclicBarrier`: multiple threads wait for each other and continue together
    

### Why It Is Called Cyclic

It can be reused across cycles or phases.

---

## 32. CompletableFuture

`CompletableFuture` is one of the most important modern Java async programming tools.

It extends the idea of `Future` by supporting:

- asynchronous execution
    
- chaining
    
- combining tasks
    
- completion callbacks
    
- exception handling
    

### Basic Async Task Without Return Value

```java
import java.util.concurrent.CompletableFuture;

CompletableFuture.runAsync(() -> {
    System.out.println("Running in " + Thread.currentThread().getName());
});
```

### Async Task With Return Value

```java
CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 100);

System.out.println(future.join());
```

### runAsync vs supplyAsync

#### runAsync

- no return value
    

#### supplyAsync

- returns a value
    

### Chaining

#### thenApply

Transform previous result and return a new one.

```java
CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 10)
        .thenApply(x -> x * 2);
```

#### thenAccept

Consume result without returning a new value.

```java
CompletableFuture.supplyAsync(() -> "Hello")
    .thenAccept(System.out::println);
```

#### thenRun

Ignore previous result and just continue with another action.

### Combining Tasks

#### allOf

Wait for all tasks.

```java
CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> "User");
CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> "Order");
CompletableFuture<String> f3 = CompletableFuture.supplyAsync(() -> "Product");

CompletableFuture.allOf(f1, f2, f3).join();
```

#### anyOf

Continue when any one task completes.

#### thenCombine

Combine two independent results.

```java
CompletableFuture<Integer> a = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<Integer> b = CompletableFuture.supplyAsync(() -> 20);

CompletableFuture<Integer> result = a.thenCombine(b, Integer::sum);
```

### Exception Handling

#### exceptionally

Provide fallback value on failure.

```java
CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 1 / 0)
        .exceptionally(ex -> -1);
```

#### handle

Process both success and failure cases.

### join() vs get()

#### get()

- checked exceptions
    
- requires try/catch or throws
    

#### join()

- throws unchecked exception wrapper
    
- often more convenient in application code
    

### Default Executor

If no executor is provided, `CompletableFuture` commonly uses `ForkJoinPool.commonPool()`.

In production systems, a dedicated business executor is often better.

---

## 33. Concurrent Collections

Ordinary collections like `HashMap` and `ArrayList` are not generally safe for concurrent mutation.  
Java therefore provides concurrent collections.

The most important early ones are:

- `ConcurrentHashMap`
    
- `CopyOnWriteArrayList`
    
- `BlockingQueue`
    
- `ConcurrentLinkedQueue`
    

---

## 34. ConcurrentHashMap

A thread-safe high-concurrency map.

### Basic Example

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

Map<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);
map.putIfAbsent("B", 2);
System.out.println(map.get("A"));
```

### Why It Matters

It supports safe concurrent access without using one giant synchronized lock over the whole structure.

### Useful Atomic-Style Methods

- `putIfAbsent()`
    
- `computeIfAbsent()`
    
- `replace()`
    

These are much safer than writing broken check-then-put logic manually.

### Example

```java
map.computeIfAbsent("user:1", key -> loadUser());
```

This is very common in cache-style logic.

---

## 35. CopyOnWriteArrayList

A thread-safe list designed for read-heavy, write-light scenarios.

### Core Idea

When writing:

- copy the internal array
    
- modify the new copy
    
- replace the reference
    

Reads are very stable because they usually work against an immutable snapshot.

### Example

```java
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

List<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");

for (String s : list) {
    System.out.println(s);
}
```

### Best Use Cases

- listener lists
    
- subscriber lists
    
- configuration snapshots
    
- white/allow lists that change rarely
    

### Bad Use Case

Frequent writes, because copying the full array every time is expensive.

---

## 36. BlockingQueue

A thread-safe queue that can block:

- producers when full
    
- consumers when empty
    

This is a key structure for producer-consumer systems and thread pools.

### Common Implementations

- `ArrayBlockingQueue`
    
- `LinkedBlockingQueue`
    
- `PriorityBlockingQueue`
    

### Example

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(2);
queue.put(1);
queue.put(2);

Integer x = queue.take();
```

### Important Methods

- `put()`
    
- `take()`
    
- `offer()`
    
- `poll()`
    

### Main Use Cases

- producer-consumer models
    
- asynchronous job buffering
    
- internal handoff pipelines
    
- thread-pool work queues
    

---

## 37. ConcurrentLinkedQueue

A non-blocking, lock-free concurrent queue.

### Example

```java
import java.util.Queue;
import java.util.concurrent.ConcurrentLinkedQueue;

Queue<Integer> queue = new ConcurrentLinkedQueue<>();
queue.offer(1);
queue.offer(2);
System.out.println(queue.poll());
```

### Main Difference from BlockingQueue

`ConcurrentLinkedQueue` does not block.  
If empty, `poll()` simply returns `null`.

It is useful when you want high-throughput non-blocking queue behavior instead of wait-based producer-consumer coordination.

---

## 38. Collection Selection Guidelines

### Use ConcurrentHashMap when

- multiple threads share a map
    
- concurrent reads/writes happen
    
- cache-like access is needed
    

### Use CopyOnWriteArrayList when

- reads are much more frequent than writes
    
- iteration stability matters
    

### Use BlockingQueue when

- you need producer-consumer coordination
    
- tasks should wait when no work is available
    
- capacity bounds matter
    

### Use ConcurrentLinkedQueue when

- you need a lightweight non-blocking queue
    
- tasks do not need queue-based waiting semantics
    

---

## 39. Final Expanded Summary

At this stage, the thread and concurrency foundation includes:

### Core Thread Basics

- what a thread is
    
- why threads exist
    
- `start()` vs `run()`
    
- thread lifecycle
    
- common thread methods
    
- daemon threads
    

### Shared State and Safety

- race conditions
    
- critical sections
    
- `synchronized`
    
- instance lock vs class lock
    
- method synchronization vs block synchronization
    
- JMM basics
    
- visibility / atomicity / ordering
    
- `volatile`
    
- `synchronized` vs `volatile`
    

### Explicit Locking

- `ReentrantLock`
    
- fair vs unfair lock
    
- `tryLock`
    
- interruptible lock acquisition
    
- `Condition`
    

### Lock-Free and Atomic Tools

- `AtomicInteger`
    
- CAS
    
- ABA idea
    
- `LongAdder`
    

### Execution and Task Management

- thread pools
    
- `ExecutorService`
    
- `ThreadPoolExecutor`
    
- `ThreadPoolTaskExecutor`
    

### Coordination Utilities

- `CountDownLatch`
    
- `Semaphore`
    
- `CyclicBarrier`
    

### Modern Async Programming

- `CompletableFuture`
    
- chaining
    
- combining
    
- exception handling
    

### Concurrent Collections

- `ConcurrentHashMap`
    
- `CopyOnWriteArrayList`
    
- `BlockingQueue`
    
- `ConcurrentLinkedQueue`
    

These topics form the practical foundation before moving into deeper internals such as:

- read-write locks
    
- AQS
    
- synchronized internals
    
- advanced concurrent design patterns
    
- JVM-level concurrency tuning