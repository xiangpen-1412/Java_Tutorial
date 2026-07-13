# 04. LeetCode 797 - All Paths From Source to Target

> 难度：Medium  
> 主题：Backtracking、图上的路径枚举、DFS

## 1. 题目要什么？

给一个有向无环图（DAG），节点编号为 `0` 到 `n - 1`。找出从起点 `0` 到终点 `n - 1` 的**所有路径**。

```java
graph[i]
```

表示从节点 `i` 可以直接走到的所有下一节点。

例如：

```java
graph = {{1, 2}, {3}, {3}, {}}
```

```text
0 → 1 → 3
 \      
  → 2 → 3
```

答案是：

```text
[0, 1, 3]
[0, 2, 3]
```

## 2. 为什么这是 Backtracking？

题目要求的是“所有路径”。每走到一个节点时，都可能有多个下一步选择；走完一条路后，需要退回来尝试下一条路。

```text
选择一个下一节点
→ 加入当前路径 path
→ 递归继续走
→ 从 path 删除该节点
→ 尝试下一个选择
```

因此它的核心模板是：

```text
choose → explore → unchoose
```

它的递归走法是 DFS：每次先沿一条边走到底。但本题重点不是 `200 / 695` 那种“遍历并标记连通块”的图 DFS，而是**用 DFS 实现回溯，枚举每一条路径**。

## 3. `path`、`res` 和终点

```text
path：当前正在构造的一条从 0 出发的路径
res：所有已完成的路径
target：最后一个节点，graph.length - 1
```

开始前必须先放入起点：

```java
path.add(0);
```

当当前节点已经是终点时，当前 `path` 就是一条完整答案：

```java
if (current == target) {
    res.add(new ArrayList<>(path));
    return;
}
```

必须复制：`new ArrayList<>(path)`。因为之后回溯会继续修改同一个 `path` 对象。

## 4. 为什么不需要 `visited`？

题目保证图是 DAG：没有环，因此不会无限绕圈。

更重要的是，节点可以属于不同的合法路径。例子中节点 `3` 同时属于：

```text
0 → 1 → 3
0 → 2 → 3
```

若使用全局 `visited`，第一条路径访问 `3` 后，第二条路径会被错误跳过。

## 5. 代码里的关键区别：下标 `i` 和节点 `next`

```java
for (int i = 0; i < graph[current].length; i++) {
    int next = graph[current][i];
}
```

```text
i    ：邻居在 graph[current] 数组中的下标
next ：真正要前往的下一个节点编号
```

递归必须传 `next`，不能传 `i`：

```java
dfs(graph, next); // 正确
dfs(graph, i);    // 错误：可能反复回到当前节点，导致无限递归 / 超时
```

## 6. 正确代码

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    int target;

    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        target = graph.length - 1;

        path.add(0);
        dfs(graph, 0);

        return res;
    }

    public void dfs(int[][] graph, int current) {
        if (current == target) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int next : graph[current]) {
            path.add(next);               // choose
            dfs(graph, next);             // explore
            path.remove(path.size() - 1); // unchoose
        }
    }
}
```

## 7. 关于当前写法

下面的判断在本题的 DAG 前提下也能工作：

```java
if (path.contains(size))
```

因为一旦路径包含终点，就不会再向下走。但它不够直接，并且每次都要扫描 `path`。更准确的终止条件是：

```java
if (current == size)
```

因为函数参数 `current` 就表示“现在站在哪个节点”。

回溯删除应删除最后加入的节点：

```java
path.remove(path.size() - 1);
```

不要写：

```java
path.remove(next);
```

`next` 是 `int` 时，Java 会优先把它当作**下标**，而不是要删除的节点值。

## 8. 一次递归过程

```text
path = [0]

选 1：path = [0, 1]
  选 3：path = [0, 1, 3]
    到达终点，复制并加入 res
  撤销 3：path = [0, 1]
撤销 1：path = [0]

选 2：path = [0, 2]
  选 3：path = [0, 2, 3]
    到达终点，复制并加入 res
  撤销 3：path = [0, 2]
撤销 2：path = [0]
```

## 9. 记忆方式

```text
797：图上的回溯。

path 记录目前走过的节点。
current 到 target：复制 path。
每个邻居都是一个 choice。
add next → dfs(next) → removeLast。
```
