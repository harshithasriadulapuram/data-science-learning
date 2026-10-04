# K-Way Merge Interview Practice in Python

## 1. What Is K-Way Merge?

K-way merge combines K sorted sequences into one sorted sequence.

Instead of comparing every element across all sequences, we use a **min-heap** to track the smallest available element from each sequence.

### Example

Input:

```python
lists = [
    [1, 4, 7],
    [2, 5, 8],
    [3, 6, 9]
]
```

Output:

```python
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## 2. How the Algorithm Works

1. Insert the first element of each non-empty list into a min-heap.
2. Remove the smallest element from the heap.
3. Add it to the result.
4. Insert the next element from the same list, if one exists.
5. Repeat until the heap is empty.

Each heap entry stores the value, the source list index, and the element index.

## 3. Implementation

```python
import heapq


def merge_k_sorted_lists(lists):
    heap = []

    for list_index, values in enumerate(lists):
        if values:
            heapq.heappush(
                heap,
                (values[0], list_index, 0)
            )

    result = []

    while heap:
        value, list_index, element_index = heapq.heappop(heap)
        result.append(value)

        next_index = element_index + 1
        values = lists[list_index]

        if next_index < len(values):
            heapq.heappush(
                heap,
                (values[next_index], list_index, next_index)
            )

    return result


data = [
    [1, 4, 7],
    [2, 5, 8],
    [3, 6, 9]
]

print(merge_k_sorted_lists(data))
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## 4. Why Store the List Index?

Heap entries are tuples. Python compares tuple elements in order.

The list index identifies which sequence supplied the current value. The element index tells us which element to insert next.

These extra fields also allow duplicate values without comparing the list objects themselves.

## 5. Complexity Analysis

Let:
- K = number of input lists
- N = total number of elements across all lists

Time complexity: **O(N log K)**, assuming K is greater than 1.

Auxiliary heap space: **O(K)**.

The output requires an additional O(N) space.

## 6. Merge Two Sorted Lists

A simpler alternative is merging two sorted lists using two pointers.

```python
def merge_two_sorted_lists(a, b):
    i = 0
    j = 0
    result = []

    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            result.append(a[i])
            i += 1
        else:
            result.append(b[j])
            j += 1

    result.extend(a[i:])
    result.extend(b[j:])

    return result


print(merge_two_sorted_lists([1, 4, 7], [2, 3, 8]))
# [1, 2, 3, 4, 7, 8]
```

Time complexity: O(n + m).

## 7. Common Interview Problems

1. Merge K sorted arrays.
2. Merge K sorted linked lists.
3. Find the Kth smallest element across sorted arrays.
4. Find the smallest range containing at least one element from each sorted list.
5. Merge multiple sorted streams.
6. Find the median of two sorted arrays.

## 8. When Should You Use K-Way Merge?

Use this pattern when:
- Several sequences are already sorted.
- You need a single sorted output.
- Sorting all N elements again would be unnecessary.
- You want to process sorted data streams efficiently.

## 9. Practice Checklist

- [ ] Merge two sorted lists.
- [ ] Merge K sorted lists using a heap.
- [ ] Handle empty input lists.
- [ ] Handle duplicate values.
- [ ] Explain O(N log K) time complexity.
- [ ] Explain why the heap contains at most K entries.
