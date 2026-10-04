
# Python Dictionary and Set Coding Problems

These problems help practise Python dictionaries and sets for coding interviews.

## 1. Count Frequency of Each Element

```python
numbers = [1, 2, 2, 3, 1, 2]
frequency = {}

for number in numbers:
    frequency[number] = frequency.get(number, 0) + 1

print(frequency)
# {1: 2, 2: 3, 3: 1}
```

## 2. Count Character Frequencies

```python
text = "banana"
frequency = {}

for char in text:
    frequency[char] = frequency.get(char, 0) + 1

print(frequency)
# {'b': 1, 'a': 3, 'n': 2}
```

## 3. Find the First Non-Repeating Character

```python
text = "aabbcddee"
frequency = {}

for char in text:
    frequency[char] = frequency.get(char, 0) + 1

for char in text:
    if frequency[char] == 1:
        print(char)
        break
else:
    print("No non-repeating character")
# c
```

## 4. Remove Duplicates While Preserving Order

```python
numbers = [1, 2, 2, 3, 1, 4]
seen = set()
result = []

for number in numbers:
    if number not in seen:
        seen.add(number)
        result.append(number)

print(result)
# [1, 2, 3, 4]
```

## 5. Find Common Elements

```python
a = [1, 2, 3, 4]
b = [3, 4, 5, 6]

print(set(a) & set(b))
# {3, 4}
```

## 6. Find Elements in One List but Not Another

```python
a = [1, 2, 3, 4]
b = [3, 4, 5]

print(set(a) - set(b))
# {1, 2}
```

## 7. Check Whether Two Lists Have the Same Unique Elements

```python
a = [1, 2, 2, 3]
b = [3, 2, 1]

print(set(a) == set(b))
# True
```

This compares unique elements, not their frequencies.

## 8. Check Whether Two Strings Are Anagrams

```python
from collections import Counter

def are_anagrams(first, second):
    return Counter(first.lower()) == Counter(second.lower())

print(are_anagrams("listen", "silent"))
# True
```

## 9. Find the Most Frequent Element

```python
numbers = [1, 2, 2, 3, 2, 4, 4]
frequency = {}

for number in numbers:
    frequency[number] = frequency.get(number, 0) + 1

most_frequent = max(frequency, key=frequency.get)
print(most_frequent)
# 2
```

Assumes the list is not empty.

## 10. Two Sum Using a Dictionary

```python
def two_sum(numbers, target):
    seen = {}

    for index, number in enumerate(numbers):
        complement = target - number

        if complement in seen:
            return [seen[complement], index]

        seen[number] = index

    return []

print(two_sum([2, 7, 11, 15], 9))
# [0, 1]
```

Average time complexity: O(n).

## 11. Find Duplicate Elements

```python
numbers = [1, 2, 3, 2, 4, 1, 5]
seen = set()
duplicates = set()

for number in numbers:
    if number in seen:
        duplicates.add(number)
    else:
        seen.add(number)

print(duplicates)
# {1, 2}
```

## 12. Group Words by Their First Letter

```python
words = ["apple", "ant", "banana", "ball", "cat"]
groups = {}

for word in words:
    first_letter = word[0]
    groups.setdefault(first_letter, []).append(word)

print(groups)
# {'a': ['apple', 'ant'], 'b': ['banana', 'ball'], 'c': ['cat']}
```

Assumes all words are non-empty.

## Important Concepts

- Dictionary: stores key-value pairs.
- Set: stores unique, hashable elements.
- dict.get(key, default): returns a value or a default.
- set.add(value): adds an element to a set.
- set(a) & set(b): intersection.
- set(a) - set(b): difference.
- enumerate(iterable): provides indices and values.
- Average dictionary lookup and set membership: O(1).
- Counting n elements with a dictionary: O(n) average.

## Practice Challenges

1. Find the element with the second-highest frequency.
2. Find all pairs that sum to a target.
3. Check whether a list contains duplicates.
4. Find the intersection without using set intersection.
5. Group anagrams together.
