
# Algorithm Complexity Analysis — Interview Practice

## 1. What Is Algorithm Complexity?

Algorithm complexity describes how an algorithm's resource usage changes as the input size increases.

We mainly analyze:

- **Time complexity:** How the number of operations grows.
- **Space complexity:** How the additional memory usage grows.

Let `n` represent the input size.

For example, if a list contains 100 elements, then `n = 100`.

## 2. Big O Notation

Big O describes an asymptotic upper bound on an algorithm's growth rate. In interviews, it is commonly used to express worst-case complexity.

### Common complexities

| Complexity | Name | Example |
|---|---|---|
| O(1) | Constant | Access an array element by index |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Traverse a list |
| O(n log n) | Linearithmic | Merge sort |
| O(n²) | Quadratic | Two nested loops |
| O(2ⁿ) | Exponential | Generate all subsets |
| O(n!) | Factorial | Generate all permutations |

Generally, algorithms with slower growth rates scale better for large inputs, assuming comparable operations and implementation details.

## 3. O(1) — Constant Time

The number of operations does not grow with the input size.

```python
def get_first(numbers):
    if not numbers:
        return None
    return numbers[0]


print(get_first([10, 20, 30]))  # 10
```

**Time complexity:** O(1)

**Auxiliary space complexity:** O(1)

Accessing the first element takes constant time regardless of the list length.

## 4. O(n) — Linear Time

The number of operations grows proportionally to the input size.

```python
def find_max(numbers):
    if not numbers:
        return None

    maximum = numbers[0]

    for number in numbers:
        if number > maximum:
            maximum = number

    return maximum


print(find_max([4, 8, 2, 10, 3]))  # 10
```

**Time complexity:** O(n)

**Auxiliary space complexity:** O(1)

The function examines every element once.

## 5. O(n²) — Quadratic Time

Nested loops over the same input commonly produce quadratic complexity.

```python
def print_pairs(numbers):
    for first in numbers:
        for second in numbers:
            print(first, second)


print_pairs([1, 2, 3])
```

If the list contains `n` elements, the inner loop runs `n` times for each of the `n` outer-loop iterations.

**Time complexity:** O(n²)

**Auxiliary space complexity:** O(1), excluding output and runtime overhead.

## 6. O(log n) — Logarithmic Time

The problem size is repeatedly reduced by a constant factor.

Binary search works on a sorted list.

```python
def binary_search(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left <= right:
        middle = (left + right) // 2

        if numbers[middle] == target:
            return middle
        elif numbers[middle] < target:
            left = middle + 1
        else:
            right = middle - 1

    return -1


print(binary_search([1, 3, 5, 7, 9], 7))  # 3
```

**Time complexity:** O(log n)

**Auxiliary space complexity:** O(1)

Each iteration eliminates approximately half of the remaining search space.

## 7. O(n log n) — Linearithmic Time

Efficient comparison-based sorting algorithms often have O(n log n) time complexity.

Merge sort is a classic example.

```python
def merge_sort(numbers):
    if len(numbers) <= 1:
        return numbers

    middle = len(numbers) // 2

    left = merge_sort(numbers[:middle])
    right = merge_sort(numbers[middle:])

    result = []
    i = j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result.extend(left[i:])
    result.extend(right[j:])

    return result


print(merge_sort([5, 2, 8, 1, 3]))
# [1, 2, 3, 5, 8]
```

**Time complexity:** O(n log n)

**Auxiliary space complexity:** O(n) for this implementation, including temporary lists and merged results.

The algorithm divides the list into smaller parts and merges the sorted parts.

## 8. O(2ⁿ) — Exponential Time

Generating all subsets of a list produces `2ⁿ` possible subsets.

```python
def generate_subsets(numbers):
    result = []

    def backtrack(index, current):
        if index == len(numbers):
            result.append(current.copy())
            return

        # Exclude the current element.
        backtrack(index + 1, current)

        # Include the current element.
        current.append(numbers[index])
        backtrack(index + 1, current)
        current.pop()

    backtrack(0, [])
    return result


print(generate_subsets([1, 2]))
# [[], [2], [1], [1, 2]]
```

**Time complexity:** O(n × 2ⁿ) when copying and storing every subset.

**Space complexity:** O(n × 2ⁿ) for the returned collection, plus O(n) recursion depth.

There are `2ⁿ` subsets, and copying each subset can take up to O(n) time.

## 9. Analyze Loops Correctly

### Example A: One loop

```python
for i in range(n):
    print(i)
```

Time complexity: O(n)

### Example B: Two consecutive loops

```python
for i in range(n):
    print(i)

for j in range(n):
    print(j)
```

Time complexity: O(n + n) = O(n)

Consecutive loops are added, and constant factors are ignored.

### Example C: Nested loops

```python
for i in range(n):
    for j in range(n):
        print(i, j)
```

Time complexity: O(n²)

### Example D: Loop that doubles

```python
i = 1

while i < n:
    i *= 2
```

Time complexity: O(log n)

### Example E: Triangular nested loop

```python
for i in range(n):
    for j in range(i):
        print(i, j)
```

Time complexity: O(n²)

The number of iterations is approximately `n × (n - 1) / 2`, which simplifies to O(n²).

## 10. Best, Average, and Worst Cases

Consider linear search:

```python
def linear_search(numbers, target):
    for index, number in enumerate(numbers):
        if number == target:
            return index

    return -1
```

- **Best case:** O(1), when the target is the first element.
- **Worst case:** O(n), when the target is last or absent.
- **Average case:** O(n), under the usual assumption that the target's position is distributed across the list.

The average case depends on the assumptions about the input distribution.

## 11. Time Complexity vs Space Complexity

| Aspect | Time complexity | Space complexity |
|---|---|---|
| Measures | Growth in operations | Growth in memory |
| Main concern | Execution work | Memory consumption |
| Example | Binary search: O(log n) | Recursive binary search: O(log n) stack space |
| Optimization | Reduce unnecessary operations | Avoid unnecessary allocations |

**Important:** Auxiliary space counts extra memory used by the algorithm. Total space may also include the input, returned output, and runtime overhead, depending on the stated convention.

## 12. Common Interview Mistakes

1. Treating two consecutive loops as O(n²) when both run `n` times independently.
2. Forgetting that nested loops can have different bounds.
3. Assuming every recursive algorithm is O(2ⁿ).
4. Ignoring the cost of slicing lists or copying collections.
5. Forgetting recursion stack space.
6. Confusing total space with auxiliary space.
7. Ignoring the cost of operations inside a loop.
8. Saying binary search works on any unsorted list.
9. Keeping constant factors such as O(2n) instead of simplifying to O(n).
10. Claiming an algorithm is always faster based only on Big O.

## 13. Interview Practice Questions

Try solving these before checking the answers.

### Questions

1. What is the time complexity of accessing `numbers[5]` in a Python list?
2. What is the complexity of two consecutive loops, each running `n` times?
3. What is the complexity of three nested loops, each running `n` times?
4. What is the complexity when a loop repeatedly doubles its counter?
5. What is the time complexity of binary search?
6. What is the time complexity of merge sort?
7. Why is generating all permutations expensive?
8. What is the auxiliary space complexity of iterative binary search?
9. What is the difference between O(n) and O(log n)?
10. Why should you analyze the operations inside a loop?

### Answers

1. O(1)
2. O(n)
3. O(n³)
4. O(log n)
5. O(log n)
6. O(n log n)
7. There are n! permutations, and producing them all requires at least proportional output work.
8. O(1)
9. O(n) grows proportionally to the input size; O(log n) grows much more slowly as the input increases.
10. Because an operation may itself take more than constant time.

## 14. Your Complexity Analysis Checklist

Before explaining your code in an interview, ask:

- [ ] What is the input size?
- [ ] How many times does each loop execute?
- [ ] Are loops nested or consecutive?
- [ ] Does the input size shrink or grow by a constant factor?
- [ ] Are there recursive calls?
- [ ] Are lists being copied, sliced, sorted, or searched?
- [ ] What extra data structures are created?
- [ ] What are the time and auxiliary space complexities?
- [ ] Have I stated any assumptions behind my analysis?

## Final Advice

Do not memorize complexity values alone. Practice deriving them from code and explaining your reasoning aloud.

A strong interview explanation includes:
1. The input size.
2. The dominant operations.
3. The resulting time complexity.
4. The auxiliary space complexity.
5. Any assumptions or important trade-offs.
