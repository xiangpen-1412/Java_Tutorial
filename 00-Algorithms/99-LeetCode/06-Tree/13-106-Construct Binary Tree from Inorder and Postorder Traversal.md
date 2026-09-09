# 106. Construct Binary Tree from Inorder and Postorder Traversal

> 难度：Medium  
> 主题：Binary Tree、中序遍历、后序遍历、递归分治、HashMap

## 问题

给定同一棵二叉树的中序遍历 `inorder` 和后序遍历 `postorder`，重建并返回这棵树。所有节点值互不相同，两个数组长度相同、元素集合相同，并且一定对应一棵合法二叉树。

关键遍历顺序是：

```text
inorder：  左 → 根 → 右
postorder：左 → 右 → 根
```

后序遍历的最后一个未消费元素是当前根节点；根节点在中序遍历中的位置则确定左右子树的范围。

## 核心收获

- **识别信号**：题目同时给出“中序 + 后序”且节点值唯一，应想到从后序末尾取根，再用中序切分左右子树。
- **核心转换**：把后序数组从后向前读取，其顺序会从“左、右、根”反转为“根、右、左”。
- **通用方法**：用一个递减的 `postorderIndex` 消费根节点，用 HashMap 定位其中序下标，递归只传中序范围。

## 示例

```text
inorder   = [9, 3, 15, 20, 7]
postorder = [9, 15, 7, 20, 3]
```

后序最后一个元素 `3` 是根节点，它在中序中的位置把数组分成：

```text
inorder = [9] 3 [15, 20, 7]
          左  根      右
```

对应的树：

```text
        3
       / \
      9   20
         /  \
        15   7
```

## 递归契约

```text
build(postorder, inLeft, inRight)
```

负责根据当前尚未消费的后序节点，构造中序闭区间 `[inLeft, inRight]` 对应的子树，并返回其根节点。

```text
postorderIndex
    指向 postorder 中下一个待处理的根节点

[inLeft, inRight]
    限定当前递归只能构造 inorder 的这一段
```

空区间表示空子树：

```java
if (inLeft > inRight) {
    return null;
}
```

## 为什么必须先构造右子树

原始后序遍历是：

```text
左 → 右 → 根
```

但算法从数组末尾向前读取：

```text
根 → 右 → 左
```

因此取得根节点后，`postorderIndex` 下一个指向的是右子树的根，而不是左子树的根：

```java
int rootVal = postorder[postorderIndex--];
int rootIndex = inorderIndexMap.get(rootVal);

root.right = build(postorder, rootIndex + 1, inRight);
root.left = build(postorder, inLeft, rootIndex - 1);
```

如果交换这两个递归调用，左子树会错误地消费本应属于右子树的节点。

## 结构骨架

```java
Map<Integer, Integer> inorderIndexMap;
int postorderIndex;

TreeNode build(int[] postorder, int inLeft, int inRight) {
    if (inLeft > inRight) return null;

    int rootVal = postorder[postorderIndex--];
    TreeNode root = new TreeNode(rootVal);
    int rootIndex = inorderIndexMap.get(rootVal);

    root.right = build(postorder, rootIndex + 1, inRight);
    root.left = build(postorder, inLeft, rootIndex - 1);
    return root;
}
```

入口调用前要完成：

```text
1. postorderIndex = postorder.length - 1
2. 建立 inorder 的“值 → 下标”映射
3. 调用 build(postorder, 0, inorder.length - 1)
```

## 与前序版本的统一理解

| 组合 | 根的位置 | 指针移动 | 递归顺序 |
| --- | --- | --- | --- |
| 前序 + 中序 | 前序第一个未消费元素 | 从左向右 | 先左，后右 |
| 中序 + 后序 | 后序最后一个未消费元素 | 从右向左 | 先右，后左 |

两题真正共用的部分是：

```text
遍历序列负责确定当前根；
inorder 负责确定左右子树的边界；
HashMap 负责 O(1) 定位根节点；
空的 inorder 区间负责终止递归。
```

前序版本见：[[12-105-Construct Binary Tree from Preorder and Inorder Traversal]]。

## 复杂度

- 建立中序下标表：`O(n)`
- 每个节点只创建和定位一次：`O(n)`
- HashMap：`O(n)`
- 递归栈：`O(h)`，其中 `h` 是树高

整体时间复杂度为 `O(n)`，额外空间为 `O(n)`。平衡树的递归深度为 `O(log n)`，退化链状树的递归深度为 `O(n)`。

## 变种与注意点

- 节点值唯一是 HashMap 唯一定位的前提。
- 使用闭区间时，左子树必须是 `[inLeft, rootIndex - 1]`，不能再次包含当前根。
- 右子树是 `[rootIndex + 1, inRight]`。
- 每次递归的区间必须严格缩小，否则全局后序指针会持续递减并最终越界。
- 反向遍历序列时，不只指针方向改变，左右递归顺序也要随之改变。

## 我的思维误区

- **左子树区间写成 `[start, rootIndex]`**：这个区间再次包含了当前根，子问题没有正确排除已经处理的节点。继续递归后，`postorderIndex` 最终变成负数并产生 `ArrayIndexOutOfBoundsException`。闭区间切分时必须写成 `[start, rootIndex - 1]`。
- **容易沿用前序题的“先左后右”习惯**：这里从后序末尾反向消费节点，实际顺序是“根、右、左”，所以必须先递归右子树。

## 一句话总结

> 后序的最后一个未消费元素确定根，中序位置确定左右范围；从后向前读取后序时按照“根、右、左”递归，即可在 `O(n)` 时间内重建二叉树。
