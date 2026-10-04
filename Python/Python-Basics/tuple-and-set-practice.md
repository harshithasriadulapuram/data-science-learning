
# Python Tuple and Set Practice

## 1. Tuples in Python

A tuple is an ordered collection that cannot be modified after creation.

```python
student = ("Harshitha", 22, "Data Science")

print(student[0])  # Harshitha
print(student[1])  # 22
```

### Create a Tuple with One Element

A comma is required for a single-element tuple.

```python
single = (10,)
print(type(single))  # <class 'tuple'>
```

### Tuple Unpacking

```python
student = ("Harshitha", 22, "Python")
name, age, course = student

print(name)
print(age)
print(course)
```

### Swap Two Variables

```python
a = 10
b = 20

a, b = b, a

print(a, b)  # 20 10
```

### Count and Find an Element

```python
numbers = (1, 2, 2, 3, 4)

print(numbers.count(2))  # 2
print(numbers.index(3))  # 3
```

## 2. Sets in Python

A set is an unordered collection of unique, hashable elements.

```python
numbers = {1, 2, 2, 3, 4}
print(numbers)  # {1, 2, 3, 4}
```

Set display order is not guaranteed.

### Add and Remove Elements

```python
numbers = {1, 2, 3}

numbers.add(4)
numbers.discard(2)

print(numbers)  # {1, 3, 4}
```

`discard()` does not raise an error if the element is absent.

### Union

Returns elements from either set.

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a | b)  # {1, 2, 3, 4, 5}
```

### Intersection

Returns elements common to both sets.

```python
print(a & b)  # {3}
```

### Difference

Returns elements present in the first set but not the second.

```python
print(a - b)  # {1, 2}
```

### Symmetric Difference

Returns elements present in either set, but not both.

```python
print(a ^ b)  # {1, 2, 4, 5}
```

## 3. Remove Duplicates from a List

```python
numbers = [1, 2, 2, 3, 1, 4]

unique_numbers = list(set(numbers))
print(unique_numbers)
```

The order may change. To preserve the original order:

```python
unique_numbers = list(dict.fromkeys(numbers))
print(unique_numbers)  # [1, 2, 3, 4]
```

## 4. Check Whether a Set Is a Subset

```python
a = {1, 2}
b = {1, 2, 3, 4}

print(a.issubset(b))  # True
print(b.issuperset(a))  # True
```

## 5. Find Common Characters in Two Strings

```python
first = "python"
second = "typhoon"

common = set(first) & set(second)
print(common)
```

The result is a set, so its order is not guaranteed.

## 6. Find Missing Numbers

Find which numbers from 1 through 5 are missing.

```python
numbers = [1, 2, 4, 5]
expected = set(range(1, 6))

missing = expected - set(numbers)
print(missing)  # {3}
```

## 7. Tuple vs List vs Set

| Feature | Tuple | List | Set |
|---|---|---|---|
| Ordered | Yes | Yes | No guaranteed order |
| Mutable | No | Yes | Yes |
| Duplicates | Allowed | Allowed | Not retained |
| Indexing | Yes | Yes | No |
| Syntax | `(1, 2)` | `[1, 2]` | `{1, 2}` |

## 8. Common Interview Questions

1. Why are tuples immutable?
2. What is the difference between a tuple and a list?
3. Why can't a set contain duplicate elements?
4. Can a tuple contain a list?
5. Why can't a list be a set element?
6. What is the difference between `remove()` and `discard()`?
7. Explain union, intersection and difference.
8. How can you remove duplicates while preserving order?

## Practice Challenges

1. Find the union and intersection of two lists.
2. Find all unique vowels in a string.
3. Check whether one set is a subset of another.
4. Find missing values in a sequence.
5. Convert a tuple of pairs into a dictionary.
