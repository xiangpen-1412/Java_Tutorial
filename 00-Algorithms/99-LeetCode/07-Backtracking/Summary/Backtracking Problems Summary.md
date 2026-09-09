# Backtracking 已做题目总结：选择、递归、撤销

回溯用于枚举所有可能的选择过程。它不是一种独立于递归的语法，而是：递归探索一个选择，返回后恢复现场，再尝试下一个选择。

## 1. 已覆盖的题型

| 题目 | 枚举目标 | 主要状态 | 防止重复选择的方法 |
| --- | --- | --- | --- |
| [[01-78-Subsets]] | 所有子集 | `path + start` | 下一层只考虑后面的元素 |
| [[02-46-Permutations]] | 所有排列 | `path + used` | 当前路径不能重复使用同一下标 |
| [[03-39-Combination Sum]] | 和为 target 的组合 | `path + start + remain` | 从 start 向后选，允许继续选择当前 i |
| [[04-797-All Paths From Source to Target]] | DAG 中所有起点到终点路径 | `path + current` | 图为 DAG，沿邻接表选择下一节点 |
| [[00-Backtracking]] | 通用理论 | 选择列表、路径、终止条件 | 谁 add，谁 remove |

## 2. 通用结构

```text
如果满足终止条件：
    保存当前 path
    返回

遍历当前可选择项：
    做选择
    递归
    撤销选择
```

Java 形式：

```java
for (...) {
    path.add(choice);
    backtrack(...);
    path.remove(path.size() - 1);
}
```

最重要的规则：

> 当前这一层添加的状态，必须由当前这一层在递归返回后撤销。

## 3. `path` 为什么保存副本？

`path` 是整个递归过程反复修改的同一个可变列表。如果直接保存它：

```java
result.add(path);
```

结果中的多项可能都指向同一个对象，之后的撤销会改变已经保存的答案。

因此完成一个答案时保存快照：

```java
result.add(new ArrayList<>(path));
```

## 4. Subsets：start 控制选择范围

子集只关心选了哪些元素，不关心选择顺序：

```text
[1,2] 与 [2,1] 是同一个子集
```

所以使用 `start`，下一层只从当前位置之后继续，避免回头产生不同顺序的重复集合。

子集的每一个递归节点本身都是一个合法答案，因此进入函数时即可保存当前 path。

## 5. Permutations：used 控制当前路径

排列关心顺序：

```text
[1,2] 与 [2,1] 是不同排列
```

每一层都可以查看所有元素，但同一个元素下标不能在当前路径重复使用，所以使用：

```text
used[i] = true  → 递归 → used[i] = false
```

终止条件通常是：

```text
path.size() == nums.length
```

## 6. Combination Sum：start 与可重复选择

组合不关心顺序，所以仍用 `start` 防止向前回头；但当前候选数字可以重复使用，因此递归时继续传 `i`，而不是 `i + 1`。

```text
传 i     → 当前数字下一层还能再选
传 i + 1 → 当前数字最多使用一次
```

状态使用 `remain`：

```text
remain == 0 → 得到一个答案
remain < 0  → 当前分支无效
```

## 7. All Paths：回溯也可以发生在图中

当前路径从节点 `0` 开始，每次从 `graph[current]` 选择一个下一节点。

```text
到达 n - 1 → 保存路径
否则 → 继续尝试当前节点的每个邻居
```

这道题给的是 DAG，所以不会沿有向边绕回形成环，不需要普通图遍历中的全局 `visited`。它的目标是枚举所有路径，而不是永久封锁已经出现过的节点。

## 8. 四道题如何区分？

| 问题 | 顺序重要吗 | 元素可重复使用吗 | 关键状态 |
| --- | --- | --- | --- |
| Subsets | 否 | 每个下标一次 | `start` |
| Permutations | 是 | 每个下标一次 | `used[]` |
| Combination Sum | 否 | 可以 | `start`，递归传 `i` |
| All Paths | 路径顺序天然重要 | 按图的边走 | `current + path` |

## 9. 回溯与普通 DFS 的区别

```text
普通图 DFS：visited 表示节点已经永久处理，通常不撤销
回溯：path/used 表示当前选择路径，返回后需要撤销
```

判断标准不是有没有递归，而是题目是否要求尝试多个选择并恢复到选择前的状态。

## 10. 常见错误

```text
add 后忘记 remove；
保存 path 本身，没有复制；
组合题没有 start，产生不同顺序的重复答案；
排列题错误使用 start，漏掉合法顺序；
Combination Sum 递归传 i + 1，意外禁止重复使用；
把 for 循环下标 i 和图中的下一节点 next 混淆；
使用全局 visited，导致 All Paths 漏掉其他合法路径。
```

## 11. 记忆主线

```text
回溯 = 做选择 → 递归 → 撤销选择
组合不看顺序 → start
排列看顺序 → used
允许重复使用当前元素 → 递归传 i
保存答案 → 复制 path
```

