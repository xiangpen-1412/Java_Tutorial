# 介绍 Backtracking

## 1. Backtracking 是什么？

Backtracking 不是某一个数据结构。

它是一种搜索思路：

```text
在一棵选择树上不断尝试。
做一个选择，进入下一层。
下一层结束以后，撤销刚才的选择。
然后换另一个选择。
```

可以记成：

```text
Backtracking = DFS + 做选择 + 撤销选择
```

它经常和这些概念一起出现：

```text
Tree
DFS
Recursion
Stack
```

输入可能是 array，但 backtracking 本身不是 array algorithm。

## 2. 它和 Recursion 的关系

递归负责：

```text
进入下一层选择。
```

Backtracking 多出来的是：

```text
递归回来以后，撤销刚刚做的选择。
```

所以：

```text
Recursion = 函数调用自己
Backtracking = 用递归试选择，试完回来撤销选择
```

不是所有 recursion 都是 backtracking。

比如阶乘是 recursion：

```java
int factorial(int n) {
    if (n == 1) {
        return 1;
    }

    return n * factorial(n - 1);
}
```

但它不是 backtracking，因为它没有：

```text
多个选择
试一条路
撤销选择
换另一条路
```

## 3. 通用伪代码

```text
result = []
path = []

function backtrack(当前状态):
    if 当前状态已经是一个完整答案:
        result.add(copy(path))
        return

    for 每一个可以尝试的选择:
        if 这个选择不合法:
            continue

        做选择
        backtrack(新的状态)
        撤销选择
```

最核心就是三步：

```text
choose
explore
unchoose
```

中文就是：

```text
做选择
递归探索
撤销选择
```

## 4. 谁 add，谁 remove

这是理解 backtracking 最重要的一句话：

```text
谁 add 的，谁 remove。
```

每一层递归只负责撤销自己这一层刚刚做的选择。

比如：

```text
Level 0: add 1
  Level 1: add 2
    Level 2: add 3
    Level 2: remove 3
  Level 1: remove 2
Level 0: remove 1
```

不是一 return 就直接撤销最外层的选择。

return 的意思是：

```text
当前这一层结束，回到上一层，继续执行递归调用后面的代码。
```

## 5. 怎么判断一道题是不是 Backtracking？

可以问自己：

```text
1. 这题是不是要找所有可能？
2. 每一步是不是有多个选择？
3. 做完一个选择后，是不是还要继续做类似的选择？
4. 试完一种可能后，是不是要退回来换选择？
```

如果大部分是 yes，大概率就是 backtracking。

常见题型：

```text
Subsets: 每个数选不选
Permutations: 每个位置放哪个数
Combination Sum: 每一步选哪个数凑 target
N-Queens: 每一行皇后放哪一列
Maze: 每一步往哪个方向走
```

## 6. 写 Backtracking 前先想什么？

不要一上来背代码。

先问这几个问题：

```text
1. path 表示什么？
2. choice 表示什么？
3. 什么时候算一个完整答案？
4. 这题要不要防止重复使用？
5. 撤销选择的时候要撤销什么？
```

不同题目的差别，主要就在这些地方。

