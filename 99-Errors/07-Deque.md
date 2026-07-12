# Java Deque 常用操作

## Deque 是什么

`Deque` 全称：

```text
Double-Ended Queue
```

中文叫：

```text
双端队列
```

它允许从头部和尾部进行：

```text
添加
删除
查看
```

因此，`Deque` 可以作为：

```text
栈
队列
真正的双端队列
```

使用。

---

# 创建 Deque

`Deque` 是接口，不能直接创建对象：

```java
Deque<Integer> deque = new Deque<>();
```

这是错误的。

通常使用 `ArrayDeque` 作为实现：

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<Integer> deque = new ArrayDeque<>();
```

这里：

```text
Deque<Integer>：
接口类型

ArrayDeque<>：
具体实现类
```

---

# Deque 的两个方向

可以把结构理解成：

```text
First / Head                    Last / Tail
     ↓                              ↓
[10, 20, 30, 40]
```

Java 中通常使用：

```text
First 表示头部
Last  表示尾部
```

---

# 头部操作

## 添加到头部

```java
deque.addFirst(value);
```

或者：

```java
deque.offerFirst(value);
```

例如：

```java
Deque<Integer> deque = new ArrayDeque<>();

deque.offerFirst(20);
deque.offerFirst(10);
```

结果：

```text
[10, 20]
```

---

## 删除头部

```java
deque.removeFirst();
```

或者：

```java
deque.pollFirst();
```

例如：

```java
int value = deque.pollFirst();
```

会返回并删除头部元素。

---

## 查看头部

```java
deque.getFirst();
```

或者：

```java
deque.peekFirst();
```

只查看元素，不会删除。

---

# 尾部操作

## 添加到尾部

```java
deque.addLast(value);
```

或者：

```java
deque.offerLast(value);
```

例如：

```java
Deque<Integer> deque = new ArrayDeque<>();

deque.offerLast(10);
deque.offerLast(20);
```

结果：

```text
[10, 20]
```

---

## 删除尾部

```java
deque.removeLast();
```

或者：

```java
deque.pollLast();
```

---

## 查看尾部

```java
deque.getLast();
```

或者：

```java
deque.peekLast();
```

---

# 抛异常版本和安全返回版本

Deque 中很多操作都有两种版本。

## 添加

```java
addFirst()
addLast()
```

失败时：

```text
抛出异常
```

```java
offerFirst()
offerLast()
```

失败时：

```text
返回 false
```

---

## 删除

```java
removeFirst()
removeLast()
```

Deque 为空时：

```text
抛出异常
```

```java
pollFirst()
pollLast()
```

Deque 为空时：

```text
返回 null
```

---

## 查看

```java
getFirst()
getLast()
```

Deque 为空时：

```text
抛出异常
```

```java
peekFirst()
peekLast()
```

Deque 为空时：

```text
返回 null
```

---

# 常用方法对照表

|操作|头部|尾部|
|---|---|---|
|添加并可能抛异常|`addFirst()`|`addLast()`|
|添加并返回状态|`offerFirst()`|`offerLast()`|
|删除并可能抛异常|`removeFirst()`|`removeLast()`|
|删除并返回 `null`|`pollFirst()`|`pollLast()`|
|查看并可能抛异常|`getFirst()`|`getLast()`|
|查看并返回 `null`|`peekFirst()`|`peekLast()`|

---

# 用 Deque 当栈

栈遵循：

```text
Last In, First Out
后进先出
```

创建：

```java
Deque<Integer> stack = new ArrayDeque<>();
```

常用操作：

```java
stack.push(value);
stack.pop();
stack.peek();
```

完整例子：

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.peek()); // 30
System.out.println(stack.pop());  // 30
System.out.println(stack.peek()); // 20
```

对应关系：

```java
push(value) ≈ addFirst(value)
pop()       ≈ removeFirst()
peek()      ≈ peekFirst()
```

所以 `push()` 是从头部加入。

---

# 用 Deque 当队列

队列遵循：

```text
First In, First Out
先进先出
```

创建：

```java
Deque<Integer> queue = new ArrayDeque<>();
```

常用操作：

```java
queue.offerLast(value);
queue.pollFirst();
queue.peekFirst();
```

完整例子：

```java
Deque<Integer> queue = new ArrayDeque<>();

queue.offerLast(10);
queue.offerLast(20);
queue.offerLast(30);

System.out.println(queue.peekFirst()); // 10
System.out.println(queue.pollFirst()); // 10
System.out.println(queue.peekFirst()); // 20
```

队列的方向是：

```text
从尾部进入
从头部出去
```

---

# 用 Deque 当真正的双端队列

```java
Deque<Integer> deque = new ArrayDeque<>();

deque.offerFirst(20);
deque.offerFirst(10);
deque.offerLast(30);
deque.offerLast(40);
```

结果：

```text
[10, 20, 30, 40]
```

可以从两端删除：

```java
deque.pollFirst(); // 删除 10
deque.pollLast();  // 删除 40
```

剩下：

```text
[20, 30]
```

---

# push、pop、peek 操作的是哪一端

```java
deque.push(value);
```

等价于：

```java
deque.addFirst(value);
```

```java
deque.pop();
```

等价于：

```java
deque.removeFirst();
```

```java
deque.peek();
```

等价于：

```java
deque.peekFirst();
```

因此，使用 Deque 作为栈时，栈顶位于头部。

---

# offerFirst 和 offerLast 的区别

```java
offerFirst(value);
```

将元素加入头部。

```java
offerLast(value);
```

将元素加入尾部。

例如：

```java
Deque<Integer> deque = new ArrayDeque<>();

deque.offerLast(20);  // [20]
deque.offerFirst(10); // [10, 20]
deque.offerLast(30);  // [10, 20, 30]
```

---

# pollFirst 和 pollLast 的区别

```java
pollFirst();
```

删除并返回头部元素。

```java
pollLast();
```

删除并返回尾部元素。

例如：

```java
Deque<Integer> deque = new ArrayDeque<>();

deque.offerLast(10);
deque.offerLast(20);
deque.offerLast(30);

int first = deque.pollFirst(); // 10
int last = deque.pollLast();   // 30
```

最后剩下：

```text
[20]
```

---

# peekFirst 和 peekLast 的区别

```java
peekFirst();
```

查看头部。

```java
peekLast();
```

查看尾部。

两者都不会删除元素。

---

# 时间复杂度

对于 `ArrayDeque`：

|操作|时间复杂度|
|---|--:|
|`offerFirst()`|均摊 `O(1)`|
|`offerLast()`|均摊 `O(1)`|
|`pollFirst()`|`O(1)`|
|`pollLast()`|`O(1)`|
|`peekFirst()`|`O(1)`|
|`peekLast()`|`O(1)`|
|`push()`|均摊 `O(1)`|
|`pop()`|`O(1)`|
|`peek()`|`O(1)`|
|`size()`|`O(1)`|
|`isEmpty()`|`O(1)`|
|`contains()`|`O(n)`|

添加操作写成均摊 `O(1)`，是因为底层数组偶尔需要扩容。

普通情况下：

```text
O(1)
```

扩容时：

```text
O(n)
```

但扩容不会每次发生，因此平均下来是：

```text
均摊 O(1)
```

---

# 为什么推荐 ArrayDeque，而不是 Stack

旧写法：

```java
Stack<Integer> stack = new Stack<>();
```

现在更推荐：

```java
Deque<Integer> stack = new ArrayDeque<>();
```

原因：

```text
Stack 是较老的类
Stack 继承 Vector
Vector 存在同步开销
ArrayDeque 通常性能更好
Deque 的接口更加完整
```

因此在算法题中，通常使用：

```java
Deque<Integer> stack = new ArrayDeque<>();
```

---

# ArrayDeque 和 LinkedList

两者都可以实现 Deque：

```java
Deque<Integer> deque1 = new ArrayDeque<>();
Deque<Integer> deque2 = new LinkedList<>();
```

通常优先使用：

```java
ArrayDeque
```

原因：

```text
底层使用数组
缓存局部性更好
没有链表节点对象
内存开销通常更小
实际速度通常更快
```

---

# ArrayDeque 不允许 null

下面的代码会报错：

```java
Deque<Integer> deque = new ArrayDeque<>();
deque.offerFirst(null);
```

因为 `ArrayDeque` 不允许保存 `null`。

因此：

```java
peekFirst()
pollFirst()
```

返回 `null` 时，可以明确表示：

```text
Deque 当前为空
```

---

# null 和自动拆箱问题

`Deque<Integer>` 中保存的是 `Integer`。

例如：

```java
Integer value = deque.peekFirst();
```

如果 Deque 为空：

```text
value = null
```

如果直接赋给 `int`：

```java
int value = deque.peekFirst();
```

Java 会自动拆箱：

```text
Integer → int
```

当返回值是 `null` 时，就会触发：

```text
NullPointerException
```

所以在无法保证 Deque 非空时，应先判断：

```java
if (!deque.isEmpty()) {
    int value = deque.peekFirst();
}
```

---

# 常见使用场景

## 栈

```text
括号匹配
逆波兰表达式
单调栈
DFS 的迭代写法
Min Stack
```

使用：

```java
push()
pop()
peek()
```

---

## 队列

```text
BFS
二叉树层序遍历
任务队列
```

使用：

```java
offerLast()
pollFirst()
peekFirst()
```

---

## 双端队列

```text
滑动窗口最大值
单调队列
需要同时维护头尾元素的问题
```

使用：

```java
offerFirst()
offerLast()
pollFirst()
pollLast()
peekFirst()
peekLast()
```

---

# 最终记忆模板

## 当作栈

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(value);
stack.pop();
stack.peek();
```

## 当作队列

```java
Deque<Integer> queue = new ArrayDeque<>();

queue.offerLast(value);
queue.pollFirst();
queue.peekFirst();
```

## 当作双端队列

```java
Deque<Integer> deque = new ArrayDeque<>();

deque.offerFirst(value);
deque.offerLast(value);

deque.pollFirst();
deque.pollLast();

deque.peekFirst();
deque.peekLast();
```

最核心的理解：

> Deque 是一个允许从两端操作的数据结构。栈和队列只是它的两种使用方式。