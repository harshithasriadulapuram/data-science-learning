
# Merge K Sorted Lists in Python

## 1. What Is the K-Way Merge Problem?

K-way merge combines `k` already-sorted lists into one sorted list.

For example:

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

The challenge is to merge the lists efficiently without sorting all the elements again.

## 2. Approach 1: Concatenate and Sort

The simplest solution is to combine every list and sort the result.

```python
def merge_k_lists(lists):
    result = []

    for arr in lists:
        result.extend(arr)

    result.sort()
    return result


lists = [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
print(merge_k_lists(lists))
```

Output:

```text
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Complexity

Let `N` be the total number of elements.

- Time: `O(N log N)`
- Extra space: `O(N)` for the combined result, excluding implementation-dependent sorting space.

This approach is simple, but it does not fully use the fact that each input list is already sorted.

## 3. Approach 2: Merge Two Lists Repeatedly

We can merge two sorted lists at a time.

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


def merge_k_lists(lists):
    result = []

    for arr in lists:
        result = merge_two_sorted_lists(result, arr)

    return result


lists = [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
print(merge_k_lists(lists))
```

### Complexity

In the worst case, repeatedly merging the growing result can take `O(Nk)` time, where `k` is the number of lists.

A balanced pairwise merge strategy can improve this to `O(N log k)`.

## 4. Approach 3: Use a Min-Heap

A min-heap lets us repeatedly select the smallest available element among the lists.

### Core idea

1. Insert the first element of every non-empty list into a min-heap.
2. Remove the smallest element and add it to the result.
3. Insert the next element from the list that supplied the removed element.
4. Repeat until the heap is empty.

Python provides a min-heap through the `heapq` module.

### Implementation

```python
import heapq


def merge_k_lists(lists):
    heap = []

    # Add the first element of each non-empty list.
    for list_index, arr in enumerate(lists):
        if arr:
            heapq.heappush(
                heap,
                (arr[0], list_index, 0)
            )

    result = []

    while heap:
        value, list_index, element_index = heapq.heappop(heap)
        result.append(value)

        next_index = element_index + 1
        arr = lists[list_index]

        if next_index < len(arr):
            heapq.heappush(
                heap,
                (arr[next_index], list_index, next_index)
            )

    return result


lists = [
    [1, 4, 7],
    [2, 5, 8],
    [3, 6, 9]
]

print(merge_k_lists(lists))
```

Output:

```text
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Why store three values in the heap?

Each heap entry contains:

```python
(value, list_index, element_index)
```

- `value`: the number to compare.
- `list_index`: identifies the source list.
- `element_index`: identifies the element's position in that list.

The indices also prevent Python from needing to compare list objects when values are equal.

### Complexity

Let:

- `N` = total number of elements across all lists.
- `k` = number of input lists.

The heap contains at most `k` elements at a time.

- Time: `O(N log k)`
- Auxiliary space: `O(k)` for the heap.
- Output space: `O(N)` for the merged result.

This is usually the preferred approach when many sorted lists must be merged.

## 5. Why Does the Min-Heap Work?

Because every input list is sorted, the next unprocessed element in each list is the smallest remaining element from that list.

Therefore, the global smallest remaining element must be among the elements currently at the front of the lists.

The min-heap efficiently finds that smallest front element.

After removing it, we add the next element from the same list. This preserves the rule that the heap always represents the smallest remaining candidate from each list.

## 6. Merge K Sorted Lists Using Divide and Conquer

Another efficient approach merges lists in pairs.

```python
def merge_two_sorted_lists(a, b):
    i = j = 0
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


def merge_k_lists(lists):
    if not lists:
        return []

    while len(lists) > 1:
        merged_lists = []

        for i in range(0, len(lists), 2):
            first = lists[i]
            second = lists[i + 1] if i + 1 < len(lists) else []

            merged_lists.append(
                merge_two_sorted_lists(first, second)
            )

        lists = merged_lists

    return lists[0]


lists = [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
print(merge_k_lists(lists))
```

### Complexity

- Time: `O(N log k)`
- Auxiliary space: depends on the merging strategy and intermediate lists; this implementation allocates additional lists during each merge round.

The divide-and-conquer approach is useful when you want to practice both merging and recursion-style problem decomposition.

## 7. Heap vs. Divide and Conquer

| Feature | Min-Heap | Divide and Conquer |
|---|---|---|
| Time complexity | `O(N log k)` | `O(N log k)` |
| Main technique | Priority queue | Pairwise merging |
| Best for | Streaming the next smallest item | Combining sorted collections in rounds |
| Python tool | `heapq` | Two-pointer merge |

## 8. Common Mistakes

1. Forgetting that some input lists may be empty.
2. Adding every element to the heap instead of only the current candidate from each list.
3. Forgetting to insert the next element from the list after popping an element.
4. Using an incorrect list index when retrieving the next element.
5. Claiming `O(N log k)` time for an implementation that repeatedly merges the entire growing result from left to right.
6. Forgetting that the output list itself requires `O(N)` space.

## 9. Interview Practice Questions

Try solving these without looking at the implementation:

1. Merge two sorted arrays.
2. Merge `k` sorted arrays using a min-heap.
3. Merge `k` sorted linked lists.
4. Find the smallest range containing at least one element from each of `k` sorted lists.
5. Find the `k` smallest pairs from two sorted arrays.
6. Merge sorted files that are too large to fit into memory.
7. Compare heap-based merging with divide-and-conquer merging.

## 10. Interview Summary

Remember these points:

- K-way merge combines multiple sorted collections.
- A min-heap tracks the smallest available element from each collection.
- The heap-based algorithm takes `O(N log k)` time and `O(k)` auxiliary heap space.
- Two pointers are useful for merging two sorted lists.
- Divide and conquer can achieve `O(N log k)` time through balanced pairwise merging.
