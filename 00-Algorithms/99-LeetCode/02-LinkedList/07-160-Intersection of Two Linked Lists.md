# 160. Intersection of Two Linked Lists

## 题目要你做什么

给你两个链表的头节点：

```java
headA
headB
```

让你返回两个链表相交的那个节点。

如果两个链表不相交，返回：

```java
null
```

## 什么叫相交

这题的相交不是值一样。

比如两个节点的值都是 `8`，不一定代表它们相交。

真正的相交是：

```text
两个链表从某个节点开始，共用同一批节点对象
```

例子：

```text
A: 4 -> 1 \
          8 -> 4 -> 5
B: 5 -> 6 -> 1 /
```

这里要返回的是那个值为 `8` 的节点本身。

不是返回：

```java
8
```

而是返回：

```java
ListNode
```

也就是：

```java
return node;
```

## 我错在哪里

### 1. 把变量名写成了 Java 关键字

一开始写了类似：

```java
Boolean switch = false;
```

问题是：

- `switch` 是 Java 关键字，不能当变量名
- 一般布尔值用 `boolean`，不是 `Boolean`

应该写成：

```java
boolean switched = false;
```

不过这题其实不需要额外的 `switched` 变量。

### 2. while 判断用了不会变化的变量

之前写过类似：

```java
while (headA != null || headB != null) {
    ...
}
```

但是循环里面移动的是 `nodea` 和 `nodeb`，不是 `headA` 和 `headB`。

所以 `headA`、`headB` 一直没变，循环条件也一直不变，最后就会超时。

循环条件应该围绕正在移动的指针：

```java
while (nodea != nodeb) {
    ...
}
```

### 3. 每次都写成 headA.next / headB.next

之前写过类似：

```java
nodea = headA.next;
nodeb = headB.next;
```

这个问题很关键。

`headA.next` 永远是头节点的下一个节点。

如果每次循环都赋值成它，就相当于一直回到同一个位置，没有继续往后走。

真正应该移动当前指针：

```java
nodea = nodea.next;
nodeb = nodeb.next;
```

### 4. 没想清楚失败时返回什么

这题返回类型是：

```java
ListNode
```

所以：

- 相交时，返回交点节点
- 不相交时，返回 `null`

双指针写法里不用单独判断失败。

如果不相交，两个指针最后会一起变成 `null`，然后循环结束，直接返回 `nodea` 就是返回 `null`。

## 正确思路：双指针

创建两个指针：

```java
ListNode nodea = headA;
ListNode nodeb = headB;
```

然后让它们一起走。

规则：

- `nodea` 走完 A 后，去 B 的开头
- `nodeb` 走完 B 后，去 A 的开头

这样两个指针最终走过的总距离一样：

```text
A长度 + B长度
B长度 + A长度
```

如果有交点，它们会在交点相遇。

如果没有交点，它们会一起走到 `null`。

## 正确代码

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode nodea = headA;
        ListNode nodeb = headB;

        while (nodea != nodeb) {
            nodea = nodea == null ? headB : nodea.next;
            nodeb = nodeb == null ? headA : nodeb.next;
        }

        return nodea;
    }
}
```

## 可以学到什么

### 1. 链表题经常比较的是节点本身

这题不能写：

```java
nodea.val == nodeb.val
```

应该比较节点对象：

```java
nodea == nodeb
```

因为相交的意思是同一个节点，不是值相等。

### 2. head 通常不要乱动

`headA` 和 `headB` 是链表入口。

很多时候应该用临时指针去走：

```java
ListNode nodea = headA;
```

然后移动 `nodea`，不要直接依赖 `headA.next` 反复跳。

### 3. 循环条件必须和循环里变化的变量有关

如果 `while` 判断的是 `headA`，但循环里没有改变 `headA`，就很容易死循环。

写循环时要检查：

```text
循环条件里的变量，循环里面有没有变化？
```

### 4. 双指针的本质是补齐长度差

两个链表长度可能不同。

让 A 指针走完 A 后去 B，让 B 指针走完 B 后去 A，本质是在让它们走同样的总长度。

这样长度差就被抵消了。

## 核心记忆

```text
Intersection of Two Linked Lists
= 找两个链表是否从某个节点开始共用同一段
= 比较节点对象，不比较 val
= 不相交返回 null
= 双指针走 A+B 和 B+A
```

