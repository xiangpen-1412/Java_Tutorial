# 08. Largest Rectangle in Histogram

## 题号

```text
LeetCode 84. Largest Rectangle in Histogram
```

## 核心知识

```text
Monotonic Stack
Previous Smaller Element
Next Smaller Element
```

这道题真正难的地方不是代码，而是思路转换：

```text
不要只想着枚举区间。
要换成枚举“哪一根柱子作为这个矩形的最矮柱子”。
```

---

## 暴力思路

矩形一定来自一段连续区间。

如果区间是：

```text
[left ... right]
```

那么这个区间能形成的矩形高度是：

```text
这个区间里的最小高度
```

面积是：

```text
area = minHeight * width
area = 区间最小高度 * (right - left + 1)
```

所以暴力法可以这样想：

```text
枚举 left
枚举 right
维护 [left ... right] 的最小高度
计算面积
更新最大面积
```

这个思路是对的，它能帮助看清楚题目的本质。

---

## 暴力法重复计算在哪里

暴力法一直在问同一个问题：

```text
这个区间里谁最矮？
```

很多柱子会在不同区间里被反复比较。

例如：

```text
heights = [2, 1, 5, 6, 2, 3]
```

暴力法会枚举：

```text
[5]
[5, 6]
[1, 5]
[2, 1, 5]
[5, 6, 2]
[1, 5, 6, 2]
...
```

同一根柱子会被反复拿来判断它是不是区间最小值。

所以优化的方向是：

```text
不要每次重新找区间最小值。
反过来，直接枚举每根柱子作为最矮柱子时，它最多能扩多宽。
```

---

## 关键思路转换

原来的问题是：

```text
枚举一个区间，然后找这个区间里的最矮柱子。
```

换一个角度：

```text
枚举一根柱子，把它当成最矮柱子，然后找它能向左向右扩展到哪里。
```

为什么这样不会漏答案？

因为任何一个合法矩形，都一定有一根最矮柱子。

这个矩形的高度，就是那根最矮柱子的高度。

所以最大矩形一定会在遍历到它自己的最矮柱子时被算出来。

---

## 用例理解

```text
heights = [2, 1, 5, 6, 2, 3]
```

最大矩形是：

```text
[5, 6]
```

注意面积不是：

```text
5 * 6
```

柱子的高度不能相乘。

矩形面积永远是：

```text
矩形高度 * 矩形宽度
```

对于 `[5, 6]`：

```text
高度 = min(5, 6) = 5
宽度 = 2
面积 = 5 * 2 = 10
```

当遍历到高度为 `5` 的柱子时：

```text
左边第一个比 5 小的是 1
右边第一个比 5 小的是 2
```

所以 `5` 能撑起来的最大连续范围就是：

```text
[5, 6]
```

面积正好是：

```text
5 * 2 = 10
```

---

## 每根柱子要找什么

对于每个位置 `i`，把：

```text
heights[i]
```

当成矩形高度。

它能向左向右扩展的范围，取决于：

```text
左边第一个比 heights[i] 小的位置
右边第一个比 heights[i] 小的位置
```

记为：

```text
previous[i] = i 左边第一个更小柱子的下标
next[i] = i 右边第一个更小柱子的下标
```

那么它能覆盖的宽度是：

```text
width = next[i] - previous[i] - 1
```

面积是：

```text
area = heights[i] * width
```

为什么要减 `1`？

因为 `previous[i]` 和 `next[i]` 本身是比当前柱子更矮的位置，不能包含在矩形里面。

真正能包含的是它们中间的柱子。

---

## 单调栈在这里做什么

单调栈用来快速找：

```text
previous smaller
next smaller
```

栈里存的是：

```text
下标
```

不是高度。

因为最后算宽度需要下标：

```text
next[i] - previous[i] - 1
```

用 `int[]` 模拟 stack：

```java
int[] stack = new int[n];
int top = -1;

// push
stack[++top] = i;

// pop
top--;

// peek
int index = stack[top];

// not empty
top >= 0;
```

---

## Previous Smaller

从左往右扫。

目标：

```text
找每个 i 左边第一个比 heights[i] 小的下标
```

代码：

```java
public int[] findPreviousSmaller(int[] heights) {
    int n = heights.length;
    int[] previous = new int[n];
    Arrays.fill(previous, -1);

    int[] stack = new int[n];
    int top = -1;

    for (int i = 0; i < n; i++) {
        while (top >= 0 && heights[stack[top]] >= heights[i]) {
            top--;
        }

        if (top >= 0) {
            previous[i] = stack[top];
        }

        stack[++top] = i;
    }

    return previous;
}
```

关键句：

```java
while (top >= 0 && heights[stack[top]] >= heights[i]) {
    top--;
}
```

意思是：

```text
如果栈顶柱子比当前柱子高或者一样高，
它就不能作为当前柱子的左边第一个更小柱子。
```

所以要弹掉。

---

## Next Smaller

从右往左扫。

目标：

```text
找每个 i 右边第一个比 heights[i] 小的下标
```

代码：

```java
public int[] findNextSmaller(int[] heights) {
    int n = heights.length;
    int[] next = new int[n];
    Arrays.fill(next, n);

    int[] stack = new int[n];
    int top = -1;

    for (int i = n - 1; i >= 0; i--) {
        while (top >= 0 && heights[stack[top]] >= heights[i]) {
            top--;
        }

        if (top >= 0) {
            next[i] = stack[top];
        }

        stack[++top] = i;
    }

    return next;
}
```

如果右边没有更小的柱子，默认是：

```text
n
```

这样表示可以一直扩到数组最右边。

---

## 最简单完整代码

```java
import java.util.*;

class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;

        int[] previous = findPreviousSmaller(heights);
        int[] next = findNextSmaller(heights);

        int area = 0;

        for (int i = 0; i < n; i++) {
            int width = next[i] - previous[i] - 1;
            int currentArea = heights[i] * width;
            area = Math.max(area, currentArea);
        }

        return area;
    }

    public int[] findPreviousSmaller(int[] heights) {
        int n = heights.length;
        int[] previous = new int[n];
        Arrays.fill(previous, -1);

        int[] stack = new int[n];
        int top = -1;

        for (int i = 0; i < n; i++) {
            while (top >= 0 && heights[stack[top]] >= heights[i]) {
                top--;
            }

            if (top >= 0) {
                previous[i] = stack[top];
            }

            stack[++top] = i;
        }

        return previous;
    }

    public int[] findNextSmaller(int[] heights) {
        int n = heights.length;
        int[] next = new int[n];
        Arrays.fill(next, n);

        int[] stack = new int[n];
        int top = -1;

        for (int i = n - 1; i >= 0; i--) {
            while (top >= 0 && heights[stack[top]] >= heights[i]) {
                top--;
            }

            if (top >= 0) {
                next[i] = stack[top];
            }

            stack[++top] = i;
        }

        return next;
    }
}
```

---

## 复杂度

```text
Time Complexity: O(n)
Space Complexity: O(n)
```

为什么是 `O(n)`？

每个下标最多：

```text
入栈一次
出栈一次
```

所以整体不是 `O(n^2)`。

---

## 复盘时不要先背代码

先问自己这些问题：

```text
1. 为什么暴力法是枚举区间？
2. 区间的矩形高度为什么由最矮柱子决定？
3. 为什么可以反过来枚举每根柱子作为最矮柱子？
4. 为什么这样不会漏掉最大矩形？
5. 对于每根柱子，为什么要找左边第一个更小和右边第一个更小？
6. width = next - previous - 1 为什么要减 1？
7. 栈里为什么存下标，不存高度？
8. while 里面为什么是 >=？
9. previous 默认为什么是 -1？
10. next 默认为什么是 n？
```

这些问题能讲清楚，这题就不是靠背了。

