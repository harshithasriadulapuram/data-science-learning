
# Binary Trees in Python

## 1. What Is a Tree?

A **tree** is a non-linear data structure that organizes elements hierarchically.

Important terminology:

- **Node:** An individual element in a tree.
- **Root:** The topmost node.
- **Parent:** A node that has child nodes.
- **Child:** A node connected below another node.
- **Leaf:** A node with no children.
- **Edge:** A connection between two nodes.
- **Subtree:** A node and its descendants.
- **Depth:** The number of edges from the root to a node.
- **Height:** The number of edges on the longest downward path from a node to a leaf.

Example:

```text
        10          Root
       /  \
      5    20       Children of 10
     / \
    2   7            Leaves: 2, 7, 20
```

## 2. What Is a Binary Tree?

A **binary tree** is a tree in which each node has at most two children:

- Left child
- Right child

A node may have zero, one, or two children.

## 3. Creating a Binary Tree Node

Python does not have a built-in binary tree node class, so we can define one.

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None


root = Node(10)
root.left = Node(5)
root.right = Node(20)

print(root.data)        # 10
print(root.left.data)   # 5
print(root.right.data)  # 20
```

### How does this work?

- `data` stores the value.
- `left` references the left child.
- `right` references the right child.
- `None` means that the child does not exist.

## 4. Types of Binary Trees

### Full Binary Tree

Every node has either zero or two children.

### Complete Binary Tree

Every level is completely filled except possibly the last, and the last level is filled from left to right.

### Perfect Binary Tree

Every internal node has two children, and all leaves are at the same depth.

### Balanced Binary Tree

The tree maintains a height that grows approximately logarithmically with the number of nodes, according to the balance conditions used by the particular tree definition.

### Skewed Binary Tree

Each node has only one child, creating a chain-like structure.

```text
10
  \
   20
     \
      30
        \
         40
```

A skewed tree can have height O(n), which makes some operations slower.

## 5. Tree Traversal

Traversal means visiting every node in a particular order.

The four important traversal methods are:

1. Inorder
2. Preorder
3. Postorder
4. Level-order

Consider this tree:

```text
        10
       /  \
      5    20
     / \
    2   7
```

### Inorder: Left → Root → Right

```python
def inorder(root):
    if root is None:
        return

    inorder(root.left)
    print(root.data, end=" ")
    inorder(root.right)
```

Output:

```text
2 5 7 10 20
```

### Preorder: Root → Left → Right

```python
def preorder(root):
    if root is None:
        return

    print(root.data, end=" ")
    preorder(root.left)
    preorder(root.right)
```

Output:

```text
10 5 2 7 20
```

### Postorder: Left → Right → Root

```python
def postorder(root):
    if root is None:
        return

    postorder(root.left)
    postorder(root.right)
    print(root.data, end=" ")
```

Output:

```text
2 7 5 20 10
```

### Level-order Traversal: Level by Level

Level-order traversal uses a queue.

```python
from collections import deque


def level_order(root):
    if root is None:
        return

    queue = deque([root])

    while queue:
        node = queue.popleft()
        print(node.data, end=" ")

        if node.left:
            queue.append(node.left)

        if node.right:
            queue.append(node.right)
```

Output:

```text
10 5 20 2 7
```

## 6. What Is a Binary Search Tree (BST)?

A **Binary Search Tree** is a binary tree that follows an ordering rule.

For the implementation in this file:

- Values smaller than a node go to its left subtree.
- Values greater than a node go to its right subtree.
- Duplicate values are not inserted.

Example:

```text
        50
       /  \
      30   70
     / \   / \
    20 40 60 80
```

This ordering makes searching efficient when the tree is reasonably balanced.

## 7. Inserting a Value into a BST

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None


def insert(root, value):
    if root is None:
        return Node(value)

    if value < root.data:
        root.left = insert(root.left, value)
    elif value > root.data:
        root.right = insert(root.right, value)

    return root


root = None

for value in [50, 30, 70, 20, 40, 60, 80]:
    root = insert(root, value)
```

### How insertion works

1. If the current position is empty, create a node.
2. If the value is smaller, continue into the left subtree.
3. If the value is greater, continue into the right subtree.
4. Return the current root so the links remain connected.

## 8. Searching in a BST

```python
def search(root, target):
    if root is None:
        return False

    if root.data == target:
        return True

    if target < root.data:
        return search(root.left, target)

    return search(root.right, target)


print(search(root, 60))  # True
print(search(root, 90))  # False
```

The BST ordering lets us eliminate one subtree at each step.

## 9. Finding the Minimum and Maximum

The minimum is the leftmost node. The maximum is the rightmost node.

```python
def find_min(root):
    if root is None:
        return None

    while root.left is not None:
        root = root.left

    return root.data


def find_max(root):
    if root is None:
        return None

    while root.right is not None:
        root = root.right

    return root.data


print(find_min(root))  # 20
print(find_max(root))  # 80
```

## 10. Deleting a Node from a BST

Deleting a node involves three possible cases:

1. The node is a leaf: remove it.
2. The node has one child: replace it with that child.
3. The node has two children: replace its value with its inorder successor, then delete the successor.

The inorder successor is the smallest value in the right subtree.

```python
def delete(root, value):
    if root is None:
        return None

    if value < root.data:
        root.left = delete(root.left, value)

    elif value > root.data:
        root.right = delete(root.right, value)

    else:
        # Case 1 and case 2
        if root.left is None:
            return root.right

        if root.right is None:
            return root.left

        # Case 3: find the inorder successor
        successor = root.right

        while successor.left is not None:
            successor = successor.left

        root.data = successor.data
        root.right = delete(root.right, successor.data)

    return root
```

Example:

```python
root = delete(root, 70)
print(search(root, 70))  # False
```

## 11. Time Complexity

Let `n` be the number of nodes and `h` be the tree height.

| Operation | Balanced BST | Worst-case unbalanced BST |
|---|---|---|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |
| Traversal | O(n) | O(n) |
| Find minimum/maximum | O(log n) | O(n) |

A regular BST does not automatically stay balanced. Self-balancing trees, such as AVL trees and Red-Black trees, maintain additional rules to control their height.

## 12. Binary Tree vs Binary Search Tree

| Feature | Binary Tree | Binary Search Tree |
|---|---|---|
| Children per node | At most two | At most two |
| Ordering rule | Not required | Smaller left, larger right |
| Search | O(n) in general | O(log n) when balanced |
| Inorder traversal | No guaranteed sorting | Produces sorted values when keys are unique |
| Common use | Hierarchical structures | Ordered searching and updates |

## 13. Practice Problems

Solve these independently:

1. Create a binary tree with five nodes.
2. Implement inorder traversal.
3. Implement preorder traversal.
4. Implement postorder traversal.
5. Implement level-order traversal.
6. Insert values into a BST.
7. Search for a value in a BST.
8. Find the minimum and maximum values.
9. Count the nodes in a binary tree.
10. Calculate the height of a binary tree.
11. Count the leaf nodes.
12. Delete a node from a BST.

## Key Takeaways

- A binary tree allows at most two children per node.
- Traversals determine the order in which nodes are visited.
- A BST maintains an ordering rule that supports efficient searching.
- BST search, insertion, and deletion depend on tree height.
- An unbalanced BST can degrade to O(n) operations.
