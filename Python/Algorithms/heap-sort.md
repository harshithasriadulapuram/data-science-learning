# Heap Sort in Python

## 1. What Is Heap Sort?

**Heap sort** is a comparison-based sorting algorithm that uses a heap to arrange elements in order.

It commonly uses a **max-heap** to sort a list in ascending order.

A max-heap keeps the largest element at its root, allowing the algorithm to move that element to its correct position at the end of the list.

Heap sort is an in-place sorting algorithm in its standard iterative implementation.

## 2. How Heap Sort Works

Heap sort follows these steps:

1. Build a max-heap from the input list.
2. Swap the root (largest element) with the last element in the unsorted portion.
3. Reduce the size of the unsorted portion by one.
4. Restore the max-heap property.
5. Repeat until the unsorted portion contains only one element.

### Example

Consider this list:

```python
numbers = [12, 11, 13, 5, 6, 7]
```

After building a max-heap, one possible representation is:

```text
        13
       /  \
      11   12
     / \   /
    5   6 7
```

The largest element, `13`, is swapped with the last element.

The algorithm then restores the heap property for the remaining unsorted elements.

This process continues until the list is sorted:

```text
[5, 6, 7, 11, 12, 13]
```

## 3. Understanding Heapify

Heapify restores the heap property for a subtree.

For heap sort using a max-heap, the root must be greater than or equal to its children.

If a child is larger than the root, swap the root with the largest child and continue restoring the property down the tree.

For an array with zero-based indexing:

* Left child: `2 * i + 1`
* Right child: `2 * i + 2`

Heapify takes O(log n) time in the worst case because it may move down the height of the heap.

## 4. Python Implementation

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

    # Step 1: Build a max-heap
    for i in range(n // 2 - 1, -1, -1):
        heapify_down(arr, n, i)

    # Step 2: Move the maximum to the end repeatedly
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify_down(arr, i, 0)

    return arr


numbers = [12, 11, 13, 5, 6, 7]

print(heap_sort(numbers))
# Output: [5, 6, 7, 11, 12, 13]
```

### How the Code Works

**Step 1: Build a max-heap**

```python
for i in range(n // 2 - 1, -1, -1):
    heapify_down(arr, n, i)
```

Start at the last non-leaf node and work backward to the root. Leaf nodes already satisfy the heap property.

Building the heap takes O(n) time.

**Step 2: Move the maximum to the end**

```python
for i in range(n - 1, 0, -1):
    arr[0], arr[i] = arr[i], arr[0]
    heapify_down(arr, i, 0)
```

The root contains the largest remaining element. Swap it with the last element in the unsorted portion, then restore the max-heap property in the reduced portion.

Repeat until the list is sorted.

## 5. Time and Space Complexity

| Case or Operation                         | Complexity |
| ----------------------------------------- | ---------- |
| Build the max-heap                        | O(n)       |
| Best-case time                            | O(n log n) |
| Average-case time                         | O(n log n) |
| Worst-case time                           | O(n log n) |
| Auxiliary space, iterative implementation | O(1)       |

The recursive `heapify_down()` implementation above uses additional call-stack space of O(log n) in the worst case. An iterative heapify implementation can achieve O(1) auxiliary space for the standard in-place algorithm.

## 6. Advantages of Heap Sort

* Guaranteed O(n log n) worst-case running time.
* In-place sorting in its standard iterative implementation.
* Does not require a separate auxiliary array.
* Performs consistently across different input arrangements.

## 7. Disadvantages of Heap Sort

* It is not a stable sorting algorithm.
* Its memory-access pattern can be less cache-friendly than some other sorting algorithms.
* It may be slower in practice than highly optimized quicksort or Timsort implementations for some workloads.

## 8. Heap Sort vs Other Sorting Algorithms

| Algorithm      | Best Case  | Average Case | Worst Case | Stable?                          |
| -------------- | ---------- | ------------ | ---------- | -------------------------------- |
| Heap sort      | O(n log n) | O(n log n)   | O(n log n) | No                               |
| Merge sort     | O(n log n) | O(n log n)   | O(n log n) | Yes, in standard implementations |
| Quicksort      | O(n log n) | O(n log n)   | O(n²)      | Usually no                       |
| Insertion sort | O(n)       | O(n²)        | O(n²)      | Yes                              |

These are standard time complexities; actual performance depends on the implementation and input.

## 9. Practice Problems

Try solving these without looking at the implementation:

1. Implement heap sort for a list of integers.
2. Sort a list containing duplicate values.
3. Sort a list that is already in ascending order.
4. Sort a list in descending order.
5. Trace each heapify operation by hand.
6. Explain why building a heap takes O(n), rather than O(n log n).
7. Compare heap sort with merge sort and quicksort.

## Key Takeaways

* Heap sort is a comparison-based sorting algorithm.
* A max-heap can be used to sort a list in ascending order.
* Building a heap takes O(n) time.
* Heap sort takes O(n log n) time in the best, average, and worst cases.
* Standard iterative heap sort uses O(1) auxiliary space.
* Heap sort is not stable.
* The heap data structure and heap sort algorithm are related but distinct topics.
