
# Hashing and Frequency Counting — Interview Practice

## 1. What Is Hashing?

Hashing is a technique for mapping keys to values so that data can be accessed efficiently.

In Python, dictionaries and sets use hashing internally.

Common uses include:

- Counting element frequencies.
- Detecting duplicates.
- Finding pairs with a target sum.
- Grouping elements.
- Checking membership efficiently.
- Tracking previously seen values.

Dictionary and set lookups take O(1) average time, although their worst-case complexity can be O(n).

---

## 2. Count the Frequency of Elements

Given a list, count how many times each element occurs.

```python
def frequency_count(nums):
    frequency = {}

    for num in nums:
        frequency[num] = frequency.get(num, 0) + 1

    return frequency


print(frequency_count([1, 2, 2, 3, 1, 2]))
# {1: 2, 2: 3, 3: 1}
```

### Alternative Using Counter

```python
from collections import Counter

nums = [1, 2, 2, 3, 1, 2]
print(Counter(nums))
# Counter({2: 3, 1: 2, 3: 1})
```

**Time complexity:** O(n) average  
**Auxiliary space:** O(n)

---

## 3. Find Duplicate Elements

Return the unique elements that appear more than once.

```python
def find_duplicates(nums):
    frequency = {}

    for num in nums:
        frequency[num] = frequency.get(num, 0) + 1

    return [
        num for num, count in frequency.items()
        if count > 1
    ]


print(find_duplicates([1, 2, 3, 2, 4, 1, 5]))
# [1, 2]
```

The output order follows the first appearance of each element in the input.

**Time complexity:** O(n) average  
**Auxiliary space:** O(n)

---

## 4. Two Sum

Given an array and a target, return the indices of two distinct elements whose sum equals the target.

```python
def two_sum(nums, target):
    seen = {}

    for i, num in enumerate(nums):
        complement = target - num

        if complement in seen:
            return [seen[complement], i]

        seen[num] = i

    return []


print(two_sum([2, 7, 11, 15], 9))
# [0, 1]

print(two_sum([3, 3], 6))
# [0, 1]
```

### How It Works

For each element:

1. Calculate the required complement.
2. Check whether that complement has already appeared.
3. If it has, return both indices.
4. Otherwise, store the current value and index.

Checking before inserting also prevents using the same element twice.

**Time complexity:** O(n) average  
**Auxiliary space:** O(n)

---

## 5. First Non-Repeating Character

Return the index of the first character that occurs exactly once. Return -1 if no such character exists.

```python
def first_unique_character(s):
    frequency = {}

    for char in s:
        frequency[char] = frequency.get(char, 0) + 1

    for i, char in enumerate(s):
        if frequency[char] == 1:
            return i

    return -1


print(first_unique_character("leetcode"))
# 0

print(first_unique_character("aabb"))
# -1
```

**Time complexity:** O(n) average  
**Auxiliary space:** O(k), where k is the number of distinct characters.

---

## 6. Group Anagrams

Group strings that contain the same characters with the same frequencies.

```python
from collections import defaultdict


def group_anagrams(words):
    groups = defaultdict(list)

    for word in words:
        key = tuple(sorted(word))
        groups[key].append(word)

    return list(groups.values())


print(group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"]))
# Example groups:
# [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```

Sorting each word produces a common key for its anagrams.

**Time complexity:** O(n × m log m), where n is the number of words and m is the maximum word length.  
**Auxiliary space:** O(n × m), including stored words and grouping keys.

For a fixed alphabet, a character-frequency tuple can replace sorting.

---

## 7. Check Whether Two Strings Are Anagrams

Two strings are anagrams if they contain the same characters with the same frequencies.

```python
from collections import Counter


def are_anagrams(first, second):
    return Counter(first) == Counter(second)


print(are_anagrams("listen", "silent"))
# True

print(are_anagrams("hello", "world"))
# False
```

This comparison is case-sensitive and considers spaces as characters.

**Time complexity:** O(n + m) average  
**Auxiliary space:** O(k), where k is the number of distinct characters.

---

## 8. Longest Consecutive Sequence

Find the length of the longest sequence of consecutive integers in an unsorted list.

```python
def longest_consecutive(nums):
    values = set(nums)
    longest = 0

    for num in values:
        # Start only at the beginning of a sequence.
        if num - 1 not in values:
            current = num
            length = 1

            while current + 1 in values:
                current += 1
                length += 1

            longest = max(longest, length)

    return longest


print(longest_consecutive([100, 4, 200, 1, 3, 2]))
# 4

print(longest_consecutive([0, 3, 7, 2, 5, 8, 4, 6, 0, 1]))
# 9
```

### Why Is It O(n) Average?

Only sequence beginnings trigger the inner loop. Every consecutive value is visited a bounded number of times across those scans.

**Time complexity:** O(n) average  
**Auxiliary space:** O(n)

---

## 9. Top K Frequent Elements

Return the k most frequent elements.

```python
from collections import Counter
import heapq


def top_k_frequent(nums, k):
    if k <= 0:
        return []

    frequency = Counter(nums)

    return [
        num for num, count in
        heapq.nlargest(
            k,
            frequency.items(),
            key=lambda item: item[1]
        )
    ]


print(top_k_frequent([1, 1, 1, 2, 2, 3], 2))
# [1, 2]
```

If multiple values have the same frequency, their relative order is not guaranteed by this implementation.

**Time complexity:** O(n + u log k) as a typical bound for heap-based selection, where u is the number of distinct values.  
**Auxiliary space:** O(u + k)

For small k, a heap can be more efficient than sorting every distinct element.

---

## 10. Subarray Sum Equals K

Count the number of contiguous subarrays whose sum equals k.

```python
def subarray_sum(nums, k):
    prefix_sum = 0
    count = 0
    frequency = {0: 1}

    for num in nums:
        prefix_sum += num

        count += frequency.get(prefix_sum - k, 0)

        frequency[prefix_sum] = (
            frequency.get(prefix_sum, 0) + 1
        )

    return count


print(subarray_sum([1, 1, 1], 2))
# 2

print(subarray_sum([1, -1, 0], 0))
# 3
```

This pattern combines prefix sums with a hashmap and works with negative numbers.

**Time complexity:** O(n) average  
**Auxiliary space:** O(n)

---

## 11. Common Mistakes

1. Forgetting that a missing dictionary key can be handled with `dict.get()`.
2. Using a list for repeated membership checks when a set is more appropriate.
3. Storing a value before checking its complement when doing so could allow an element to match itself.
4. Assuming hash-based operations are always O(1) in the worst case.
5. Forgetting that dictionary keys and set elements must be hashable.
6. Using an unnecessarily complicated nested loop for a frequency-counting problem.
7. Ignoring whether the problem requires indices, values, unique elements, or occurrence counts.

---

## 12. Practice Problems

### Beginner

- Contains Duplicate.
- Valid Anagram.
- Two Sum.
- First Unique Character in a String.
- Intersection of Two Arrays.

### Intermediate

- Group Anagrams.
- Top K Frequent Elements.
- Longest Consecutive Sequence.
- Subarray Sum Equals K.
- Find All Anagrams in a String.

### Advanced

- Minimum Window Substring.
- Count of Smaller Numbers After Self.
- 4Sum II.
- Number of Subarrays With Bounded Maximum.

---

## 13. Interview Checklist

Before coding, ask:

1. Do I need to count, group, detect, or look up values?
2. Can a dictionary or set eliminate a nested loop?
3. Do I need to preserve the original indices?
4. Should repeated values be counted separately?
5. What is the expected time and auxiliary space complexity?
6. Are the keys hashable?
7. What edge cases occur with empty input, duplicates, or negative numbers?

### Final Takeaway

Hashing is one of the most reusable interview techniques. Learn to recognize when a dictionary or set can replace repeated scanning, especially in frequency counting, pair-sum, grouping, and prefix-sum problems.
