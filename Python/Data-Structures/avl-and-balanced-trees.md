
# AVL Trees and Balanced Binary Search Trees

## 1. What Is a Balanced Binary Search Tree?

A Binary Search Tree (BST) follows these rules:

- Values smaller than a node are stored in its left subtree.
- Values greater than a node are stored in its right subtree.
- Each subtree follows the same rules.

A BST can become unbalanced when values are inserted in sorted order.

For example, inserting `10, 20, 30, 40, 50` into a normal BST may produce a chain of nodes rather than a balanced tree.

In the worst case, searching, inserting, and deleting can take O(n) time.

A **balanced BST** keeps its height relatively small so that operations can remain efficient.

Examples include:
- AVL Trees
- Red-Black Trees

## 2. What Is an AVL Tree?

An **AVL Tree** is a self-balancing Binary Search Tree.

After insertion or deletion, it checks whether nodes have become unbalanced and performs rotations when necessary.

The name AVL comes from its inventors, Adelson-Velsky and Landis.

## 3. Height of a Node

The height of a node is the number of edges on the longest path from that node to a leaf.

For this implementation:
- An empty subtree has height `0`.
- A leaf node has height `1`.

```python
def height(node):
    if node is None:
        return 0

    return node.height
```

Storing the height in each node helps the tree calculate its balance efficiently.

## 4. Balance Factor

The balance factor of a node is:

Balance Factor = Height of Left Subtree - Height of Right Subtree

For an AVL Tree, each node must have a balance factor of `-1`, `0`, or `1`.

Examples:

| Left Height | Right Height | Balance Factor |
|---|---|---|
| 2 | 2 | 0 |
| 3 | 2 | 1 |
| 2 | 3 | -1 |
| 4 | 2 | 2 — unbalanced |

A balance factor outside the range `-1` to `1` means that the node needs rebalancing.

## 5. AVL Rotations

AVL Trees use four main cases.

### Case 1: Left-Left (LL)

A node becomes unbalanced because a value was inserted into the left subtree of its left child.

Solution: Perform a **right rotation**.

Example insertion order:

`30, 20, 10`

Before rotation:

```text
       30
      /
     20
    /
   10
```

After rotation:

```text
      20
     /  \
    10   30
```

### Case 2: Right-Right (RR)

A node becomes unbalanced because a value was inserted into the right subtree of its right child.

Solution: Perform a **left rotation**.

Example insertion order:

`10, 20, 30`

Before rotation:

```text
10
  \
   20
     \
      30
```

After rotation:

```text
      20
     /  \
    10   30
```

### Case 3: Left-Right (LR)

A node becomes unbalanced because a value was inserted into the right subtree of its left child.

Solution:
1. Perform a left rotation on the left child.
2. Perform a right rotation on the unbalanced node.

Example insertion order:

`30, 10, 20`

After rebalancing:

```text
      20
     /  \
    10   30
```

### Case 4: Right-Left (RL)

A node becomes unbalanced because a value was inserted into the left subtree of its right child.

Solution:
1. Perform a right rotation on the right child.
2. Perform a left rotation on the unbalanced node.

Example insertion order:

`10, 30, 20`

After rebalancing:

```text
      20
     /  \
    10   30
```

## 6. Create an AVL Node

Each node stores its value, children, and height.

```python
class AVLNode:
    def __init__(self, key):
        self.key = key
        self.left = None
        self.right = None
        self.height = 1
```

A new node starts with height `1` because it is initially a leaf.

## 7. Complete AVL Tree Implementation

The following implementation supports insertion, searching, and inorder traversal. Duplicate keys are ignored.

```python
class AVLNode:
    def __init__(self, key):
        self.key = key
        self.left = None
        self.right = None
        self.height = 1


class AVLTree:
    def height(self, node):
        if node is None:
            return 0
        return node.height

    def balance_factor(self, node):
        if node is None:
            return 0

        return (
            self.height(node.left)
            - self.height(node.right)
        )

    def update_height(self, node):
        node.height = 1 + max(
            self.height(node.left),
            self.height(node.right)
        )

    def rotate_right(self, y):
        x = y.left
        middle_subtree = x.right

        x.right = y
        y.left = middle_subtree

        self.update_height(y)
        self.update_height(x)

        return x

    def rotate_left(self, x):
        y = x.right
        middle_subtree = y.left

        y.left = x
        x.right = middle_subtree

        self.update_height(x)
        self.update_height(y)

        return y

    def insert(self, node, key):
        if node is None:
            return AVLNode(key)

        if key < node.key:
            node.left = self.insert(node.left, key)
        elif key > node.key:
            node.right = self.insert(node.right, key)
        else:
            return node

        self.update_height(node)
        balance = self.balance_factor(node)

        # Left-Left case
        if balance > 1 and key < node.left.key:
            return self.rotate_right(node)

        # Right-Right case
        if balance < -1 and key > node.right.key:
            return self.rotate_left(node)

        # Left-Right case
        if balance > 1 and key > node.left.key:
            node.left = self.rotate_left(node.left)
            return self.rotate_right(node)

        # Right-Left case
        if balance < -1 and key < node.right.key:
            node.right = self.rotate_right(node.right)
            return self.rotate_left(node)

        return node

    def search(self, node, key):
        if node is None or node.key == key:
            return node

        if key < node.key:
            return self.search(node.left, key)

        return self.search(node.right, key)

    def inorder(self, node):
        if node is not None:
            self.inorder(node.left)
            print(node.key, end=" ")
            self.inorder(node.right)


# Example usage
avl = AVLTree()
root = None

for value in [10, 20, 30, 40, 50, 25]:
    root = avl.insert(root, value)

print("Inorder traversal:")
avl.inorder(root)

print("\nSearch for 25:")
print(avl.search(root, 25).key)

print("\nSearch for 100:")
result = avl.search(root, 100)
print(result)  # None
```

Expected inorder output:

```text
10 20 25 30 40 50
```

Inorder traversal returns the values in sorted order.

## 8. How Insertion Works

When inserting a key:

1. Insert it using normal BST rules.
2. Update the height of each ancestor.
3. Calculate the balance factor.
4. Identify the LL, RR, LR, or RL case.
5. Perform the appropriate rotation.
6. Return the new root of the affected subtree.

Rotations preserve the BST ordering property while changing the tree's structure.

## 9. Time and Space Complexity

Let `n` be the number of nodes.

| Operation | AVL Tree |
|---|---|
| Search | O(log n) |
| Insert | O(log n) |
| Delete | O(log n) |
| Inorder traversal | O(n) |
| Height | O(1) to read stored height |
| Space for tree | O(n) |

Recursive insertion and search use O(log n) call-stack space in an AVL Tree because its height is logarithmic.

## 10. AVL Tree vs Normal BST vs Red-Black Tree

| Feature | Normal BST | AVL Tree | Red-Black Tree |
|---|---|---|---|
| Self-balancing | No | Yes | Yes |
| Worst-case height | O(n) | O(log n) | O(log n) |
| Search | O(n) worst case | O(log n) | O(log n) |
| Insert | O(n) worst case | O(log n) | O(log n) |
| Balancing rules | None | Strict height balance | Color-based rules |

AVL Trees maintain stricter balance than Red-Black Trees. This can make AVL Trees attractive for lookup-heavy workloads, while Red-Black Trees are often used when updates are frequent.

## 11. Common Interview Questions

1. What is an AVL Tree?
2. Why do we need self-balancing BSTs?
3. What is a balance factor?
4. Explain LL, RR, LR, and RL rotations.
5. What is the height of an AVL Tree?
6. Why does AVL search take O(log n) time?
7. What is the difference between an AVL Tree and a Red-Black Tree?
8. How do rotations preserve BST ordering?
9. What happens when duplicate keys are inserted?
10. How would you implement AVL deletion?

## 12. Practice Problems

### Beginner
1. Create an AVL node and calculate its height.
2. Calculate the balance factor of a node.
3. Implement left and right rotations.

### Intermediate
4. Implement AVL insertion.
5. Search for a key in an AVL Tree.
6. Print the tree using inorder traversal.
7. Insert sorted values and verify that the tree stays balanced.

### Advanced
8. Implement AVL deletion.
9. Print the tree level by level.
10. Compare the heights of a normal BST and an AVL Tree after inserting sorted values.

## Key Takeaways

- An AVL Tree is a self-balancing BST.
- Its balance factor must be -1, 0, or 1 at every node.
- Four rotation cases restore balance: LL, RR, LR, and RL.
- Search, insertion, and deletion take O(log n) time in a correctly maintained AVL Tree.
- Rotations change structure without breaking BST ordering.
