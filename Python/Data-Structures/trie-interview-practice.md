
# Trie (Prefix Tree) — Interview Practice

## 1. What Is a Trie?

A **Trie**, also called a **Prefix Tree**, is a tree-based data structure used to store and search strings efficiently.

It is especially useful when multiple words share common prefixes.

### Example

Consider these words:

- cat
- car
- care
- dog

The words `cat`, `car`, and `care` share the prefix `ca`.

A Trie stores shared prefixes in common paths instead of storing every word as a completely separate structure.

## 2. Applications of Tries

- Autocomplete and search suggestions
- Spell checkers
- Dictionary word lookup
- Prefix searching
- Word games
- Contact search

## 3. Implementing a Trie in Python

Each node contains:
- A dictionary of child nodes.
- A Boolean indicating whether a complete word ends at that node.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end_of_word = False


class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root

        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()

            node = node.children[char]

        node.is_end_of_word = True

    def search(self, word):
        node = self.root

        for char in word:
            if char not in node.children:
                return False

            node = node.children[char]

        return node.is_end_of_word

    def starts_with(self, prefix):
        node = self.root

        for char in prefix:
            if char not in node.children:
                return False

            node = node.children[char]

        return True
```

### Example Usage

```python
trie = Trie()

trie.insert("cat")
trie.insert("car")
trie.insert("care")

print(trie.search("cat"))       # True
print(trie.search("ca"))        # False
print(trie.search("care"))      # True
print(trie.starts_with("ca"))   # True
print(trie.starts_with("dog"))  # False
```

**Important:** `search("ca")` returns `False` because `ca` is a prefix, not a complete inserted word.

## 4. Insert a Word

To insert a word:

1. Start at the root.
2. Process each character.
3. Create a child node if the character does not exist.
4. Move to the corresponding child.
5. Mark the final node as the end of a word.

```python
trie = Trie()
trie.insert("apple")

print(trie.search("apple"))  # True
print(trie.search("app"))    # False
```

## 5. Search for a Prefix

Prefix search checks whether a path exists for every character in the prefix.

```python
trie = Trie()
trie.insert("apple")

print(trie.starts_with("app"))  # True
print(trie.starts_with("apl"))  # False
```

A prefix does not need to be a complete word.

## 6. Delete a Word from a Trie

Deletion must preserve nodes that are still needed by other words.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end_of_word = False


class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root

        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()

            node = node.children[char]

        node.is_end_of_word = True

    def search(self, word):
        node = self.root

        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]

        return node.is_end_of_word

    def delete(self, word):
        def remove(node, index):
            if index == len(word):
                if not node.is_end_of_word:
                    return False

                node.is_end_of_word = False
                return len(node.children) == 0

            char = word[index]

            if char not in node.children:
                return False

            should_delete_child = remove(
                node.children[char], index + 1
            )

            if should_delete_child:
                del node.children[char]

            return (
                not node.is_end_of_word
                and len(node.children) == 0
            )

        remove(self.root, 0)
```

### Example

```python
trie = Trie()

trie.insert("car")
trie.insert("care")

trie.delete("car")

print(trie.search("car"))   # False
print(trie.search("care"))  # True
```

Deleting `car` must not delete the shared path needed by `care`.

## 7. Time and Space Complexity

Let `L` be the length of the word or prefix being processed.

| Operation | Time Complexity |
|---|---|
| Insert | O(L) |
| Search | O(L) |
| Prefix search | O(L) |
| Delete | O(L) |

Space complexity depends on the number of nodes and the characters stored. In the worst case, inserting a new word of length `L` may create `L` new nodes.

## 8. Interview Practice Problems

### Beginner
1. Implement a Trie with insert, search, and starts_with.
2. Count how many words start with a given prefix.
3. Find whether a word exists in a dictionary stored in a Trie.

### Intermediate
4. Implement word deletion.
5. Find the longest common prefix among a list of strings using a Trie.
6. Replace words in a sentence using the shortest matching dictionary prefix.

### Advanced
7. Implement a Word Search II solution using a Trie and backtracking.
8. Build an autocomplete system that returns suggestions for a prefix.
9. Find the longest word that can be built one character at a time from other words in a dictionary.

## 9. Common Interview Questions

**Q1. What is a Trie?**

A tree-based data structure that stores strings character by character and supports efficient prefix operations.

**Q2. How is a Trie different from a hash table?**

A hash table is useful for exact-key lookup. A Trie naturally supports prefix searches and can share common prefixes among words.

**Q3. Why do we need `is_end_of_word`?**

A path may represent a prefix without representing a complete word. This flag distinguishes complete words from prefixes.

**Q4. What is the time complexity of searching for a word?**

O(L), where L is the length of the word, assuming child lookup in a dictionary takes average O(1) time.

**Q5. What is a major disadvantage of a Trie?**

Tries can use considerable memory because they may create many nodes and child dictionaries.

## 10. Practice Checklist

- [ ] Implement TrieNode and Trie from memory.
- [ ] Implement insert().
- [ ] Implement search().
- [ ] Implement starts_with().
- [ ] Implement delete() without removing shared prefixes.
- [ ] Solve one autocomplete problem.
- [ ] Explain Trie time and space complexity in an interview.
