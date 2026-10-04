
# Circular Queue in Python

## 1. What Is a Circular Queue?

A circular queue is a queue in which the last position connects back to the first position.

It follows FIFO (First In, First Out), just like a normal queue, but it reuses empty spaces in a fixed-size array.

### Why Do We Need It?

Consider a normal array-based queue of capacity 5.

After inserting five elements and removing the first three, the first three positions become empty. A simple linear implementation might not reuse them without shifting elements.

A circular queue solves this problem by wrapping around to the beginning of the array.

## 2. Important Concepts

- `front`: Index of the first element.
- `rear`: Index of the last element.
- `capacity`: Maximum number of elements.
- `size`: Current number of elements.
- `enqueue`: Insert an element.
- `dequeue`: Remove an element.

The next index is calculated using:

```python
next_index = (current_index + 1) % capacity
```

The modulo operator `%` wraps the index back to zero when it reaches the capacity.

For example, with capacity 5:

```text
(0 + 1) % 5 = 1
(3 + 1) % 5 = 4
(4 + 1) % 5 = 0
```

## 3. Implementing a Circular Queue

```python
class CircularQueue:
    def __init__(self, capacity):
        if capacity <= 0:
            raise ValueError("Capacity must be positive")

        self.capacity = capacity
        self.items = [None] * capacity
        self.front = 0
        self.rear = -1
        self.size = 0

    def is_empty(self):
        return self.size == 0

    def is_full(self):
        return self.size == self.capacity

    def enqueue(self, item):
        if self.is_full():
            raise OverflowError("Circular queue is full")

        self.rear = (self.rear + 1) % self.capacity
        self.items[self.rear] = item
        self.size += 1

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Circular queue is empty")

        item = self.items[self.front]
        self.items[self.front] = None
        self.front = (self.front + 1) % self.capacity
        self.size -= 1
        return item

    def peek(self):
        if self.is_empty():
            raise IndexError("Circular queue is empty")

        return self.items[self.front]

    def display(self):
        elements = []

        for i in range(self.size):
            index = (self.front + i) % self.capacity
            elements.append(self.items[index])

        print(elements)

    def get_size(self):
        return self.size


queue = CircularQueue(5)

queue.enqueue(10)
queue.enqueue(20)
queue.enqueue(30)
queue.enqueue(40)

queue.display()

print("Removed:", queue.dequeue())
print("Removed:", queue.dequeue())

queue.enqueue(50)
queue.enqueue(60)
queue.enqueue(70)

queue.display()
print("Front:", queue.peek())
print("Size:", queue.get_size())
```

## 4. Expected Output

```text
[10, 20, 30, 40]
Removed: 10
Removed: 20
[30, 40, 50, 60, 70]
Front: 30
Size: 5
```

## 5. Understanding Enqueue

The enqueue operation inserts an element at the rear.

```python
self.rear = (self.rear + 1) % self.capacity
self.items[self.rear] = item
self.size += 1
```

Steps:

1. Check whether the queue is full.
2. Calculate the next rear index.
3. Store the new element.
4. Increase the size.

Time complexity: O(1).

## 6. Understanding Dequeue

The dequeue operation removes the element at the front.

```python
item = self.items[self.front]
self.items[self.front] = None
self.front = (self.front + 1) % self.capacity
self.size -= 1
return item
```

Steps:

1. Check whether the queue is empty.
2. Save the front element.
3. Clear its array position.
4. Advance the front index using modulo.
5. Decrease the size.
6. Return the removed element.

Time complexity: O(1).

## 7. Overflow and Underflow

### Queue Overflow

Overflow occurs when an insertion is attempted while the queue is full.

```python
if self.is_full():
    raise OverflowError("Circular queue is full")
```

### Queue Underflow

Underflow occurs when removal is attempted while the queue is empty.

```python
if self.is_empty():
    raise IndexError("Circular queue is empty")
```

Both conditions must be handled to prevent invalid operations.

## 8. Time Complexity

| Operation | Time Complexity |
|---|---|
| Enqueue | O(1) |
| Dequeue | O(1) |
| Peek | O(1) |
| Check empty | O(1) |
| Check full | O(1) |
| Display all elements | O(n) |

Space complexity: O(n), where n is the queue capacity.

## 9. Practice Problems

1. Implement a circular queue without using `collections.deque`.
2. Add a method to return the front and rear indices.
3. Add a method to clear the queue.
4. Handle a queue with capacity 1.
5. Write tests for full and empty queue conditions.
6. Trace the front and rear indices after several enqueue and dequeue operations.

## 10. Key Takeaways

- A circular queue follows FIFO.
- Modulo arithmetic allows indices to wrap around.
- A fixed-size array can reuse positions freed by dequeue.
- Enqueue and dequeue take O(1) time.
- Tracking `size` makes full and empty conditions easy to distinguish.
- Always handle overflow and underflow.
