# Python Strings

A string is a sequence of characters enclosed in single, double, or triple quotes. Strings are immutable, meaning their characters cannot be changed in place.

## 1. Creating Strings

```python
name = "Harshitha"
message = 'Hello, Python!'
paragraph = """This is a
multiline string."""
```

## 2. Indexing

Python uses zero-based indexing. Negative indexes count from the end.

```python
text = "Python"
print(text[0])   # P
print(text[2])   # t
print(text[-1])  # n
```

## 3. Slicing

Syntax: `string[start:stop:step]`. The stop index is excluded.

```python
text = "Python Programming"
print(text[0:6])   # Python
print(text[:6])    # Python
print(text[7:])    # Programming
print(text[::2])   # Every second character
print(text[::-1])  # Reverse the string
```

## 4. Immutability

You cannot assign directly to an individual string character.

```python
word = "cat"
# word[0] = "b"  # Raises TypeError
word = "b" + word[1:]
print(word)  # bat
```

The example creates a new string and reassigns the variable.

## 5. Common String Methods

```python
text = "  Hello Python  "
print(text.lower())
print(text.upper())
print(text.strip())
print(text.replace("Python", "World"))
print(text.find("Python"))
print(text.count("o"))
print(text.startswith("  Hello"))
print(text.endswith("  "))
```

`find()` returns `-1` if a substring is not found. `count()` counts non-overlapping occurrences.

## 6. split() and join()

```python
sentence = "Python is easy to practise"
words = sentence.split()
print(words)

result = "-".join(words)
print(result)  # Python-is-easy-to-practise
```

`split()` divides a string into a list. `join()` combines strings from an iterable using the chosen separator.

## 7. Checking Characters

```python
print("Python".isalpha())     # True
print("123".isdigit())        # True
print("Python123".isalnum())  # True
print("hello".islower())      # True
print("HELLO".isupper())      # True
```

These methods return Boolean values.

## 8. String Formatting

### f-strings

```python
name = "Harshitha"
score = 95
print(f"{name} scored {score} marks.")
```

### Formatting numbers

```python
price = 123.456
print(f"Price: {price:.2f}")  # Price: 123.46
```

## 9. Escape Characters

```python
print("Hello\nWorld")  # New line
print("Hello\tWorld")  # Tab
print("It\'s Python")
```

A raw string, such as `r"C:\new\test"`, treats backslashes as literal characters for most escape sequences.

## 10. Useful Operations

```python
a = "Data"
b = "Science"
print(a + " " + b)  # Concatenation
print("ha" * 3)     # hahaha
print("Data" in "Data Science")  # True
print(len("Python"))  # 6
```

## 11. Reverse a String

```python
text = "python"
reversed_text = text[::-1]
print(reversed_text)  # nohtyp
```

## 12. Count Vowels

```python
def count_vowels(text):
    vowels = "aeiou"
    count = 0
    for char in text.lower():
        if char in vowels:
            count += 1
    return count

print(count_vowels("Hello World"))  # 3
```

## 13. Check a Palindrome

A palindrome reads the same forward and backward.

```python
def is_palindrome(text):
    text = text.lower()
    return text == text[::-1]

print(is_palindrome("madam"))  # True
print(is_palindrome("python"))  # False
```

This simple version does not ignore spaces or punctuation.

## 14. Common Mistakes

- Trying to modify a string character directly.
- Forgetting that indexes start at zero.
- Expecting the stop index in a slice to be included.
- Confusing `find()` with `index()`; `index()` raises `ValueError` when no match exists.
- Forgetting that string methods usually return a new string rather than modifying the original.

## Practice Problems

1. Reverse a string without using slicing.
2. Count vowels and consonants in a string.
3. Check whether a string is a palindrome, ignoring spaces and punctuation.
4. Count the frequency of each character.
5. Find the first non-repeating character.
6. Check whether two strings are anagrams.
7. Remove duplicate characters while preserving order.
8. Find the longest word in a sentence.
9. Count words in a sentence.
10. Convert a sentence to title case.

## Key Takeaways

- Strings are ordered and immutable.
- Indexing accesses individual characters; slicing extracts portions.
- Methods such as `lower()`, `strip()`, `split()`, and `join()` are frequently useful.
- Practise writing string solutions using loops and functions, not just built-in shortcuts.
