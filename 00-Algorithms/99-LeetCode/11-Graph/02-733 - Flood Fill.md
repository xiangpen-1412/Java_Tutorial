# LeetCode 733：Flood Fill

> 难度：Easy  
> 主题：网格图（Grid Graph）、BFS / DFS、连通分量

## 1. 题目在做什么？

Flood Fill 就是画图软件里的“油漆桶填充”。

从起点 `image[sr][sc]` 出发，将所有满足以下条件的格子改成 `color`：

1. 与起点通过**上、下、左、右**连通；
2. 原始颜色和起点原始颜色相同。

注意：不是将图片中所有同色格子都修改，只修改起点所在的那一整块区域。

## 2. 为什么这是图题？

二维数组中的每个格子都可以看作图的一个节点：

```text
image[row][col] = 一个节点
```

每个节点的邻居是四个方向：

```text
    上：row - 1, col
左：row, col - 1      右：row, col + 1
    下：row + 1, col
```

图中的边不需要另外建立；格子坐标已经隐含了相邻关系，因此这类题叫做**隐式网格图**。

本题要找的是：

> 起点所在的、颜色为起点原色的连通分量。

## 3. 本题的核心数据结构

```java
int[][] image;         // 原始图片
int[][] res;           // 修改后的图片
boolean[][] visited;   // 格子是否已经处理过
Deque<int[]> queue;    // BFS 待处理坐标
```

### 坐标如何存入队列？

一个格子的位置由行和列组成：

```java
new int[]{row, col}
```

因此可以使用：

```java
Deque<int[]> queue = new ArrayDeque<>();
```

虽然 `int` 是基本类型、不能直接写成 `Queue<int>`，但 `int[]` 是数组对象，因此可以作为泛型中的元素类型。

## 4. BFS 的流程

```text
1. 保存起点原始颜色 oColor。
2. 将起点标记为已访问，并放入队列。
3. 队列不为空时：
   - 取出一个坐标；
   - 将该坐标在 res 中改为新颜色；
   - 检查四个方向的邻居。
4. 一个邻居只有同时满足以下条件才能入队：
   - 没有越界；
   - 尚未访问；
   - 在原始 image 中颜色等于 oColor。
5. 队列为空时，起点所在的同色区域已经处理完。
```

## 5. 四方向数组

将四个方向统一保存，避免分别编写四段重复逻辑：

```java
int[][] directions = {
    {-1, 0}, // 上
    {1, 0},  // 下
    {0, -1}, // 左
    {0, 1}   // 右
};
```

对于当前坐标 `(row, col)`：

```java
int nextRow = row + direction[0];
int nextCol = col + direction[1];
```

## 6. 为什么需要 `visited`？

同一个区域中，格子可能从多个方向被访问到。如果没有 `visited`：

- 同一个坐标会多次进队；
- 会产生重复工作；
- 在某些图结构中甚至可能不断往返。

正确习惯是：**格子入队时立即标记**。

```java
visited[nextRow][nextCol] = true;
queue.offer(new int[]{nextRow, nextCol});
```

## 7. `image` 与 `res` 分开的意义

```text
image：始终保存原始颜色，用于判断邻居是否属于目标区域。
res：保存修改后的结果。
```

若新建 `res`：

```java
int[][] res = new int[image.length][image[0].length];
```

它默认全是 `0`，必须先复制原始图片：

```java
for (int i = 0; i < image.length; i++) {
    res[i] = image[i].clone();
}
```

否则，不在填充区域内的格子也会错误地变成 `0`。

## 8. 边界检查

处理邻居前必须确保坐标有效：

```java
nextRow >= 0 && nextRow < rowSize
nextCol >= 0 && nextCol < colSize
```

否则会访问到数组外的坐标，例如 `image[-1][0]`，导致异常。

## 9. 相关题型对比

| 题目 | 在网格图中做什么 |
| --- | --- |
| Flood Fill | 修改起点所在的一个同色连通分量 |
| Number of Islands | 统计所有陆地连通分量数量 |
| Max Area of Island | 找出最大陆地连通分量大小 |
| Rotting Oranges | 多个起点同时按层扩散 |

## 10. 复杂度

若图片有 `m` 行、`n` 列，最坏情况下所有格子都属于同一填充区域：

```text
时间复杂度：O(m × n)
空间复杂度：O(m × n)
```

每个格子最多被加入队列、访问一次。

## 11. 做网格题时的检查清单

```text
格子代表什么？
可以走哪几个方向？
哪些格子允许继续走？
如何判断越界？
如何避免重复访问？
是只处理起点区域，还是要扫描全部区域？
```
