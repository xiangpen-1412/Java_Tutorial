# 02. Permutations

## 题目

给一个不含重复数字的数组 `nums`，返回它的所有排列。

排列的意思是：

```text
把所有数字按不同顺序排出来。
```

顺序重要，所以 `[1,2,3]` 和 `[2,1,3]` 是不同结果。
## 题号

```text
LeetCode 46. Permutations
```

## 1. 这题和 Backtracking 的关系

这题是非常典型的 backtracking。

它要你找：

```text
一个数组的所有排列。
```

比如：

```text
nums = [1, 2, 3]
```

答案包括：

```text
[1,2,3]
[1,3,2]
[2,1,3]
[2,3,1]
[3,1,2]
[3,2,1]
```

本质上是在问：

```text
每一个位置，应该放哪个还没有用过的数字？
```

## 2. Permutations 的核心特点

全排列里：

```text
顺序重要。
```

也就是说：

```text
[1,2,3] 和 [2,1,3] 是不同答案。
```

所以这题不能像 subsets 那样用 `start` 限制只能往后选。

如果用了：

```text
dfs(i + 1)
```

那就只能从左往右选，容易只得到：

```text
[1,2,3]
```

却得不到：

```text
[2,1,3]
[3,1,2]
```

## 3. 这题不关心 start，关心 used

Permutations 每一层都应该从头扫描 `nums`。

但是已经放进 `path` 的数字不能再用。

所以它关心的是：

```text
这个数字有没有被用过？
```

而不是：

```text
下一层从哪个 index 开始？
```

因此通常需要：

```text
path = 当前正在拼的排列
used / selected = 哪些数字已经用过
```

## 4. 选择树

```text
[]
├── [1]
│   ├── [1,2]
│   │   └── [1,2,3]
│   └── [1,3]
│       └── [1,3,2]
├── [2]
│   ├── [2,1]
│   │   └── [2,1,3]
│   └── [2,3]
│       └── [2,3,1]
└── [3]
    ├── [3,1]
    │   └── [3,1,2]
    └── [3,2]
        └── [3,2,1]
```

只有当 `path` 长度等于 `nums.length` 时，才是一个完整答案。

## 5. Pseudo Code

```text
result = []
path = []
used = []

function dfs():
    if path.length == nums.length:
        result.add(copy(path))
        return

    for i from 0 to nums.length - 1:
        if nums[i] already used:
            continue

        path.add(nums[i])
        mark nums[i] as used

        dfs()

        unmark nums[i] as used
        path.removeLast()
```

## 6. 从 [1,2,3] 开始回退时发生了什么？

假设已经得到第一个完整排列：

```text
path = [1,2,3]
```

这时加入答案：

```text
result.add([1,2,3])
```

然后 return 回上一层。

上一层刚刚做的选择是：

```text
add 3
```

所以这一层撤销：

```text
remove 3
```

状态变成：

```text
path = [1,2]
```

然后 `[1,2]` 这一层发现没有别的数字可以选，于是它结束，返回上一层。

上一层刚刚做的选择是：

```text
add 2
```

所以撤销：

```text
remove 2
```

状态变成：

```text
path = [1]
```

现在程序还在 `[1]` 这一层的 for loop 里。

它之前试过 `2`，接下来可以试 `3`。

于是：

```text
path = [1,3]
```

再进入下一层，最后得到：

```text
[1,3,2]
```

所以过程是：

```text
[1,2,3] 加入答案
撤销 3 -> [1,2]
[1,2] 没得选了
撤销 2 -> [1]
[1] 继续试 3
得到 [1,3,2]
```

重点：

```text
不是直接撤销 1。
```

只有当所有以 `1` 开头的排列都试完以后，才会撤销 `1`。

也就是：

```text
[1,2,3]
[1,3,2]
```

都完成以后，才会：

```text
path = [1] -> []
```

然后开始试以 `2` 开头的排列。

## 7. 这题最重要的变量

```text
result = 所有答案
path = 当前正在构造的排列
used / selected = 哪些数字已经放进 path
```

## 8. 记忆方式

```text
Permutations:
path 满了才是答案。
每层从头选 nums。
已经 used 的数字跳过。
```

