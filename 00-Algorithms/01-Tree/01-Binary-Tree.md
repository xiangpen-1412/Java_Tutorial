# Binary Tree

## 1. What a Binary Tree Is

A **binary tree** is a tree in which every node has at most two children:

- a `left` child

- a `right` child

The two positions are distinct. A node with only a left child is structurally different from a node with only a right child.

```text
Only left child       Only right child

      A                       A
     /                         \
    B                           B
```

Both are valid binary trees.

The word **binary** describes the maximum number of child positions. It does not imply:

- every node has exactly two children

- the values are sorted

- the tree is balanced

- the tree is complete

These are separate properties.

---

## 2. Binary Tree Is a Recursive Structure

For any node `root`:

- `root.left` is the root of another binary tree

- `root.right` is the root of another binary tree

- `null` represents an empty binary tree

So a binary tree can be defined recursively:

```text
binary tree = root + left subtree + right subtree
```

This is why recursion fits binary trees naturally. Most recursive solutions follow the same shape:

```java
Result solve(TreeNode root) {
    if (root == null) {
        return baseResult;
    }

    Result leftResult = solve(root.left);
    Result rightResult = solve(root.right);

    return combine(root, leftResult, rightResult);
}
```

The important questions are:

1. What does `solve(root)` mean for the subtree rooted at `root`?

2. What should an empty subtree return?

3. What information must the parent receive from its two children?

4. How should the parent combine that information?

If the meaning of the return value is clear, the code is usually short and reliable.

---

## 3. Basic Java Representation

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    public TreeNode(int val) {
        this.val = val;
    }
}
```

Example tree:

```text
        1
       / \
      2   3
     / \
    4   5
```

Construction:

```java
TreeNode root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
root.left.left = new TreeNode(4);
root.left.right = new TreeNode(5);
```

The variable `root` stores a reference to the root node. The complete tree is reachable by following `left` and `right` references.

---

## 4. Important Binary Tree Shapes

These terms describe different structural properties. They are related, but they are not interchangeable.

### 4.1 Full Binary Tree

A **full binary tree** is a binary tree in which every node has either:

- `0` children, or

- exactly `2` children

No node has only one child.

```text
        1
       / \
      2   3
         / \
        4   5
```

This tree is full even though its leaves are not all on the same level.

### 4.2 Perfect Binary Tree

A **perfect binary tree** satisfies both conditions:

- every internal node has exactly two children

- all leaves are on the same level

```text
          1
        /   \
       2     3
      / \   / \
     4   5 6   7
```

If the root is at level `0` and the tree height is measured in edges as `h`:

```text
number of nodes  = 2^(h + 1) - 1
number of leaves = 2^h
```

### 4.3 Complete Binary Tree

A **complete binary tree** satisfies:

- every level except possibly the last is completely filled

- nodes on the last level are filled from left to right with no gaps

Valid:

```text
        1
       / \
      2   3
     / \  /
    4  5 6
```

Not complete:

```text
        1
       / \
      2   3
       \  /
       5 6
```

There is a gap at `2.left` before later nodes appear.

Complete binary trees are important because they can be stored compactly in arrays. This is the structural foundation of a binary heap.

For zero-based indexing:

```text
left child  = 2 * i + 1
right child = 2 * i + 2
parent      = (i - 1) / 2
```

### 4.4 Balanced Binary Tree

In common algorithm problems, a binary tree is **height-balanced** if, for every node:

```text
| height(left subtree) - height(right subtree) | <= 1
```

The condition must hold at **every node**, not only at the root.

Balanced does not mean perfect or complete. It only controls height differences.

### 4.5 Skewed Binary Tree

A **skewed tree** is dominated by one direction:

```text
1
 \
  2
   \
    3
     \
      4
```

Its height is close to the number of nodes, so many tree operations behave like linked-list operations.

### 4.6 Relationship Between the Shapes

```text
perfect tree  -> full tree
perfect tree  -> complete tree
```

But the reverse implications are not generally true:

- a full tree may not be perfect

- a complete tree may contain a node with only a left child, so it may not be full

- a balanced tree does not have to be complete or perfect

---

## 5. Traversal: The Core Operation

Traversal means visiting every node according to a defined order.

The two major families are:

- **Depth-First Search (DFS)**: go deep into one subtree before switching sides

- **Breadth-First Search (BFS)**: visit the tree level by level

Use this tree for all traversal examples:

```text
        1
       / \
      2   3
     / \   \
    4   5   6
```

---

## 6. Depth-First Traversal

The three classic DFS orders are named according to when the root is processed relative to its subtrees.

```text
preorder:  root -> left -> right
inorder:   left -> root -> right
postorder: left -> right -> root
```

The words `pre`, `in`, and `post` describe the position of the **root**.

### 6.1 Preorder Traversal

Order:

```text
root -> left subtree -> right subtree
```

Result for the example tree:

```text
1, 2, 4, 5, 3, 6
```

Recursive implementation:

```java
public void preorder(TreeNode root) {
    if (root == null) {
        return;
    }

    System.out.println(root.val);
    preorder(root.left);
    preorder(root.right);
}
```

Preorder is useful when the current node must be handled before its children, such as:

- copying a tree top-down

- serialization with null markers

- passing information from parent to child

### 6.2 Inorder Traversal

Order:

```text
left subtree -> root -> right subtree
```

Result:

```text
4, 2, 5, 1, 3, 6
```

Recursive implementation:

```java
public void inorder(TreeNode root) {
    if (root == null) {
        return;
    }

    inorder(root.left);
    System.out.println(root.val);
    inorder(root.right);
}
```

For an ordinary binary tree, inorder does not imply sorted output. It becomes sorted only when the tree also satisfies the BST ordering property.

### 6.3 Postorder Traversal

Order:

```text
left subtree -> right subtree -> root
```

Result:

```text
4, 5, 2, 6, 3, 1
```

Recursive implementation:

```java
public void postorder(TreeNode root) {
    if (root == null) {
        return;
    }

    postorder(root.left);
    postorder(root.right);
    System.out.println(root.val);
}
```

Postorder is useful when the parent depends on results calculated by its children, such as:

- computing height

- checking whether a tree is balanced

- calculating subtree sizes

- deleting or releasing a tree bottom-up

### 6.4 The Code Difference Is the Processing Position

The recursive structure is the same in all three traversals:

```java
traverse(root.left);
traverse(root.right);
```

Only the position of the node-processing statement changes:

```java
// preorder position
process(root);

traverse(root.left);

// inorder position
process(root);

traverse(root.right);

// postorder position
process(root);
```

This is more useful than memorizing three unrelated functions.

---

## 7. Iterative DFS and the Role of the Stack

Recursive calls are stored on the call stack. An iterative traversal makes that stack explicit.

### 7.1 Iterative Preorder

```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();

    if (root == null) {
        return result;
    }

    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);

    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        result.add(node.val);

        if (node.right != null) {
            stack.push(node.right);
        }
        if (node.left != null) {
            stack.push(node.left);
        }
    }

    return result;
}
```

The right child is pushed first because a stack is LIFO. This makes the left child come out first.

### 7.2 Iterative Inorder

```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode current = root;

    while (current != null || !stack.isEmpty()) {
        while (current != null) {
            stack.push(current);
            current = current.left;
        }

        current = stack.pop();
        result.add(current.val);
        current = current.right;
    }

    return result;
}
```

The stack stores nodes whose left subtree is being processed but whose own value and right subtree still remain.

That invariant explains the algorithm better than memorizing the loop.

### 7.3 Iterative Postorder

One clear implementation stores whether a node has already been expanded:

```java
private static class Frame {
    TreeNode node;
    boolean expanded;

    Frame(TreeNode node, boolean expanded) {
        this.node = node;
        this.expanded = expanded;
    }
}
```

```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();

    if (root == null) {
        return result;
    }

    Deque<Frame> stack = new ArrayDeque<>();
    stack.push(new Frame(root, false));

    while (!stack.isEmpty()) {
        Frame frame = stack.pop();

        if (frame.expanded) {
            result.add(frame.node.val);
            continue;
        }

        stack.push(new Frame(frame.node, true));

        if (frame.node.right != null) {
            stack.push(new Frame(frame.node.right, false));
        }
        if (frame.node.left != null) {
            stack.push(new Frame(frame.node.left, false));
        }
    }

    return result;
}
```

The first time a node is seen, its children are scheduled. The second time, both subtrees have been handled, so the node can be processed.

---

## 8. Breadth-First Traversal

BFS visits nodes level by level and uses a queue.

For the example tree:

```text
level 0: 1
level 1: 2, 3
level 2: 4, 5, 6
```

Flattened traversal:

```text
1, 2, 3, 4, 5, 6
```

### 8.1 Basic Level-Order Traversal

```java
public List<Integer> levelOrderFlat(TreeNode root) {
    List<Integer> result = new ArrayList<>();

    if (root == null) {
        return result;
    }

    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        result.add(node.val);

        if (node.left != null) {
            queue.offer(node.left);
        }
        if (node.right != null) {
            queue.offer(node.right);
        }
    }

    return result;
}
```

### 8.2 Separating the Levels

The key technique is to record the queue size before processing a level:

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();

    if (root == null) {
        return result;
    }

    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        int levelSize = queue.size();
        List<Integer> level = new ArrayList<>();

        for (int i = 0; i < levelSize; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);

            if (node.left != null) {
                queue.offer(node.left);
            }
            if (node.right != null) {
                queue.offer(node.right);
            }
        }

        result.add(level);
    }

    return result;
}
```

`levelSize` must be captured before the loop because new child nodes are added while the current level is being processed.

BFS is the natural choice for:

- level-order output

- minimum depth

- right-side or left-side view

- nearest target in an unweighted tree or graph

---

## 9. Two Major Recursive Thinking Models

Many binary tree problems become easier after deciding which of these models fits.

### 9.1 Return Information Upward: Bottom-Up

Each subtree returns a result to its parent.

Example: tree height.

Define:

```text
height(root) = number of nodes on the longest path
               from root down to a leaf
```

Then:

```text
height(null) = 0
height(root) = 1 + max(height(root.left), height(root.right))
```

Code:

```java
public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }

    int leftDepth = maxDepth(root.left);
    int rightDepth = maxDepth(root.right);

    return 1 + Math.max(leftDepth, rightDepth);
}
```

The node is computed after both child results are known, so this is postorder reasoning.

### 9.2 Carry State Downward: Top-Down

The parent passes accumulated information to its children.

Example: determine whether a root-to-leaf path has a target sum.

```java
public boolean hasPathSum(TreeNode root, int targetSum) {
    if (root == null) {
        return false;
    }

    if (root.left == null && root.right == null) {
        return targetSum == root.val;
    }

    int remaining = targetSum - root.val;

    return hasPathSum(root.left, remaining)
        || hasPathSum(root.right, remaining);
}
```

At every step, `remaining` means:

> the sum still required from the current subtree down to a leaf

This invariant keeps the recursion understandable.

### 9.3 Some Problems Need Both

A problem may pass context downward while also returning a computed result upward. Do not force every problem into only one style; first define what each parameter and return value means.

---

## 10. Reusable Binary Tree Patterns

### 10.1 Count Nodes

```text
count(null) = 0
count(root) = 1 + count(left) + count(right)
```

```java
public int countNodes(TreeNode root) {
    if (root == null) {
        return 0;
    }

    return 1 + countNodes(root.left) + countNodes(root.right);
}
```

### 10.2 Count Leaves

```java
public int countLeaves(TreeNode root) {
    if (root == null) {
        return 0;
    }

    if (root.left == null && root.right == null) {
        return 1;
    }

    return countLeaves(root.left) + countLeaves(root.right);
}
```

The leaf test requires **both** children to be `null`.

### 10.3 Invert a Binary Tree

```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) {
        return null;
    }

    TreeNode left = invertTree(root.left);
    TreeNode right = invertTree(root.right);

    root.left = right;
    root.right = left;

    return root;
}
```

The return contract is:

> return the root of the inverted subtree

Returning the subtree root is a highly reusable pattern because a recursive structural change may replace the root of that subtree.

### 10.4 Check Whether Two Trees Are the Same

```java
public boolean isSameTree(TreeNode a, TreeNode b) {
    if (a == null && b == null) {
        return true;
    }

    if (a == null || b == null) {
        return false;
    }

    return a.val == b.val
        && isSameTree(a.left, b.left)
        && isSameTree(a.right, b.right);
}
```

Two trees are the same only if both are true:

- corresponding node values are equal

- the structure is equal

Equal traversal values alone do not always prove equal structure unless null positions are also represented.

### 10.5 Check Height Balance in One Pass

A slow approach calculates height repeatedly at every node, which may take `O(n^2)` on a skewed tree.

A better contract is:

```text
return subtree height if balanced
return -1 if unbalanced
```

```java
public boolean isBalanced(TreeNode root) {
    return heightOrUnbalanced(root) != -1;
}

private int heightOrUnbalanced(TreeNode root) {
    if (root == null) {
        return 0;
    }

    int leftHeight = heightOrUnbalanced(root.left);
    if (leftHeight == -1) {
        return -1;
    }

    int rightHeight = heightOrUnbalanced(root.right);
    if (rightHeight == -1) {
        return -1;
    }

    if (Math.abs(leftHeight - rightHeight) > 1) {
        return -1;
    }

    return 1 + Math.max(leftHeight, rightHeight);
}
```

This combines two pieces of information into one return value and stops early when an unbalanced subtree is found.

---

## 11. Height and Depth Conventions

Two conventions are common.

### Height Measured in Edges

```text
empty tree height = -1
leaf height       = 0
```

### Height Measured in Nodes

```text
empty tree height = 0
leaf height       = 1
```

Both are valid. The important requirement is consistency.

Most coding problems that ask for “maximum depth” count nodes, so a single-node tree has depth `1`.

Do not memorize a formula without checking its base case. The base case determines the convention used by the rest of the recursion.

---

## 12. Reconstructing a Tree from Traversals

Traversal output does not always uniquely determine a binary tree.

### Preorder + Inorder

If node values are unique, these two traversals uniquely determine the tree:

- preorder identifies the root first

- inorder divides the remaining nodes into left and right subtrees

Example:

```text
preorder: 3, 9, 20, 15, 7
inorder:  9, 3, 15, 20, 7
```

Reasoning:

1. `3` is first in preorder, so it is the root.

2. In inorder, values left of `3` belong to the left subtree: `[9]`.

3. Values right of `3` belong to the right subtree: `[15, 20, 7]`.

4. Repeat the same reasoning recursively for both ranges.

### Postorder + Inorder

This pair also uniquely determines the tree when values are unique:

- postorder identifies the root last

- inorder splits left and right subtrees

### Preorder + Postorder

These traversals do **not** uniquely determine a general binary tree.

For example, both trees have preorder `[1, 2]` and postorder `[2, 1]`:

```text
    1          1
   /            \
  2              2
```

The traversals cannot tell whether `2` is a left child or a right child.

---

## 13. Complexity of Binary Tree Traversal

If every node is visited once:

```text
time complexity = O(n)
```

where `n` is the number of nodes.

### DFS Auxiliary Space

Recursive DFS uses the call stack:

```text
space = O(h)
```

where `h` is the tree height.

- balanced tree: `O(log n)`

- skewed tree: `O(n)`

### BFS Auxiliary Space

BFS stores nodes from a level in the queue:

```text
space = O(w)
```

where `w` is the maximum width of the tree.

For a wide complete tree, this can be `O(n)`.

The recursive call stack is real memory usage even when it is not written as an explicit data structure.

---

## 14. How to Choose DFS or BFS

Choose based on the information the problem asks for.

### Prefer DFS When

- the answer depends on complete subtrees

- child results must be combined at the parent

- the problem asks about height, diameter, balance, subtree sums, or paths

- preorder, inorder, or postorder naturally matches the required processing order

### Prefer BFS When

- the problem explicitly asks about levels

- the nearest or shallowest valid node is needed

- the problem asks for minimum depth

- nodes must be grouped or viewed by level

This is a recognition rule, not an absolute restriction. Many problems can be solved both ways, but one traversal usually makes the invariant clearer.

---

## 15. Common Mistakes

### Mistake 1: Treating Every Binary Tree as a BST

An ordinary binary tree has no value-ordering guarantee.

You cannot decide to search only left or only right by comparing values unless the tree is explicitly a BST.

### Mistake 2: Confusing Full, Complete, Perfect, and Balanced

- full: every node has `0` or `2` children

- complete: filled level by level, last level from left to right

- perfect: all internal nodes have two children and all leaves share a level

- balanced: subtree heights remain sufficiently close

Always check the actual definition instead of judging by whether the tree “looks symmetrical.”

### Mistake 3: Forgetting the Empty-Tree Base Case

Without:

```java
if (root == null) {
    ...
}
```

recursion eventually dereferences `null`.

The returned base value must match the meaning of the function. It might be `0`, `true`, `false`, `null`, or another sentinel value.

### Mistake 4: Writing Recursion Before Defining Its Meaning

Before coding, complete this sentence:

```text
solve(root) returns / does __________________
for the subtree rooted at root.
```

If that sentence is vague, the recursive combination will usually be vague too.

### Mistake 5: Mixing Node Depth with Tree Height

- depth is measured from the root down to a node

- height is measured from a node down to its deepest leaf

They may have the same numeric value in one example but represent different directions and meanings.

### Mistake 6: Detecting a Leaf with Only One Null Check

Correct leaf condition:

```java
root.left == null && root.right == null
```

A node with one missing child is not necessarily a leaf.

### Mistake 7: Losing a Changed Subtree Root

If a recursive operation returns the new subtree root, reconnect it:

```java
root.left = transform(root.left);
root.right = transform(root.right);
```

Ignoring the return value may lose structural changes performed below the current node.

### Mistake 8: Assuming Traversal Values Preserve Structure

Different trees can produce the same traversal sequence. To serialize structure unambiguously, include null markers or use enough traversal information under valid assumptions.

### Mistake 9: Using `Stack` by Default in Java

For stack behavior, prefer:

```java
Deque<TreeNode> stack = new ArrayDeque<>();
```

Use:

```java
stack.push(node);
stack.pop();
stack.peek();
```

For queue behavior:

```java
Queue<TreeNode> queue = new ArrayDeque<>();
```

Use:

```java
queue.offer(node);
queue.poll();
queue.peek();
```

---

## 16. Binary Tree vs Binary Search Tree

### Binary Tree

Only guarantees:

```text
each node has at most two children
```

Searching for an arbitrary value may require visiting every node:

```text
O(n)
```

### Binary Search Tree

Adds an ordering rule:

```text
left subtree values < root value < right subtree values
```

This ordering can guide search, insertion, and deletion along one path. Their complexity depends on the tree height.

So every BST is a binary tree, but not every binary tree is a BST.

The natural next topic is [[02-Binary-Search-Tree]].

---

## 17. Review Checklist

Before moving on, make sure you can explain all of these without looking at code:

- Why is a binary tree naturally recursive?

- What is the difference between full, complete, perfect, and balanced?

- Why do recursive traversals use the call stack?

- Why does iterative preorder push the right child first?

- What invariant does the stack maintain during iterative inorder traversal?

- Why does BFS capture `queue.size()` before processing one level?

- When should information flow downward, and when should subtree results return upward?

- Why is the time complexity of a full traversal `O(n)` but DFS space `O(h)`?

- Why does inorder traversal produce sorted values only for a BST?

- Why are preorder and postorder alone insufficient to reconstruct a general binary tree?
