# Python Comprehensions

Comprehensions provide a concise way to create lists, sets, and dictionaries from iterable objects.

## 1. List Comprehension

Syntax:

```python
[expression for item in iterable]
```

Example:

```python
numbers = [1, 2, 3, 4, 5]
squares = [number ** 2 for number in numbers]
print(squares)  # [1, 4, 9, 16, 25]
```

## 2. List Comprehension with a Condition

Syntax:

```python
[expression for item in iterable if condition]
```

Example:

```python
even_numbers = [n for n in range(1, 11) if n % 2 == 0]
print(even_numbers)  # [2, 4, 6, 8, 10]
```

## 3. if-else Inside a Comprehension

Use a conditional expression when you want to choose the output value for every item.

```python
labels = ["even" if n % 2 == 0 else "odd" for n in range(1, 6)]
print(labels)  # ['odd', 'even', 'odd', 'even', 'odd']
```

Remember the difference:

- `expression for item in iterable if condition` filters items.
- `expression_if_true if condition else expression_if_false for item in iterable` chooses a value for each item.

## 4. String Comprehension Examples

```python
word = "Python"
letters = [character.upper() for character in word]
print(letters)  # ['P', 'Y', 'T', 'H', 'O', 'N']

vowels = [character for character in word.lower() if character in "aeiou"]
print(vowels)  # ['o']
```

## 5. Nested List Comprehensions

A comprehension can contain nested loops.

```python
pairs = [(x, y) for x in [1, 2] for y in [3, 4]]
print(pairs)
# [(1, 3), (1, 4), (2, 3), (2, 4)]
```

This is equivalent to an outer loop over `x` and an inner loop over `y`.

## 6. Flatten a Nested List

```python
matrix = [[1, 2], [3, 4], [5, 6]]
flat = [number for row in matrix for number in row]
print(flat)  # [1, 2, 3, 4, 5, 6]
```

Read the loops from left to right: for each row, process each number in that row.

## 7. Set Comprehension

Set comprehensions create a set of unique results.

```python
numbers = [1, 2, 2, 3, 3, 4]
squares = {number ** 2 for number in numbers}
print(squares)  # Contains 1, 4, 9, and 16
```

A set does not preserve duplicate elements or provide positional indexing.

## 8. Dictionary Comprehension

Syntax:

```python
{key_expression: value_expression for item in iterable}
```

Example:

```python
squares = {n: n ** 2 for n in range(1, 6)}
print(squares)
# {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

## 9. Dictionary Comprehension with a Condition

```python
scores = {"Asha": 85, "Ravi": 42, "Maya": 91}
passed = {name: score for name, score in scores.items() if score >= 50}
print(passed)  # {'Asha': 85, 'Maya': 91}
```

## 10. Using zip() in a Comprehension

`zip()` combines corresponding items from iterables.

```python
names = ["Asha", "Ravi", "Maya"]
scores = [85, 42, 91]
result = {name: score for name, score in zip(names, scores)}
print(result)
```

By default, `zip()` stops when the shortest iterable ends.

## 11. Using enumerate()

`enumerate()` provides each item with an index.

```python
fruits = ["apple", "banana", "mango"]
indexed = [(index, fruit) for index, fruit in enumerate(fruits)]
print(indexed)
```

## 12. Generator Expressions

A generator expression uses parentheses and produces values lazily rather than building a list immediately.

```python
squares = (n ** 2 for n in range(1, 6))
for square in squares:
    print(square)
```

A generator is useful when you want to process values one at a time.

## 13. When Not to Use Comprehensions

Use a normal loop when the logic is complicated, requires multiple statements, or becomes difficult to read.

```python
results = []
for number in range(1, 6):
    square = number ** 2
    if square > 10:
        results.append(square)
```

Readable code is more important than making every expression short.

## Practice Problems

1. Create a list of squares from 1 to 20.
2. Filter multiples of 3 from a list.
3. Convert a list of names to uppercase.
4. Create a dictionary mapping each number from 1 to 10 to its cube.
5. Remove duplicate values using a set comprehension.
6. Flatten a two-dimensional list.
7. Create a dictionary containing only students who passed.
8. Use `zip()` to combine two lists into a dictionary.
9. Extract all vowels from a string.
10. Create a generator expression for the squares of even numbers.

## Key Takeaways

- List comprehensions create lists.
- Set comprehensions create sets of unique values.
- Dictionary comprehensions create key-value mappings.
- Conditions can filter items or select output values.
- Nested comprehensions are useful but should remain readable.
- Generator expressions produce values lazily.
