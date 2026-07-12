# 739. Daily Temperatures

## 题目要你做什么

给你一个数组 `temperatures`，每个元素表示某一天的温度。

你要返回一个新数组 `answer`，其中：

```text
answer[i] = 从第 i 天开始，还要等多少天才会出现更高的温度
```

如果后面没有更高温度，就填 `0`。

## 例子

```text
temperatures = [73,74,75,71,69,72,76,73]
```

逐个看：

```text
第 0 天：73，下一天 74 更高，等 1 天
第 1 天：74，下一天 75 更高，等 1 天
第 2 天：75，要等到第 6 天 76，等 4 天
第 3 天：71，要等到第 5 天 72，等 2 天
第 4 天：69，要等到第 5 天 72，等 1 天
第 5 天：72，要等到第 6 天 76，等 1 天
第 6 天：76，后面没有更高温度，填 0
第 7 天：73，后面没有更高温度，填 0
```

所以答案是：

```text
[1,1,4,2,1,1,0,0]
```

## 为什么想到用 Stack

这题的关键问题是：

> 对每一天，找它右边第一个比它温度更高的日子。

这和 `Next Greater Element` 很像：

> 往右边找第一个更大的元素。

如果用暴力做法，就是每一天都往后扫：

```text
第 0 天往后找
第 1 天往后找
第 2 天往后找
...
```

这样会有很多重复查找，最坏是 `O(n^2)`。

Stack 的作用是保存：

> 还没有找到更高温度的那些天。

更准确地说，栈里放的是下标，不是温度。

因为题目最后要的是“等几天”，所以要用：

```text
当前下标 - 栈顶下标
```

## 单调栈思路

从左到右遍历温度数组。

当前温度是 `temperatures[i]`。

如果它比栈顶那一天的温度更高：

```text
temperatures[i] > temperatures[stack.peek()]
```

说明当前第 `i` 天，就是栈顶那一天等到的第一个更暖和的日子。

于是：

```text
answer[stack.peek()] = i - stack.peek()
```

然后把栈顶弹出来。

如果当前温度还可以解决更多之前的低温天，就继续弹。

最后把当前下标 `i` 放进栈里，等待未来某一天来解决它。

## 代码

```java
import java.util.*;

class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int[] answer = new int[temperatures.length];
        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < temperatures.length; i++) {
            while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
                int prevIndex = stack.pop();
                answer[prevIndex] = i - prevIndex;
            }

            stack.push(i);
        }

        return answer;
    }
}
```

## 栈里到底放了什么

栈里放的是“还没找到答案的天数下标”。

比如：

```text
temperatures = [73,74,75,71,69,72,76,73]
```

遍历到 `72` 的时候，它可以解决前面的：

```text
71
69
```

因为 `72` 比它们都高。

但是它不能解决 `75`，因为 `72` 不比 `75` 高。

所以 `75` 还要留在栈里继续等。

## 核心记忆

看到这种题：

```text
对每个元素，找右边第一个比它大的元素
```

就可以想到单调栈。

这题用 stack 的原因不是为了普通的先进后出，而是为了维护一批“还没找到右边更大值”的元素。

当前元素一旦比栈顶更大，就说明它找到了栈顶元素的答案。

