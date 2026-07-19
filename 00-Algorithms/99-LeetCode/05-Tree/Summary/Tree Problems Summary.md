# Tree 已做题目总结：递归契约、返回值与层序状态

树题的重点不是“看到树就递归”，而是先定义当前函数对一棵子树负责什么。递归 DFS 依赖子问题返回值，BFS 则用队列保存下一批待处理节点。

## 1. 已覆盖的题型

| 题目 | 题目类型 | 主要方法 | 当前函数/状态的含义 |
| --- | --- | --- | --- |
| [[01-101-Symmetric Tree]] | 镜像结构判断 | 成对递归 | 两棵子树是否互为镜像 |
| [[02-102-Binary Tree Level Order Traversal]] | 按层遍历 | BFS + queue size | 当前队列前部的一整层 |
| [[03-110-Balanced Binary Tree]] | 子树高度与全局性质 | 后序 DFS | 返回高度或不平衡信号 |
| [[04-112-Path Sum]] | 根到叶路径判断 | DFS + 剩余目标 | 当前路径还需要多少和 |
| [[05-98-Validate Binary Search Tree]] | 全局范围约束 | DFS + 上下界 | 当前节点允许的数值范围 |
| [[00-Recursion Summary]] | 递归基础 | base case + recursive step | 明确函数契约 |

## 2. 写递归前先定义函数契约

不要只写“递归左右子树”，而要补完整：

```text
函数输入是什么？
函数返回值代表什么？
空节点应该返回什么？
如何使用左右子树返回值组成当前答案？
```

当前题目的契约示例：

```text
101：isMirror(left, right) 表示两棵树是否镜像
110：height(node) 返回子树高度，或返回不平衡信号
112：dfs(node, remain) 表示是否存在满足剩余和的根到叶路径
98：validate(node, low, high) 表示整棵子树是否落在合法范围内
```

## 3. 对称树：两个位置必须交叉比较

镜像不是比较：

```text
left.left 与 right.left
```

而是交叉比较：

```text
left.left  ↔ right.right
left.right ↔ right.left
```

成对递归的 base case：

```text
两个都 null → 镜像
只有一个 null → 不镜像
值不同 → 不镜像
```

## 4. 层序遍历：queue.size() 固定当前层

BFS 队列在处理过程中会不断加入下一层。每轮开始时先记录：

```java
int levelSize = queue.size();
```

接下来恰好处理 `levelSize` 个节点，它们属于同一层；新加入的孩子留到下一轮。

识别信号：

```text
按层返回
每层单独统计
最浅深度或逐层扩散
```

## 5. 平衡树：后序返回子树信息

平衡与否取决于左右子树高度差，并且左右子树本身也必须平衡。因此先获得左右结果，再判断当前节点，属于后序思维：

```text
左子树结果
右子树结果
组合成当前结果
```

如果发现不平衡，可以向上传递特殊信号，避免重复计算高度。

## 6. Path Sum：终点必须是真正叶子

叶子节点是：

```java
node.left == null && node.right == null
```

不能把递归走到 `null` 当作路径结束，否则可能在非叶节点缺失的一侧错误判断成功。

状态可以使用：

```text
remain = remain - node.val
```

只有到叶子时才检查剩余目标是否满足。

## 7. Validate BST：约束来自所有祖先

只判断：

```text
node.left.val < node.val < node.right.val
```

不够，因为更深节点仍可能违反祖先限制。

正确模型是给每棵子树传递允许范围：

```text
左子树范围：(low, node.val)
右子树范围：(node.val, high)
```

边界使用 `long`，避免节点值恰好为 `Integer.MIN_VALUE` 或 `Integer.MAX_VALUE` 时无法再设置更外层哨兵。

## 8. DFS 与 BFS 的选择

| 题目目标 | 优先考虑 |
| --- | --- |
| 结构是否满足某性质 | DFS |
| 需要子树高度、和、范围等返回信息 | DFS |
| 同时比较两个对称位置 | 成对 DFS |
| 明确要求逐层输出或统计 | BFS |

## 9. 常见错误

```text
没有定义递归返回值含义，左右结果无法组合；
空节点 base case 与题目含义不一致；
把 null 当成叶子；
层序遍历在 for 循环中直接使用不断变化的 queue.size()；
平衡树重复计算高度，退化为 O(n²)；
BST 只比较父子节点，没有传递祖先范围；
对称树比较了同方向孩子，而不是交叉位置。
```

## 10. 记忆主线

```text
递归先写契约，再写 base case。
需要左右子树信息后再判断当前节点 → 后序 DFS。
需要祖先约束 → 参数向下传。
需要子树结果 → 返回值向上传。
需要逐层处理 → BFS + 固定 levelSize。
```

