
# Priority Queue in Python

## 1. What Is a Priority Queue?

A priority queue is a data structure in which elements are removed according to their priority rather than simply their insertion order.

Python provides the `heapq` module to implement a priority queue efficiently.

By default, `heapq` implements a min-heap, so the smallest value is removed first.

## 2. Basic Example

```python
import heapq

pq = []

heapq.heappush(pq, 30)
heapq.heappush(pq, 10)
heapq.heappush(pq, 20)

print(heapq.heappop(pq))  # 10
print(heapq.heappop(pq))  # 20
print(heapq.heappop(pq))  # 30
```

Output:

```text
10
20
30
```

## 3. Priority Queue with Tasks

We can store a priority and a task together as a tuple.

```python
import heapq

pq = []

heapq.heappush(pq, (2, "Write report"))
heapq.heappush(pq, (1, "Fix critical bug"))
heapq.heappush(pq, (3, "Organize files"))

while pq:
    priority, task = heapq.heappop(pq)
    print(priority, task)
```

Output:

```text
1 Fix critical bug
2 Write report
3 Organize files
```

Smaller numbers represent higher priority in this example.

## 4. Handling Equal Priorities

When priorities are equal, tuples compare their next elements. If tasks are not comparable, this may raise a `TypeError`.

Use a sequence number to preserve insertion order for equal priorities.

```python
import heapq
from itertools import count

pq = []
sequence = count()

def add_task(priority, task):
    heapq.heappush(pq, (priority, next(sequence), task))

add_task(1, "First task")
add_task(1, "Second task")
add_task(2, "Third task")

while pq:
    priority, _, task = heapq.heappop(pq)
    print(priority, task)
```

Output:

```text
1 First task
1 Second task
2 Third task
```

## 5. Implementing a Priority Queue Using a Class

```python
import heapq
from itertools import count


class PriorityQueue:
    def __init__(self):
        self.items = []
        self.sequence = count()

    def enqueue(self, priority, item):
        entry = (priority, next(self.sequence), item)
        heapq.heappush(self.items, entry)

    def dequeue(self):
        if not self.items:
            raise IndexError("Priority queue is empty")

        priority, _, item = heapq.heappop(self.items)
        return priority, item

    def peek(self):
        if not self.items:
            raise IndexError("Priority queue is empty")

        priority, _, item = self.items[0]
        return priority, item

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)


pq = PriorityQueue()

pq.enqueue(2, "Write report")
pq.enqueue(1, "Fix critical bug")
pq.enqueue(3, "Organize files")

print("Next:", pq.peek())
print("Removed:", pq.dequeue())
print("Next:", pq.peek())
```

Output:

```text
Next: (1, 'Fix critical bug')
Removed: (1, 'Fix critical bug')
Next: (2, 'Write report')
```

## 6. Implementing a Max-Priority Queue

A max-priority queue removes the largest numeric value first.

For numeric priorities, negate the priority when inserting into a min-heap.

```python
import heapq

pq = []

heapq.heappush(pq, (-10, "Task A"))
heapq.heappush(pq, (-30, "Task B"))
heapq.heappush(pq, (-20, "Task C"))

while pq:
    negative_priority, task = heapq.heappop(pq)
    print(-negative_priority, task)
```

Output:

```text
30 Task B
20 Task C
10 Task A
```

## 7. Time Complexity

| Operation | Time Complexity |
|---|---|
| Insert with heappush | O(log n) |
| Remove with heappop | O(log n) |
| View the next element | O(1) |
| Build a heap with heapify | O(n) |
| Search for an arbitrary item | O(n) |

Here, n is the number of elements in the heap.

## 8. Applications

1. CPU scheduling.
2. Emergency-room triage systems.
3. Dijkstra's shortest-path algorithm.
4. A* search.
5. Event simulation.
6. Task scheduling.
7. Finding the smallest or largest K elements.

## 9. Common Interview Questions

### Q1. What is the difference between a normal queue and a priority queue?

A normal queue follows FIFO. A priority queue removes elements according to priority.

### Q2. What is a heap?

A heap is a complete binary tree that satisfies the heap property. In a min-heap, each parent is no larger than its children.

### Q3. What is the difference between a min-heap and a max-heap?

A min-heap exposes the smallest element; a max-heap exposes the largest element.

### Q4. Does a priority queue sort all its elements?

No. A heap guarantees the heap property, not that the entire internal list is sorted.

## 10. Practice Problems

1. Find the K largest elements in a list.
2. Merge K sorted lists.
3. Implement a task scheduler.
4. Find the Kth smallest element.
5. Use a priority queue to simulate hospital triage.
6. Solve a shortest-path problem using Dijkstra's algorithm.

## 11. Key Takeaways

- Python's `heapq` provides a min-heap.
- Smaller priority values are removed first in our examples.
- Use a sequence number to break ties safely.
- Insertions and removals take O(log n).
- Viewing the next element takes O(1).
