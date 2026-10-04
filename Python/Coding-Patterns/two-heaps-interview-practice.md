
# Two Heaps — Interview Practice

## 1. What Is the Two Heaps Pattern?

The Two Heaps pattern uses two heaps to maintain two groups of elements efficiently.

In Python, `heapq` provides a min-heap.

- **Min-heap:** The smallest element is at the top.
- **Max-heap:** The largest element is at the top. Python can simulate a max-heap using negative values.

The Two Heaps pattern is useful when a problem requires finding a median, balancing two groups, or tracking elements on either side of a boundary.

## 2. When Should You Use Two Heaps?

Look for problems involving:

- Running median.
- Median of a data stream.
- Splitting numbers into smaller and larger halves.
- Scheduling tasks based on priorities.
- Maintaining a boundary between two groups.
- Finding the closest or most relevant elements in changing data.

---

## 3. Find the Median from a Data Stream

The median is the middle value in a sorted collection.

- For an odd number of elements, it is the middle element.
- For an even number of elements, it is the average of the two middle elements.

### Core Idea

Maintain two heaps:

1. `lower`: a max-heap containing the smaller half.
2. `upper`: a min-heap containing the larger half.

Maintain these conditions:

- Every value in `lower` is less than or equal to every value in `upper`.
- `lower` has either the same number of elements as `upper`, or one extra element.

### Python Implementation

```python
import heapq


class MedianFinder:
    def __init__(self):
        self.lower = []  # Max-heap using negative values
        self.upper = []  # Min-heap

    def add_num(self, num):
        heapq.heappush(self.lower, -num)

        # Move the largest value in lower to upper.
        value = -heapq.heappop(self.lower)
        heapq.heappush(self.upper, value)

        # Keep lower the same size as upper or one larger.
        if len(self.upper) > len(self.lower):
            value = heapq.heappop(self.upper)
            heapq.heappush(self.lower, -value)

    def find_median(self):
        if not self.lower:
            raise ValueError("No numbers have been added")

        if len(self.lower) > len(self.upper):
            return -self.lower[0]

        return (-self.lower[0] + self.upper[0]) / 2


finder = MedianFinder()

for num in [5, 15, 1, 3]:
    finder.add_num(num)
    print(finder.find_median())

# Output:
# 5
# 10.0
# 5
# 4.0
```

### Explanation

After inserting `5` and `15`:

- `lower` contains the smaller half: `[5]`.
- `upper` contains the larger half: `[15]`.
- The median is `(5 + 15) / 2 = 10`.

After inserting `1` and `3`, the sorted values are `[1, 3, 5, 15]`.

The two middle values are `3` and `5`, so the median is `4`.

**Insertion time:** O(log n)  
**Median query:** O(1)  
**Auxiliary space:** O(n)

---

## 4. Find the K Largest Elements

A min-heap of size `k` can maintain the largest `k` elements seen so far.

```python
import heapq


def k_largest(nums, k):
    if k < 0:
        raise ValueError("k must be non-negative")

    if k == 0:
        return []

    heap = []

    for num in nums:
        heapq.heappush(heap, num)

        if len(heap) > k:
            heapq.heappop(heap)

    return sorted(heap, reverse=True)


print(k_largest([3, 1, 5, 12, 2, 11], 3))
# [12, 11, 5]
```

### Why Does It Work?

Whenever the heap contains more than `k` values, the smallest value is removed.

At the end, the heap contains the `k` largest values.

**Time complexity:** O(n log k + k log k)  
**Auxiliary space:** O(k)

This implementation treats duplicate values as separate elements.

---

## 5. Merge K Sorted Lists

The Two Heaps pattern is closely related to heap-based K-way merging.

Given multiple sorted lists, repeatedly extract the smallest current element and insert the next element from that list.

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


print(
    merge_k_sorted_lists([
        [1, 4, 7],
        [2, 5, 8],
        [3, 6, 9]
    ])
)
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

The heap stores at most one current element from each nonempty list.

**Time complexity:** O(N log k), where N is the total number of elements and k is the number of lists.  
**Auxiliary space:** O(k) for the heap, excluding the output.

---

## 6. Two Heaps vs. One Heap

| Requirement | Suitable Approach |
|---|---|
| Find the smallest element | Min-heap |
| Find the largest element | Max-heap |
| Find the top K largest values | Min-heap of size K |
| Maintain a running median | Two heaps |
| Merge K sorted lists | Min-heap |
| Maintain smaller and larger halves | Two heaps |

Not every problem involving heaps requires two heaps. Choose the structure that directly maintains the information needed.

---

## 7. Common Mistakes

1. Forgetting that Python's `heapq` is a min-heap by default.
2. Forgetting to negate values when simulating a max-heap.
3. Allowing the two heaps to become unbalanced.
4. Failing to maintain the ordering between the smaller and larger halves.
5. Returning the wrong middle element when the number of values is even.
6. Confusing the Two Heaps pattern with the Top K Elements pattern.
7. Forgetting to handle empty input.

---

## 8. Practice Problems

### Beginner

- Find the Median from a Data Stream.
- K Largest Elements.
- Last Stone Weight.

### Intermediate

- Sliding Window Median.
- Find K Closest Elements.
- Reorganize String.

### Advanced

- IPO.
- Smallest Range Covering Elements from K Lists.
- Maximum Sum Combinations.
- Median of Two Sorted Arrays.

---

## 9. Interview Checklist

Before coding, ask:

1. Do I need to maintain two groups of values?
2. Is one group smaller than a boundary and the other larger?
3. Which heap should contain each group?
4. What size-balance invariant must hold?
5. Can I answer the query directly from the heap tops?
6. What are the insertion, query, and space complexities?

### Final Takeaway

The Two Heaps pattern is particularly valuable for maintaining a running median. Use a max-heap for the smaller half and a min-heap for the larger half, while keeping both the ordering and size invariants correct.
