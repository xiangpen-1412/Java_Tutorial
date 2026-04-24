# Binary Search Tree (BST)

## 1. What a BST Is

A **Binary Search Tree (BST)** is a binary tree with an ordering rule:

- Every node has at most two children: `left` and `right`
    
- All values in the left subtree are **smaller** than the current node
    
- All values in the right subtree are **greater** than the current node
    
- Both left and right subtrees must also be BSTs
    

This ordering property is the entire reason BSTs are useful. It allows search, insertion, and deletion to skip half of the remaining structure at each step in the ideal case.

---

## 2. Core BST Property

For any node `node`:

- `value in left subtree < node.val`
    
- `value in right subtree > node.val`
    

This is stronger than just comparing the node with its direct children.

For example, in the following tree:

```text
        8
      /   \
     4     12
    / \   /  \
   2   6 10  14
```

- `2` and `6` are both less than `8`, because they are inside the left subtree of `8`
    
- `10` and `14` are both greater than `8`, because they are inside the right subtree of `8`
    

So BST validation is not just:

- left child < parent
    
- right child > parent
    

It is:

- **every value in the entire left subtree** must be smaller
    
- **every value in the entire right subtree** must be greater
    

---

## 3. Why BST Matters

BST is important because it combines:

- the hierarchical structure of a tree
    
- the ordered property of sorted data
    

This gives it a major advantage over an ordinary binary tree.

In a normal binary tree, to find a value, you may need to scan many nodes.

In a BST, you can decide at each node:

- go left
    
- go right
    
- stop if found
    

That makes BST the conceptual foundation for:

- balanced search trees
    
- `TreeMap`
    
- `TreeSet`
    
- tree-based ordered dictionaries / symbol tables
    
- many interview problems involving recursion and ordered search
    

---

## 4. Basic Node Definition in Java

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

This is the minimal structure used in most BST problems.

---

## 5. Search in BST

### 5.1 Search Idea

Suppose you want to find `key` starting from `root`.

At each node:

- if `root == null`, the value does not exist
    
- if `key == root.val`, found it
    
- if `key < root.val`, search left subtree
    
- if `key > root.val`, search right subtree
    

This works because the BST ordering property tells you which side can still possibly contain the target.

---

### 5.2 Recursive Search Code

```java
public TreeNode searchBST(TreeNode root, int key) {
    if (root == null) {
        return null;
    }

    if (root.val == key) {
        return root;
    }

    if (key < root.val) {
        return searchBST(root.left, key);
    } else {
        return searchBST(root.right, key);
    }
}
```

---

### 5.3 Iterative Search Code

```java
public TreeNode searchBST(TreeNode root, int key) {
    TreeNode current = root;

    while (current != null) {
        if (current.val == key) {
            return current;
        }

        if (key < current.val) {
            current = current.left;
        } else {
            current = current.right;
        }
    }

    return null;
}
```

---

### 5.4 Search Complexity

- Best / average case in a balanced tree: `O(log n)`
    
- Worst case in a skewed tree: `O(n)`
    

---

## 6. Insert in BST

### 6.1 Insert Idea

Insertion follows the same path logic as search.

At each node:

- if `key < root.val`, go left
    
- if `key > root.val`, go right
    
- when a null position is reached, create a new node there
    

A BST usually assumes one of these duplicate policies:

- duplicates are not allowed
    
- duplicates always go left
    
- duplicates always go right
    
- store frequency count inside each node
    

For interview-style BST basics, the most common assumption is:

- **no duplicates**
    

---

### 6.2 Recursive Insert Code (Returning Root)

```java
public TreeNode insertBST(TreeNode root, int key) {
    if (root == null) {
        return new TreeNode(key);
    }

    if (key < root.val) {
        root.left = insertBST(root.left, key);
    } else if (key > root.val) {
        root.right = insertBST(root.right, key);
    }

    return root;
}
```

This version is usually cleaner than a `void` version because it works naturally with recursion and subtree replacement.

---

### 6.3 Iterative Insert Code

```java
public TreeNode insertBST(TreeNode root, int key) {
    if (root == null) {
        return new TreeNode(key);
    }

    TreeNode current = root;

    while (true) {
        if (key < current.val) {
            if (current.left == null) {
                current.left = new TreeNode(key);
                break;
            }
            current = current.left;
        } else if (key > current.val) {
            if (current.right == null) {
                current.right = new TreeNode(key);
                break;
            }
            current = current.right;
        } else {
            break;
        }
    }

    return root;
}
```

---

### 6.4 Insert Complexity

- Best / average case in a balanced tree: `O(log n)`
    
- Worst case in a skewed tree: `O(n)`
    

---

## 7. Delete in BST

Deletion is the most important BST operation because it requires structural adjustment.

### 7.1 Delete Has Two Major Stages

#### Stage 1: Find the target node

Use BST ordering exactly like search.

#### Stage 2: Remove it correctly while preserving BST rules

There are three cases.

---

### 7.2 Case 1: The Node Has No Children

This is a leaf node.

Just delete it by returning `null`.

Example:

```text
    5
   /
  3
```

Delete `3`:

```text
    5
```

---

### 7.3 Case 2: The Node Has One Child

Remove the node and let its only child take its place.

Example:

```text
    5
   /
  3
   \
    4
```

Delete `3`:

```text
    5
   /
  4
```

---

### 7.4 Case 3: The Node Has Two Children

This is the key BST deletion case.

You cannot simply remove the node because that would disconnect its subtrees.

Instead, replace the node with a valid successor value.

The two standard choices are:

- **inorder successor** = smallest node in the right subtree
    
- **inorder predecessor** = largest node in the left subtree
    

The most common approach is:

- use the **right subtree minimum**
    

Then:

1. find successor
    
2. copy successor value into current node
    
3. delete the original successor node from the right subtree
    

---

### 7.5 Why the Right Subtree Minimum Works

If a node has two children, then the minimum node in its right subtree is:

- greater than everything in the left subtree
    
- greater than or equal to the current node position requirement
    
- smaller than the rest of the right subtree
    

So it is a legal replacement.

---

### 7.6 Standard Delete Code

```java
public TreeNode deleteNode(TreeNode root, int key) {
    if (root == null) {
        return null;
    }

    if (key < root.val) {
        root.left = deleteNode(root.left, key);
    } else if (key > root.val) {
        root.right = deleteNode(root.right, key);
    } else {
        if (root.left == null) {
            return root.right;
        }

        if (root.right == null) {
            return root.left;
        }

        TreeNode successor = findMin(root.right);
        root.val = successor.val;
        root.right = deleteNode(root.right, successor.val);
    }

    return root;
}

private TreeNode findMin(TreeNode node) {
    while (node.left != null) {
        node = node.left;
    }
    return node;
}
```

---

### 7.7 Why Delete Usually Returns `TreeNode`

This is one of the most important recursion ideas in tree problems.

Deletion can change the root of a subtree.

Examples:

- deleting a leaf may turn a child reference into `null`
    
- deleting a node with one child may replace that node with its child
    
- deleting the overall tree root may produce a new root for the entire tree
    

So each recursive call returns:

- **the new root of the current subtree after deletion**
    

That is why we write:

```java
root.left = deleteNode(root.left, key);
root.right = deleteNode(root.right, key);
```

The parent must reconnect to the updated subtree root.

---

### 7.8 Delete Complexity

- Best / average case in a balanced tree: `O(log n)`
    
- Worst case in a skewed tree: `O(n)`
    

---

## 8. `findMin` and `findMax`

These helper methods are very common in BST problems.

### Minimum

In a BST, the minimum value is the leftmost node.

```java
public TreeNode findMin(TreeNode root) {
    if (root == null) {
        return null;
    }

    TreeNode current = root;
    while (current.left != null) {
        current = current.left;
    }
    return current;
}
```

### Maximum

In a BST, the maximum value is the rightmost node.

```java
public TreeNode findMax(TreeNode root) {
    if (root == null) {
        return null;
    }

    TreeNode current = root;
    while (current.right != null) {
        current = current.right;
    }
    return current;
}
```

---

## 9. Inorder Traversal and Sorted Output

A major BST property:

- **inorder traversal of a BST produces values in sorted order**
    

Traversal order:

1. left subtree
    
2. current node
    
3. right subtree
    

Example:

```text
        8
      /   \
     4     12
    / \   /  \
   2   6 10  14
```

Inorder result:

```text
2 4 6 8 10 12 14
```

---

### Inorder Code

```java
public void inorder(TreeNode root) {
    if (root == null) {
        return;
    }

    inorder(root.left);
    System.out.print(root.val + " ");
    inorder(root.right);
}
```

This property is frequently used in:

- BST validation
    
- sorted output
    
- kth smallest / kth largest problems
    
- converting BST to sorted structures
    

---

## 10. Time Complexity of BST Operations

Let `h` be the tree height.

For BST operations like:

- search
    
- insert
    
- delete
    
- find min / max
    

The time complexity is generally:

- `O(h)`
    

So performance depends directly on tree height.

### Balanced BST

If the tree is balanced:

- `h = O(log n)`
    

Then operations are fast.

### Skewed BST

If the tree becomes one-sided:

- `h = O(n)`
    

Then operations degrade to linear time.

---

## 11. BST Degeneration Problem

A normal BST does **not** guarantee good shape.

If values are inserted in sorted order:

```text
1, 2, 3, 4, 5, 6, 7
```

The BST may become:

```text
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
           \
            7
```

This is essentially a linked list.

Then:

- search becomes `O(n)`
    
- insert becomes `O(n)`
    
- delete becomes `O(n)`
    

This is the main weakness of plain BST.

---

## 12. BST vs Balanced BST

A plain BST only guarantees the ordering property.

A balanced BST tries to maintain both:

- BST ordering
    
- approximately small height
    

Examples of self-balancing BSTs:

- AVL Tree
    
- Red-Black Tree
    

Why they matter:

- they avoid severe degeneration
    
- they preserve near-`O(log n)` performance
    
- they are used in real libraries and engineering systems
    

This is the natural next step after learning plain BST.

---

## 13. Common BST Interview Patterns

Once the basics are clear, BST questions usually extend into these areas.

### 13.1 Search / Insert / Delete

Foundational implementation problems.

### 13.2 Validate BST

Check whether a binary tree satisfies BST rules.

Important idea:

- not just comparing parent-child
    
- use lower and upper bounds through recursion
    

### 13.3 Lowest Common Ancestor in BST

A BST allows a more efficient solution than a general binary tree.

### 13.4 Kth Smallest Element in BST

Use inorder traversal because it produces sorted order.

### 13.5 Convert Sorted Array to BST

Classic balanced-construction problem.

### 13.6 Range Queries

Because the tree is ordered, parts of the tree can be pruned.

---

## 14. Common Mistakes

### Mistake 1: Confusing BST with a normal binary tree

A BST has ordering constraints. A normal binary tree does not.

### Mistake 2: Thinking validation only compares direct children

Wrong.

The whole left subtree must be smaller, and the whole right subtree must be greater.

### Mistake 3: Forgetting that delete may change subtree roots

That is why delete usually returns a `TreeNode`.

### Mistake 4: Not reassigning recursive results

Wrong style:

```java
deleteNode(root.left, key);
```

Correct style:

```java
root.left = deleteNode(root.left, key);
```

### Mistake 5: Forgetting duplicate policy

You must define whether duplicates are allowed and how they are handled.

### Mistake 6: Assuming BST is always `O(log n)`

Only balanced BSTs give reliable logarithmic performance.

### Mistake 7: Printing a `PriorityQueue`-style mental model onto BST

BST is node-linked and structurally ordered.  
Heap / `PriorityQueue` is shape-constrained and priority-ordered.  
They are not the same thing.

---

## 15. Full Basic BST Example

```java
public class BinarySearchTreeOperator {

    public TreeNode searchBST(TreeNode root, int key) {
        if (root == null) {
            return null;
        }

        if (root.val == key) {
            return root;
        }

        if (key < root.val) {
            return searchBST(root.left, key);
        } else {
            return searchBST(root.right, key);
        }
    }

    public TreeNode insertBST(TreeNode root, int key) {
        if (root == null) {
            return new TreeNode(key);
        }

        if (key < root.val) {
            root.left = insertBST(root.left, key);
        } else if (key > root.val) {
            root.right = insertBST(root.right, key);
        }

        return root;
    }

    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) {
            return null;
        }

        if (key < root.val) {
            root.left = deleteNode(root.left, key);
        } else if (key > root.val) {
            root.right = deleteNode(root.right, key);
        } else {
            if (root.left == null) {
                return root.right;
            }

            if (root.right == null) {
                return root.left;
            }

            TreeNode successor = findMin(root.right);
            root.val = successor.val;
            root.right = deleteNode(root.right, successor.val);
        }

        return root;
    }

    private TreeNode findMin(TreeNode node) {
        while (node.left != null) {
            node = node.left;
        }
        return node;
    }

    public void inorder(TreeNode root) {
        if (root == null) {
            return;
        }

        inorder(root.left);
        System.out.print(root.val + " ");
        inorder(root.right);
    }
}
```

---

## 16. What You Should Fully Understand Before Moving On

Before going to balanced trees, you should be completely comfortable with these points:

1. What BST ordering means
    
2. Why search can discard half the tree directionally
    
3. How insertion finds a null position and attaches a new node
    
4. Why deletion has three structural cases
    
5. Why the two-child delete case uses successor / predecessor
    
6. Why delete usually returns the new subtree root
    
7. Why inorder traversal of a BST is sorted
    
8. Why plain BST can degrade badly
    
9. Why balanced trees are the next step
    

---

## 17. Natural Next Topic

The next topic after plain BST is:

- **Balanced Tree concept**
    
- then **rotation**
    
- then **AVL Tree**
    
- then **Red-Black Tree concept**
    

That sequence matters because plain BST teaches ordering, while balanced trees teach how to preserve performance.