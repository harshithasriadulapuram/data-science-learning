# Python List Coding Problems

## 1. Find the largest element
```python
def find_largest(numbers: list[int]) -> int:
    if not numbers:
        raise ValueError("List must not be empty")
    return max(numbers)

print(find_largest([10, 25, 7, 40, 15]))  # 40
```
**Practice:** Solve this without using `max()`.

## 2. Find the second-largest distinct element
```python
def second_largest(numbers: list[int]) -> int:
    unique = set(numbers)
    if len(unique) < 2:
        raise ValueError("Need at least two distinct values")
    unique.remove(max(unique))
    return max(unique)

print(second_largest([10, 25, 7, 40, 25]))  # 25
```

## 3. Calculate the sum of all elements
```python
def list_sum(numbers: list[int]) -> int:
    total = 0
    for number in numbers:
        total += number
    return total

print(list_sum([1, 2, 3, 4]))  # 10
```

## 4. Remove duplicates while preserving order
```python
def remove_duplicates(numbers: list[int]) -> list[int]:
    seen = set()
    result = []
    for number in numbers:
        if number not in seen:
            seen.add(number)
            result.append(number)
    return result

print(remove_duplicates([1, 2, 2, 3, 1, 4]))  # [1, 2, 3, 4]
```

## 5. Reverse a list
```python
def reverse_list(items: list) -> list:
    return items[::-1]

print(reverse_list([1, 2, 3, 4]))  # [4, 3, 2, 1]
```
This creates a reversed copy rather than changing the original list.

## 6. Count even and odd numbers
```python
def count_even_odd(numbers: list[int]) -> tuple[int, int]:
    even = sum(number % 2 == 0 for number in numbers)
    odd = len(numbers) - even
    return even, odd

print(count_even_odd([1, 2, 3, 4, 6]))  # (3, 2)
```

## 7. Find common elements between two lists
```python
def common_elements(first: list, second: list) -> list:
    second_set = set(second)
    result = []
    seen = set()
    for item in first:
        if item in second_set and item not in seen:
            result.append(item)
            seen.add(item)
    return result

print(common_elements([1, 2, 3, 2], [2, 3, 4]))  # [2, 3]
```
This returns unique common elements in the order of their first appearance in `first`.

## 8. Find missing number from 1 to n
Assume the list contains distinct integers from 1 through n, with exactly one number missing.

```python
def find_missing(numbers: list[int], n: int) -> int:
    expected = n * (n + 1) // 2
    return expected - sum(numbers)

print(find_missing([1, 2, 4, 5], 5))  # 3
```
The function assumes the input satisfies the stated conditions; it does not validate them.

## 9. Move all zeros to the end
```python
def move_zeros(numbers: list[int]) -> list[int]:
    nonzero = [number for number in numbers if number != 0]
    zeros = [0] * (len(numbers) - len(nonzero))
    return nonzero + zeros

print(move_zeros([0, 1, 0, 3, 12]))  # [1, 3, 12, 0, 0]
```
The relative order of nonzero elements is preserved.

## 10. Rotate a list to the right by k positions
```python
def rotate_right(items: list, k: int) -> list:
    if not items:
        return []
    k %= len(items)
    return items[-k:] + items[:-k] if k else items[:]

print(rotate_right([1, 2, 3, 4, 5], 2))  # [4, 5, 1, 2, 3]
```

## 11. Flatten a one-level nested list
```python
def flatten_once(nested: list[list[int]]) -> list[int]:
    return [item for sublist in nested for item in sublist]

print(flatten_once([[1, 2], [3, 4], [5]]))  # [1, 2, 3, 4, 5]
```
This flattens one level only, not arbitrarily deep nesting.

## 12. Find the frequency of each element
```python
from collections import Counter


def element_frequency(items: list) -> dict:
    return dict(Counter(items))

print(element_frequency([1, 2, 2, 3, 1, 1]))
# {1: 3, 2: 2, 3: 1}
```
The elements must be hashable for `Counter` to count them.

## Practice checklist
- [ ] Find the largest element without `max()`
- [ ] Find the second-largest distinct element
- [ ] Sum list elements
- [ ] Remove duplicates while preserving order
- [ ] Reverse a list
- [ ] Count even and odd numbers
- [ ] Find common elements
- [ ] Find a missing number
- [ ] Move zeros to the end
- [ ] Rotate a list
- [ ] Flatten a nested list by one level
- [ ] Count element frequencies

## Interview tips
1. Ask whether duplicates are allowed and whether order must be preserved.
2. Test empty lists and lists containing one element.
3. Know when a set or dictionary can improve lookup time.
4. Explain time and space complexity.
5. Practise implementing the logic without relying on built-in shortcuts.
