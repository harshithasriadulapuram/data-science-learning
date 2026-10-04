
# Linked Lists in Python

## 1. What Is a Linked List?

A linked list is a linear data structure made of nodes. Each node stores a value and a reference to the next node.

Unlike a Python list, linked-list nodes do not need to occupy contiguous memory locations.

A singly linked list looks like this:

`10 -> 20 -> 30 -> None`

- `10`, `20`, and `30` are node values.
- Each node points to the next node.
- `None` marks the end of the list.
- `head` points to the first node.

## 2. Create a Node

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

first = Node(10)
second = Node(20)

first.next = second

print(first.data)       # 10
print(first.next.data)  # 20
```

Here, `first.next` references the second node.

## 3. Create a Linked List Class

```python
class LinkedList:
    def __init__(self):
        self.head = None
```

Initially, `head` is `None` because the list is empty.

## 4. Insert at the Beginning

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def insert_at_beginning(self, data):
        new_node = Node(data)
        new_node.next = self.head
        self.head = new_node

    def display(self):
        current = self.head

        while current is not None:
            print(current.data, end=" -> ")
            current = current.next

        print("None")

linked_list = LinkedList()
linked_list.insert_at_beginning(20)
linked_list.insert_at_beginning(10)
linked_list.display()

# Output:
# 10 -> 20 -> None
```

**Time complexity:** O(1)

The new node becomes the head.

## 5. Insert at the End

Add this method inside the `LinkedList` class:

```python
def insert_at_end(self, data):
    new_node = Node(data)

    if self.head is None:
        self.head = new_node
        return

    current = self.head

    while current.next is not None:
        current = current.next

    current.next = new_node
```

Example:

```python
linked_list = LinkedList()
linked_list.insert_at_end(10)
linked_list.insert_at_end(20)
linked_list.insert_at_end(30)
linked_list.display()

# 10 -> 20 -> 30 -> None
```

**Time complexity:** O(n) when there is no tail pointer.

## 6. Search for a Value

Add this method inside the class:

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
linked_list = LinkedList()
linked_list.insert_at_end(10)
linked_list.insert_at_end(20)

print(linked_list.search(20))  # True
print(linked_list.search(50))  # False
```

**Time complexity:** O(n)

## 7. Delete the First Matching Value

Add this method inside the class:

```python
def delete(self, target):
    if self.head is None:
        return False

    if self.head.data == target:
        self.head = self.head.next
        return True

    current = self.head

    while current.next is not None:
        if current.next.data == target:
            current.next = current.next.next
            return True

        current = current.next

    return False
```

Example:

```python
linked_list = LinkedList()

for value in [10, 20, 30]:
    linked_list.insert_at_end(value)

linked_list.delete(20)
linked_list.display()

# 10 -> 30 -> None
```

**Time complexity:** O(n)

This method deletes only the first matching value.

## 8. Count the Nodes

Add this method inside the class:

```python
def length(self):
    count = 0
    current = self.head

    while current is not None:
        count += 1
        current = current.next

    return count
```

Example:

```python
print(linked_list.length())  # 2
```

**Time complexity:** O(n)

## 9. Reverse a Linked List

Add this method inside the class:

```python
def reverse(self):
    previous = None
    current = self.head

    while current is not None:
        next_node = current.next
        current.next = previous
        previous = current
        current = next_node

    self.head = previous
```

Example:

```python
linked_list = LinkedList()

for value in [10, 20, 30]:
    linked_list.insert_at_end(value)

linked_list.reverse()
linked_list.display()

# 30 -> 20 -> 10 -> None
```

**Time complexity:** O(n)  
**Auxiliary space:** O(1)

The method reverses links without creating new nodes.

## 10. Linked List vs Python List

| Feature | Linked List | Python List |
|---|---|---|
| Access by index | O(n) | O(1) |
| Insert at beginning | O(1) | O(n) |
| Search by value | O(n) | O(n) |
| Append at end | O(n) without tail | Amortized O(1) |
| Extra memory | References between nodes | Dynamic array storage |

Actual performance also depends on implementation and workload.

## Practice Challenges

1. Find the middle node of a linked list.
2. Find the nth node from the end.
3. Detect a cycle in a linked list.
4. Remove duplicates from a sorted linked list.
5. Merge two sorted linked lists.
6. Check whether a linked list is a palindrome.
