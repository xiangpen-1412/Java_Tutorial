# 07. Koko Eating Bananas

## 题号

```text
LeetCode 875. Koko Eating Bananas
```

## 核心知识

这题用 Binary Search，但不是在数组 `piles` 里二分。

它属于：

```text
答案空间二分
```

也就是：

```text
在可能的答案 k 里面二分。
```

## 题目意思

`piles` 表示每一堆香蕉的数量。

比如：

```text
piles = [3, 6, 7, 11]
```

表示有 4 堆香蕉：

```text
第 1 堆：3 根
第 2 堆：6 根
第 3 堆：7 根
第 4 堆：11 根
```

`h` 表示 Koko 必须在 `h` 小时内吃完所有香蕉。

Koko 可以选择一个速度：

```text
k
```

含义是：

```text
每小时最多吃 k 根香蕉。
```

但每小时只能选择一堆香蕉吃。

如果这一堆少于 `k` 根，她会吃完这一堆，但这一小时不会继续吃下一堆。

## 返回什么

题目要求返回：

```text
最小的 k
```

也就是：

```text
最小的吃香蕉速度，使得 Koko 可以在 h 小时内吃完所有香蕉。
```

返回的是速度，不是小时数，也不是香蕉总数。

## 为什么是 Binary Search

这题没有直接说必须 `O(log n)`。

它的二分信号是：

```text
Return the minimum integer k such that ...
```

也就是：

```text
找最小的可行值。
```

速度 `k` 有单调性：

```text
k 越小，越难吃完。
k 越大，越容易吃完。
```

所以结果像这样：

```text
k:    1   2   3   4   5   6   ...
结果: 不行 不行 不行 可以 可以 可以 ...
```

题目要找的是：

```text
第一个可以的 k
```

这就是答案空间二分。

## 二分思考公式

```text
目标：找最小可行速度 k
候选：1 到 max(piles)
比较：check(k)，判断速度 k 能不能在 h 小时内吃完
排除：
  如果 k 可行，保留 k，继续找更小速度
  如果 k 不可行，排除 k 和更小速度
```

## 为什么速度范围是 1 到 max(piles)

最小速度：

```text
1
```

因为每小时至少要吃 1 根。

最大速度：

```text
max(piles)
```

因为速度达到最大那堆香蕉的数量时，每一堆最多 1 小时就能吃完。

所以答案一定在：

```text
[1, max(piles)]
```

## check(k) 怎么判断

对于某一堆香蕉 `num`，速度是 `k` 时，需要的小时数是：

```text
ceil(num / k)
```

因为只要有剩余香蕉，就还需要多花 1 小时。

比如：

```text
num = 11
k = 4
```

吃法：

```text
第 1 小时吃 4
第 2 小时吃 4
第 3 小时吃 3
```

所以需要：

```text
3 小时
```

不是：

```text
11 / 4 = 2
```

而是：

```text
ceil(11 / 4) = 3
```

## Java 向上取整

常用写法：

```java
int hours = (num + k - 1) / k;
```

也可以写成更直观的：

```java
int hours = num / k;
if (num % k != 0) {
    hours++;
}
```

这两个写法等价。

## 二分移动

假设当前速度是 `mid`。

如果：

```text
check(mid) == true
```

说明 `mid` 可以在 `h` 小时内吃完。

但是题目要最小速度，所以 `mid` 仍然可能是答案，要保留：

```java
right = mid;
```

如果：

```text
check(mid) == false
```

说明 `mid` 太慢了。

更小的速度也一定不行，所以排除 `mid` 和左边：

```java
left = mid + 1;
```

最后：

```text
left == right
```

这个位置就是最小可行速度。

## 代码

```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int right = 0;
        for (int pile : piles) {
            right = Math.max(right, pile);
        }

        int left = 1;

        while (left < right) {
            int mid = left + (right - left) / 2;

            if (check(mid, h, piles)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        return left;
    }

    public boolean check(int k, int h, int[] piles) {
        int sum = 0;

        for (int num : piles) {
            sum += (num + k - 1) / k;
        }

        return sum <= h;
    }
}
```

## 容易错的点

### 1. left 不能从 0 开始

速度 `k` 不能是 0。

否则会出现：

```text
除以 0
```

所以：

```java
int left = 1;
```

### 2. 不可行时要 left = mid + 1

如果速度 `mid` 不可行，说明它太慢。

那么：

```text
mid 和比 mid 更小的速度都不可能是答案。
```

所以要排除 `mid`：

```java
left = mid + 1;
```

不能写：

```java
left = mid;
```

否则可能死循环。

### 3. 可行时要 right = mid

如果速度 `mid` 可行，说明它可以吃完。

但题目要最小速度，所以 `mid` 仍然可能是答案。

因此要保留：

```java
right = mid;
```

### 4. 向上取整括号不能少

错误写法：

```java
sum += num + k - 1 / k;
```

因为 Java 会先算：

```text
1 / k
```

正确写法：

```java
sum += (num + k - 1) / k;
```

### 5. 判断条件是 <= h

题目要求在 `h` 小时内吃完。

刚好等于 `h` 也算可以。

所以：

```java
return sum <= h;
```

不是：

```java
return sum < h;
```

## 复杂度

```text
时间复杂度: O(n log m)
```

其中：

```text
n = piles.length
m = max(piles)
```

每次 `check(k)` 要遍历所有堆，时间是 `O(n)`。

二分速度范围 `[1, max(piles)]`，次数是 `O(log m)`。

空间复杂度：

```text
O(1)
```

## 记忆

```text
Koko Eating Bananas:

不是在数组里二分。
是在答案 k 里面二分。

题目问最小可行速度：
小速度不行，大速度可以。

check(k):
计算用速度 k 吃完所有香蕉需要多少小时。

num 这一堆需要：
(num + k - 1) / k

可行：right = mid
不可行：left = mid + 1
```

