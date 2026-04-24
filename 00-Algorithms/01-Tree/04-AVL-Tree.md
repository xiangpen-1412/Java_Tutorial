___
## 1. What AVL Tree Is

An **AVL tree** is a **self-balancing binary search tree (BST)**.

It is still a BST, which means:

- every node’s left subtree contains only smaller values
    
- every node’s right subtree contains only larger values
    

So all ordinary BST search rules still apply.

The extra rule in an AVL tree is:

> for every node, the height difference between its left subtree and right subtree must not be greater than 1

This is what makes AVL a **balanced search tree** rather than just a normal BST.

## 2. Why AVL Tree Is Needed

A plain BST can become skewed.

For example, if values are inserted in sorted order:

```java
10, 20, 30, 40, 50
```

the BST may become:

```text
10
  \
   20
     \
      30
        \
         40
           \
            50
```

This is basically a linked list shape.

In that case:

- search degrades from `O(log n)` to `O(n)`
    
- insert degrades from `O(log n)` to `O(n)`
    
- delete also degrades from `O(log n)` to `O(n)`
    

AVL trees solve this by automatically rebalancing the tree after insertions and deletions.

## 3. Core Idea of AVL Tree

AVL does not just say “keep the tree roughly balanced.”

It uses a precise rule based on **height**.

For each node:

```text
| height(left subtree) - height(right subtree) | <= 1
```

If this condition is violated, the node is considered **unbalanced**, and the tree must be repaired using rotations.

So AVL is a **strictly balanced BST**.

## 4. Height

Height measures how deep a subtree is.

A common implementation convention is:

- `null` node height = `0`
    
- leaf node height = `1`
    

Example:

```text
    20
   /  \
 10   30
```

If `10` and `30` are leaves:

- `height(10) = 1`
    
- `height(30) = 1`
    
- `height(20) = 2`
    

A node’s height is usually calculated as:

```java
node.height = 1 + Math.max(height(node.left), height(node.right));
```

## 5. Balance Factor

The **balance factor** tells us whether a node is balanced.

```text
balance factor = height(left subtree) - height(right subtree)
```

Interpretation:

- `0` → perfectly balanced
    
- `1` → left subtree is slightly taller
    
- `-1` → right subtree is slightly taller
    
- `2` or more → left-heavy and unbalanced
    
- `-2` or less → right-heavy and unbalanced
    

Valid balance factors in an AVL tree are only:

```text
-1, 0, 1
```

Anything outside this range means rebalancing is required.

## 6. AVL Tree Is Still a BST First

This is extremely important.

AVL does **not** change BST ordering rules.

It only adds balancing maintenance.

So operations still happen in two layers:

### Layer 1: BST logic

Find the correct place by comparing keys.

### Layer 2: AVL balancing logic

After insertion or deletion, update heights and rebalance if needed.

This mental separation is very important for both AVL and Red-Black Tree.

## 7. Rotations

AVL repair is based on **rotations**.

There are only two primitive rotation operations:

- **left rotation**
    
- **right rotation**
    

All AVL rebalancing is built from these two operations.

### Important

Rotations do **not** swap values.

They change:

- parent-child references
    
- subtree attachment positions
    

while preserving BST ordering.

## 8. Right Rotation

Suppose we have this structure:

```text
        y
       / \
      x   T4
     / \
    T1 T2
```

After a **right rotation on `y`**:

```text
        x
       / \
      T1  y
         / \
        T2 T4
```

### What happened

- `x` moves up
    
- `y` moves down to the right
    
- `x.right` (`T2`) becomes `y.left`
    

### Why it works

It preserves the BST ordering:

```text
T1 < x < T2 < y < T4
```

before and after rotation.

## 9. Left Rotation

Suppose we have:

```text
      y
     / \
    T1  x
       / \
      T2 T4
```

After a **left rotation on `y`**:

```text
        x
       / \
      y  T4
     / \
    T1 T2
```

### What happened

- `x` moves up
    
- `y` moves down to the left
    
- `x.left` (`T2`) becomes `y.right`
    

Again, BST ordering is preserved.

## 10. Why Rotations Fix Balance

Rotations work because they do two things at the same time:

### 1. Preserve BST order

The relative in-order ordering of keys stays valid.

### 2. Redistribute height

A subtree that became too deep on one side gets restructured into a shallower and more balanced shape.

So rotations are not “tricks.” They are local structural repairs.

## 11. Four AVL Imbalance Patterns

AVL imbalance always falls into one of four patterns:

- LL
    
- RR
    
- LR
    
- RL
    

These are not four different kinds of rotations. They are four different **imbalance shapes**.

The actual repair actions are still only:

- left rotation
    
- right rotation
    

## 12. How to Recognize the Pattern

Suppose node `A` becomes unbalanced.

### Step 1: Check which side of `A` is heavy

- if `A` is left-heavy → problem is on the left side
    
- if `A` is right-heavy → problem is on the right side
    

### Step 2: Check which side of the child is heavy

- left child left-heavy → LL
    
- left child right-heavy → LR
    
- right child right-heavy → RR
    
- right child left-heavy → RL
    

You can also think in terms of insertion path:

- left → left = LL
    
- right → right = RR
    
- left → right = LR
    
- right → left = RL
    

## 13. LL Imbalance

### Shape

```text
        A
       /
      B
     /
    C
```

This means:

- `A` is left-heavy
    
- `A.left` is also left-heavy
    

### Example insertion order

```java
30, 20, 10
```

### Resulting BST

```text
    30
   /
  20
 /
10
```

### Fix

Perform a **right rotation on `A`**.

### After fix

```text
    20
   /  \
 10   30
```

### Intuition

This is a straight-line left-left chain. One rotation is enough.

## 14. RR Imbalance

### Shape

```text
    A
     \
      B
       \
        C
```

### Example insertion order

```java
10, 20, 30
```

### Resulting BST

```text
10
  \
   20
     \
      30
```

### Fix

Perform a **left rotation on `A`**.

### After fix

```text
   20
  /  \
10   30
```

### Intuition

This is the mirror image of LL.

## 15. LR Imbalance

### Shape

```text
        A
       /
      B
       \
        C
```

This means:

- `A` is left-heavy
    
- but `A.left` is right-heavy
    

### Example insertion order

```java
30, 10, 20
```

### Resulting BST

```text
   30
  /
10
  \
   20
```

### Why one rotation is not enough

This is not a straight line. It is a bend.

### Fix

Two steps:

1. left rotate `B`
    
2. right rotate `A`
    

### After step 1

```text
   30
  /
20
/
10
```

### After step 2

```text
   20
  /  \
10   30
```

### Intuition

First straighten the bend, then fix it like an LL case.

## 16. RL Imbalance

### Shape

```text
    A
     \
      B
     /
    C
```

This means:

- `A` is right-heavy
    
- but `A.right` is left-heavy
    

### Example insertion order

```java
10, 30, 20
```

### Resulting BST

```text
10
  \
   30
   /
  20
```

### Fix

Two steps:

1. right rotate `B`
    
2. left rotate `A`
    

### After step 1

```text
10
  \
   20
     \
      30
```

### After step 2

```text
   20
  /  \
10   30
```

### Intuition

Again, first straighten the bend, then fix it like an RR case.

## 17. One-Rotation vs Two-Rotation Cases

### One rotation

Used for straight-line patterns:

- LL
    
- RR
    

### Two rotations

Used for bent patterns:

- LR
    
- RL
    

This is a very important pattern to remember:

> straight line → one rotation  
> bend → two rotations

## 18. AVL Insertion Logic

AVL insertion is basically BST insertion plus repair.

### Full process

1. insert using normal BST rules
    
2. return upward through recursion
    
3. update height of each ancestor
    
4. compute balance factor
    
5. detect imbalance pattern
    
6. perform corresponding rotation
    
7. return the new subtree root
    

This is why recursive AVL insertion usually returns a `Node`.

Because after rotation, the root of that subtree may change.

## 19. Why Insert Must Return the New Root

This is one of the most important implementation ideas.

Example:

```text
   30
  /
20
/
10
```

After rebalancing, it becomes:

```text
   20
  /  \
10   30
```

The subtree root changed from `30` to `20`.

So insertion must return the new root after balancing.

That is why AVL insertion often looks like this:

```java
node.left = insert(node.left, key);
node.right = insert(node.right, key);
return node;
```

and after rebalancing, the returned node may be different from the original one.

## 20. AVL Insertion Code Example

```java
public class AVLTree {

    static class Node {
        int key;
        int height;
        Node left;
        Node right;

        Node(int key) {
            this.key = key;
            this.height = 1;
        }
    }

    private Node root;

    public void insert(int key) {
        root = insert(root, key);
    }

    private Node insert(Node node, int key) {
        // Step 1: normal BST insert
        if (node == null) {
            return new Node(key);
        }

        if (key < node.key) {
            node.left = insert(node.left, key);
        } else if (key > node.key) {
            node.right = insert(node.right, key);
        } else {
            return node;
        }

        // Step 2: update height
        updateHeight(node);

        // Step 3: compute balance
        int balance = getBalance(node);

        // Step 4: rebalance if needed

        // LL
        if (balance > 1 && key < node.left.key) {
            return rightRotate(node);
        }

        // RR
        if (balance < -1 && key > node.right.key) {
            return leftRotate(node);
        }

        // LR
        if (balance > 1 && key > node.left.key) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }

        // RL
        if (balance < -1 && key < node.right.key) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }

    private Node rightRotate(Node y) {
        Node x = y.left;
        Node t2 = x.right;

        x.right = y;
        y.left = t2;

        updateHeight(y);
        updateHeight(x);

        return x;
    }

    private Node leftRotate(Node y) {
        Node x = y.right;
        Node t2 = x.left;

        x.left = y;
        y.right = t2;

        updateHeight(y);
        updateHeight(x);

        return x;
    }

    private void updateHeight(Node node) {
        node.height = 1 + Math.max(height(node.left), height(node.right));
    }

    private int height(Node node) {
        return node == null ? 0 : node.height;
    }

    private int getBalance(Node node) {
        return node == null ? 0 : height(node.left) - height(node.right);
    }
}
```

## 21. AVL Deletion

Deletion in an AVL tree is more complicated than insertion.

### Why

Insertion makes one path taller. Deletion makes one path shorter.

That means after deletion:

- one ancestor may become unbalanced
    
- after fixing that ancestor, a higher ancestor may also become unbalanced
    

So deletion can trigger multiple rounds of rebalancing on the path upward.

### Two-layer mental model

#### Layer 1: BST deletion

Do the normal BST deletion first:

- remove leaf
    
- remove node with one child
    
- remove node with two children using predecessor or successor replacement
    

#### Layer 2: AVL repair

After deletion:

- update heights on the way back up
    
- compute balance factors
    
- apply LL / RR / LR / RL repairs where needed
    

Important: deletion still only uses the same four imbalance patterns.

## 22. Time Complexity

Because AVL keeps the height logarithmic:

### Search

```text
O(log n)
```

### Insert

```text
O(log n)
```

### Delete

```text
O(log n)
```

### Extra Space

If recursion is used, stack space is usually:

```text
O(log n)
```

## 23. AVL vs Normal BST

### Normal BST

- simple
    
- can become skewed
    
- worst-case operations can degrade to `O(n)`
    

### AVL Tree

- stricter balancing
    
- better guaranteed height
    
- search is reliably fast
    
- insertion and deletion need extra maintenance
    

So AVL is a tradeoff:

> more balancing work in exchange for more stable performance

## 24. AVL vs Red-Black Tree

This is especially important for interview preparation and for understanding `TreeMap` / `TreeSet`.

### AVL

- balances by directly controlling subtree height difference
    
- stricter balancing
    
- usually shorter height
    
- may require more maintenance on updates
    

### Red-Black Tree

- balances indirectly using color rules
    
- less strict
    
- may be slightly taller
    
- usually preferred for general-purpose ordered maps and sets
    

Java’s `TreeMap` and `TreeSet` are based on **Red-Black Tree**, not AVL.

So the value of learning AVL is not that Java ordered collections use it directly. The value is that AVL teaches you:

- what balancing means
    
- what rotations do
    
- how local structural repair works
    
- how to recognize imbalance patterns
    

All of that becomes very useful when you move into Red-Black Tree.

## 25. What You Must Really Remember About AVL

If you only keep the most important ideas, keep these:

1. AVL is a self-balancing BST.
    
2. It keeps every node’s left and right subtree heights within 1.
    
3. Balance factor = `height(left) - height(right)`.
    
4. Invalid balance means the node is unbalanced.
    
5. There are four imbalance patterns: LL, RR, LR, RL.
    
6. LL → right rotation
    
7. RR → left rotation
    
8. LR → left rotate child, then right rotate ancestor
    
9. RL → right rotate child, then left rotate ancestor
    
10. Rotations change structure, not values.
    
11. Insert and delete both use BST logic first, then balancing logic.
    
12. AVL is mainly important as the conceptual bridge to more advanced balanced trees like Red-Black Tree.
    

## 26. Final Summary

An AVL tree is a BST that strictly maintains balance by limiting the height difference of every node’s two subtrees.

Its purpose is to prevent the tree from becoming skewed and losing `O(log n)` performance.

The key concepts are:

- height
    
- balance factor
    
- four imbalance patterns
    
- left and right rotations
    
- recursive insertion/deletion with rebalancing
    

AVL is one of the clearest ways to learn how balanced search trees work, because it shows balancing directly through local structural repair.

It is also one of the best conceptual stepping stones toward understanding **Red-Black Tree**, which is the balanced tree model used internally by Java’s `TreeMap` and `TreeSet`.