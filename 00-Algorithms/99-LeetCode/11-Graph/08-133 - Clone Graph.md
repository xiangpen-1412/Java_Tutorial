# LeetCode 133：Clone Graph

> 难度：Medium  
> 主题：Graph、DFS、BFS、深拷贝、对象映射、环

> 前置知识：[[Theory/01-Graph Properties & Representation|图的表示方式]]、[[Theory/02-Graph Traversal - DFS & BFS|图遍历 DFS 与 BFS]]

## 1. 题目在问什么？

给定一个**连通无向图**中的入口节点 `node`，创建整张图的**深拷贝**，并返回复制图中与入口对应的新节点。

每个节点包含：

```java
class Node {
    int val;
    List<Node> neighbors;
}
```

这里的方法参数不是二维数组，而是原图中的一个 `Node` 对象：

```java
public Node cloneGraph(Node node)
```

LeetCode 页面显示的邻接表只是测试数据的展示形式。因为题目保证图连通，从入口节点沿着 `neighbors` 可以访问整张图。

## 2. 图论翻译

```text
Node 对象             = 图的节点
node.neighbors        = 当前节点的邻接表
neighbors 中的引用     = 图的边
无向图                 = 两端节点的 neighbors 互相保存对方
返回结果               = 新图的入口节点
```

例如：

```text
1 —— 2
```

对象关系实际上是：

```text
原1.neighbors = [原2]
原2.neighbors = [原1]
```

复制后必须是：

```text
新1.neighbors = [新2]
新2.neighbors = [新1]
```

而且：

```text
原1 != 新1
原2 != 新2
```

## 3. 深拷贝与浅拷贝

### 浅拷贝

只创建入口的新节点，但新节点仍然引用原图节点：

```text
新1.neighbors = [原2]
```

这样新旧两张图共享对象，修改新图可能影响原图，不符合题意。

### 深拷贝

每个原节点都创建一个全新的节点，所有邻居关系也只连接新节点：

```text
原图：原1 —— 原2
新图：新1 —— 新2
```

深拷贝必须同时满足：

1. 节点值相同；
2. 节点数量相同；
3. 连接关系相同；
4. 新图不引用任何原图节点；
5. 一个原节点只对应一个新节点。

## 4. 为什么普通 `visited` 不够？

普通图遍历只需要回答：

```text
这个节点以前访问过吗？
```

因此 `Set<Node>` 或 `boolean[]` 就够了。

但复制图还必须回答：

```text
如果这个原节点已经复制过，它对应的新节点是谁？
```

所以需要保存一一对应关系：

```text
原节点 → 新节点
```

Java 中最自然的结构是：

```java
Map<Node, Node> cloned;
```

例如：

| 原图节点 | 复制图节点 |
| --- | --- |
| 原1 | 新1 |
| 原2 | 新2 |
| 原3 | 新3 |

这个 `Map` 同时承担两个职责：

```text
containsKey(original)：判断原节点是否已经处理
get(original)：取得它唯一对应的复制节点
```

因此本题中的映射表可以理解成“加强版 visited”。

## 5. 为什么必须先登记，再递归邻居？

无向图天然会走回上一个节点：

```text
1 —— 2
```

如果先递归、后登记：

```text
复制原1
→ 递归复制原2
  → 原2又递归复制原1
    → 原1又递归复制原2
      → 无限递归
```

最终产生 `StackOverflowError`。

安全顺序必须是：

```text
1. 创建当前复制节点
2. 立即登记：原节点 → 复制节点
3. 再递归处理邻居
```

也就是：

```java
Node copy = new Node(node.val);
cloned.put(node, copy);       // 必须在递归之前

for (Node neighbor : node.neighbors) {
    // 再处理邻居
}
```

这和 BFS 中“节点入队时立刻标记 visited”是同一个原则：一个节点一旦被发现，就立即登记，避免其他路径重复处理。

## 6. 标准 DFS 解法

```java
class Solution {
    private final Map<Node, Node> cloned = new HashMap<>();

    public Node cloneGraph(Node node) {
        if (node == null) {
            return null;
        }

        if (cloned.containsKey(node)) {
            return cloned.get(node);
        }

        Node copy = new Node(node.val);
        cloned.put(node, copy);

        for (Node neighbor : node.neighbors) {
            copy.neighbors.add(cloneGraph(neighbor));
        }

        return copy;
    }
}
```

### 每段代码的职责

```java
if (node == null) {
    return null;
}
```

处理空图。注意：`node.neighbors.isEmpty()` 表示图中存在一个没有邻居的节点，不等于空图。

```java
if (cloned.containsKey(node)) {
    return cloned.get(node);
}
```

当前原节点以前已经复制过，不再创建第二份，直接复用原来的复制节点。这一步既阻止环中的无限递归，也保证一个原节点只有一个复制品。

```java
Node copy = new Node(node.val);
cloned.put(node, copy);
```

创建当前复制节点，并在处理邻居之前保存映射。

```java
for (Node neighbor : node.neighbors) {
    copy.neighbors.add(cloneGraph(neighbor));
}
```

对原节点的每一个邻居，取得该邻居对应的复制节点，再加入当前复制节点的邻居列表。

这里无论邻居以前是否访问过，原图中的这条连接都必须保留：

```text
未复制过：递归创建，再返回复制节点
已经复制：直接从 Map 返回已有复制节点
```

## 7. 完整执行过程

输入邻接表：

```text
[[2,4], [1,3], [2,4], [1,3]]
```

原图：

```text
1 —— 2
|     |
4 —— 3
```

### 处理原节点 1

```text
创建新1
Map：{ 原1 → 新1 }
```

然后处理原1的邻居：原2、原4。

### 处理原节点 2

```text
创建新2
Map：{
  原1 → 新1,
  原2 → 新2
}
```

原2的第一个邻居是原1。原1已经在 Map 中，所以直接返回新1：

```text
新2.neighbors 加入 新1
```

不会再次递归原1。

原2的另一个邻居是原3。

### 处理原节点 3

```text
创建新3
Map：{
  原1 → 新1,
  原2 → 新2,
  原3 → 新3
}
```

原3连接原2和原4：

```text
原2已经复制 → 新3连接新2
原4尚未复制 → 继续处理原4
```

### 处理原节点 4

```text
创建新4
Map：{
  原1 → 新1,
  原2 → 新2,
  原3 → 新3,
  原4 → 新4
}
```

原4的邻居原1、原3都已经复制：

```text
新4.neighbors 加入 新1
新4.neighbors 加入 新3
```

递归逐层返回后，剩余邻居关系也被加入，最终得到：

```text
新1.neighbors = [新2, 新4]
新2.neighbors = [新1, 新3]
新3.neighbors = [新2, 新4]
新4.neighbors = [新1, 新3]
```

结构与原图一致，但所有节点都是新对象。

## 8. 常见错误

### 错误一：用 `neighbors.isEmpty()` 判断空图

```java
if (node.neighbors.isEmpty()) {
    return null;
}
```

只有一个节点的图，其 `neighbors` 为空，但仍然需要复制这个节点。真正的空图条件是：

```java
node == null
```

### 错误二：递归回来以后才标记

```java
Node copy = cloneGraph(neighbor);
visited.add(neighbor);
```

在环中，递归永远没有机会返回，因此标记代码执行不到。

### 错误三：`visited` 每次递归都重新创建

```java
public Node cloneGraph(Node node) {
    List<Node> visited = new ArrayList<>();
}
```

每层调用得到独立的空列表，递归层之间无法共享状态。

### 错误四：访问过就忽略这条边

```java
if (!visited.contains(neighbor)) {
    copy.neighbors.add(...);
}
```

访问过只表示不需要再次创建节点，不表示原图中的连接可以删除。复制图必须保留所有边。

### 错误五：只使用 `Set<Node>`

`Set` 可以阻止重复递归，但不能取得已经创建的复制节点。深拷贝需要的是 `Map<原节点, 新节点>`。

### 错误六：根据 `val` 判断是否为同一个节点

```java
if (a.val == b.val)
```

值相同不代表对象相同。标准写法直接使用原 `Node` 对象作为 Map 的 key，表达对象之间的对应关系。

### 错误七：先处理邻居，最后才创建当前复制节点

邻居可能通过环立即引用回来。如果当前复制节点尚未创建并登记，邻居就无法连接到它。

## 9. BFS 写法

DFS 是最常见的标准答案，BFS 也可以完成相同任务：

```java
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {
            return null;
        }

        Map<Node, Node> cloned = new HashMap<>();
        Deque<Node> queue = new ArrayDeque<>();

        cloned.put(node, new Node(node.val));
        queue.offer(node);

        while (!queue.isEmpty()) {
            Node current = queue.poll();

            for (Node neighbor : current.neighbors) {
                if (!cloned.containsKey(neighbor)) {
                    cloned.put(neighbor, new Node(neighbor.val));
                    queue.offer(neighbor);
                }

                cloned.get(current).neighbors.add(cloned.get(neighbor));
            }
        }

        return cloned.get(node);
    }
}
```

DFS 和 BFS 的共同核心不是递归或队列，而是：

```text
一个共享的“原节点 → 新节点”映射
发现原节点时立即创建并登记复制品
每一条原图边都连接对应的复制节点
```

## 10. 复杂度

设：

```text
V = 节点数量
E = 边的数量
```

每个节点只创建一次，每条边只检查有限次数：

```text
时间复杂度：O(V + E)
额外空间复杂度：O(V)
```

DFS 的递归调用栈最坏为 `O(V)`；BFS 的队列最坏也可能保存 `O(V)` 个节点。

无向图中的一条边会分别出现在两个节点的邻居列表里，因此遍历时可能看到两次，但常数 `2E` 在复杂度中仍然记作 `O(E)`。

## 11. `visited`、`Set` 与 `Map` 的选择

| 任务需要回答的问题 | 合适的数据结构 |
| --- | --- |
| 编号节点是否访问过 | `boolean[] visited` |
| 对象节点是否访问过 | `Set<Node>` |
| 原对象是否处理过，并取得对应的新对象 | `Map<Node, Node>` |

本题不是简单地把 `visited` 换成 Map，而是因为题目需要保存更多信息：

```text
普通遍历：访问状态
图的深拷贝：访问状态 + 原对象与新对象的对应关系
```

## 12. 记忆模板

```text
Clone Graph = 图遍历 + 对象深拷贝

遇到原节点：
1. Map 有它 → 返回已有复制节点
2. Map 没有 → 创建复制节点
3. 先放入 Map
4. 再递归邻居
5. 添加的是复制邻居，不是原邻居
```

最重要的一句话：

> Map 不只是为了防止重复访问，更是为了保证每个原节点只有一个复制品，并让所有复制边都指向正确的新节点。

