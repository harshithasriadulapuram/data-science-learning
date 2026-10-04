# Python Iterators and Generators

Iterators and generators let Python process values one at a time instead of requiring every value to be stored in a list.

## 1. What Is an Iterable?

An iterable is an object that can provide an iterator. Lists, tuples, strings, dictionaries, and sets are common examples.

```python
numbers = [10, 20, 30]
for number in numbers:
    print(number)
```

## 2. What Is an Iterator?

An iterator produces the next value when requested. It maintains its current position.

```python
numbers = [10, 20, 30]
iterator = iter(numbers)

print(next(iterator))  # 10
print(next(iterator))  # 20
print(next(iterator))  # 30
```

Calling `next()` again raises `StopIteration` because there are no more values.

## 3. Iterable vs Iterator

- Iterable: an object from which an iterator can be created.
- Iterator: an object that produces values one at a time.
- `iter(object)`: obtains an iterator.
- `next(iterator)`: requests the next value.

A list is iterable, but it is not itself an iterator.

## 4. Looping Through an Iterator

```python
colors = ["red", "green", "blue"]
iterator = iter(colors)

for color in iterator:
    print(color)
```

A `for` loop obtains an iterator and requests values until iteration is complete.

## 5. Creating a Custom Iterator

A custom iterator defines `__iter__()` and `__next__()`.

```python
class CountUpTo:
    def __init__(self, limit):
        self.current = 1
        self.limit = limit

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > self.limit:
            raise StopIteration
        value = self.current
        self.current += 1
        return value

for number in CountUpTo(3):
    print(number)
```

This prints 1, 2, and 3.

## 6. What Is a Generator?

A generator is a convenient way to create an iterator. A generator function uses `yield` to produce a value and pause its execution.

```python
def count_up_to(limit):
    number = 1
    while number <= limit:
        yield number
        number += 1

for value in count_up_to(3):
    print(value)
```

## 7. yield vs return

- `return` ends a function call and sends back a result.
- `yield` produces a value and suspends a generator so it can resume later.

```python
def normal_function():
    return 10

def generator_function():
    yield 10
    yield 20

print(normal_function())
print(list(generator_function()))
```

## 8. Generators Are Lazy

A generator computes values as they are requested rather than building the entire result in advance.

```python
def squares(limit):
    for number in range(limit):
        yield number ** 2

for square in squares(5):
    print(square)
```

This can reduce memory usage when processing large sequences, although the benefit depends on the workload.

## 9. Generator Expressions

Generator expressions provide a compact way to create generators.

```python
squares = (number ** 2 for number in range(1, 6))
print(next(squares))  # 1
print(next(squares))  # 4
```

Unlike a list comprehension, this expression does not immediately build a list of every result.

## 10. Handling StopIteration

A normal `for` loop handles the end of iteration automatically. When using `next()` directly, you can provide a default value.

```python
iterator = iter([1, 2])
print(next(iterator, None))  # 1
print(next(iterator, None))  # 2
print(next(iterator, None))  # None
```

## 11. Practical Data Processing Example

```python
def read_numbers(numbers):
    for number in numbers:
        if number >= 0:
            yield number

values = [4, -2, 7, -1, 0]
print(list(read_numbers(values)))  # [4, 7, 0]
```

This generator yields only non-negative values. Converting it to a list stores all yielded results; iterating directly processes them one at a time.

## 12. Common Mistakes

- Calling `next()` on an iterable that is not an iterator.
- Forgetting that an exhausted iterator is generally not reusable from the beginning.
- Expecting a generator to produce every value before iteration begins.
- Using `return` when you intended to yield several values over time.
- Converting a huge generator to a list unnecessarily, which uses memory for all the results.

## Practice Problems

1. Create an iterator from a list and retrieve its first two values.
2. Write a generator that yields numbers from 1 to n.
3. Write a generator that yields even numbers up to a limit.
4. Create a generator that yields squares of numbers.
5. Write a generator that reads a list and yields only positive values.
6. Build a custom iterator that counts down from n to 1.
7. Compare a list comprehension with a generator expression.
8. Write a generator that yields words from a sentence one at a time.

## Key Takeaways

- Iterables can provide iterators.
- Iterators produce values using `next()`.
- Generators use `yield` to produce values lazily.
- `for` loops manage iteration automatically.
- Lazy processing can be useful for large data streams and files.
