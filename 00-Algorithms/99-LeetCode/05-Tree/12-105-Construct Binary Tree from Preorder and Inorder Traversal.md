# 105. Construct Binary Tree from Preorder and Inorder Traversal

> 难度：Medium  
> 主题：Binary Tree、前序遍历、中序遍历、递归分治、HashMap

## 问题

给定同一棵二叉树的前序遍历 `preorder` 和中序遍历 `inorder`，重建并返回这棵树。所有节点值互不相同，两个数组长度相同、元素集合相同，并且一定对应一棵合法二叉树。

决定性条件是：

```text
preorder：根 → 左 → 右
inorder： 左 → 根 → 右
```

前序遍历可以确定当前根节点，中序遍历可以确定根节点左右两侧分别有哪些节点。单独使用其中任意一个遍历序列，通常不能唯一确定一棵普通二叉树。

## 核心收获

- **识别信号**：题目同时给出“前序 + 中序”且节点值唯一，应想到用前序确定根、用中序切分左右子树。
- **核心转换**：把“重建整棵树”转成两个结构相同的子问题——重建左子树和右子树。
- **通用方法**：用 `HashMap<节点值, inorder 下标>` 消除重复查找；递归中只传中序区间，不复制数组。

## 示例

```text
preorder = [3, 9, 20, 15, 7]
inorder  = [9, 3, 15, 20, 7]
```

前序第一个元素 `3` 是根节点，它在中序中的位置把数组分成：

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
build(preorder, inLeft, inRight)
```

负责根据当前尚未消费的前序节点，构造中序闭区间 `[inLeft, inRight]` 对应的子树，并返回其根节点。

状态含义：

```text
preorderIndex
    指向 preorder 中下一个待处理的根节点

[inLeft, inRight]
    限定当前递归只能构造 inorder 的这一段
```

空区间是空子树：

```java
if (inLeft > inRight) {
    return null;
}
```

## 为什么只传 inorder 区间就够了

`preorderIndex` 按照前序遍历的顺序持续向右移动：

```text
当前根 → 左子树的所有节点 → 右子树的所有节点
```

取得当前根后，中序位置可以确定左右子树的范围。接下来先递归构造左子树，指针自然会依次消费完左子树节点；之后再构造右子树。因此不需要额外传递或切割 `preorder` 区间。

```java
int rootVal = preorder[preorderIndex++];
int rootIndex = inorderIndexMap.get(rootVal);

root.left = build(preorder, inLeft, rootIndex - 1);
root.right = build(preorder, rootIndex + 1, inRight);
```

左右递归顺序不能交换，因为正向读取 `preorder` 时的顺序是：

```text
根 → 左 → 右
```

## 结构骨架

```java
Map<Integer, Integer> inorderIndexMap;
int preorderIndex;

TreeNode build(int[] preorder, int inLeft, int inRight) {
    if (inLeft > inRight) return null;

    int rootVal = preorder[preorderIndex++];
    TreeNode root = new TreeNode(rootVal);
    int rootIndex = inorderIndexMap.get(rootVal);

    root.left = build(preorder, inLeft, rootIndex - 1);
    root.right = build(preorder, rootIndex + 1, inRight);
    return root;
}
```

入口调用前要完成：

```text
1. preorderIndex = 0
2. 建立 inorder 的“值 → 下标”映射
3. 调用 build(preorder, 0, inorder.length - 1)
```

## 复杂度取舍

### 使用数组切片

最初的实现通过 `Arrays.copyOfRange()` 分割左右子树，并在每次递归中线性扫描 `inorder` 查找根节点。

对于退化成链表的树，处理规模依次为：

```text
n + (n - 1) + (n - 2) + ... + 1
```

因此最坏时间为 `O(n²)`，并且会创建大量临时数组，增加内存和垃圾回收压力。

### 使用 HashMap 和区间

- 建立下标表：`O(n)`
- 每个节点只创建和定位一次：`O(n)`
- HashMap：`O(n)`
- 递归栈：`O(h)`，其中 `h` 是树高

整体时间复杂度为 `O(n)`，额外空间为 `O(n)`。

## 变种与注意点

- 节点值唯一很重要，否则 `值 → 唯一中序下标` 的映射不成立。
- 使用闭区间时，左右子树分别是 `[inLeft, rootIndex - 1]` 和 `[rootIndex + 1, inRight]`。
- 如果改用左闭右开区间，base case 和边界必须统一改写，不能混用两种定义。
- 相关变种：[[13-106-Construct Binary Tree from Inorder and Postorder Traversal]]。后序从末尾读取时，递归方向需要改成先右后左。

## 我的思维误区

- **只处理长度为 1，没有处理长度为 0**：叶子节点继续递归时会产生空子树。如果在检查空数组前访问 `preorder[0]`，会触发 `ArrayIndexOutOfBoundsException`。真正通用的 base case 是“当前区间为空”。
- **把数组切片理解为单纯的语法便利**：`Arrays.copyOfRange()` 会创建并复制新数组；递归中反复切片会改变算法复杂度，不只是代码风格问题。
- **每层重新扫描 inorder**：根节点位置会被反复查找。节点值唯一时，应先建立 HashMap，把每次定位从 `O(n)` 降为 `O(1)`。

## 一句话总结

> 前序的第一个未消费元素确定根，中序位置确定左右范围；正向读取前序时按照“根、左、右”递归，即可用 HashMap 和区间在 `O(n)` 时间内重建二叉树。
