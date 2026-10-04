# Heaps in Python

## 1. What Is a Heap?

A **heap** is a specialized tree-based data structure that satisfies a specific heap property.

A binary heap is a complete binary tree, usually implemented using a list or array.

A complete binary tree has every level completely filled except possibly the last, which is filled from left to right.

There are two main types of binary heaps:

* **Min-heap:** Every parent is less than or equal to its children.
* **Max-heap:** Every parent is greater than or equal to its children.

### Min-Heap Example

```text
        2
       / \
      5   8
     / \
    10  12
```

The smallest element is at the root.

### Max-Heap Example

```text
        20
       /  \
      15   10
     / \
    8   5
```

The largest element is at the root.

## 2. Why Do We Use Heaps?

Heaps are useful for:

* Implementing priority queues.
* Finding the smallest or largest elements quickly.
* Scheduling tasks according to priority.
* Finding the top K elements.
* Processing streaming data.
* Supporting graph algorithms such as Dijkstra's and Prim's algorithms.

## 3. Representing a Heap Using an Array

A binary heap can be represented using a Python list without explicitly creating tree nodes.

For a zero-indexed array, if a node is at index `i`:

* Left child: `2 * i + 1`
* Right child: `2 * i + 2`
* Parent: `(i - 1) // 2`, when `i > 0`

Example:

```python
heap = [2, 5, 8, 10, 12]
```

This represents:

```text
        2
       / \
      5   8
     / \
    10  12
```

The array representation is compact and efficient.

## 4. Python's heapq Module

Python provides the built-in `heapq` module for heap operations.

It implements a **min-heap** by default.

```python
import heapq

numbers = [10, 4, 15, 2, 8]

heapq.heapify(numbers)

print(numbers)
print(numbers[0])  # Smallest element: 2
```

`heapify()` transforms a list into a heap in O(n) time.

**Important:** The list is not necessarily sorted. Only the heap property is guaranteed.

## 5. Inserting an Element

Use `heappush()` to insert an element while preserving the heap property.

```python
import heapq

heap = [2, 5, 8, 10, 12]

heapq.heappush(heap, 3)

print(heap)
print(heap[0])  # 2
```

Time complexity: **O(log n)**.

## 6. Removing the Smallest Element

Use `heappop()` to remove and return the smallest element.

```python
import heapq

heap = [2, 5, 8, 10, 12]

smallest = heapq.heappop(heap)

print(smallest)  # 2
print(heap)
```

Time complexity: **O(log n)**.

Repeatedly popping from a min-heap returns elements in ascending order.

```python
import heapq

numbers = [9, 3, 7, 1, 5]
heapq.heapify(numbers)

while numbers:
    print(heapq.heappop(numbers), end=" ")

# Output: 1 3 5 7 9
```

## 7. Finding the Smallest and Largest Elements

Use `nsmallest()` and `nlargest()` when you need a specific number of extreme values.

```python
import heapq

numbers = [12, 5, 8, 20, 3, 15]

print(heapq.nsmallest(3, numbers))
# [3, 5, 8]

print(heapq.nlargest(3, numbers))
# [20, 15, 12]
```

Both functions return sorted results.

These functions are convenient for top-K problems. Their performance depends on the number of requested elements and the input size.

## 8. Implementing a Max-Heap

Python's `heapq` is a min-heap. A common technique for numeric values is to store negative numbers to simulate a max-heap.

```python
import heapq

numbers = [10, 40, 20, 5, 30]

max_heap = [-number for number in numbers]
heapq.heapify(max_heap)

while max_heap:
    print(-heapq.heappop(max_heap), end=" ")

# Output: 40 30 20 10 5
```

The largest original value becomes the smallest negative value, so it is removed first.

This technique works directly for numeric values.

## 9. Building a Priority Queue

A heap can process tasks according to priority.

```python
import heapq

tasks = []

heapq.heappush(tasks, (2, "Write report"))
heapq.heappush(tasks, (1, "Fix critical bug"))
heapq.heappush(tasks, (3, "Reply to emails"))

while tasks:
    priority, task = heapq.heappop(tasks)
    print(priority, task)
```

Output:

```text
1 Fix critical bug
2 Write report
3 Reply to emails
```

Smaller priority numbers are processed first.

When priorities tie, Python compares the next tuple elements. If task objects are not mutually comparable, include a unique sequence number as a tie-breaker.

## 10. Heap vs Binary Search Tree

| Feature                    | Heap                           | Binary Search Tree         |
| -------------------------- | ------------------------------ | -------------------------- |
| Main property              | Parent-child priority ordering | Left-right value ordering  |
| Root                       | Minimum or maximum             | Depends on inserted values |
| Find minimum in min-heap   | O(1)                           | O(log n) when balanced     |
| Search for arbitrary value | O(n) in general                | O(log n) when balanced     |
| Insert                     | O(log n)                       | O(log n) when balanced     |
| Typical use                | Priority queues                | Ordered searching          |

A heap does not keep all its elements sorted.

The binary search tree complexities shown here assume a balanced tree. An ordinary, unbalanced binary search tree can have O(n) operations in the worst case.

## 11. Heap Operation Complexities

| Operation                      | Time Complexity |
| ------------------------------ | --------------- |
| Build a heap using `heapify()` | O(n)            |
| Insert an element              | O(log n)        |
| Remove the root                | O(log n)        |
| Find the root                  | O(1)            |
| Search for an arbitrary value  | O(n) in general |

## 12. Practice Problems

Try solving these without looking at the examples:

1. Convert a list into a min-heap.
2. Insert an element into a heap.
3. Remove the smallest element.
4. Find the three largest elements in a list.
5. Implement a max-heap using negative values.
6. Find the Kth largest element in a list.
7. Merge K sorted lists using a heap.
8. Implement a task scheduler using a priority queue.
9. Find the median of a stream of numbers using two heaps.

## Key Takeaways

* A binary heap is a complete binary tree.
* A min-heap keeps the smallest value at its root.
* A max-heap keeps the largest value at its root.
* Python's `heapq` implements a min-heap.
* Insertion and root removal take O(log n) time.
* Reading the root takes O(1) time.
* Heaps are especially useful for priority queues and top-K problems.
* Heap sort is a separate sorting algorithm that uses a heap.

