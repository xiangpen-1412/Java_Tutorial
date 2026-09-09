# 740. Delete and Earn

> 难度：Medium  
> 主题：Dynamic Programming、记忆化搜索、问题转换  
> 前置题目：[[02-198-House Robber]]

## 问题

给定整数数组 `nums`。每次选择一个数字 `x`，可以获得 `x` 点，但数组中所有的 `x - 1` 和 `x + 1` 都会被删除。可以重复操作，目标是获得最多点数。

题目的限制不是发生在原数组的相邻下标之间，而是发生在相邻的**数值**之间：

```text
选择 x 后，不能再选择 x - 1 和 x + 1。
```

## Hint 的含义

题目的 Hint 是：

> If you take a number, you might as well take them all.

如果决定选择数字 `x`，就应该拿走所有的 `x`。选择第一个 `x` 时，所有的 `x - 1` 和 `x + 1` 已经不能再选；继续选择剩余的 `x` 不会增加新的限制，却可以继续得分。

因此可以先把相同数字合并：

```text
points[x] = x * x 出现的次数
```

原问题就被转换成：

```text
每个数值位置 x 有 points[x] 的收益；
相邻数值不能同时选择；
求最大收益。
```

这正是 House Robber 模型。原数组中的房屋下标，在这里变成了数值轴上的数字。

## Top-down 状态定义

定义：

```text
dfs(x) = 只考虑数值范围 [0, x] 时，最多能够获得的点数
```

注意：

```text
dfs(x) 不表示必须选择 x；
dfs(x) 也不要求数字 x 一定在 nums 中出现。
```

处理数值 `x` 时有两个选择：

```text
选择 x：points[x] + dfs(x - 2)
不选择 x：dfs(x - 1)
```

所以：

```text
dfs(x) = max(points[x] + dfs(x - 2), dfs(x - 1))
```

选择 `x` 后不能选择 `x - 1`，因此接着考虑不超过 `x - 2` 的范围。`x + 1` 不需要在当前状态中检查，因为当前子问题的最大值是 `x`；将来处理 `dfs(x + 1)` 时，它与 `x` 的冲突自然会被处理。

## 不存在的数值为什么不会漏掉前面的数字

例如：

```text
nums = [3, 5, 6]
```

聚合后的收益为：

```text
points[3] = 3
points[4] = 0
points[5] = 5
points[6] = 6
```

从 `dfs(6)` 开始：

```text
选择 6：6 + dfs(4)
不选 6：dfs(5)
```

虽然输入中没有 `4`，但 `dfs(4)` 的含义是“考虑所有不超过 4 的数字”，不是“选择数字 4”：

```text
dfs(4)
= max(points[4] + dfs(2), dfs(3))
= max(0 + dfs(2), dfs(3))
= dfs(3)
= 3
```

因此选择 `6` 的收益是：

```text
6 + dfs(4) = 6 + 3 = 9
```

数字 `3` 并没有被忽略。不存在的数值只是数值轴上收益为 `0` 的位置，递归仍会继续向前找到真正出现过的数字。

## 数组型 Top-down

本题中 `nums[i] <= 10000`，数值范围不大，可以直接用数组聚合收益，并用数组记忆化。

```java
class Solution {
    private int[] points;
    private int[] memo;

    public int deleteAndEarn(int[] nums) {
        int max = 0;

        for (int num : nums) {
            max = Math.max(max, num);
        }

        points = new int[max + 1];
        for (int num : nums) {
            points[num] += num;
        }

        memo = new int[max + 1];
        Arrays.fill(memo, -1);

        return dfs(max);
    }

    private int dfs(int num) {
        if (num <= 0) {
            return 0;
        }

        if (memo[num] != -1) {
            return memo[num];
        }

        int pick = points[num] + dfs(num - 2);
        int skip = dfs(num - 1);

        memo[num] = Math.max(pick, skip);
        return memo[num];
    }
}
```

复杂度：

```text
时间：O(n + M)
空间：O(M)
```

其中 `M` 是数组中的最大数值。空间包含收益数组、记忆化数组和递归栈。

## TreeMap 方案

最初的思路是：

1. 用 `TreeMap<Integer, Integer>` 统计每个数字出现的次数。
2. 从 `lastKey()` 开始。
3. 用 `lowerKey(current)` 找下一个更小的不同数字。
4. 如果两个数字相邻，就比较选择当前与跳过当前；不相邻时可以一起选择。

这个思路是正确的，也能避免扫描数值轴上的空位置，但实现开销更大：

```text
TreeMap 查询是 O(log k)；
HashMap memo 有哈希和装箱开销；
如果已经使用 TreeMap，预先排序 nums 是多余的；
所有递归分支都必须写入 memo，提前 return 时也不能漏掉缓存。
```

此外，`lowerKey()` 在不存在更小 key 时会返回 `null`。如果自动拆箱成 `int`，会抛出 `NullPointerException`，所以使用这种写法必须为“没有更小数字”设置边界。

在本题数值上限较小的条件下，数组型 Top-down 更直接、常数更小。若数值范围非常大且不同数字很少，使用有序去重数组或 `TreeMap` 才更有意义。

## 核心收获

- **识别信号**：每个数值可以选择或跳过，选择相邻数值会冲突，并且目标是最大化收益，应联想到 House Robber。
- **核心转换**：先将所有相同数字的收益合并为 `points[x]`，再把数值轴看作房屋数组。
- **状态含义**：`dfs(x)` 表示 `[0, x]` 范围内的最优答案，不表示一定选择 `x`，也不要求 `x` 出现在输入中。
- **处理方向**：从最大值向下递归时，只需显式处理与 `x - 1` 的冲突；`x + 1` 不在当前子问题中。
- **数据结构选择**：数值范围小时优先用数组；数值范围巨大而有效 key 稀疏时，再考虑有序去重数组或 `TreeMap`。

## 我的思维误区

- **认为选择 `6` 后调用 `dfs(4)` 会漏掉输入中的 `3`**：这是把参数 `4` 误解成了“当前必须选择的节点”。实际上它是子问题的数值上界，`dfs(4)` 会经过收益为 `0` 的位置继续得到 `dfs(3)`。
- **认为当前必须同时检查 `x - 1` 和 `x + 1`**：状态只考虑不超过 `x` 的范围，`x + 1` 尚未进入当前子问题；按固定方向建立状态后，只需处理已经位于子问题内的相邻冲突。
- **为了找到第二大、第三大的数字而依赖 TreeMap**：这可以实现，但不是本题约束下最合适的表示。将数值直接作为数组下标后，不存在的位置自然成为收益 `0` 的状态，不再需要寻找前一个有效 key。
