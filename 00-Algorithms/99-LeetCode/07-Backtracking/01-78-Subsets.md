# 01. Subsets

## 题目

给一个整数数组 `nums`，返回它的所有子集。

子集的意思是：

```text
每个数字可以选，也可以不选。
```

顺序不重要，不能返回重复子集。
## 题号

```text
LeetCode 78. Subsets
```

## 1. 这题和 Backtracking 的关系

这题是 backtracking 的入门题。

它要你找：

```text
一个数组的所有子集。
```

比如：

```text
nums = [1, 2, 3]
```

答案包括：

```text
[]
[1]
[2]
[3]
[1,2]
[1,3]
[2,3]
[1,2,3]
```

本质上是在一棵选择树上搜索：

```text
当前 path 是一个子集。
我继续往里面尝试加后面的数字。
```

## 2. Subsets 的核心特点

子集问题里：

```text
顺序不重要。
```

也就是说：

```text
[1,2] 和 [2,1] 是同一个子集。
```

所以为了避免重复，选完一个数以后，下一层只能继续往后选。

这就是为什么这题需要 `start`。

```text
start = 下一层从哪个 index 开始选
```

如果现在：

```text
path = [1]
```

下一层只能从：

```text
[2, 3]
```

里面继续选，不能回头再选 `1`。

## 3. 选择树

```text
[]
├── [1]
│   ├── [1,2]
│   │   └── [1,2,3]
│   └── [1,3]
├── [2]
│   └── [2,3]
└── [3]
```

注意：

```text
每一个节点都是一个合法答案。
```

所以 subsets 通常是一进入递归函数，就保存当前 `path`。

## 4. Pseudo Code

```text
result = []
path = []

function dfs(start):
    result.add(copy(path))

    for i from start to nums.length - 1:
        path.add(nums[i])
        dfs(i + 1)
        path.removeLast()
```

## 5. 为什么可以没有明显的结束条件？

递归一定要能停。

但是它不一定非要写成：

```text
if (...) return
```

在 subsets 里，结束条件藏在 for loop 里面。

当：

```text
start == nums.length
```

for loop 没有任何东西可以选。

函数自然结束，然后返回上一层。

所以这题的停止方式是：

```text
没有更多数字可以选了，函数自然 return。
```

也可以写成显式版本：

```text
function dfs(start):
    result.add(copy(path))

    if start == nums.length:
        return

    for i from start to nums.length - 1:
        path.add(nums[i])
        dfs(i + 1)
        path.removeLast()
```

## 6. 这题最重要的变量

```text
result = 所有答案
path = 当前正在构造的子集
start = 下一层从哪里开始选
```

## 7. 记忆方式

```text
Subsets:
每个 path 都是答案。
用 start 控制只能往后选。
```

