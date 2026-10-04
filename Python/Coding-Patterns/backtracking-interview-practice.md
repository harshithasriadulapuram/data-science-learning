
# Backtracking Interview Practice in Python

Backtracking is a problem-solving technique that explores possible choices, abandons invalid paths, and returns to try other choices.

A typical backtracking algorithm follows these steps:

1. Choose an option.
2. Check whether the choice is valid.
3. Explore the next step recursively.
4. Undo the choice when returning.

This is commonly called the **choose → explore → unchoose** pattern.

---

## 1. Generate All Subsets

**Problem:** Given a list of distinct integers, return every possible subset.

Example:

Input: `[1, 2]`

Output: `[[], [2], [1], [1, 2]]`

```python
def subsets(nums):
    result = []
    path = []

    def backtrack(index):
        if index == len(nums):
            result.append(path.copy())
            return

        # Exclude the current element.
        backtrack(index + 1)

        # Include the current element.
        path.append(nums[index])
        backtrack(index + 1)
        path.pop()

    backtrack(0)
    return result


print(subsets([1, 2]))
```

**Why use `path.copy()`?**

The `path` list is modified during recursion. Copying it saves the current subset instead of storing references to one changing list.

**Complexity:**
- Time: `O(n * 2^n)` to generate and copy all subsets.
- Space: `O(n)` auxiliary recursion/path space, excluding the output.

---

## 2. Generate All Permutations

**Problem:** Generate every ordering of distinct elements.

Example:

Input: `[1, 2, 3]`

Number of permutations: `3! = 6`.

```python
def permutations(nums):
    result = []
    path = []
    used = [False] * len(nums)

    def backtrack():
        if len(path) == len(nums):
            result.append(path.copy())
            return

        for i in range(len(nums)):
            if used[i]:
                continue

            used[i] = True
            path.append(nums[i])

            backtrack()

            path.pop()
            used[i] = False

    backtrack()
    return result


print(permutations([1, 2, 3]))
```

**Complexity:**
- Time: `O(n * n!)` to generate and copy all permutations.
- Space: `O(n)` auxiliary space, excluding output.

---

## 3. Combination Sum

**Problem:** Given positive candidate numbers and a target, find all unique combinations that sum to the target. Each candidate may be used repeatedly.

```python
def combination_sum(candidates, target):
    result = []
    path = []
    candidates = sorted(candidates)

    def backtrack(start, remaining):
        if remaining == 0:
            result.append(path.copy())
            return

        for i in range(start, len(candidates)):
            value = candidates[i]

            if value > remaining:
                break

            path.append(value)
            backtrack(i, remaining - value)
            path.pop()

    backtrack(0, target)
    return result


print(combination_sum([2, 3, 6, 7], 7))
# [[2, 2, 3], [7]]
```

Passing `i` rather than `i + 1` allows the current candidate to be reused.

**Complexity:**
- Time: Exponential in the worst case; depends on the candidates and target.
- Space: Proportional to the maximum recursion depth, excluding output.

This implementation assumes candidates are distinct positive integers.

---

## 4. Generate All Valid Parentheses

**Problem:** Generate all valid combinations of `n` pairs of parentheses.

```python
def generate_parentheses(n):
    result = []
    path = []

    def backtrack(open_count, close_count):
        if len(path) == 2 * n:
            result.append("".join(path))
            return

        if open_count < n:
            path.append("(")
            backtrack(open_count + 1, close_count)
            path.pop()

        if close_count < open_count:
            path.append(")")
            backtrack(open_count, close_count + 1)
            path.pop()

    backtrack(0, 0)
    return result


print(generate_parentheses(3))
# ['((()))', '(()())', '(())()', '()(())', '()()()']
```

**Key rules:**
- Never use more than `n` opening parentheses.
- Never use more closing parentheses than opening parentheses.

**Complexity:**
- Time: `O(C_n * n)`, where `C_n` is the nth Catalan number.
- Space: `O(n)` auxiliary space, excluding output.

---

## 5. Solve the N-Queens Problem

**Problem:** Place `n` queens on an `n × n` chessboard so that no two queens attack one another.

No two queens can share a column or diagonal. Place exactly one queen in each row.

```python
def solve_n_queens(n):
    result = []
    board = [["."] * n for _ in range(n)]

    columns = set()
    diagonal1 = set()  # row - column
    diagonal2 = set()  # row + column

    def backtrack(row):
        if row == n:
            result.append(["".join(line) for line in board])
            return

        for col in range(n):
            if (
                col in columns
                or row - col in diagonal1
                or row + col in diagonal2
            ):
                continue

            board[row][col] = "Q"
            columns.add(col)
            diagonal1.add(row - col)
            diagonal2.add(row + col)

            backtrack(row + 1)

            board[row][col] = "."
            columns.remove(col)
            diagonal1.remove(row - col)
            diagonal2.remove(row + col)

    backtrack(0)
    return result


solutions = solve_n_queens(4)
print(len(solutions))  # 2
```

**Complexity:**
- Time: Exponential; a common upper bound is `O(n!)` candidate placements with efficient conflict checks.
- Space: `O(n^2)` for the board, excluding the output.

---

## 6. Word Search

**Problem:** Given a character grid and a word, determine whether the word can be formed from horizontally or vertically adjacent cells. A cell cannot be used more than once in a path.

```python
def word_exists(board, word):
    if not word:
        return True

    if not board or not board[0]:
        return False

    rows, cols = len(board), len(board[0])

    def backtrack(r, c, index):
        if index == len(word):
            return True

        if (
            r < 0 or r >= rows
            or c < 0 or c >= cols
            or board[r][c] != word[index]
        ):
            return False

        char = board[r][c]
        board[r][c] = "#"

        found = (
            backtrack(r + 1, c, index + 1)
            or backtrack(r - 1, c, index + 1)
            or backtrack(r, c + 1, index + 1)
            or backtrack(r, c - 1, index + 1)
        )

        board[r][c] = char
        return found

    for r in range(rows):
        for c in range(cols):
            if backtrack(r, c, 0):
                return True

    return False


board = [
    ["A", "B", "C", "E"],
    ["S", "F", "C", "S"],
    ["A", "D", "E", "E"]
]

print(word_exists(board, "ABCCED"))  # True
```

**Complexity:**
- Time: `O(R * C * 3^L)` as a common upper bound after the first character, where `L` is the word length.
- Space: `O(L)` recursion space.

The function temporarily modifies the board but restores visited cells during backtracking.

---

## 7. Key Backtracking Interview Questions

1. What is backtracking?
2. How is backtracking different from brute force?
3. Why do we undo choices after recursion?
4. Why must mutable paths be copied before storing results?
5. How do you avoid duplicate combinations?
6. How do you prune branches that cannot lead to a valid answer?
7. Why is N-Queens a backtracking problem?
8. How does the Word Search problem prevent reusing a cell?

## Revision Checklist

- [ ] Generate subsets.
- [ ] Generate permutations.
- [ ] Solve Combination Sum.
- [ ] Generate valid parentheses.
- [ ] Solve N-Queens.
- [ ] Solve Word Search.
- [ ] Explain choose, explore, and unchoose.
- [ ] Identify pruning opportunities.
- [ ] Explain time and auxiliary-space complexity.
