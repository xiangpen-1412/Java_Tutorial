# 01. Range Sum Query - Immutable

## 题号

```text
LeetCode 303. Range Sum Query - Immutable
```

## 核心知识

这题用的是：

```text
Prefix Sum 前缀和
```

题目会多次查询：

```text
nums[left] + nums[left + 1] + ... + nums[right]
```

如果每次都重新 loop 计算，查询多了会慢。

所以先预处理一个前缀和数组。

## 思路

定义：

```text
sum[i] = nums[0] + nums[1] + ... + nums[i]
```

也就是：

```text
sum[i] 表示从 index 0 到 index i 的总和
```

那么区间 `[left, right]` 的和就是：

```text
sum[right] - sum[left - 1]
```

如果 `left == 0`，说明区间从开头开始，直接返回：

```text
sum[right]
```

## 代码

```java
class NumArray {
    int[] sum;

    public NumArray(int[] nums) {
        sum = new int[nums.length];
        sum[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            sum[i] = sum[i - 1] + nums[i];
        }
    }

    public int sumRange(int left, int right) {
        if (left == 0) {
            return sum[right];
        } else {
            return sum[right] - sum[left - 1];
        }
    }
}
```

## 复杂度

```text
初始化: O(n)
每次查询: O(1)
额外空间: O(n)
```

## 记忆

```text
前缀和 = 先存从开头到当前位置的累计和。
区间和 = 两个前缀和相减。
```

