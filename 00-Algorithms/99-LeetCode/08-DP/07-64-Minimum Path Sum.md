# 64. Minimum Path Sum

> 难度：Medium  
> 主题：Dynamic Programming、二维网格、记忆化搜索

## 问题

给定一个由非负整数组成的 `m × n` 网格 `grid`，从左上角出发到达右下角。每次只能向右或向下移动，要求返回路径上所有数字之和的最小值。

虽然题目描述的是从左上角向右下角移动，但使用 Top-down 思考时，可以反过来问：

```text
到达当前格子的最后一步来自哪里？
```

到达 `(m, n)` 的最后一步只可能来自：

```text
(m, n - 1)：左边的格子
(m - 1, n)：上边的格子
```

## Top-down 状态定义

定义：

```text
dfs(m, n) = 从左上角 (0, 0) 到达 (m, n) 的最小路径和
```

因此状态转移为：

```text
dfs(m, n)
= grid[m][n] + min(
    dfs(m, n - 1),
    dfs(m - 1, n)
)
```

起点是最小的有效子问题：

```text
dfs(0, 0) = grid[0][0]
```

目标格子的递归返回值已经是整个问题的答案，因此不需要再维护一个全局的 `res`。

## 我的代码

```java
class Solution {
    Map<Integer, Integer> memo = new HashMap<>();
    int row = 0;
    int col = 0;
    public int minPathSum(int[][] grid) {
        row = grid.length;
        col = grid[0].length;

        if (row == 0 || col == 0) {
            return 0;
        }

        int res = dfs(grid, row - 1, col - 1);
        return res;
    }

    public int dfs(int[][] grid, int m, int n) {
        if (m < 0 || n < 0) {
            return Integer.MAX_VALUE;
        }

        if (m == 0 && n == 0) {
            return grid[0][0];
        }

        int k = m * col + n;

        if (memo.containsKey(k)) {
            return memo.get(k);
        }
        
        int top = dfs(grid, m, n - 1);
        int left = dfs(grid, m - 1, n);
        int current = Math.min(top, left) + grid[m][n];
        memo.put(k, current);
        return current;
    }
}
```

## 记忆化方式

每个状态原本由两个坐标 `(m, n)` 表示。代码使用：

```text
k = m * col + n
```

把二维坐标映射成唯一的一维编号，并将它作为 `HashMap` 的 key。

例如 `col = 3` 时：

```text
(0, 0) -> 0
(0, 1) -> 1
(1, 0) -> 3
(1, 2) -> 5
```

只要 `0 <= n < col`，不同的有效坐标就不会得到相同的 key。

进入一个状态后先查询 `memo`：

```java
if (memo.containsKey(k)) {
    return memo.get(k);
}
```

计算结束后再缓存：

```java
memo.put(k, current);
```

这样每个有效格子最多只会被完整计算一次。

## 越界为什么不能返回 0

递归位于第一行或第一列时，其中一个来源会越界。例如计算 `(0, 1)`：

```text
左边：(0, 0)，合法
上边：(-1, 1)，非法
```

如果非法路径返回 `0`，那么：

```text
min(合法路径和, 0)
```

会把不存在的路径当成更优路径。由于这里求最小值，非法路径应返回一个足够大的值，使 `Math.min()` 自动避开它：

```text
非法状态 = Integer.MAX_VALUE
```

这体现了一个通用原则：

```text
求最小值时，非法分支应返回不会被选中的大值；
求最大值时，非法分支应返回不会被选中的小值。
```

返回哨兵值时还要注意溢出。本题从有效格子递归时，左边和上边至少有一个合法来源，因此 `Math.min()` 不会选择 `Integer.MAX_VALUE`，再与当前格子相加是安全的。如果某道题可能让所有候选分支同时非法，就不能直接对 `Integer.MAX_VALUE` 做加法。

## 复杂度

网格中共有 `row × col` 个有效状态，每个状态只做常数次操作：

```text
时间复杂度：O(row × col)
```

记忆化缓存和最深递归路径都需要额外空间：

```text
空间复杂度：O(row × col)
```

其中递归栈的最大深度约为 `row + col`，缓存最多保存 `row × col` 个状态。

## 核心收获

- **识别信号**：当前位置只可能由固定的前驱状态到达，并要求所有路径中的最小值，可以定义“到达当前格子的最优答案”。
- **逆向思考**：题目虽然要求向右、向下走，Top-down 可以从终点反向寻找左边和上边。
- **状态返回值**：`dfs(m, n)` 已经返回当前子问题的完整答案，所以目标状态的返回值就是最终答案，不需要全局结果变量。
- **非法状态**：边界返回值必须配合聚合操作设计；使用 `min` 时，非法路径不能返回具有吸引力的 `0`。
- **记忆化**：只写入 `memo` 不够，进入状态时必须先读取缓存，才能消除重复计算。

## 我的思维误区

- **只为 `(0, 0)` 设置边界**：第一行和第一列会继续递归到负坐标，导致递归无法终止并产生 `StackOverflowError`。
- **越界时返回 `0`**：`0` 会参与 `Math.min()`，使非法路径被当成成本更低的合法路径。
- **使用全局 `res` 重复维护答案**：当网格只有一个格子时，递归在起点直接返回，全局变量没有更新；更根本的问题是 `dfs` 的返回值本来就已经表示答案。

## 实现注意点

- 代码中的变量名与方向相反：`dfs(m, n - 1)` 是左边，`dfs(m - 1, n)` 是上边。这不影响结果，但改成对应名称会更容易阅读。
- 当前代码在读取 `grid[0].length` 后才判断 `row == 0`。本题约束保证网格非空，因此不会触发问题；在不保证输入非空的场景中，应先确认存在第 `0` 行再访问 `grid[0]`。
- 连续的二维坐标也可以直接使用二维数组记忆化，能够避免 `HashMap<Integer, Integer>` 的哈希和装箱开销。
