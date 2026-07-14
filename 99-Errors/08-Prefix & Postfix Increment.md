# Prefix Increment vs Postfix Increment in Array Index

## Core Difference

这两行代码的区别在于：`stackIndex` 是先变化，还是后变化。

```java
stack[++stackIndex] = i;
```

含义是：

```text
先让 stackIndex 加 1
再使用新的 stackIndex 作为数组下标
```

```java
stack[stackIndex++] = i;
```

含义是：

```text
先使用当前 stackIndex 作为数组下标
再让 stackIndex 加 1
```

---

## Example

假设一开始：

```java
int stackIndex = -1;
int i = 10;
```

### 1. `stack[++stackIndex] = i`

执行过程：

```text
stackIndex 原来是 -1
++stackIndex 先变成 0
stack[0] = 10
```

执行后：

```text
stackIndex = 0
stack[0] = 10
```

这适合手写栈中 `top = -1` 的情况。

---

### 2. `stack[stackIndex++] = i`

执行过程：

```text
stackIndex 原来是 -1
先使用 stack[-1]
然后 stackIndex 才加 1
```

这会直接出错：

```text
ArrayIndexOutOfBoundsException
```

因为 Java 数组下标不能是 `-1`。

---

## Stack Push Rule

如果你用数组手写栈，并且空栈时：

```java
int stackIndex = -1;
```

那么 push 应该写成：

```java
stack[++stackIndex] = i;
```

也就是：

```text
先移动到新的栈顶位置
再放入元素
```

---

## When Is `stack[stackIndex++] = i` Correct?

如果你的 `stackIndex` 表示的是“下一个可以插入的位置”，而不是“当前栈顶位置”，那么可以使用：

```java
stack[stackIndex++] = i;
```

例如：

```java
int stackIndex = 0;
stack[stackIndex++] = 10; // 放入 stack[0]，然后 stackIndex 变成 1
stack[stackIndex++] = 20; // 放入 stack[1]，然后 stackIndex 变成 2
```

此时 `stackIndex` 的含义不是栈顶下标，而是：

```text
下一个空位置
```

---

## Memory Rule

记住一句话：

```text
++x：先加，再用
x++：先用，再加
```

放进数组下标时：

```java
stack[++stackIndex] = i; // 用加完之后的位置
stack[stackIndex++] = i; // 用加之前的位置
```

---

## Common Mistake

错误通常不是因为 `++` 本身难，而是因为没有先定义清楚变量含义。

### Case 1: `stackIndex` means current top

```java
int stackIndex = -1;
```

正确 push：

```java
stack[++stackIndex] = i;
```

正确 pop：

```java
int value = stack[stackIndex--];
```

---

### Case 2: `stackIndex` means next empty position

```java
int stackIndex = 0;
```

正确 push：

```java
stack[stackIndex++] = i;
```

正确 pop：

```java
int value = stack[--stackIndex];
```

---

## Final Summary

`stack[++stackIndex] = i` 和 `stack[stackIndex++] = i` 的最终结果都会让 `stackIndex` 增加 1，但它们使用的数组下标不同。

```text
stack[++stackIndex] = i
先加 1，再存入新位置

stack[stackIndex++] = i
先存入当前位置，再加 1
```

手写栈时最关键的是先确定：

```text
stackIndex 表示当前栈顶？
还是表示下一个空位置？
```

如果空栈用 `-1` 表示，通常用：

```java
stack[++stackIndex] = i;
```
