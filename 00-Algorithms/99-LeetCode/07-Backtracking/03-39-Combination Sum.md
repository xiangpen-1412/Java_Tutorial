# 03. Combination Sum

## 题目

给一个不含重复数字的数组 `candidates` 和一个目标值 `target`。

题目要你找出所有组合，使组合里的数字和等于 `target`。

同一个数字可以重复使用，但组合顺序不重要。
## 题号

```text
LeetCode 39. Combination Sum
```

## 1. 这题和 Backtracking 的关系

这题是 backtracking 里的组合问题。

题目要找：

```text
所有和为 target 的组合。
```

比如：

```text
candidates = [2, 3, 6, 7]
target = 7
```

答案是：

```text
[2,2,3]
[7]
```

它的核心是：

```text
每一步从 candidates 里选一个数。
选完以后，target 减去这个数。
如果 target 变成 0，说明找到一个答案。
如果 target 小于 0，说明这条路走不通。
```

## 2. 这题不是 Permutations

这题是 combination，不是 permutation。

所以：

```text
[2,2,3]
[2,3,2]
[3,2,2]
```

它们在这题里算同一个组合。

因此不能每一层都从 `0` 开始选，否则会产生重复顺序。

这题需要 `start`。

```text
start = 当前这一层从哪个 index 开始选
```

## 3. 为什么递归传 i，不是 i + 1？

这是这题最关键的地方。

题目说：

```text
同一个数字可以被选择无限次。
```

所以如果当前选择了 `candidates[i]`，下一层仍然可以继续选择它。

因此递归时传：

```java
backTrack(candidates, target - candidates[i], i);
```

而不是：

```java
backTrack(candidates, target - candidates[i], i + 1);
```

区别是：

```text
传 i:
当前数字下一层还可以继续用。

传 i + 1:
当前数字下一层不能再用了。
```

Combination Sum 要允许重复使用当前数字，所以传 `i`。

## 4. 为什么 for loop 从 start 开始？

虽然同一个数字可以重复用，但不能回头选前面的数字。

比如已经有：

```text
path = [2, 3]
```

就不应该再回头选前面的 `2` 生成：

```text
[2,3,2]
```

因为它和：

```text
[2,2,3]
```

是同一个组合。

所以：

```java
for (int i = start; i < candidates.length; i++)
```

代表：

```text
这一层只能从 start 往后选。
```

这样可以避免重复组合。

## 5. Pseudo Code

```text
result = []
path = []

function backTrack(target, start):
    if target == 0:
        result.add(copy(path))
        return

    for i from start to candidates.length - 1:
        if candidates[i] <= target:
            path.add(candidates[i])
            backTrack(target - candidates[i], i)
            path.removeLast()
```

## 6. 你的代码逻辑

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> path = new ArrayList<>();

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        backTrack(candidates, target, 0);
        return res;
    }

    public void backTrack(int[] candidates, int target, int start) {
        if (target == 0) {
            res.add(new ArrayList<Integer>(path));
            return;
        }

        for (int i = start; i < candidates.length; i++) {
            if (candidates[i] <= target) {
                path.add(candidates[i]);
                backTrack(candidates, target - candidates[i], i);
                path.remove(path.size() - 1);
            }
        }
    }
}
```

## 7. 用 [2,3,6,7], target = 7 理解

一开始：

```text
path = []
target = 7
start = 0
```

选择 `2`：

```text
path = [2]
target = 5
start = 0
```

递归传的是 `i = 0`，所以下一层还可以继续选 `2`。

继续选 `2`：

```text
path = [2,2]
target = 3
start = 0
```

继续从 index 0 开始尝试：

```text
2 <= 3，可以试
3 <= 3，也可以试
```

如果选 `3`：

```text
path = [2,2,3]
target = 0
```

说明找到答案：

```text
[2,2,3]
```

然后 return，撤销刚才的 `3`，继续试别的选择。

## 8. 这题最重要的变量

```text
res = 所有答案
path = 当前正在构造的组合
target = 当前还差多少
start = 当前这一层从哪里开始选
```

注意这里的 `target` 可以理解成：

```text
remaining target
```

也就是：

```text
还剩多少需要凑出来。
```

## 9. 和 Subsets / Permutations 的区别

| 题目 | 顺序重要吗 | 可以重复用数字吗 | 递归传什么 |
|---|---|---|---|
| Subsets | 不重要 | 不可以 | `i + 1` |
| Permutations | 重要 | 不可以 | 不用 start，用 used |
| Combination Sum | 不重要 | 可以 | `i` |

### Subsets

```text
选了当前数字以后，不能再选它。
所以传 i + 1。
```

### Permutations

```text
每个位置都可以从所有数字里选。
但已经 used 的不能再选。
```

### Combination Sum

```text
选了当前数字以后，还可以继续选它。
但不能回头选前面的数字。
所以传 i。
```

## 10. 记忆方式

```text
Combination Sum:
组合，顺序不重要，所以用 start。
数字可重复，所以递归传 i。
target 减到 0，就是答案。
```

