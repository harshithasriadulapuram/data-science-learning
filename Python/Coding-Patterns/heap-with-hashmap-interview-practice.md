# Heap + Hash Map Interview Practice in Python

## 1. Why Combine a Heap and Hash Map?

A heap efficiently retrieves the smallest or largest element, while a hash map stores and retrieves information by key.

Together, they are useful for:
- Finding the top K frequent elements
- Tracking the most frequent items
- Scheduling tasks by priority
- Maintaining frequency-based rankings

## 2. Top K Frequent Elements

### Problem
Given a list of integers, return the K most frequent elements.

### Code

```python
from collections import Counter
import heapq


def top_k_frequent(nums, k):
    frequency = Counter(nums)
    return heapq.nlargest(
        k,
        frequency.keys(),
        key=frequency.get
    )


print(top_k_frequent([1, 1, 1, 2, 2, 3], 2))
# [1, 2]
```

### Explanation
1. Counter counts each number's occurrences.
2. The heap-based operation selects the K keys with the highest frequencies.
3. The result contains the K most frequent numbers.

Time complexity: approximately O(n + u log k) for a bounded-size heap implementation, where n is the input length and u is the number of unique values. Python's `heapq.nlargest` may use a different strategy depending on K.

## 3. Sort Items by Frequency

### Problem
Return numbers ordered from highest frequency to lowest.

```python
from collections import Counter


def sort_by_frequency(nums):
    frequency = Counter(nums)
    return sorted(nums, key=lambda x: (-frequency[x], x))


print(sort_by_frequency([4, 4, 1, 2, 2, 2, 3]))
# [2, 2, 2, 4, 4, 1, 3]
```

The negative frequency sorts higher-frequency items first. The second key sorts equal-frequency values in ascending order.

## 4. Task Scheduler by Priority

### Problem
Process tasks in order of priority, with the largest priority first.

```python
import heapq


def process_tasks(tasks):
    heap = []

    for task, priority in tasks:
        heapq.heappush(heap, (-priority, task))

    result = []

    while heap:
        priority, task = heapq.heappop(heap)
        result.append((task, -priority))

    return result


tasks = [
    ("email", 2),
    ("backup", 5),
    ("report", 3)
]

print(process_tasks(tasks))
# [('backup', 5), ('report', 3), ('email', 2)]
```

Python's heap is a min-heap, so negative priorities make larger priorities come out first.

## 5. Important Complexity

| Operation | Typical complexity |
|---|---:|
| Hash map lookup | O(1) average |
| Heap insertion | O(log n) |
| Heap removal | O(log n) |
| Read heap root | O(1) |
| Build a heap from a list | O(n) |

## 6. Practice Problems

1. Find the K most frequent elements.
2. Find the K most frequent words.
3. Find the K closest points to the origin.
4. Design a task scheduler using priorities.
5. Maintain a leaderboard of the highest-scoring users.
6. Find the K largest elements in a stream.
7. Merge multiple sorted lists.
8. Design a system that tracks the most frequently accessed items.

## 7. Interview Tips

- Use a hash map when you need fast lookup or frequency counting.
- Use a heap when you repeatedly need the smallest or largest item.
- Use a bounded heap when K is much smaller than the number of unique items.
- Explain tie-breaking rules explicitly.
- State time and space complexity for your chosen implementation.

## 8. Self-Test

Before checking your solution, explain:

1. Why is a hash map useful for counting frequencies?
2. Why can a heap be better than sorting every element?
3. What changes when K is close to the total number of unique elements?
4. Why does Python's `heapq` use negative priorities for max-heap behavior in this example?
