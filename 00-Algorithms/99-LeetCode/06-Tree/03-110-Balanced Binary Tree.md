# 110. Balanced Binary Tree

## 题目在问什么

判断一棵二叉树是不是平衡二叉树。

平衡二叉树的定义是：

```text
对每一个节点来说，它的左子树高度和右子树高度差不能超过 1
```

注意重点是：

```text
每一个节点
```

不是只检查根节点。

## 例子

```text
root = [1,2,2,3,null,null,3,4,null,null,4]
```

树长这样：

```text
        1
       / \
      2   2
     /     \
    3       3
   /         \
  4           4
```

根节点 `1` 看起来是平衡的：

```text
左子树高度 = 3
右子树高度 = 3
高度差 = 0
```

但是左边的 `2` 不是平衡的：

```text
      2
     /
    3
   /
  4
```

它的：

```text
左子树高度 = 2
右子树高度 = 0
高度差 = 2
```

所以整棵树不是平衡二叉树。

## 容易写错的地方

错误思路：

```java
public boolean isBalanced(TreeNode root) {
    if (root == null) {
        return true;
    }

    return check(root.left) && check(root.right);
}
```

问题是：

```text
check(root.left) 只检查 root.left 这个节点自己
check(root.right) 只检查 root.right 这个节点自己
```

如果 `check()` 里面没有继续递归检查更下面的节点，就会漏掉深层不平衡的情况。

比如：

```text
        1
       / \
      2   2
     /     \
    3       3
   /         \
  4           4
```

根节点左右高度一样，但下面的 `2` 已经不平衡了。

所以不能只看根节点，也不能只看根节点的左右孩子。

## 正确思路

对任意一个节点 `root`，它要平衡，需要同时满足三个条件：

```text
1. root 自己左右子树高度差 <= 1
2. root.left 是平衡的
3. root.right 是平衡的
```

所以递归结构是：

```text
当前节点平衡
并且左子树平衡
并且右子树平衡
```

也就是：

```java
return Math.abs(leftDepth - rightDepth) <= 1
    && check(root.left)
    && check(root.right);
```

## 代码

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return check(root);
    }

    public boolean check(TreeNode root) {
        if (root == null) {
            return true;
        }

        int leftDepth = getDepth(root.left);
        int rightDepth = getDepth(root.right);

        if (Math.abs(leftDepth - rightDepth) > 1) {
            return false;
        }

        return check(root.left) && check(root.right);
    }

    public int getDepth(TreeNode node) {
        if (node == null) {
            return 0;
        }

        return Math.max(getDepth(node.left), getDepth(node.right)) + 1;
    }
}
```

## 为什么 `getDepth(null)` 要返回 0

空树没有节点，所以高度是 `0`。

这样递归时就不用单独判断：

```java
if (root.left != null)
```

可以直接写：

```java
int leftDepth = getDepth(root.left);
int rightDepth = getDepth(root.right);
```

如果某一边是 `null`，高度自然就是 `0`。

## 核心记忆

这题不是问：

```text
root 左右高度差是否 <= 1
```

而是问：

```text
每一个节点的左右高度差是否都 <= 1
```

所以看到这种题，要想到：

```text
当前节点检查一次
左子树递归检查
右子树递归检查
```

树的题里，如果题目说“整棵树满足某个性质”，通常都要递归检查左右子树。
