# LeetCode 207：Course Schedule

> 难度：Medium  
> 主题：有向图、依赖关系、入度、拓扑排序、环检测

## 1. 题目到底在问什么？

共有 `numCourses` 门课程，编号为 `0` 到 `numCourses - 1`。

```text
prerequisites[i] = [course, prerequisite]
```

表示：要学习 `course`，必须先完成 `prerequisite`。

例如：

```text
[1, 0]：学课程 1 之前必须先学课程 0
```

题目只要求返回：是否存在一种顺序，可以完成全部课程。

```text
能完成全部课程：true
依赖关系形成循环，无法完成全部课程：false
```

题目不要求返回具体课程顺序；那是 LeetCode 210 的要求。

## 2. 为什么它是图题？

把每门课程看成一个节点，把“先修课完成后可以解锁后续课程”看成有向边：

```text
prerequisite → course
```

因此：

```text
[1, 0] 建边为 0 → 1
[2, 0] 建边为 0 → 2
```

邻接表保存：完成某门课程以后，会影响哪些后续课程。

```java
graph.computeIfAbsent(pre, key -> new ArrayList<>()).add(after);
```

它表达的是：

```text
pre 不在邻接表中时，先创建空列表；
然后把 after 加到 pre 的后续课程列表中；
最终得到 pre → List<after>。
```

Lambda 中的 `key` 就是传入的 `pre`，这里只是参数名；创建空列表并不需要使用它。

## 3. 入度在这道题中表示什么？

课程 `x` 的入度表示：还有多少门直接先修课程必须先完成。

```text
0 → 2
1 → 2
```

课程 `2` 的入度是 `2`，因为它等待课程 `0` 和课程 `1`。

处理边 `pre → after` 时，应增加 `after` 的入度：

```java
degrees.put(after, degrees.get(after) + 1);
```

不能增加 `pre`，因为 `pre` 是这条边的起点，这条边指向的是 `after`。

## 4. 为什么必须先初始化所有课程？

节点全集由 `numCourses` 决定，不是由邻接表决定。

某门课程可能：

- 没有先修课；
- 没有后续课程；
- 完全没有出现在 `prerequisites` 中。

但它仍然是一门真实课程。因此应先把所有课程的入度初始化为 `0`：

```java
for (int course = 0; course < numCourses; course++) {
    degrees.put(course, 0);
}
```

如果只在扫描边时创建入度记录，就会漏掉完全孤立或原始入度为 `0` 的课程。

## 5. 拓扑排序的核心逻辑

入度为 `0` 的课程没有尚未完成的前置条件，因此现在就可以学习。

```text
1. 把所有入度为 0 的课程加入队列。
2. 取出一门可以学习的课程，把它记为已完成。
3. 沿着它的出边找到所有后续课程。
4. 当前课程已完成，所以每个后续课程少一个未完成前置条件：入度减一。
5. 某门后续课程入度变成 0 时，把它加入队列。
6. 重复，直到队列为空。
```

这里的“删除节点”不是必须真的从 Map 中移除；用处理计数和入度变化模拟删除即可。

## 6. 为什么最终比较完成课程数量？

队列为空有两种可能：

```text
成功：所有课程都处理完了。
失败：剩余课程互相循环依赖，没有任何课程入度为 0。
```

因此队列为空本身不能判断成功。需要记录：

```java
int completedCourse = 0;
```

每取出一门课程就加一，最后判断：

```java
return completedCourse == numCourses;
```

## 7. 一个完整例子

```text
numCourses = 5
prerequisites = [[1,4], [2,4], [3,1], [3,2]]
```

建边：

```text
4 → 1
4 → 2
1 → 3
2 → 3
```

初始入度：

```text
课程 0：0
课程 1：1
课程 2：1
课程 3：2
课程 4：0
```

过程：

```text
队列初始：[0, 4]

处理 0：没有后续课程
处理 4：课程 1、2 的入度都变成 0，它们入队
处理 1：课程 3 的入度从 2 变成 1
处理 2：课程 3 的入度从 1 变成 0，课程 3 入队
处理 3：没有后续课程

共处理 5 门课程，因此可以完成全部课程。
```

课程 `0` 没出现在任何 prerequisite 中，但仍然必须计入完成数量，这就是初始化全部课程的重要性。

## 8. 本次实现中最重要的易错点

### 错误 1：用邻接列表长度当入度

```java
graph.get(node).size()
```

这是节点的后续课程数量，也就是出度，不是它的入度。入度必须单独统计。

### 错误 2：从邻接表中找零入度节点

邻接表的 value 为空只表示“没有后续课程”，即出度为 `0`；拓扑排序的起点需要的是入度为 `0`。

### 错误 3：没有出边时遍历 `null`

只给有出边的节点创建邻接列表时：

```java
graph.get(course)
```

可能返回 `null`。应使用：

```java
graph.getOrDefault(course, Collections.emptyList())
```

### 错误 4：入度减了两次

错误写法：

```java
int degree = degrees.get(after) - 1;
degrees.put(after, degree - 1);
```

第一行已经减一，第二行又减了一次。正确逻辑是：

```java
int degree = degrees.get(after) - 1;
degrees.put(after, degree);
```

然后用同一个新值判断是否为 `0`。

### 错误 5：对 `size()` 使用 `--`

```java
--graph.get(after).size()
```

`size()` 返回一个临时数值，不能被修改；而且列表大小也不是入度。需要修改的是独立保存的 `degrees[after]` 或 `indegree[after]`。

## 9. 当前 Map 写法的完整代码

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        Map<Integer, List<Integer>> graph = new HashMap<>();
        Map<Integer, Integer> degrees = new HashMap<>();
        Deque<Integer> queue = new ArrayDeque<>();

        for (int course = 0; course < numCourses; course++) {
            degrees.put(course, 0);
        }

        for (int[] prerequisite : prerequisites) {
            int pre = prerequisite[1];
            int after = prerequisite[0];

            graph.computeIfAbsent(pre, key -> new ArrayList<>())
                 .add(after);
            degrees.put(after, degrees.get(after) + 1);
        }

        for (Map.Entry<Integer, Integer> entry : degrees.entrySet()) {
            if (entry.getValue() == 0) {
                queue.offer(entry.getKey());
            }
        }

        int completedCourse = 0;

        while (!queue.isEmpty()) {
            int course = queue.poll();
            completedCourse++;

            for (int after :
                    graph.getOrDefault(course, Collections.emptyList())) {
                int degree = degrees.get(after) - 1;
                degrees.put(after, degree);

                if (degree == 0) {
                    queue.offer(after);
                }
            }
        }

        return completedCourse == numCourses;
    }
}
```

## 10. 复杂度

设课程数为 `V`，依赖关系数为 `E`：

```text
时间复杂度：O(V + E)
空间复杂度：O(V + E)
```

每门课程最多入队一次，每条依赖边只会在其起点被处理时检查一次。

## 11. 这题背后真正要学会什么？

```text
依赖关系可以建模为有向图。
入度可以解释为一个任务尚未解决的前置条件数量。
处理当前节点，就是删除它的出边，所以后续节点入度减一。
拓扑排序能处理全部节点，当且仅当有向图无环。
队列为空只是过程停止；完成数量才验证题目目标。
节点全集来自题目定义，不能只从出现过的边推断。
```

## 12. 一句话总结

> Course Schedule 是不断完成“当前没有前置条件”的课程，并解除它对后续课程的限制；如果最后仍有课程没被处理，说明剩余依赖形成了环。

理论链接：[[07-Topological Sort]]
