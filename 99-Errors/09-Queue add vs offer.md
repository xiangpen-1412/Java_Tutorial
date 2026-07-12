# Java Queue: add() vs offer()

## Core mistake

`add()` and `offer()` can both insert an element into a `Queue`, but they behave differently when insertion fails.

| Method | Success | Failure |
|---|---|---|
| `add(x)` | returns `true` | throws exception |
| `offer(x)` | returns `true` | returns `false` |

## Memory rule

```text
add   = force add; fail -> exception
offer = try add;  fail -> false
```

For LeetCode / algorithm problems, prefer:

```java
queue.offer(x);
queue.poll();
queue.peek();
```

## Why offer() is usually safer

Most algorithm code uses queues like this:

```java
Queue<Integer> queue = new ArrayDeque<>();

queue.offer(1);
queue.offer(2);

int first = queue.poll();
```

`offer()` matches the queue idea better: try to insert into the queue.

For `ArrayDeque` and `LinkedList`, insertion usually does not fail because they are not fixed-size queues, so `add()` and `offer()` often look the same in practice.

But for bounded queues, the difference matters:

```java
Queue<Integer> queue = new ArrayBlockingQueue<>(2);

queue.add(1);
queue.add(2);
queue.add(3); // throws exception
```

With `offer()`:

```java
Queue<Integer> queue = new ArrayBlockingQueue<>(2);

queue.offer(1);
queue.offer(2);
boolean ok = queue.offer(3); // false
```

## Related safe methods

| Operation | Exception version | Safe-return version |
|---|---|---|
| insert | `add(x)` | `offer(x)` |
| remove head | `remove()` | `poll()` |
| view head | `element()` | `peek()` |

## Final habit

In normal queue code, use this set first:

```java
offer()
poll()
peek()
```

