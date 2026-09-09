# LeetCode Menu

## 当前完成情况

你现在已经完成了四个阶段。注意：这里的“完成”按之前 Problem 清单和实际做题记录综合计算，不只按有没有单独写题解笔记来算。

- 第一批基础题：20 题
- 第二批基础加强题：20 题
- 第三批进阶入门题：18 题
- 第四阶段核心进阶：新增约 36 题
- 当前累计完成：**约 97 题**

有些题在不同阶段被复习过，也有些做过但没有单独建笔记，所以这里不追求虚假的精确数字。现在可以认为已经完成了约 100 题，学习重点从“继续堆题量”转为“稳定识别模型”。

已经覆盖的能力：

- HashMap / HashSet：查找、计数、分组、频率统计
- Two Pointers：左右夹逼、快慢指针、原地覆盖
- Sliding Window：固定窗口、可变窗口、字符计数窗口
- Binary Search：基础二分、二维矩阵二分
- Stack / Monotonic Stack：括号匹配、最小栈、下一个更大元素
- Linked List：反转、合并、找环、找中点、删除倒数节点、相交链表
- Tree / BFS / DFS / BST：递归、层序遍历、路径判断、BST 判断
- Backtracking：子集、排列、组合、组合总和
- Prefix Sum：一维前缀和、HashMap 前缀和计数、前后缀乘积
- Binary Search 进阶：旋转数组、峰值、答案空间
- Heap / Priority Queue：Top K、双堆维护数据流
- Graph：网格图、邻接表、DFS / BFS、`visited`、拓扑思想
- Tree 进阶：BST 中序、递归返回值、构造树、树上路径
- Basic DP：基础一维状态、打家劫舍模型、网格路径模型

### 当前决定：暂停继续推进 DP

DP 目前只保留已经走完的打家劫舍与网格路径两条线，具体题目统一列在下方“DP 已完成部分”的表格中。

`416. Partition Equal Subset Sum` 已经尝试过回溯并理解了为什么会超时，但还没有真正完成 0/1 背包推导，因此暂时不计入完成。背包、LIS、LCS、编辑距离和树形 DP 全部放入以后再学，不作为当前任务。

### 当前学习方式：一次做透一个模块

接下来不再按“每个模块补几道题”横向推进，也不立即开 Greedy。任何时刻只保留一个主模块，在这个模块内部循环完成：

```text
学习一个小模型
→ 做新的标准题
→ 做新的变种题
→ 闭卷整理识别信号与不变量
→ 用新的混合题检查迁移
→ 暴露薄弱点后回到对应小模型继续加深
```

题目数量不是换模块的依据。只有通过本模块的退出标准，才选择下一个模块。已经完成的 LeetCode 题不重新安排；巩固通过新题、对比整理和闭卷复述完成。

---

## 已完成 / 第一批基础题

### 01. HashMap / HashSet

| LeetCode | Problem                 | Priority |
| -------- | ----------------------- | -------- |
| 1        | Two Sum                 | ⭐⭐⭐⭐⭐   |
| 217      | Contains Duplicate      | ⭐⭐⭐     |
| 242      | Valid Anagram           | ⭐⭐⭐     |
| 49       | Group Anagrams          | ⭐⭐⭐⭐    |
| 347      | Top K Frequent Elements | ⭐⭐⭐⭐⭐   |

### 02. Two Pointers

| LeetCode | Problem                   | Priority |
| -------- | ------------------------- | -------- |
| 125      | Valid Palindrome          | ⭐⭐⭐     |
| 344      | Reverse String            | ⭐⭐      |
| 283      | Move Zeroes               | ⭐⭐⭐     |
| 167      | Two Sum II                | ⭐⭐⭐     |
| 11       | Container With Most Water | ⭐⭐⭐⭐    |
| 15       | 3Sum                      | ⭐⭐⭐⭐⭐   |

### 03. Binary Search

| LeetCode | Problem                | Priority |
| -------- | ---------------------- | -------- |
| 704      | Binary Search          | ⭐⭐⭐     |
| 35       | Search Insert Position | ⭐⭐⭐     |
| 74       | Search a 2D Matrix     | ⭐⭐⭐⭐    |

### 04. Stack

| LeetCode | Problem           | Priority |
| -------- | ----------------- | -------- |
| 20       | Valid Parentheses | ⭐⭐⭐⭐⭐   |

### 05. Linked List

| LeetCode | Problem                | Priority |
| -------- | ---------------------- | -------- |
| 206      | Reverse Linked List    | ⭐⭐⭐⭐⭐   |
| 21       | Merge Two Sorted Lists | ⭐⭐⭐⭐    |
| 141      | Linked List Cycle      | ⭐⭐⭐⭐    |
| 2        | Add Two Numbers        | ⭐⭐⭐⭐    |

### 06. Sliding Window

| LeetCode | Problem                                        | Priority |
| -------- | ---------------------------------------------- | -------- |
| 3        | Longest Substring Without Repeating Characters | ⭐⭐⭐⭐⭐   |

---

## 已完成 / 第二批基础加强题

### 01. Sliding Window

| LeetCode | Problem                       | Priority |
| -------- | ----------------------------- | -------- |
| 209      | Minimum Size Subarray Sum     | ⭐⭐⭐     |
| 567      | Permutation in String         | ⭐⭐⭐     |
| 438      | Find All Anagrams in a String | ⭐⭐⭐     |
| 76       | Minimum Window Substring      | ⭐⭐⭐⭐⭐   |

### 02. Two Pointers

| LeetCode | Problem                             | Priority |
| -------- | ----------------------------------- | -------- |
| 26       | Remove Duplicates from Sorted Array | ⭐⭐⭐     |
| 27       | Remove Element                      | ⭐⭐⭐     |
| 42       | Trapping Rain Water                 | ⭐⭐⭐⭐⭐   |

### 03. Stack / Monotonic Stack

| LeetCode | Problem                          | Priority |
| -------- | -------------------------------- | -------- |
| 155      | Min Stack                        | ⭐⭐⭐     |
| 150      | Evaluate Reverse Polish Notation | ⭐⭐⭐     |
| 739      | Daily Temperatures               | ⭐⭐⭐⭐⭐   |
| 496      | Next Greater Element I           | ⭐⭐⭐     |

### 04. Linked List

| LeetCode | Problem                          | Priority |
| -------- | -------------------------------- | -------- |
| 19       | Remove Nth Node From End of List | ⭐⭐⭐⭐⭐   |
| 876      | Middle of the Linked List        | ⭐⭐⭐     |
| 234      | Palindrome Linked List           | ⭐⭐⭐⭐    |
| 160      | Intersection of Two Linked Lists | ⭐⭐⭐⭐    |

### 05. Tree

| LeetCode | Problem                           | Priority |
| -------- | --------------------------------- | -------- |
| 104      | Maximum Depth of Binary Tree      | ⭐⭐⭐     |
| 226      | Invert Binary Tree                | ⭐⭐⭐     |
| 100      | Same Tree                         | ⭐⭐⭐     |
| 101      | Symmetric Tree                    | ⭐⭐⭐⭐    |
| 102      | Binary Tree Level Order Traversal | ⭐⭐⭐⭐⭐   |

---

## 已完成 / 第三批进阶入门题

这一批从“会写基础模板”进入“识别题型”。重点练：

- 单调栈：继续巩固“右边第一个更大”
- Tree：递归、BFS、BST
- Backtracking：选择、撤销选择
- Prefix Sum：区间和、HashMap 计数
- DP：最基础的一维动态规划

### 01. Monotonic Stack

| LeetCode | Problem                        | Priority |
| -------- | ------------------------------ | -------- |
| 503      | Next Greater Element II        | ⭐⭐⭐⭐    |
| 84       | Largest Rectangle in Histogram | ⭐⭐⭐⭐⭐   |

### 02. Tree / BFS / DFS

| LeetCode | Problem                     | Priority |
| -------- | --------------------------- | -------- |
| 110      | Balanced Binary Tree        | ⭐⭐⭐     |
| 112      | Path Sum                    | ⭐⭐⭐     |
| 543      | Diameter of Binary Tree     | ⭐⭐⭐⭐    |
| 199      | Binary Tree Right Side View | ⭐⭐⭐⭐    |
| 98       | Validate Binary Search Tree | ⭐⭐⭐⭐⭐   |

### 03. Backtracking

| LeetCode | Problem         | Priority |
| -------- | --------------- | -------- |
| 78       | Subsets         | ⭐⭐⭐     |
| 46       | Permutations    | ⭐⭐⭐⭐    |
| 77       | Combinations    | ⭐⭐⭐     |
| 39       | Combination Sum | ⭐⭐⭐⭐    |

### 04. Prefix Sum / HashMap

| LeetCode | Problem                      | Priority |
| -------- | ---------------------------- | -------- |
| 303      | Range Sum Query - Immutable  | ⭐⭐⭐     |
| 560      | Subarray Sum Equals K        | ⭐⭐⭐⭐⭐   |
| 238      | Product of Array Except Self | ⭐⭐⭐⭐    |

### 05. Basic DP

| LeetCode | Problem                         | Priority |
| -------- | ------------------------------- | -------- |
| 70       | Climbing Stairs                 | ⭐⭐⭐     |
| 198      | House Robber                    | ⭐⭐⭐⭐    |
| 121      | Best Time to Buy and Sell Stock | ⭐⭐⭐     |
| 53       | Maximum Subarray                | ⭐⭐⭐⭐    |

---

## 已完成 / 第四阶段核心进阶

这一阶段已经把前面的基础模板扩展到了更完整的题型。下面只记录实际走过的主线，不把“计划过但没有完成”的题混进来。

### 01. Binary Search 进阶

| LeetCode | Problem                              | Priority |
| -------- | ------------------------------------ | -------- |
| 33       | Search in Rotated Sorted Array       | ⭐⭐⭐⭐⭐   |
| 153      | Find Minimum in Rotated Sorted Array | ⭐⭐⭐⭐    |
| 162      | Find Peak Element                    | ⭐⭐⭐     |
| 875      | Koko Eating Bananas                  | ⭐⭐⭐⭐⭐   |

已经练过旋转数组、局部单调性、峰值判断和答案空间二分。

### 02. Monotonic Stack 进阶

| LeetCode | Problem                              | Priority |
| -------- | ------------------------------------ | -------- |
| 1475     | Final Prices With a Special Discount in a Shop | ⭐⭐⭐     |
| 901      | Online Stock Span                    | ⭐⭐⭐⭐    |
| 84       | Largest Rectangle in Histogram       | ⭐⭐⭐⭐⭐   |
| 907      | Sum of Subarray Minimums             | ⭐⭐⭐⭐⭐   |

已经从 next greater 扩展到 next smaller、previous greater、左右边界和贡献法。

### 03. Heap / Priority Queue

| LeetCode | Problem                         | Priority |
| -------- | ------------------------------- | -------- |
| 215      | Kth Largest Element in an Array | ⭐⭐⭐⭐⭐   |
| 295      | Find Median from Data Stream    | ⭐⭐⭐⭐⭐   |

已经练过堆维护第 k 大元素，以及用大小堆维护动态中位数。`973` 暂时不补，它与当前掌握的堆模型重复度较高。

### 04. Graph / BFS / DFS

| LeetCode | Problem                                       | Priority |
| -------- | --------------------------------------------- | -------- |
| 1791     | Find Center of Star Graph                     | ⭐⭐      |
| 1971     | Find if Path Exists in Graph                  | ⭐⭐⭐     |
| 733      | Flood Fill                                    | ⭐⭐⭐⭐    |
| 200      | Number of Islands                             | ⭐⭐⭐⭐⭐   |
| 695      | Max Area of Island                            | ⭐⭐⭐⭐    |
| 841      | Keys and Rooms                                | ⭐⭐⭐⭐    |
| 1557     | Minimum Number of Vertices to Reach All Nodes | ⭐⭐⭐     |
| 994      | Rotting Oranges                               | ⭐⭐⭐⭐⭐   |
| 797      | All Paths From Source to Target               | ⭐⭐⭐⭐    |
| 207      | Course Schedule                               | ⭐⭐⭐⭐⭐   |
| 133      | Clone Graph                                   | ⭐⭐⭐⭐    |
| 802      | Find Eventual Safe States                     | ⭐⭐⭐⭐    |
| 310      | Minimum Height Trees                          | ⭐⭐⭐⭐    |

已经覆盖网格连通块、邻接表遍历、最短层数、图复制、有向图环检测和拓扑思想。`210. Course Schedule II` 没有单独完成，但当前不需要为了凑清单回头补题。

### 05. Tree 进阶

| LeetCode | Problem                                                    | Priority |
| -------- | ---------------------------------------------------------- | -------- |
| 230      | Kth Smallest Element in a BST                              | ⭐⭐⭐⭐    |
| 530      | Minimum Absolute Difference in BST                         | ⭐⭐⭐     |
| 173      | Binary Search Tree Iterator                                | ⭐⭐⭐⭐    |
| 450      | Delete Node in a BST                                       | ⭐⭐⭐⭐    |
| 235      | Lowest Common Ancestor of a Binary Search Tree             | ⭐⭐⭐     |
| 236      | Lowest Common Ancestor of a Binary Tree                    | ⭐⭐⭐⭐⭐   |
| 105      | Construct Binary Tree from Preorder and Inorder Traversal  | ⭐⭐⭐⭐⭐   |
| 106      | Construct Binary Tree from Inorder and Postorder Traversal | ⭐⭐⭐⭐    |
| 114      | Flatten Binary Tree to Linked List                         | ⭐⭐⭐⭐    |
| 124      | Binary Tree Maximum Path Sum                               | ⭐⭐⭐⭐⭐   |

已经覆盖 BST 中序性质、结构修改、最近公共祖先、根据遍历序列构造树，以及“递归返回给父节点的值”和“全局答案”的区别。

### 06. DP 已完成部分

| LeetCode | Problem                         | Priority |
| -------- | ------------------------------- | -------- |
| 70       | Climbing Stairs                 | ⭐⭐⭐     |
| 121      | Best Time to Buy and Sell Stock | ⭐⭐⭐     |
| 53       | Maximum Subarray                | ⭐⭐⭐⭐    |
| 198      | House Robber                    | ⭐⭐⭐⭐    |
| 213      | House Robber II                 | ⭐⭐⭐⭐    |
| 740      | Delete and Earn                 | ⭐⭐⭐⭐    |
| 64       | Minimum Path Sum                | ⭐⭐⭐⭐    |
| 120      | Triangle                        | ⭐⭐⭐⭐    |

到这里先暂停。DP 不是放弃，而是延后；现在不安排 `337、416、322、518、377、300、673、354、1143、1035、72`。

---

## 当前唯一主线：HashMap / HashSet 做透

### 当前进度（2026-09-08）

本模块已完成前 9 题：`349、350、383、205、290、219、202、36、454`。当前正在做第 10 题 **128. Longest Consecutive Sequence**，尚未标记完成。

笔记只补实际遇到、值得复习的问题：350、383、454 已记录；本轮补充 [[07-202 - Happy Number]] 的数位存储与状态判环，以及 [[08-36 - Valid Sudoku]] 的分区域去重与坐标换算。没有具体问题记录的题目不强行补题解。入口：[[HashMap HashSet Problems Summary]]。

### 为什么先做这个模块

这是第一批最早接触的模块之一，目前完成题主要集中在查找、简单计数、分组和 Top K，但下面这些能力还没有连续练透：

- `Set`、计数表、普通映射、双向映射和下标映射分别解决什么问题；
- 如何设计 `key`，让原本需要重复比较的关系变成一次查询；
- 如何用“只从边界开始”“拆成两半”等转换消除重复计算；
- 如何把 HashMap 与数组、二分等结构组合；
- HashSet / HashMap 自身如何设计，以及平均 `O(1)` 的边界是什么。

这个模块又会反复出现在 Sliding Window、Prefix Sum、Graph、Backtracking 和设计题中，所以先把它做透，后面的多个模块都会一起受益。

### 第一层：分清到底要存什么

目标不是看到题就机械地创建 HashMap，而是先判断需要保存的是“是否存在”“出现次数”“对应关系”还是“最近位置”。

| Order | LeetCode | Problem                    | Difficulty | 核心训练                           |
| ----- | -------- | -------------------------- | ---------- | ---------------------------------- |
| 1     | 349      | Intersection of Two Arrays | Easy       | HashSet 表示存在性与结果去重       |
| 2     | 350      | Intersection of Two Arrays II | Easy    | 计数表表示同一元素还能使用几次     |
| 3     | 383      | Ransom Note                | Easy       | 统计后逐个消耗，区分存在与数量足够 |
| 4     | 205      | Isomorphic Strings         | Easy       | 双向映射与一一对应                 |
| 5     | 290      | Word Pattern               | Easy       | 把双向映射迁移到单词与字符         |
| 6     | 219      | Contains Duplicate II      | Easy       | 保存最近出现下标，而不是只存存在性 |

#### 第一层整理点

完成后闭卷写出下面五种信息的区别，不重做旧题：

```text
HashSet<T>
Map<T, Integer> frequency
Map<K, V> mapping
两个方向的 mapping
Map<T, Integer> lastIndex
```

如果还不能根据题目目标判断应该存哪一种信息，就继续增加同层新题，不进入第二层。

### 第二层：把重复搜索变成状态或查询

这一层开始训练真正的建模：什么状态需要去重，什么关系可以预处理，什么时候只应该从边界开始。

| Order | LeetCode | Problem                              | Difficulty | 核心训练                                |
| ----- | -------- | ------------------------------------ | ---------- | --------------------------------------- |
| 7     | 202      | Happy Number                         | Easy       | 用 visited state 判断过程是否进入循环   |
| 8     | 36       | Valid Sudoku                         | Medium     | 同时维护行、列、宫三组约束              |
| 9     | 454      | 4Sum II                              | Medium     | 将四层枚举拆成两组和的查询              |
| 10    | 128      | Longest Consecutive Sequence         | Medium     | 只从没有前驱的边界开始，保证总体 `O(n)` |
| 11    | 1657     | Determine if Two Strings Are Close   | Medium     | 区分字符集合与频率集合两个必要条件      |
| 12    | 554      | Brick Wall                           | Medium     | 把位置边界变成 key，统计最佳切割位置    |

#### 第二层整理点

完成后必须能独立解释：

1. `202` 中为什么状态一旦重复，就可以确认以后会循环；
2. `454` 为什么能从 `O(n^4)` 降到 `O(n^2)`；
3. `128` 为什么不能从每个数字都向后扫描；
4. 一个对象本身不好查询时，怎样提取稳定的特征作为 key。

解释不清的部分用新的同模型题继续补，不返回重做已经完成的题。

### 第三层：组合结构与 Hash 设计

这一层不再只是调用集合，而是理解 Hash 结构怎样与其他数据结构配合，以及为什么能达到题目要求的复杂度。

| Order | LeetCode | Problem                         | Difficulty | 核心训练                                |
| ----- | -------- | ------------------------------- | ---------- | --------------------------------------- |
| 13    | 705      | Design HashSet                  | Easy       | 理解桶、哈希位置与冲突处理              |
| 14    | 706      | Design HashMap                  | Easy       | 自己维护 key-value 与更新语义            |
| 15    | 380      | Insert Delete GetRandom O(1)    | Medium     | HashMap + 动态数组，交换末尾完成删除     |
| 16    | 981      | Time Based Key-Value Store      | Medium     | HashMap + 有序记录 + Binary Search       |
| 17    | 939      | Minimum Area Rectangle          | Medium     | 用点集把二维几何关系转换成 `O(1)` 查询   |
| 18    | 149      | Max Points on a Line            | Hard       | 设计规范化斜率 key，处理精度与重复关系   |

`149` 不是为了凑一道 Hard。只有前面的 key 设计已经稳定后才进入；如果它暴露出最大公约数、符号归一化或重复点处理问题，就在这一层继续补新的中间题，而不是硬判定模块结束。

### 模块内的记忆循环

每完成 3～4 道新题，暂停加题，做一次短整理：

1. 不看笔记，写出这几题各自的识别信号；
2. 对比它们的 key、value 和不变量；
3. 记录本轮真实出现的概念错误；
4. 下一题安排一个新的变种，检查刚整理的判断能否迁移；
5. 新变种仍然暴露同一问题，就继续留在当前小模型。

这叫“来回巩固”：不是反复背同一份代码，而是在学习、整理、陌生变种、再次整理之间循环。

### HashMap / HashSet 的退出标准

上面的 18 题只是当前主干，不是“做完数字就自动毕业”。同时满足下面条件，才进入下一个模块：

- 能在存在性、计数、映射、双向映射、下标和状态去重之间正确选择；
- 能清楚说出每道题的 key 和 value 为什么这样设计；
- 能发现暴力解法中重复发生的搜索，并转换成预处理查询；
- 能解释平均 `O(1)`，并知道最坏情况和哈希冲突不能被完全忽略；
- 能完成 HashMap 与数组、二分或二维坐标的组合题；
- 连续完成若干道未提前标注模型的新题，并能说明为什么用 Hash，而不只是把代码写过；
- 最终 Summary 能恢复识别信号、核心转换、复杂度和主要变种。

如果退出检查失败，就根据失败类型继续追加新的针对题。下一模块不提前锁死；等这一块做透后，再依据当时暴露出来的薄弱点，在 Sliding Window、Backtracking、Prefix Sum、Heap 等已学模块中选择最需要加深的一块。
