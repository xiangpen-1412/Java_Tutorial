# 213. House Robber II

> 难度：Medium  
> 主题：Dynamic Programming、环形数组、问题转换  
> 前置题目：[[02-198-House Robber]]

## 问题

给定数组 `nums`，`nums[i]` 表示第 `i` 间房里的金额。不能同时选择相邻的房子，要求返回能够获得的最大金额。

与 198 题不同，这里的房子围成一个圆，因此：

```text
第 0 间和第 n - 1 间也相邻
```

例如：

```text
nums = [2, 3, 2]
```

三间房围成圆后，两端的两个 `2` 也是相邻的，不能同时选择，所以最大金额是 `3`。

## 核心变化：第一间和最后一间不能同时选

普通 House Robber 的房子排成直线，只需要处理相邻限制：

```text
0 — 1 — 2 — 3
```

House Robber II 多了一条首尾相连的边：

```text
0 — 1 — 2 — 3
|             |
└─────────────┘
```

所以原来处理直线数组的 DP 不能直接覆盖整个数组，否则可能同时选择第一间和最后一间。

## 核心转换：把圆拆成两条直线

第一间和最后一间不能同时选，因此任何合法方案一定满足下面至少一个条件：

```text
不选第一间
不选最后一间
```

于是可以分别计算：

```text
情况一：只考虑 [1, n - 1]
情况二：只考虑 [0, n - 2]
```

这两个区间都已经变回普通的 House Robber，最后取两种情况的最大值。

例如：

```text
nums = [1, 2, 3, 1]
```

拆成：

```text
不选第一间：[2, 3, 1]
不选最后一间：[1, 2, 3]
```

这里不是强制选择某一端，而是通过排除一端，保证首尾不会被同时选择。

## Top-down：HashMap 记忆化搜索

可以直接传递区间边界，不需要复制两个新数组。

`dfs(nums, index, start)` 的含义是：

```text
在闭区间 [start, index] 中，最多能够获得多少钱
```

递归时仍然只有两个选择：

```text
选择当前房子：nums[index] + dfs(index - 2)
跳过当前房子：dfs(index - 1)
```

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    private Map<Integer, Integer> memo;

    public int rob(int[] nums) {
        if (nums.length == 1) {
            return nums[0];
        }

        memo = new HashMap<>();
        int skipLast = dfs(nums, nums.length - 2, 0);

        memo = new HashMap<>();
        int skipFirst = dfs(nums, nums.length - 1, 1);

        return Math.max(skipLast, skipFirst);
    }

    private int dfs(int[] nums, int index, int start) {
        if (index < start) {
            return 0;
        }

        if (memo.containsKey(index)) {
            return memo.get(index);
        }

        int choose = nums[index] + dfs(nums, index - 2, start);
        int skip = dfs(nums, index - 1, start);

        int result = Math.max(choose, skip);
        memo.put(index, result);
        return result;
    }
}
```

### 为什么两次计算之间要清空 `memo`

两次递归处理的起点不同：

```text
[0, n - 2]
[1, n - 1]
```

缓存中只有 `index`，没有包含 `start`。相同的 `index` 在两个区间中代表的问题不同，因此不能直接复用第一次的缓存。

如果把完整状态 `(start, index)` 一起作为缓存键，也可以共享缓存，但对这道题来说，分别创建两次 `memo` 更简单。

## Bottom-up：标准空间压缩写法

普通 House Robber 的状态转移是：

```text
当前最优 = max(
    不选当前房子时的最优,
    选择当前房子的钱 + 前前状态
)
```

因为当前状态只依赖前两个状态，所以可以用两个变量完成：

```java
class Solution {
    public int rob(int[] nums) {
        if (nums.length == 1) {
            return nums[0];
        }

        int skipFirst = robRange(nums, 1, nums.length - 1);
        int skipLast = robRange(nums, 0, nums.length - 2);

        return Math.max(skipFirst, skipLast);
    }

    private int robRange(int[] nums, int start, int end) {
        int previousTwo = 0;
        int previousOne = 0;

        for (int i = start; i <= end; i++) {
            int current = Math.max(
                    previousOne,
                    previousTwo + nums[i]
            );

            previousTwo = previousOne;
            previousOne = current;
        }

        return previousOne;
    }
}
```

这个版本不创建子数组，也没有递归栈：

- 时间复杂度：`O(n)`
- 额外空间复杂度：`O(1)`

## Top-down 与 Bottom-up

两者使用的是同一个状态关系，区别只是计算方向：

```text
Top-down：
从完整问题开始递归拆分
递归 + memo

Bottom-up：
从最小状态开始逐步推导
循环 + dp 状态
```

这道题所有状态都会被连续访问，而且每个状态只依赖前两个状态，因此 Bottom-up 可以很自然地压缩成 `O(1)` 额外空间。

Top-down 的优势是“选择或跳过”的递归含义直观；Bottom-up 更适合作为最终的简洁实现。

## 我的原始方案

原始正确方案是先复制两个子数组：

```java
int[] one = Arrays.copyOfRange(nums, 1, nums.length);
int[] two = Arrays.copyOfRange(nums, 0, nums.length - 1);
```

再分别运行普通 House Robber 的记忆化搜索，最后取最大值。

这个方案逻辑正确，因为它同样排除了：

```text
同时选择第一间和最后一间
```

但复制数组需要额外的 `O(n)` 空间。直接向辅助函数传入 `start` 和 `end`，可以表达相同的区间限制而不用复制数据。

## 我的思维误区

- **尝试复制整个数组后运行普通 DP，再把结果除以 2**：复制后的两个相同元素会被当成不同房子，新的相邻关系也不能准确表达原来的圆环限制；复制数组的最优方案与原问题没有成倍对应关系，所以最后除以 `2` 无法修正。
- **把循环误认为 Top-down**：循环从小状态逐步推出大状态属于 Bottom-up；从完整问题递归拆分并缓存结果才属于 Top-down。
- **认为 Top-down 不用 HashMap 就一定能更省空间**：可以用数组代替 HashMap 做记忆化，但递归栈和缓存仍需 `O(n)`；如果完全取消缓存，就会产生大量重复计算。

## 边界情况

### 只有一间房

```text
[8]
```

不存在首尾冲突，直接返回这一间的金额。这个情况需要单独处理，否则拆出的区间可能为空。

### 只有两间房

```text
[6, 9]
```

两间房相邻，只能选择其中金额更大的一间。拆成两个单元素区间后，通用逻辑可以自然处理。

## 复杂度

两种写法都会计算两个长度约为 `n - 1` 的线性区间：

```text
O(n - 1) + O(n - 1) = O(n)
```

- Top-down + HashMap：时间 `O(n)`，缓存和递归栈空间 `O(n)`。
- Bottom-up + 两个变量：时间 `O(n)`，额外空间 `O(1)`。

## 一句话总结

> 圆环问题的关键冲突在首尾：分别排除第一间和最后一间，把两个区间都还原成普通 House Robber，再取较大结果。
