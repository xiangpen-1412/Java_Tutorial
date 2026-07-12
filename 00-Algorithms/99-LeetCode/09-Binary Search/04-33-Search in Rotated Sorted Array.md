# 04. Search in Rotated Sorted Array

## 题号

```text
LeetCode 33. Search in Rotated Sorted Array
```

## 核心知识

这题是 Binary Search 的变形。

题目给一个原本升序、但被旋转过的数组 `nums`，再给一个目标值 `target`。

要求返回：

```text
target 的 index
```

如果不存在，返回：

```text
-1
```

## 题目本质

这个数组整体不再完全有序。

比如：

```text
[4, 5, 6, 7, 0, 1, 2]
```

但是它仍然有一个关键性质：

```text
每次取 mid 后，左半边或右半边至少有一边是有序的。
```

所以这题仍然可以二分。

核心不是直接判断 `target` 和 `nums[mid]` 后就完事，而是先判断：

```text
哪一半是有序的？
target 在不在有序的那一半？
```

## 思路

每轮先算：

```java
int mid = left + (right - left) / 2;
```

如果：

```text
nums[mid] == target
```

直接返回 `mid`。

否则判断左半边是否有序：

```java
nums[left] <= nums[mid]
```

如果左半边有序，就看 `target` 是否落在：

```text
nums[left] 到 nums[mid]
```

之间。

如果在，就去左边：

```text
right = mid - 1
```

否则去右边：

```text
left = mid + 1
```

如果左半边不是有序的，那右半边一定是有序的。

然后看 `target` 是否落在：

```text
nums[mid] 到 nums[right]
```

之间。

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
            }

            if (nums[left] <= nums[mid]) {
                if (target >= nums[left] && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else {
                if (target > nums[mid] && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }

        return -1;
    }
}
```

## 容易错的点

### 1. 判断有序性时不要用 mid - 1 / mid + 1

不要这样写：

```java
nums[mid - 1]
nums[mid + 1]
```

因为当 `mid` 在边界时会越界。

比如只剩一个元素或两个元素时：

```text
mid - 1 可能是 -1
mid + 1 可能超过 right
```

### 2. 判断有序性时要带上 mid

判断哪边有序时，用：

```java
nums[left] <= nums[mid]
```

或者：

```java
nums[mid] <= nums[right]
```

也就是：

```text
判断有序性：带上 mid
```

### 3. 移动搜索范围时要排除 mid

虽然判断有序性时带上 `mid`，但是移动边界时还是要排除 `mid`。

因为前面已经判断过：

```text
nums[mid] != target
```

所以：

```text
往右找：left = mid + 1
往左找：right = mid - 1
```

## 复杂度

```text
时间复杂度: O(log n)
空间复杂度: O(1)
```

## 记忆

```text
旋转数组二分：
每次至少有一边是有序的。

先判断哪边有序。
再判断 target 在不在有序那边。

判断有序性：带上 mid。
移动搜索范围：排除 mid。
```

