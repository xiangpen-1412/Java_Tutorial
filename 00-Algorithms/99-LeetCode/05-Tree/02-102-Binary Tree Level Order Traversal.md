# 102. Binary Tree Level Order Traversal

## 题目

给一棵二叉树，按层从左到右返回节点值。

也就是：

```text
第一层放一组
第二层放一组
第三层放一组
```

题目要返回每一层的结果列表。
## 这题为什么用 Queue

`Binary Tree Level Order Traversal` 要求按层输出：

```text
第 1 层
第 2 层
第 3 层
...
```

这种题属于 BFS，通常用 `Queue`。

记忆：

```text
按层扫，用 Queue。
往下钻，用 Recursion。
```

## 为什么这次代码 Accepted 但比较慢

原来的思路是：

```text
deque 负责 BFS
temp 负责记录当前层
```

这个方向能做出来，但会有额外开销。

慢的主要原因是每一层都做了额外复制：

```java
temp.addAll(deque);
```

这会把 `deque` 里的节点复制到 `temp`。

然后又遍历了一遍 `temp`：

```java
for (TreeNode cur : temp) {
    list.add(cur.val);
}
```

所以每一层除了正常 BFS 之外，还多做了：

```text
复制当前队列
遍历复制出来的队列
```

代码能通过，但不够轻。

## 层序遍历的关键技巧

层序遍历不需要两个队列。

每一轮开始时，记录当前队列大小：

```java
int size = queue.size();
```

这个 `size` 就是当前这一层的节点数量。

然后只处理这 `size` 个节点。

处理过程中，新加入队列的孩子节点属于下一层，不应该在当前层处理。

## 为什么 queue.size() 能区分层

例如：

```text
        3
       / \
      9   20
         /  \
        15   7
```

一开始：

```text
queue = [3]
```

此时：

```java
size = queue.size(); // 1
```

说明当前层只有 1 个节点。

处理 `3` 时，把它的孩子加入队列：

```text
queue = [9, 20]
```

下一轮开始：

```java
size = queue.size(); // 2
```

说明当前层有 2 个节点。

只处理 `9` 和 `20`。

处理过程中加入的 `15` 和 `7` 会进入队列：

```text
queue = [15, 7]
```

但它们属于下一层，因为这一轮的 `size` 已经固定是 2。

## 更推荐的思路

不要提前单独把 root 加进答案。

让 BFS 循环统一处理每一层，包括 root 那一层。

整体结构是：

```text
如果 root 是 null，返回空结果

创建 queue
把 root 放入 queue

while queue 不为空：
    当前层 size = queue.size()
    创建当前层 list

    重复 size 次：
        取出队首节点
        把节点值加入当前层 list
        如果左孩子不为空，加入 queue
        如果右孩子不为空，加入 queue

    把当前层 list 加入结果

返回结果
```

## 一句话总结

层序遍历用一个队列就够了；每一层开始时的 `queue.size()` 就是当前层的节点数量。
