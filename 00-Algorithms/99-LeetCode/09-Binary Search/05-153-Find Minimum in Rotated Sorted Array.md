# 05. Find Minimum in Rotated Sorted Array

## 题号

```text
LeetCode 153. Find Minimum in Rotated Sorted Array
```

## 核心知识

这题用 Binary Search。

题目给一个原本升序、但被旋转过的数组 `nums`，数组里的元素不重复。

要求返回：

```text
数组里的最小值本身
```

不是返回 index。

## 题目本质

旋转后的数组会出现一个断点。

比如：

```text
[4, 5, 6, 7, 0, 1, 2]
```

这里：

```text
7 -> 0
```

从大变小，最小值就是第二段升序数组的开头。

所以这题可以理解成：

```text
找旋转点
找第二段升序数组的开头
找数组里的最小值
```

## 思考方式

看到题目里的：

```text
O(log n)
sorted
rotated
unique
```

第一反应应该是：

```text
这不是普通查找，但大概率仍然可以二分。
```

原因是：

```text
旋转数组虽然整体不完全有序，但每次看 mid 时，左半边或右半边至少有一边是有序的。
```

## 你的写法

你的思路是判断当前区间的左右两部分是否有序：

```java
boolean leftValid = nums[left] <= nums[mid];
boolean rightValid = nums[right] >= nums[mid];
```

含义是：

```text
leftValid: left 到 mid 是有序的
rightValid: mid 到 right 是有序的
```

注意这里要用 `<=` 和 `>=`。

因为当区间只剩一个元素，或者 `left == mid`、`mid == right` 时，单个元素也应该被认为是有序的。

## 关键判断

如果：

```text
leftValid == true
rightValid == true
```

说明当前 `[left, right]` 整段已经有序。

此时最小值就在：

```text
nums[left]
```

所以可以停止循环。

如果：

```text
leftValid == true
rightValid == false
```

说明左半边是有序的，但右半边被旋转断开了。

最小值不在左半边已经有序的部分里，所以去右边：

```java
left = mid + 1;
```

如果：

```text
leftValid == false
rightValid == true
```

说明右半边是有序的，但断点在左边或 mid 附近。

这时 `mid` 仍然可能是最小值，所以要保留 mid：

```java
right = mid;
```

## 代码

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length - 1;

        while (left < right) {
            int mid = (right - left) / 2 + left;
            boolean leftValid = nums[left] <= nums[mid];
            boolean rightValid = nums[right] >= nums[mid];

            if (leftValid) {
                if (rightValid) {
                    break;
                } else {
                    left = mid + 1;
                    continue;
                }
            }

            if (rightValid) {
                right = mid;
            }
        }

        return nums[left];
    }
}
```

## 为什么这里有时排除 mid，有时保留 mid

当左半边有序、右半边无序时：

```text
left 到 mid 都是升序的
nums[mid] 不可能是最小值
```

所以可以排除 mid：

```java
left = mid + 1;
```

当右半边有序、左半边无序时：

```text
mid 到 right 是升序的
nums[mid] 可能就是最小值
```

所以不能排除 mid：

```java
right = mid;
```

这正好对应二分边界原则：

```text
mid 没资格当答案：mid +/- 1
mid 还有资格当答案：保留 mid
```

## 复杂度

```text
时间复杂度: O(log n)
空间复杂度: O(1)
```

## 记忆

```text
Find Minimum in Rotated Sorted Array:

返回最小值本身，不是 index。

旋转数组的最小值 = 断点后面的第一个元素。

当前区间已经整体有序时：
return nums[left]

mid 不可能是最小值：
left = mid + 1

mid 可能是最小值：
right = mid
```
