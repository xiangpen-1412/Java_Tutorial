# 124. Binary Tree Maximum Path Sum

> 难度：Hard  
> 主题：Binary Tree、DFS、后序遍历、树上路径

## 问题

给定一棵二叉树，在树中选择一条**非空路径**，返回路径上所有节点值的最大总和。

一条合法路径需要满足：

- 相邻节点之间有边相连；
- 每个节点最多出现一次；
- 可以从任意节点开始，在任意节点结束；
- 不要求经过根节点；
- 必须是一条连续的线，不能分叉。

例如：

```text
          -10
          /  \
         9    20
             /  \
            15   7
```

最大路径是：

```text
15 → 20 → 7
```

路径和为：

```text
15 + 20 + 7 = 42
```

它没有经过根节点 `-10`，仍然是合法答案。

## 负数为什么可以不选

路径可以在任意节点开始或结束，所以某个子树提供的路径和如果小于 `0`，连接它只会让当前路径变小，可以把它当成 `0`，表示不选择这一边：

```java
Math.max(0, childContribution)
```

但整条路径不能为空，因此全局最大值不能初始化为 `0`。如果所有节点都是负数，例如：

```text
      -10
      /  \
    -20  -3
```

答案应该是单个节点 `-3`，不是 `0`。

## 两个不同的值

和 543 题一样，这里必须区分：

### 1. 经过当前节点的完整路径

当前路径可以连接左边和右边：

```java
int currentPath =
        node.val
        + Math.max(0, left)
        + Math.max(0, right);
```

这一个式子覆盖了四种选择：

```text
只选 node
node + 左边
node + 右边
node + 左边 + 右边
```

用它更新整棵树的最大路径和：

```java
max = Math.max(max, currentPath);
```

### 2. 返回给父节点的单边贡献

如果当前结果还要交给父节点继续连接，就不能同时携带左右两边，否则会在当前节点产生分叉。

因此返回值只能是：

```text
node
node + 左边
node + 右边
```

简化后是：

```java
return node.val + Math.max(0, Math.max(left, right));
```

## 为什么不能返回完整路径

考虑这个测试用例：

```text
                 5
               /   \
              4     8
             /     / \
           11     13  4
          /  \          \
         7    2          1
```

在节点 `11` 处，完整路径可以是：

```text
7 → 11 → 2
```

但如果把这条完整路径返回给节点 `4`，再继续连接到 `5`，就会形成：

```text
    7
     \
     11 → 4 → 5
     /
    2
```

这不是一条线，而是在 `11` 处分叉。也就是说：

> 左右都选的结果只能在当前节点作为完整答案候选，不能继续向父节点传递。

## 标准写法

```java
class Solution {
    int max = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        contribution(root);
        return max;
    }

    private int contribution(TreeNode node) {
        if (node == null) {
            return 0;
        }

        int left = contribution(node.left);
        int right = contribution(node.right);

        int currentPath =
                node.val
                + Math.max(0, left)
                + Math.max(0, right);

        max = Math.max(max, currentPath);

        return node.val + Math.max(0, Math.max(left, right));
    }
}
```

## 与 543 题的联系

两道题的结构几乎相同：

| 含义 | 543 二叉树直径 | 124 最大路径和 |
|---|---|---|
| 子树返回什么 | 单边高度 | 单边最大贡献 |
| 当前节点如何形成完整候选 | `leftHeight + rightHeight` | `node.val + max(0,left) + max(0,right)` |
| 返回父节点时 | 左右只能选一边 | 左右只能选一边 |
| 全局答案 | 最大边数 | 最大节点值总和 |

共同模式是：

```text
完整答案可以在当前节点连接左右两边；
向父节点返回时只能保留一边。
```

## 我的思维误区

- **把 `currentPath` 直接返回给父节点**：`currentPath` 可能已经同时选择左右两边，父节点再连接它就会制造分叉，因此会算出不存在的路径。
- **一开始把四种情况写成多层 `Math.max()`**：因为每种情况都包含 `node.val`，可以把负贡献丢弃，统一写成 `node.val + max(0,left) + max(0,right)`。
- **没有区分“当前完整答案”和“向上贡献”**：前者允许同时使用左右子树，后者必须保持单链结构，只能选其中一边。
- **将全局最大值初始化为 `0`**：当整棵树全是负数时，`0` 代表空路径，不符合题目要求；应允许某个负数节点成为答案。

## 复杂度

- 时间复杂度：`O(n)`，每个节点只处理一次。
- 空间复杂度：`O(h)`，来自递归调用栈；`h` 是树的高度。

## 一句话总结

> 当前节点计算完整路径时左右都可以选；返回父节点时只能选贡献更大的一边，负贡献则不选。
