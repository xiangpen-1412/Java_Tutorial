# 543. Diameter of Binary Tree

> 难度：Easy  
> 主题：Binary Tree、DFS、后序遍历、树的高度

## 问题

给定一棵二叉树，返回树的直径。直径是任意两个节点之间最长路径的**边数**，这条路径不一定经过整棵树的根节点。

例如：

```text
        1
       / \
      2   3
     / \
    4   5
```

最长路径可以是：

```text
4 → 2 → 1 → 3
```

它经过 4 个节点，但只有 3 条边，所以答案是 `3`。

## 识别信号：父节点依赖左右子树结果

如果把某个节点 `node` 当成路径的转折点，那么经过它的最长路径由两部分组成：

```text
左子树最深路径 + 右子树最深路径
```

必须先知道左右子树的高度，才能计算经过当前节点的直径，因此适合使用后序遍历：

```text
左子树 → 右子树 → 当前节点
```

可以记成：

> 信息从孩子传给父亲，通常考虑后序遍历。

## 两个不同的值

这道题最重要的是区分：

### 1. 当前节点处的直径候选

路径可以同时连接左右两边：

```java
leftHeight + rightHeight
```

### 2. 返回给父节点的高度

父节点要继续向上连接时，当前节点只能提供一条单边路径，所以只能选择左右较高的一边：

```java
Math.max(leftHeight, rightHeight) + 1
```

不能把左右两边一起返回，否则路径向上延伸后会出现分叉。

## 标准写法

```java
class Solution {
    int maxDiameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        height(root);
        return maxDiameter;
    }

    private int height(TreeNode node) {
        if (node == null) {
            return 0;
        }

        int leftHeight = height(node.left);
        int rightHeight = height(node.right);

        maxDiameter = Math.max(
                maxDiameter,
                leftHeight + rightHeight
        );

        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```

## 为什么这里不用额外减一

`height()` 使用节点数量表示高度：

```text
null 的高度 = 0
叶子节点的高度 = 1
```

但在计算直径时，使用的是：

```java
height(node.left) + height(node.right)
```

例如：

```text
    1
   /
  2
```

节点 `1` 的左子树高度是 `1`，右子树高度是 `0`：

```text
直径 = 1 + 0 = 1 条边
```

因此这个定义恰好直接得到边数。

## 我的思维误区

- **把树的高度当成直径**：`height(root)` 只表示从根节点向下选择一边能走多远，直径则可能连接左右两边，也可能完全位于某棵子树中。
- **分别递归求当前、左子树和右子树的答案**：这种思路可以得到答案，但会反复计算子树高度。后序遍历可以在一次 DFS 中计算高度并更新直径。
- **忘记更新全局最大值**：只声明 `max` 并最终返回它是不够的，处理每个节点时都要用当前候选值更新它。
- **混淆返回值和全局答案**：全局直径可以连接左右两边；返回给父节点的高度只能选择一边。这是树上路径题最关键的区别。

## 复杂度

- 时间复杂度：`O(n)`，每个节点只处理一次。
- 空间复杂度：`O(h)`，来自递归调用栈；`h` 是树的高度。

## 一句话总结

> 求直径时左右两边可以连接：`leftHeight + rightHeight`；向父节点返回高度时只能选择一边：`max(leftHeight, rightHeight) + 1`。
