___
## 1. What a Tree Is

A **tree** is a **non-linear data structure** used to represent **hierarchical relationships**.

Unlike arrays, lists, stacks, and queues, which are mainly linear, a tree allows one node to branch into multiple child nodes.

Typical examples:

- folder structures
    
- DOM trees
    
- organization charts
    
- expression trees
    
- search trees
    

A tree is made of **nodes** connected by **edges**.

---

## 2. Why Tree Matters

Tree is one of the most important data structures because many advanced structures are built on top of it or closely related to it:

- Binary Tree
    
- Binary Search Tree (BST)
    
- Heap
    
- AVL Tree
    
- Red-Black Tree
    
- Trie
    
- segment tree
    
- syntax / expression trees
    

In Java, tree knowledge is also necessary for understanding:

- `TreeMap`
    
- `TreeSet`
    
- part of `HashMap` treeification behavior
    

---

## 3. Core Terminology

### Node

A **node** is one element in a tree.

A node usually contains:

- stored data
    
- references to child nodes
    

### Root

The **root** is the topmost node of a tree.

A tree has exactly one root.

### Parent / Child

If node `A` directly connects to node `B` below it:

- `A` is the **parent** of `B`
    
- `B` is the **child** of `A`
    

### Sibling

Two nodes are **siblings** if they have the same parent.

### Leaf

A **leaf** is a node with no children.

### Internal Node

A node that has at least one child is usually called an **internal node**.

### Edge

An **edge** is a connection between two nodes.

A tree with `n` nodes always has exactly `n - 1` edges.

### Subtree

A **subtree** is any node together with all of its descendants.

This idea is extremely important because recursion on trees is based on it.

### Ancestor / Descendant

If a node is above another node on the path toward the root, it is an **ancestor**.

If a node is below another node, it is a **descendant**.

### Degree

The **degree** of a node is the number of its direct children.

- leaf node degree = `0`
    
- tree degree = maximum node degree in the whole tree
    

---

## 4. Depth, Level, and Height

These are easy to confuse.

### Depth

The **depth** of a node is the number of edges from the root to that node.

- root depth = `0`
    
- every step downward adds `1`
    

### Level

**Level** usually describes which layer the node is on.

Two common conventions exist:

- root at level `0`
    
- root at level `1`
    

So always check which definition is being used.

### Height of a Node

The **height** of a node is the number of edges on the longest path from that node down to a leaf.

- leaf height = `0`
    

### Height of a Tree

The **height of the tree** is the height of the root.

### Good Memory Rule

- **depth** = how far a node is from the root
    
- **height** = how far a node can still go downward
    

---

## 5. Path

A **path** is a sequence of nodes connected by edges.

The **path length** is usually the number of edges.

---

## 6. Why Trees Work So Well with Recursion

A tree is naturally recursive.

Any node, together with the structure below it, forms another tree.

That means a large tree problem can often be split into:

- solve left subtree
    
- solve right subtree
    
- combine results
    

This is the foundation of most binary tree problems.

---

## 7. Binary Tree

A **binary tree** is a tree where each node has **at most two children**.

These are usually called:

- `left`
    
- `right`
    

Important: **at most two** does not mean exactly two.

So these are all valid binary trees:

- node with two children
    
- node with only left child
    
- node with only right child
    
- single-node tree
    

---

## 8. General Tree vs Binary Tree

### General Tree

A node may have any number of children.

### Binary Tree

A node may have at most two children.

Binary tree is a special case of tree.

Not every tree is a binary tree.

---

## 9. Basic Java Tree Node Structure

```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}
```

This means each node stores:

- one integer value
    
- one reference to the left child
    
- one reference to the right child
    

---

## 10. Building a Small Binary Tree in Java

Target tree:

```text
        10
       /  \
      5    20
     / \
    3   7
```

Code:

```java
public class Main {
    public static void main(String[] args) {
        TreeNode root = new TreeNode(10);

        root.left = new TreeNode(5);
        root.right = new TreeNode(20);

        root.left.left = new TreeNode(3);
        root.left.right = new TreeNode(7);
    }
}

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}
```

This is the core idea of tree construction in Java:

- create node objects
    
- connect them using references
    

---

## 11. Tree vs Linear Data Structures

### Array / ArrayList

Focus:

- indexed access
    
- ordered positions
    
- often contiguous logical access
    

### LinkedList

Focus:

- sequential linking
    
- previous / next relationship
    

### Tree

Focus:

- hierarchy
    
- branching
    
- parent / child relationship
    
- subtrees
    

So in tree problems, the most important question is usually not "what is the next element?" but:

- what is this node's left subtree?
    
- what is this node's right subtree?
    
- what is the relationship between these nodes?
    

---

## 12. Example Terminology Practice

Consider this tree:

```text
          A
        /   \
       B     C
      / \     \
     D   E     F
        /
       G
```

### Correct Analysis

- **root**: `A`
    
- **leaf nodes**: `D`, `G`, `F`
    
- `E`'s **parent**: `B`
    
- `B`'s **children**: `D`, `E`
    
- `G`'s **depth**: `3`
    
- `E`'s **height**: `1`
    
- whole tree **height**: `3`
    
- subtree rooted at `B`: `B`, `D`, `E`, `G`
    
- `D` and `E` are **siblings**
    
- this **is** a binary tree
    

---

## 13. Common Mistakes

### Mistake 1: Forgetting to include the subtree root itself

If the subtree is rooted at `B`, then `B` must be included.

Wrong:

```text
D, E, G
```

Correct:

```text
B, D, E, G
```

### Mistake 2: Mixing up depth and height

- `depth` is measured from the root downward
    
- `height` is measured from the node downward to the deepest leaf
    

### Mistake 3: Thinking binary tree means every node must have two children

Wrong.

Binary tree only requires **at most two children**.

---

## 14. What You Should Be Able to Do After This Part

You should be able to look at a tree and quickly identify:

- root
    
- leaf nodes
    
- parent / child relationships
    
- siblings
    
- subtree of a node
    
- depth of a node
    
- height of a node
    
- whether it is a binary tree
    

That is the foundation for the next stage:

- binary tree traversal
    
- recursion on trees
    
- BST
    
- balanced trees
    
- heap
    
- advanced tree problems