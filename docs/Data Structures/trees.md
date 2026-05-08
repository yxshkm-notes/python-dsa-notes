# Trees

!!! abstract "What You'll Learn"
    - ✅ What a tree is and core terminology (root, leaf, height, depth)
    - ✅ Binary trees vs Binary Search Trees (BST)
    - ✅ How to implement a BST in Python from scratch
    - ✅ Tree traversals: inorder, preorder, postorder, level-order
    - ✅ Time and space complexity of each operation
    - ✅ Balanced trees, AVL intuition, and when trees degrade to O(n)
    - ✅ Classic interview patterns: LCA, diameter, max depth, path sum

A **tree** is a hierarchical, non-linear data structure made of **nodes** connected by **edges**. Every tree has one **root** node at the top, and each node can have zero or more **children**. Unlike linked lists, trees branch — making them ideal for representing hierarchies, sorted data, and decision logic.

!!! tip "New to Trees?"
    Think of a tree like a **company org chart** — the CEO is the root, managers are internal nodes, and individual contributors are leaves. Every employee (except the CEO) has exactly one boss (parent), but a boss can have many reports (children).

!!! info "Coming from Linked Lists?"
    A linked list is just a degenerate tree — every node has at most one child. Trees generalize this: each node can point to multiple children. A **binary tree** restricts this to at most two children (left and right).

!!! warning "Keep in mind"
    Python has no built-in tree type. You build one with classes. Also, an **unbalanced** BST degrades to O(n) for all operations — the same as a linked list — so balance matters in practice.

---

## Tree Terminology

```
                    [10]          ← root (depth 0)
                   /    \
                [5]      [15]     ← internal nodes (depth 1)
               /   \       \
             [3]   [7]     [20]   ← leaves (depth 2)

Height of tree = 2  (longest path from root to leaf)
Depth of node  = distance from root
Degree of node = number of children
```

| Term | Meaning |
|---|---|
| **Root** | Top node, no parent |
| **Leaf** | Node with no children |
| **Internal node** | Node with at least one child |
| **Height** | Longest path from root to a leaf |
| **Depth** | Distance from root to a given node |
| **Subtree** | A node and all its descendants |
| **Balanced** | Heights of left and right subtrees differ by at most 1 |

---

## 1️⃣ Node & BinaryTree Class

```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None    # left child
        self.right = None   # right child

# Manual construction
root = TreeNode(10)
root.left = TreeNode(5)
root.right = TreeNode(15)
root.left.left = TreeNode(3)
root.left.right = TreeNode(7)
root.right.right = TreeNode(20)
```

**Memory diagram:**
```
        [10]
       /    \
    [5]      [15]
   /   \        \
 [3]   [7]      [20]
```

---

## 2️⃣ Binary Search Tree (BST)

A **BST** enforces an ordering rule at every node:

```
left subtree values  <  node.value  <  right subtree values
```

This rule makes search, insert, and delete all O(log n) on a balanced tree.

### BST Insert — O(log n) average

```python
class BST:
    def __init__(self):
        self.root = None

    def insert(self, value):
        if not self.root:
            self.root = TreeNode(value)
        else:
            self._insert(self.root, value)

    def _insert(self, node, value):
        if value < node.value:
            if node.left is None:
                node.left = TreeNode(value)
            else:
                self._insert(node.left, value)
        else:
            if node.right is None:
                node.right = TreeNode(value)
            else:
                self._insert(node.right, value)
```

**Step-by-step (inserting 10, 5, 15, 3):**
```
Insert 10:   [10]

Insert 5:    [10]          5 < 10 → go left
              /
            [5]

Insert 15:   [10]          15 > 10 → go right
            /    \
          [5]    [15]

Insert 3:    [10]          3 < 10 → left, 3 < 5 → left
            /    \
          [5]    [15]
          /
        [3]
```

---

### BST Search — O(log n) average

```python
def search(self, value):
    return self._search(self.root, value)

def _search(self, node, value):
    if node is None:
        return False          # not found
    if value == node.value:
        return True           # found
    elif value < node.value:
        return self._search(node.left, value)
    else:
        return self._search(node.right, value)
```

**Output:**
```python
bst.search(7)    # → True
bst.search(99)   # → False
```

---

### BST Delete — O(log n) average

Deletion has three cases:

```python
def delete(self, value):
    self.root = self._delete(self.root, value)

def _delete(self, node, value):
    if node is None:
        return None

    if value < node.value:
        node.left = self._delete(node.left, value)
    elif value > node.value:
        node.right = self._delete(node.right, value)
    else:
        # Case 1: No children (leaf) — just remove
        if not node.left and not node.right:
            return None
        # Case 2: One child — replace with child
        if not node.left:
            return node.right
        if not node.right:
            return node.left
        # Case 3: Two children — replace with inorder successor
        successor = self._min_node(node.right)
        node.value = successor.value
        node.right = self._delete(node.right, successor.value)

    return node

def _min_node(self, node):
    while node.left:
        node = node.left
    return node
```

**The 3 delete cases:**
```
Case 1 — Leaf node (delete 3):
    [5]          [5]
    /      →
  [3]

Case 2 — One child (delete 5):
  [10]          [10]
  /      →      /
[5]           [3]
/
[3]

Case 3 — Two children (delete 10):
    [10]              [15]       ← inorder successor replaces
   /    \    →       /    \
 [5]    [15]       [5]    [20]
           \
           [20]
```

---

## 3️⃣ Tree Traversals

There are four standard ways to visit every node. The order matters depending on what you need.

=== "Inorder (Left → Root → Right)"

    ```python
    def inorder(root):
        if root is None:
            return []
        return inorder(root.left) + [root.value] + inorder(root.right)
    ```

    **Output:**
    ```
    Tree:   [10]
           /    \
         [5]    [15]
         /
       [3]

    Inorder: [3, 5, 10, 15]  ← always sorted for a BST ✅
    ```

    !!! tip "Inorder on a BST = sorted order"
        This is the most useful traversal for BSTs. It visits nodes in ascending order.

=== "Preorder (Root → Left → Right)"

    ```python
    def preorder(root):
        if root is None:
            return []
        return [root.value] + preorder(root.left) + preorder(root.right)
    ```

    **Output:**
    ```
    Preorder: [10, 5, 3, 15]   ← root first, useful for copying/serializing a tree
    ```

=== "Postorder (Left → Right → Root)"

    ```python
    def postorder(root):
        if root is None:
            return []
        return postorder(root.left) + postorder(root.right) + [root.value]
    ```

    **Output:**
    ```
    Postorder: [3, 5, 15, 10]  ← children before parent, useful for deletion/eval
    ```

=== "Level-order (BFS)"

    ```python
    from collections import deque

    def level_order(root):
        if not root:
            return []
        result, queue = [], deque([root])
        while queue:
            node = queue.popleft()
            result.append(node.value)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        return result
    ```

    **Output:**
    ```
    Level-order: [10, 5, 15, 3]   ← row by row, top to bottom
    ```

    !!! info "Level-order uses a queue (BFS), all others use recursion (DFS)"
        Level-order is the go-to for problems involving rows, cousins, or minimum depth.

---

## 4️⃣ Complexity: Balanced vs Unbalanced

=== "Balanced BST — O(log n)"

    ```
           [10]
          /    \
        [5]    [15]       Height = log₂(n)
       /   \   /   \      Each level eliminates half the nodes
     [3]  [7][12] [20]
    ```

    | Operation | Time |
    |---|---|
    | Search | O(log n) |
    | Insert | O(log n) |
    | Delete | O(log n) |
    | Traversal | O(n) |
    | Space | O(n) |

=== "Unbalanced BST — O(n)"

    ```
    [10]
       \
       [15]        Inserting sorted data: [10, 15, 20, 25]
          \        creates a right-skewed tree —
          [20]     effectively a linked list!
             \
             [25]
    ```

    | Operation | Time |
    |---|---|
    | Search | O(n) |
    | Insert | O(n) |
    | Delete | O(n) |

    !!! warning "Always ask: is the input sorted?"
        Inserting sorted data into a plain BST gives worst-case O(n). Use a **self-balancing tree** (AVL, Red-Black) or sort + build from middle to avoid this.

=== "Self-Balancing (AVL intuition)"

    ```python
    # AVL trees auto-rotate to maintain balance factor ≤ 1
    # Python's sortedcontainers.SortedList uses a B-tree internally
    # For interviews, know the concept — rarely need to implement

    from sortedcontainers import SortedList
    sl = SortedList([5, 3, 10, 1])
    sl.add(7)           # O(log n) insert, stays sorted
    sl.discard(3)       # O(log n) delete
    print(sl[0])        # O(log n) access
    ```

    Balance factor = height(left subtree) − height(right subtree)
    Must stay in {-1, 0, 1} at every node. Rotations fix violations.

---

## 5️⃣ Classic Interview Patterns

### Maximum Depth — O(n)

```python
def max_depth(root):
    if not root:
        return 0
    left_depth = max_depth(root.left)
    right_depth = max_depth(root.right)
    return 1 + max(left_depth, right_depth)
```

**Output:**
```
      [1]
     /   \
   [2]   [3]
   /
 [4]

max_depth → 3
```

---

### Check if Balanced — O(n)

```python
def is_balanced(root):
    def check(node):
        if not node:
            return 0
        left = check(node.left)
        right = check(node.right)
        if left == -1 or right == -1 or abs(left - right) > 1:
            return -1       # signal imbalance upward
        return 1 + max(left, right)
    return check(root) != -1
```

---

### Lowest Common Ancestor (LCA) — O(n)

```python
def lca(root, p, q):
    if not root or root == p or root == q:
        return root
    left = lca(root.left, p, q)
    right = lca(root.right, p, q)
    if left and right:
        return root     # p and q are on different sides
    return left or right
```

**Visualization:**
```
        [3]
       /   \
     [5]   [1]
    /   \
  [6]   [2]

LCA(6, 2) → [5]   (both under 5)
LCA(5, 1) → [3]   (split at root)
```

---

### Path Sum — O(n)

```python
def has_path_sum(root, target):
    if not root:
        return False
    if not root.left and not root.right:    # leaf
        return root.value == target
    remaining = target - root.value
    return has_path_sum(root.left, remaining) or \
           has_path_sum(root.right, remaining)
```

**Output:**
```
      [5]
     /   \
   [4]   [8]
   /    /   \
 [11] [13]  [4]
 /  \          \
[7] [2]        [1]

has_path_sum(root, 22) → True   (5→4→11→2)
```

---

### Diameter of a Tree — O(n)

```python
def diameter(root):
    max_d = [0]

    def depth(node):
        if not node:
            return 0
        left = depth(node.left)
        right = depth(node.right)
        max_d[0] = max(max_d[0], left + right)  # path through this node
        return 1 + max(left, right)

    depth(root)
    return max_d[0]
```

!!! info "Diameter = longest path between any two nodes"
    The path doesn't have to pass through the root. Track the best `left + right` at every node during the depth recursion.

---

### Invert a Binary Tree — O(n)

```python
def invert_tree(root):
    if not root:
        return None
    root.left, root.right = invert_tree(root.right), invert_tree(root.left)
    return root
```

**Output:**
```
Before:   [4]          After:    [4]
         /   \                  /   \
       [2]   [7]              [7]   [2]
       / \   / \              / \   / \
      [1][3][6][9]           [9][6][3][1]
```

---

## ✅ Quick Reference Summary

| Operation | BST (balanced) | BST (worst) | Notes |
|---|---|---|---|
| Search | O(log n) | O(n) | Halves search space each step |
| Insert | O(log n) | O(n) | Walk to correct leaf |
| Delete | O(log n) | O(n) | 3 cases: leaf, 1 child, 2 children |
| Inorder traversal | O(n) | O(n) | Sorted output for BST |
| Level-order (BFS) | O(n) | O(n) | Uses a queue |
| Max depth | O(n) | O(n) | Recursive post-order |
| Space (call stack) | O(log n) | O(n) | Recursion depth = height |

| Traversal | Order | Use case |
|---|---|---|
| **Inorder** | Left → Root → Right | Sorted output, BST validation |
| **Preorder** | Root → Left → Right | Tree copy, serialization |
| **Postorder** | Left → Right → Root | Tree deletion, expression eval |
| **Level-order** | Row by row (BFS) | Shortest path, row-based problems |

!!! warning "Key interview gotchas"
    - Always handle `root is None` as the base case in recursive functions
    - Inorder traversal of a BST gives sorted order — use this to validate a BST
    - When a problem says "minimum depth" or "level", reach for level-order (BFS), not DFS
    - Two-children deletion replaces with the **inorder successor** (smallest in right subtree)
    - Diameter and max path sum problems need a **global variable** or list to track the best answer across all nodes