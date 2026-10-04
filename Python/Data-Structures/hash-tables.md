
# Hash Tables in Python

## 1. What Is a Hash Table?

A **hash table** is a data structure that stores data as key-value pairs and uses a hash function to determine where each key should be stored.

In Python, the built-in `dict` type implements a hash table.

```python
student = {
    "name": "Harshitha",
    "age": 22,
    "course": "Data Science"
}

print(student["name"])  # Harshitha
print(student["course"])  # Data Science
```

Here:
- `"name"`, `"age"`, and `"course"` are keys.
- `"Harshitha"`, `22`, and `"Data Science"` are values.

## 2. Why Do We Need Hash Tables?

Hash tables help us:
- Find values quickly using keys.
- Count how often an item appears.
- Remove duplicate values using related structures such as sets.
- Store and retrieve records.
- Solve coding interview problems efficiently.

Example: Counting the frequency of each number.

```python
numbers = [1, 2, 2, 3, 1, 2]

frequency = {}

for number in numbers:
    if number in frequency:
        frequency[number] += 1
    else:
        frequency[number] = 1

print(frequency)
# {1: 2, 2: 3, 3: 1}
```

## 3. How Does a Hash Table Work?

A hash table uses a **hash function** to convert a key into a hash value, which helps determine where the associated value belongs.

Conceptually:

1. You provide a key.
2. A hash function processes the key.
3. The hash table uses the result to locate a storage position.
4. The value is stored or retrieved.

For example:

```python
student = {"name": "Harshitha"}

print(student["name"])
```

Python manages the hashing and storage internally. You do not need to calculate the storage position yourself.

## 4. What Is a Hash Function?

A hash function produces a hash value from an object.

Python provides the built-in `hash()` function for hashable objects.

```python
print(hash("Python"))
print(hash(100))
print(hash((1, 2, 3)))
```

Important:
- Hash values for strings can differ between separate Python processes.
- Hashable objects must have a stable hash during their lifetime.
- Dictionary keys must be hashable.

## 5. What Is a Collision?

A **collision** occurs when two different keys produce the same hash value.

Collisions are a normal possibility in hash tables. Implementations use strategies to handle them.

Common strategies include:

### Separate Chaining

Multiple entries that map to the same location are grouped together, often using a linked structure.

### Open Addressing

If a position is occupied, the implementation searches for another available position according to a probing strategy.

Python dictionaries handle collisions internally. You generally do not implement these mechanisms when using a normal dictionary.

## 6. What Is a Load Factor?

The load factor describes how full a hash table is relative to its capacity.

Conceptually:

Load factor = Number of stored entries / Number of available slots

A higher load factor can increase collisions in many hash-table implementations. Implementations may resize their storage to maintain efficient operations.

Python manages dictionary resizing automatically.

## 7. Time Complexity

Typical average-case complexities for hash-table operations are:

| Operation | Average Time | Worst Case |
|---|---|---|
| Insert | O(1) | O(n) |
| Search by key | O(1) | O(n) |
| Delete by key | O(1) | O(n) |
| Update | O(1) | O(n) |

The average-case performance is constant time because the operation does not usually require scanning every entry. Worst-case performance depends on implementation details and collision behavior.

## 8. Creating, Reading, Updating, and Deleting

```python
# Create
student = {
    "name": "Harshitha",
    "score": 85
}

# Read
print(student["name"])

# Update
student["score"] = 92

# Insert a new key
student["course"] = "Data Science"

# Delete a key
del student["score"]

print(student)
```

Use `get()` when a key might not exist:

```python
student = {"name": "Harshitha"}

print(student.get("name"))       # Harshitha
print(student.get("score"))      # None
print(student.get("score", 0))   # 0
```

Using `student["score"]` when the key is absent raises a `KeyError`.

## 9. What Makes a Valid Dictionary Key?

Common hashable key types include:
- Integers
- Strings
- Booleans
- Tuples containing only hashable elements

Lists, sets, and dictionaries cannot be dictionary keys because they are mutable and unhashable.

```python
data = {
    10: "integer",
    "name": "string",
    (1, 2): "tuple"
}

print(data[(1, 2)])
```

This raises an error:

```python
# Invalid: a list is unhashable
data = {[1, 2]: "value"}
```

A tuple is not automatically hashable if it contains an unhashable element:

```python
# Invalid: the tuple contains a list
data = {(1, [2, 3]): "value"}
```

## 10. Iterating Through a Dictionary

```python
student = {
    "name": "Harshitha",
    "age": 22,
    "score": 92
}

# Keys
for key in student:
    print(key)

# Values
for value in student.values():
    print(value)

# Keys and values
for key, value in student.items():
    print(key, value)
```

## 11. Counting Frequencies with get()

The `get()` method makes frequency counting concise.

```python
words = ["apple", "banana", "apple", "orange", "banana", "apple"]

frequency = {}

for word in words:
    frequency[word] = frequency.get(word, 0) + 1

print(frequency)
# {'apple': 3, 'banana': 2, 'orange': 1}
```

## 12. Using defaultdict

The `defaultdict` class from `collections` supplies a default value for missing keys.

```python
from collections import defaultdict

frequency = defaultdict(int)

for number in [1, 2, 2, 3, 1, 2]:
    frequency[number] += 1

print(dict(frequency))
# {1: 2, 2: 3, 3: 1}
```

`int` produces `0` when a new key is accessed.

## 13. Checking Whether Two Strings Are Anagrams

Two strings are anagrams if they contain the same characters with the same frequencies.

```python
def are_anagrams(first, second):
    if len(first) != len(second):
        return False

    frequency = {}

    for character in first:
        frequency[character] = frequency.get(character, 0) + 1

    for character in second:
        if frequency.get(character, 0) == 0:
            return False

        frequency[character] -= 1

    return True


print(are_anagrams("listen", "silent"))  # True
print(are_anagrams("hello", "world"))    # False
```

## 14. Common Interview Questions

1. What is a hash table?
2. How does a hash function work?
3. What is a collision?
4. What are separate chaining and open addressing?
5. What is a load factor?
6. Why are dictionary lookups usually O(1) on average?
7. Why can't a list be used as a dictionary key?
8. What is the difference between a dictionary and a set?
9. How can you count element frequencies using a dictionary?
10. What happens when you access a missing dictionary key?

## 15. Practice Problems

Try solving these without looking at the examples:

1. Count the frequency of every character in a string.
2. Count the frequency of every number in a list.
3. Find the first non-repeating character in a string.
4. Find the first repeating number in a list.
5. Check whether two strings are anagrams.
6. Find the intersection of two lists using a set or dictionary.
7. Find two numbers that add up to a target value (Two Sum).
8. Group a list of words by their sorted characters to find anagram groups.

## Key Takeaways

- A hash table stores key-value pairs.
- Python's `dict` is a hash-table-based data structure.
- Hash functions help locate entries.
- Collisions are possible and are handled internally by Python dictionaries.
- Dictionary operations are typically O(1) on average.
- Dictionary keys must be hashable.
- Frequency counting and lookup problems are common hash-table applications.
