
# Fast and Slow Pointers in Python

## 1. What Is the Fast and Slow Pointers Pattern?

Fast and Slow Pointers is a technique that uses two pointers moving through a sequence at different speeds.

- **Slow pointer:** Usually moves one step at a time.
- **Fast pointer:** Usually moves two steps at a time.

This technique is particularly useful for linked lists because it can solve several problems in linear time without requiring extra collections.

Common applications include:

- Detecting a cycle in a linked list.
- Finding the middle node of a linked list.
- Finding the start of a cycle.
- Finding the Nth node from the end.
- Detecting happy numbers.
- Finding the duplicate number in certain arrays.

## 2. Finding the Middle of a Linked List

### Problem statement

Given the head of a singly linked list, return its middle node.

If the list has an even number of nodes, return the second middle node.

Example:

```text
1 -> 2 -> 3 -> 4 -> 5
```

Output:

```text
3
```

For an even-length list:

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

Output:

```text
4
```

### Implementation

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def middle_node(head):
    slow = head
    fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    return slow
```

### How it works

Initially, both pointers start at the head.

During every iteration:

- `slow` advances by one node.
- `fast` advances by two nodes.

When `fast` reaches the end, `slow` is at the middle.

### Complexity

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 3. Detecting a Cycle in a Linked List

### Problem statement

Determine whether a linked list contains a cycle.

A cycle exists when a node's `next` pointer eventually leads back to a previously visited node.

Example:

```text
1 -> 2 -> 3 -> 4
          ^    |
          |____|
```

The list contains a cycle.

### Floyd's Cycle Detection Algorithm

The slow pointer moves one step, while the fast pointer moves two steps.

If a cycle exists, the fast pointer eventually catches up with the slow pointer.

If the fast pointer reaches `None`, there is no cycle.

### Implementation

```python
def has_cycle(head):
    slow = head
    fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

        if slow is fast:
            return True

    return False
```

### Why compare with `is`?

We want to determine whether both pointers refer to the exact same node, not merely whether two nodes contain equal values.

### Complexity

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 4. Finding the Start of a Cycle

### Problem statement

If a linked list contains a cycle, return the node where the cycle begins. Otherwise, return `None`.

### Implementation

```python
def detect_cycle(head):
    slow = head
    fast = head

    # Phase 1: Find a meeting point inside the cycle.
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

        if slow is fast:
            break
    else:
        return None

    # Phase 2: Find the cycle's starting node.
    slow = head

    while slow is not fast:
        slow = slow.next
        fast = fast.next

    return slow
```

### Explanation

Once the two pointers meet inside the cycle:

1. Move the slow pointer back to the head.
2. Keep the fast pointer at the meeting point.
3. Move both pointers one step at a time.
4. Their next meeting point is the start of the cycle.

This is Floyd's cycle-finding algorithm.

### Complexity

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 5. Finding the Nth Node from the End

### Problem statement

Given a linked list and an integer `n`, return the Nth node from the end.

Example:

```text
1 -> 2 -> 3 -> 4 -> 5
```

For `n = 2`, return the node containing `4`.

### Approach

Maintain a gap of `n` nodes between the fast and slow pointers.

1. Move the fast pointer `n` steps forward.
2. Move both pointers together until fast reaches the end.
3. Slow now points to the Nth node from the end.

### Implementation

```python
def nth_from_end(head, n):
    if n <= 0:
        return None

    fast = head
    slow = head

    for _ in range(n):
        if fast is None:
            return None
        fast = fast.next

    while fast:
        slow = slow.next
        fast = fast.next

    return slow
```

This version returns `None` if `n` is invalid or exceeds the list length.

### Complexity

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 6. Detecting a Happy Number

### Problem statement

A number is happy if repeatedly replacing it with the sum of the squares of its digits eventually produces `1`.

Example:

```text
19 -> 82 -> 68 -> 100 -> 1
```

Therefore, `19` is a happy number.

An unhappy number eventually enters a repeating cycle.

We can detect this cycle using fast and slow pointers.

### Implementation

```python
def next_number(n):
    total = 0

    while n > 0:
        digit = n % 10
        total += digit * digit
        n //= 10

    return total


def is_happy(n):
    if n <= 0:
        return False

    slow = n
    fast = n

    while True:
        slow = next_number(slow)
        fast = next_number(next_number(fast))

        if fast == 1:
            return True

        if slow == fast:
            return False


print(is_happy(19))
print(is_happy(2))
```

Output:

```text
True
False
```

### Complexity

For a fixed-width integer domain, the digit-processing operation takes time proportional to the number of digits. The pointer algorithm uses `O(1)` auxiliary space.

## 7. Finding a Duplicate Number with Floyd's Algorithm

### Problem statement

An array contains `n + 1` integers, where every value is between `1` and `n`. At least one value is duplicated.

Find a duplicate without modifying the array and using constant extra space.

Example:

```python
nums = [1, 3, 4, 2, 2]
```

Output:

```text
2
```

### Important idea

Treat every array value as a pointer to another index.

```python
index -> nums[index]
```

This creates a linked structure with a cycle. Floyd's algorithm can find the cycle's entrance, which corresponds to a duplicate value.

### Implementation

```python
def find_duplicate(nums):
    slow = nums[0]
    fast = nums[0]

    # Phase 1: Find a meeting point.
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]

        if slow == fast:
            break

    # Phase 2: Find the cycle entrance.
    slow = nums[0]

    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]

    return slow


print(find_duplicate([1, 3, 4, 2, 2]))
```

Output:

```text
2
```

### Complexity

- Time: `O(N)`
- Auxiliary space: `O(1)`

The solution relies on the array constraints stated in the problem.

## 8. Fast and Slow Pointers vs. Other Patterns

| Pattern | Typical use |
|---|---|
| Fast and slow pointers | Linked-list cycles and middle nodes |
| Two pointers from opposite ends | Sorted arrays and pair-sum problems |
| Sliding window | Contiguous subarrays or substrings |
| Monotonic stack | Next greater or smaller elements |
| Hash set | Simple cycle detection with extra memory |

## 9. Common Mistakes

1. Forgetting to check `fast` and `fast.next` before advancing two steps.
2. Comparing node values instead of node identity when detecting linked-list cycles.
3. Moving both pointers at the same speed during cycle detection.
4. Forgetting to handle an empty list.
5. Incorrectly handling invalid values of `n` in the Nth-from-end problem.
6. Assuming every array can be treated as a linked structure without verifying its constraints.
7. Returning the meeting point instead of the cycle entrance when the problem asks for the starting node.

## 10. Interview Practice Questions

Solve these problems in order:

1. Middle of the Linked List.
2. Linked List Cycle.
3. Linked List Cycle II.
4. Remove Nth Node From End of List.
5. Happy Number.
6. Find the Duplicate Number.
7. Palindrome Linked List.
8. Reorder List.
9. Circular Array Loop.
10. Linked List Cycle Detection with Different Pointer Speeds.

## 11. Final Interview Checklist

Make sure you can:

- Explain why fast and slow pointers can detect cycles.
- Find the middle node in one traversal.
- Find the starting node of a cycle.
- Maintain a fixed pointer gap to find a node from the end.
- Explain why these approaches use constant auxiliary space.
- Identify the constraints required for the duplicate-number technique.

**Key takeaway:** Fast and slow pointers are especially powerful when a sequence behaves like a linked structure and you need to find its middle, detect a cycle, or locate a cycle's entrance without storing every visited node.
