
# Tree and Binary Search Tree Interview Practice

This file contains common tree and Binary Search Tree (BST) interview problems with Python solutions, explanations, and time and space complexity.

## 1. Tree Node Definition

A binary tree node stores a value and references to its left and right children.

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

Example tree:

```text
        4
       / \
      2   7
     / \
    1   3
```

Create this tree in Python:

```python
root = TreeNode(4)
root.left = TreeNode(2)
root.right = TreeNode(7)
root.left.left = TreeNode(1)
root.left.right = TreeNode(3)
```

---

## 2. Find the Maximum Depth of a Binary Tree

**Problem:** Return the number of nodes along the longest path from the root to a leaf.

```python
def max_depth(root):
    if root is None:
        return 0

    left_depth = max_depth(root.left)
    right_depth = max_depth(root.right)

    return 1 + max(left_depth, right_depth)
```

**Example:**

```python
print(max_depth(root))  # 3
```

**How it works:**
1. If the node is `None`, return `0`.
2. Recursively calculate the left subtree depth.
3. Recursively calculate the right subtree depth.
4. Return one plus the greater depth.

**Complexity:**
- Time: `O(n)`
- Space: `O(h)` recursion stack, where `h` is tree height.

---

## 3. Invert a Binary Tree

**Problem:** Swap the left and right children of every node.

```python
def invert_tree(root):
    if root is None:
        return None

    root.left, root.right = root.right, root.left

    invert_tree(root.left)
    invert_tree(root.right)

    return root
```

**Example:**

Before:

```text
    4
   / \
  2   7
```

After:

```text
    4
   / \
  7   2
```

**Complexity:**
- Time: `O(n)`
- Space: `O(h)` recursion stack.

---

## 4. Level-Order Traversal

**Problem:** Return the node values level by level, from left to right.

This uses a queue to process nodes in breadth-first order.

```python
from collections import deque

def level_order(root):
    if root is None:
        return []

    result = []
    queue = deque([root])

    while queue:
        level_size = len(queue)
        level = []

        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)

            if node.left:
                queue.append(node.left)

            if node.right:
                queue.append(node.right)

        result.append(level)

    return result
```

**Example:**

```python
print(level_order(root))
# [[4], [2, 7], [1, 3]]
```

**Complexity:**
- Time: `O(n)`
- Space: `O(n)` for the queue and result.

---

## 5. Validate a Binary Search Tree

**Problem:** Determine whether a binary tree satisfies BST rules.

For every node:
- All values in its left subtree must be smaller.
- All values in its right subtree must be larger.
- Both subtrees must also satisfy these rules.

Checking only the immediate children is not enough. Each node must respect the limits inherited from its ancestors.

```python
def is_valid_bst(root):
    def validate(node, low, high):
        if node is None:
            return True

        if not (low < node.val < high):
            return False

        return (
            validate(node.left, low, node.val)
            and validate(node.right, node.val, high)
        )

    return validate(root, float("-inf"), float("inf"))
```

**Example:**

```python
valid_root = TreeNode(5)
valid_root.left = TreeNode(3)
valid_root.right = TreeNode(8)

print(is_valid_bst(valid_root))  # True
```

**Complexity:**
- Time: `O(n)`
- Space: `O(h)` recursion stack.

This implementation assumes duplicate values are not allowed.

---

## 6. Find the Kth Smallest Element in a BST

**Problem:** Return the kth smallest value in a BST, where `k` starts from `1`.

An inorder traversal of a BST visits values in ascending order.

```python
def kth_smallest(root, k):
    stack = []
    current = root

    while current or stack:
        while current:
            stack.append(current)
            current = current.left

        current = stack.pop()
        k -= 1

        if k == 0:
            return current.val

        current = current.right

    return None
```

**Example:**

```python
bst = TreeNode(5)
bst.left = TreeNode(3)
bst.right = TreeNode(7)
bst.left.left = TreeNode(2)
bst.left.right = TreeNode(4)

print(kth_smallest(bst, 3))  # 4
```

The sorted values are `2, 3, 4, 5, 7`; the third smallest is `4`.

**Complexity:**
- Time: `O(h + k)` in the usual analysis, with `O(n)` as the worst case.
- Space: `O(h)` for the stack.

---

## 7. Lowest Common Ancestor in a BST

**Problem:** Find the lowest node that has both target nodes as descendants. A node can be a descendant of itself.

Because this is a BST, we can use the values to choose which direction to move.

```python
def lowest_common_ancestor(root, p, q):
    current = root

    while current:
        if p.val < current.val and q.val < current.val:
            current = current.left
        elif p.val > current.val and q.val > current.val:
            current = current.right
        else:
            return current

    return None
```

**Example:**

```python
bst = TreeNode(6)
bst.left = TreeNode(2)
bst.right = TreeNode(8)
bst.left.left = TreeNode(0)
bst.left.right = TreeNode(4)

answer = lowest_common_ancestor(
    bst, bst.left, bst.left.right
)

print(answer.val)  # 2
```

**Complexity:**
- Time: `O(h)`
- Space: `O(1)`.

This solution assumes both target nodes are present in the BST.

---

## 8. Check Whether a Binary Tree Is Height-Balanced

**Problem:** A binary tree is height-balanced if, at every node, the heights of its left and right subtrees differ by at most one.

```python
def is_balanced(root):
    def height(node):
        if node is None:
            return 0

        left_height = height(node.left)
        if left_height == -1:
            return -1

        right_height = height(node.right)
        if right_height == -1:
            return -1

        if abs(left_height - right_height) > 1:
            return -1

        return 1 + max(left_height, right_height)

    return height(root) != -1
```

**Example:**

```python
balanced_root = TreeNode(1)
balanced_root.left = TreeNode(2)
balanced_root.right = TreeNode(3)

print(is_balanced(balanced_root))  # True
```

**Why return `-1`?**

It acts as a signal that a subtree is unbalanced. This avoids repeatedly calculating the same subtree heights.

**Complexity:**
- Time: `O(n)`
- Space: `O(h)` recursion stack.

---

## 9. Diameter of a Binary Tree

**Problem:** Find the length of the longest path between any two nodes.

The diameter is measured in **edges**, not nodes. The longest path does not necessarily pass through the root.

```python
def diameter_of_binary_tree(root):
    diameter = 0

    def height(node):
        nonlocal diameter

        if node is None:
            return 0

        left_height = height(node.left)
        right_height = height(node.right)

        diameter = max(
            diameter,
            left_height + right_height
        )

        return 1 + max(left_height, right_height)

    height(root)
    return diameter
```

**Example:**

```python
diameter_root = TreeNode(1)
diameter_root.left = TreeNode(2)
diameter_root.right = TreeNode(3)
diameter_root.left.left = TreeNode(4)
diameter_root.left.right = TreeNode(5)

print(diameter_of_binary_tree(diameter_root))  # 3
```

The longest path is `4 → 2 → 1 → 3` or `5 → 2 → 1 → 3`, depending on the tree's edges; here the longest path contains three edges.

**Complexity:**
- Time: `O(n)`
- Space: `O(h)` recursion stack.

---

## 10. Count the Number of Nodes

**Problem:** Return the total number of nodes in a binary tree.

```python
def count_nodes(root):
    if root is None:
        return 0

    return (
        1
        + count_nodes(root.left)
        + count_nodes(root.right)
    )
```

**Example:**

```python
print(count_nodes(root))  # 5
```

**Complexity:**
- Time: `O(n)`
- Space: `O(h)` recursion stack.

---

## 11. Find the Minimum Value in a BST

**Problem:** Return the smallest value in a non-empty BST.

The minimum value is the leftmost node.

```python
def find_min(root):
    if root is None:
        return None

    current = root

    while current.left:
        current = current.left

    return current.val
```

**Example:**

```python
print(find_min(bst))  # 2
```

**Complexity:**
- Time: `O(h)`
- Space: `O(1)`.

---

## 12. Find the Maximum Value in a BST

**Problem:** Return the largest value in a non-empty BST.

The maximum value is the rightmost node.

```python
def find_max(root):
    if root is None:
        return None

    current = root

    while current.right:
        current = current.right

    return current.val
```

**Example:**

```python
print(find_max(bst))  # 7
```

**Complexity:**
- Time: `O(h)`
- Space: `O(1)`.

---

## Interview Revision Checklist

- [ ] Explain a binary tree versus a BST.
- [ ] Find a tree's maximum depth.
- [ ] Invert a binary tree.
- [ ] Perform level-order traversal using a queue.
- [ ] Validate a BST using lower and upper bounds.
- [ ] Find the kth smallest element using inorder traversal.
- [ ] Find the lowest common ancestor in a BST.
- [ ] Check whether a tree is balanced.
- [ ] Calculate the diameter of a binary tree.
- [ ] Count nodes and find minimum and maximum values.
- [ ] Explain time and space complexity using `n` for node count and `h` for tree height.

## Practice Strategy

1. Read the problem and identify the base case.
2. Try to write the solution without looking at the answer.
3. Trace the code on a small tree by hand.
4. Test empty trees, single-node trees, and unbalanced trees.
5. Explain the approach and complexity aloud as if answering an interviewer.
