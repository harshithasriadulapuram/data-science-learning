
# Binary Search Tree (BST) — Coding Practice

## 1. Count the Nodes

Write a function that returns the total number of nodes in a binary tree.

```python
def count_nodes(root):
    if root is None:
        return 0

    return 1 + count_nodes(root.left) + count_nodes(root.right)
```

## 2. Calculate the Height

Height is the number of edges in the longest path from the root to a leaf. An empty tree has height -1 under this convention.

```python
def height(root):
    if root is None:
        return -1

    return 1 + max(height(root.left), height(root.right))
```

A tree containing only one node has height 0.

## 3. Count Leaf Nodes

A leaf is a node with no children.

```python
def count_leaves(root):
    if root is None:
        return 0

    if root.left is None and root.right is None:
        return 1

    return count_leaves(root.left) + count_leaves(root.right)
```

## 4. Find the Sum of All Nodes

```python
def tree_sum(root):
    if root is None:
        return 0

    return root.data + tree_sum(root.left) + tree_sum(root.right)
```

## 5. Validate a Binary Search Tree

Every node must satisfy the ordering constraints imposed by all its ancestors, not just its immediate parent.

```python
def is_bst(root, low=float("-inf"), high=float("inf")):
    if root is None:
        return True

    if not low < root.data < high:
        return False

    return (
        is_bst(root.left, low, root.data)
        and is_bst(root.right, root.data, high)
    )
```

This version assumes unique values and excludes duplicates.

## 6. Find the Kth Smallest Element

Inorder traversal of a BST with unique values visits the values in ascending order.

```python
def kth_smallest(root, k):
    stack = []
    current = root

    while stack or current:
        while current:
            stack.append(current)
            current = current.left

        current = stack.pop()
        k -= 1

        if k == 0:
            return current.data

        current = current.right

    return None  # k is invalid or too large
```

## 7. Find the Lowest Common Ancestor in a BST

The lowest common ancestor is the lowest node that has both target values as descendants, allowing a target node to be its own ancestor.

```python
def lowest_common_ancestor(root, p, q):
    while root:
        if p < root.data and q < root.data:
            root = root.left
        elif p > root.data and q > root.data:
            root = root.right
        else:
            return root

    return None
```

This function assumes both target values exist in the BST.

## Practice Tasks

Try these without looking at the solutions first:

1. Find the maximum depth of a binary tree.
2. Determine whether two binary trees are identical.
3. Search for a target in a BST iteratively.
4. Find the kth largest element.
5. Find the inorder predecessor of a node.
6. Find the inorder successor of a node.
7. Find the range of values between two numbers in a BST.
8. Convert a sorted list into a height-balanced BST.

## Complexity

Let n be the number of nodes and h be the tree height.

| Operation | Time |
|---|---|
| Count nodes | O(n) |
| Calculate height | O(n) |
| Count leaves | O(n) |
| Sum all nodes | O(n) |
| Validate BST | O(n) |
| Find kth smallest | O(h + k) |
| Lowest common ancestor in BST | O(h) |

For an unbalanced BST, h can be O(n). For a balanced BST, h is O(log n).
