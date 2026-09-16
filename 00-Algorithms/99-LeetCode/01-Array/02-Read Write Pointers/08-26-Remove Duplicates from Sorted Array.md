# 26. Remove Duplicates from Sorted Array

## 题目核心

给定一个已经按照非递减顺序排列的数组：

```text
nums = [0,0,1,1,1,2,2,3,3,4]
```

要求直接修改原数组，使前 `k` 个位置保存所有不重复元素：

```text
[0,1,2,3,4,...]
```

返回唯一元素的数量：

```text
k = 5
```

数组后面的元素不重要，LeetCode 只检查前 `k` 个位置。

---

## 第一版：HashSet 去重

最开始的思路是使用 `HashSet` 判断当前数字是否第一次出现：

```java
public int removeDuplicates(int[] nums) {
    int nonDuplicateCount = 0;
    Set<Integer> set = new HashSet<>();

    for (int i = 0; i < nums.length; i++) {
        if (set.add(nums[i])) {
            nums[nonDuplicateCount] = nums[i];
            nonDuplicateCount++;
        }
    }

    return nonDuplicateCount;
}
```

### 这版为什么能工作

`HashSet.add()` 的返回值是：

```text
成功加入新元素 → true
元素已经存在 → false
```

因此：

```java
if (set.add(nums[i]))
```

可以判断当前数字是不是第一次出现。

第一次出现时，把它写到数组前面的有效区域：

```java
nums[nonDuplicateCount] = nums[i];
```

---

## HashSet 版本的问题

虽然时间复杂度是：

```text
O(n)
```

但空间复杂度是：

```text
O(n)
```

因为最坏情况下，所有数字都不同，需要把所有数字存入 `HashSet`。

而且 `HashSet<Integer>` 还存在额外开销：

```text
int 自动装箱成 Integer
哈希计算
哈希桶查询
对象存储
扩容
```

更重要的是：

> 这个解法没有利用数组已经排序的条件。

---

# 关键条件：数组已经排序

题目明确说明：

```text
sorted in non-decreasing order
```

因为数组有序，所以相同元素一定连续出现：

```text
[0,0,1,1,1,2,2,3,3,4]
```

因此不需要记录所有出现过的数字。

判断当前数字是否重复，只需要比较：

```text
当前数字
和
上一个已经保留的唯一数字
```

这使得 `HashSet` 可以被完全去掉。

---

# 第二版：快慢双指针

最终代码：

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int left = 0;

        for (int right = 1; right < nums.length; right++) {
            if (nums[right] != nums[left]) {
                left++;
                nums[left] = nums[right];
            }
        }

        return left + 1;
    }
}
```

---

# 两个指针分别负责什么

## `right`：扫描指针

```java
for (int right = 1; right < nums.length; right++)
```

`right` 负责从左到右检查数组中的每个元素。

它的任务是：

```text
寻找下一个新的、没有重复的数字
```

---

## `left`：有效区域末尾

```java
int left = 0;
```

`left` 指向：

```text
目前最后一个已经保留下来的唯一元素
```

例如：

```text
nums = [0,0,1,1,1,2]
```

当数组前面已经整理成：

```text
[0,1,...]
```

此时：

```text
left = 1
nums[left] = 1
```

说明前 `left + 1` 个位置是有效区域。

---

# 为什么比较 `nums[right]` 和 `nums[left]`

代码：

```java
if (nums[right] != nums[left])
```

因为数组已经排序。

如果：

```text
nums[right] == nums[left]
```

说明当前数字是重复元素，直接跳过。

如果：

```text
nums[right] != nums[left]
```

说明发现了一个新的唯一元素。

此时先扩大有效区域：

```java
left++;
```

再把新元素写入：

```java
nums[left] = nums[right];
```

---

# 执行过程

假设：

```text
nums = [0,0,1,1,1,2]
```

初始状态：

```text
left = 0
nums[left] = 0
```

第一个元素天然保留，所以 `right` 从 `1` 开始。

---

## right = 1

```text
nums[right] = 0
nums[left]  = 0
```

相同，说明重复：

```text
跳过
```

---

## right = 2

```text
nums[right] = 1
nums[left]  = 0
```

不同，发现新元素：

```java
left++;
nums[left] = nums[right];
```

数组前面变成：

```text
[0,1,...]
```

此时：

```text
left = 1
```

---

## right = 3

```text
nums[right] = 1
nums[left]  = 1
```

相同，跳过。

---

## right = 4

仍然是：

```text
1 == 1
```

继续跳过。

---

## right = 5

```text
nums[right] = 2
nums[left]  = 1
```

不同，保存：

```text
[0,1,2,...]
```

最终：

```text
left = 2
```

唯一元素数量是：

```text
left + 1 = 3
```

---

# 为什么返回 `left + 1`

`left` 是数组下标，下标从 `0` 开始。

如果有效区域是：

```text
nums[0 ... left]
```

元素数量就是：

```text
left - 0 + 1
```

也就是：

```java
left + 1
```

例如：

```text
left = 4
```

有效下标是：

```text
0,1,2,3,4
```

一共有 5 个元素。

---

# 为什么 `right` 从 1 开始

数组的第一个元素不需要比较。

因为无论它是什么，它都是第一个出现的元素，天然属于唯一元素。

所以：

```java
int left = 0;
```

表示第一个元素已经放在有效区域中。

扫描指针从第二个元素开始：

```java
int right = 1;
```

---

# 为什么不需要真正删除数组元素

Java 数组长度固定，不能真正改变长度。

题目要求的“删除重复元素”实际是：

```text
把不重复元素覆盖写到数组前面
```

例如原数组：

```text
[0,0,1,1,2]
```

修改后可能是：

```text
[0,1,2,1,2]
```

后面的：

```text
[1,2]
```

不重要。

只需要保证前 `k` 个位置正确：

```text
[0,1,2]
```

并返回：

```text
k = 3
```

---

# 时间复杂度

`right` 从头到尾遍历数组一次：

```text
O(n)
```

每轮只进行：

```text
数组访问
整数比较
指针移动
数组赋值
```

这些都是 `O(1)`。

因此总时间复杂度是：

```text
O(n)
```

---

# 空间复杂度

只使用了两个整数变量：

```java
int left;
int right;
```

没有创建随输入规模增长的数据结构。

因此辅助空间复杂度是：

```text
O(1)
```

---

# 两种解法对比

|解法|时间复杂度|空间复杂度|是否利用有序条件|
|---|--:|--:|---|
|HashSet|O(n)|O(n)|否|
|快慢双指针|O(n)|O(1)|是|

两种方法时间复杂度都是 `O(n)`，但双指针：

```text
没有 HashSet
没有自动装箱
没有哈希计算
没有额外对象
```

所以实际运行更快，空间也更优。

---

# 和 Move Zeroes 的联系

这道题和 `283. Move Zeroes` 使用的是相同模式：

```text
扫描指针负责寻找有效元素
写入指针负责整理数组前面的有效区域
```

区别只是“有效元素”的定义不同。

## Move Zeroes

```text
有效元素 = 非零元素
```

## Remove Duplicates

```text
有效元素 = 和上一个保留元素不同的元素
```

所以可以抽象成：

```text
遍历数组
→ 判断当前元素是否值得保留
→ 如果值得保留，写入有效区域
```

---

# 通用快慢指针模板

```java
int write = 0;

for (int read = 0; read < nums.length; read++) {
    if (当前元素应该保留) {
        nums[write] = nums[read];
        write++;
    }
}

return write;
```

在不同题目中，只需要改变：

```text
当前元素应该保留
```

这个判断条件。

---

# 这道题真正学到的东西

这道题不是让你记住某几行代码，而是学会：

```text
看到原地修改数组
+ 保持相对顺序
+ 把有效元素放到前面
```

应该考虑：

```text
快慢双指针
```

同时还要特别注意题目给出的条件：

```text
数组已经排序
```

这个条件意味着：

```text
重复元素一定相邻
```

因此可以从：

```text
记录所有历史元素
```

优化为：

```text
只比较当前元素和上一个有效元素
```

最终的思维变化是：

```text
HashSet 记录所有出现过的数字
        ↓
利用有序条件，只比较相邻的唯一元素
        ↓
使用快慢双指针原地覆盖
```