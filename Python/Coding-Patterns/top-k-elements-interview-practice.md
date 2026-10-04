
# Top K Elements — Interview Practice

## 1. What Is the Top K Pattern?

The Top K pattern is used when a problem asks for the largest, smallest, most frequent, or least frequent k elements.

Examples:

- Find the k largest numbers.
- Find the k smallest numbers.
- Find the k most frequent elements.
- Find the kth largest element.
- Find the k closest points to the origin.
- Find the k most frequent words.

A heap is often useful because it allows us to keep track of a limited number of candidates without sorting the entire input.

## 2. Python Heap Basics

Python's `heapq` module implements a min-heap.

In a min-heap, the smallest element is at index zero.

```python
import heapq

numbers = [7, 2, 9, 1, 5]

heapq.heapify(numbers)

print(numbers[0])  # 1

smallest = heapq.heappop(numbers)
print(smallest)    # 1

heapq.heappush(numbers, 3)
```

Complexities:

- `heapify()`: O(n).
- `heappush()`: O(log n).
- `heappop()`: O(log n).
- Accessing the minimum at index zero: O(1).

## 3. Find the K Largest Elements

Maintain a min-heap containing at most k elements.

Whenever the heap grows beyond k elements, remove its smallest element. At the end, the heap contains the k largest values.

```python
import heapq


def k_largest(numbers, k):
    if k < 0 or k > len(numbers):
        raise ValueError("k must be between 0 and len(numbers)")

    if k == 0:
        return []

    heap = []

    for number in numbers:
        heapq.heappush(heap, number)

        if len(heap) > k:
            heapq.heappop(heap)

    return sorted(heap, reverse=True)


print(k_largest([3, 1, 5, 12, 2, 11], 3))
# [12, 11, 5]
```

**Time complexity:** O(n log k + k log k).

**Auxiliary space complexity:** O(k).

The heap never stores more than k elements. Sorting the final heap produces descending output.

For k equal to n, this approach has O(n log n) time complexity.

## 4. Find the K Smallest Elements

Maintain a max-heap of size k. Python provides a min-heap, so store negative values to simulate a max-heap.

```python
import heapq


def k_smallest(numbers, k):
    if k < 0 or k > len(numbers):
        raise ValueError("k must be between 0 and len(numbers)")

    if k == 0:
        return []

    heap = []

    for number in numbers:
        heapq.heappush(heap, -number)

        if len(heap) > k:
            heapq.heappop(heap)

    return sorted(-number for number in heap)


print(k_smallest([3, 1, 5, 12, 2, 11], 3))
# [1, 2, 3]
```

**Time complexity:** O(n log k + k log k).

**Auxiliary space complexity:** O(k).

The negative values make the largest original value the smallest heap entry.

## 5. Find the Kth Largest Element

The kth largest element is the element that would appear at position k if the list were sorted in descending order.

Duplicates count as separate elements.

```python
import heapq


def kth_largest(numbers, k):
    if not 1 <= k <= len(numbers):
        raise ValueError("k must be between 1 and len(numbers)")

    heap = []

    for number in numbers:
        heapq.heappush(heap, number)

        if len(heap) > k:
            heapq.heappop(heap)

    return heap[0]


print(kth_largest([3, 2, 1, 5, 6, 4], 2))
# 5
```

**Time complexity:** O(n log k).

**Auxiliary space complexity:** O(k).

The smallest value remaining in the size-k min-heap is the kth largest value in the input.

## 6. Find the K Most Frequent Elements

First count each element's frequency. Then use a heap to select the k most frequent values.

```python
from collections import Counter
import heapq


def top_k_frequent(numbers, k):
    if not 0 <= k <= len(set(numbers)):
        raise ValueError("k must be between 0 and the number of unique values")

    frequencies = Counter(numbers)

    return [
        number
        for number, frequency in heapq.nlargest(
            k,
            frequencies.items(),
            key=lambda item: item[1],
        )
    ]


print(top_k_frequent([1, 1, 1, 2, 2, 3], 2))
# [1, 2]
```

Let u be the number of unique elements.

**Time complexity:** O(n + u log k) for heap selection, with implementation-dependent details and output handling.

**Auxiliary space complexity:** O(u) for the frequency map and selection.

If several values have the same frequency, their relative order is not guaranteed by this implementation. If deterministic tie-breaking is required, include a secondary sorting key.

## 7. Sort Characters by Frequency

```python
from collections import Counter
import heapq


def frequency_sort(text):
    frequencies = Counter(text)

    ordered = heapq.nlargest(
        len(frequencies),
        frequencies.items(),
        key=lambda item: item[1],
    )

    return "".join(
        character * frequency
        for character, frequency in ordered
    )


print(frequency_sort("tree"))
# "eetr" or "eert"
```

Characters with equal frequencies may appear in different orders.

Let u be the number of unique characters and n the string length.

**Time complexity:** O(n + u log u).

**Auxiliary space complexity:** O(n + u), including the output string and frequency data.

## 8. K Closest Points to the Origin

The Euclidean distance from the origin is:

\[
d = \sqrt{x^2 + y^2}
\]

To compare distances, we can compare squared distances instead:

\[
d^2 = x^2 + y^2
\]

The square root is unnecessary because it does not change the ordering of non-negative distances.

```python
import heapq


def k_closest(points, k):
    if not 0 <= k <= len(points):
        raise ValueError("k must be between 0 and len(points)")

    return heapq.nsmallest(
        k,
        points,
        key=lambda point: point[0] ** 2 + point[1] ** 2,
    )


points = [[1, 3], [-2, 2], [5, 8], [0, 1]]

print(k_closest(points, 2))
# [[0, 1], [-2, 2]]
```

**Time complexity:** O(n log k) for heap selection, with O(n log n) worst-case behavior possible depending on k and implementation details.

**Auxiliary space complexity:** O(k) for the selected results and heap-based selection, excluding input storage.

## 9. Merge K Sorted Lists

Given k sorted lists, repeatedly select the smallest available element using a min-heap.

```python
import heapq


def merge_k_sorted_lists(lists):
    heap = []

    for list_index, values in enumerate(lists):
        if values:
            heapq.heappush(
                heap,
                (values[0], list_index, 0),
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
                (values[next_index], list_index, next_index),
            )

    return result


print(
    merge_k_sorted_lists([
        [1, 4, 7],
        [2, 5, 8],
        [3, 6, 9],
    ])
)
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Let N be the total number of elements and k the number of lists.

**Time complexity:** O(N log k) when k is greater than one.

**Auxiliary space complexity:** O(k) for the heap, excluding the returned output list.

The heap contains at most one current element from each non-empty list.

The list index is included in each heap entry to break ties between equal values without comparing the original lists.

## 10. Choosing Between Sorting and a Heap

| Requirement | Suitable approach |
|---|---|
| Need every element in sorted order | Sorting |
| Need only the k largest elements | Size-k min-heap |
| Need only the k smallest elements | Size-k max-heap |
| Need the kth largest element | Size-k min-heap or quickselect |
| Need repeated minimum extraction | Min-heap |
| Need repeated maximum extraction | Max-heap or negative values in `heapq` |
| Need frequent updates to priorities | Consider a heap or another suitable priority structure |

A heap does not keep every element globally sorted. It only guarantees the heap property.

## 11. Common Interview Mistakes

1. Using a max-heap when a min-heap of size k is sufficient.
2. Forgetting to validate k.
3. Confusing kth largest with the kth distinct largest.
4. Sorting the entire input when only a few elements are needed.
5. Assuming heap iteration returns sorted values.
6. Forgetting that duplicate values count separately unless the problem says otherwise.
7. Using square roots when comparing squared distances is sufficient.
8. Ignoring tie-breaking requirements for equally frequent values.
9. Forgetting the memory cost of a frequency map.
10. Claiming O(log k) time for processing the entire input without accounting for all n elements.

## 12. Practice Problems

Implement these independently:

1. Find the kth smallest element.
2. Find the k largest elements in descending order.
3. Find the k most frequent numbers.
4. Find the k most frequent words with lexicographic tie-breaking.
5. Find the k closest points to the origin.
6. Merge k sorted arrays.
7. Find the kth largest element in a stream.
8. Find the top k products by sales.
9. Find the k most common words in a document.
10. Compare heap-based selection with sorting and quickselect.

## 13. Final Checklist

- [ ] Understand Python's `heapq` min-heap.
- [ ] Find the k largest and smallest elements.
- [ ] Find the kth largest element.
- [ ] Count frequencies with `Counter`.
- [ ] Use a heap to merge sorted sequences.
- [ ] Explain time and auxiliary space complexity.
- [ ] Handle duplicates and ties correctly.
- [ ] Choose between sorting, heaps, and quickselect.
