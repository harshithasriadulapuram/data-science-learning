
# Doubly Linked List in Python

## 1. What Is a Doubly Linked List?

A doubly linked list is a linear data structure in which each node stores:

- `data`: the value.
- `prev`: a reference to the previous node.
- `next`: a reference to the next node.

Example:

`None <- 10 <-> 20 <-> 30 -> None`

Unlike a singly linked list, a doubly linked list supports traversal in both directions.

## 2. Create a Node

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.prev = None
        self.next = None
```

## 3. Create the Doubly Linked List

```python
class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
```

The `head` points to the first node, while `tail` points to the last node.

## 4. Insert at the Beginning

Add this method inside `DoublyLinkedList`:

```python
def insert_at_beginning(self, data):
    new_node = Node(data)

    if self.head is None:
        self.head = self.tail = new_node
        return

    new_node.next = self.head
    self.head.prev = new_node
    self.head = new_node
```

Example:

```python
dll = DoublyLinkedList()
dll.insert_at_beginning(20)
dll.insert_at_beginning(10)
```

The list becomes:

`None <- 10 <-> 20 -> None`

**Time complexity:** O(1)

## 5. Insert at the End

Add this method inside `DoublyLinkedList`:

```python
def insert_at_end(self, data):
    new_node = Node(data)

    if self.tail is None:
        self.head = self.tail = new_node
        return

    new_node.prev = self.tail
    self.tail.next = new_node
    self.tail = new_node
```

Example:

```python
dll.insert_at_end(30)
dll.insert_at_end(40)
```

**Time complexity:** O(1), because we maintain a tail pointer.

## 6. Display from Beginning to End

```python
def display_forward(self):
    current = self.head

    while current is not None:
        print(current.data, end=" <-> ")
        current = current.next

    print("None")
```

Example:

```python
dll.display_forward()
# 10 <-> 20 <-> 30 <-> 40 <-> None
```

## 7. Display from End to Beginning

```python
def display_backward(self):
    current = self.tail

    while current is not None:
        print(current.data, end=" <-> ")
        current = current.prev

    print("None")
```

Example:

```python
dll.display_backward()
# 40 <-> 30 <-> 20 <-> 10 <-> None
```

## 8. Search for a Value

```python
def search(self, target):
    current = self.head

    while current is not None:
        if current.data == target:
            return True
        current = current.next

    return False
```

Example:

```python
print(dll.search(30))  # True
print(dll.search(99))  # False
```

**Time complexity:** O(n)

## 9. Delete the First Matching Value

```python
def delete(self, target):
    current = self.head

    while current is not None and current.data != target:
        current = current.next

    if current is None:
        return False

    if current.prev is not None:
        current.prev.next = current.next
    else:
        self.head = current.next

    if current.next is not None:
        current.next.prev = current.prev
    else:
        self.tail = current.prev

    return True
```

Example:

```python
dll.delete(20)
dll.display_forward()
```

This removes the first node whose value matches `20`.

**Time complexity:** O(n) to search for the value. Once the node is known, unlinking it takes O(1).

## 10. Complete Example

Combine all the methods into the same class, then run:

```python
dll = DoublyLinkedList()

dll.insert_at_end(10)
dll.insert_at_end(20)
dll.insert_at_end(30)

dll.display_forward()
dll.display_backward()

dll.delete(20)
dll.display_forward()
```

Expected output:

```text
10 <-> 20 <-> 30 <-> None
30 <-> 20 <-> 10 <-> None
10 <-> 30 <-> None
```

## 11. Singly vs Doubly Linked List

| Feature | Singly Linked List | Doubly Linked List |
|---|---|---|
| References per node | `next` | `prev` and `next` |
| Forward traversal | Yes | Yes |
| Backward traversal | No | Yes |
| Extra memory | Less | More |
| Insert at beginning | O(1) | O(1) |
| Insert at end with tail | O(1) | O(1) |

## 12. Common Interview Questions

1. What is a doubly linked list?
2. Why does a doubly linked list need more memory?
3. How do you traverse it backwards?
4. How do you insert a node at the beginning?
5. How do you delete the head or tail?
6. Why is deleting a known node O(1)?
7. What problems can occur if `prev` and `next` are not updated correctly?

## Practice Challenges

1. Insert a node before a given value.
2. Insert a node after a given value.
3. Reverse a doubly linked list.
4. Find the middle node.
5. Implement a deque using a doubly linked list.
