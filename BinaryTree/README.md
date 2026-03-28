# 05 — Binary Trees: BST & AVL Tree

> **Files in project:** `BinaryTree/BST.c` · `BinaryTree/AVLtree.py`

---

## 🧠 Concept Overview

A **tree** is a hierarchical data structure consisting of nodes connected by edges, with exactly one node designated as the **root** and no cycles. A **binary tree** restricts each node to at most two children: `left` and `right`.

Trees are everywhere in computing: file systems, HTML DOM, expression parsing, database indexes (B-trees), and compiler abstract syntax trees all use tree structures.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Root | The topmost node (no parent) |
| Leaf | A node with no children |
| Height | Longest path from root to any leaf |
| Depth | Distance from root to a specific node |
| Subtree | Any node and all its descendants |
| BST Property | Left subtree values < node < right subtree values |
| Balanced | Heights of left and right subtrees differ by at most 1 at every node |
| Inorder | Left → Root → Right traversal |
| Preorder | Root → Left → Right traversal |
| Postorder | Left → Right → Root traversal |
| Level-order (BFS) | Level by level, top to bottom |
| AVL Tree | Self-balancing BST — balance factor ∈ {-1, 0, 1} |
| Rotation | Restructuring operation to restore balance |
| Balance Factor | `height(left) - height(right)` |

---

## 🔧 Binary Search Tree (BST) — `BST.c`

### Structure

```c
struct BinaryTree {
    int data;
    struct BinaryTree *left;
    struct BinaryTree *right;
    struct BinaryTree *parent;   // bonus: parent pointer for efficient operations
};
```

### BST Property

For every node N:
- All values in N's **left subtree** are **< N.data**
- All values in N's **right subtree** are **> N.data**

```
         8
       /   \
      3     10
     / \      \
    1   6      14
       / \    /
      4   7  13
```

### Operations

#### Insert
```c
struct BinaryTree* insert(struct BinaryTree* root, int value) {
    if (root == NULL) return create_node(value);   // found insertion point
    if (value < root->data)
        root->left = insert(root->left, value);     // go left
    if (value > root->data)
        root->right = insert(root->right, value);   // go right
    return root;
}
```
Complexity: O(h) where h = height. Best case O(log n) balanced, worst O(n) skewed.

#### Search
```c
struct BinaryTree* search(struct BinaryTree *root, int key) {
    if (root == NULL || key == root->data) return root;
    if (key < root->data) return search(root->left, key);
    return search(root->right, key);
}
```

#### Delete — Three Cases

| Case | Condition | Action |
|------|-----------|--------|
| 1 | Node has no children (leaf) | Simply remove it |
| 2 | Node has one child | Replace node with its child |
| 3 | Node has two children | Replace with **in-order successor** (minimum of right subtree), then delete successor |

```c
// Case 3: find the minimum in right subtree
struct BinaryTree *temp = find_min(root->right);
root->data = temp->data;              // copy successor's value
root->right = delete_node(root->right, temp->data);  // delete successor
```

#### Traversals

```c
// Inorder — produces SORTED output from a BST
void inorder(struct BinaryTree *root) {
    if (root != NULL) {
        inorder(root->left);
        printf("%d ", root->data);   // visit
        inorder(root->right);
    }
}

// Preorder — used to copy/serialize a tree
// Postorder — used to delete a tree (process children before parent)
```

**Traversal output for the tree above:**
- Inorder: `1 3 4 6 7 8 10 13 14` ← sorted!
- Preorder: `8 3 1 6 4 7 10 14 13`
- Postorder: `1 4 7 6 3 13 14 10 8`

---

## 🔧 AVL Tree — `AVLtree.py`

### Why AVL?

A plain BST degenerates into a linked list when elements are inserted in sorted order:
```
insert 1, 2, 3, 4, 5:
1
 \
  2
   \
    3
     \
      4 ← height = n, search is O(n)!
```

An **AVL tree** (Adelson-Velsky and Landis, 1962) maintains balance after every insert/delete by performing **rotations**.

### Balance Factor

```python
def get_balance(self, root):
    if not root: return 0
    return self.get_height(root.left) - self.get_height(root.right)
```

A node is **balanced** if its balance factor is −1, 0, or +1.

### Four Rotation Cases

After inserting a node, walk back up the tree. If balance factor becomes ±2, apply the appropriate rotation:

#### Case 1: Left-Left (balance > 1 and key < root.left.key)
```
    z                y
   /      Right     / \
  y    ─────────►  x   z
 /      Rotate
x
```
```python
if balance > 1 and key < root.left.key:
    return self.right_rotate(root)
```

#### Case 2: Right-Right (balance < -1 and key > root.right.key)
```
z                  y
 \      Left      / \
  y  ─────────►  z   x
   \    Rotate
    x
```

#### Case 3: Left-Right (balance > 1 and key > root.left.key)
```
  z              z              x
 /     Left     /    Right     / \
y    ────────► x   ─────────► y   z
 \    on y    /     on z
  x          y
```
```python
if balance > 1 and key > root.left.key:
    root.left = self.left_rotate(root.left)
    return self.right_rotate(root)
```

#### Case 4: Right-Left (balance < -1 and key < root.right.key)
Symmetric to Left-Right.

### Right Rotation Implementation

```python
def right_rotate(self, y):
    x = y.left
    T2 = x.right    # T2 is x's right subtree
    x.right = y     # y becomes right child of x
    y.left = T2     # T2 becomes y's left child
    # Update heights (y first, then x)
    y.height = 1 + max(self.get_height(y.left), self.get_height(y.right))
    x.height = 1 + max(self.get_height(x.left), self.get_height(x.right))
    return x        # x is the new root of this subtree
```

---

## 📐 Complexity Comparison

| Operation | BST Average | BST Worst | AVL Tree |
|-----------|-------------|-----------|----------|
| Search | O(log n) | O(n) | **O(log n)** |
| Insert | O(log n) | O(n) | **O(log n)** |
| Delete | O(log n) | O(n) | **O(log n)** |
| Space | O(n) | O(n) | **O(n)** |

AVL guarantees O(log n) by keeping height ≤ 1.44 log₂(n+2) − 1.328.

---

## 🌳 Other Tree Types (Study Topics)

### Red-Black Tree
Another self-balancing BST. Uses color (red/black) instead of height. Slightly less balanced than AVL but faster insertions. Used in Java's `TreeMap`, Linux kernel's CFS scheduler, and C++ `std::map`.

### B-Tree / B+ Tree
Multi-way tree (more than 2 children per node). Designed for disk access — nodes match disk block sizes. Used in every database index system (MySQL InnoDB, PostgreSQL).

### Trie (Prefix Tree)
Tree where each edge represents a character. Used for autocomplete, spell checking, IP routing tables.

### Segment Tree
Binary tree for range queries (range sum, range minimum). Supports update and query both in O(log n).

### Fenwick Tree (Binary Indexed Tree)
Space-efficient structure for prefix sum queries and point updates. O(log n) for both.

---

## ⚠️ Common Pitfalls

1. **BST with duplicates** — this implementation ignores duplicates (`<` not `<=`). Define your policy explicitly.
2. **Parent pointer maintenance** — when deleting, ensure parent pointers stay consistent.
3. **Rotation height update order** — in AVL rotation, always update the lower node's height before the upper node.
4. **Forgot to return root** — in recursive insert/delete, the new root must be returned and re-assigned by the caller.

---

## 💡 Practice Problems

1. Find the lowest common ancestor (LCA) of two nodes in a BST.
2. Convert a BST to a sorted doubly linked list.
3. Check if a binary tree is a valid BST.
4. Find the k-th smallest element in a BST.
5. Construct a BST from preorder traversal.
6. Implement level-order (BFS) traversal.

---

## 📚 Further Reading

- CLRS Chapter 12 (BST) and Chapter 13 (Red-Black Trees)
- [AVL Tree Visualizer](https://visualgo.net/en/bst)
- Knuth, TAOCP Vol. 3 — Section 6.2.3
