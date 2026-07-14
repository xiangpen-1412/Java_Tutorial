# 00. Binary Search

## 核心理解

Binary Search 的本质不是“查找有序数组”，而是：

```text
每次通过一个判断，排除掉一半不可能有答案的范围。
```

有序数组只是最常见的二分场景，因为它天然能判断：

```text
nums[mid] < target
```

然后就知道：

```text
mid 左边都不用看了
```

所以二分真正需要的是：

```text
1. 有一个候选范围
2. 能通过 mid 判断答案在左边还是右边
3. 每次判断后可以安全排除一半
```

## 二分思考公式

知道一道题要用 Binary Search 以后，不要先写代码。

先写这四行：

```text
目标：
候选：
比较：
排除：
```

含义是：

```text
目标：我要找的到底是什么？
候选：哪些位置 / 数值可能成为答案？
比较：mid 要和谁比较，才能得到信息？
排除：根据比较结果，哪一半可以安全丢掉？
```

如果这四行写不出来，说明现在还没找到二分能推进下去的逻辑。

## 二分证明句

二分不是靠感觉移动边界，而是要能证明一边没有答案。

写代码前，尽量先说出一句证明：

```text
如果 ________，
那么 ________ 一定不在答案里，
所以可以丢掉 ________。
```

比如普通有序数组查找：

```text
如果 nums[mid] < target，
那么 mid 和左边都不可能是答案，
所以可以丢掉左半边。
```

再比如旋转数组找最小值：

```text
如果某一段已经整体有序，
那么最小值不在这段中间，
所以可以丢掉这段不可能包含最小值的部分。
```

再比如找峰值：

```text
如果 nums[mid] < nums[mid + 1]，
说明右边是上坡方向，
那么右边一定存在 peak，
所以可以往右边缩小范围。
```

## mid 应该和谁比

二分卡住时，不要只问“模板是什么”，而要问：

```text
mid 应该和谁比？
这个比较能证明哪边没有答案？
```

常见比较对象：

```text
1. mid 和 target 比
2. mid 和 left / right 边界比
3. mid 和 mid + 1 / mid - 1 邻居比
4. mid 作为答案猜测，放进 check(mid) 里判断可不可行
```

对应题型：

```text
普通有序查找：mid 和 target 比
旋转数组：mid 和 left / right 比
峰值 / 山峰：mid 和邻居比
答案空间二分：mid 是一个猜测答案，用 check(mid) 判断
```

## 写代码前的检查

每次准备移动边界前，先问：

```text
我丢掉的那一半，能不能保证没有答案？
```

如果不能保证，就不能这么移动。

Binary Search 真正的核心是：

```text
mid 给了我什么证据？
这个证据能让我安全排除哪一边？
```

## mid 怎么取

常见写法：

```java
int mid = left + (right - left) / 2;
```

如果元素个数是偶数，它会取左中位。

比如 8 个元素：

```text
index: 0 1 2 3 4 5 6 7
left = 0
right = 7
mid = 0 + (7 - 0) / 2 = 3
```

所以第一次看 index `3`。

偶数个元素没有唯一中间值，取左中位或右中位都可以，关键是规则要稳定。

## 边界移动的核心原则

二分里最重要的问题之一是：

```text
移动边界时，到底是移动到 mid，还是 mid - 1 / mid + 1？
```

核心判断只有一句：

```text
如果 mid 已经确定不可能是答案，就排除 mid。
如果 mid 仍然可能是答案，就保留 mid。
```

也就是：

```text
排除 mid：
left = mid + 1
right = mid - 1

保留 mid：
left = mid
right = mid
```

### 什么时候排除 mid

当已经确定 `mid` 不可能是答案时，就排除它。

比如普通查找里：

```text
nums[mid] < target
```

说明 `nums[mid]` 太小了，`mid` 本身不可能是答案，左边也不可能。

所以：

```text
left = mid + 1
```

再比如：

```text
nums[mid] > target
```

说明 `nums[mid]` 太大了，`mid` 本身不可能是答案，右边也不可能。

所以：

```text
right = mid - 1
```

### 什么时候保留 mid

当 `mid` 可能就是答案，但还不能确定它是不是最终答案时，要保留它。

常见情况：

```text
找最小值
找最大值
找第一个满足条件的位置
找最后一个满足条件的位置
找最小可行答案
找最大可行答案
```

这类题里，`mid` 可能已经满足条件，但还要继续往一边缩小范围，看看有没有更好的答案。

这时不能直接用：

```text
mid - 1
mid + 1
```

否则可能把真正答案扔掉。

应该根据情况保留：

```text
right = mid
```

或者：

```text
left = mid
```

### 记忆

```text
mid 没资格当答案：mid +/- 1
mid 还有资格当答案：保留 mid
```

边界移动不是背模板，而是问：

```text
mid 还有没有可能是答案？
```

## 普通二分查找

适用于：

```text
数组整体升序
要找 target 是否存在
```

比较 `nums[mid]` 后：

```text
nums[mid] == target -> 找到了
nums[mid] < target  -> target 只能在右边
nums[mid] > target  -> target 只能在左边
```

边界移动：

```java
if (nums[mid] < target) {
    left = mid + 1;
} else if (nums[mid] > target) {
    right = mid - 1;
}
```

注意：

```text
mid 已经检查过了，所以移动边界时要排除 mid。
```

也就是：

```text
往右找：left = mid + 1
往左找：right = mid - 1
```

## Search Insert Position

LeetCode 35 的本质是：

```text
找第一个 >= target 的位置。
```

如果找到了 `target`，返回它的 index。

如果没有找到，最后返回：

```text
left
```

因为循环结束时，`left` 就是 target 应该插入的位置。

例子：

```text
nums = [1, 3, 5, 6]
target = 2
```

最后：

```text
left = 1
```

所以 `2` 应该插入 index `1`。

## Search a 2D Matrix

LeetCode 74 的本质是：

```text
二维矩阵可以看成一个一维升序数组。
```

如果矩阵有：

```text
m 行
n 列
```

总元素数量：

```text
m * n
```

一维下标转二维坐标：

```java
row = mid / n;
col = mid % n;
```

然后比较：

```text
matrix[row][col] 和 target
```

## Rotated Sorted Array

LeetCode 33 不是普通有序数组，但它仍然可以二分。

关键原因：

```text
虽然整体被旋转了，但每次看 mid 时，左半边或右半边至少有一边是有序的。
```

所以每轮要先判断：

```text
哪一边是有序的？
target 是否在有序的那一边？
```

### 容易错的点

判断哪边有序时，不要用：

```java
nums[mid - 1]
nums[mid + 1]
```

因为当 `mid` 在边界时会越界。

判断有序性时应该带上 `mid`：

```java
nums[left] <= nums[mid]
nums[mid] <= nums[right]
```

也就是判断：

```text
left 到 mid 是否有序
mid 到 right 是否有序
```

但是移动搜索范围时，仍然要排除 `mid`：

```text
left = mid + 1
right = mid - 1
```

所以要分清两件事：

```text
判断有序性：带上 mid
缩小搜索范围：排除 mid
```

这是第 33 题最容易混的地方。

## Binary Search 的题目信号

看到这些信号，可以先想 Binary Search：

```text
有序数组
查找某个值
插入位置
第一个满足条件的位置
最后一个满足条件的位置
最小可行答案
最大可行答案
答案范围很大
每次判断可以排除一半
```

最核心的一句：

```text
只要我能判断一半一定没答案，就可以考虑二分。
```

## 它属于哪个分类

Binary Search 可以出现在数组题里，但它不只是 Array 的子类。

更准确地说：

```text
Binary Search 是一种算法套路。
Array 是一种数据结构。
```

所以在 LeetCode 分类里，Binary Search 应该单独作为一个分类。

它常见地依附在：

```text
Array
Matrix
Answer Space
Rotated Sorted Array
```

但分类上应该单独记：

```text
Binary Search
```

## 记忆

```text
普通二分：整体有序，每次排除一半。
插入位置：找第一个 >= target 的位置，最后 return left。
二维矩阵：把二维坐标映射成一维下标。
旋转数组：每次至少有一边有序，先判断哪边有序。
判断有序性：带上 mid。
移动搜索范围：排除 mid。
```
