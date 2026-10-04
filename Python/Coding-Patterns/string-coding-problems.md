# Python String Coding Problems

## 1. Reverse a string
```python
def reverse_string(text: str) -> str:
    return text[::-1]

print(reverse_string("python"))  # nohtyp
```
**Concept:** Slicing with a step of `-1` reads the string backward.

## 2. Check whether a string is a palindrome
```python
def is_palindrome(text: str) -> bool:
    cleaned = text.casefold()
    return cleaned == cleaned[::-1]

print(is_palindrome("Madam"))  # True
```
This version ignores letter case but does not remove spaces or punctuation.

## 3. Count vowels and consonants
```python
def count_vowels_consonants(text: str) -> tuple[int, int]:
    vowels = set("aeiou")
    vowel_count = 0
    consonant_count = 0

    for char in text.casefold():
        if "a" <= char <= "z":
            if char in vowels:
                vowel_count += 1
            else:
                consonant_count += 1

    return vowel_count, consonant_count

print(count_vowels_consonants("Hello World"))  # (3, 7)
```
Only English letters A-Z are counted; digits, spaces, and punctuation are ignored.

## 4. Count the frequency of each character
```python
from collections import Counter


def character_frequency(text: str) -> dict[str, int]:
    return dict(Counter(text))

print(character_frequency("hello"))
# {'h': 1, 'e': 1, 'l': 2, 'o': 1}
```

## 5. Find the first non-repeating character
```python
def first_non_repeating(text: str) -> str | None:
    for char in text:
        if text.count(char) == 1:
            return char
    return None

print(first_non_repeating("aabbcddee"))  # c
```
This straightforward solution can take O(n²) time because `count()` scans the string repeatedly. Try improving it with `Counter` for O(n) average-time processing.

## 6. Remove duplicate characters while preserving order
```python
def remove_duplicate_chars(text: str) -> str:
    seen = set()
    result = []

    for char in text:
        if char not in seen:
            seen.add(char)
            result.append(char)

    return "".join(result)

print(remove_duplicate_chars("programming"))  # progamin
```

## 7. Check whether two strings are anagrams
Anagrams contain the same characters with the same frequencies, ignoring order.

```python
from collections import Counter


def are_anagrams(first: str, second: str) -> bool:
    return Counter(first.casefold()) == Counter(second.casefold())

print(are_anagrams("listen", "silent"))  # True
```
This version ignores case but treats spaces and punctuation as characters.

## 8. Count words in a sentence
```python
def count_words(sentence: str) -> int:
    return len(sentence.split())

print(count_words("Python is easy to learn"))  # 5
```
`split()` without an argument handles repeated whitespace and leading or trailing spaces.

## 9. Capitalize the first letter of every word
```python
def capitalize_words(text: str) -> str:
    return text.title()

print(capitalize_words("learn python programming"))
# Learn Python Programming
```
Note that `title()` can capitalize text differently from some names and abbreviations; it is not a universal name-formatting solution.

## 10. Find the longest word
```python
def longest_word(sentence: str) -> str | None:
    words = sentence.split()
    return max(words, key=len) if words else None

print(longest_word("Python makes coding enjoyable"))  # enjoyable
```
If multiple words tie for the greatest length, this returns the first one.

## 11. Check whether a string contains only digits
```python
def contains_only_digits(text: str) -> bool:
    return text.isdigit()

print(contains_only_digits("12345"))  # True
print(contains_only_digits("12a45"))  # False
```
An empty string returns `False`. Python's `isdigit()` recognizes some Unicode digit characters too, not just ASCII 0-9.

## 12. Reverse the order of words
```python
def reverse_word_order(sentence: str) -> str:
    return " ".join(sentence.split()[::-1])

print(reverse_word_order("I love Python"))  # Python love I
```
This normalizes whitespace to single spaces.

## Practice checklist
- [ ] Reverse a string
- [ ] Check a palindrome
- [ ] Count vowels and consonants
- [ ] Count character frequency
- [ ] Find the first non-repeating character
- [ ] Remove duplicate characters
- [ ] Check anagrams
- [ ] Count words
- [ ] Capitalize words
- [ ] Find the longest word
- [ ] Check whether text contains only digits
- [ ] Reverse word order

## Interview tips
1. Clarify whether the solution should ignore case, spaces, or punctuation.
2. Consider empty strings and repeated characters.
3. Explain the time complexity of your approach.
4. Practise solving each problem without looking at the solution.
5. Test your function with normal and boundary cases.
