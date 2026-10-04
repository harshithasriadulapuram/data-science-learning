
# Queue Data Structure in Python

## 1. What Is a Queue?

A queue is a linear data structure that follows **FIFO (First In, First Out)**.

The first element inserted is the first element removed.

### Real-Life Example

Imagine people standing in a ticket queue:
- The first person to join is served first.
- New people join at the end.
- People leave from the front.

### Queue Structure

```text
FRONT -> 10 -> 20 -> 30 <- REAR
```

- `FRONT`: The element removed next.
- `REAR`: The position where new elements are added.

## 2. Basic Queue Operations

| Operation | Meaning |
|---|---|
| Enqueue | Add an element to the rear |
| Dequeue | Remove an element from the front |
| Peek / Front | View the front element |
| is_empty | Check whether the queue is empty |
| Size | Count the elements |

## 3. Queue Using a Python List

```python
queue = []

queue.append(10)
queue.append(20)
queue.append(30)

print(queue)

removed = queue.pop(0)
print("Removed:", removed)
print("Queue:", queue)
```

### Output

```text
[10, 20, 30]
Removed: 10
Queue: [20, 30]
```

**Important:** `pop(0)` takes O(n) time because the remaining list elements must shift. Avoid it for large queues.

## 4. Queue Using collections.deque

Python's `collections.deque` supports efficient insertion and removal at both ends.

```python
from collections import deque

queue = deque()

queue.append(10)       # Enqueue
queue.append(20)
queue.append(30)

print(queue)

removed = queue.popleft()  # Dequeue
print("Removed:", removed)
print("Front:", queue[0])
print("Queue:", queue)
```

### Output

```text
deque([10, 20, 30])
Removed: 10
Front: 20
Queue: deque([20, 30])
```

Both `append()` and `popleft()` take O(1) time.

## 5. Implementing a Queue Using a Class

```python
from collections import deque


class Queue:
    def __init__(self):
        self.items = deque()

    def enqueue(self, item):
        self.items.append(item)

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Cannot dequeue from an empty queue")

        return self.items.popleft()

    def peek(self):
        if self.is_empty():
            raise IndexError("Cannot peek at an empty queue")

        return self.items[0]

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)

    def display(self):
        print(list(self.items))


queue = Queue()

queue.enqueue(10)
queue.enqueue(20)
queue.enqueue(30)

queue.display()
print("Front:", queue.peek())
print("Removed:", queue.dequeue())
print("Size:", queue.size())
queue.display()
```

### Output

```text
[10, 20, 30]
Front: 10
Removed: 10
Size: 2
[20, 30]
```

## 6. Implementing a Queue Using a Linked List

A linked-list queue maintains two references:
- `front`: the first node.
- `rear`: the last node.

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None


class LinkedListQueue:
    def __init__(self):
        self.front = None
        self.rear = None

    def enqueue(self, data):
        new_node = Node(data)

        if self.rear is None:
            self.front = self.rear = new_node
            return

        self.rear.next = new_node
        self.rear = new_node

    def dequeue(self):
        if self.front is None:
            raise IndexError("Cannot dequeue from an empty queue")

        value = self.front.data
        self.front = self.front.next

        if self.front is None:
            self.rear = None

        return value

    def peek(self):
        if self.front is None:
            raise IndexError("Queue is empty")

        return self.front.data

    def is_empty(self):
        return self.front is None


queue = LinkedListQueue()
queue.enqueue(100)
queue.enqueue(200)
queue.enqueue(300)

print(queue.dequeue())
print(queue.peek())
```

### Output

```text
100
200
```

Both enqueue and dequeue take O(1) time because we maintain front and rear references.

## 7. Circular Queue

A circular queue connects the last position of a fixed-size array back to the first position.

It reuses empty positions created by dequeuing elements.

For an array of capacity 4, the indices can wrap around:

```text
0 -> 1 -> 2 -> 3
^              |
|______________|
```

Circular queues are useful when a fixed-size buffer is needed.

## 8. Priority Queue

A priority queue removes elements according to priority rather than simply following FIFO order.

Python provides `heapq` for min-heap priority queues.

```python
import heapq

priority_queue = []

heapq.heappush(priority_queue, (2, "Write report"))
heapq.heappush(priority_queue, (1, "Handle urgent issue"))
heapq.heappush(priority_queue, (3, "Organize files"))

while priority_queue:
    priority, task = heapq.heappop(priority_queue)
    print(priority, task)
```

### Output

```text
1 Handle urgent issue
2 Write report
3 Organize files
```

Smaller numbers represent higher priority in this example.

A priority queue does not necessarily preserve insertion order. If equal priorities need FIFO ordering, include a sequence number in each heap entry.

## 9. Queue Complexity

| Operation | `deque` | Linked-list queue |
|---|---|---|
| Enqueue | O(1) | O(1) |
| Dequeue | O(1) | O(1) |
| Peek | O(1) | O(1) |
| Check empty | O(1) | O(1) |
| Search | O(n) | O(n) |

These complexities assume the standard implementations shown above.

## 10. Applications of Queues

1. CPU and process scheduling.
2. Breadth-first search (BFS).
3. Printer job management.
4. Network packet buffering.
5. Customer service systems.
6. Message queues and task processing.
7. Streaming data buffers.

## 11. Common Interview Questions

### Q1. What is FIFO?

First In, First Out: the earliest inserted element is removed first.

### Q2. What is the difference between a stack and a queue?

- Stack: LIFO.
- Queue: FIFO.

### Q3. Why is deque preferred over a list for a queue?

Removing from the front of a list takes O(n), while `deque.popleft()` takes O(1).

### Q4. What is a circular queue?

A fixed-capacity queue that wraps around to reuse available array positions.

### Q5. What is a priority queue?

A queue-like structure that selects elements according to priority.

### Q6. What is queue underflow?

Attempting to remove an element when the queue is empty.

## 12. Practice Problems

1. Implement a queue using two stacks.
2. Implement a stack using two queues.
3. Reverse the first K elements of a queue.
4. Generate binary numbers from 1 to N using a queue.
5. Implement a circular queue using a fixed-size list.
6. Implement a priority queue using `heapq`.
7. Solve a BFS traversal problem using a queue.

## 13. Key Takeaways

- A standard queue follows FIFO.
- Enqueue adds an element; dequeue removes the front element.
- Use `collections.deque` for ordinary Python queues.
- A linked-list queue can achieve O(1) enqueue and dequeue.
- Circular queues reuse fixed-size storage.
- Priority queues process elements by priority.
