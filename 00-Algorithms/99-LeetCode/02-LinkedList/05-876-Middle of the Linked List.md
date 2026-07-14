# LeetCode 876：Middle of the Linked List 背后要学什么

## 1. 这道题在考什么

这道题给你一个单链表 `head`，让你返回链表的中间节点。

如果链表长度是奇数，中间节点很好理解：

```text
1 -> 2 -> 3 -> 4 -> 5
          ^
        middle
```

返回的是节点 `3`。

如果链表长度是偶数，会有两个中间节点：

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6
          ^    ^
       first  second
```

题目要求返回第二个中间节点，也就是节点 `4`。

所以这道题真正想训练的是：

> 如何在不知道链表长度的情况下，用指针关系找到中间位置。

---

## 2. 最直接的想法：先数长度

最容易想到的做法是：

```text
第一遍遍历链表，算出 length。
第二遍从 head 开始走 length / 2 步。
走到的位置就是要返回的中间节点。
```

这个方法可以通过，而且很好理解。

但是它需要遍历两次链表。

这时就可以继续思考：

```text
能不能只遍历一次，就让某个指针刚好停在中间？
```

这就是快慢指针的入口。

---

## 3. 快慢指针的核心想法

使用两个指针：

```text
slow：每次走 1 步
fast：每次走 2 步
```

当 `fast` 走到链表末尾时，`slow` 刚好走到中间。

因为 `fast` 的速度是 `slow` 的两倍：

```text
fast 走完整条链表
slow 就走完整条链表的一半
```

所以代码结构是：

```java
ListNode slow = head;
ListNode fast = head;

while (fast != null && fast.next != null) {
    fast = fast.next.next;
    slow = slow.next;
}

return slow;
```

---

## 4. 为什么 slow 和 fast 都从 head 开始

这道题要求偶数长度时返回第二个中间节点。

例如：

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

初始状态：

```text
slow = 1
fast = 1
```

每轮移动后：

```text
第 1 轮：slow = 2, fast = 3
第 2 轮：slow = 3, fast = 5
第 3 轮：slow = 4, fast = null
```

循环结束后，`slow` 停在 `4`，刚好是第二个中间节点。

所以这题不需要 dummy node。

如果随便加 dummy node，指针的起点会整体往前偏一格，偶数长度时就容易返回错误位置。

---

## 5. while 条件为什么必须用 &&

循环条件应该写成：

```java
while (fast != null && fast.next != null)
```

原因是循环体里有：

```java
fast = fast.next.next;
```

所以进入循环前，必须先保证：

```text
fast 不是 null
fast.next 也不是 null
```

如果写成：

```java
while (fast != null || fast.next != null)
```

就会出现空指针异常。

因为 `||` 是“或者”，当 `fast == null` 时，Java 还会继续尝试判断右边的 `fast.next != null`。

这时相当于：

```text
null.next
```

所以会报：

```text
NullPointerException
```

这里要记住：

> 访问 `fast.next` 之前，一定要先确认 `fast != null`。

---

## 6. 这道题和 Linked List Cycle 的关系

这道题和 LeetCode 141 Linked List Cycle 都用了快慢指针。

但是两道题的目的不一样。

LeetCode 141 是：

```text
fast 如果追上 slow，说明有环。
fast 如果走到 null，说明没环。
```

LeetCode 876 是：

```text
fast 负责走完整条链表。
slow 借助 fast 的两倍速度，停在中间位置。
```

所以快慢指针不是只用来判断环。

它更本质的作用是：

> 用两个指针之间的速度差，定位链表里的特殊位置。

---

## 7. 这道题背后要掌握的知识点

```text
1. 链表不能像数组一样直接用 index 跳到中间。
2. 如果想找中间节点，可以让 fast 走两步，slow 走一步。
3. fast 走到结尾时，slow 正好在中间。
4. 偶数长度返回第二个中间节点时，slow 和 fast 都从 head 开始。
5. while 条件要用 fast != null && fast.next != null，避免空指针异常。
6. dummy node 不是所有链表题都需要，用错反而会让位置偏移。
```

---

## 8. 复杂度

时间复杂度：

```text
O(n)
```

因为最多遍历一次链表。

空间复杂度：

```text
O(1)
```

因为只用了 `slow` 和 `fast` 两个额外指针。

---

## 一句话总结

LeetCode 876 的核心不是单纯“找中点”，而是理解：

> 当 `fast` 每次走两步、`slow` 每次走一步时，`fast` 到达链表结尾的那一刻，`slow` 正好停在链表中间；偶数长度时从 `head` 同时出发，会自然返回第二个中间节点。

