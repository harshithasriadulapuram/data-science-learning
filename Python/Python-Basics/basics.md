# Python Basics

## 1. What Is Python?

Python is a high-level, general-purpose programming language.
It is widely used in software development, automation,
Data Science, Machine Learning, and Generative AI.

## 2. Variables

Variables store references to values.

```python
name = "Harshitha"
age = 22
height = 5.0

print(name)
print(age)
print(height)
```

## 3. Basic Data Types

- int: Whole numbers, such as 10
- float: Decimal numbers, such as 3.14
- str: Text, such as "Python"
- bool: True or False
- list: Ordered, mutable collection
- tuple: Ordered, immutable collection
- set: Collection of unique elements
- dict: Key-value pairs

Example:

```python
number = 10
price = 99.5
language = "Python"
is_learning = True

print(type(number))
print(type(price))
print(type(language))
print(type(is_learning))
```

## 4. Taking User Input

The input() function reads user input as a string.

```python
name = input("Enter your name: ")
print("Hello,", name)
```

Convert input when you need a number:

```python
age = int(input("Enter your age: "))
print("Next year, you will be", age + 1)
```

## 5. Type Conversion

Type conversion changes a value from one type to another.

```python
number = "100"

converted_number = int(number)

print(converted_number + 50)
print(type(converted_number))
```

Common conversion functions:
- int()
- float()
- str()
- bool()
- list()
- tuple()
- set()

Not every value can be converted successfully.

## 6. Operators

### Arithmetic Operators

```python
a = 10
b = 3

print(a + b)   # Addition
print(a - b)   # Subtraction
print(a * b)   # Multiplication
print(a / b)   # Division
print(a // b)  # Floor division
print(a % b)   # Remainder
print(a ** b)  # Exponentiation
```

### Comparison Operators

```python
a = 10
b = 5

print(a == b)
print(a != b)
print(a > b)
print(a < b)
print(a >= b)
print(a <= b)
```

Comparison expressions return True or False.

### Logical Operators

```python
age = 22
has_id = True

print(age >= 18 and has_id)
print(age < 18 or has_id)
print(not has_id)
```

## 7. Strings

Strings represent text.

```python
language = "Python"

print(language[0])
print(language[-1])
print(language[0:3])
print(len(language))
print(language.upper())
print(language.lower())
```

Python indexing starts at 0.
Negative indexing counts from the end.
Slicing uses a start index and an exclusive stop index.

## 8. Comments

Comments explain code and are ignored by Python.

```python
# This is a single-line comment

age = 22  # This stores an age
```

## 9. Useful Built-in Functions

```python
numbers = [10, 20, 30, 40]

print(len(numbers))
print(sum(numbers))
print(min(numbers))
print(max(numbers))
print(sorted(numbers))
```

## 10. Practice Problems

Try solving these without looking at solutions:

1. Store your name, age, and favorite language in variables.
2. Take two numbers as input and print their sum.
3. Convert a string containing a number into an integer.
4. Check whether a number is even or odd using the % operator.
5. Swap two variables.
6. Calculate the area of a rectangle.
7. Convert Celsius temperature into Fahrenheit.
8. Reverse a string using slicing.
9. Count the characters in a string.
10. Calculate the average of three numbers.

## Key Takeaway

Understand variables, data types, input/output, type conversion,
operators, and strings before moving to control flow.

The best way to learn Python is to type, run, modify, and
debug code yourself.
