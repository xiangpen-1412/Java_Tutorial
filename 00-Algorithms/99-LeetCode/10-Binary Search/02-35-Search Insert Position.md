# 02. Search Insert Position

## 题号

```text
LeetCode 35. Search Insert Position
```

## 核心知识

这题用 Binary Search。

题目给一个升序数组 `nums` 和一个目标值 `target`：

```text
如果 target 存在，返回它的 index。
如果 target 不存在，返回它应该插入的位置。
```

## 题目本质

这题不是单纯问“target 是否存在”。

它真正要找的是：

```text
第一个 >= target 的位置。
```

如果这个位置上的值刚好等于 `target`，那就是目标 index。

如果数组里没有 `target`，这个位置就是插入位置。

## 思路

还是普通二分：

```text
left = 0
right = nums.length - 1
```

每次比较 `nums[mid]`：

```text
nums[mid] == target -> return mid
nums[mid] < target  -> target 应该在右边
nums[mid] > target  -> target 应该在左边
```

如果循环结束还没找到，返回：

```text
left
```

因为此时 `left` 就是第一个应该放 `target` 的位置。

## 例子

```text
nums = [1, 3, 5, 6]
target = 2
```

`2` 不存在，但它应该插在 `1` 和 `3` 中间。

所以返回：

```text
1
```

## 代码

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        return left;
    }
}
```

## 复杂度

```text
时间复杂度: O(log n)
空间复杂度: O(1)
```

## 记忆

```text
Search Insert Position = 找第一个 >= target 的位置。

找到了就返回 mid。
没找到就返回 left。
```

