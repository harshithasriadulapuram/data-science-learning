 
# Two Pointers Technique in Python

## 1. Introduction

The Two Pointers technique is an algorithmic approach that uses two variables to track positions in a data structure, usually an array, list, or string.

Instead of checking every possible pair using nested loops, we move two pointers according to specific conditions.

This technique can reduce time complexity from O(n²) to O(n) in many problems.

### Example

Consider this array:

```python
numbers = [1, 2, 4, 6, 8, 10]
```

Suppose we want to find two numbers whose sum is 10.

We can use:
- One pointer at the beginning.
- Another pointer at the end.

Move the pointers until the target condition is satisfied.

---

## 2. Types of Two Pointers

The two common patterns are:

1. Opposite-direction pointers
2. Same-direction pointers

Other applications include fast and slow pointers, commonly used in linked lists.

---

## 3. Opposite-Direction Pointers

### Definition

One pointer starts at the beginning, and the other starts at the end.

They move toward each other until they meet or the required condition is satisfied.

This approach is particularly useful for sorted arrays and palindrome problems.

### Example 1: Find Two Numbers with a Target Sum

Given a sorted array, determine whether two numbers add up to a target.

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


numbers = [1, 2, 4, 6, 8, 10]

print(has_pair_with_sum(numbers, 10))  # True
print(has_pair_with_sum(numbers, 17))  # True
print(has_pair_with_sum(numbers, 20))  # False
```

### How It Works

For `numbers = [1, 2, 4, 6, 8, 10]` and `target = 10`:

1. Left points to 1, right points to 10. Sum = 11.
2. The sum is too large, so move right leftward.
3. Left points to 1, right points to 8. Sum = 9.
4. The sum is too small, so move left rightward.
5. Left points to 2, right points to 8. Sum = 10.
6. The target is found, so return `True`.

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

**Important:** This implementation requires the input array to be sorted in ascending order.

---

## 4. Return the Pair Instead of True or False

Sometimes we need the actual pair of values.

```python
def find_pair_with_sum(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left < right:
        total = numbers[left] + numbers[right]

        if total == target:
            return numbers[left], numbers[right]
        elif total < target:
            left += 1
        else:
            right -= 1

    return None


numbers = [1, 2, 4, 6, 8, 10]

print(find_pair_with_sum(numbers, 10))  # (2, 8)
```

This returns one matching pair, not necessarily every possible pair.

---

## 5. Check Whether a String Is a Palindrome

### Definition

A palindrome reads the same forward and backward.

Examples:
- `madam`
- `level`
- `racecar`

### Python Implementation

```python
def is_palindrome(text):
    left = 0
    right = len(text) - 1

    while left < right:
        if text[left] != text[right]:
            return False

        left += 1
        right -= 1

    return True


print(is_palindrome("madam"))   # True
print(is_palindrome("level"))   # True
print(is_palindrome("python"))  # False
```

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

This function compares characters exactly, including capitalization and spaces.

For a case-insensitive palindrome check that ignores non-alphanumeric characters:

```python
def is_clean_palindrome(text):
    left = 0
    right = len(text) - 1

    while left < right:
        while left < right and not text[left].isalnum():
            left += 1

        while left < right and not text[right].isalnum():
            right -= 1

        if text[left].lower() != text[right].lower():
            return False

        left += 1
        right -= 1

    return True


print(is_clean_palindrome("A man, a plan, a canal: Panama!"))
# True
```

---

## 6. Reverse an Array Using Two Pointers

### Definition

Swap elements at the beginning and end, then move both pointers inward.

```python
def reverse_array(numbers):
    numbers = numbers.copy()

    left = 0
    right = len(numbers) - 1

    while left < right:
        numbers[left], numbers[right] = (
            numbers[right],
            numbers[left]
        )

        left += 1
        right -= 1

    return numbers


print(reverse_array([1, 2, 3, 4, 5]))
# [5, 4, 3, 2, 1]
```

### Complexity

- Time: O(n)
- Auxiliary space: O(n) because this implementation copies the input.

If the array is modified directly instead of copied, the reversal itself uses O(1) auxiliary space.

---

## 7. Same-Direction Pointers

### Definition

Both pointers move from left to right, but they may move at different speeds or serve different purposes.

This approach is useful for removing duplicates, filtering elements, and processing arrays in place.

### Example: Remove Duplicates from a Sorted Array

We want to keep only unique values at the beginning of the array.

```python
def remove_duplicates(numbers):
    if not numbers:
        return 0

    write = 1

    for read in range(1, len(numbers)):
        if numbers[read] != numbers[write - 1]:
            numbers[write] = numbers[read]
            write += 1

    return write


numbers = [1, 1, 2, 2, 3, 4, 4, 5]

length = remove_duplicates(numbers)

print(length)           # 5
print(numbers[:length]) # [1, 2, 3, 4, 5]
```

### How It Works

- `read` scans the array.
- `write` tracks where the next unique value should be placed.
- When a new value is found, it is copied to the write position.
- The function returns the number of unique elements.

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

**Important:** The input must be sorted. The function modifies the original list.

---

## 8. Move All Zeros to the End

### Problem

Move every zero to the end while preserving the relative order of non-zero elements.

```python
def move_zeros(numbers):
    write = 0

    for read in range(len(numbers)):
        if numbers[read] != 0:
            numbers[write], numbers[read] = (
                numbers[read],
                numbers[write]
            )
            write += 1

    return numbers


numbers = [0, 1, 0, 3, 12]

print(move_zeros(numbers))
# [1, 3, 12, 0, 0]
```

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

This solution modifies the original list.

---

## 9. Fast and Slow Pointers

### Definition

Fast and slow pointers are a special pointer pattern.

- The slow pointer usually moves one step at a time.
- The fast pointer usually moves two steps at a time.

This pattern is particularly useful for linked lists.

### Example: Find the Middle of a Linked List

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None


def find_middle(head):
    slow = head
    fast = head

    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next

    return slow


head = Node(10)
head.next = Node(20)
head.next.next = Node(30)
head.next.next.next = Node(40)
head.next.next.next.next = Node(50)

middle = find_middle(head)

print(middle.value)  # 30
```

### How It Works

1. Both pointers begin at the head.
2. Slow moves one node.
3. Fast moves two nodes.
4. When fast reaches the end, slow is at the middle.

For an even number of nodes, this implementation returns the second middle node.

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

---

## 10. Two Pointers vs Nested Loops

Consider finding whether an array contains a pair that adds up to a target.

### Nested Loop Approach

```python
def has_pair_brute_force(numbers, target):
    for i in range(len(numbers)):
        for j in range(i + 1, len(numbers)):
            if numbers[i] + numbers[j] == target:
                return True

    return False
```

- Time: O(n²)
- Auxiliary space: O(1)

### Two Pointers Approach

```python
def has_pair_two_pointers(numbers, target):
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
```

- Time: O(n)
- Auxiliary space: O(1)

The Two Pointers approach is faster for this problem when the array is sorted.

---

## 11. Common Mistakes

1. Using opposite-direction pointers on an unsorted array without a suitable strategy.
2. Using `left <= right` when the problem requires two distinct elements.
3. Forgetting to move one or both pointers inside the loop.
4. Accessing an index before checking the boundary.
5. Forgetting that some solutions modify the original list.
6. Assuming every Two Pointers problem can be solved in O(n).
7. Returning array values when the problem expects indices, or vice versa.

---

## 12. Interview Questions

### Beginner

1. What is the Two Pointers technique?
2. When should you use two pointers instead of nested loops?
3. How can you check whether a string is a palindrome?
4. How can you reverse an array using two pointers?
5. What is the difference between opposite-direction and same-direction pointers?

### Intermediate

6. Find a pair with a target sum in a sorted array.
7. Remove duplicates from a sorted array.
8. Move all zeros to the end.
9. Find the middle of a linked list.
10. Check whether a sorted array contains a pair with a target sum.

### Advanced

11. Find all unique triplets whose sum is zero.
12. Calculate the maximum area of water that can be contained between vertical lines.
13. Find the maximum number of consecutive elements satisfying a condition using an appropriate pointer technique.
14. Remove a specific value from an array in place.
15. Find the intersection of two sorted arrays.

---

## 13. Coding Practice Checklist

- [ ] Find a pair with a target sum.
- [ ] Return the actual pair of values.
- [ ] Check whether a string is a palindrome.
- [ ] Reverse an array in place.
- [ ] Remove duplicates from a sorted array.
- [ ] Move all zeros to the end.
- [ ] Find the middle node of a linked list.
- [ ] Solve the two-sum problem on a sorted array.
- [ ] Solve the three-sum problem.
- [ ] Compare a brute-force solution with a Two Pointers solution.

---

## 14. Key Takeaways

- Two Pointers uses two positions to process data efficiently.
- Opposite-direction pointers often work well with sorted arrays.
- Same-direction pointers are useful for scanning, filtering, and in-place modifications.
- Fast and slow pointers are common in linked-list problems.
- Many problems can be reduced from O(n²) to O(n), but the correct approach depends on the problem's constraints.
- Always verify whether the input must be sorted and whether the original collection can be modified.
