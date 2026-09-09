# LeetCode 1557：Minimum Number of Vertices to Reach All Nodes

> 难度：Medium  
> 主题：Directed Acyclic Graph、入度（in-degree）

## 1. 题目在问什么？

给定一个有向无环图（DAG），节点编号为 `0` 到 `n - 1`，以及边列表：

```java
edges.get(i) = [from, to]
```

表示一条有向边：

```text
from → to
```

要求找出最小的一组起点：从这些起点分别出发，沿箭头方向走，能够到达图中的所有节点。

题目不是问：

```text
最短路径
从 0 出发能否到达所有点
能到达多少个节点
```

而是问：

```text
哪些节点必须主动作为起点，才能覆盖整张图？
```

## 2. 输入示例和邻接表

```java
n = 6
edges = [[0,1], [0,2], [2,5], [3,4], [4,2]]
```

图：

```text
0 → 1
 \
  → 2 → 5

3 → 4 → 2
```

若转换成邻接表：

```text
0: [1, 2]
1: []
2: [5]
3: [4]
4: [2]
5: []
```

这题的标准思路不需要实际建立邻接表或运行 DFS；直接扫描边列表即可。

## 3. 入度是什么？

一个节点的入度（in-degree）是指：有多少条边指向它。

```text
0 → 2
4 → 2
```

节点 `2` 有两条 incoming edge，因此入度是 `2`。

```text
0 → 1
```

节点 `1` 有一条 incoming edge，入度是 `1`。

没有 incoming edge 的节点，入度为 `0`。

## 4. 为什么入度为 0 的节点必须选？

若节点 `x` 的入度为 `0`：

```text
没有任何节点 → x
```

因此如果不把 `x` 作为起点，就无法从其他节点走到 `x`。

```text
入度为 0 的节点：必须出现在答案中。
```

这说明它们不能少。

## 5. 为什么选完所有入度为 0 的节点就够？

不是说一个入度为 `0` 的节点一定能覆盖整张图；图可能有多个独立来源。

真正的结论是：

```text
每一个节点，都能从某一个入度为 0 的节点到达。
```

对任意节点，例如 `5`，沿着 incoming edge 倒着找：

```text
5 ← 2 ← 4 ← 3
```

节点 `3` 没有 incoming edge。把方向还原：

```text
3 → 4 → 2 → 5
```

所以从 `3` 出发能到 `5`。

对任何节点都可以不断往回找前驱：

```text
当前节点 ← 前驱 ← 前驱的前驱 ← ...
```

因为题目保证是 DAG：

```text
不会形成环；
节点数量有限；
因此不可能无限往回找；
最终一定停在入度为 0 的节点。
```

将这条反向追溯的链倒过来，就是一个“入度为 0 的起点 → 当前节点”的正向路径。

因此所有入度为 `0` 的节点合起来，能覆盖所有节点。

```text
入度为 0 的节点：不选不行。
所有入度为 0 的节点：选了就够。

所以它们恰好是最小答案。
```

## 6. 为什么不用 DFS？

DFS 能从一个起点找出它可以到达哪些节点；但若对许多节点重复做 DFS，再比较覆盖范围，会重复遍历图。

这题真正需要的信息只有：

```text
哪个节点至少有一条 incoming edge？
```

扫描每条边时，将边的终点 `to` 标记为“有入边”即可。

## 7. 标准代码

```java
class Solution {
    public List<Integer> findSmallestSetOfVertices(
            int n, List<List<Integer>> edges) {

        boolean[] hasIncoming = new boolean[n];

        for (List<Integer> edge : edges) {
            int to = edge.get(1);
            hasIncoming[to] = true;
        }

        List<Integer> result = new ArrayList<>();

        for (int node = 0; node < n; node++) {
            if (!hasIncoming[node]) {
                result.add(node);
            }
        }

        return result;
    }
}
```

`hasIncoming` 比 `visited` 更准确：它不表示 DFS 是否访问过，只表示该节点是否存在 incoming edge。

## 8. 复杂度

设：

```text
V = n（节点数）
E = edges.size()（边数）
```

```text
时间复杂度：O(V + E)
空间复杂度：O(V)
```

每条边只检查一次，每个节点只检查一次。

## 9. 记忆方式

```text
1557：找所有没有 incoming edge 的节点。

它们无法从别的节点到达，所以必须自己当起点。
DAG 中其余节点一定能从某个这样的起点追溯而来，所以不必额外选。
```

## 10. 与拓扑排序的关系

这题使用了“入度为 `0`”这个概念，但不需要执行完整拓扑排序。

```text
1557：题目保证是 DAG，只需读取原始图中哪些节点入度为 0。
207：图可能有环，需要不断处理零入度节点、减少后继入度，并验证能否处理全部节点。
```

因此不能看到入度就机械地套队列。先判断题目只是在问图的一个静态性质，还是要求模拟依赖逐步解除。

理论链接：[[07-Topological Sort]]
