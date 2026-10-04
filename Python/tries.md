
# Tries (Prefix Trees) in Python

## 1. What Is a Trie?

A **Trie**, also called a **Prefix Tree**, is a tree-based data structure used to store and search strings efficiently.

Tries are especially useful when multiple words share common prefixes.

**Example words:**
- cat
- car
- cart
- dog

The words `cat`, `car`, and `cart` share the prefix `ca`, so they can share nodes in a Trie.

## 2. Why Do We Use Tries?

Tries are useful for:

- Autocomplete and search suggestions
- Spell checkers
- Dictionary word lookup
- Prefix-based searching
- Word games
- Contact search

## 3. How Does a Trie Work?

Each node represents a character.

A node typically stores:
1. A dictionary of child nodes.
2. A Boolean indicating whether a complete word ends at that node.

For example, inserting `"cat"` creates a path:

`root -> c -> a -> t`

The node representing `t` is marked as the end of a word.

The root represents the starting point and does not represent a character.

## 4. Implement a Trie in Python

### Step 1: Create a Trie Node

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end_of_word = False
```

**Explanation:**

- `children` maps characters to their corresponding child nodes.
- `is_end_of_word` indicates whether a complete word ends at this node.

### Step 2: Create the Trie Class

```python
class Trie:
    def __init__(self):
        self.root = TrieNode()
```

The root is the starting node for every word.

### Step 3: Insert a Word

```python
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
```

**How insertion works:**

1. Start at the root.
2. Visit each character in the word.
3. Create a node if that character does not already exist.
4. Move to the character's node.
5. Mark the final node as the end of a word.

### Step 4: Search for a Complete Word

Add this method inside the `Trie` class:

```python
def search(self, word):
    node = self.root

    for char in word:
        if char not in node.children:
            return False

        node = node.children[char]

    return node.is_end_of_word
```

A word is found only if its complete path exists and the final node marks the end of a word.

### Step 5: Check Whether a Prefix Exists

Add this method inside the `Trie` class:

```python
def starts_with(self, prefix):
    node = self.root

    for char in prefix:
        if char not in node.children:
            return False

        node = node.children[char]

    return True
```

Unlike `search()`, this method does not require the final node to mark the end of a complete word.

## 5. Complete Working Implementation

Copy and run this entire program:

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


trie = Trie()

trie.insert("cat")
trie.insert("car")
trie.insert("cart")
trie.insert("dog")

print(trie.search("cat"))       # True
print(trie.search("car"))       # True
print(trie.search("ca"))        # False
print(trie.search("cab"))       # False

print(trie.starts_with("ca"))   # True
print(trie.starts_with("do"))   # True
print(trie.starts_with("xyz"))  # False
```

**Important:** In the complete program, the `search()` and `starts_with()` methods must be indented inside the `Trie` class, just like `insert()`.

## 6. Delete a Word from a Trie

Deletion requires care because nodes may be shared by other words.

For example, deleting `"car"` must not remove `"cart"`.

Add this method inside the `Trie` class:

```python
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

This recursive method:
- Unmarks the end of the word.
- Removes unnecessary nodes when they are no longer used.
- Preserves nodes needed by other words.

The method leaves the Trie unchanged when the requested word is absent.

## 7. Time and Space Complexity

Let `L` be the length of the word or prefix being processed.

| Operation | Time Complexity |
|---|---|
| Insert a word | O(L) |
| Search for a word | O(L) |
| Check a prefix | O(L) |
| Delete a word | O(L) |

The Trie can require O(N) nodes in the worst case, where N is the total number of characters inserted across all words. Each node also has dictionary overhead.

## 8. Trie vs Hash Table

| Feature | Trie | Hash Table |
|---|---|---|
| Exact word search | O(L) | O(L) average to hash the string |
| Prefix search | Efficient | Usually requires checking stored keys |
| Preserves character paths | Yes | No |
| Memory usage | Can be high | Depends on keys and storage |
| Common use | Autocomplete | Exact key-value lookup |

## 9. Common Interview Questions

1. What is a Trie?
2. Why is a Trie called a Prefix Tree?
3. What is the role of `is_end_of_word`?
4. What is the difference between `search()` and `starts_with()`?
5. How does a Trie support autocomplete?
6. How can you delete a word without affecting other words?
7. What are the advantages and disadvantages of a Trie?
8. What is the time complexity of Trie insertion and search?
9. How does a Trie compare with a hash table?
10. How would you implement a contact search system using a Trie?

## 10. Practice Problems

### Beginner
1. Implement insertion and exact word search.
2. Implement prefix search.
3. Count how many words have a given prefix.

### Intermediate
4. Implement word deletion.
5. Find all words starting with a given prefix.
6. Build an autocomplete system.

### Advanced
7. Find the longest common prefix using a Trie.
8. Find the longest word that can be built one character at a time from other inserted words.
9. Implement a Trie that supports counting duplicate insertions.

## Key Takeaways

- A Trie stores strings as paths of characters.
- Words can share common prefixes.
- `is_end_of_word` distinguishes complete words from prefixes.
- Trie operations depend mainly on the length of the input string.
- Tries are commonly used in autocomplete, dictionaries, and prefix search.
