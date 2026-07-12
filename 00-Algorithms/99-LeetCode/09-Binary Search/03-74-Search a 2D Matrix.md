# 03. Search a 2D Matrix

## 题号

```text
LeetCode 74. Search a 2D Matrix
```

## 核心知识

这题用 Binary Search。

题目给一个二维矩阵 `matrix` 和一个目标值 `target`，要求判断 `target` 是否存在。

返回：

```text
true / false
```

## 题目本质

矩阵有两个特点：

```text
1. 每一行从左到右升序
2. 每一行第一个数大于上一行最后一个数
```

所以整个矩阵可以看成一个一维升序数组。

比如：

```text
[
  [1, 3, 5, 7],
  [10, 11, 16, 20],
  [23, 30, 34, 60]
]
```

可以看成：

```text
[1, 3, 5, 7, 10, 11, 16, 20, 23, 30, 34, 60]
```

## 思路

假设：

```text
m = 行数
n = 列数
```

总共有：

```text
m * n
```

个元素。

二分范围就是：

```text
left = 0
right = m * n - 1
```

关键是把一维下标 `mid` 转成二维坐标：

```java
int row = mid / n;
int col = mid % n;
```

然后比较：

```text
matrix[row][col] 和 target
```

## 代码

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;

        int left = 0;
        int right = m * n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            int row = mid / n;
            int col = mid % n;
            int value = matrix[row][col];

            if (value == target) {
                return true;
            } else if (value < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        return false;
    }
}
```

## 复杂度

```text
时间复杂度: O(log(m * n))
空间复杂度: O(1)
```

## 记忆

```text
二维矩阵二分 = 假装它是一维数组。

一维下标转二维坐标：
row = mid / n
col = mid % n
```

