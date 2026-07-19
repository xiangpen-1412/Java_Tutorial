# Lecture 4：图算法地图

> 前置：[[02-Graph Traversal - DFS & BFS]]

图算法不是一堆需要死记的名字。先看题目要解决什么，再选择工具。

| 题目问法 | 图论类型 | 常用算法 | 理论入口 |
| --- | --- | --- | --- |
| A 能否到 B？ | 可达性 / 连通性 | DFS、BFS、并查集 | [[02-Graph Traversal - DFS & BFS]]、[[06-Union Find]] |
| 有几块互不相连区域？ | 连通分量 | DFS、BFS、并查集 | [[04-Grid Graph & Connected Components]]、[[06-Union Find]] |
| 网格中一整片区域有多大？ | 网格图遍历 | DFS、BFS | [[04-Grid Graph & Connected Components]] |
| 最少走几步？每步代价相同 | 无权最短路径 | BFS | [[05-BFS for Shortest Path]] |
| 是否有循环依赖？ | 有向图环检测 | 拓扑排序、三色 DFS | [[07-Topological Sort]] |
| 如何安排任务 / 课程顺序？ | 拓扑排序 | BFS（入度）/ DFS | [[07-Topological Sort]] |
| 不断新增边后，是否属于同一组？ | 动态连通性 | 并查集（Union-Find） | [[06-Union Find]] |
| 最短距离 / 最低费用？边代价不同 | 带权最短路径 | Dijkstra | 待学习 |
| 地图中快速找向目标的最短路 | 启发式最短路径 | A* | Dijkstra 之后 |
| 用最低总代价连通所有节点 | 最小生成树 | Kruskal、Prim | 待学习 |

## A* 与其他最短路算法的关系

不要把 A* 当作最早要学的算法。它依赖你先理解：路径、权重、优先队列和 Dijkstra。

```text
BFS：每条边代价相同，求最少步数
↓
Dijkstra：每条边代价可不同，求最低总代价
↓
A*：在 Dijkstra 基础上增加“离目标还有多远”的合理预估，常用于地图 / 游戏寻路
```

## 当前 Theory 已经覆盖什么

| Lecture | 内容 | 能解决的核心问题 |
| --- | --- | --- |
| [[00-Graph Basics]] | 节点、边、方向、权重、度、路径、环 | 把现实关系翻译成图 |
| [[01-Graph Properties & Representation]] | 边列表、邻接表、邻接矩阵、DAG | 读懂输入并选择存图方式 |
| [[02-Graph Traversal - DFS & BFS]] | DFS、BFS、`visited` | 从起点访问全部可达节点 |
| [[04-Grid Graph & Connected Components]] | 隐式网格图、区域扫描 | Flood Fill、岛屿、区域大小 |
| [[05-BFS for Shortest Path]] | BFS 分层与无权最短路 | 最少步数、最少轮数 |
| [[06-Union Find]] | 集合合并与动态连通性 | 是否同组、连通分量数量 |
| [[07-Topological Sort]] | 入度、依赖解除、有向环检测 | 课程 / 任务能否完成及排序 |

当前阶段应掌握以下主线：

1. 节点、边、有向 / 无向、权重；
2. 路径、连通、连通分量、环；
3. 邻接表与网格图；
4. DFS、BFS、`visited` 的意义；
5. BFS 的层为什么能表示无权最短距离或传播轮数；
6. 入度为什么能表示剩余依赖，拓扑排序为什么能检测有向环；
7. 什么时候用遍历，什么时候只需并查集合并集合。

## 推荐练习路径

1. **[[00-1791 - Find Center of Star Graph|1791 Find Center of Star Graph]]**：认识边列表、节点、边与度。
2. **[[01-1971 - Find if Path Exists in Graph|1971 Find if Path Exists in Graph]]**：连通性与遍历。
3. **[[02-733 - Flood Fill|733 Flood Fill]]**：从指定起点处理一个网格连通区域。
4. **[[03-200 - Number of Islands|200 Number of Islands]]**：扫描并统计所有连通分量。
5. **[[03A-695 - Max Area of Island|695 Max Area of Island]]**：统计连通分量大小。
6. **[[04-841 - Keys and Rooms|841 Keys and Rooms]]**：有向图可达性与 `visited`。
7. **[[05-1557 - Minimum Number of Vertices to Reach All Nodes|1557 Minimum Number of Vertices]]**：认识入度为 0 的源节点。
8. **[[06-994 - Rotting Oranges|994 Rotting Oranges]]**：多源 BFS 的分层扩散。
9. **[[07-207 - Course Schedule|207 Course Schedule]]**：拓扑排序与有向环检测。

> 做每道题前，先写下：节点是什么？边是什么？图有方向吗？题目到底在问什么？

## 后续学习

已经完成基础遍历、网格图、无权 BFS、并查集和拓扑排序。下一阶段可学习带权最短路 Dijkstra，再进入最小生成树与 A*。
