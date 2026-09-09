# 112. Path Sum

## 题目在问什么

给一棵二叉树 `root` 和一个整数 `targetSum`。

判断这棵树里有没有一条路径满足：

```text
从 root 出发
一直走到 leaf
路径上的节点值加起来 == targetSum
```

注意题目要求的是：

```text
root-to-leaf path
```

所以必须走到叶子节点才算一条完整路径。

## 最重要的点：leaf 不是 null

这题最容易错的地方是把 `null` 当成叶子节点。

但是：

```text
null 不是叶子节点
```

叶子节点是一个真实存在的节点，并且：

```java
root.left == null && root.right == null
```

比如：

```text
root = [1,2]
targetSum = 1
```

树是：

```text
  1
 /
2
```

虽然从 `1` 往右会走到 `null`，而且当前和是 `1`，但这不算合法路径。

因为 `1` 不是叶子节点，它还有左孩子 `2`。

合法路径必须是：

```text
1 -> 2
```

所以这个例子的答案是：

```text
false
```

## DFS 思路

用 DFS 从根节点往下走，同时记录当前路径和。

每到一个节点：

```text
currentSum += root.val
```

如果当前节点是叶子节点，再判断：

```text
currentSum == targetSum
```

如果不是叶子节点，就继续往左子树和右子树递归。

只要左边或者右边有一条路径满足，就返回 `true`。

## 代码

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) {
            return false;
        }

        return dfsCompare(root, 0, targetSum);
    }

    public boolean dfsCompare(TreeNode root, int currentSum, int targetSum) {
        if (root.left == null && root.right == null) {
            return currentSum + root.val == targetSum;
        }

        boolean left = false;
        if (root.left != null) {
            left = dfsCompare(root.left, currentSum + root.val, targetSum);
        }

        boolean right = false;
        if (root.right != null) {
            right = dfsCompare(root.right, currentSum + root.val, targetSum);
        }

        return left || right;
    }
}
```

## 为什么这里不用判断 `dfsCompare(root == null)`

这版代码在递归前已经判断了：

```java
if (root.left != null)
if (root.right != null)
```

所以不会把 `null` 传进 `dfsCompare`。

如果写成直接递归：

```java
dfsCompare(root.left, ...)
dfsCompare(root.right, ...)
```

那 `dfsCompare` 开头就必须加：

```java
if (root == null) {
    return false;
}
```

否则访问：

```java
root.left
```

会空指针。

## 核心记忆

这题的判断位置是：

```text
到叶子节点时，比较路径和
```

不是：

```text
走到 null 时，比较路径和
```

一句话记：

```text
leaf 是真实节点，条件是 left == null && right == null。
```
