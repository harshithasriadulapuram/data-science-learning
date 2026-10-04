
# Arrays and Strings — DSA Interview Practice in Python

## 1. Why Are Arrays and Strings Important?

Arrays and strings are fundamental topics in coding interviews. Many advanced problems build on techniques learned from them.

Common problem-solving patterns include:
- Hashing
- Two pointers
- Sliding window
- Prefix sums
- Sorting
- Binary search
- Stack-based processing

The goal is not just to memorize solutions. Learn to recognize which pattern fits a problem.

---

## 2. Find the Largest Element

### Problem

Given a list of integers, return the largest element without using `max()`.

### Code

```python
def find_largest(arr):
    if not arr:
        raise ValueError("Array must not be empty")

    largest = arr[0]

    for number in arr:
        if number > largest:
            largest = number

    return largest


print(find_largest([4, 9, 2, 15, 7]))  # 15
```

### Complexity

- Time: O(n)
- Space: O(1)

### Key Idea

Keep track of the largest value seen so far.

---

## 3. Find the Second-Largest Distinct Element

### Problem

Return the second-largest distinct value without sorting the list.

```python
def second_largest(arr):
    largest = None
    second = None

    for number in arr:
        if largest is None or number > largest:
            if number != largest:
                second = largest
                largest = number
        elif number != largest and (
            second is None or number > second
        ):
            second = number

    if second is None:
        raise ValueError("No second-largest distinct value")

    return second


print(second_largest([10, 5, 20, 8, 20]))  # 10
```

### Complexity

- Time: O(n)
- Space: O(1)

### Key Idea

Track the two largest distinct values while making one pass.

---

## 4. Reverse an Array In Place

### Problem

Reverse the array without creating another array.

```python
def reverse_array(arr):
    left = 0
    right = len(arr) - 1

    while left < right:
        arr[left], arr[right] = arr[right], arr[left]
        left += 1
        right -= 1

    return arr


print(reverse_array([1, 2, 3, 4, 5]))
# [5, 4, 3, 2, 1]
```

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

### Key Idea

Swap elements from opposite ends and move inward.

---

## 5. Remove Duplicates from a Sorted Array

### Problem

Modify a sorted array so that each distinct value appears once in the beginning. Return the number of distinct values.

```python
def remove_duplicates(arr):
    if not arr:
        return 0

    write = 1

    for read in range(1, len(arr)):
        if arr[read] != arr[write - 1]:
            arr[write] = arr[read]
            write += 1

    return write


arr = [1, 1, 2, 2, 3, 4, 4]
count = remove_duplicates(arr)

print(count)       # 4
print(arr[:count]) # [1, 2, 3, 4]
```

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

### Key Idea

Use separate pointers for reading values and writing unique values.

---

## 6. Two Sum

### Problem

Given an array and a target, return the indices of two distinct elements whose values add up to the target.

Assume exactly one valid answer exists.

```python
def two_sum(nums, target):
    seen = {}

    for index, number in enumerate(nums):
        complement = target - number

        if complement in seen:
            return [seen[complement], index]

        seen[number] = index

    return []


print(two_sum([2, 7, 11, 15], 9))  # [0, 1]
```

### Complexity

- Time: O(n) average
- Space: O(n)

### Key Idea

Use a hash table to remember previously visited values and their indices.

---

## 7. Move Zeros to the End

### Problem

Move all zeros to the end while preserving the order of nonzero elements.

```python
def move_zeros(nums):
    write = 0

    for number in nums:
        if number != 0:
            nums[write] = number
            write += 1

    while write < len(nums):
        nums[write] = 0
        write += 1

    return nums


print(move_zeros([0, 1, 0, 3, 12]))
# [1, 3, 12, 0, 0]
```

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

### Key Idea

Write nonzero values first, then fill the remaining positions with zeros.

---

## 8. Check Whether a String Is a Palindrome

### Problem

Determine whether a string reads the same forward and backward.

This version ignores spaces, punctuation, and letter case.

```python
def is_palindrome(text):
    cleaned = "".join(
        char.lower()
        for char in text
        if char.isalnum()
    )

    left = 0
    right = len(cleaned) - 1

    while left < right:
        if cleaned[left] != cleaned[right]:
            return False

        left += 1
        right -= 1

    return True


print(is_palindrome("Madam"))  # True
print(is_palindrome("A man, a plan, a canal: Panama"))
# True
print(is_palindrome("Python"))  # False
```

### Complexity

- Time: O(n)
- Space: O(n) for the cleaned string

### Key Idea

Compare matching characters from opposite ends.

---

## 9. Check Whether Two Strings Are Anagrams

### Problem

Two strings are anagrams if they contain the same characters with the same frequencies.

This version is case-sensitive and does not ignore spaces or punctuation.

```python
from collections import Counter


def are_anagrams(first, second):
    return Counter(first) == Counter(second)


print(are_anagrams("listen", "silent"))  # True
print(are_anagrams("hello", "world"))    # False
```

### Complexity

- Time: O(n + m) average
- Space: O(n + m)

Here, n and m are the lengths of the two strings.

### Key Idea

Compare character-frequency counts instead of comparing positions.

---

## 10. Find the First Non-Repeating Character

### Problem

Return the first character that occurs exactly once. Return `None` if every character repeats.

```python
from collections import Counter


def first_unique_character(text):
    frequencies = Counter(text)

    for char in text:
        if frequencies[char] == 1:
            return char

    return None


print(first_unique_character("swiss"))  # w
print(first_unique_character("aabb"))   # None
```

### Complexity

- Time: O(n) average
- Space: O(k), where k is the number of distinct characters

### Key Idea

Count frequencies first, then scan the original string to preserve order.

---

## 11. Longest Substring Without Repeating Characters

### Problem

Find the length of the longest substring containing no repeated characters.

```python
def longest_unique_substring(text):
    last_seen = {}
    left = 0
    longest = 0

    for right, char in enumerate(text):
        if char in last_seen and last_seen[char] >= left:
            left = last_seen[char] + 1

        last_seen[char] = right
        longest = max(longest, right - left + 1)

    return longest


print(longest_unique_substring("abcabcbb"))  # 3
print(longest_unique_substring("bbbbb"))     # 1
print(longest_unique_substring("pwwkew"))    # 3
```

### Complexity

- Time: O(n) average
- Space: O(k), where k is the number of distinct characters encountered

### Key Idea

Use a sliding window and remember the most recent position of each character.

---

## 12. Maximum Sum of a Subarray of Size K

### Problem

Given an array and a positive integer `k`, find the maximum sum among all contiguous subarrays of exactly `k` elements.

```python
def max_sum_subarray(nums, k):
    if k <= 0 or k > len(nums):
        raise ValueError("Invalid window size")

    window_sum = sum(nums[:k])
    maximum = window_sum

    for right in range(k, len(nums)):
        window_sum += nums[right]
        window_sum -= nums[right - k]
        maximum = max(maximum, window_sum)

    return maximum


print(max_sum_subarray([2, 1, 5, 1, 3, 2], 3))
# 9
```

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

### Key Idea

Calculate the first window once. For each next window, add the incoming element and subtract the outgoing element.

---

## 13. Prefix Sum Range Query

### Problem

Answer multiple inclusive range-sum queries efficiently.

```python
def build_prefix_sum(nums):
    prefix = [0]

    for number in nums:
        prefix.append(prefix[-1] + number)

    return prefix


def range_sum(prefix, left, right):
    if left < 0 or right < left or right >= len(prefix) - 1:
        raise ValueError("Invalid range")

    return prefix[right + 1] - prefix[left]


nums = [2, 4, 6, 8, 10]
prefix = build_prefix_sum(nums)

print(range_sum(prefix, 1, 3))  # 18
print(range_sum(prefix, 0, 4))  # 30
```

### Complexity

- Build prefix sums: O(n)
- Each range query: O(1)
- Extra space: O(n)

### Key Idea

The sum from index `left` through `right` is:

`prefix[right + 1] - prefix[left]`

---

## 14. Binary Search

### Problem

Find a target in a sorted array. Return its index or `-1` if absent.

```python
def binary_search(nums, target):
    left = 0
    right = len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid

        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1


print(binary_search([1, 3, 5, 7, 9], 7))  # 3
print(binary_search([1, 3, 5, 7, 9], 4))  # -1
```

### Complexity

- Time: O(log n)
- Auxiliary space: O(1)

### Key Idea

Discard half of the remaining search interval at each step.

The input must be sorted.

---

## 15. Common Mistakes to Avoid

1. Forgetting to handle empty arrays.
2. Confusing an element's value with its index.
3. Using nested loops when hashing can solve the problem in one pass.
4. Forgetting that sliding-window problems require a clearly defined window.
5. Applying binary search to unsorted data.
6. Modifying an array when the problem requires preserving the original.
7. Ignoring duplicates when finding the second-largest distinct value.
8. Reporting auxiliary space without considering temporary data structures.
9. Memorizing code without understanding why it works.
10. Forgetting edge cases such as one element, all duplicates, negative values, and empty strings.

---

## 16. Practice Checklist

### Beginner
- [ ] Find the largest and smallest elements.
- [ ] Find the second-largest distinct element.
- [ ] Reverse an array.
- [ ] Check whether a string is a palindrome.
- [ ] Check whether two strings are anagrams.

### Intermediate
- [ ] Solve Two Sum.
- [ ] Move zeros to the end.
- [ ] Remove duplicates from a sorted array.
- [ ] Find the first non-repeating character.
- [ ] Find the longest substring without repeated characters.

### Advanced
- [ ] Find the maximum sum of a fixed-size subarray.
- [ ] Answer multiple range-sum queries.
- [ ] Solve binary-search variations.
- [ ] Combine hashing with two pointers or sliding windows.

## Key Takeaways

- Learn the problem-solving pattern, not just the solution.
- Hashing helps with fast lookup and frequency counting.
- Two pointers often reduce nested loops to linear time.
- Sliding windows handle many contiguous-subarray and substring problems.
- Prefix sums answer repeated range-sum queries efficiently.
- Binary search works on sorted data or suitable monotonic search spaces.
- Always test edge cases and analyze time and space complexity.
