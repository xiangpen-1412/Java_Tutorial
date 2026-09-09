# 02. Subarray Sum Equals K

## 题号

```text
LeetCode 560. Subarray Sum Equals K
```

## 1. 这题学到的核心

这题学到的是：

```text
连续子数组的和，可以用两个 prefix sum 的差表示。
```

如果：

```text
prefix[j] - prefix[i] = k
```

说明：

```text
i 后面到 j 这一段连续子数组的和等于 k。
```

所以这题不是在找两个数字相加等于 `k`。

它是在找：

```text
两个 prefix sum 的差等于 k。
```

## 2. 题目要什么？

题目给：

```text
nums
k
```

要求返回：

```text
有多少个连续子数组的和等于 k。
```

注意：

```text
返回的是数量，不是返回这些子数组。
```

并且 subarray 必须连续。

## 3. 暴力思路

最直接的想法是：

```text
枚举 left
枚举 right
计算 nums[left...right] 的和
如果等于 k，答案 +1
```

如果每次都重新加区间和，会很慢。

用 prefix sum 后，区间和可以 O(1) 得到。

但如果还是枚举所有 `left` 和 `right`：

```text
时间复杂度仍然是 O(n^2)
```

所以这题还要继续优化：

```text
不要枚举两个边界。
```

## 4. 从枚举两个边界到固定当前 right

换一个角度：

```text
当我走到当前位置 right 时，有多少个以前的 left 可以和我组成 sum = k？
```

设当前累计和是：

```text
currentSum
```

如果存在一个以前的累计和：

```text
previousSum
```

满足：

```text
currentSum - previousSum = k
```

那么：

```text
previousSum = currentSum - k
```

所以走到当前位置时，只需要问：

```text
以前出现过几次 currentSum - k？
```

这就是 HashMap 的作用。

## 5. HashMap 存什么？

HashMap 存：

```text
prefix sum -> 出现次数
```

不是存：

```text
prefix sum -> index
```

因为同一个 prefix sum 可能出现多次。

每出现一次，都代表一个不同的位置。

如果：

```text
currentSum - k
```

以前出现过多次，就说明有多个不同的起点可以形成合法 subarray。

## 6. 为什么不能先把所有 prefix sum 放进 map？

你之前的问题就出在这里。

如果先把所有 prefix sum 都放进 map，再统一查询，map 里会混着：

```text
过去的 prefix
当前的 prefix
未来的 prefix
```

但 subarray 有顺序要求：

```text
left <= right
```

prefix sum 也必须有前后顺序：

```text
后面的 prefix - 前面的 prefix = k
```

如果 map 里已经放了未来的 prefix，就可能数到不合法的组合。

如果 `k = 0`，还可能把当前 prefix 和自己配对：

```text
prefix - prefix = 0
```

这代表空区间，不是题目要的 non-empty subarray。

所以正确思路是：

```text
边走边查。
map 里只存当前位置之前出现过的 prefix sum。
```

## 7. 为什么需要 map.put(0, 1)？

这是这题最容易漏的点。

```java
map.put(0, 1);
```

表示：

```text
在数组开始之前，prefix sum = 0 出现过一次。
```

它是一个虚拟的起点。

比如：

```text
nums = [1, 1, 1]
k = 2
```

当走到 index 1 时：

```text
currentSum = 2
currentSum - k = 0
```

如果 map 里有：

```text
0 -> 1
```

就能数到：

```text
nums[0..1]
```

如果没有这个虚拟 prefix sum，就会漏掉从 index 0 开始的 subarray。

## 8. 正确的顺序

每一步的顺序是：

```text
1. 更新 currentSum
2. 查 map 里有没有 currentSum - k
3. 把当前 currentSum 放进 map
```

不能先 put 再查。

原因是：

```text
map 里应该只存当前位置之前的 prefix sum。
```

如果先 put 当前 prefix，再查，就可能把当前 prefix 和自己配对。

## 9. Pseudo Code

```text
map = {0 -> 1}
currentSum = 0
result = 0

for num in nums:
    currentSum += num

    need = currentSum - k
    result += map.getOrDefault(need, 0)

    map[currentSum] += 1

return result
```

## 10. 和 303 的区别

LeetCode 303 Range Sum Query 是：

```text
给定 left 和 right，问区间和是多少。
```

所以只需要 prefix sum。

LeetCode 560 是：

```text
不给 left 和 right，让你统计有多少个区间和为 k。
```

所以除了 prefix sum，还需要 HashMap 记录以前出现过的 prefix sum。

## 11. 这题的学习重点

这题真正要记住的是：

```text
1. subarray 是连续区间
2. 区间和 = 两个 prefix sum 的差
3. 固定当前 right，去找以前的 previous prefix
4. HashMap 存 prefix sum 出现次数
5. map 要边走边更新，不能先装完整个数组
6. map.put(0, 1) 是为了处理从 index 0 开始的区间
```

## 12. 记忆

```text
currentSum - previousSum = k
```

所以：

```text
previousSum = currentSum - k
```

每走到一个位置，就问：

```text
以前出现过几次 currentSum - k？
```

