
# Stack Data Structure in Python

## 1. What Is a Stack?

A stack is a linear data structure that follows **LIFO (Last In, First Out)**.

The element inserted last is the first one removed.

### Real-Life Example

Imagine a stack of plates:
- You place a new plate on top.
- You remove the top plate first.
- You cannot directly remove a plate from the middle without moving the plates above it.

## 2. Important Stack Operations

| Operation | Meaning |
|---|---|
| Push | Add an element to the top |
| Pop | Remove the top element |
| Peek / Top | View the top element without removing it |
| is_empty | Check whether the stack is empty |
| Size | Find the number of elements |

## 3. Implementing a Stack Using a Python List

Python lists support stack operations through `append()` and `pop()`.

```python
stack = []

stack.append(10)
stack.append(20)
stack.append(30)

print(stack)

top = stack.pop()
print("Removed:", top)

print("Current stack:", stack)
print("Top element:", stack[-1])
```

### Output

```text
[10, 20, 30]
Removed: 30
Current stack: [10, 20]
Top element: 20
```

### Explanation

1. `append()` inserts an element at the end of the list.
2. `pop()` removes and returns the last element.
3. `stack[-1]` accesses the top element without removing it.

**Important:** Calling `pop()` on an empty list raises an `IndexError`.

## 4. Building a Stack Using a Class

A class lets us organize stack data and operations together.

```python
class Stack:
    def __init__(self):
        self.items = []

    def push(self, item):
        self.items.append(item)

    def pop(self):
        if self.is_empty():
            raise IndexError("Cannot pop from an empty stack")

        return self.items.pop()

    def peek(self):
        if self.is_empty():
            raise IndexError("Cannot peek at an empty stack")

        return self.items[-1]

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)

    def display(self):
        print(self.items)


stack = Stack()

stack.push(10)
stack.push(20)
stack.push(30)

stack.display()
print("Top:", stack.peek())
print("Removed:", stack.pop())
print("Size:", stack.size())
print("Empty:", stack.is_empty())
stack.display()
```

### Output

```text
[10, 20, 30]
Top: 30
Removed: 30
Size: 2
Empty: False
[10, 20]
```

## 5. Understanding Each Method

### __init__()

```python
def __init__(self):
    self.items = []
```

Creates an empty list whenever a new Stack object is created.

### push()

```python
def push(self, item):
    self.items.append(item)
```

Adds a new element to the top of the stack.

Time complexity: O(1) amortized.

### pop()

```python
def pop(self):
    if self.is_empty():
        raise IndexError("Cannot pop from an empty stack")

    return self.items.pop()
```

Removes and returns the top element. We check for an empty stack first.

Time complexity: O(1).

### peek()

```python
def peek(self):
    if self.is_empty():
        raise IndexError("Cannot peek at an empty stack")

    return self.items[-1]
```

Returns the top element without removing it.

Time complexity: O(1).

### is_empty()

```python
def is_empty(self):
    return len(self.items) == 0
```

Returns `True` if the stack contains no elements; otherwise, returns `False`.

Time complexity: O(1).

### size()

```python
def size(self):
    return len(self.items)
```

Returns the number of elements in the stack.

Time complexity: O(1).

## 6. Stack Using a Linked List

A stack can also be implemented using linked-list nodes.

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None


class LinkedListStack:
    def __init__(self):
        self.top = None

    def push(self, data):
        new_node = Node(data)
        new_node.next = self.top
        self.top = new_node

    def pop(self):
        if self.top is None:
            raise IndexError("Cannot pop from an empty stack")

        value = self.top.data
        self.top = self.top.next
        return value

    def peek(self):
        if self.top is None:
            raise IndexError("Cannot peek at an empty stack")

        return self.top.data

    def is_empty(self):
        return self.top is None


stack = LinkedListStack()
stack.push(100)
stack.push(200)
stack.push(300)

print(stack.pop())
print(stack.peek())
```

### Output

```text
300
200
```

In this implementation, `top` points to the first node. Push and pop both happen at the beginning of the linked list.

## 7. Stack Using a List vs. Linked List

| Feature | Python List | Linked List |
|---|---|---|
| Push | O(1) amortized | O(1) |
| Pop | O(1) | O(1) |
| Peek | O(1) | O(1) |
| Memory | Dynamic array storage | Separate nodes and links |
| Implementation | Simpler | Requires node management |

For ordinary Python programs, a list is usually the simplest choice. For learning pointer-based data structures, a linked-list implementation is useful.

## 8. Applications of Stacks

1. Undo and redo operations.
2. Browser back navigation.
3. Function calls and recursion.
4. Parentheses matching.
5. Expression evaluation.
6. Depth-first search (DFS).
7. Backtracking algorithms.

## 9. Common Interview Questions

### Q1. What is LIFO?

Last In, First Out: the most recently inserted element is removed first.

### Q2. What is the difference between pop and peek?

- `pop()` removes and returns the top element.
- `peek()` returns the top element without removing it.

### Q3. What happens when you pop an empty stack?

A stack should handle underflow. In our implementation, `pop()` raises an `IndexError`.

### Q4. Can a stack be implemented using a queue?

Yes. It is possible, but additional operations may be required to maintain LIFO order.

### Q5. What is stack overflow?

Stack overflow occurs when a stack's capacity is exceeded in a bounded implementation. In recursion, the term can also refer to exhausting the call stack.

## 10. Practice Problems

1. Reverse a string using a stack.
2. Check whether parentheses are balanced.
3. Implement a stack without using Python's list `append()` or `pop()`.
4. Find the next greater element for every array element.
5. Evaluate a postfix expression.
6. Convert an infix expression to postfix.
7. Implement a minimum stack that supports retrieving the minimum element efficiently.

## 11. Key Takeaways

- A stack follows LIFO.
- `push()` adds an element; `pop()` removes the top element.
- `peek()` reads the top without removing it.
- Python lists provide a convenient stack implementation.
- Linked lists provide another way to implement a stack.
- Always handle operations on an empty stack.
