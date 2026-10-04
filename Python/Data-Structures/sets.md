
# Sets in Python

## 1. What Is a Set?

A **set** is a built-in Python data structure that stores unique elements.

Key characteristics:
- Duplicate elements are removed.
- Sets are unordered.
- Sets are mutable, so elements can be added or removed.
- Set elements must be hashable.
- Sets do not support indexing or slicing.

```python
numbers = {10, 20, 30, 20, 10}

print(numbers)  # {10, 20, 30}
```

The order of elements in the output is not guaranteed.

## 2. Creating a Set

```python
# Create a set using curly braces
numbers = {1, 2, 3, 4}

# Create an empty set
empty_set = set()

# Create a set from a list
numbers_from_list = set([1, 2, 2, 3, 3])

print(numbers)
print(empty_set)
print(numbers_from_list)
```

Important: `{}` creates an empty dictionary, not an empty set.

```python
print(type({}))       # <class 'dict'>
print(type(set()))    # <class 'set'>
```

## 3. Adding Elements

Use `add()` to insert one element and `update()` to insert multiple elements.

```python
fruits = {"apple", "banana"}

fruits.add("orange")
fruits.update(["mango", "grapes"])

print(fruits)
```

Adding an element that already exists does not create a duplicate.

```python
numbers = {1, 2, 3}
numbers.add(2)

print(len(numbers))  # 3
```

## 4. Removing Elements

Python provides several methods for removing elements.

```python
numbers = {10, 20, 30, 40}

numbers.remove(20)  # Raises KeyError if absent
numbers.discard(30) # Does nothing if absent

print(numbers)

numbers.pop()       # Removes and returns an arbitrary element
numbers.clear()     # Removes all elements
```

Use `discard()` when the element may not exist and you want to avoid a `KeyError`.

## 5. Membership Testing

The `in` operator checks whether an element exists in a set.

```python
numbers = {10, 20, 30, 40}

print(20 in numbers)      # True
print(100 in numbers)     # False
print(50 not in numbers)  # True
```

Membership testing is typically O(1) on average.

## 6. Set Union

The **union** combines elements from both sets, removing duplicates.

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a | b)
print(a.union(b))
# {1, 2, 3, 4, 5}
```

Use union when you need all unique elements from both collections.

## 7. Set Intersection

The **intersection** returns elements present in both sets.

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

print(a & b)
print(a.intersection(b))
# {3, 4}
```

Use intersection to find common elements.

## 8. Set Difference

The **difference** returns elements present in the first set but not the second.

```python
a = {1, 2, 3, 4}
b = {3, 4, 5}

print(a - b)  # {1, 2}
print(b - a)  # {5}
```

Difference is directional: `a - b` is not generally the same as `b - a`.

## 9. Symmetric Difference

The **symmetric difference** returns elements present in either set, but not in both.

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

print(a ^ b)
print(a.symmetric_difference(b))
# {1, 2, 5, 6}
```

## 10. Subsets and Supersets

A set `a` is a subset of `b` if every element in `a` also belongs to `b`.

```python
a = {1, 2}
b = {1, 2, 3, 4}

print(a.issubset(b))       # True
print(a <= b)              # True
print(b.issuperset(a))     # True
print(b >= a)              # True
```

A **proper subset** contains fewer elements than the set it belongs to.

```python
print(a < b)  # True
```

## 11. Disjoint Sets

Two sets are disjoint if they have no elements in common.

```python
a = {1, 2, 3}
b = {4, 5, 6}

print(a.isdisjoint(b))  # True
```

## 12. Removing Duplicates from a List

Sets provide a convenient way to remove duplicate values.

```python
numbers = [10, 20, 10, 30, 20, 40]

unique_numbers = list(set(numbers))

print(unique_numbers)
```

The resulting order is not guaranteed. If order matters, use:

```python
numbers = [10, 20, 10, 30, 20, 40]

unique_numbers = list(dict.fromkeys(numbers))

print(unique_numbers)  # [10, 20, 30, 40]
```

## 13. Finding Common Elements Between Lists

```python
list_a = [1, 2, 3, 4, 5]
list_b = [3, 4, 5, 6, 7]

common = set(list_a) & set(list_b)

print(common)  # {3, 4, 5}
```

If you need the result as a list:

```python
common_list = list(set(list_a) & set(list_b))
```

## 14. Set Comprehensions

A set comprehension creates a set using a compact expression.

```python
squares = {number ** 2 for number in range(1, 6)}

print(squares)  # {1, 4, 9, 16, 25}
```

Duplicate results are automatically removed.

```python
remainders = {number % 3 for number in range(10)}

print(remainders)  # {0, 1, 2}
```

## 15. Frozen Sets

A `frozenset` is an immutable set.

You can perform set operations on it, but you cannot add or remove elements after creation.

```python
values = frozenset([1, 2, 3, 3])

print(values)

# values.add(4)  # Raises AttributeError
```

Because a `frozenset` is hashable when its elements are hashable, it can be used as a dictionary key.

```python
groups = {
    frozenset({1, 2}): "Group A"
}

print(groups[frozenset({2, 1})])  # Group A
```

## 16. Time Complexity

Typical average-case complexities:

| Operation | Average Time |
|---|---|
| Add an element | O(1) |
| Remove an element | O(1) |
| Membership testing | O(1) |
| Union | O(len(a) + len(b)) |
| Intersection | Depends on set sizes and implementation |
| Difference | Depends on set sizes and implementation |

Individual operations can take longer in exceptional cases because of hashing and collisions.

## 17. Set vs List vs Tuple

| Feature | Set | List | Tuple |
|---|---|---|---|
| Ordered sequence | No | Yes | Yes |
| Allows duplicates | No | Yes | Yes |
| Mutable | Yes | Yes | No |
| Indexing | No | Yes | Yes |
| Membership test | O(1) average | O(n) | O(n) |
| Main use | Unique elements | Changeable sequence | Fixed sequence |

## 18. Common Interview Questions

1. What is a set in Python?
2. Why does a set remove duplicates?
3. What is the difference between `remove()` and `discard()`?
4. What is the difference between union and intersection?
5. Explain difference and symmetric difference.
6. What is a subset and a superset?
7. What is a `frozenset`?
8. Why can't a list be stored inside a set?
9. How do you remove duplicates from a list?
10. How can you find common elements between two lists?

## 19. Practice Problems

Try solving these without looking at the examples:

1. Remove duplicates from a list.
2. Find the common elements in two lists.
3. Find elements present in the first list but absent from the second.
4. Check whether two lists contain the same unique elements.
5. Find the union of three sets.
6. Find all unique vowels in a string.
7. Check whether two strings share any characters.
8. Find duplicate numbers in a list using a set.
9. Check whether one set is a subset of another.
10. Find the missing numbers between two collections.

## Key Takeaways

- Sets store unique elements.
- Sets are useful for membership checks and duplicate removal.
- Union, intersection, difference, and symmetric difference are fundamental operations.
- Set membership is typically O(1) on average.
- Use `frozenset` when an immutable set is required.
- Sets do not preserve a guaranteed element order.
