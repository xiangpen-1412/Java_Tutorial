# 01. Climbing Stairs

## 题号

```text
LeetCode 70. Climbing Stairs
```

## 1. 题目意思

你要爬到第 `n` 阶楼梯。

每次只能走：

```text
1 阶
或者
2 阶
```

题目问：

```text
到达第 n 阶，一共有多少种不同走法？
```

这题返回的是数量，不是返回所有路径。

## 2. 这题是什么类型？

这题是：

```text
一维 DP
```

也可以先用递归理解，再用 memo 或 DP 优化。

它的核心是：

```text
到第 n 阶的方法数，可以由前面更小的楼梯数推出来。
```

## 3. 为什么是 DP？

要到第 `n` 阶，最后一步只有两种可能：

```text
从第 n - 1 阶走 1 步上来
从第 n - 2 阶走 2 步上来
```

所以：

```text
到第 n 阶的方法数
= 到第 n - 1 阶的方法数
+ 到第 n - 2 阶的方法数
```

这就是状态转移。

## 4. dp[i] 表示什么？

```text
dp[i] = 到达第 i 阶楼梯的方法数
```

比如：

```text
dp[1] = 到第 1 阶的方法数
dp[2] = 到第 2 阶的方法数
dp[3] = 到第 3 阶的方法数
```

## 5. 初始状态

最基础的情况是：

```text
dp[1] = 1
dp[2] = 2
```

含义：

```text
到第 1 阶，只有 1 种走法。
到第 2 阶，有 2 种走法。
```

这些不用再往前推，是递推开始的基础答案。

## 6. 状态转移

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

含义：

```text
到第 i 阶的方法
= 先到第 i - 1 阶，再走 1 阶
+ 先到第 i - 2 阶，再走 2 阶
```

## 7. 为什么普通递归会超时？

普通递归会重复计算同一个问题。

比如：

```text
climb(5)
├── climb(4)
│   ├── climb(3)
│   └── climb(2)
└── climb(3)
    ├── climb(2)
    └── climb(1)
```

这里：

```text
climb(3) 被重复算
climb(2) 也被重复算
```

n 越大，重复越多，所以会超时。

## 8. Memo 写法里容易犯的错误

你之前这版会超时的原因是：

```java
int step = climbStairs(n - 1) + climbStairs(n - 2);
```

问题是：

```text
climbStairs 是入口函数。
每次调用它都会重新 new HashMap。
```

所以缓存被反复清空。

流程会变成：

```text
climbStairs(5)
  new map
  climb(5)
    climbStairs(4)
      new map
      climb(4)
        climbStairs(3)
          new map
```

这样 memo 没有起作用。

正确的思路是：

```text
入口函数只负责初始化 memo。
真正递归时，要调用递归函数本身。
这样所有递归层共享同一个 memo。
```

## 9. 两种实现思路

### 递归 + memo

```text
climbStairs(n):
    初始化 memo
    return climb(n)

climb(n):
    如果 memo 里有 n，直接返回
    ans = climb(n - 1) + climb(n - 2)
    memo[n] = ans
    return ans
```

### 迭代 DP

```text
dp[1] = 1
dp[2] = 2

for i from 3 to n:
    dp[i] = dp[i - 1] + dp[i - 2]

return dp[n]
```

## 10. 复杂度

普通递归：

```text
时间复杂度接近 O(2^n)
会超时
```

递归 + memo：

```text
时间复杂度 O(n)
空间复杂度 O(n)
```

迭代 DP：

```text
时间复杂度 O(n)
空间复杂度 O(n)
```

如果只保留前两个状态，还可以优化空间。

## 11. 记忆

```text
Climbing Stairs:
最后一步不是 1 阶，就是 2 阶。
所以 dp[i] = dp[i - 1] + dp[i - 2]。
```

```text
Memo 的关键：
不要在递归过程中重新初始化 map。
```

