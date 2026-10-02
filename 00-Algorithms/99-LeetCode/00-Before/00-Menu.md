# LeetCode Menu

## 当前完成情况

截至 2026-10-01，最新完成 **1314. Matrix Block Sum**。前四阶段、HashMap / HashSet 专题和 Sliding Window 主线均已完成；当前继续推进第七阶段 **Prefix Sum：前缀和、差分与区间统计**。

- 第一批基础题：20 题
- 第二批基础加强题：20 题
- 第三批进阶入门题：18 题
- 第四阶段核心进阶：新增 36 题（跨阶段复习题去重）
- 第五阶段 HashMap / HashSet：新增 18 题
- 第六阶段 Sliding Window：新增 19 题
- 第七阶段 Prefix Sum：前三层六题全部完成，包含 525、1524、974、523、304、1314
- 当前清单累计登记完成：**137 道不重复题目**

统计按题号去重：94 + 18 + 19 + 6 = 137。完成状态依据已有清单与用户确认；独立掌握程度通过后续新题检查。525、1524、974 已按 2026-10-01 用户确认补记完成；416 仍暂缓，未计入总数。

**当前走到第七阶段第四层：差分与区间更新；下一题是 1109. Corporate Flight Bookings。完整后续路线见文末，按层连续推进。**

已完成题统一使用 `LeetCode | Problem | Priority` 三列表，沿用原来的阶段与小节格式。Priority 表示复习与迁移的重要程度；训练说明放在表后，当前待做路线另列。

复习入口：[[HashMap HashSet Problems Summary]]、[[00-滑动窗口分类导航]]、[[00-前缀和分类导航]]、[[Prefix Sum Problems Summary]]。

已经覆盖的能力：

- HashMap / HashSet：存在性、计数、双向映射、下标与状态、key 设计、哈希表实现、组合结构
- Two Pointers：左右夹逼、快慢指针、原地覆盖
- Sliding Window：定长窗口、最长与最短区间、至多 / 至少 / 恰好 K 计数、单调队列维护定长最值
- Binary Search：基础二分、二维矩阵二分
- Stack / Monotonic Stack：括号匹配、最小栈、下一个更大元素
- Linked List：反转、合并、找环、找中点、删除倒数节点、相交链表
- Tree / BFS / DFS / BST：递归、层序遍历、路径判断、BST 判断
- Backtracking：子集、排列、组合、组合总和
- Prefix Sum：一维前缀和、HashMap 前缀和计数、同余状态与长度限制、二维矩形查询及块边界裁剪、前后缀乘积
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

每个模块在 menu 中保留整段分层路线。后续更新时，把完成题归档、移动当前进度，并根据实际薄弱点调整后面的变种；沿已有路线继续往下走。

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

## 已完成 / 第五阶段：HashMap 与 HashSet

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

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 149 | [[14-149 - Max Points on a Line\|Max Points on a Line]] | ⭐⭐⭐⭐⭐ |

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

## 已完成 / 第六阶段：Sliding Window（滑动窗口）

### 完成情况与复习入口

**截至 2026-09-29，按用户确认，第一至第六层共 19 题全部完成，包含 239 与 1438。** 已有 [[23-239-Sliding Window Maximum]] 保留自己的堆方案、错误例子、单调队列推导和 Deque 的双端用途。此前新增的 1358、2962、930、1248、992 继续保留；方法之间的联系统一见 [[Sliding Window Problems Summary]]。以下路线与检查点保留供 recall 使用。

实际笔记目录已在 [[00-滑动窗口分类导航]] 中细分：可变窗口按“最长区间与最大得分、最短区间与反向转换、至多或小于计数、至少与覆盖计数、恰好 K 计数”归档。239 放在定长窗口中；菜单的第六层是学习顺序，单调队列是维护工具，不是与定长 / 可变并列的窗口长度类别。

本轮直接看到 1248 的正确 cal 结构、1358 的补集实现与修正，以及 930 的失败计数方式和推导。992 等题未在本轮展示独立完整实现，不据此否定完成状态，也不直接标为独立熟练。继续练习时重点检查是否能自己说明“为什么能移走 left”和“这一轮究竟数了哪些起止位置”。

643 的原有复盘保留两处实际错误：`left` 已在上一轮递增，移出的应是旧窗口左端；`(double) (sum / k)` 在整数除法之后转换，已经丢失小数。优化时比较窗口总和、最后除一次 `k` 即可，原来的固定窗口思路已经达到最优 `O(n)` 时间和 `O(1)` 额外空间。

前两批已经做过 `3、209、567、438、76`，本轮只把它们作为概念参照。这一阶段已覆盖定长窗口、可变窗口、求最长与求最短、统计子数组、恰好 K，以及窗口最值之间的联系。

刚完成的 HashMap / HashSet 训练可以直接迁移：频次表从“整个数组的统计”变成“当前窗口内的统计”，每次加入和移出都要保持含义一致。这样能够在新题中持续巩固哈希，而不是切换模块后停止使用。

已有 [[04-53-Maximum Subarray]] 复盘记录过“看到连续子数组就想到窗口”的困惑。本轮尤其要练清楚适用条件：连续性只是第一步，还要解释左边界为什么可以安全右移。定长窗口可以包含负数；利用区间和阈值收缩的普通可变窗口，则需要另外检查数值条件与单调性。

以下按原学习层次归档已完成题，表格与前五阶段统一。Priority 表示复习与迁移的重要程度，不等于平台难度；表后保留各组覆盖内容和复习检查点。

### 01. 定长窗口

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 643 | [[09-643-Maximum Average Subarray I\|Maximum Average Subarray I]] | ⭐⭐⭐ |
| 1456 | [Maximum Number of Vowels in a Substring of Given Length](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | ⭐⭐⭐ |
| 1343 | [Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold](https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/) | ⭐⭐⭐ |
| 2461 | [Maximum Sum of Distinct Subarrays With Length K](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/) | ⭐⭐⭐⭐ |

本组覆盖：643：维护固定长度的和；处理首个窗口、负数与平均值；1456：把窗口和迁移为满足某个条件的字符数量；1343：从求最值变为统计满足条件的定长窗口；2461：同时维护窗口和与频次，判断窗口内是否有重复。

整理重点：明确区间边界、何时形成完整窗口、加入与移出的对称关系。2461 中频次降到零时应删除对应 key，或同步减少单独维护的种类数；窗口和需要按数据范围选用 `long`。

复习时应能解释相邻窗口为什么只变动两端、无需重新扫描，并对比可变窗口中边界移动的依据。

### 02. 可变窗口：最长合法区间

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 904 | [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) | ⭐⭐⭐⭐ |
| 1004 | [Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/) | ⭐⭐⭐⭐ |
| 1208 | [Get Equal Substrings Within Budget](https://leetcode.com/problems/get-equal-substrings-within-budget/) | ⭐⭐⭐ |
| 424 | [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) | ⭐⭐⭐⭐⭐ |
| 1695 | [Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/) | ⭐⭐⭐⭐ |

本组覆盖：904：将两个篮子转换成窗口内最多两种元素；1004：把翻转次数转换成窗口内零的数量限制；1208：从零的个数推广到非负修改费用之和；424：推导窗口长度与最高字符频次之间的关系；1695：将无重复窗口与正整数区间和结合，改变答案目标。

整理重点：每题先说出“什么算非法”，再解释为什么缩小可以恢复合法、为什么被丢弃的左端点无需回头。说明两层循环的总工作量，不能仅凭嵌套外观判断为平方级。

424 先用扫描 26 个计数求当前真实最大频次的版本，理解完整的合法窗口；需要优化时再讨论历史最大频次写法及不同的不变量。1695 要能解释正整数条件为何允许在最长的无重复后缀上更新最大和。

### 03. 最短区间与反向转换

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 1234 | [Replace the Substring for Balanced String](https://leetcode.com/problems/replace-the-substring-for-balanced-string/) | ⭐⭐⭐⭐ |
| 1658 | [Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/) | ⭐⭐⭐⭐⭐ |

本组覆盖：1234：用窗口外剩余频次判断能否通过替换窗口使全串平衡；1658：将两端删除转换成中间保留的连续区间。

整理重点：求最长时通常在恢复合法后更新；求最短覆盖时，在仍然合法的收缩过程中记录候选答案。不要把一套更新位置机械套到所有题。

1658 先说明删除部分与保留部分的互补关系，再利用题目全为正数的条件寻找最长保留区间；检查目标和为零、目标和为负及无解的区别。题目写“最少操作”，不代表窗口本身一定求最短。

### 04. 区间计数：至多与至少

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 713 | [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/) | ⭐⭐⭐⭐ |
| 1358 | [Number of Substrings Containing All Three Characters](https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/) | ⭐⭐⭐⭐ |
| 2962 | [Count Subarrays Where Max Element Appears at Least K Times](https://leetcode.com/problems/count-subarrays-where-max-element-appears-at-least-k-times/) | ⭐⭐⭐⭐ |

本组覆盖：713：固定右端点，推导这一轮可计入多少个合法起点；1358：从“至多”限制切换到“至少覆盖”，重新确定合法起点范围；2962：将覆盖三个字符迁移成目标元素出现次数阈值。

整理重点：先画出固定右端点时所有合法左端点的范围，再推导计数公式；不能看到计数题就背 `right - left + 1`。说明为什么既没有漏计，也没有重复计。

713 的元素是正整数，先处理 `k <= 1`。2962 统计的是**整个 nums 的最大值**在各个窗口里的出现次数，不是在每个窗口重新找最大值；答案可能需要 `long`。

### 05. 恰好 K 计数

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 930 | [Binary Subarrays With Sum](https://leetcode.com/problems/binary-subarrays-with-sum/) | ⭐⭐⭐⭐⭐ |
| 1248 | [Count Number of Nice Subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays/) | ⭐⭐⭐⭐ |
| 992 | [Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) | ⭐⭐⭐⭐⭐ |

本组覆盖：930：在 0/1 数组中将恰好目标和的计数转换为范围计数；1248：将奇偶性转成 0/1 贡献，迁移上一题的计数方法；992：结合频次表、种类数与恰好 K 的计数，作为综合检验。

先从集合包含关系推导 `exactly(K) = atMost(K) - atMost(K - 1)`，再定义辅助函数；分别处理 `K = 0`、负阈值和空窗口。能做差不代表 `atMost` 一定能用普通窗口求出，必须检查指标随窗口扩张、收缩的变化。

930 也能用前缀和与哈希计数。这里用它比较两种方法的条件：非负数组的区间和可以支持普通阈值窗口；改成任意整数后，前缀和计数仍有适用空间。旧题 560 只作概念参照，不重做。

992 的复习重点是把前两题的计数推导与种类数维护结合起来。若新题迁移时仍卡住，按“种类数维护”或“计数推导”的具体缺口补题；不能只凭 Hard 标签判断整个模块是否掌握。

### 06. 窗口最值与单调队列

这是本模块的后段扩展。239 已完成并整理复盘；1438 按 2026-09-29 用户确认记为已完成。

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 239 | [[23-239-Sliding Window Maximum\|Sliding Window Maximum]] | ⭐⭐⭐⭐⭐ |
| 1438 | [Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) | ⭐⭐⭐⭐⭐ |

本组覆盖：239：用候选下标维护定长窗口最大值，理解过期与淘汰；1438：用两个单调队列维护可变窗口的最大值和最小值。

239 的复盘用 `[1,3,1,2,0,5]、k=3` 手画候选队列：队首处理过期下标，队尾删除被新元素支配的候选。单调栈经验可以帮助理解淘汰，但队列还要负责窗口过期；这里先学单个定长最大值，再组合成 1438 的可变极差约束，因此顺序不按 Easy / Medium / Hard 机械排列。239 的完成状态按用户确认登记，讲解过单调队列不等于本轮已展示并验证独立的单调队列实现。

单调队列的独立掌握情况仍通过新题迁移检查：重点解释候选为什么能淘汰、下标何时过期。

### 怎样持续巩固与调整

- 同一小模型连续做新的题。每完成一个小组，闭卷整理“识别条件 → 状态含义 → 加入／移出 → 更新答案 → 复杂度与边界”。
- 经过后面的几个新题，再口头回忆前面的小模型，并对照笔记补缺；检查同一个错误是否仍然出现。
- 将哈希频次的增减、零频次处理、是否需要保存次数等判断贯穿整个模块，持续巩固上一阶段。
- 每次先看你的思路和实现，再决定提示或补题；不预先把每道题完整解法写进 menu。
- 补充题必须先与完成清单、题解笔记去重。后续检查题临近学习节点再选择，不预先标出所属小模型。
- 用新的综合题检查已学模型能否独立迁移；仍有缺口就针对该缺口补题，已完成的 19 题保留为复习索引。

### 本模块掌握标准

能够解释并在未做过的题里应用下面这些判断：

1. 为什么能使用窗口，哪些条件变化会破坏当前解法；区分定长窗口与依赖单调条件的可变窗口。
2. 当前 `[left, right]` 和统计量分别表示什么，加入与移出后如何保持一致。
3. 求最长、求最短、计数时，收缩条件和答案更新时机为什么不同。
4. 计数时合法左端点构成什么范围，为什么本轮贡献不会漏计或重计。
5. 恰好 K 为什么能做两次范围计数，以及辅助窗口成立的前提。
6. 两个指针的总移动次数如何分析，什么时候需要 `long`。
7. 在新的变种中持续正确使用频次表，并能比较窗口与前缀和等候选方法。
8. 能解释单调队列中过期下标和被支配候选值的区别。

后续根据独立解题、隔一段时间的概念回忆和新变种表现，更新实际掌握情况。Greedy 与 DP 进阶继续暂缓。

---

## 已完成 / 第七阶段：Prefix Sum（已完成部分）

本阶段前三层六题均已完成，当前继续第四层。已有基础 303、560、238 记录在第三阶段；这里归档本阶段新增完成的题。523、304 于 2026-09-30 确认，1314、525、1524、974 于 2026-10-01 确认。

### 01. 前缀状态：最长与计数

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 525 | [[04-525-Contiguous Array\|Contiguous Array]] | ⭐⭐⭐⭐ |
| 1524 | [[05-1524-Number of Sub-arrays With Odd Sum\|Number of Sub-arrays With Odd Sum]] | ⭐⭐⭐⭐ |

已经练过将两类数量的平衡、前缀和的奇偶性转换为历史状态查询，并区分求最长与统计数量时需要保存的信息。525 的笔记保留推导与完整 Java 答案；1524 保留个人思路、空前缀修正和逐轮状态。复习时解释初始化、查询与登记顺序及答案取模。不能仅凭数组是否包含负数决定能否用窗口，例如正数的前缀奇偶性同样不单调。

### 02. 余数状态：计数与存在性

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 974 | [Subarray Sums Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/) | ⭐⭐⭐⭐ |
| 523 | [[06-523-Continuous Subarray Sum\|Continuous Subarray Sum]] | ⭐⭐⭐⭐ |

已经练过同余状态与区间整除条件之间的关系，以及统计数量和判断存在性时为什么要选择不同的历史信息。复习时检查负数余数、空前缀、零元素、从下标 0 开始的区间、523 的长度限制，以及查询和登记的顺序。

### 03. 二维前缀与区域查询

| LeetCode | Problem | Priority |
| --- | --- | --- |
| 304 | [[07-304-Range Sum Query 2D - Immutable\|Range Sum Query 2D - Immutable]] | ⭐⭐⭐⭐⭐ |
| 1314 | [Matrix Block Sum](https://leetcode.com/problems/matrix-block-sum/) | ⭐⭐⭐⭐ |

已经练过矩形查询、重叠区域补偿，以及将每个位置周围的块裁剪到矩阵内。304 的笔记保留了补零、坐标含义与 Java 答案；复习时先画区域，再解释构建与查询中的符号。1314 按用户确认登记完成，本轮未查看实现。

---

## 当前主线 / 第七阶段：Prefix Sum 完整后续路线

### 整体路线与当前所在位置

本模块从 2026-09-29 开始。原路线的前三层是前缀状态、余数状态、二维前缀；后段原已安排差分与二维计数综合。现在沿这条方向展开标准题、变种与组合应用，让整个模块有连续的学习顺序。

| 层次 | 学习内容 | 题目顺序 | 当前进度 |
| --- | --- | --- | --- |
| 第一层 | 前缀状态：最长与计数 | 525 → 1524 | 已完成 |
| 第二层 | 余数状态：计数与存在性 | 974 → 523 | 已完成 |
| 第三层 | 二维前缀：查询与边界 | 304 → 1314 | 已完成 |
| **第四层** | **一维差分：批量区间更新** | **1109 → 1094 → 2381** | **当前从这里继续** |
| 第五层 | 二维差分：矩形更新 | 2536 | 待开始 |
| 第六层 | 前缀状态深化：最短与不等式 | 1590 → 1124 | 待开始 |
| 第七层 | 前缀统计与树上路径 | 437 | 待开始 |
| 第八层 | 二维查询与搜索、计数组合 | 1292 → 1074 | 1292 待开始；1074 为可选进阶 |

**当前后续主线顺序：1109 → 1094 → 2381 → 2536 → 1590 → 1124 → 437 → 1292。** 随后根据表现进入 1074 或综合迁移检查。下方另保留稀疏边界、前缀异或两条扩展分支，明确它们尚未覆盖。

这份路线按知识依赖组织：先接住 304、1314 的累计与边界，再改变区间更新方式和查询目标，最后结合已经学过的树遍历与答案空间搜索。每一层都用新的题目练习同一方法的变化；题量随实际缺口调整。

目录入口为 [[00-前缀和分类导航]]，跨题推导见 [[Prefix Sum Problems Summary]]，数组总入口见 [[Array Problems Summary]]。笔记继续集中在 `01-Array/04-Prefix Sum`，新分类在产生实际复盘后再补充。

### 第四层：一维差分——从查询区间到批量修改区间

| 顺序  | LeetCode | Problem                                                                               | Difficulty | 核心训练                               |
| --- | -------- | ------------------------------------------------------------------------------------- | ---------- | ---------------------------------- |
| 1   | 1109     | [Corporate Flight Bookings](https://leetcode.com/problems/corporate-flight-bookings/) | Medium     | 对连续航班多次增加座位，最后求各航班总数；建立区间更新与变化量的联系 |
| 2   | 1094     | [Car Pooling](https://leetcode.com/problems/car-pooling/)                             | Medium     | 将累计结果用于容量判断，重新解释上下车端点与区间是否包含终点     |
| 3   | 2381     | [Shifting Letters II](https://leetcode.com/problems/shifting-letters-ii/)             | Medium     | 将区间更新迁移到字符前移、后移及字母表循环              |

这层沿用原定的 1109 → 1094，并用 2381 检查能否迁移到不同题面。先说明每个记录代表什么，再解释如何恢复各位置的最终状态。做完三题后，应能自己处理重叠更新、最后一个位置、同点上下车、正负变化以及 a / z 回绕。

特别对比 1109 的航班编号与数组下标、1094 的下车位置含义。端点由题意决定，不能把上一题的下标直接照搬。先独立估算逐区间修改的总工作量，再说明优化后省掉了哪些重复操作。

### 第五层：二维差分——把一维更新推广到矩形

| 顺序 | LeetCode | Problem | Difficulty | 核心训练 |
| --- | --- | --- | --- | --- |
| 4 | 2536 | [Increment Submatrices by One](https://leetcode.com/problems/increment-submatrices-by-one/) | Medium | 多次给子矩形整体加一，最后恢复矩阵；连接一维差分和已学的二维累计 |

这是对 304、1314 的直接延伸：此前查询一个矩形，现在修改一个矩形。先画清楚变化会影响哪些区域，再比较逐格修改、逐行处理与二维处理的工作量。允许先写出正确的逐行方案，再推导二维方案；复习时要能解释边界与重叠，避免只记四个角的符号。

### 第六层：前缀状态深化——答案目标和关系发生变化

| 顺序 | LeetCode | Problem | Difficulty | 核心训练 |
| --- | --- | --- | --- | --- |
| 5 | 1590 | [Make Sum Divisible by P](https://leetcode.com/problems/make-sum-divisible-by-p/) | Medium | 删除最短连续区间，使剩余总和能被 p 整除；根据最短目标重新选择历史信息 |
| 6 | 1124 | [Longest Well-Performing Interval](https://leetcode.com/problems/longest-well-performing-interval/) | Medium | 求劳累天数严格多于不劳累天数的最长区间；从等量关系推进到大小关系 |

1590 与 974、523 对照：状态相似，题目分别问数量、存在性和最短长度，历史记录的含义必须重新推导。检查无需删除、不能删除全数组、无解、取模以及累计和范围。

1124 与 525 对照：从两类数量相等变成一类严格更多，先写区间条件，再判断之前的相等状态查询能保留哪些部分。`hours[i] > 8` 与两类数量的严格大小关系都要从题意读准确；若使用特殊状态性质优化，要说出它成立的前提。

### 第七层：结构迁移——前缀统计进入树上的路径

| 顺序 | LeetCode | Problem | Difficulty | 核心训练 |
| --- | --- | --- | --- | --- |
| 7 | 437 | [Path Sum III](https://leetcode.com/problems/path-sum-iii/) | Medium | 统计和为目标的向下路径；把一维区间计数迁移到已有的树遍历经验中 |

这层结合已经学过的 DFS 与前缀计数。重点解释遍历到当前节点时，哪些历史信息属于当前路径；离开一个分支后，哪些信息仍能供另一个分支使用。检查路径不必从根开始、也不必在叶子结束，以及负数和路径累计和的范围。这里训练的是前缀记录的有效范围。

### 第八层：二维综合——从快速查询到寻找和统计区域

| 顺序 | LeetCode | Problem | Difficulty | 核心训练 |
| --- | --- | --- | --- | --- |
| 8 | 1292 | [Maximum Side Length of a Square with Sum Less than or Equal to Threshold](https://leetcode.com/problems/maximum-side-length-of-a-square-with-sum-less-than-or-equal-to-threshold/) | Medium | 在二维区域查询之上寻找最大正方形；比较候选枚举与已学的答案空间搜索 |
| 进阶 | 1074 | [Number of Submatrices That Sum to Target](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/) | Hard | 统计所有和为目标的子矩形；组合二维区域与一维区间计数 |

1292 先区分“一个正方形的和怎么算”与“怎样找到最大的合法正方形”。若选择二分，必须说明判定条件的单调性，以及矩阵元素非负这个条件起什么作用；不能只因题目求最大就套二分。

1074 是原计划保留的二维综合进阶。在已知矩形边界时快速查询，不代表枚举所有矩形也已经足够快；先估算候选区域数量，再寻找能够复用的一维子问题。它可以暂缓，暂缓时明确记录二维计数综合尚待进阶。

### 后续扩展分支：按缺口进入

完成上面的主线后，结合实际表现选择；这些是同一方法的进一步分支，暂不插入当前顺序。

| 分支 | LeetCode | Problem | Difficulty | 何时进入 |
| --- | --- | --- | --- | --- |
| 差分边界与分段输出 | 1943 | [Describe the Painting](https://leetcode.com/problems/describe-the-painting/) | Medium | 需要把逐位置恢复推广到按边界组织区间时；练左闭右开、颜色集合的分段语义和累计值范围 |
| 前缀异或入门 | 1310 | [XOR Queries of a Subarray](https://leetcode.com/problems/xor-queries-of-a-subarray/) | Medium | 学完必要的异或性质后，比较前缀抵消与普通前缀和查询 |
| 多个奇偶状态 | 1371 | [Find the Longest Substring Containing Vowels in Even Counts](https://leetcode.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/) | Medium | 在 1310 之后，练多个字符奇偶条件的状态表示和最长区间 |

1943 需要理解题目区分的是混合颜色集合，即使相邻区间输出的颜色和相同，也可能不能合并。1310、1371 涉及新的状态表示，作为后续前缀状态扩展保留。

### 持续推进与模块检查

- 每题先独立读题、写思路和实现；menu 只给学习目标与检查点，具体推导结合自己的尝试展开。
- 做完一层后，闭卷解释“识别条件 → 保存的信息 → 更新与查询顺序 → 边界 → 总工作量”，再继续下一层。发现缺口时在该层补新的变种。
- 经过后面的新题，再回忆前面的模型。303、560、238、523、304、1314 和已完成的窗口题作为概念对照，无需重复提交。
- 主线推进后安排未做过的综合迁移题，题号临近检查时再选，不提前标出模型；根据实际表现决定是否进入扩展分支或下一模块。
- 完成题归入前面的三列表，更新当前层与下一题。Greedy、背包及其他 DP 进阶继续按原决定暂缓。

本模块应逐步达到：能从区间条件推导前缀关系；能按最长、最短、计数或存在性选择历史信息；能解释空前缀、负数、取模与数值范围；能区分查询和批量更新；能处理一维与二维边界；能在树或矩阵搜索中维持前缀信息的正确含义。完成数量与独立掌握程度分别记录。
