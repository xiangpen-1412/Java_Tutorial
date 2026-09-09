# LeetCode 695：Max Area of Island

> 难度：Medium  
> 主题：网格图、DFS / BFS、连通分量大小

## 1. 题目在问什么？

给定一个只包含 `0` 和 `1` 的二维网格：

```text
0：水
1：陆地
```

上下左右相连的陆地属于同一座岛。岛屿面积等于这座岛包含的陆地格子数量。题目要求返回所有岛屿中的最大面积；如果没有陆地，返回 `0`。

注意：

```text
不是求岛屿数量；
不是求所有陆地格子的总数；
不是把斜对角陆地视为相连；
而是分别计算每一个连通分量的大小，再取最大值。
```

## 2. 和 Number of Islands 的关系

两题的建模与遍历方式完全相同：

```text
格子 = 节点
上下左右相邻的陆地 = 有边
一座岛 = 一个陆地连通分量
```

区别只在“完成一次 DFS / BFS 后统计什么”：

```text
200：发现一个新分量时，islandCount++。
695：遍历一个分量时，area++；完成后更新 maxArea。
```

## 3. 核心逻辑

```text
扫描每个格子：
    如果是水或已经访问过：跳过
    如果是未访问陆地：
        从这里启动一次 DFS / BFS
        统计这一次一共访问了多少陆地格子
        用它更新最大面积
```

外层扫描负责发现新岛，内部遍历负责计算当前岛的面积。

## 4. 面积应该在哪里增加？

每个陆地节点只能为当前岛贡献一次面积。因此可以在“确认这个格子第一次被发现”时增加：

```text
陆地第一次入队 / 入栈
→ 立即标记 visited
→ 当前面积加一
```

如果同一格子被重复加入待处理结构，面积就会重复统计。所以应在入队或入栈时标记，而不是等到取出时才标记。

## 5. 通用 BFS 结构

```java
private int bfs(int[][] grid, int startRow, int startCol,
                boolean[][] visited) {
    int[][] directions = {
        {-1, 0}, {1, 0}, {0, -1}, {0, 1}
    };

    Deque<int[]> queue = new ArrayDeque<>();
    queue.offer(new int[]{startRow, startCol});
    visited[startRow][startCol] = true;

    int area = 0;

    while (!queue.isEmpty()) {
        int[] current = queue.poll();
        area++;

        for (int[] direction : directions) {
            int nextRow = current[0] + direction[0];
            int nextCol = current[1] + direction[1];

            if (nextRow < 0 || nextRow >= grid.length
                    || nextCol < 0 || nextCol >= grid[0].length
                    || visited[nextRow][nextCol]
                    || grid[nextRow][nextCol] == 0) {
                continue;
            }

            visited[nextRow][nextCol] = true;
            queue.offer(new int[]{nextRow, nextCol});
        }
    }

    return area;
}
```

代码不是重点，真正的接口意义是：

```text
bfs(start) 返回 start 所在连通分量的节点数量。
```

## 6. 复杂度

设网格有 `m` 行、`n` 列。每个格子最多被访问一次：

```text
时间复杂度：O(m × n)
空间复杂度：O(m × n)
```

## 7. 这题背后能学到什么？

```text
同一个图遍历模板可以承载不同统计目标。
外层扫描发现分量，内层遍历处理分量。
连通分量数量、大小、最大值只是遍历过程中的不同统计量。
“什么时候计数”必须和计数对象一致：发现新岛时数岛，访问新陆地时数面积。
```

## 8. 一句话总结

> 695 就是在完整扫描网格的过程中，逐个计算陆地连通分量的大小，并保留最大值。
