
# Deque (Double-Ended Queue) in Python

## 1. What Is a Deque?

A deque stands for **Double-Ended Queue**. It is a linear data structure that allows insertion and deletion at both the front and the rear.

Unlike a standard queue, which normally inserts at the rear and removes from the front, a deque supports operations at both ends.

Example:

```text
FRONT <-> 10 <-> 20 <-> 30 <-> REAR
```

## 2. Types of Deque

### Input-Restricted Deque

Insertion is allowed at only one end, but deletion is allowed at both ends.

### Output-Restricted Deque

Deletion is allowed at only one end, but insertion is allowed at both ends.

## 3. Python's collections.deque

Python provides `deque` in the `collections` module.

```python
from collections import deque

dq = deque()

dq.append(10)       # Insert at rear
dq.append(20)
dq.appendleft(5)    # Insert at front

print(dq)

dq.pop()            # Remove from rear
dq.popleft()        # Remove from front

print(dq)
```

Output:

```text
deque([5, 10, 20])
deque([10])
```

## 4. Important Deque Operations

| Operation | Purpose | Complexity |
|---|---|---|
| `append(x)` | Insert at rear | O(1) |
| `appendleft(x)` | Insert at front | O(1) |
| `pop()` | Remove from rear | O(1) |
| `popleft()` | Remove from front | O(1) |
| `dq[0]` | Access front element | O(1) |
| `dq[-1]` | Access rear element | O(1) |
| `len(dq)` | Find number of elements | O(1) |

## 5. Complete Deque Implementation Using a Class

```python
from collections import deque


class Deque:
    def __init__(self):
        self.items = deque()

    def insert_front(self, value):
        self.items.appendleft(value)

    def insert_rear(self, value):
        self.items.append(value)

    def delete_front(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.items.popleft()

    def delete_rear(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.items.pop()

    def get_front(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.items[0]

    def get_rear(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.items[-1]

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)

    def display(self):
        print(list(self.items))


dq = Deque()

dq.insert_rear(10)
dq.insert_rear(20)
dq.insert_front(5)

dq.display()

print("Front:", dq.get_front())
print("Rear:", dq.get_rear())

print("Deleted from front:", dq.delete_front())
print("Deleted from rear:", dq.delete_rear())

dq.display()
```

Output:

```text
[5, 10, 20]
Front: 5
Rear: 20
Deleted from front: 5
Deleted from rear: 20
[10]
```

## 6. Using a Deque to Check a Palindrome

A palindrome reads the same forward and backward.

```python
from collections import deque


def is_palindrome(text):
    dq = deque(
        character.lower()
        for character in text
        if character.isalnum()
    )

    while len(dq) > 1:
        if dq.popleft() != dq.pop():
            return False

    return True


print(is_palindrome("level"))
print(is_palindrome("Python"))
print(is_palindrome("Never odd or even"))
```

Output:

```text
True
False
True
```

The function ignores spaces, punctuation, and letter case.

Time complexity: O(n).

## 7. Deque vs Queue vs Stack

| Feature | Stack | Queue | Deque |
|---|---|---|---|
| Insertion | Top | Rear | Both ends |
| Removal | Top | Front | Both ends |
| Ordering | LIFO | FIFO | Depends on operations |
| Python implementation | `list` | `deque` | `collections.deque` |

## 8. Applications of Deque

1. Sliding window algorithms.
2. Palindrome checking.
3. Implementing stacks and queues.
4. Breadth-first search.
5. Maintaining maximum or minimum values in a sliding window.
6. Undo and redo systems.

## 9. Common Interview Questions

### Q1. What does deque stand for?

Double-Ended Queue.

### Q2. What is the difference between a queue and a deque?

A standard queue inserts at the rear and removes from the front. A deque supports insertion and deletion at both ends.

### Q3. Why use collections.deque instead of a list?

Deque supports efficient insertion and removal at both ends. Removing the first item from a list using `pop(0)` requires O(n) time.

### Q4. Can a deque behave like a stack?

Yes. Use `append()` and `pop()`.

### Q5. Can a deque behave like a queue?

Yes. Use `append()` and `popleft()`.

## 10. Practice Problems

1. Implement a deque using a circular array.
2. Reverse a string using a deque.
3. Check whether a string is a palindrome.
4. Find the maximum value in every sliding window of size K.
5. Implement a queue using a deque.
6. Implement a stack using a deque.

## 11. Key Takeaways

- A deque supports operations at both ends.
- `append()` and `pop()` operate at the rear.
- `appendleft()` and `popleft()` operate at the front.
- Python's `collections.deque` is useful for efficient double-ended operations.
- A deque can implement both stack and queue behavior.
