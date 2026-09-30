# LeetCode Menu

## 当前完成情况

截至 2026-09-30，前四阶段已完成，HashMap / HashSet 专题的 18 题已完成；按用户确认，滑动窗口主线 19 题全部完成，包含 1438。本轮确认完成 523、304，并整理单题笔记和 Java 答案；525、1524 已讨论并分别整理复盘，仍未确认完成。上述前缀和题目的平台提交通过情况未核验。这里按 menu 已完成清单、解题记录与口头确认记录进度；没有单独题解不等于未完成，完成题目也不等于已经通过独立迁移检验。

- 第一批基础题：20 题
- 第二批基础加强题：20 题
- 第三批进阶入门题：18 题
- 第四阶段核心进阶：新增 36 题（跨阶段复习题去重）
- 第五阶段 HashMap / HashSet：新增 18 题，包含本次完成的 149
- Sliding Window 主线：新增 19 题，六层全部完成
- 当前 Prefix Sum 主线：新增完成 523、304 两题，按 2026-09-30 用户确认；525、1524 已整理复盘，尚未确认完成
- 当前清单累计完成：**133 道不重复题目**

统计口径：前四阶段按题号去重为 94 题，加上 HashMap / HashSet 的 18 题、滑动窗口主线的 19 题及本轮确认完成的 523、304 两题，共 133 题。滑动窗口这 19 题为 643、1456、1343、2461、904、1004、1208、424、1695、1234、1658、713、1358、2962、930、1248、992、239、1438；这里按用户确认登记完成，不代表每题都在本轮展示并核验过代码。416、525、1524 暂不计入完成；平台上未录入本清单的其他题不在此统计中。

**当前复盘：523. Continuous Subarray Sum 与 304. Range Sum Query 2D - Immutable；当前唯一主线是 Prefix Sum（前缀和与区间统计深化）。**

**Sliding Window（滑动窗口）主线已全部完成。** 前缀和已从 525 开始，下面保留后续新题顺序；525、1524 已整理笔记，提交通过未确认，不计入完成数量。前缀和笔记见 [[Prefix Sum Problems Summary]]，已整体并入 `01-Array/04-Prefix Sum`；滑动窗口复习入口为 [[00-滑动窗口分类导航]]。

2026-09-30 新增 [[06-523-Continuous Subarray Sum]] 与 [[07-304-Range Sum Query 2D - Immutable]]：两题按本轮用户确认计入完成，平台通过未核验。523 记录同余数与最早位置的标准参考解法；304 保留本次重叠、补零和边界问题的详细复盘。

已经覆盖的能力：

- HashMap / HashSet：存在性、计数、双向映射、下标与状态、key 设计、哈希表实现、组合结构
- Two Pointers：左右夹逼、快慢指针、原地覆盖
- Sliding Window：定长窗口、最长与最短区间、至多 / 至少 / 恰好 K 计数、单调队列维护定长最值
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

题目数量不是换模块的唯一依据；结合题型覆盖、独立解题与迁移表现选择下一步，并明确保留尚未掌握的分支。专项 Hard 可单独暂存。已经完成的 LeetCode 题不重新安排；巩固通过新题、对比整理和间隔一段时间后的闭卷复述完成。

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

## 第五阶段 / HashMap 与 HashSet：主干已完成

### 当前进度（2026-09-11）

原路线 18 题均已完成。本轮补完 `149. Max Points on a Line`（Hard）：自己的 `double` 版本在修正单点初始化、整数除法和正负零后通过，并补充学习了 GCD 约分。下表记录完成进度；能否独立迁移仍要结合后续新题中的表现判断。

入口：[[HashMap HashSet Problems Summary]]。笔记只记录实际遇到、值得复习的问题；没有单独题解的题目仍按本次确认计入完成。已有 [[07-202 - Happy Number]] 的数位存储与状态判环、[[08-36 - Valid Sudoku]] 的分区域去重与坐标换算等复盘继续保留。

### 01. 存在性、计数与映射

| LeetCode | Problem | Priority |
| -------- | ------- | -------- |
| 349 | Intersection of Two Arrays | ⭐⭐⭐ |
| 350 | Intersection of Two Arrays II | ⭐⭐⭐ |
| 383 | Ransom Note | ⭐⭐⭐ |
| 205 | Isomorphic Strings | ⭐⭐⭐⭐ |
| 290 | Word Pattern | ⭐⭐⭐ |
| 219 | Contains Duplicate II | ⭐⭐⭐⭐ |

这组区分了五类信息：是否存在、剩余次数、对应关系、双向对应、最近出现下标。HashMap 的 value 应由查询目标决定，而不是固定写成一种含义。

### 02. 状态、约束与查询转换

| LeetCode | Problem | Priority |
| -------- | ------- | -------- |
| 202 | Happy Number | ⭐⭐⭐ |
| 36 | Valid Sudoku | ⭐⭐⭐⭐ |
| 454 | 4Sum II | ⭐⭐⭐⭐ |
| 128 | Longest Consecutive Sequence | ⭐⭐⭐⭐⭐ |
| 1657 | Determine if Two Strings Are Close | ⭐⭐⭐⭐ |
| 554 | Brick Wall | ⭐⭐⭐⭐ |

这组把重复搜索转换为可查询的状态：记录已访问状态、分别维护多组约束、折半枚举配对和、避免重复扩展连续段，以及提取频率特征或砖缝位置作为 key。

### 03. 哈希表实现与组合结构

| LeetCode | Problem | Priority |
| -------- | ------- | -------- |
| 705 | Design HashSet | ⭐⭐⭐ |
| 706 | Design HashMap | ⭐⭐⭐ |
| 380 | Insert Delete GetRandom O(1) | ⭐⭐⭐⭐⭐ |
| 981 | Time Based Key-Value Store | ⭐⭐⭐⭐ |
| 939 | Minimum Area Rectangle | ⭐⭐⭐⭐ |

这组覆盖桶与冲突处理、key-value 更新、数组下标映射、按 key 分组后二分查询，以及用点集判断二维关系。

### 04. 几何方向与 key 规范化

| LeetCode | Problem | Difficulty | 状态 |
| -------- | ------- | ---------- | ---- |
| 149 | [[14-149 - Max Points on a Line\|Max Points on a Line]] | Hard | 已完成；保留自己的 double 解法与 GCD 推导复盘 |

149 的复习重点是固定基准点后再按方向分组、数学等价如何映射到相同的 key，以及 GCD 为什么可以反复取余。单题笔记保留了 `1071` 与 `462` 的完整例子；完成浮点版本不等于 GCD 推导已经熟练，后续可通过口头复述检查。题目保证坐标点互不相同，无需额外设计重复点计数。

### 留作后续巩固的概念检查

以下问题通过口头解释、整理和新的变种题检查，不要求重新提交旧题：

- `Set`、频次表、双向映射、下标映射和 visited 状态，分别保留什么信息？
- 频次表示可消耗库存时要减一，表示可复用的配对数量时为什么不减？
- 确定过程的状态重复为什么意味着循环？哈希冲突又为什么不意味着 key 相同？
- 折半枚举为什么能从四层枚举变成两组二层枚举？
- 连续段搜索中，怎样确保每个数字只参与有限次扩展？只写入 visited 却不查询跳过为什么无效？
- 怎样为不便直接比较的对象设计稳定的 key？有哪些范围小且已知的 key 可以用数组直接寻址？
- Hash 查询的平均常数时间依赖什么条件？桶内扫描和总工作量如何分析？
- HashMap 与数组、二分或坐标组合时，各自负责回答哪一类问题？

---

## 已完成主线：Sliding Window（滑动窗口）

### 当前进度与选择理由

**截至 2026-09-29，按用户确认，第一至第六层共 19 题全部完成，包含 239 与 1438；当前已转入前缀和 525。** 已有 [[23-239-Sliding Window Maximum]] 保留自己的堆方案、错误例子、单调队列推导和 Deque 的双端用途。此前新增的 1358、2962、930、1248、992 继续保留；方法之间的联系统一见 [[Sliding Window Problems Summary]]。以下路线与检查点保留供 recall 使用。

实际笔记目录已在 [[00-滑动窗口分类导航]] 中细分：可变窗口按“最长区间与最大得分、最短区间与反向转换、至多或小于计数、至少与覆盖计数、恰好 K 计数”归档。239 放在定长窗口中；菜单的第六层是学习顺序，单调队列是维护工具，不是与定长 / 可变并列的窗口长度类别。

本轮直接看到 1248 的正确 cal 结构、1358 的补集实现与修正，以及 930 的失败计数方式和推导。992 等题未在本轮展示独立完整实现，不据此否定完成状态，也不直接标为独立熟练。继续练习时重点检查是否能自己说明“为什么能移走 left”和“这一轮究竟数了哪些起止位置”。

643 的原有复盘保留两处实际错误：`left` 已在上一轮递增，移出的应是旧窗口左端；`(double) (sum / k)` 在整数除法之后转换，已经丢失小数。优化时比较窗口总和、最后除一次 `k` 即可，原来的固定窗口思路已经达到最优 `O(n)` 时间和 `O(1)` 额外空间。

前两批已经做过 `3、209、567、438、76`，本轮只把它们作为概念参照。接下来补齐定长窗口、可变窗口、求最长与求最短、统计子数组、恰好 K，以及窗口最值之间的联系。

刚完成的 HashMap / HashSet 训练可以直接迁移：频次表从“整个数组的统计”变成“当前窗口内的统计”，每次加入和移出都要保持含义一致。这样能够在新题中持续巩固哈希，而不是切换模块后停止使用。

已有 [[04-53-Maximum Subarray]] 复盘记录过“看到连续子数组就想到窗口”的困惑。本轮尤其要练清楚适用条件：连续性只是第一步，还要解释左边界为什么可以安全右移。定长窗口可以包含负数；利用区间和阈值收缩的普通可变窗口，则需要另外检查数值条件与单调性。

以下是递进主线，顺序按前置知识安排，不单按平台难度排列。每层练习后检查理解，发现缺口就在本层追加未做过的新题；完成表格本身不代表模块已经掌握。

### 第一层：定长窗口——先把加入和移出做稳

| Order | LeetCode | Problem | Difficulty | 核心训练 | 状态 |
| ----- | -------- | ------- | ---------- | -------- | ---- |
| 1 | 643 | [[09-643-Maximum Average Subarray I\|Maximum Average Subarray I]] | Easy | 维护固定长度的和；处理首个窗口、负数与平均值 | 已完成 |
| 2 | 1456 | [Maximum Number of Vowels in a Substring of Given Length](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | Medium | 把窗口和迁移为满足某个条件的字符数量 | 已完成，按本轮确认 |
| 3 | 1343 | [Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold](https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/) | Medium | 从求最值变为统计满足条件的定长窗口 | 已完成 |
| 4 | 2461 | [Maximum Sum of Distinct Subarrays With Length K](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/) | Medium | 同时维护窗口和与频次，判断窗口内是否有重复 | 已完成，按本轮确认 |

整理重点：明确区间边界、何时形成完整窗口、加入与移出的对称关系。2461 中频次降到零时应删除对应 key，或同步减少单独维护的种类数；窗口和需要按数据范围选用 `long`。

能解释相邻窗口只变动两端、无需重新扫描后，再进入可变窗口。

### 第二层：可变窗口——求最长合法区间

| Order | LeetCode | Problem | Difficulty | 核心训练 |
| ----- | -------- | ------- | ---------- | -------- |
| 5 | 904 | [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) | Medium | 将两个篮子转换成窗口内最多两种元素 |
| 6 | 1004 | [Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/) | Medium | 把翻转次数转换成窗口内零的数量限制 |
| 7 | 1208 | [Get Equal Substrings Within Budget](https://leetcode.com/problems/get-equal-substrings-within-budget/) | Medium | 从零的个数推广到非负修改费用之和 |
| 8 | 424 | [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) | Medium | 推导窗口长度与最高字符频次之间的关系 |
| 9 | 1695 | [Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/) | Medium | 将无重复窗口与正整数区间和结合，改变答案目标 |

整理重点：每题先说出“什么算非法”，再解释为什么缩小可以恢复合法、为什么被丢弃的左端点无需回头。说明两层循环的总工作量，不能仅凭嵌套外观判断为平方级。

424 先用扫描 26 个计数求当前真实最大频次的版本，理解完整的合法窗口；需要优化时再讨论历史最大频次写法及不同的不变量。1695 要能解释正整数条件为何允许在最长的无重复后缀上更新最大和。

### 第三层：求最短与反向转换

| Order | LeetCode | Problem | Difficulty | 核心训练 |
| ----- | -------- | ------- | ---------- | -------- |
| 10 | 1234 | [Replace the Substring for Balanced String](https://leetcode.com/problems/replace-the-substring-for-balanced-string/) | Medium | 用窗口外剩余频次判断能否通过替换窗口使全串平衡 |
| 11 | 1658 | [Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/) | Medium | 将两端删除转换成中间保留的连续区间 |

整理重点：求最长时通常在恢复合法后更新；求最短覆盖时，在仍然合法的收缩过程中记录候选答案。不要把一套更新位置机械套到所有题。

1658 先说明删除部分与保留部分的互补关系，再利用题目全为正数的条件寻找最长保留区间；检查目标和为零、目标和为负及无解的区别。题目写“最少操作”，不代表窗口本身一定求最短。

### 第四层：从求一个最优区间，到统计全部合法区间

| Order | LeetCode | Problem                                                                                                                                                 | Difficulty | 核心训练                        |
| ----- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------------------- |
| 12    | 713      | [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)                                                             | Medium     | 固定右端点，推导这一轮可计入多少个合法起点       |
| 13    | 1358     | [Number of Substrings Containing All Three Characters](https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/)             | Medium     | 从“至多”限制切换到“至少覆盖”，重新确定合法起点范围 |
| 14    | 2962     | [Count Subarrays Where Max Element Appears at Least K Times](https://leetcode.com/problems/count-subarrays-where-max-element-appears-at-least-k-times/) | Medium     | 将覆盖三个字符迁移成目标元素出现次数阈值        |

整理重点：先画出固定右端点时所有合法左端点的范围，再推导计数公式；不能看到计数题就背 `right - left + 1`。说明为什么既没有漏计，也没有重复计。

713 的元素是正整数，先处理 `k <= 1`。2962 统计的是**整个 nums 的最大值**在各个窗口里的出现次数，不是在每个窗口重新找最大值；答案可能需要 `long`。

### 第五层：恰好 K——将相等条件拆成可维护的范围

| Order | LeetCode | Problem                                                                                                   | Difficulty | 核心训练                      |
| ----- | -------- | --------------------------------------------------------------------------------------------------------- | ---------- | ------------------------- |
| 15    | 930      | [Binary Subarrays With Sum](https://leetcode.com/problems/binary-subarrays-with-sum/)                     | Medium     | 在 0/1 数组中将恰好目标和的计数转换为范围计数 |
| 16    | 1248     | [Count Number of Nice Subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays/)           | Medium     | 将奇偶性转成 0/1 贡献，迁移上一题的计数方法  |
| 17    | 992      | [Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) | Hard       | 结合频次表、种类数与恰好 K 的计数，作为综合检验 |

先从集合包含关系推导 `exactly(K) = atMost(K) - atMost(K - 1)`，再定义辅助函数；分别处理 `K = 0`、负阈值和空窗口。能做差不代表 `atMost` 一定能用普通窗口求出，必须检查指标随窗口扩张、收缩的变化。

930 也能用前缀和与哈希计数。这里用它比较两种方法的条件：非负数组的区间和可以支持普通阈值窗口；改成任意整数后，前缀和计数仍有适用空间。旧题 560 只作概念参照，不重做。

992 等前两题的计数推导稳定后再进入。若卡住，按“种类数维护”或“计数推导”的具体缺口补新的中间题；它是一道综合题，不是单凭 Hard 标签设定的毕业门槛。

### 第六层：窗口最值——单调队列进阶

这是本模块的后段扩展。239 已完成并整理复盘；1438 按 2026-09-29 用户确认记为已完成。

| Order | LeetCode | Problem | Difficulty | 核心训练 | 状态 |
| ----- | -------- | ------- | ---------- | -------- | ---- |
| 18 | 239 | [[23-239-Sliding Window Maximum\|Sliding Window Maximum]] | Hard | 用候选下标维护定长窗口最大值，理解过期与淘汰 | 已完成，按本轮确认；已整理堆与单调队列 |
| 19 | 1438 | [Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) | Medium | 用两个单调队列维护可变窗口的最大值和最小值 | 已完成，按 2026-09-29 用户确认 |

239 的复盘用 `[1,3,1,2,0,5]、k=3` 手画候选队列：队首处理过期下标，队尾删除被新元素支配的候选。单调栈经验可以帮助理解淘汰，但队列还要负责窗口过期；这里先学单个定长最大值，再组合成 1438 的可变极差约束，因此顺序不按 Easy / Medium / Hard 机械排列。239 的完成状态按用户确认登记，讲解过单调队列不等于本轮已展示并验证独立的单调队列实现。

这一层单独记录掌握状态。如果暂缓，写明“基础窗口与计数已掌握，单调队列待进阶”，不用一道难题代替整个模块的能力判断。

### 怎样持续巩固与调整

- 同一小模型连续做新的题。每完成一个小组，闭卷整理“识别条件 → 状态含义 → 加入／移出 → 更新答案 → 复杂度与边界”。
- 经过后面的几个新题，再口头回忆前面的小模型，并对照笔记补缺；检查同一个错误是否仍然出现。
- 将哈希频次的增减、零频次处理、是否需要保存次数等判断贯穿整个模块，持续巩固上一阶段。
- 每次先看你的思路和实现，再决定提示或补题；不预先把每道题完整解法写进 menu。
- 补充题必须先与完成清单、题解笔记去重。后续检查题临近学习节点再选择，不预先标出所属小模型。
- 常见模型已能独立处理后，用新的综合题检查迁移。仍有缺口就针对该缺口补题；这 19 个练习位可按表现调整，不是固定配额。

### 本模块掌握标准

能够解释并在未做过的题里应用下面这些判断：

1. 为什么能使用窗口，哪些条件变化会破坏当前解法；区分定长窗口与依赖单调条件的可变窗口。
2. 当前 `[left, right]` 和统计量分别表示什么，加入与移出后如何保持一致。
3. 求最长、求最短、计数时，收缩条件和答案更新时机为什么不同。
4. 计数时合法左端点构成什么范围，为什么本轮贡献不会漏计或重计。
5. 恰好 K 为什么能做两次范围计数，以及辅助窗口成立的前提。
6. 两个指针的总移动次数如何分析，什么时候需要 `long`。
7. 在新的变种中持续正确使用频次表，并能比较窗口与前缀和等候选方法。
8. 若已进入单调队列扩展，能解释过期下标和被支配候选值的区别。

后续根据独立解题、隔一段时间的概念回忆和新变种表现，更新实际掌握情况。Greedy 与 DP 进阶继续暂缓。

---

## 当前唯一主线：Prefix Sum（前缀和与区间统计深化）

### 启动时间与学习目标

**2026-09-29：滑动窗口主线已全部完成，正式进入本模块；525 与 1524 已讨论并分别整理复盘。2026-09-30：523、304 按用户确认记录已完成，并新增笔记与完整 Java 答案。** 525 记录完整 Java 答案，1524 记录自己的前缀数组思路、空前缀修正及每轮处理前后的状态。525、1524 尚未确认完成；974、1314 待开始。平台通过情况单独记录，仍然一次只推进一个主模块。

目录归类：前缀和是一种方法，笔记集中放在 `01-Array/04-Prefix Sum`，与数组下的双指针、滑动窗口方法并列。原有 303、560、238 及 [[Prefix Sum Problems Summary]] 一并归入，不再保留单独的顶层前缀目录；总入口见 [[Array Problems Summary]]。

滑动窗口的 recall 可围绕两种计数公式、恰好 K 的范围转换，以及单调队列的过期与淘汰。后续若发现具体缺口，再针对性补新题；不为换模块额外设置一长串必刷旧题。

选择前缀和是因为刚练过的 HashMap 频次和连续区间计数可以直接衔接，而区间条件未必总能支持“超标就缩窗”。这一模块要学会判断：什么时候可以安全移走左端点，什么时候应保留历史前缀信息，按当前目标查询对应状态。

已有基础为 303、560、238，只通过 [[Prefix Sum Problems Summary]] 回顾概念，不重新安排提交。930、1248 也只作方法对照，不当作新的练习重复计数。新题中先看自己的想法和实现，菜单只记录训练目标，不预先给完整解法。

### 第一层：前缀状态——区分求最长与统计数量

| 顺序  | LeetCode | Problem                                                                          | 难度     | 核心训练                                      | 状态                       |
| --- | -------- | -------------------------------------------------------------------------------- | ------ | ----------------------------------------- | ------------------------ |
| 1   | 525      | [[04-525-Contiguous Array\|Contiguous Array]]                                    | Medium | 0 和 1 数量相等的最长区间；把平衡条件转换成可查询状态，判断应保存哪种位置信息 | 已整理推导与完整 Java 答案；提交通过待确认 |
| 2   | 1524     | [[05-1524-Number of Sub-arrays With Odd Sum\|Number of Sub-arrays With Odd Sum]] | Medium | 和为奇数的子数组数量；从记录位置转为累计匹配次数，并处理答案取模          | 已整理个人思路与前中后状态逻辑；提交通过待确认  |

这一层要能说明 Map 或数组的 value 为什么有时保存位置、有时保存次数。与滑动窗口对比时，从条件是否支持安全排除起点出发，不能只凭输入有无负数选择算法；例如和的奇偶性在加入正数后也可能反复变化。

### 第二层：余数状态——从数量迁移到存在性与长度限制

| 顺序  | LeetCode | Problem                                                                                     | 难度     | 核心训练                                     | 状态  |
| --- | -------- | ------------------------------------------------------------------------------------------- | ------ | ---------------------------------------- | --- |
| 3   | 974      | [Subarray Sums Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/) | Medium | 统计和能被 k 整除的子数组；确定应保存的前缀状态，处理负数与余数        | 待开始 |
| 4   | 523      | [[06-523-Continuous Subarray Sum\|Continuous Subarray Sum]]                              | Medium | 判断是否存在长度至少为 2、和为 k 的倍数的子数组；重新选择历史信息的保存方式 | 已完成，按 2026-09-30 用户确认；已整理标准参考解法，平台通过未核验 |

先做数量问题，再做条件相近但答案改为存在性的问题，避免机械复用同一个 value。单独检查零元素、从下标 0 开始的区间，以及查询与登记当前状态的先后关系。

### 第三层：二维前缀——矩形查询与边界迁移

| 顺序  | LeetCode | Problem                                                                                       | 难度     | 核心训练                               | 状态  |
| --- | -------- | --------------------------------------------------------------------------------------------- | ------ | ---------------------------------- | --- |
| 5   | 304      | [[07-304-Range Sum Query 2D - Immutable\|Range Sum Query 2D - Immutable]]                 | Medium | 多次查询矩形区域之和；从一维前缀定义推广到二维，解释重叠部分如何处理 | 已完成，按 2026-09-30 用户确认；已整理边界复盘与 Java 答案，平台通过未核验 |
| 6   | 1314     | [Matrix Block Sum](https://leetcode.com/problems/matrix-block-sum/)                           | Medium | 为矩阵每个位置求周围块的总和；复用矩形查询并处理靠边时的范围裁剪   | 待开始 |

这一层先画区域再写下标，能够说明预处理负责什么、单次查询负责什么。不能只背二维公式而说不清哪些区域被重复扣除。

### 核心完成后的扩展：按表现选择

六道核心新题做完后，根据独立推导和新题迁移情况决定扩展；不把做满某个数量等同于掌握。

- **差分与区间更新**：[1109. Corporate Flight Bookings](https://leetcode.com/problems/corporate-flight-bookings/) → [1094. Car Pooling](https://leetcode.com/problems/car-pooling/)。由“查询区间累计量”转向“多次修改区间，再恢复各位置状态”，继续练边界及累计含义。这是可选扩展，不与核心路线并行开新主线。
- **二维计数综合**：[1074. Number of Submatrices That Sum to Target](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/)（Hard）。在二维查询和一维计数稳定后再尝试，检查如何组合已有方法；暂缓这题不代表前缀和基础未掌握。

综合迁移检查题到时再从未做过的题中选择，不提前揭示所属模型。Greedy 与 DP 进阶仍不在本轮安排中。

### 本模块掌握标准

1. 能定义前缀状态的含义，解释区间条件为什么对应两个前缀之间的关系。
2. 能根据求最长、计数或存在性，决定保存最早位置、出现次数还是其他信息。
3. 能解释初始空前缀、先查历史再登记当前状态，以及零、负数、取模和数值范围带来的边界。
4. 能比较普通阈值窗口与前缀方法的适用条件，不把“连续子数组”自动等同于其中一种。
5. 能从图形推导二维查询，正确处理矩阵边缘和空前缀边界。
6. 若进入差分扩展，能说清前缀累计与变化量恢复的关系，以及区间端点是否包含。
