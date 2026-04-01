# Complexity in Java

## 1. What Complexity Means

In algorithms, **time complexity** describes how the number of operations grows as the input size grows.

**Space complexity** describes how much extra memory an algorithm uses as the input size grows.

Complexity is **not** the actual running time in seconds. It is a model for growth.

---

## 2. The Three Main Asymptotic Notations

### Big O — Upper Bound

**Big O** describes an upper bound.

It answers:

> How fast can this algorithm grow at most?

Example:

If an algorithm is `O(n^2)`, its running time does not grow faster than some constant multiple of `n^2` for large enough `n`.

Big O is the notation used most often in interviews and engineering.

---

### Big Omega — Lower Bound

**Big Ω** describes a lower bound.

It answers:

> How fast must this algorithm grow at least?

Example:

If finding the maximum in an unsorted array requires checking all elements, then the problem has at least `Ω(n)` work.

---

### Big Theta — Tight Bound

**Big Θ** describes a tight bound.

It answers:

> What is the real growth rate, both upper and lower?

If an algorithm is both `O(n)` and `Ω(n)`, then it is `Θ(n)`.

Example:

A single loop over an array is usually `Θ(n)`.

---

## 3. Why We Ignore Constants and Lower-Order Terms

When analyzing complexity, we focus on the dominant growth term.

Examples:

- `O(2n)` becomes `O(n)`
    
- `O(n + 100)` becomes `O(n)`
    
- `O(n^2 + n)` becomes `O(n^2)`
    

Because for large `n`, the fastest-growing term dominates everything else.

---

## 4. Common Complexity Classes

This is the most important practical section.

### `O(1)` — Constant Time

The number of operations does not depend on input size.

#### Java example

```java
int first = arr[0];
System.out.println(first);
```

Accessing an array element by index is `O(1)`.

#### More examples

- `stack.push(x)` → usually `O(1)`
    
- `stack.pop()` → usually `O(1)`
    
- `queue.peek()` → usually `O(1)`
    
- `HashMap.get(key)` → average case close to `O(1)`
    

---

### `O(log n)` — Logarithmic Time

The work grows slowly.

This usually happens when the problem size is cut in half each step.

#### Java example: Binary Search

```java
public static int binarySearch(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1;
}
```

Each step removes half of the remaining search space, so the complexity is `O(log n)`.

#### Another example

```java
for (int i = 1; i < n; i *= 2) {
    System.out.println(i);
}
```

Values of `i` are:

`1, 2, 4, 8, 16, ...`

The loop runs about `log n` times.

---

### `O(n)` — Linear Time

The amount of work grows proportionally with input size.

#### Java example

```java
public static int findMax(int[] arr) {
    int max = arr[0];
    for (int i = 1; i < arr.length; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}
```

Every element is checked once, so the complexity is `O(n)`.

#### Another example

```java
public static boolean contains(int[] arr, int target) {
    for (int num : arr) {
        if (num == target) {
            return true;
        }
    }
    return false;
}
```

Worst case: scan the whole array.

---

### `O(m)` and `O(log m)`

Sometimes the input is not called `n`.

You may see `m`, `k`, or multiple variables like `n` and `m`.

This matters when there are multiple independent input sizes.

#### Example: loop over a second array

```java
public static void printSecondArray(int[] a, int[] b) {
    for (int x : b) {
        System.out.println(x);
    }
}
```

If `b.length = m`, then this is `O(m)`.

#### Example: binary search in array of size `m`

```java
public static int binarySearchInSecondArray(int[] b, int target) {
    int left = 0;
    int right = b.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (b[mid] == target) {
            return mid;
        } else if (b[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1;
}
```

This is `O(log m)` if the second input size is `m`.

---

### `O(n + m)` — Two Separate Linear Passes

This means the algorithm processes two different inputs separately.

#### Java example

```java
public static void printTwoArrays(int[] a, int[] b) {
    for (int x : a) {
        System.out.println(x);
    }

    for (int y : b) {
        System.out.println(y);
    }
}
```

If `a.length = n` and `b.length = m`, the complexity is:

`O(n + m)`

This is **not** `O(nm)` because the loops are not nested.

---

### `O(n log n)` — Linear Times Logarithmic

This is one of the most important complexities in algorithms.

It often appears in efficient sorting and divide-and-conquer algorithms.

#### Example idea

- `log n` levels of splitting
    
- `n` total work per level
    
- total = `n log n`
    

#### Java example: Merge Sort

```java
public static void mergeSort(int[] arr, int left, int right) {
    if (left >= right) {
        return;
    }

    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}

private static void merge(int[] arr, int left, int mid, int right) {
    int[] temp = new int[right - left + 1];
    int i = left;
    int j = mid + 1;
    int k = 0;

    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) {
            temp[k++] = arr[i++];
        } else {
            temp[k++] = arr[j++];
        }
    }

    while (i <= mid) {
        temp[k++] = arr[i++];
    }

    while (j <= right) {
        temp[k++] = arr[j++];
    }

    for (int p = 0; p < temp.length; p++) {
        arr[left + p] = temp[p];
    }
}
```

Merge sort runs in `O(n log n)` time.

---

### `O(n^2)` — Quadratic Time

Usually caused by nested loops over the same input.

#### Java example

```java
public static void printAllPairs(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        for (int j = 0; j < arr.length; j++) {
            System.out.println(arr[i] + "," + arr[j]);
        }
    }
}
```

The outer loop runs `n` times, and for each outer iteration the inner loop also runs `n` times.

Total = `n * n = n^2`

#### Another classic example: Bubble Sort

```java
public static void bubbleSort(int[] arr) {
    for (int i = 0; i < arr.length - 1; i++) {
        for (int j = 0; j < arr.length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}
```

Bubble sort is `O(n^2)` in average and worst cases.

---

### `O(n^3)` — Cubic Time

Usually caused by three nested loops.

#### Java example

```java
public static void printTriples(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        for (int j = 0; j < arr.length; j++) {
            for (int k = 0; k < arr.length; k++) {
                System.out.println(arr[i] + "," + arr[j] + "," + arr[k]);
            }
        }
    }
}
```

This is `O(n^3)`.

---

### `O(2^n)` — Exponential Time

This often appears in brute-force recursion where each step branches into two choices.

#### Java example: naive Fibonacci

```java
public static int fib(int n) {
    if (n <= 1) {
        return n;
    }
    return fib(n - 1) + fib(n - 2);
}
```

This has exponential growth because the same subproblems are recomputed many times.

---

### `O(n!)` — Factorial Time

This is even worse than exponential time.

It usually appears in full permutation generation.

#### Java example idea

Generating all permutations of `n` elements requires `n!` outputs.

---

## 5. Comparing Common Growth Rates

From smaller growth to larger growth:

- `O(1)`
    
- `O(log n)`
    
- `O(n)`
    
- `O(n log n)`
    
- `O(n^2)`
    
- `O(n^3)`
    
- `O(2^n)`
    
- `O(n!)`
    

For interviews, you should immediately recognize that:

- `O(n log n)` is usually good
    
- `O(n^2)` may be acceptable for small inputs
    
- `O(2^n)` and `O(n!)` usually need pruning, memoization, DP, or a completely different idea
    

---

## 6. How to Analyze Time Complexity

### Case 1: Single loop

```java
for (int i = 0; i < n; i++) {
    // O(1)
}
```

Complexity: `O(n)`

---

### Case 2: Two consecutive loops

```java
for (int i = 0; i < n; i++) {
    System.out.println(i);
}

for (int j = 0; j < n; j++) {
    System.out.println(j);
}
```

Complexity: `O(n + n) = O(n)`

Consecutive loops are added, not multiplied.

---

### Case 3: Nested loops

```java
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        System.out.println(i + "," + j);
    }
}
```

Complexity: `O(n^2)`

Nested loops usually multiply.

---

### Case 4: Different input sizes

```java
for (int i = 0; i < n; i++) {
    System.out.println(a[i]);
}

for (int j = 0; j < m; j++) {
    System.out.println(b[j]);
}
```

Complexity: `O(n + m)`

---

### Case 5: Halving or doubling each step

```java
for (int i = 1; i < n; i *= 2) {
    System.out.println(i);
}
```

Complexity: `O(log n)`

---

### Case 6: Outer loop with logarithmic inner work

```java
for (int i = 0; i < n; i++) {
    for (int j = 1; j < n; j *= 2) {
        System.out.println(i + "," + j);
    }
}
```

Outer loop: `n`

Inner loop: `log n`

Total: `O(n log n)`

---

## 7. Space Complexity

Space complexity measures **extra memory usage**.

### `O(1)` space

```java
public static int sum(int[] arr) {
    int total = 0;
    for (int num : arr) {
        total += num;
    }
    return total;
}
```

Only a few variables are used, so extra space is `O(1)`.

---

### `O(n)` space

```java
public static int[] reverseCopy(int[] arr) {
    int[] result = new int[arr.length];
    for (int i = 0; i < arr.length; i++) {
        result[i] = arr[arr.length - 1 - i];
    }
    return result;
}
```

The new array has size `n`, so extra space is `O(n)`.

---

### Recursive space

```java
public static void countdown(int n) {
    if (n == 0) {
        return;
    }
    countdown(n - 1);
}
```

Time complexity is `O(n)`.

Space complexity is also `O(n)` because of the recursion call stack.

---

## 8. Best Case, Average Case, Worst Case

When analyzing complexity, always ask:

- Best case?
    
- Average case?
    
- Worst case?
    

#### Example: linear search

```java
public static int linearSearch(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            return i;
        }
    }
    return -1;
}
```

- Best case: `O(1)` — target is at index 0
    
- Worst case: `O(n)` — target is last or not present
    
- Average case: `O(n)`
    

In interviews, worst-case complexity is usually the default unless stated otherwise.

---

## 9. Common Java Data Structure Complexities

### Array

- Access by index: `O(1)`
    
- Search: `O(n)`
    

### ArrayList

- `get(index)`: `O(1)`
    
- `add(element)`: amortized `O(1)`
    
- `add(index, element)`: `O(n)`
    
- `remove(index)`: `O(n)`
    

### LinkedList

- Add/remove at head or tail: `O(1)` if position is known
    
- Access by index: `O(n)`
    

### HashMap

- `get`, `put`, `remove`: average `O(1)`
    
- worst case can degrade, though Java uses treeification in some collision-heavy cases
    

### HashSet

- `add`, `contains`, `remove`: average `O(1)`
    

### TreeMap / TreeSet

- `get`, `put`, `remove`: `O(log n)`
    

### PriorityQueue

- `offer`: `O(log n)`
    
- `poll`: `O(log n)`
    
- `peek`: `O(1)`
    

### ArrayDeque

- add/remove from both ends: usually `O(1)` amortized
    

---

## 10. Common Mistakes

### Mistake 1: Mixing up consecutive loops and nested loops

```java
for (int i = 0; i < n; i++) {}
for (int j = 0; j < n; j++) {}
```

This is `O(n)`, not `O(n^2)`.

---

### Mistake 2: Forgetting that constants do not matter asymptotically

```java
for (int i = 0; i < 2 * n; i++) {}
```

This is still `O(n)`.

---

### Mistake 3: Ignoring extra memory

Using a `HashMap`, `HashSet`, `Stack`, `Queue`, or helper array often changes space complexity.

---

### Mistake 4: Ignoring recursion stack space

A recursive solution may have good-looking code but still use `O(n)` stack space.

---

### Mistake 5: Writing only `O(...)` when a tighter statement exists

For example, a simple single pass is not just `O(n)`, it is also `Θ(n)`.

---

## 11. Quick Recognition Patterns

### Usually `O(1)`

- direct index access
    
- hash lookup average case
    
- push/pop/peek on stack-like structures
    

### Usually `O(log n)`

- binary search
    
- balanced BST operations
    
- heap insertion/removal
    

### Usually `O(n)`

- one pass over an array/string/list
    
- linear search
    
- copying an array
    

### Usually `O(n log n)`

- merge sort
    
- heap sort
    
- efficient divide-and-conquer with `log n` levels and `n` work per level
    

### Usually `O(n^2)`

- double nested loop over same input
    
- bubble sort / selection sort / insertion sort worst case
    

### Usually `O(2^n)` or worse

- brute-force subset recursion
    
- naive recursive branching without memoization
    

---

## 12. Final Summary

### Time Complexity

Describes how running cost grows with input size.

### Space Complexity

Describes how extra memory usage grows with input size.

### Three Main Notations

- **Big O**: upper bound
    
- **Big Ω**: lower bound
    
- **Big Θ**: tight bound
    

### Most Important Growth Order

`O(1) < O(log n) < O(n) < O(n log n) < O(n^2) < O(n^3) < O(2^n) < O(n!)`

### Most Important Practical Rule

When analyzing code:

1. count loops
    
2. check whether loops are consecutive or nested
    
3. check whether the problem size is shrinking
    
4. check recursion depth
    
5. check extra data structures for space usage
    

---

## 13. Interview Tip

When you explain complexity in interviews, use this structure:

1. **Time complexity:** explain how many times the main work runs
    
2. **Space complexity:** explain whether extra memory is used
    
3. **Why:** mention the exact loop / recursion / data structure reason
    

Example:

> The time complexity is `O(n)` because we scan the array once. The space complexity is `O(1)` because we only use a few variables and do not allocate any extra data structure.