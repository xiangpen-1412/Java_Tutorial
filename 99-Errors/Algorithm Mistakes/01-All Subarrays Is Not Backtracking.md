# 01. All Subarrays Is Not Backtracking

## 错误判断

看到题目里有：

```text
all subarrays
every subarray
所有子数组
```

就以为它是：

```text
Backtracking
```

这是一个常见误判。

## 正确理解

`subarray` 是连续子数组。

连续子数组不是随便选元素，而是由两个边界决定：

```text
start
end
```

所以它更像：

```text
区间问题
```

而不是：

```text
选择路径问题
```

## 为什么不是 Backtracking

Backtracking 通常有这些特征：

```text
每一步有多个选择
需要递归
需要撤销选择
返回所有方案本身
```

比如：

```text
Subsets
Permutations
Combinations
Combination Sum
```

这些题里，元素通常可以：

```text
选 / 不选
换顺序
组合成不同路径
```

所以会形成选择树。

但是 subarray 必须连续。

比如：

```text
arr = [3, 1, 2, 4]
```

合法 subarray：

```text
[3]
[1]
[2]
[4]
[3,1]
[1,2]
[2,4]
[3,1,2]
[1,2,4]
[3,1,2,4]
```

不合法：

```text
[3,2]
[1,4]
[3,2,4]
```

因为它们不连续。

所以 subarray 暴力枚举通常是：

```text
for start
    for end
```

不是：

```text
choose
recurse
undo
```

## 关键区别

```text
Backtracking:
我要把所有方案构造出来。

Subarray 统计题:
所有连续区间都参与计算，但不能真的全部列出来。
```

## 以 907 为例

LeetCode 907. Sum of Subarray Minimums

题目说：

```text
find the sum of min(b), where b ranges over every contiguous subarray
```

它要求的是：

```text
所有连续子数组最小值的总和
```

不是要求：

```text
返回所有连续子数组
```

所以这题的核心不是构造所有方案，而是统计贡献。

更准确的方向是：

```text
Monotonic Stack
Contribution Counting
区间贡献
```

## 判断公式

看到 `all` 时，不要立刻判断成 Backtracking。

先问：

```text
1. 题目要返回所有方案本身吗？
2. 每一步是否有多个选择分支？
3. 是否需要递归和撤销选择？
4. 元素是否可以不连续地组合？
```

如果这些成立，才像 Backtracking。

再问：

```text
1. 题目是不是只返回一个数？
2. 是不是在统计所有连续区间？
3. 数据范围是不是很大，不能真的枚举？
4. 能不能换成每个元素贡献多少次？
```

如果这些成立，更像：

```text
Prefix Sum
Monotonic Stack
DP
Contribution Counting
```

## 记忆

```text
all possible combinations/permutations/subsets -> Backtracking

all subarrays + return sum/count/min/max -> 通常不是 Backtracking
```

一句话：

```text
Backtracking 是选择树。
Subarray 是连续区间。
```

