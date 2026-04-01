# BFS and DFS in Trees

## 1. Why BFS and DFS Matter

BFS and DFS are two of the most important traversal strategies in algorithms.

In tree problems, they are not just traversal techniques. They are the foundation for:

- tree traversal
    
- recursive reasoning
    
- path problems
    
- level-based problems
    
- binary search tree problems
    
- many graph algorithms later
    

If you understand BFS and DFS deeply in trees, it becomes much easier to learn graphs, backtracking, shortest path, topological sort, and many interview problems.

---

## 2. Core Idea: What BFS and DFS Really Mean

### 2.1 BFS

**BFS = Breadth-First Search**

Core idea:

> Explore nodes level by level, from near to far.

In a tree, this means:

- visit the root first
    
- then visit all nodes on level 2
    
- then visit all nodes on level 3
    
- continue level by level
    

So in trees, BFS is most commonly associated with:

- **level order traversal**
    

BFS is usually implemented with a **queue**.

---

### 2.2 DFS

**DFS = Depth-First Search**

Core idea:

> Follow one branch as deep as possible, then backtrack.

In a tree, this means:

- start from the root
    
- keep going down one subtree
    
- when you cannot go deeper, return
    
- then explore another branch
    

DFS is usually implemented with:

- **recursion**
    
- or an explicit **stack**
    

In trees, DFS most commonly appears as:

- preorder traversal
    
- inorder traversal
    
- postorder traversal
    

---

### 2.3 One-Sentence Difference

- **BFS**: expand by levels
    
- **DFS**: go deep along a branch first
    

---

## 3. Connecting BFS and DFS to Trees

Consider this binary tree:

```text
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

### BFS order

```text
1, 2, 3, 4, 5, 6, 7
```

### DFS orders

#### Preorder

```text
1, 2, 4, 5, 3, 6, 7
```

#### Inorder

```text
4, 2, 5, 1, 6, 3, 7
```

#### Postorder

```text
4, 5, 2, 6, 7, 3, 1
```

So in tree problems:

- **BFS** usually means level order traversal
    
- **DFS** usually means preorder / inorder / postorder traversal
    

---

## 4. TreeNode Definition in Java

A standard binary tree node in Java usually looks like this:

```java
public class TreeNode {
    public int val;
    public TreeNode left;
    public TreeNode right;

    public TreeNode(int val) {
        this.val = val;
    }
}
```

This is the base structure for most tree algorithm problems.

---

## 5. BFS in Trees

## 5.1 Why BFS Uses a Queue

BFS must process nodes in the same order they are discovered:

- first discovered -> first processed
    

That is exactly **FIFO** behavior.

So BFS uses a queue.

In Java, a good implementation is:

```java
Queue<TreeNode> queue = new ArrayDeque<>();
```

`ArrayDeque` is generally preferred over `LinkedList` for queue usage in algorithm problems.

---

## 5.2 Basic BFS Template

```java
import java.util.*;

public class BinaryTreeBFS {

    public List<Integer> levelOrderFlat(TreeNode root) {
        List<Integer> result = new ArrayList<>();

        if (root == null) {
            return result;
        }

        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            TreeNode current = queue.poll();
            result.add(current.val);

            if (current.left != null) {
                queue.offer(current.left);
            }

            if (current.right != null) {
                queue.offer(current.right);
            }
        }

        return result;
    }
}
```

---

## 5.3 Step-by-Step Queue Evolution

For this tree:

```text
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

### Initial state

```text
queue = [1]
result = []
```

### Process 1

- poll 1
    
- add 1 to result
    
- offer 2 and 3
    

```text
queue = [2, 3]
result = [1]
```

### Process 2

- poll 2
    
- add 2 to result
    
- offer 4 and 5
    

```text
queue = [3, 4, 5]
result = [1, 2]
```

### Process 3

- poll 3
    
- add 3 to result
    
- offer 6 and 7
    

```text
queue = [4, 5, 6, 7]
result = [1, 2, 3]
```

And so on until the queue is empty.

---

## 5.4 BFS by Level

Many tree problems require grouping nodes by level:

```text
[
  [1],
  [2, 3],
  [4, 5, 6, 7]
]
```

To do that, record the current queue size before processing a level.

That size represents exactly the number of nodes on the current level.

```java
import java.util.*;

public class BinaryTreeLevelOrder {

    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();

        if (root == null) {
            return result;
        }

        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> currentLevel = new ArrayList<>();

            for (int i = 0; i < levelSize; i++) {
                TreeNode current = queue.poll();
                currentLevel.add(current.val);

                if (current.left != null) {
                    queue.offer(current.left);
                }

                if (current.right != null) {
                    queue.offer(current.right);
                }
            }

            result.add(currentLevel);
        }

        return result;
    }
}
```

---

## 5.5 Typical BFS Use Cases in Trees

BFS is especially suitable for:

- level order traversal
    
- zigzag level order traversal
    
- right side view
    
- average of levels
    
- largest value in each row
    
- minimum depth
    

### Why minimum depth is a classic BFS problem

Because BFS explores shallow levels first.

The first leaf node you encounter must be the shallowest leaf.

That makes BFS very natural for minimum depth.

---

## 6. DFS in Trees

## 6.1 Why DFS Matches Trees So Well

A tree is naturally recursive:

- one node
    
- a left subtree
    
- a right subtree
    

Each subtree is itself a tree.

That makes recursion a very natural way to solve tree problems.

DFS in trees is often written recursively.

---

## 6.2 The Three Major DFS Orders

The difference between preorder, inorder, and postorder is simply:

> Where you place the processing of the current node.

Suppose the current node is `node`.

### Preorder

```java
action(node);
dfs(node.left);
dfs(node.right);
```

### Inorder

```java
dfs(node.left);
action(node);
dfs(node.right);
```

### Postorder

```java
dfs(node.left);
dfs(node.right);
action(node);
```

So the real difference is not three unrelated algorithms.  
It is one DFS pattern with the current-node action placed in different positions.

---

## 7. Preorder Traversal

## 7.1 Rule

**Root -> Left -> Right**

---

## 7.2 Code

```java
import java.util.*;

public class BinaryTreePreorder {

    public List<Integer> preorder(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        dfs(root, result);
        return result;
    }

    private void dfs(TreeNode node, List<Integer> result) {
        if (node == null) {
            return;
        }

        result.add(node.val);
        dfs(node.left, result);
        dfs(node.right, result);
    }
}
```

---

## 7.3 When Preorder Is Useful

Preorder is useful when you need to:

- process the current node before its children
    
- record root-to-node path information
    
- copy a tree
    
- serialize a tree
    
- build path strings or path sums during traversal
    

You can think of preorder as:

> Visit parent first, then explore children.

---

## 8. Inorder Traversal

## 8.1 Rule

**Left -> Root -> Right**

---

## 8.2 Code

```java
import java.util.*;

public class BinaryTreeInorder {

    public List<Integer> inorder(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        dfs(root, result);
        return result;
    }

    private void dfs(TreeNode node, List<Integer> result) {
        if (node == null) {
            return;
        }

        dfs(node.left, result);
        result.add(node.val);
        dfs(node.right, result);
    }
}
```

---

## 8.3 Why Inorder Is Extremely Important for BST

For a **Binary Search Tree (BST)**:

- all values in the left subtree are smaller than the root
    
- all values in the right subtree are larger than the root
    

Therefore:

> Inorder traversal of a BST produces values in sorted order.

This is one of the most important properties in tree algorithms.

It is the basis for many BST problems, including:

- validate BST
    
- kth smallest element in BST
    
- BST iterator
    
- converting BST to sorted output
    

---

## 9. Postorder Traversal

## 9.1 Rule

**Left -> Right -> Root**

---

## 9.2 Code

```java
import java.util.*;

public class BinaryTreePostorder {

    public List<Integer> postorder(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        dfs(root, result);
        return result;
    }

    private void dfs(TreeNode node, List<Integer> result) {
        if (node == null) {
            return;
        }

        dfs(node.left, result);
        dfs(node.right, result);
        result.add(node.val);
    }
}
```

---

## 9.3 When Postorder Is Useful

Postorder is especially useful when:

- you must solve child subproblems first
    
- the answer for the current node depends on left and right subtree results
    
- you need bottom-up computation
    

Classic examples:

- maximum depth
    
- subtree sum
    
- balanced binary tree
    
- diameter of binary tree
    
- deleting or freeing a tree
    

You can think of postorder as:

> Children first, parent later.

---

## 10. DFS as Recursive Problem Solving

This is one of the most important ideas in tree algorithms.

Many tree problems can be solved by asking:

- what is the answer for the left subtree?
    
- what is the answer for the right subtree?
    
- how do I combine them for the current node?
    

That is why postorder DFS is so powerful.

---

## 10.1 Example: Maximum Depth

```java
public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }

    int leftDepth = maxDepth(root.left);
    int rightDepth = maxDepth(root.right);

    return Math.max(leftDepth, rightDepth) + 1;
}
```

Why is this postorder thinking?

Because you must first know:

- left subtree depth
    
- right subtree depth
    

Only then can you compute the depth of the current node.

---

## 10.2 Example: Sum of All Nodes

```java
public int sum(TreeNode root) {
    if (root == null) {
        return 0;
    }

    return sum(root.left) + sum(root.right) + root.val;
}
```

Again, this is bottom-up reasoning.

---

## 10.3 Example: Count Leaf Nodes

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

This is also DFS with recursive subtree reasoning.

---

## 11. BFS vs DFS: How to Choose

This is a very important interview skill.

## 11.1 Prefer BFS When

The problem is about:

- levels
    
- nearest / shallowest / minimum number of steps
    
- expanding outward layer by layer
    
- per-level statistics
    

Examples:

- level order traversal
    
- zigzag level order traversal
    
- minimum depth
    
- average of levels
    
- right side view
    
- largest value in each row
    

---

## 11.2 Prefer DFS When

The problem is about:

- recursive subtree structure
    
- path exploration
    
- combining left and right subtree answers
    
- depth-based reasoning
    
- BST inorder property
    

Examples:

- maximum depth
    
- subtree sum
    
- count leaves
    
- invert tree
    
- same tree
    
- validate BST
    
- path sum
    
- balanced binary tree
    
- diameter of binary tree
    

---

## 12. Time and Space Complexity

Assume the tree has `n` nodes.

## 12.1 Time Complexity

Both BFS and DFS usually visit each node once.

So time complexity is:

```text
O(n)
```

---

## 12.2 BFS Space Complexity

BFS stores nodes in a queue.

In the worst case, one level may contain many nodes.

So BFS space complexity is:

```text
O(n)
```

More precisely, it depends on the maximum width of the tree.

---

## 12.3 DFS Space Complexity

Recursive DFS uses the call stack.

The space depends on tree height `h`:

```text
O(h)
```

- balanced tree: `O(log n)`
    
- skewed tree: `O(n)`
    

So DFS space complexity is often written as `O(h)`, with worst case `O(n)`.

---

## 13. Iterative DFS with Stack

Recursion is not the only way to write DFS.

You can also use an explicit stack.

That means:

- recursive DFS = system call stack
    
- iterative DFS = your own stack
    

### Iterative preorder example

```java
import java.util.*;

public class BinaryTreePreorderIterative {

    public List<Integer> preorder(TreeNode root) {
        List<Integer> result = new ArrayList<>();

        if (root == null) {
            return result;
        }

        Deque<TreeNode> stack = new ArrayDeque<>();
        stack.push(root);

        while (!stack.isEmpty()) {
            TreeNode current = stack.pop();
            result.add(current.val);

            if (current.right != null) {
                stack.push(current.right);
            }

            if (current.left != null) {
                stack.push(current.left);
            }
        }

        return result;
    }
}
```

Why push right before left?

Because stack is **LIFO**.  
If you want left to be processed first, right must be pushed first.

---

## 14. Common Mistakes

## 14.1 Treating BFS and DFS as Pure Templates

Do not only memorize code.

You must connect them to problem structure:

- BFS = layer expansion
    
- DFS = deep exploration / recursive decomposition
    

---

## 14.2 Weak Base Case in Recursion

In tree recursion, the most common source of bugs is poor base case handling.

Always think clearly about:

- what should happen when the node is `null`
    
- what should happen for a leaf node
    
- what exactly the method should return
    

---

## 14.3 Mixing Up Preorder / Inorder / Postorder

The safest way to remember them is not by name alone.

Remember the location of the current-node action:

- preorder: before children
    
- inorder: between left and right
    
- postorder: after children
    

---

## 14.4 Wrong Level Handling in BFS

When processing level by level, save `queue.size()` before the loop for that level.

Do not keep checking queue size dynamically inside the same level loop, or nodes from the next level may be mixed in.

---

## 14.5 Forgetting That Tree Is Not General Graph

In a tree:

- there is no cycle
    
- nodes are already structured by parent/child references
    
- you usually do not need a `visited` set
    

In graphs, BFS and DFS are often more complex because cycles and repeated visits are possible.

---

## 15. Important Tree Topics Closely Related to BFS and DFS

If you are learning tree algorithms in Java, you should study these topics together with BFS and DFS.

## 15.1 Tree Traversal

You must be able to write:

- preorder traversal
    
- inorder traversal
    
- postorder traversal
    
- level order traversal
    

without looking at notes.

---

## 15.2 Height and Depth

These terms are easy to confuse.

### Depth

How far a node is from the root.

### Height

How far a node is from its deepest descendant leaf.

In many interview problems, `maxDepth(root)` is effectively computing the height of the tree rooted at `root`.

Do not get trapped by terminology alone; focus on the actual definition used by the problem.

---

## 15.3 Path Problems

Examples:

- path sum
    
- root-to-leaf paths
    
- collect all root-to-leaf paths
    
- maximum path sum
    

These often use DFS, especially preorder-style traversal when maintaining path state during descent.

---

## 15.4 Balanced Tree Thinking

A balanced binary tree problem often requires:

- computing left subtree height
    
- computing right subtree height
    
- checking whether their difference is within a limit
    

This is classic postorder reasoning.

---

## 15.5 BST Reasoning

BST adds ordering structure to a binary tree.

Important facts:

- left subtree < root < right subtree
    
- inorder traversal is sorted
    

This connects tree traversal directly to search and ordered data reasoning.

---

## 16. Recommended Practice Order

If you are currently building tree fundamentals, this is a strong learning order:

1. preorder traversal
    
2. inorder traversal
    
3. postorder traversal
    
4. level order traversal
    
5. maximum depth
    
6. minimum depth
    
7. sum of nodes
    
8. count leaves
    
9. invert binary tree
    
10. same tree
    
11. path sum
    
12. balanced binary tree
    
13. diameter of binary tree
    
14. search in BST
    
15. validate BST
    
16. kth smallest element in BST
    

This order builds from traversal to recursive reasoning, then to structural analysis and BST problems.

---

## 17. Summary Table

|Topic|BFS|DFS|
|---|---|---|
|Main idea|explore by levels|go deep first|
|Typical structure|queue|recursion / stack|
|Tree usage|level order traversal|preorder / inorder / postorder|
|Best for|layer-based problems, minimum depth|subtree recursion, path problems, BST logic|
|Time complexity|O(n)|O(n)|
|Space complexity|O(n) worst case|O(h), worst case O(n)|

---

## 18. Final Takeaway

BFS and DFS are not isolated techniques.

They are two fundamental ways to think about tree problems.

### BFS mindset

- process by levels
    
- expand outward
    
- use a queue
    
- ideal for shallowest / nearest / layer-based problems
    

### DFS mindset

- go down a branch first
    
- use recursion or a stack
    
- ideal for recursive subtree problems and path reasoning
    

### DFS order meanings

- preorder: process parent first
    
- inorder: process parent between left and right
    
- postorder: process parent after children
    

### Most important practical connection

- **BFS** is naturally linked to **level order traversal**
    
- **DFS** is naturally linked to **preorder / inorder / postorder**
    
- **postorder** is especially important because many tree problems depend on results from child subtrees
    
- **inorder** is especially important in BST because it produces sorted order
    

If you can clearly recognize when a problem is asking for:

- level expansion -> BFS
    
- subtree decomposition -> DFS
    
- left-root-right order in BST -> inorder
    
- bottom-up combination -> postorder
    

then your tree foundation is becoming solid.

---

## 19. Quick Reference Templates

### BFS

```java
public List<Integer> bfs(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) {
        return result;
    }

    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        TreeNode current = queue.poll();
        result.add(current.val);

        if (current.left != null) {
            queue.offer(current.left);
        }
        if (current.right != null) {
            queue.offer(current.right);
        }
    }

    return result;
}
```

### Preorder DFS

```java
public void preorder(TreeNode node, List<Integer> result) {
    if (node == null) {
        return;
    }

    result.add(node.val);
    preorder(node.left, result);
    preorder(node.right, result);
}
```

### Inorder DFS

```java
public void inorder(TreeNode node, List<Integer> result) {
    if (node == null) {
        return;
    }

    inorder(node.left, result);
    result.add(node.val);
    inorder(node.right, result);
}
```

### Postorder DFS

```java
public void postorder(TreeNode node, List<Integer> result) {
    if (node == null) {
        return;
    }

    postorder(node.left, result);
    postorder(node.right, result);
    result.add(node.val);
}
```

---

## 20. What You Should Be Able to Do After This Note

You should be able to:

- explain the difference between BFS and DFS clearly
    
- connect BFS to level order traversal
    
- connect DFS to preorder, inorder, and postorder
    
- write all four traversal styles in Java
    
- explain why BFS is suitable for minimum depth
    
- explain why postorder is suitable for bottom-up tree problems
    
- explain why inorder is important for BST
    
- choose between BFS and DFS based on problem structure
    

Once these are solid, you are ready to move into more advanced tree problems and later graph traversal.