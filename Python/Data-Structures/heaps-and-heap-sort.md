
# Heaps and Heap Sort in Python

## 1. What Is a Heap?

A **heap** is a specialized tree-based data structure that satisfies the heap property.

A binary heap is a complete binary tree, usually implemented using a list or array.

There are two main types:

- **Min-heap:** Every parent is less than or equal to its children.
- **Max-heap:** Every parent is greater than or equal to its children.

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

- Implementing priority queues.
- Finding the smallest or largest elements quickly.
- Scheduling tasks by priority.
- Finding the top K elements.
- Implementing heap sort.
- Graph algorithms such as Dijkstra's algorithm and Prim's algorithm.

## 3. Representing a Heap Using an Array

A binary heap can be represented using a list without explicitly creating tree nodes.

For a zero-indexed array, if a node is at index `i`:

- Left child: `2 * i + 1`
- Right child: `2 * i + 2`
- Parent: `(i - 1) // 2`, for `i > 0`

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

Python provides the `heapq` module for heap operations.

It implements a **min-heap** by default.

```python
import heapq

numbers = [10, 4, 15, 2, 8]

heapq.heapify(numbers)

print(numbers)
print(numbers[0])  # Smallest element
```

`heapify()` transforms a list into a heap in O(n) time.

Do not assume that the entire list becomes sorted. Only the heap property is guaranteed.

## 5. Inserting an Element

Use `heappush()` to insert an element while preserving the heap property.

```python
import heapq

heap = [2, 5, 8, 10, 12]

heapq.heappush(heap, 3)

print(heap)
print(heap[0])  # 2
```

Time complexity: O(log n).

## 6. Removing the Smallest Element

Use `heappop()` to remove and return the smallest element.

```python
import heapq

heap = [2, 5, 8, 10, 12]

smallest = heapq.heappop(heap)

print(smallest)  # 2
print(heap)
```

Time complexity: O(log n).

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

These functions return sorted results.

## 8. Implementing a Max-Heap

Python's `heapq` is a min-heap. A common technique is to store negative values to simulate a max-heap.

```python
import heapq

numbers = [10, 40, 20, 5, 30]

max_heap = [-number for number in numbers]
heapq.heapify(max_heap)

while max_heap:
    print(-heapq.heappop(max_heap), end=" ")

# Output: 40 30 20 10 5
```

The negative of the largest original value becomes the smallest stored value.

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

Smaller priority numbers are processed first.

When priorities tie, tuples compare their next elements. If tasks are not mutually comparable, include a unique sequence number as a tie-breaker.

## 10. What Is Heap Sort?

**Heap sort** is a comparison-based sorting algorithm that uses a heap to sort elements.

The general process is:

1. Build a max-heap.
2. Swap the largest element with the last element in the unsorted portion.
3. Reduce the unsorted portion.
4. Restore the max-heap property.
5. Repeat until sorted.

Heap sort runs in O(n log n) time in the best, average, and worst cases.

## 11. Implementing Heap Sort

The following implementation sorts a list in ascending order using a max-heap.

```python
def heapify_down(arr, size, root):
    largest = root
    left = 2 * root + 1
    right = 2 * root + 2

    if left < size and arr[left] > arr[largest]:
        largest = left

    if right < size and arr[right] > arr[largest]:
        largest = right

    if largest != root:
        arr[root], arr[largest] = arr[largest], arr[root]
        heapify_down(arr, size, largest)


def heap_sort(arr):
    n = len(arr)

    # Build a max-heap
    for i in range(n // 2 - 1, -1, -1):
        heapify_down(arr, n, i)

    # Move the maximum to the end repeatedly
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify_down(arr, i, 0)

    return arr


numbers = [12, 11, 13, 5, 6, 7]
print(heap_sort(numbers))
# [5, 6, 7, 11, 12, 13]
```

The algorithm sorts the list in place.

## 12. Heap Sort Complexity

| Operation | Time Complexity |
|---|---|
| Build a heap | O(n) |
| Insert into a heap | O(log n) |
| Remove the root | O(log n) |
| Find the minimum in a min-heap | O(1) |
| Heap sort | O(n log n) |

Heap sort uses O(1) auxiliary space in its iterative, in-place form. The recursive implementation above uses additional call-stack space during heap restoration.

## 13. Heap vs Binary Search Tree

| Feature | Heap | Binary Search Tree |
|---|---|---|
| Main property | Parent-child priority ordering | Left-right value ordering |
| Root | Minimum or maximum | Depends on inserted values |
| Find minimum in min-heap | O(1) | O(log n) when balanced |
| Search for arbitrary value | O(n) in general | O(log n) when balanced |
| Insert | O(log n) | O(log n) when balanced |
| Typical use | Priority queues | Ordered searching |

A heap does not keep all its elements sorted.

## 14. Practice Problems

Try solving these without looking at the examples:

1. Convert a list into a min-heap.
2. Insert an element into a heap.
3. Remove the smallest element.
4. Find the three largest elements in a list.
5. Implement a max-heap using negative values.
6. Sort a list using heap sort.
7. Find the Kth largest element in a list.
8. Merge K sorted lists.
9. Implement a task scheduler using a priority queue.
10. Find the median of a stream of numbers using two heaps.

## Key Takeaways

- A binary heap is a complete binary tree.
- A min-heap keeps the smallest value at its root.
- A max-heap keeps the largest value at its root.
- Python's `heapq` implements a min-heap.
- Heap insertion and root removal take O(log n) time.
- Heap sort takes O(n log n) time.
- Heaps are especially useful for priority queues and top-K problems.
