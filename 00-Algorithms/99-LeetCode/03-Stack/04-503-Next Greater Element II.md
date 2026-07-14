# 503. Next Greater Element II

## 题目在问什么

给一个循环数组 `nums`，对每个位置 `i`，找它右边第一个严格比 `nums[i]` 大的数。

因为数组是循环的，所以最后一个元素后面可以回到第一个元素继续找。

如果找完整整一圈都没有更大的数，答案就是 `-1`。

例子：

```text
nums = [1,2,1]
```

对最后一个 `1` 来说，它后面已经到数组末尾了，但是可以循环回开头：

```text
1 -> 1 -> 2
```

第一个比它大的数是 `2`，所以答案是：

```text
[2,-1,2]
```

## 这题和 Next Greater Element I 的关系

本质还是：

```text
往右找第一个更大的元素
```

所以仍然可以用单调栈。

区别只是这题多了一个“循环数组”。

循环数组可以用两种方式理解：

```text
1. 真的复制一份数组：nums + nums
2. 不复制数组，只遍历 2 * n 次，用 i % n 取下标
```

第二种更省空间。

## 为什么不能用 max 补答案

这题要找的是：

```text
右边第一个更大的数
```

不是：

```text
整个数组里最大的数
```

比如：

```text
nums = [1,2,3,2,1]
```

最后一个 `1` 往右循环：

```text
1 -> 1 -> 2 -> 3
```

第一个比它大的数是 `2`，不是最大值 `3`。

所以答案最后一位应该是 `2`：

```text
[2,3,-1,3,2]
```

## 单调栈思路

栈里放的是：

```text
还没有找到 next greater 的下标
```

遍历数组两遍。

每次看到当前值 `nums[i % n]`，就去看栈顶下标对应的值。

如果当前值更大：

```text
nums[i % n] > nums[stack.peek()]
```

说明当前值就是栈顶元素的下一个更大元素。

于是：

```text
result[stack.pop()] = nums[i % n]
```

如果当前值还能继续解决更早的元素，就继续弹。

第一遍遍历时，把下标压入栈。

第二遍只负责帮第一遍剩下的元素找循环后的答案，通常不需要再压入新下标。

## 代码

```java
import java.util.*;

class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        Arrays.fill(result, -1);

        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < 2 * n; i++) {
            int curIndex = i % n;

            while (!stack.isEmpty() && nums[curIndex] > nums[stack.peek()]) {
                int prevIndex = stack.pop();
                result[prevIndex] = nums[curIndex];
            }

            if (i < n) {
                stack.push(i);
            }
        }

        return result;
    }
}
```

## 复杂度

虽然遍历了 `2 * n` 次，但每个下标最多：

```text
入栈一次
出栈一次
```

所以时间复杂度是：

```text
O(n)
```

不是 `O(n^2)`。

空间复杂度是：

```text
O(n)
```

## 常见错误

### 1. 用 `nums[i]` 访问第二遍

如果 `i` 遍历到 `n` 以后，再写：

```java
nums[i]
```

就会越界。

应该写：

```java
nums[i % n]
```

或者如果真的复制了数组，就统一访问复制后的数组。

### 2. 栈里只存值，不存下标

数组里可能有重复值。

而且结果数组要按位置填答案，所以栈里最好存下标。

### 3. `while` 忘记判断栈是否为空

错误写法：

```java
while (nums[i] > nums[stack.peek()])
```

正确写法：

```java
while (!stack.isEmpty() && nums[curIndex] > nums[stack.peek()])
```

## 核心记忆

这题可以记成：

```text
Next Greater Element + 循环数组 = 遍历两遍 + i % n + 单调栈
```

不要用 `max` 补答案，因为题目要的是“第一个更大”，不是“最大”。
