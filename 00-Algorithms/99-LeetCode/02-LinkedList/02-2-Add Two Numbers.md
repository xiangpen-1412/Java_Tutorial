# 局部处理思想

## 题目

给两个非空链表，分别表示两个非负整数。

每个链表的数字是反向存储的：

```text
个位在前，十位在后，百位继续往后。
```

题目要你把这两个数相加，并用同样的链表形式返回结果。
## 1. 核心意思

局部处理的核心是：

> 不要一上来就想着拿到完整结果，而是每次只处理当前能看到的一小部分。

很多题不需要先把所有数据都整理完。

更常见的方式是：

```text
处理当前这一项
更新当前状态
继续处理下一项
```

---

## 2. 为什么重要？

因为很多数据结构不是一次性全部展开给你的。

比如：

```text
链表：只能一个节点一个节点往后走
日志：一条一条处理
Excel：一行一行读取
用户操作：一次一次发生
```

所以你要学会问自己：

```text
当前我能拿到什么？
当前我要处理什么？
什么状态要留给下一步？
```

---

## 3. 局部处理和整体处理的区别

整体处理是：

```text
先拿到全部数据
再统一计算
```

局部处理是：

```text
每次只处理当前数据
处理完就更新状态
最后自然得到结果
```

比如计算订单总价：

```text
遍历每个商品
当前商品多少钱
加到 total 里
继续下一个商品
```

这里的 `total` 就是保存下来的状态。

---

## 4. Add Two Numbers 里的局部处理

LeetCode 2 Add Two Numbers 就是典型的局部处理。

每一轮只处理当前这一位：

```text
当前能拿到：l1 当前节点的值，l2 当前节点的值
当前要做：算出这一位的结果
保存给下一步：carry 进位
```

所以它不是先把两个链表完整转成数字。

而是：

```text
处理当前位
生成当前结果节点
保存进位
继续下一位
```

这就是局部处理。

---

## 5. dummy node 和 cur 的作用

在链表题里，经常会创建一个 dummy node：

```java
ListNode sum = new ListNode(0);
ListNode cur = sum;
```

这里的重点是：

```text
sum：固定不动，用来记住结果链表的头
cur：不断往后走，用来接新的节点
```

一开始：

```text
sum 和 cur 指向同一个节点
```

后面执行：

```java
cur.next = new ListNode(value);
cur = cur.next;
```

意思是：

```text
先在当前节点后面接一个新节点
再让 cur 移动到新节点
```

所以最后 `cur` 一直往后走，但是 `sum` 不动。

`sum` 不动的原因是：

> 它要一直抓住结果链表的开头。

最后返回：

```java
return sum.next;
```

因为 `sum` 本身只是一个假的头节点，真正的结果从 `sum.next` 开始。

---

## 6. Java 引用类型里的关键逻辑

这里最容易混的是：

```java
ListNode sum = new ListNode(0);
ListNode cur = sum;
```

这不是复制了一个新的节点。

而是：

```text
sum 和 cur 两个变量，一开始指向同一个 ListNode 对象。
```

所以一开始执行：

```java
cur.next = new ListNode(7);
```

其实是在修改这个共同对象内部的 `next`。

因此 `sum.next` 也能看到这个新节点。

---

## 7. mutable 和 immutable 的区别

Java 里对象变量通常是 reference type。

但是对象能不能被改，要看它是不是 mutable。

比如：

```text
ListNode 是 mutable
ArrayList 是 mutable
HashMap 是 mutable
```

它们内部的数据可以被修改。

所以：

```java
ArrayList<Integer> a = new ArrayList<>();
ArrayList<Integer> b = a;

a.add(10);
```

此时 `b` 也能看到 `10`。

因为 `a` 和 `b` 指向的是同一个 ArrayList 对象。

但是：

```text
Integer、Long、String 这些通常是 immutable
```

比如：

```java
Integer a = 10;
Integer b = a;
b = 20;
```

这里不是把原来的 `10` 改成 `20`。

而是让 `b` 指向了另一个新的 Integer 对象。

所以 `a` 还是 `10`。

---

## 8. 修改对象内部 vs 变量重新指向

这个区别非常重要。

### 修改对象内部

```java
cur.next = new ListNode(7);
list.add(10);
map.put("name", "Tom");
```

这种操作会影响所有指向同一个对象的变量。

### 变量重新指向

```java
cur = cur.next;
list = new ArrayList<>();
str = "hello";
```

这种操作只是让当前变量换一个指向，不会影响其他变量。

---

## 9. 最终理解

局部处理不是某一道题的技巧，而是一种思维方式：

```text
每次只处理当前数据
用变量保存必要状态
不断推进
最后得到完整结果
```

在链表题里，还要额外理解：

```text
变量保存的是对象引用
mutable 对象内部可以被修改
dummy node 用来固定链表头
cur 用来移动并构建链表
```

---

## 一句话总结

局部处理就是：

> 不急着一次性得到完整答案，而是每次处理当前这一步，并保存下一步需要的状态。

链表里的 dummy node 思想就是：

> 让一个指针固定头部，另一个指针负责往后构建结果。