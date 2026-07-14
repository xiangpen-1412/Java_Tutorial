# 05. Final Prices With a Special Discount in a Shop

## 题号

```text
LeetCode 1475. Final Prices With a Special Discount in a Shop
```

## 核心知识

这题用 Monotonic Stack。

本质是：

```text
对每个商品，找它右边第一个价格 <= 当前价格的商品。
```

也就是：

```text
Next Smaller or Equal Element
```

找到后：

```text
最终价格 = 当前价格 - 折扣价格
```

如果右边没有这样的商品：

```text
最终价格 = 当前价格
```

## 题目意思

给一个数组：

```text
prices
```

其中：

```text
prices[i] 表示第 i 个商品的价格
```

买第 `i` 个商品时，要从它右边找第一个满足条件的商品 `j`：

```text
j > i
prices[j] <= prices[i]
```

这个 `prices[j]` 就是折扣金额。

注意：

```text
不是找右边最小的价格。
而是找右边第一个 <= 当前价格的价格。
```

## 例子

```text
prices = [8, 4, 6, 2, 3]
```

第 0 个商品：

```text
price = 8
右边第一个 <= 8 的是 4
final = 8 - 4 = 4
```

第 1 个商品：

```text
price = 4
右边是 [6, 2, 3]
第一个 <= 4 的是 2
final = 4 - 2 = 2
```

第 2 个商品：

```text
price = 6
右边第一个 <= 6 的是 2
final = 6 - 2 = 4
```

第 3 个商品：

```text
price = 2
右边只有 3，不满足 <= 2
final = 2
```

第 4 个商品：

```text
右边没有商品
final = 3
```

答案：

```text
[4, 2, 4, 2, 3]
```

## 为什么用单调栈

如果暴力做，对每个商品都往右找第一个 `<=` 它的价格：

```text
最坏时间复杂度 O(n^2)
```

单调栈可以做到：

```text
O(n)
```

因为每个下标最多：

```text
入栈一次
出栈一次
```

## 你的写法

你的写法是从左往右扫。

栈里保存的是：

```text
还没有找到折扣的商品下标
```

当当前价格 `prices[i]` 小于等于栈顶商品价格时：

```text
prices[i] <= prices[stackTop]
```

说明当前商品就是栈顶商品右边第一个可用折扣。

于是：

```text
res[stackTop] = prices[stackTop] - prices[i]
```

然后把这个下标弹出。

## 为什么当前元素是“第一个”折扣

因为我们是从左往右扫描。

对于栈里的某个旧商品来说，在遇到当前 `i` 之前，它一直没有遇到符合条件的折扣。

现在第一次遇到：

```text
prices[i] <= prices[oldIndex]
```

所以当前 `prices[i]` 就是它右边第一个可用折扣。

## 栈的含义

栈里不是放价格，而是放下标：

```text
stack 存 index
```

原因是：

```text
要回到 res[index] 里填写最终价格
```

并且要比较：

```text
prices[i] <= prices[stack[stackIndex]]
```

## 代码

```java
import java.util.Arrays;

class Solution {
    public int[] finalPrices(int[] prices) {
        int[] stack = new int[prices.length];
        int[] res = new int[prices.length];
        Arrays.fill(res, -1);

        stack[0] = 0;
        int stackIndex = 0;

        for (int i = 1; i < prices.length; i++) {
            while (stackIndex >= 0 && prices[i] <= prices[stack[stackIndex]]) {
                res[stack[stackIndex]] = prices[stack[stackIndex]] - prices[i];
                stackIndex--;
            }

            stackIndex++;
            stack[stackIndex] = i;
        }

        for (int i = 0; i < res.length; i++) {
            if (res[i] == -1) {
                res[i] = prices[i];
            }
        }

        return res;
    }
}
```

## 推演

```text
prices = [8, 4, 6, 2, 3]
```

开始：

```text
stack = [0]   // prices[0] = 8 还没找到折扣
res = [-1, -1, -1, -1, -1]
```

处理 `i = 1`：

```text
prices[1] = 4
4 <= 8
```

所以 `4` 是 `8` 的折扣：

```text
res[0] = 8 - 4 = 4
pop 0
push 1
```

处理 `i = 2`：

```text
prices[2] = 6
6 <= 4 不成立
push 2
```

处理 `i = 3`：

```text
prices[3] = 2
2 <= 6，res[2] = 6 - 2 = 4，pop 2
2 <= 4，res[1] = 4 - 2 = 2，pop 1
push 3
```

处理 `i = 4`：

```text
prices[4] = 3
3 <= 2 不成立
push 4
```

最后还有下标 `3` 和 `4` 没找到折扣。

所以它们保持原价：

```text
res[3] = prices[3] = 2
res[4] = prices[4] = 3
```

答案：

```text
[4, 2, 4, 2, 3]
```

## 单调性

这题可以理解为：

```text
找右边第一个 <= 当前值
```

用的是：

```text
单调递增栈
```

这里你的栈里存的是下标，但对应的价格保持一种“还没被解决的递增关系”。

当前价格如果小于等于栈顶价格，就可以解决栈顶。

所以弹出条件是：

```java
prices[i] <= prices[stack[stackIndex]]
```

注意这里是 `<=`，因为题目允许：

```text
prices[j] <= prices[i]
```

相等也可以作为折扣。

## 复杂度

```text
时间复杂度: O(n)
空间复杂度: O(n)
```

虽然里面有 `while`，但每个商品下标最多：

```text
入栈一次
出栈一次
```

所以总时间是 `O(n)`。

## 记忆

```text
Final Prices:

找右边第一个 <= 当前价格。
当前元素可以解决前面还没找到折扣的商品。
栈里存还没找到折扣的下标。

prices[i] <= prices[stackTop]:
当前价格就是 stackTop 的折扣。

没找到折扣：
保持原价。
```

