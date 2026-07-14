# LeetCode 841：Keys and Rooms

> 难度：Medium  
> 主题：Graph、邻接表、DFS、可达性

## 1. 题目在问什么？

有 `n` 个房间，编号从 `0` 到 `n - 1`。一开始只能进入 `0` 号房间。

每个房间中可能放着若干把钥匙；一把钥匙的值就是它能打开的房间编号。

判断从 `0` 号房间出发，能否进入**所有房间**。

```java
rooms.get(i)
```

表示 `i` 号房间内的所有钥匙。

例如：

```java
rooms = [[1, 3], [3, 0, 1], [2], [0]]
```

可以看成有向图：

```text
0 → 1, 3
1 → 3, 0, 1
2 → 2
3 → 0
```

## 2. 图论翻译

```text
房间 = 节点
钥匙 = 有向边
0 号房间 = 起点
题目目标 = 从起点出发，所有节点是否都可达
```

这是典型的邻接表图。`rooms.get(current)` 就是当前节点的邻居列表。

## 3. 核心思路：传统 DFS

从房间 `0` 出发：

```text
进入一个房间 → 标记已访问
遍历房间里的每把钥匙
若钥匙对应的房间未访问 → DFS 进入该房间
```

DFS 结束后，检查 `visited`：

```text
全部为 true  → 所有房间都可以进入
存在 false   → 至少有一个房间不可达
```

## 4. 代码

```java
class Solution {
    boolean[] visited;

    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        visited = new boolean[rooms.size()];
        dfs(rooms, 0);

        for (boolean visit : visited) {
            if (!visit) {
                return false;
            }
        }

        return true;
    }

    public void dfs(List<List<Integer>> rooms, int current) {
        visited[current] = true;

        for (int room : rooms.get(current)) {
            if (!visited[room]) {
                dfs(rooms, room);
            }
        }
    }
}
```

## 5. 为什么必须有 `visited`？

图可能有环或自环：

```text
0 → 1
1 → 0
```

若不标记访问状态，DFS 会在 `0` 和 `1` 之间无限来回递归。

```java
if (!visited[room]) {
    dfs(rooms, room);
}
```

确保每个房间最多进入一次。

## 6. 为什么 `visited` 不需要撤销？

这题只关心：

```text
这个房间是否从 0 可达？
```

某个房间一旦已访问，之后再从其他路径到达它也不会产生新价值，所以 `visited` 应永久保留。

它是传统图 DFS，不是回溯：

| 题目 | 目标 | 状态是否撤销 |
| --- | --- | --- |
| 841 Keys and Rooms | 判断所有节点是否可达 | `visited` 不撤销 |
| 797 All Paths From Source to Target | 枚举所有路径 | `path` 需要回溯删除 |

## 7. 复杂度

设：

```text
V = 房间数量
E = 所有钥匙数量
```

```text
时间复杂度：O(V + E)
空间复杂度：O(V)
```

每个房间最多访问一次，每把钥匙最多检查一次；`visited` 与递归调用栈最多占用 `O(V)` 空间。

## 8. 记忆方式

```text
841：从 0 出发，钥匙就是边。

visited[current] = true
遍历 rooms.get(current)
未访问的房间继续 DFS
最后检查 visited 是否全为 true
```
