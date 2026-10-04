# Python Data Structures

Data structures organize and store data so that programs can access and modify it efficiently.

## 1. Lists

A list is ordered, mutable, and allows duplicate values.

```python
numbers = [10, 20, 30, 20]
print(numbers[0])       # 10
print(numbers[-1])      # 20
numbers.append(40)
numbers.insert(1, 15)
numbers.remove(20)      # Removes the first matching value
last = numbers.pop()    # Removes and returns the last item
print(numbers)
```

### List slicing

```python
values = [10, 20, 30, 40, 50]
print(values[1:4])  # [20, 30, 40]
print(values[:3])   # [10, 20, 30]
print(values[::2])  # [10, 30, 50]
```

The stop index is excluded.

### List comprehension

```python
squares = [n * n for n in range(1, 6)]
print(squares)  # [1, 4, 9, 16, 25]
```

## 2. Tuples

A tuple is ordered and immutable. It can contain duplicate values.

```python
point = (10, 20)
print(point[0])
x, y = point  # Unpacking
print(x, y)
```

A one-item tuple needs a trailing comma: `single = (5,)`.

Use tuples for fixed collections of values that should not be changed.

## 3. Sets

A set stores unique elements. It does not support indexing, and it does not guarantee a meaningful positional order.

```python
numbers = {1, 2, 2, 3}
print(numbers)  # Contains 1, 2, and 3
numbers.add(4)
numbers.discard(2)
```

An empty set is created with `set()`, not `{}`. Empty curly braces create a dictionary.

### Set operations

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a | b)  # Union: {1, 2, 3, 4, 5}
print(a & b)  # Intersection: {3}
print(a - b)  # Difference: {1, 2}
print(a ^ b)  # Symmetric difference: {1, 2, 4, 5}
```

## 4. Dictionaries

A dictionary stores key-value pairs. Keys must be hashable and unique.

```python
student = {
    "name": "Harshitha",
    "age": 22,
    "course": "Data Science"
}

print(student["name"])
print(student.get("marks", 0))
student["marks"] = 90
student.update({"age": 23})
```

`student["missing"]` raises `KeyError` if the key is absent. `get()` can return a default instead.

### Looping through a dictionary

```python
for key, value in student.items():
    print(key, value)
```

### Dictionary comprehension

```python
squares = {n: n * n for n in range(1, 5)}
print(squares)
```

## 5. Comparing the Four Structures

| Structure | Ordered | Mutable | Duplicates |
|---|---|---|---|
| List | Yes | Yes | Yes |
| Tuple | Yes | No | Yes |
| Set | No positional order | Yes | No duplicate elements |
| Dictionary | Insertion order | Yes | Keys are unique |

## 6. Useful Built-in Functions

```python
numbers = [4, 2, 8, 1]
print(len(numbers))   # 4
print(min(numbers))   # 1
print(max(numbers))   # 8
print(sum(numbers))   # 15
print(sorted(numbers))  # [1, 2, 4, 8]
```

`sorted()` returns a new sorted list; `list.sort()` sorts a list in place.

## 7. Choosing a Data Structure

- Use a list when you need an ordered, changeable sequence.
- Use a tuple for a fixed group of values.
- Use a set when uniqueness or membership checks matter.
- Use a dictionary when you need to look up values by keys.

## 8. Practice Problems

1. Find the largest and second-largest distinct values in a list.
2. Remove duplicates from a list while preserving order.
3. Count the frequency of each element using a dictionary.
4. Find common elements between two lists using sets.
5. Group words by their first letter using a dictionary.
6. Find the key with the largest value in a dictionary.
7. Flatten a one-level nested list.
8. Count how many unique words appear in a sentence.

## Key Takeaways

Understand each structure's behavior, common operations, and when to use it. Practise writing the code yourself instead of only memorizing examples.
