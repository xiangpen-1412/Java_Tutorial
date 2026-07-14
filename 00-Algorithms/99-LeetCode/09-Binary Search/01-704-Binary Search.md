# 01. Binary Search

## 题号

```text
LeetCode 704. Binary Search
```

## 核心知识

这题是最基础的 Binary Search。

题目给一个升序数组 `nums` 和一个目标值 `target`，要求返回 `target` 的 index。

如果不存在，返回：

```text
-1
```

## 题目本质

因为数组是升序的，所以每次看中间元素 `nums[mid]`：

```text
nums[mid] == target -> 找到了
nums[mid] < target  -> target 只能在右边
nums[mid] > target  -> target 只能在左边
```

也就是：

```text
每次排除一半不可能有答案的范围。
```

## 思路

定义左右边界：

```text
left = 0
right = nums.length - 1
```

只要范围还存在：

```text
left <= right
```

就继续查找。

中间位置：

```java
int mid = left + (right - left) / 2;
```

比较之后：

```text
如果 nums[mid] 太小，去右边找：left = mid + 1
如果 nums[mid] 太大，去左边找：right = mid - 1
```

## 代码

```java
class Solution {
    public int search(int[] nums, int target) {
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

        return -1;
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
普通二分查找：
mid 查过后就不要再保留。

nums[mid] < target -> left = mid + 1
nums[mid] > target -> right = mid - 1
```

