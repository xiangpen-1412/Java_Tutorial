# 155. Min Stack：解题复盘

## 题目

设计一个栈，除了普通栈操作以外，还要支持快速获取当前栈里的最小值。

需要支持：

```text
push
pop
top
getMin
```

重点是：

```text
getMin 要足够快，不能每次都重新扫描整个栈。
```
## 题目难点

普通栈可以做到：

```text
push() → O(1)
pop()  → O(1)
top()  → O(1)
```

但题目还要求：

```text
getMin() → O(1)
```

如果每次遍历整个栈找最小值，就是：

```text
O(n)
```

所以需要额外维护“当前最小值”。

---

## 我的核心思路

使用两个栈：

```java
Deque<Integer> stack;
Deque<Integer> minStack;
```

职责：

```text
stack：
保存所有元素

minStack：
保存当前阶段出现过的最小值
```

当新值小于或等于当前最小值时，同时压入 `minStack`。

当弹出的值等于当前最小值时，`minStack` 也同步弹出。

这样：

```java
getMin()
```

只需要读取 `minStack` 栈顶。

---

## 问题一：判断错了哪个栈是否为空

最初先执行：

```java
stack.offerFirst(value);
```

然后判断：

```java
if (stack.isEmpty())
```

这时 `stack` 已经不可能为空。

随后执行：

```java
int curMin = minStack.peekFirst();
```

但 `minStack` 还是空的，`peekFirst()` 返回 `null`。

`null` 被自动拆箱成 `int` 时，会触发：

```text
NullPointerException
```

正确判断应该是：

```java
if (minStack.isEmpty())
```

---

## 问题二：重复最小值没有保存

最初写的是：

```java
if (value < curMin)
```

这会漏掉重复的最小值。

例如：

```text
push(0)
push(1)
push(0)
```

如果第二个 `0` 不进入 `minStack`，弹出栈顶 `0` 后，`minStack` 就会错误地认为没有最小值了。

因此必须写：

```java
if (value <= curMin)
```

重复最小值也要分别记录。

---

## 为什么两个栈必须同步

压入时：

```text
所有元素进入 stack

只有小于或等于当前最小值的元素进入 minStack
```

弹出时：

```java
if (value == minStack.peekFirst()) {
    minStack.pollFirst();
}
```

否则普通栈已经删除最小值，但辅助栈仍然保留旧最小值。

---

## 改成数组栈时遇到的问题

后来为了减少 `Deque<Integer>` 的装箱和对象开销，尝试使用：

```java
int[] stack;
int[] minStack;
```

并维护：

```java
int top;
int minTop;
```

空栈使用：

```java
top = -1;
minTop = -1;
```

---

## 问题三：局部变量和成员变量

错误写法：

```java
public MinStack() {
    int top = -1;
    int minTop = -1;
}
```

这里的 `top` 和 `minTop` 只在构造方法内部有效。

`push()`、`pop()` 无法访问它们。

应该定义成成员变量：

```java
int top = -1;
int minTop = -1;
```

需要被多个方法共享的状态，必须放在类中。

---

## 问题四：赋值和比较

错误：

```java
if (minTop = -1)
```

正确：

```java
if (minTop == -1)
```

```text
=  表示赋值
== 表示比较
```

---

## 问题五：int[] 不能存 null

错误：

```java
stack[top] = null;
```

`int[]` 只能保存基本类型整数，不能保存 `null`。

数组栈删除元素时，只需要：

```java
top--;
```

数组中的旧值即使还在，也已经不属于有效栈区域。

---

## 两种辅助栈设计

### 方案一：只记录新的最小值

```text
minStack 只保存小于或等于当前最小值的值
```

优点：

```text
可能节省一些空间
```

缺点：

```text
需要单独维护 minTop
必须处理重复最小值
pop 时需要同步判断
```

### 方案二：每层都保存当前最小值

例如：

```text
stack:    [-2,  0, -3]
minStack: [-2, -2, -3]
```

`minStack[i]` 表示第 `i` 层及以下的最小值。

优点：

```text
两个数组共用一个 top
pop 只需要 top--
getMin 直接读取 minStack[top]
逻辑更简单
```

---

## 复杂度

所有操作：

```text
push()   → O(1)
pop()    → O(1)
top()    → O(1)
getMin() → O(1)
```

空间复杂度：

```text
O(n)
```

因为需要额外保存辅助最小值信息。

---

## 这道题学到的内容

```text
1. 用额外空间换取 O(1) 查询
2. 主栈和辅助栈必须保持同步
3. 重复最小值必须使用 <=
4. 集合返回 null 后自动拆箱会导致异常
5. 需要跨方法共享的变量必须是成员变量
6. 数组栈通过移动 top 实现逻辑删除
```

最核心的思维是：

> 当一个查询需要反复遍历时，可以在数据更新时同步维护额外状态，让查询直接得到结果。