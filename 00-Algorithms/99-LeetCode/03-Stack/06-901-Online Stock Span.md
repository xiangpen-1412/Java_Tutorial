# 06. Online Stock Span

## 题号

```text
LeetCode 901. Online Stock Span
```

## 核心知识

这题用 Monotonic Stack。

本质是：

```text
对今天的价格，找左边最近的一个 > 今天价格的价格。
```

也就是：

```text
Previous Greater Element
```

找到这个更高价格的位置后，今天的 span 就是：

```text
今天 index - 左边最近更高价格的 index
```

如果左边没有更高价格，说明前面所有天都 `<=` 今天价格，那么：

```text
span = 今天 index + 1
```

## 题目意思

每次调用：

```java
next(price)
```

表示新的一天来了，今天股票价格是 `price`。

需要返回今天的 span。

span 的意思是：

```text
从今天开始往前数，连续多少天的价格都 <= 今天价格。
```

注意：

```text
包括今天自己。
遇到第一个 > 今天价格的天，就停止。
```

## 例子

```text
prices = [100, 80, 60, 70, 65, 72, 69, 80, 71]
```

对应 span：

```text
[1, 1, 1, 2, 1, 4, 1, 7, 1]
```

解释几个：

```text
70:
往前看 60 <= 70，再看 80 > 70，停止。
span = 2

72:
往前看 65 <= 72，70 <= 72，60 <= 72，再看 80 > 72，停止。
span = 4

80:
往前看 69 <= 80，72 <= 80，65 <= 80，70 <= 80，60 <= 80，80 <= 80，再看 100 > 80，停止。
span = 7
```

## 你的写法

你的写法是保存所有历史价格，并用一个栈保存下标。

栈里保存的是：

```text
还可能成为未来某一天 Previous Greater 的价格下标。
```

当新价格 `price` 来了：

```java
while (stackIndex >= 0 && price >= prices[stack[stackIndex]]) {
    stackIndex--;
}
```

这一步是在弹掉所有：

```text
<= 当前 price 的历史价格
```

因为这些价格不可能成为当前价格的 Previous Greater。

弹完后：

```text
如果栈不空，栈顶就是左边最近的 > price 的位置。
如果栈为空，说明左边没有任何价格 > price。
```

## 为什么栈顶就是最近的更高价格

因为我们是按时间顺序从左往右处理。

栈里存的是历史下标。

越靠近栈顶：

```text
时间越新
离今天越近
```

当所有 `<= 当前 price` 的元素都被弹掉后，栈顶剩下的第一个元素满足：

```text
prices[stackTop] > price
```

并且它是离今天最近的一个更高价格。

所以 span 可以直接用距离计算：

```text
span = 今天 index - stackTop
```

如果没有 previous greater：

```text
span = 今天 index + 1
```

## 代码

```java
class StockSpanner {
    int[] stack;
    int stackIndex;
    int[] prices;
    int pricesIndex;

    public StockSpanner() {
        stack = new int[10000];
        stackIndex = -1;
        prices = new int[10000];
        pricesIndex = -1;
    }

    public int next(int price) {
        pricesIndex++;
        prices[pricesIndex] = price;

        while (stackIndex >= 0 && price >= prices[stack[stackIndex]]) {
            stackIndex--;
        }

        int res = 1;

        if (stackIndex >= 0) {
            if (prices[stack[stackIndex]] > price) {
                res = pricesIndex - stack[stackIndex];
            }
        } else {
            res = pricesIndex + 1;
        }

        stackIndex++;
        stack[stackIndex] = pricesIndex;

        return res;
    }
}
```

## 推演

```text
prices = [31, 41, 48, 59, 79]
```

这些价格一路上涨。

处理 `31`：

```text
左边没有更高价格
span = 0 + 1 = 1
```

处理 `41`：

```text
31 <= 41，被弹掉
左边没有更高价格
span = 1 + 1 = 2
```

处理 `48`：

```text
41 <= 48，被弹掉
左边没有更高价格
span = 2 + 1 = 3
```

处理 `59`：

```text
48 <= 59，被弹掉
左边没有更高价格
span = 3 + 1 = 4
```

处理 `79`：

```text
59 <= 79，被弹掉
左边没有更高价格
span = 4 + 1 = 5
```

答案：

```text
[1, 2, 3, 4, 5]
```

## 单调性

这题找的是：

```text
Previous Greater
```

所以栈维护的是：

```text
单调递减栈
```

也就是栈里对应的价格从栈底到栈顶保持递减。

当当前价格更大或相等时：

```text
price >= prices[stackTop]
```

栈顶不可能再挡住当前价格，所以弹掉。

注意这里是 `>=`，因为 span 允许：

```text
历史价格 <= 今天价格
```

相等也应该被今天覆盖。

## 复杂度

每个价格下标最多：

```text
入栈一次
出栈一次
```

所以总时间复杂度：

```text
O(n)
```

每次 `next` 的均摊时间复杂度：

```text
O(1)
```

空间复杂度：

```text
O(n)
```

## 记忆

```text
Online Stock Span:

span = 从今天往前连续多少天 <= 今天价格。

换句话说：
找左边最近的 > 今天价格的位置。

有 previous greater:
span = todayIndex - previousGreaterIndex

没有 previous greater:
span = todayIndex + 1

找 Previous Greater:
用单调递减栈。
弹掉所有 <= 当前价格的元素。
```

