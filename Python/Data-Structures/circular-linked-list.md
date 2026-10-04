
# Circular Linked List in Python

## 1. What Is a Circular Linked List?

A circular linked list is a linked list in which the last node points back to the first node instead of pointing to `None`.

In a singly circular linked list:
- Each node contains data and a `next` reference.
- The last node's `next` points to the head.
- The list can be traversed starting from any node, but traversal must stop using a suitable condition.

Example:

10 -> 20 -> 30
^           |
|___________|

## 2. Creating a Node

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None
```

## 3. Creating the Circular Linked List

```python
class CircularLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
```

We maintain `head` and `tail` so that insertion at the beginning and end can be performed efficiently.

## 4. Inserting at the Beginning

```python
def insert_at_beginning(self, data):
    new_node = Node(data)

    if self.head is None:
        self.head = self.tail = new_node
        new_node.next = new_node
        return

    new_node.next = self.head
    self.head = new_node
    self.tail.next = self.head
```

When the list is empty, the new node points to itself. Otherwise, we update the head and reconnect the tail to the new head.

Time complexity: O(1)

## 5. Inserting at the End

```python
def insert_at_end(self, data):
    new_node = Node(data)

    if self.head is None:
        self.head = self.tail = new_node
        new_node.next = new_node
        return

    new_node.next = self.head
    self.tail.next = new_node
    self.tail = new_node
```

Time complexity: O(1)

## 6. Traversing the List

A normal `while current is not None` loop will not work because the list never reaches `None`. Instead, stop when traversal returns to the head.

```python
def display(self):
    if self.head is None:
        print("List is empty")
        return

    current = self.head

    while True:
        print(current.data, end=" -> ")
        current = current.next

        if current == self.head:
            break

    print("(back to head)")
```

Time complexity: O(n)

## 7. Deleting the First Node

```python
def delete_at_beginning(self):
    if self.head is None:
        print("List is empty")
        return

    if self.head == self.tail:
        self.head = self.tail = None
        return

    self.head = self.head.next
    self.tail.next = self.head
```

Handle an empty list and a single-node list separately before updating the head.

Time complexity: O(1)

## 8. Deleting the Last Node

```python
def delete_at_end(self):
    if self.head is None:
        print("List is empty")
        return

    if self.head == self.tail:
        self.head = self.tail = None
        return

    current = self.head

    while current.next != self.tail:
        current = current.next

    current.next = self.head
    self.tail = current
```

We must find the node immediately before the tail. With only `head` and `tail` references in a singly circular list, this operation takes O(n).

Time complexity: O(n)

## 9. Searching for a Value

```python
def search(self, target):
    if self.head is None:
        return False

    current = self.head

    while True:
        if current.data == target:
            return True

        current = current.next

        if current == self.head:
            break

    return False
```

Time complexity: O(n)

## 10. Complete Implementation

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None


class CircularLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    def insert_at_beginning(self, data):
        new_node = Node(data)

        if self.head is None:
            self.head = self.tail = new_node
            new_node.next = new_node
            return

        new_node.next = self.head
        self.head = new_node
        self.tail.next = self.head

    def insert_at_end(self, data):
        new_node = Node(data)

        if self.head is None:
            self.head = self.tail = new_node
            new_node.next = new_node
            return

        new_node.next = self.head
        self.tail.next = new_node
        self.tail = new_node

    def display(self):
        if self.head is None:
            print("List is empty")
            return

        current = self.head

        while True:
            print(current.data, end=" -> ")
            current = current.next

            if current == self.head:
                break

        print("(back to head)")

    def delete_at_beginning(self):
        if self.head is None:
            print("List is empty")
            return

        if self.head == self.tail:
            self.head = self.tail = None
            return

        self.head = self.head.next
        self.tail.next = self.head

    def delete_at_end(self):
        if self.head is None:
            print("List is empty")
            return

        if self.head == self.tail:
            self.head = self.tail = None
            return

        current = self.head

        while current.next != self.tail:
            current = current.next

        current.next = self.head
        self.tail = current

    def search(self, target):
        if self.head is None:
            return False

        current = self.head

        while True:
            if current.data == target:
                return True

            current = current.next

            if current == self.head:
                break

        return False


cll = CircularLinkedList()

cll.insert_at_end(10)
cll.insert_at_end(20)
cll.insert_at_end(30)
cll.display()

cll.insert_at_beginning(5)
cll.display()

print(cll.search(20))

cll.delete_at_beginning()
cll.display()

cll.delete_at_end()
cll.display()
```

### Expected Output

```text
10 -> 20 -> 30 -> (back to head)
5 -> 10 -> 20 -> 30 -> (back to head)
True
10 -> 20 -> 30 -> (back to head)
10 -> 20 -> (back to head)
```

## 11. Time Complexity Summary

| Operation | Complexity |
|---|---|
| Insert at beginning | O(1) |
| Insert at end | O(1) |
| Delete at beginning | O(1) |
| Delete at end | O(n) |
| Traversal | O(n) |
| Search | O(n) |

## 12. Practice Questions

1. Count the nodes in a circular linked list.
2. Find the length without entering an infinite loop.
3. Insert a node after a specified value.
4. Delete a node by value.
5. Check whether a given linked list is circular.
6. Implement a circular linked list using only a tail reference.

## 13. Key Takeaways

- The last node points back to the head.
- Traversal needs a stopping condition to prevent an infinite loop.
- An empty list and a single-node list require special handling.
- Maintaining a tail reference makes insertion at the end O(1).
- Deleting the last node is O(n) in this singly linked list implementation.
