___
# 1. List & Array Conversion
## Core Confusion

When converting between **array**, **List**, and **ArrayList**, the biggest source of mistakes is that Java treats:

- **object arrays** (`String[]`, `Integer[]`) differently from
    
- **primitive arrays** (`int[]`, `double[]`)
    

Also, `Arrays.asList(...)`, `List.of(...)`, and `new ArrayList<>(...)` do **not** behave the same way.

---

## 1. The Most Important Distinction

### Object array

Examples:

```java
String[] arr = {"a", "b", "c"};
Integer[] nums = {1, 2, 3};
```

These work naturally with `Arrays.asList(...)`.

### Primitive array

Examples:

```java
int[] arr = {1, 2, 3};
double[] values = {1.2, 3.4};
```

These do **not** convert to `List<Integer>` or `List<Double>` by `Arrays.asList(...)` the way many beginners expect.

---

## 2. Correct Conversions: Object Array <-> List / ArrayList

### A. Object array -> List

```java
String[] arr = {"Tom", "Jerry", "Alice"};
List<String> list = Arrays.asList(arr);
```

### Important note

This creates a **fixed-size List view** backed by the original array.

That means:

- `list.add(...)` -> not allowed
    
- `list.remove(...)` -> not allowed
    
- `list.set(...)` -> allowed
    

Example:

```java
String[] arr = {"Tom", "Jerry", "Alice"};
List<String> list = Arrays.asList(arr);

list.set(0, "Bob");

System.out.println(list);           // [Bob, Jerry, Alice]
System.out.println(Arrays.toString(arr)); // [Bob, Jerry, Alice]
```

Because the list is backed by the same array, changing one affects the other.

---

### B. Object array -> real mutable ArrayList

```java
String[] arr = {"Tom", "Jerry", "Alice"};
ArrayList<String> list = new ArrayList<>(Arrays.asList(arr));
```

Now this is a normal resizable `ArrayList`:

```java
list.add("Bob");
list.remove("Jerry");
```

This is the correct format when you want to modify the collection structure.

---

### C. List / ArrayList -> object array

```java
List<String> list = new ArrayList<>();
list.add("Tom");
list.add("Jerry");
list.add("Alice");

String[] arr = list.toArray(new String[0]);
```

Also valid:

```java
String[] arr = list.toArray(new String[list.size()]);
```

### Why `new String[0]`?

This tells Java the target array type is `String[]`. Java will create an array of the correct size automatically.

---

## 3. Correct Conversions: Primitive Array <-> List / ArrayList

This is where mistakes happen most often.

### Wrong expectation

```java
int[] arr = {1, 2, 3};
List<int[]> list = Arrays.asList(arr);
```

This does **not** create `List<Integer>`.

It creates a list with **one element**, and that element is the entire `int[]`.

Reason:

- generic collections require reference types
    
- `int` is primitive
    
- `Arrays.asList(...)` works element-by-element only for object arrays, not primitive arrays
    

---

### A. `int[]` -> `List<Integer>`

#### Correct format using Stream

```java
int[] arr = {1, 2, 3, 4};
List<Integer> list = Arrays.stream(arr)
        .boxed()
        .toList();
```

### Explanation

- `Arrays.stream(arr)` -> `IntStream`
    
- `boxed()` -> converts each `int` into `Integer`
    
- `toList()` -> collects to `List<Integer>`
    

---

### B. `int[]` -> `ArrayList<Integer>`

```java
int[] arr = {1, 2, 3, 4};
ArrayList<Integer> list = new ArrayList<>(
        Arrays.stream(arr).boxed().toList()
);
```

---

### C. `List<Integer>` -> `int[]`

```java
List<Integer> list = List.of(1, 2, 3, 4);

int[] arr = list.stream()
        .mapToInt(Integer::intValue)
        .toArray();
```

### Explanation

- `stream()` -> `Stream<Integer>`
    
- `mapToInt(Integer::intValue)` -> `IntStream`
    
- `toArray()` -> `int[]`
    

---

### D. Manual loop version

Sometimes the loop version is easier to understand.

#### `int[]` -> `List<Integer>`

```java
int[] arr = {1, 2, 3, 4};
List<Integer> list = new ArrayList<>();

for (int num : arr) {
    list.add(num);
}
```

#### `List<Integer>` -> `int[]`

```java
List<Integer> list = List.of(1, 2, 3, 4);
int[] arr = new int[list.size()];

for (int i = 0; i < list.size(); i++) {
    arr[i] = list.get(i);
}
```

---

## 4. `Arrays.asList(...)` vs `List.of(...)` vs `new ArrayList<>(...)`

### `Arrays.asList(...)`

```java
List<String> list = Arrays.asList("a", "b", "c");
```

Properties:

- fixed size
    
- backed by array
    
- cannot `add()` or `remove()`
    
- can `set()`
    

---

### `List.of(...)`

```java
List<String> list = List.of("a", "b", "c");
```

Properties:

- immutable
    
- cannot `add()`
    
- cannot `remove()`
    
- cannot `set()`
    
- cleaner syntax for creating read-only lists
    

---

### `new ArrayList<>(...)`

```java
ArrayList<String> list = new ArrayList<>(List.of("a", "b", "c"));
```

Properties:

- fully mutable
    
- can `add()`
    
- can `remove()`
    
- can `set()`
    

This is the safe format when you want a real modifiable list.

---

## 5. Correct Formats Summary

### Object array -> List

```java
String[] arr = {"a", "b", "c"};
List<String> list = Arrays.asList(arr);
```

### Object array -> ArrayList

```java
String[] arr = {"a", "b", "c"};
ArrayList<String> list = new ArrayList<>(Arrays.asList(arr));
```

### List -> object array

```java
String[] arr = list.toArray(new String[0]);
```

### `int[]` -> `List<Integer>`

```java
List<Integer> list = Arrays.stream(arr).boxed().toList();
```

### `int[]` -> `ArrayList<Integer>`

```java
ArrayList<Integer> list = new ArrayList<>(Arrays.stream(arr).boxed().toList());
```

### `List<Integer>` -> `int[]`

```java
int[] arr = list.stream().mapToInt(Integer::intValue).toArray();
```

---

## 6. Error Patterns to Remember

### Error 1: Thinking `Arrays.asList(intArray)` gives `List<Integer>`

Wrong idea:

```java
int[] arr = {1, 2, 3};
var list = Arrays.asList(arr);
```

Actual result:

- a `List<int[]>`
    
- size is `1`
    
- the only element is the entire array
    

---

### Error 2: Thinking `Arrays.asList(...)` returns a normal resizable ArrayList

Wrong idea:

```java
List<String> list = Arrays.asList("a", "b", "c");
list.add("d");
```

This throws an exception because the size is fixed.

---

### Error 3: Forgetting primitive vs wrapper type difference

Wrong idea:

```java
List<int> list;
```

This is illegal because generics require reference types.

Correct:

```java
List<Integer> list;
```

---

## 7. Recommended Practical Rules

### Rule 1

If you are converting a **reference type array** like `String[]` or `Integer[]`, use:

```java
new ArrayList<>(Arrays.asList(arr))
```

when you want a mutable list.

### Rule 2

If you are converting a **primitive array** like `int[]`, use Stream:

```java
Arrays.stream(arr).boxed().toList()
```

### Rule 3

If you need `int[]` back from `List<Integer>`, use:

```java
list.stream().mapToInt(Integer::intValue).toArray()
```

### Rule 4

Do not confuse:

- fixed-size list
    
- immutable list
    
- mutable ArrayList
    

These are three different things.

---

## 8. One Final Memory Shortcut

### Object array

Easy conversion.

### Primitive array

Needs boxing / unboxing.

That is the core reason why array/list conversion in Java feels inconsistent.

___
