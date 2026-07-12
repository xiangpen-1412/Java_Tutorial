# 98. Validate Binary Search Tree

## 题目在问什么

给一棵二叉树，判断它是不是合法的二叉搜索树 BST。

BST 的规则是：

```text
左子树所有节点都必须 < 当前节点
右子树所有节点都必须 > 当前节点
左右子树自己也必须是 BST
```

注意是：

```text
strictly less / strictly greater
```

所以相等也不行。

## 不能只比较父子节点

错误思路是只判断：

```text
左孩子 < 当前节点
右孩子 > 当前节点
```

这样会漏掉祖先节点带来的限制。

比如：

```text
      5
     / \
    4   6
       / \
      3   7
```

局部看：

```text
4 < 5
6 > 5
3 < 6
7 > 6
```

每个父子关系都对。

但是 `3` 在 `5` 的右子树里，所以它必须：

```text
3 > 5
```

可实际上：

```text
3 < 5
```

所以这不是合法 BST。

## 正确思路：带上下界递归

每个节点都有一个合法范围：

```text
lower < node.val < upper
```

根节点一开始没有限制，可以看成：

```text
(-∞, +∞)
```

如果往左走：

```text
upper 变成当前节点的值
```

因为左子树所有节点都必须小于当前节点。

如果往右走：

```text
lower 变成当前节点的值
```

因为右子树所有节点都必须大于当前节点。

## 代码

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValid(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    public boolean isValid(TreeNode root, long lower, long upper) {
        if (root == null) {
            return true;
        }

        if (root.val <= lower || root.val >= upper) {
            return false;
        }

        return isValid(root.left, lower, root.val)
            && isValid(root.right, root.val, upper);
    }
}
```

## 为什么边界要用 long

不要用：

```java
Integer.MIN_VALUE
Integer.MAX_VALUE
```

当初始边界配合严格比较时，会卡在极值测试。

比如：

```text
root = [2147483647]
```

也就是：

```java
root.val == Integer.MAX_VALUE
```

如果初始上界是：

```java
Integer.MAX_VALUE
```

判断会变成：

```java
root.val < Integer.MAX_VALUE
```

也就是：

```text
2147483647 < 2147483647
```

这是 `false`。

但单个节点本身是合法 BST，所以这里会误判。

所以要用比 `int` 范围更大的边界：

```java
Long.MIN_VALUE
Long.MAX_VALUE
```

## Java 最大值和最小值

常用写法：

```java
Integer.MAX_VALUE
Integer.MIN_VALUE

Long.MAX_VALUE
Long.MIN_VALUE

Short.MAX_VALUE
Short.MIN_VALUE

Byte.MAX_VALUE
Byte.MIN_VALUE
```

这题因为节点值是 `int`，边界最好用 `long`。

## 核心记忆

BST 不是只看父子关系。

要记成：

```text
每个节点都要满足祖先传下来的范围：
lower < node.val < upper
```

并且边界要用 `long`，避免 `Integer.MIN_VALUE` 和 `Integer.MAX_VALUE` 卡边界。
