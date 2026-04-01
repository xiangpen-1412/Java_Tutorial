# Arrays

## 0. Commonly Used `Arrays` Methods

Below are the **most commonly used methods** from `java.util.Arrays`, with **minimal explanation + runnable examples**.

```java
import java.util.Arrays;
```

### 0.1 `Arrays.toString` / `Arrays.deepToString`

```java
int[] a = {1, 2, 3};
System.out.println(Arrays.toString(a));
// [1, 2, 3]

int[][] m = {{1, 2}, {3, 4}};
System.out.println(Arrays.deepToString(m));
// [[1, 2], [3, 4]]
```

- `toString` → 1D arrays
    
- `deepToString` → multi-dimensional arrays
    

---

### 0.2 `Arrays.sort`

```java
int[] a = {5, 2, 9, 1};
Arrays.sort(a);
System.out.println(Arrays.toString(a));
// [1, 2, 5, 9]
```

- Sorts **in-place**
    
- Ascending order for primitives
    

---

### 0.3 `Arrays.binarySearch`

```java
int[] a = {1, 2, 5, 9};
int idx = Arrays.binarySearch(a, 5);
System.out.println(idx); // 2
```

Return rules:

- `>= 0` → index of the element
    
- `< 0` → `-(insertionPoint) - 1`
    

⚠️ **Array must already be sorted** using the same ordering.

---

### 0.4 `Arrays.equals` / `Arrays.deepEquals`

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(a == b);                 // false (reference)
System.out.println(Arrays.equals(a, b));    // true  (content)
```

---

### 0.5 `Arrays.copyOf` / `Arrays.copyOfRange`

```java
int[] a = {1, 2, 3, 4, 5};

int[] copy = Arrays.copyOf(a, a.length);
int[] mid  = Arrays.copyOfRange(a, 1, 4); // [1,4)

System.out.println(Arrays.toString(copy)); // [1, 2, 3, 4, 5]
System.out.println(Arrays.toString(mid));  // [2, 3, 4]
```

---

### 0.6 `Arrays.fill`

```java
int[] a = new int[5];
Arrays.fill(a, 7);
System.out.println(Arrays.toString(a));
// [7, 7, 7, 7, 7]
```

---

## 0.7 Binary Search: Full Example (Unsorted → Sort → Search)

```java
import java.util.Arrays;

public class BinarySearchDemo {
    public static void main(String[] args) {

        int[] data = {7, 2, 9, 1, 5};

        // 1) Unsorted array
        System.out.println("original = " + Arrays.toString(data));

        // 2) Sort first (required)
        Arrays.sort(data);
        System.out.println("sorted   = " + Arrays.toString(data));

        // 3) Binary search
        int target = 5;
        int index = Arrays.binarySearch(data, target);

        if (index >= 0) {
            System.out.println("Found " + target + " at index " + index);
        } else {
            int insertionPoint = -index - 1;
            System.out.println("Not found, insertion point = " + insertionPoint);
        }
    }
}
```

Output example:

```text
original = [7, 2, 9, 1, 5]
sorted   = [1, 2, 5, 7, 9]
Found 5 at index 2
```

Key rules:

- **Never** binary-search an unsorted array
    
- Sort order must match search order
    
- Negative result encodes insertion position
    

---

> Scope: core syntax, traversal patterns, `java.util.Arrays` helpers, searching/sorting, reference semantics, varargs, and multi-dimensional arrays.

---

## 1. What an Array Is

- A Java array is a **fixed-length**, **indexed** container of elements of the **same type**.
    
- Arrays are **objects** (even for primitive arrays), so the variable holds a **reference** to the array.
    
- Index range: `0 .. length - 1`.
    

Key property:

- `length` is a **field** (not a method): `arr.length`.
    

---

## 2. Declaring, Initializing, Accessing

### 2.1 Declaration

```java
int[] a;      // preferred
int b[];      // valid but avoid (C-style)
String[] s;
```

### 2.2 Initialization

```java
int[] x = new int[5];          // default values
int[] y = {1, 2, 3};           // array initializer
var z = new double[] {1, 2};   // anonymous array
```

### 2.3 Default Values

- numeric primitives: `0`, `0.0`
    
- `boolean`: `false`
    
- `char`: `\u0000`
    
- reference types: `null`
    

### 2.4 Access

```java
int first = y[0];
y[1] = 99;
```

Out-of-bounds: `ArrayIndexOutOfBoundsException`.

---

## 3. Populating Arrays

### 3.1 Simple loop

```java
int[] data = new int[10];
for (int i = 0; i < data.length; i++) {
    data[i] = i * i;
}
```

### 3.2 From input (pattern)

- Always validate index and input.
    
- Prefer parsing to a temporary variable first, then assign.
    

---

## 4. Looping Patterns (Traversal)

### 4.1 Index-based for (most flexible)

```java
for (int i = 0; i < arr.length; i++) {
    // can access i, arr[i], neighbors
}
```

Use when:

- you need indices
    
- you modify elements
    
- you need multiple arrays in sync
    

### 4.2 Enhanced for (for-each)

```java
for (int v : arr) {
    // v is a copy for primitives
}
```

Notes:

- You **cannot** change the underlying primitive array by modifying `v`.
    
- For reference arrays, `v` is a copy of the reference; you can mutate the _object_, but reassigning `v` doesn’t change the array slot.
    

### 4.3 Two-pointer traversal

Common for reversing, partitioning, in-place operations.

---

## 5. Searching

### 5.1 Linear search (unsorted)

```java
static int indexOf(int[] a, int target) {
    for (int i = 0; i < a.length; i++) {
        if (a[i] == target) return i;
    }
    return -1;
}
```

### 5.2 Binary search (sorted)

Use `Arrays.binarySearch(...)` **only if the array is sorted according to the same order**.

Return contract:

- `>= 0`: found index
    
- `< 0`: `-(insertionPoint) - 1`
    

---

## 6. `java.util.Arrays` Helper Class

Most-used APIs:

- `Arrays.toString(int[])` / `Arrays.deepToString(Object[][])`
    
- `Arrays.sort(...)` (dual-pivot quicksort for primitives; TimSort for objects)
    
- `Arrays.fill(arr, value)`
    
- `Arrays.copyOf(arr, newLen)`
    
- `Arrays.copyOfRange(arr, from, to)`
    
- `Arrays.equals(a, b)` / `Arrays.deepEquals(...)`
    
- `Arrays.binarySearch(...)`
    

Important:

- `arr.toString()` does **not** print content; it prints identity info.
    
- For 2D primitive arrays, `Arrays.deepToString(...)` works because it’s an `Object[]` of `int[]`.
    

---

## 7. Sorting (Ascending / Descending)

### 7.1 Ascending (primitives)

```java
Arrays.sort(a);
```

### 7.2 Descending

Primitives do not support a comparator directly. Two common approaches:

1. Sort ascending then reverse in-place.
    
2. Convert to boxed `Integer[]` and use a comparator (slower, allocates).
    

Recommended (approach 1) for performance:

```java
Arrays.sort(a);
reverseInPlace(a);
```

---

## 8. Equality Checks

- `a == b` compares **references** (same object?)
    
- `Arrays.equals(a, b)` compares **contents** (1D)
    
- `Arrays.deepEquals(a, b)` compares nested arrays
    

---

## 9. Reference vs Value Semantics (Critical)

### 9.1 Arrays are references

```java
int[] a = {1, 2, 3};
int[] b = a;
b[0] = 99;
System.out.println(a[0]); // 99
```

### 9.2 Passing arrays to methods

- Java passes references **by value**.
    
- <span style="color:rgb(255, 0, 0)">Mutating the array inside a method affects the caller.</span>
    
- <span style="color:rgb(255, 0, 0)">Reassigning the parameter does not affect the caller.</span>
    

```java
static void mutate(int[] x) {
    x[0] = 7;        // affects caller
    x = new int[10]; // does NOT affect caller
}
```

### 9.3 Defensive copying

Use when you must prevent callers from mutating internal state.

```java
this.data = Arrays.copyOf(input, input.length);
```

---

## 10. Varargs (`...`) and Arrays

- Varargs is syntactic sugar for an array parameter.
    

```java
static int sum(int... values) {
    int s = 0;
    for (int v : values) s += v;
    return s;
}

sum(1, 2, 3);
sum(new int[] {1, 2, 3});
```

Rules:

- Varargs must be **last** parameter.
    
- `sum()` with no args is valid: `values.length == 0`.
    

---

## 11. Common Array Algorithms (Reusable Patterns)

### 11.1 Minimum / maximum

```java
static int min(int[] a) {
    if (a == null || a.length == 0) {
        throw new IllegalArgumentException("array must be non-empty");
    }
    int m = a[0];
    for (int i = 1; i < a.length; i++) {
        if (a[i] < m) m = a[i];
    }
    return m;
}
```

### 11.2 Reverse in-place (two pointers)

```java
static void reverseInPlace(int[] a) {
    if (a == null) return;
    int i = 0, j = a.length - 1;
    while (i < j) {
        int tmp = a[i];
        a[i] = a[j];
        a[j] = tmp;
        i++; j--;
    }
}
```

---

## 12. 2D Arrays (Matrices)

### 12.1 Structure

`int[][] m` is an array of `int[]` rows.

```java
int[][] m = {
    {1, 2, 3},
    {4, 5, 6}
};

int rows = m.length;
int cols = m[0].length;
```

### 12.2 Traversal

```java
for (int r = 0; r < m.length; r++) {
    for (int c = 0; c < m[r].length; c++) {
        // m[r][c]
    }
}
```

---

## 13. Jagged Arrays (Ragged Arrays)

Rows can have different lengths.

```java
int[][] jagged = new int[3][];
jagged[0] = new int[] {1, 2};
jagged[1] = new int[] {3, 4, 5};
jagged[2] = new int[] {6};

for (int r = 0; r < jagged.length; r++) {
    for (int c = 0; c < jagged[r].length; c++) {
        // safe per-row length
    }
}
```

Pitfall:

- `jagged[r]` can be `null` if not initialized.
    

---

## 14. Pitfalls & Best Practices (High-Value)

### 14.1 Off-by-one

- Always prefer `i < arr.length`.
    

### 14.2 Printing arrays

- Use `Arrays.toString(...)` / `Arrays.deepToString(...)`.
    

### 14.3 Sorting + searching must match

- `binarySearch` requires the same ordering used for sorting.
    

### 14.4 Avoid exposing internal arrays

- Use defensive copies in constructors/getters.
    

### 14.5 Prefer `System.arraycopy` / `Arrays.copyOf` for copying

- Manual loops are error-prone.
    

### 14.6 Be explicit with `null` handling

- Decide contract: throw, return sentinel, or ignore.
    

---

## 15. Integrated Example (Non-trivial)

This example demonstrates: populate, copy, sort, descending, binary search, equality, min, reverse, 2D/jagged printing.

```java
import java.util.Arrays;

public class ArraysDemo {

    public static void main(String[] args) {
        int[] a = {7, 3, 9, 3, -1, 10, 4};

        // 1) Content printing
        System.out.println("a=" + Arrays.toString(a));

        // 2) Defensive copy example
        int[] copy = Arrays.copyOf(a, a.length);
        System.out.println("copy equals a? " + Arrays.equals(copy, a));
        System.out.println("same reference? " + (copy == a));

        // 3) Sort ascending
        Arrays.sort(copy);
        System.out.println("sorted=" + Arrays.toString(copy));

        // 4) Binary search (requires sorted)
        int idx = Arrays.binarySearch(copy, 4);
        System.out.println("indexOf(4) in sorted=" + idx);

        // 5) Descending via reverse
        reverseInPlace(copy);
        System.out.println("desc=" + Arrays.toString(copy));

        // 6) Min in original (unsorted)
        System.out.println("min(a)=" + min(a));

        // 7) Fill + copyOfRange
        int[] filled = new int[5];
        Arrays.fill(filled, 42);
        System.out.println("filled=" + Arrays.toString(filled));

        int[] mid = Arrays.copyOfRange(a, 1, 5); // [1,5)
        System.out.println("range(1,5)=" + Arrays.toString(mid));

        // 8) 2D and jagged arrays
        int[][] matrix = {
                {1, 2, 3},
                {4, 5, 6}
        };
        int[][] jagged = {
                {1, 2},
                {3, 4, 5},
                {6}
        };

        System.out.println("matrix=" + Arrays.deepToString(matrix));
        System.out.println("jagged=" + Arrays.deepToString(jagged));

        // 9) Varargs
        System.out.println("sum(1,2,3,4)=" + sum(1, 2, 3, 4));
    }

    static int min(int[] a) {
        if (a == null || a.length == 0) {
            throw new IllegalArgumentException("array must be non-empty");
        }
        int m = a[0];
        for (int i = 1; i < a.length; i++) {
            if (a[i] < m) m = a[i];
        }
        return m;
    }

    static void reverseInPlace(int[] a) {
        if (a == null) return;
        int i = 0, j = a.length - 1;
        while (i < j) {
            int tmp = a[i];
            a[i] = a[j];
            a[j] = tmp;
            i++; j--;
        }
    }

    static int sum(int... values) {
        int s = 0;
        for (int v : values) s += v;
        return s;
    }
}
```

---

## 16. Quick Checklist

- Use `arr.length` correctly
    
- Choose traversal style intentionally (index vs for-each)
    
- Prefer `Arrays.*` helpers for correctness
    
- `binarySearch` only on properly sorted arrays
    
- Remember: arrays are references; copy defensively when needed
    
- 2D arrays are arrays of rows; jagged arrays are normal