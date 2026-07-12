# 01. Kth Largest Element in an Array

## 题号

```text
LeetCode 215. Kth Largest Element in an Array
```

## 题目意思

给一个数组 `nums` 和一个整数 `k`，返回数组中第 `k` 大的元素。

注意：

```text
第 k 大，不是第 k 个不同的元素。
```

比如：

```text
nums = [3, 2, 1, 5, 6, 4]
k = 2
```

排序后是：

```text
[6, 5, 4, 3, 2, 1]
```

第 2 大是：

```text
5
```

## 核心思路

这题可以用 `PriorityQueue` 维护一个大小为 `k` 的小顶堆。

小顶堆特点：

```text
peek() 是当前堆里最小的元素
poll() 弹出当前堆里最小的元素
```

我们要维护：

```text
堆里始终保存当前见过的最大的 k 个数
```

这样最后：

```text
堆里有最大的 k 个数
堆顶是这 k 个数里最小的
所以堆顶就是全局第 k 大
```

## 为什么用小顶堆

如果要找第 `k` 大，可以保留最大的 `k` 个数。

比如：

```text
nums = [3, 2, 1, 5, 6, 4]
k = 2
```

最后只需要保留最大的 2 个数：

```text
[5, 6]
```

这两个数里较小的是 `5`。

所以：

```text
第 2 大 = 5
```

用小顶堆保存 `[5, 6]` 时：

```java
heap.peek() == 5
```

正好就是答案。

## 你的版本

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> queue = new PriorityQueue<>(k, (a, b) -> (a - b));

        for (int i = 0; i < nums.length; i++) {
            if (queue.size() == k) {
                int currentMin = queue.peek();
                if (currentMin < nums[i]) {
                    queue.poll();
                    queue.offer(nums[i]);
                }
            } else {
                queue.offer(nums[i]);
            }
        }

        return queue.peek();
    }
}
```

这版逻辑是对的。

含义是：

```text
如果堆还没满：
    直接加入当前元素

如果堆已经有 k 个元素：
    看当前元素 nums[i] 是否比堆顶更大

    如果 nums[i] 更大：
        弹出当前最小值
        加入 nums[i]

    如果 nums[i] 不更大：
        不加入
```

为什么不加入更小的数？

因为堆里已经保存了当前最大的 `k` 个数。如果新来的数连堆顶都不如，它不可能进入最大的 `k` 个数。

## 你的版本可以优化的地方

### 1. Comparator 可以省略

`PriorityQueue<Integer>` 默认就是小顶堆。

所以：

```java
PriorityQueue<Integer> queue = new PriorityQueue<>(k, (a, b) -> (a - b));
```

可以写成：

```java
PriorityQueue<Integer> queue = new PriorityQueue<>();
```

### 2. 不推荐写 a - b

```java
(a, b) -> (a - b)
```

在极端情况下可能整数溢出。

如果真的要写 comparator，更推荐：

```java
(a, b) -> Integer.compare(a, b)
```

但这题默认小顶堆就够了。

## 优化版 1：保留你的判断逻辑

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();

        for (int num : nums) {
            if (minHeap.size() < k) {
                minHeap.offer(num);
            } else if (num > minHeap.peek()) {
                minHeap.poll();
                minHeap.offer(num);
            }
        }

        return minHeap.peek();
    }
}
```

这版和你的思路一样，只是写法更清楚：

```text
堆没满：先加
堆满了：只有比堆顶大，才替换堆顶
```

## 优化版 2：更常见的标准模板

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();

        for (int num : nums) {
            minHeap.offer(num);

            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }

        return minHeap.peek();
    }
}
```

这版的逻辑是：

```text
每个数都先放进去
如果堆大小超过 k，就弹出最小值
```

最后堆里剩下的就是：

```text
最大的 k 个数
```

所以：

```java
return minHeap.peek();
```

就是第 `k` 大。

## 过程例子

```text
nums = [3, 2, 1, 5, 6, 4]
k = 2
```

使用标准模板：

```text
offer 3 -> [3]
offer 2 -> [2, 3]

offer 1 -> [1, 3, 2]
size > 2, poll 1 -> [2, 3]

offer 5 -> [2, 3, 5]
size > 2, poll 2 -> [3, 5]

offer 6 -> [3, 5, 6]
size > 2, poll 3 -> [5, 6]

offer 4 -> [4, 6, 5]
size > 2, poll 4 -> [5, 6]
```

最后堆里是：

```text
[5, 6]
```

堆顶：

```text
5
```

答案：

```text
5
```

## 之前容易错的点

### 1. 不能满了就直接 poll

错误思路：

```java
if (queue.size() == k) {
    queue.poll();
}

queue.offer(nums[i]);
```

这个写法的问题是：

```text
只要堆满了，就无条件弹出最小值，再无条件加入当前值。
```

这样不能保证堆里保存的是最大的 `k` 个数。

正确逻辑应该是：

```text
如果当前数比堆顶大，才替换堆顶。
```

或者使用标准模板：

```text
先加入，再在 size > k 时弹出最小值。
```

### 2. 最后不能 poll k 次

错误写法：

```java
int res = nums[0];

for (int i = 0; i < k; i++) {
    res = queue.poll();
}

return res;
```

如果堆里已经只保存了最大的 `k` 个数，那么：

```text
queue.peek() 已经是第 k 大
```

不需要继续 `poll()`。

比如最后堆是：

```text
[5, 6]
```

`k = 2`。

答案是：

```text
peek() = 5
```

如果 `poll()` 两次：

```text
第一次 poll -> 5
第二次 poll -> 6
```

最后会错误返回 `6`。

## 什么时候需要 poll k 次

`poll k 次` 是另一种做法：把所有元素放进大顶堆。

大顶堆里：

```text
poll() 每次弹出当前最大值
```

所以：

```text
第 1 次 poll -> 第 1 大
第 2 次 poll -> 第 2 大
...
第 k 次 poll -> 第 k 大
```

代码：

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> maxHeap =
            new PriorityQueue<>((a, b) -> Integer.compare(b, a));

        for (int num : nums) {
            maxHeap.offer(num);
        }

        int ans = 0;
        for (int i = 0; i < k; i++) {
            ans = maxHeap.poll();
        }

        return ans;
    }
}
```

这个做法也对，但是空间更大：

```text
空间复杂度 O(n)
```

而大小为 `k` 的小顶堆只需要：

```text
空间复杂度 O(k)
```

## 两种堆思路对比

| 思路 | 堆类型 | 堆里保存什么 | 最后怎么返回 | 空间 |
|---|---|---|---|---|
| 小顶堆 | `PriorityQueue<>` | 最大的 `k` 个数 | `peek()` | `O(k)` |
| 大顶堆 | 自定义 comparator | 所有数 | `poll()` 第 `k` 次 | `O(n)` |

## 复杂度

使用大小为 `k` 的小顶堆：

```text
时间复杂度: O(n log k)
空间复杂度: O(k)
```

原因：

```text
每个元素最多进入堆一次
堆的大小最多是 k
每次 offer / poll 是 O(log k)
```

## 记忆模板

```text
找第 k 大：

用大小为 k 的小顶堆。
堆里保存最大的 k 个数。
堆顶是这 k 个数里最小的。
所以堆顶就是第 k 大。
```

代码模板：

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

for (int num : nums) {
    minHeap.offer(num);

    if (minHeap.size() > k) {
        minHeap.poll();
    }
}

return minHeap.peek();
```

