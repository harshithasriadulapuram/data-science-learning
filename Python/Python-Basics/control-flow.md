# Python Control Flow

Control flow determines the order in which Python statements execute.

## 1. if Statement

```python
age = 20

if age >= 18:
    print("Adult")
```

The condition is checked first. The indented block runs only when the condition is True.

## 2. if-else Statement

```python
number = 7

if number % 2 == 0:
    print("Even")
else:
    print("Odd")
```

## 3. if-elif-else

```python
marks = 85

if marks >= 90:
    print("Grade A")
elif marks >= 75:
    print("Grade B")
else:
    print("Grade C")
```

Python checks conditions from top to bottom and executes the first matching block.

## 4. for Loop

A `for` loop iterates over items in a sequence.

```python
for number in range(1, 6):
    print(number)
```

Output: 1, 2, 3, 4, 5 (each on a separate line).

`range(start, stop, step)` includes `start` but excludes `stop`.

```python
for number in range(2, 11, 2):
    print(number)
```

This prints the even numbers from 2 through 10.

## 5. while Loop

A `while` loop repeats as long as its condition is True.

```python
count = 1

while count <= 5:
    print(count)
    count += 1
```

Update the loop variable to avoid an unintended infinite loop.

## 6. break

`break` immediately exits the nearest loop.

```python
for number in range(1, 10):
    if number == 5:
        break
    print(number)
```

## 7. continue

`continue` skips the rest of the current iteration and moves to the next one.

```python
for number in range(1, 6):
    if number == 3:
        continue
    print(number)
```

## 8. pass

`pass` is a placeholder that does nothing.

```python
if True:
    pass
```

## 9. Nested Loops

A loop inside another loop is called a nested loop.

```python
for row in range(1, 4):
    for column in range(1, 3):
        print(row, column)
```

## 10. Common Mistakes

- Forgetting the colon `:` after `if`, `elif`, `else`, `for`, or `while`.
- Using incorrect indentation.
- Forgetting to update a `while` loop variable.
- Assuming `range(1, 5)` includes 5; it stops before 5.
- Using `=` for comparison instead of `==`.

## Practice Problems

1. Check whether a number is positive, negative, or zero.
2. Check whether a number is even or odd.
3. Print numbers from 1 to 100.
4. Calculate the sum of numbers from 1 to n.
5. Print the multiplication table of a number.
6. Find the factorial of a number using a loop.
7. Print all even numbers between 1 and 50.
8. Count the digits in an integer.
9. Reverse the digits of an integer using a loop.
10. Check whether a number is prime.

## Key Takeaways

- `if`, `elif`, and `else` control decisions.
- `for` loops iterate over sequences or iterables.
- `while` loops repeat while a condition is True.
- `break` exits a loop; `continue` skips to the next iteration; `pass` does nothing.
- Indentation defines Python code blocks.
