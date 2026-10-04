
# Algorithm Optimization — Interview Practice

## 1. What Is Algorithm Optimization?

Algorithm optimization means improving a solution so it uses less execution time, less memory, or fewer computational resources while producing the correct result.

The goal is not simply to write fewer lines of code. The goal is to choose an appropriate algorithm and data structure.

Common optimization techniques include:

- Replacing nested loops with hash-based lookups.
- Sorting data to enable efficient searching.
- Using two pointers instead of repeatedly scanning a list.
- Using a sliding window for contiguous subarray problems.
- Using prefix sums to answer repeated range-sum queries.
- Using dynamic programming to avoid repeated calculations.
- Choosing an appropriate data structure.
- Avoiding unnecessary copying and memory allocation.

## 2. Example: Find a Pair With a Target Sum

Problem: Given a list of integers and a target, return the indices of two elements whose sum equals the target.

### Approach A: Brute Force

```python
def two_sum_brute_force(numbers, target):
    for i in range(len(numbers)):
        for j in range(i + 1, len(numbers)):
            if numbers[i] + numbers[j] == target:
                return [i, j]

    return []
```

Example:

```python
print(two_sum_brute_force([2, 7, 11, 15], 9))
# [0, 1]
```

**Time complexity:** O(n²)

**Auxiliary space complexity:** O(1)

The algorithm checks pairs until it finds a match.

### Approach B: Hash Map

```python
def two_sum_optimized(numbers, target):
    seen = {}

    for i, number in enumerate(numbers):
        complement = target - number

        if complement in seen:
            return [seen[complement], i]

        seen[number] = i

    return []
```

Example:

```python
print(two_sum_optimized([2, 7, 11, 15], 9))
# [0, 1]
```

**Expected time complexity:** O(n)

**Auxiliary space complexity:** O(n)

The dictionary remembers previously seen values and their indices.

### Comparison

| Approach | Time | Auxiliary space |
|---|---|---|
| Brute force | O(n²) | O(1) |
| Hash map | O(n) expected | O(n) |

The optimized approach uses extra memory to reduce execution time.

This is a classic example of a time-space trade-off.

## 3. Example: Membership Testing

Suppose you need to check whether many values exist in a collection.

### Using a list

```python
def count_matches_list(values, queries):
    count = 0

    for query in queries:
        if query in values:
            count += 1

    return count
```

Membership testing in a list takes O(n) in the worst case.

If there are q queries and n values, the total time can be O(nq).

### Using a set

```python
def count_matches_set(values, queries):
    values_set = set(values)
    count = 0

    for query in queries:
        if query in values_set:
            count += 1

    return count
```

Building the set takes O(n) expected time. Each membership test takes O(1) expected time.

Total expected time: O(n + q).

Auxiliary space: O(n).

**Lesson:** Choosing the right data structure can eliminate repeated work.

## 4. Example: Repeated Calculations

Problem: Calculate the nth Fibonacci number.

### Naive recursion

```python
def fibonacci_naive(n):
    if n <= 1:
        return n

    return fibonacci_naive(n - 1) + fibonacci_naive(n - 2)
```

This implementation has exponential time complexity, commonly expressed as O(2ⁿ) as an upper bound.

It repeatedly calculates the same Fibonacci values.

### Optimized iterative solution

```python
def fibonacci_optimized(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n <= 1:
        return n

    previous, current = 0, 1

    for _ in range(2, n + 1):
        previous, current = current, previous + current

    return current
```

Example:

```python
print(fibonacci_optimized(10))
# 55
```

**Time complexity:** O(n)

**Auxiliary space complexity:** O(1)

The iterative version calculates each required value once and keeps only two previous values.

## 5. Example: Range Sum Queries

Suppose you need to calculate the sum of several contiguous ranges in a list.

### Direct approach

```python
def range_sum_direct(numbers, left, right):
    return sum(numbers[left:right + 1])
```

For a range containing k elements, the time complexity is O(k).

Repeated queries may require substantial repeated work.

### Prefix sum approach

```python
def build_prefix_sum(numbers):
    prefix = [0]

    for number in numbers:
        prefix.append(prefix[-1] + number)

    return prefix


def range_sum(prefix, left, right):
    return prefix[right + 1] - prefix[left]


numbers = [2, 4, 6, 8, 10]
prefix = build_prefix_sum(numbers)

print(range_sum(prefix, 1, 3))
# 18
```

Building the prefix array takes O(n) time and O(n) additional space.

Each range-sum query takes O(1) time.

For q queries:

- Direct approach: up to O(qn) time in the worst case.
- Prefix sums: O(n + q) time.

**Lesson:** Preprocessing can make repeated queries much faster.

## 6. Example: Two Pointers

Problem: Determine whether a sorted list contains two numbers whose sum equals a target.

```python
def has_pair_with_sum(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left < right:
        total = numbers[left] + numbers[right]

        if total == target:
            return True
        elif total < target:
            left += 1
        else:
            right -= 1

    return False


print(has_pair_with_sum([1, 2, 4, 6, 9], 10))
# True
```

**Time complexity:** O(n)

**Auxiliary space complexity:** O(1)

This works because the input is sorted. Moving the left pointer increases the sum, while moving the right pointer decreases it.

Do not apply this exact approach to unsorted data without first addressing the ordering requirement.

## 7. Example: Sliding Window

Problem: Find the maximum sum of any contiguous subarray of length k.

```python
def max_window_sum(numbers, k):
    if k <= 0 or k > len(numbers):
        raise ValueError("k must be between 1 and len(numbers)")

    current_sum = sum(numbers[:k])
    maximum_sum = current_sum

    for right in range(k, len(numbers)):
        current_sum += numbers[right]
        current_sum -= numbers[right - k]
        maximum_sum = max(maximum_sum, current_sum)

    return maximum_sum


print(max_window_sum([2, 1, 5, 1, 3, 2], 3))
# 9
```

**Time complexity:** O(n)

**Auxiliary space complexity:** O(1)

The first window is calculated once. Every subsequent window adds one element and removes one element.

This avoids recalculating the sum of every window from scratch.

## 8. Example: Sorting Before Searching

Suppose a dataset must be searched repeatedly.

For an unsorted list, a single linear search takes O(n).

If you sort the list first, sorting generally takes O(n log n), and each binary search takes O(log n).

For q searches:

- Linear search each time: O(qn).
- Sort once, then binary search: O(n log n + q log n).

Sorting can be worthwhile when the data is reused for many searches.

However, it may not be worthwhile for a single query, and sorting can change the original order of the data.

## 9. Time-Space Trade-offs

An algorithm can often become faster by storing extra information.

| Technique | Time benefit | Memory cost |
|---|---|---|
| Hash map | Faster lookups | Extra storage |
| Memoization | Avoids repeated calculations | Cache storage |
| Prefix sums | Faster range queries | Prefix array |
| Precomputed lookup table | Faster repeated calculations | Table storage |
| In-place processing | Can reduce allocations | May modify input |

There is no universally best solution. The right choice depends on input size, memory constraints, expected queries, and correctness requirements.

## 10. A Systematic Optimization Workflow

Use this process when solving a coding problem.

1. Understand the requirements and edge cases.
2. Write a straightforward correct solution.
3. Determine its time and space complexity.
4. Identify the expensive operation or repeated work.
5. Select a suitable data structure or algorithmic pattern.
6. Implement the improved solution.
7. Test normal cases, boundary cases, and invalid inputs.
8. Compare the original and optimized complexities.
9. Explain the trade-offs clearly.

Do not optimize code before understanding what it must do.

## 11. Common Optimization Mistakes

1. Using a set or dictionary without considering memory requirements.
2. Assuming hash-table operations are guaranteed O(1) in every case.
3. Applying binary search to unsorted data.
4. Using two pointers when the required ordering or problem properties do not support them.
5. Replacing a clear correct solution with complicated code that has no meaningful performance benefit.
6. Ignoring the cost of preprocessing.
7. Forgetting that sorting changes element positions.
8. Failing to test empty inputs, duplicates, and boundary conditions.
9. Confusing improved asymptotic complexity with guaranteed real-world speed.
10. Optimizing a small section of code while ignoring the actual bottleneck.

## 12. Interview Practice Questions

1. How would you optimize a nested-loop pair-sum solution?
2. When would you use a dictionary instead of a list?
3. Why is iterative Fibonacci more efficient than naive recursion?
4. When are prefix sums useful?
5. What conditions make binary search applicable?
6. What is the difference between sliding window and two pointers?
7. Explain a time-space trade-off.
8. When can preprocessing improve performance?
9. Why should you benchmark code rather than rely only on Big O?
10. How would you decide whether an optimization is worth implementing?

## 13. Final Checklist

- [ ] Identify the bottleneck in a simple solution.
- [ ] Compare brute-force and optimized approaches.
- [ ] Explain time and auxiliary space complexity.
- [ ] Recognize opportunities for hash maps and sets.
- [ ] Apply prefix sums, sliding windows, and two pointers appropriately.
- [ ] Explain the costs of preprocessing and caching.
- [ ] Test edge cases after optimizing.
- [ ] Justify the trade-off in an interview.

**Interview tip:** Start with a correct brute-force solution, explain its bottleneck, and then justify the optimization. Interviewers want to understand your reasoning, not just see the final code.
