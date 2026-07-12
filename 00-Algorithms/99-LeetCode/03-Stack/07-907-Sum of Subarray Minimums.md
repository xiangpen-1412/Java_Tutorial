# 07. Sum of Subarray Minimums

## 题号

```text
LeetCode 907. Sum of Subarray Minimums
```

## 核心知识

这题用：

```text
Monotonic Stack
Contribution Counting 贡献法
```

题目要求：

```text
所有连续子数组的最小值之和。
```

不是返回所有子数组，也不是返回某一个最小值。

## 题目意思

给一个数组：

```text
arr
```

找出所有连续子数组。

对每个连续子数组取最小值。

最后把这些最小值全部加起来。

因为答案可能很大，所以要对：

```text
10^9 + 7
```

取模。

## 例子

```text
arr = [3, 1, 2, 4]
```

所有连续子数组：

```text
[3]       -> min = 3
[1]       -> min = 1
[2]       -> min = 2
[4]       -> min = 4
[3,1]     -> min = 1
[1,2]     -> min = 1
[2,4]     -> min = 2
[3,1,2]   -> min = 1
[1,2,4]   -> min = 1
[3,1,2,4] -> min = 1
```

相加：

```text
3 + 1 + 2 + 4 + 1 + 1 + 2 + 1 + 1 + 1 = 17
```

所以答案是：

```text
17
```

## 为什么不是 Backtracking

虽然题目说的是：

```text
every subarray
```

但这不是 Backtracking。

Backtracking 通常是：

```text
返回所有方案
每一步有多个选择
递归
撤销选择
```

而 subarray 是连续区间，只由两个边界决定：

```text
start
end
```

所以这题不是选择树，而是区间统计。

更准确的方向是：

```text
贡献法
```

## 贡献法思路

不要枚举每个 subarray。

换成问：

```text
每个元素 arr[i] 可以作为多少个 subarray 的最小值？
```

如果 `arr[i]` 作为最小值出现了 `count` 次，那么它对答案的贡献就是：

```text
arr[i] * count
```

总答案：

```text
sum(arr[i] * count[i])
```

## 怎么计算 count

对于每个位置 `i`，需要找两个边界：

```text
prev = 左边最近的更小元素位置
next = 右边最近的更小或相等元素位置
```

这里用的是方案 A：

```text
previous strictly less
next less or equal
```

也就是：

```text
左边找 <
右边找 <=
```

那么 `arr[i]` 可以作为最小值的范围是：

```text
(prev, next)
```

左端点可以选：

```text
prev + 1 到 i
```

所以左边选择数：

```text
left = i - prev
```

右端点可以选：

```text
i 到 next - 1
```

所以右边选择数：

```text
right = next - i
```

总 subarray 数：

```text
count = left * right
```

贡献：

```text
arr[i] * left * right
```

## 为什么是 left * right

一个 subarray 由两个端点决定：

```text
start
end
```

如果 `arr[i]` 必须包含在 subarray 里：

```text
start 有 left 种选择
end 有 right 种选择
```

每一个 `start` 都可以搭配每一个 `end`。

所以总数是：

```text
left * right
```

不是：

```text
left + right
```

例子：

```text
arr = [3, 1, 2, 4]
i = 1
arr[i] = 1
```

对于 `1`：

```text
prev = -1
next = 4
```

所以：

```text
left = 1 - (-1) = 2
right = 4 - 1 = 3
```

左端点可以选：

```text
0, 1
```

右端点可以选：

```text
1, 2, 3
```

组合：

```text
start = 0, end = 1 -> [3,1]
start = 0, end = 2 -> [3,1,2]
start = 0, end = 3 -> [3,1,2,4]
start = 1, end = 1 -> [1]
start = 1, end = 2 -> [1,2]
start = 1, end = 3 -> [1,2,4]
```

一共：

```text
2 * 3 = 6
```

## 为什么 previous 没找到是 -1

`-1` 表示：

```text
左边界在数组左外侧。
```

比如：

```text
arr = [2, 1, 3]
i = 1
arr[i] = 1
```

`1` 左边没有更小值。

它作为最小值时，左端点可以选：

```text
0, 1
```

左边选择数应该是：

```text
2
```

如果：

```text
prev = -1
```

那么：

```text
i - prev = 1 - (-1) = 2
```

刚好正确。

如果错误地写成：

```text
prev = i
```

那么：

```text
i - prev = 0
```

贡献就会变成 0，错误。

## 为什么 next 没找到是 n

`n` 表示：

```text
右边界在数组右外侧。
```

比如：

```text
arr = [1, 2, 4]
i = 0
arr[i] = 1
```

`1` 右边没有更小或相等元素。

它作为最小值时，右端点可以选：

```text
0, 1, 2
```

右边选择数应该是：

```text
3
```

如果：

```text
next = n = 3
```

那么：

```text
next - i = 3 - 0 = 3
```

刚好正确。

如果默认是：

```text
next = 0
```

那么：

```text
next - i = 0
```

会直接错误。

## 为什么要处理等号

数组里可能有重复值。

比如：

```text
arr = [2, 2]
```

子数组：

```text
[2]
[2]
[2,2]
```

`[2,2]` 里两个 `2` 都可以说自己是最小值。

为了避免重复计算，必须规定：

```text
相同最小值时，只让其中一个元素负责。
```

本笔记使用方案 A：

```text
previous strictly less
next less or equal
```

也就是：

```text
相等时，归右边那个元素负责。
```

## 为什么 previous 的代码用 >=

目标：

```text
找左边最近的严格更小元素。
```

也就是：

```text
previous less <
```

当栈顶满足：

```text
nums[stack.peek()] >= nums[i]
```

它不可能是 previous less。

所以要弹掉：

```java
while (!stack.isEmpty() && nums[stack.peek()] >= nums[i]) {
    stack.pop();
}
```

弹完以后，栈顶如果存在，一定满足：

```text
nums[stack.peek()] < nums[i]
```

所以它就是 previous strictly less。

重点：

```text
while 条件写的是“不合格条件”。
```

## 为什么 next 的代码用 <=

目标：

```text
找右边第一个小于等于当前元素的位置。
```

也就是：

```text
next less or equal <=
```

从左往右扫时，栈里存的是：

```text
还没有找到 next less or equal 的旧元素。
```

当当前元素满足：

```text
nums[i] <= nums[stack.peek()]
```

说明当前 `i` 就是栈顶旧元素右边第一个：

```text
<= 它的元素。
```

所以记录：

```java
res[index] = i;
```

代码：

```java
while (!stack.isEmpty() && nums[i] <= nums[stack.peek()]) {
    int index = stack.pop();
    res[index] = i;
}
```

## 代码

```java
import java.util.*;

class Solution {
    public int sumSubarrayMins(int[] arr) {
        int MOD = 1_000_000_007;

        int[] prev = findPreviousSmall(arr);
        int[] next = findNextSmall(arr);

        long res = 0;

        for (int i = 0; i < arr.length; i++) {
            int left = i - prev[i];
            int right = next[i] - i;

            res = (res + (long) left * right * arr[i]) % MOD;
        }

        return (int) res;
    }

    public int[] findPreviousSmall(int[] nums) {
        int[] res = new int[nums.length];
        Arrays.fill(res, -1);

        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < nums.length; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] >= nums[i]) {
                stack.pop();
            }

            if (!stack.isEmpty()) {
                res[i] = stack.peek();
            }

            stack.push(i);
        }

        return res;
    }

    public int[] findNextSmall(int[] nums) {
        int[] res = new int[nums.length];
        Arrays.fill(res, nums.length);

        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < nums.length; i++) {
            while (!stack.isEmpty() && nums[i] <= nums[stack.peek()]) {
                int index = stack.pop();
                res[index] = i;
            }

            stack.push(i);
        }

        return res;
    }
}
```

## 常见错误

### 1. 把贡献次数写成加法

错误：

```java
left + right + 1
```

正确：

```java
left * right
```

原因：

```text
subarray 由 start 和 end 两个端点组合出来。
```

### 2. previous 没找到写成 i

错误：

```java
res[i] = i;
```

正确：

```java
res[i] = -1;
```

### 3. next 没找到默认 0

错误：

```text
res 默认是 0，不处理。
```

正确：

```java
Arrays.fill(res, nums.length);
```

### 4. 用 int 直接累加

中间结果可能溢出。

应该用：

```java
long
```

### 5. 用 Math.pow 写 MOD

不推荐：

```java
int mod = (int) Math.pow(10, 9) + 7;
```

推荐：

```java
int MOD = 1_000_000_007;
```

## 复杂度

每个元素最多：

```text
入栈一次
出栈一次
```

所以：

```text
时间复杂度: O(n)
空间复杂度: O(n)
```

## 记忆

```text
907:

不要枚举每个 subarray。
改成枚举每个元素作为 minimum 的贡献。

贡献 = arr[i] * left * right

left = i - previousLessIndex
right = nextLessOrEqualIndex - i

previous 没有 -> -1
next 没有 -> n

相等值要规定归属方向，避免重复计算。
```

