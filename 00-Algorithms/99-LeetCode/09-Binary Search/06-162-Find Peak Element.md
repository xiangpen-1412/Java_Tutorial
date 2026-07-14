# 06. Find Peak Element

## 题号

```text
LeetCode 162. Find Peak Element
```

## 核心知识

这题用 Binary Search。

题目给一个数组 `nums`，要求找到任意一个 peak element，并返回它的 index。

peak element 指的是：

```text
严格大于左右相邻元素的元素。
```

如果有多个 peak：

```text
返回任意一个 peak 的 index 都可以。
```

注意：

```text
返回的是 index，不是元素值。
```

## 边界规则

题目允许把数组外面看成：

```text
nums[-1] = -∞
nums[n] = -∞
```

所以：

```text
第一个元素只要比右边大，就可以是 peak。
最后一个元素只要比左边大，就可以是 peak。
```

比如：

```text
nums = [5, 3, 2]
```

`5` 左边是 `-∞`，右边是 `3`，所以 `5` 是 peak，返回 index `0`。

再比如：

```text
nums = [1, 3, 5]
```

`5` 左边是 `3`，右边是 `-∞`，所以 `5` 是 peak，返回 index `2`。

## 题目本质

这题不是在有序数组里找 target。

它和 Binary Search 的关系是：

```text
虽然数组整体无序，但通过 mid 和 mid + 1 的比较，可以判断哪一边一定存在 peak。
```

Binary Search 的本质不是数组有序，而是：

```text
每次能安全排除一半。
```

## 山路理解

可以把数组想象成一条山路。

你站在 `mid`，只能感觉当前和右边一步的高度变化：

```text
如果地面往上走，就继续往右走。
如果地面往下走，就往左边找。
最后一定会到一个山顶，也就是 peak。
```

对应到代码：

```text
nums[mid] < nums[mid + 1] -> 右边是上坡
nums[mid] > nums[mid + 1] -> 左边或 mid 这边有峰
```

## 为什么上坡就去右边

如果：

```text
nums[mid] < nums[mid + 1]
```

说明从 `mid` 到 `mid + 1` 是上坡。

那么右边一定存在 peak。

原因：

```text
要么一直上升到最后，那么最后一个元素就是 peak。
要么中途开始下降，那么上升转下降的位置就是 peak。
```

所以可以去右边：

```java
left = mid + 1;
```

这里 `mid` 不可能是 peak，因为：

```text
nums[mid] < nums[mid + 1]
```

所以 `mid` 右边有比它更大的元素。

## 为什么下坡就去左边

如果：

```text
nums[mid] > nums[mid + 1]
```

说明从 `mid` 到 `mid + 1` 是下坡。

那么左边或者 `mid` 自己这一侧一定存在 peak。

原因：

```text
要么 nums[mid] 自己就是峰。
要么继续往左，某个位置会从上坡变下坡，那里就是峰。
```

所以要保留 `mid`：

```java
right = mid;
```

这里不能写：

```java
right = mid - 1;
```

因为 `mid` 可能就是 peak。

## 代码

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int left = 0;
        int right = nums.length - 1;

        while (left < right) {
            int mid = (right - left) / 2 + left;

            if (nums[mid] < nums[mid + 1]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        return left;
    }
}
```

## 为什么用 left < right

这题不是在循环里找到 target 就返回。

它是在不断缩小候选范围，直到：

```text
left == right
```

这个位置就是一个 peak。

所以使用：

```java
while (left < right)
```

最后返回：

```java
return left;
```

## 边界移动原则

这题正好体现了二分边界原则：

```text
mid 不可能是答案：排除 mid
mid 可能是答案：保留 mid
```

当：

```text
nums[mid] < nums[mid + 1]
```

`mid` 不可能是 peak，所以：

```java
left = mid + 1;
```

当：

```text
nums[mid] > nums[mid + 1]
```

`mid` 可能是 peak，所以：

```java
right = mid;
```

## 复杂度

```text
时间复杂度: O(log n)
空间复杂度: O(1)
```

## 记忆

```text
Find Peak Element:

看坡度，不看整体有序。

上坡：往右走。
下坡：往左找。

右边更高，mid 不可能是峰，排除 mid。
右边更低，mid 可能是峰，保留 mid。

最后 left == right 的位置就是 peak。
```

