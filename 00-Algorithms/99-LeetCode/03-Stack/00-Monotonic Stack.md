# Monotonic Stack

## Monotonic Stack 是什么

Monotonic Stack 叫做单调栈。

它不是一种新的数据结构，本质上还是 Stack，只是我们在使用它的时候，刻意让栈里的元素保持某种单调性：

```text
单调递增栈：栈底到栈顶越来越大
单调递减栈：栈底到栈顶越来越小
```

它最常用来解决这一类问题：

```text
对每个元素，找它左边或右边第一个比它大 / 小的元素
```

常见问题有：

```text
Next Greater Element：右边第一个更大的元素
Next Smaller Element：右边第一个更小的元素
Previous Greater Element：左边第一个更大的元素
Previous Smaller Element：左边第一个更小的元素
```

如果用暴力做法，通常要对每个元素继续往左或往右扫描，最坏是 `O(n^2)`。

单调栈可以把这类问题优化到 `O(n)`。

---

## Previous Smaller Element

Previous Smaller Element 的意思是：

> 对数组里的每个元素，找到它左边第一个比它小的元素。

如果左边没有比它小的元素，就记为 `-1`。

例如：

```text
nums = [4, 5, 2, 10, 8]
```

答案是：

```text
[-1, 4, -1, 2, 2]
```

逐个看：

```text
4 左边没有元素，所以是 -1
5 左边第一个比它小的是 4
2 左边没有比它小的，所以是 -1
10 左边第一个比它小的是 2
8 左边第一个比它小的是 2
```

注意 `8` 的左边是：

```text
[4, 5, 2, 10]
```

从 `8` 往左看，先看到 `10`，但是 `10 > 8`，不符合。

再看到 `2`，`2 < 8`，所以答案是 `2`。

---

## Previous Smaller Element 的算法

从左往右遍历数组，用一个栈保存：

> 可能成为后面元素 Previous Smaller Element 的数。

这个栈要保持单调递增。

为什么？

假设当前元素是 `nums[i]`。

如果栈顶元素 `>= nums[i]`，那么它一定不可能是当前元素的 Previous Smaller Element。

所以要把它弹出去：

```java
while (!stack.isEmpty() && stack.peek() >= nums[i]) {
    stack.pop();
}
```

弹完之后：

```java
ans[i] = stack.isEmpty() ? -1 : stack.peek();
```

如果栈为空，说明左边没有比当前元素小的数。

如果栈不为空，栈顶就是左边最近的、比当前元素小的数。

最后把当前元素放进栈里：

```java
stack.push(nums[i]);
```

因为当前元素也可能成为后面元素的 Previous Smaller Element。

---

## Previous Smaller Element 代码

```java
import java.util.*;

class Solution {
    public int[] previousSmallerElement(int[] nums) {
        int n = nums.length;
        int[] ans = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && stack.peek() >= nums[i]) {
                stack.pop();
            }

            ans[i] = stack.isEmpty() ? -1 : stack.peek();

            stack.push(nums[i]);
        }

        return ans;
    }
}
```

---

## Previous Smaller Element 推演

数组：

```text
nums = [4, 5, 2, 10, 8]
```

### 处理 4

```text
stack = []
```

栈为空，说明 `4` 左边没有更小元素。

```text
ans[0] = -1
push 4
stack = [4]
```

### 处理 5

```text
stack = [4]
```

栈顶 `4 < 5`，所以 `4` 就是 `5` 左边最近的更小元素。

```text
ans[1] = 4
push 5
stack = [4, 5]
```

### 处理 2

```text
stack = [4, 5]
```

栈顶 `5 >= 2`，弹出 `5`。

栈顶 `4 >= 2`，弹出 `4`。

栈为空，说明 `2` 左边没有更小元素。

```text
ans[2] = -1
push 2
stack = [2]
```

### 处理 10

```text
stack = [2]
```

栈顶 `2 < 10`，所以 `2` 就是 `10` 左边最近的更小元素。

```text
ans[3] = 2
push 10
stack = [2, 10]
```

### 处理 8

```text
stack = [2, 10]
```

栈顶 `10 >= 8`，弹出 `10`。

栈顶 `2 < 8`，所以 `2` 就是 `8` 左边最近的更小元素。

```text
ans[4] = 2
push 8
stack = [2, 8]
```

最后答案：

```text
[-1, 4, -1, 2, 2]
```

---

## 为什么栈顶就是最近的

因为我们是从左往右遍历。

对于当前元素来说，栈里的元素都在它左边。

越靠近栈顶，说明这个元素出现得越晚，也就越靠近当前元素。

当我们把所有 `>= nums[i]` 的元素弹掉以后，剩下的栈顶元素满足两个条件：

```text
1. 它比当前元素小
2. 它离当前元素最近
```

所以它就是 Previous Smaller Element。

---

## Next Greater Element

Next Greater Element 的意思是：

> 对数组里的每个元素，找到它右边第一个比它大的元素。

如果右边没有比它大的元素，就记为 `-1`。

例如：

```text
nums = [2, 1, 2, 4, 3]
```

答案是：

```text
[4, 2, 4, -1, -1]
```

逐个看：

```text
2 右边第一个更大的是 4
1 右边第一个更大的是 2
2 右边第一个更大的是 4
4 右边没有更大的，所以是 -1
3 右边没有更大的，所以是 -1
```

---

## Next Greater Element 的算法

从左往右遍历数组。

栈里保存还没有找到 Next Greater Element 的元素下标。

当当前元素 `nums[i]` 比栈顶下标对应的元素更大时：

```text
nums[i] > nums[stack.peek()]
```

说明当前元素就是栈顶元素右边第一个更大的元素。

于是弹出栈顶，并记录答案：

```java
int index = stack.pop();
ans[index] = nums[i];
```

为什么是第一个更大的元素？

因为我们是从左往右遍历的。

栈顶元素在遇到当前元素之前，还没有遇到比它更大的数。

现在第一次遇到了，所以当前元素就是答案。

---

## Next Greater Element 代码

```java
import java.util.*;

class Solution {
    public int[] nextGreaterElement(int[] nums) {
        int n = nums.length;
        int[] ans = new int[n];
        Arrays.fill(ans, -1);

        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums[i] > nums[stack.peek()]) {
                int index = stack.pop();
                ans[index] = nums[i];
            }

            stack.push(i);
        }

        return ans;
    }
}
```

这里栈里放的是下标，不是值。

原因是我们要给 `ans[index]` 填答案，所以需要知道原数组中元素的位置。

---

## 怎么判断用哪种单调栈

可以先看问题方向：

```text
Previous：找左边，从左往右遍历，当前元素的答案来自栈顶
Next：找右边，从左往右遍历，当前元素负责解决栈里的旧元素
```

再看要找大还是小：

```text
找更小：弹掉所有 >= 当前元素的数
找更大：弹掉所有 <= 当前元素的数
```

简单记忆：

```text
Previous Smaller：维护单调递增栈
Previous Greater：维护单调递减栈
Next Greater：维护单调递减栈，当前大元素解决前面的小元素
Next Smaller：维护单调递增栈，当前小元素解决前面的大元素
```

---

## 复杂度

虽然代码里有 `while`，但每个元素最多：

```text
入栈一次
出栈一次
```

所以总时间复杂度是：

```text
O(n)
```

空间复杂度是：

```text
O(n)
```

---

## 一句话总结

Monotonic Stack 的核心是：

> 用一个保持单调性的栈，快速找到某个元素左边或右边第一个比它大 / 小的元素。

Previous Smaller Element 用单调递增栈。

Next Greater Element 用单调递减栈。
