# 00. Heap & PriorityQueue

## 核心理解

`Heap` 是一种数据结构。

`PriorityQueue` 是 Java 里基于 heap 实现的工具类。

可以先这样理解：

```text
Heap 负责维护“谁最优先”
PriorityQueue 是 Java 给你封装好的 heap
```

在 Java 里：

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
```

默认就是一个小顶堆。

小顶堆的意思是：

```text
peek() 永远能看到当前最小的元素
poll() 永远弹出当前最小的元素
```

## PriorityQueue 不是完整排序

这是最容易误会的地方。

`PriorityQueue` 不保证里面所有元素都是排好序的。

它只保证一件事：

```text
堆顶元素一定是当前优先级最高的元素
```

比如：

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(5);
pq.offer(1);
pq.offer(3);
pq.offer(8);
pq.offer(2);

System.out.println(pq);
```

打印结果可能是：

```text
[1, 2, 3, 8, 5]
```

看起来像排序了，但不要依赖这个顺序。

真正可靠的是：

```java
pq.peek(); // 1
pq.poll(); // 1
pq.poll(); // 2
pq.poll(); // 3
```

也就是说：

```text
PriorityQueue 只有每次 poll 出来的顺序是有序的
内部数组本身不是完整排序结果
```

## 和排序有什么区别

如果只是要完整排序，直接 sort 更自然：

```java
Arrays.sort(nums);
```

排序的特点是：

```text
把所有元素从小到大排好
```

时间复杂度：

```text
O(n log n)
```

但是很多题不需要完整排序。

比如：

```text
找最大的 k 个
找最小的 k 个
找第 k 大
找第 k 小
找离原点最近的 k 个点
```

这些题只关心前 `k` 个，不关心剩下的元素之间怎么排序。

这时候 heap 就有意义：

```text
不把所有元素完整排序
只维护当前最有用的一部分
```

## 一个直觉例子

假设有 100 万个学生成绩，但是只要最高的 10 个。

排序做法是：

```text
把 100 万个成绩全部排名
然后取前 10 个
```

Heap 做法是：

```text
手里只保留当前最高的 10 个
新的成绩来了，如果比这 10 个里面最低的还低，就直接忽略
```

所以：

```text
sort 是全班完整排名
heap 是只维护前 k 名
```

这就是为什么 Top K 问题常用 heap。

## 小顶堆

Java 默认的 `PriorityQueue` 是小顶堆。

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

特点：

```text
peek() 是最小值
poll() 弹出最小值
```

例子：

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

minHeap.offer(5);
minHeap.offer(1);
minHeap.offer(3);

System.out.println(minHeap.peek()); // 1
System.out.println(minHeap.poll()); // 1
System.out.println(minHeap.poll()); // 3
System.out.println(minHeap.poll()); // 5
```

## 大顶堆

Java 没有单独的 `MaxHeap` 类。

想要大顶堆，需要自己写 comparator：

```java
PriorityQueue<Integer> maxHeap =
    new PriorityQueue<>((a, b) -> Integer.compare(b, a));
```

特点：

```text
peek() 是最大值
poll() 弹出最大值
```

例子：

```java
PriorityQueue<Integer> maxHeap =
    new PriorityQueue<>((a, b) -> Integer.compare(b, a));

maxHeap.offer(5);
maxHeap.offer(1);
maxHeap.offer(3);

System.out.println(maxHeap.peek()); // 5
System.out.println(maxHeap.poll()); // 5
System.out.println(maxHeap.poll()); // 3
System.out.println(maxHeap.poll()); // 1
```

注意不要写：

```java
(a, b) -> b - a
```

更推荐：

```java
(a, b) -> Integer.compare(b, a)
```

因为 `b - a` 在极端情况下可能整数溢出。

## 常用操作

| 操作 | 含义 | 时间复杂度 |
|---|---|---|
| `offer(x)` | 加入一个元素 | `O(log n)` |
| `poll()` | 弹出堆顶元素 | `O(log n)` |
| `peek()` | 查看堆顶元素 | `O(1)` |
| `size()` | 查看元素数量 | `O(1)` |

其中最重要的是：

```text
peek() 看当前最优先的元素
poll() 删除当前最优先的元素
offer() 加入新元素并调整堆
```

## Top K 模板：找最大的 k 个

找最大的 `k` 个元素，用大小为 `k` 的小顶堆。

为什么是小顶堆？

因为我们要保留最大的 `k` 个。

在这 `k` 个里面，最没用的是最小的那个。

所以堆顶放最小值，方便随时踢掉。

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

for (int num : nums) {
    minHeap.offer(num);

    if (minHeap.size() > k) {
        minHeap.poll();
    }
}
```

循环结束后：

```text
minHeap 里保存的是最大的 k 个数
minHeap.peek() 是这 k 个数里面最小的
```

所以如果题目问第 `k` 大：

```java
return minHeap.peek();
```

## Top K 模板：找最小的 k 个

找最小的 `k` 个元素，用大小为 `k` 的大顶堆。

为什么是大顶堆？

因为我们要保留最小的 `k` 个。

在这 `k` 个里面，最没用的是最大的那个。

所以堆顶放最大值，方便随时踢掉。

```java
PriorityQueue<Integer> maxHeap =
    new PriorityQueue<>((a, b) -> Integer.compare(b, a));

for (int num : nums) {
    maxHeap.offer(num);

    if (maxHeap.size() > k) {
        maxHeap.poll();
    }
}
```

循环结束后：

```text
maxHeap 里保存的是最小的 k 个数
maxHeap.peek() 是这 k 个数里面最大的
```

## 以 973 为例

LeetCode 973 要找离原点最近的 `k` 个点。

本质是：

```text
找距离最小的 k 个点
```

距离可以用：

```text
x * x + y * y
```

不需要开根号，因为比较大小时结果一样。

这题可以直接排序：

```java
Arrays.sort(points, (a, b) -> {
    int da = a[0] * a[0] + a[1] * a[1];
    int db = b[0] * b[0] + b[1] * b[1];
    return Integer.compare(da, db);
});

return Arrays.copyOfRange(points, 0, k);
```

这个做法完全正确。

复杂度：

```text
O(n log n)
```

如果用 heap，只维护最近的 `k` 个点：

```java
class Solution {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) ->
            Integer.compare(distance(b), distance(a))
        );

        for (int[] point : points) {
            maxHeap.offer(point);

            if (maxHeap.size() > k) {
                maxHeap.poll();
            }
        }

        int[][] ans = new int[k][2];

        for (int i = 0; i < k; i++) {
            ans[i] = maxHeap.poll();
        }

        return ans;
    }

    private int distance(int[] point) {
        return point[0] * point[0] + point[1] * point[1];
    }
}
```

这里用大顶堆。

原因是：

```text
我要保留最近的 k 个点
在这 k 个点里，最应该被淘汰的是距离最远的那个
所以让堆顶变成“当前 k 个点里最远的点”
```

每次堆大小超过 `k`，就：

```java
maxHeap.poll();
```

弹掉当前最远的点。

最后堆里剩下的就是最近的 `k` 个点。

复杂度：

```text
O(n log k)
```

## 怎么判断用小顶堆还是大顶堆

记住一句话：

```text
堆顶放“最容易被淘汰”的那个元素。
```

如果要找最大的 `k` 个：

```text
保留大的
淘汰小的
所以用小顶堆
```

如果要找最小的 `k` 个：

```text
保留小的
淘汰大的
所以用大顶堆
```

## sort 和 heap 怎么选

| 场景 | 推荐 |
|---|---|
| 要完整顺序 | `sort` |
| 只要前 `k` 个 | `heap` |
| `k` 接近 `n` | `sort` 也很好 |
| `k` 远小于 `n` | `heap` 更省 |
| 想先快速 AC | `sort` 更简单 |
| 面试想展示优化 | `heap` 更合适 |

复杂度对比：

```text
sort: O(n log n)
heap: O(n log k)
```

所以不是说 PriorityQueue 一定比排序好。

真正的区别是：

```text
sort 会完整排序全部元素
heap 可以只维护前 k 个候选
```

## 记忆

```text
PriorityQueue 默认小顶堆。

小顶堆：
peek() 是最小值。
适合维护最大的 k 个，因为最小的最容易被踢掉。

大顶堆：
peek() 是最大值。
适合维护最小的 k 个，因为最大的最容易被踢掉。

Top K 问题：
堆大小通常控制在 k。
超过 k 就 poll。
堆顶永远放当前最该被淘汰的元素。
```

