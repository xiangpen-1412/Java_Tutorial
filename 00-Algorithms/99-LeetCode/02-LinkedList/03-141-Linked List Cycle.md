# LeetCode 141：Linked List Cycle 背后的知识

## 1. 题目在考什么

这道题给你一个链表头节点 `head`，让你判断这个链表里有没有环。

正常链表是：

```text
1 -> 2 -> 3 -> 4 -> null
```

最后会走到 `null`，说明链表结束了。

有环链表是：

```text
1 -> 2 -> 3 -> 4
     ↑         ↓
     ← ← ← ← ←
```

如果一直沿着 `next` 走，会不断重复：

```text
2 -> 3 -> 4 -> 2 -> 3 -> 4 ...
```

所以这道题本质上是在问：

> 从 `head` 开始一直走 `next`，会不会进入死循环？

---

## 2. 最直观的想法：HashSet

最容易想到的方法是：

```text
我每走到一个节点，就把这个节点记录下来。
如果以后又走到了同一个节点，说明有环。
```

所以可以用：

```java
Set<ListNode> visited = new HashSet<>();
```

遍历时：

```text
当前节点没出现过：加入 set，继续走
当前节点出现过：说明有环
走到 null：说明没有环
```

这个思路非常直观。

---

## 3. HashSet 到底是怎么判断重复的？

HashSet 判断一个元素是否重复，核心靠两个方法：

```java
hashCode()
equals()
```

它的大概逻辑是：

```text
先用 hashCode() 找位置
如果位置上可能有相同元素
再用 equals() 判断是不是真的相同
```

所以 HashSet 不是固定“看值”，也不是固定“看地址”。

它真正看的是：

> 这个类的 `hashCode()` 和 `equals()` 怎么定义。

---

## 4. 普通类型放进 HashSet

比如：

```java
Set<Integer> set = new HashSet<>();

set.add(1);
set.add(1);
```

最后 set 里只有一个 `1`。

因为 `Integer` 已经重写好了：

```java
hashCode()
equals()
```

它会按照数值判断重复。

再比如：

```java
Set<String> set = new HashSet<>();

set.add(new String("abc"));
set.add(new String("abc"));
```

最后 set 里也只有一个 `"abc"`。

虽然这是两个不同的 String 对象，但是 String 的 `equals()` 比较的是内容。

所以 HashSet 认为它们重复。

---

## 5. ListNode 放进 HashSet

LeetCode 的 `ListNode` 通常是这样：

```java
class ListNode {
    int val;
    ListNode next;
}
```

它没有重写：

```java
equals()
hashCode()
```

所以它使用的是 `Object` 默认逻辑。

默认情况下：

```text
equals() 判断的是不是同一个对象引用
hashCode() 也和对象身份有关
```

所以：

```java
ListNode a = new ListNode(1);
ListNode b = new ListNode(1);

Set<ListNode> set = new HashSet<>();

set.add(a);
set.add(b);
```

虽然 `a.val` 和 `b.val` 都是 `1`，但它们是两个不同的节点对象。

所以 HashSet 认为它们不是重复元素。

但是：

```java
ListNode a = new ListNode(1);
ListNode b = a;

Set<ListNode> set = new HashSet<>();

set.add(a);
set.add(b);
```

这里 `a` 和 `b` 指向同一个节点对象。

所以 HashSet 认为它们是重复元素。

---

## 6. 为什么判断环要存 ListNode，而不是存 val？

判断链表有没有环，真正要判断的是：

> 有没有再次走到同一个节点对象。

不是判断：

> 有没有遇到相同的节点值。

比如：

```text
1 -> 2 -> 1 -> 3 -> null
```

这里值 `1` 出现了两次。

但是这是两个不同的节点对象，链表没有环。

如果你用：

```java
Set<Integer> visited = new HashSet<>();
```

然后存 `head.val`，就会误判。

所以判断链表环应该用：

```java
Set<ListNode> visited = new HashSet<>();
```

它判断的是：

```text
这个 ListNode 对象以前有没有出现过
```

而不是：

```text
这个节点的 val 以前有没有出现过
```

---

## 7. HashSet 解法的问题

HashSet 解法能过，而且很好理解。

但是它需要额外空间。

因为每走一个节点，都要把节点放进 set 里。

所以复杂度是：

```text
时间复杂度：O(n)
空间复杂度：O(n)
```

它的问题不是不能用，而是：

> 它用了额外内存来记录所有走过的节点。

这时候就可以进一步思考：

```text
能不能不记录所有节点，也判断有没有环？
```

这就是 Floyd 判圈算法的出发点。

---

## 8. Floyd 判圈算法是什么

Floyd 判圈算法也叫快慢指针。

它用两个指针：

```text
slow：慢指针，每次走 1 步
fast：快指针，每次走 2 步
```

代码思想是：

```java
slow = slow.next;
fast = fast.next.next;
```

如果没有环：

```text
fast 会先走到 null
```

如果有环：

```text
fast 会在环里追上 slow
```

一旦：

```java
slow == fast
```

说明两个指针指向了同一个节点对象，链表有环。

---

## 9. 这个算法是怎么想出来的？

这个算法的核心类比是：

> 两个人在跑道上跑步，一个跑得慢，一个跑得快。

如果是在一条直路上跑：

```text
快的人会先跑到终点。
```

对应链表就是：

```text
fast 先走到 null
```

说明没有环。

如果是在一个圆形跑道上跑：

```text
快的人永远不会到终点。
而且他一定会追上慢的人。
```

因为快指针每一轮都比慢指针多走一步。

只要它们都进入同一个环，快指针和慢指针之间的距离就会不断变化，最后一定相遇。

所以 Floyd 算法不是靠记忆，而是靠：

> 速度差制造相遇。

---

## 10. 为什么有环时一定会相遇？

假设 slow 和 fast 都进入环之后。

每一轮：

```text
slow 走 1 步
fast 走 2 步
```

也就是说，相对于 slow 来说，fast 每轮多走 1 步。

如果环的长度是 `k`，那么 fast 和 slow 在环里的距离每轮都会变化 1。

最终一定会出现：

```text
距离变成 0
```

距离变成 0 就代表：

```java
slow == fast
```

也就是两个指针指向同一个节点。

这就是为什么只要有环，它们一定会相遇。

---

## 11. 为什么没有环时不会相遇？

如果链表没有环，它就是一条直线：

```text
1 -> 2 -> 3 -> 4 -> null
```

fast 每次走两步，所以它会比 slow 更快到达终点。

一旦：

```java
fast == null
```

或者：

```java
fast.next == null
```

就说明后面没有路了。

所以没有环。

---

## 12. 为什么 while 条件要这样写？

通常写法是：

```java
while (fast != null && fast.next != null)
```

原因是 fast 每次要走两步：

```java
fast = fast.next.next;
```

所以必须先保证：

```text
fast 不是 null
fast.next 也不是 null
```

否则会空指针异常。

这个条件也顺便代表：

```text
只要 fast 还能继续走，就继续判断。
如果 fast 不能走了，说明没有环。
```

---

## 13. 为什么比较 `slow == fast`

这里比较的是：

```java
slow == fast
```

不是：

```java
slow.val == fast.val
```

因为链表里不同节点的值可以相同。

判断链表是否有环，看的是：

> 有没有回到同一个节点对象。

所以必须比较引用：

```java
slow == fast
```

---

## 14. 两种思路对比

### HashSet 解法

思想是：

```text
我记住所有走过的节点。
如果再次遇到同一个节点对象，就说明有环。
```

特点是：

```text
容易理解
代码直观
需要额外空间
```

复杂度：

```text
时间复杂度：O(n)
空间复杂度：O(n)
```

---

### Floyd 快慢指针解法

思想是：

```text
一个指针走得慢，一个指针走得快。
如果有环，快指针一定会追上慢指针。
如果没环，快指针会先走到 null。
```

特点是：

```text
不需要额外空间
更经典
更适合面试
```

复杂度：

```text
时间复杂度：O(n)
空间复杂度：O(1)
```

---

## 15. 这道题背后的核心知识

这道题背后包含很多知识点：

```text
1. 链表节点是引用类型
2. 判断环不能只看 val，要看是不是同一个节点对象
3. HashSet 判断重复依赖 hashCode() 和 equals()
4. ListNode 默认没重写 equals/hashCode，所以按对象引用判断
5. HashSet 解法空间复杂度是 O(n)
6. 快慢指针可以把空间优化到 O(1)
7. Floyd 算法利用速度差判断是否会相遇
8. 链表题经常不是处理值，而是处理节点之间的引用关系
```

---

## 一句话总结

LeetCode 141 的核心不是单纯判断链表有没有环，而是理解：

> 如果链表有环，沿着 next 走会再次回到同一个节点对象。

HashSet 解法是：

> 记录走过的节点对象，看有没有重复访问。

Floyd 快慢指针解法是：

> 利用快慢速度差，如果有环，快指针一定会追上慢指针。

这道题真正训练的是：

> 链表引用关系、HashSet 对象比较逻辑、空间复杂度优化，以及快慢指针思想。