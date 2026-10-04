
# Backtracking in Python

## 1. What Is Backtracking?

Backtracking is an algorithmic technique that solves problems by building a solution step by step.

Whenever a choice leads to an invalid or unwanted solution, the algorithm **undoes that choice and tries another one**.

Backtracking follows three main steps:

1. Choose an option.
2. Explore the consequences of that choice.
3. Undo the choice if it does not lead to a valid solution.

It is commonly used in:
- Permutations and combinations
- Subsets
- N-Queens problem
- Sudoku solver
- Maze solving
- Word search

## 2. Backtracking vs Recursion

**Recursion** is a technique in which a function calls itself.

**Backtracking** uses recursion in many implementations, but adds the idea of exploring choices and undoing them when necessary.

Not every recursive algorithm is a backtracking algorithm.

## 3. General Backtracking Template

```python
def backtrack(state):
    if is_solution(state):
        process_solution(state)
        return

    for choice in available_choices(state):
        if is_valid(choice, state):
            make_choice(choice, state)
            backtrack(state)
            undo_choice(choice, state)
```

### Understanding the template

- `is_solution()`: Checks whether the current state is a complete solution.
- `available_choices()`: Provides the choices we can try.
- `is_valid()`: Checks whether a choice is allowed.
- `make_choice()`: Applies the choice.
- `backtrack()`: Explores the next step.
- `undo_choice()`: Reverses the choice so another option can be tried.

The undo step is also called **backtracking**.

## 4. Example 1: Generate All Subsets

Given a list, generate every possible subset.

Input:

```python
nums = [1, 2, 3]
```

Output:

```text
[]
[1]
[1, 2]
[1, 2, 3]
[1, 3]
[2]
[2, 3]
[3]
```

### Code

```python
def subsets(nums):
    result = []
    current = []

    def backtrack(index):
        if index == len(nums):
            result.append(current.copy())
            return

        # Choice 1: Include the current element
        current.append(nums[index])
        backtrack(index + 1)

        # Undo the choice
        current.pop()

        # Choice 2: Exclude the current element
        backtrack(index + 1)

    backtrack(0)
    return result


print(subsets([1, 2, 3]))
```

### How it works

For each element, we have two choices:

- Include it in the current subset.
- Exclude it from the current subset.

For three elements, there are \(2^3 = 8\) possible subsets.

**Time complexity:** \(O(n \times 2^n)\), because there are \(2^n\) subsets and copying each subset can take up to \(O(n)\).

**Auxiliary space:** \(O(n)\) for the recursion stack and current subset, excluding the output.

## 5. Why Do We Use `current.copy()`?

Consider:

```python
result.append(current)
```

This stores a reference to the same list. Later modifications to `current` can affect the stored results.

Instead, use:

```python
result.append(current.copy())
```

This stores a separate copy of the current subset.

Remember this rule when saving solutions in backtracking problems.

## 6. Example 2: Generate All Permutations

A permutation is an arrangement of elements in a particular order.

For `[1, 2, 3]`, some permutations are:

```text
[1, 2, 3]
[1, 3, 2]
[2, 1, 3]
[2, 3, 1]
[3, 1, 2]
[3, 2, 1]
```

### Code

```python
def permutations(nums):
    result = []
    current = []
    used = [False] * len(nums)

    def backtrack():
        if len(current) == len(nums):
            result.append(current.copy())
            return

        for i in range(len(nums)):
            if used[i]:
                continue

            # Choose
            current.append(nums[i])
            used[i] = True

            # Explore
            backtrack()

            # Undo
            current.pop()
            used[i] = False

    backtrack()
    return result


print(permutations([1, 2, 3]))
```

### How it works

1. Select an unused element.
2. Mark it as used.
3. Recursively build the remaining permutation.
4. Remove the element and mark it unused.
5. Try another available element.

For \(n\) distinct elements, there are \(n!\) permutations.

**Time complexity:** \(O(n \times n!)\), including copying each permutation.

**Auxiliary space:** \(O(n)\) for the current permutation, used array, and recursion stack, excluding the output.

## 7. Example 3: Combination Sum

Given a list of positive candidate numbers and a target, find combinations that add up to the target. Each candidate can be used repeatedly.

Input:

```python
candidates = [2, 3, 6, 7]
target = 7
```

Output:

```text
[[2, 2, 3], [7]]
```

### Code

```python
def combination_sum(candidates, target):
    result = []
    current = []

    def backtrack(start, remaining):
        if remaining == 0:
            result.append(current.copy())
            return

        if remaining < 0:
            return

        for i in range(start, len(candidates)):
            current.append(candidates[i])

            # Pass i, allowing the same candidate again
            backtrack(i, remaining - candidates[i])

            current.pop()

    backtrack(0, target)
    return result


print(combination_sum([2, 3, 6, 7], 7))
```

### Important points

- `remaining == 0`: A valid combination has been found.
- `remaining < 0`: The current combination exceeds the target.
- `start`: Prevents generating the same combination in different orders.
- `backtrack(i, ...)`: Allows candidate `i` to be reused.

This implementation assumes candidates are positive and distinct. If candidates contain duplicates, additional handling is needed to avoid duplicate combinations.

**Time complexity:** Depends on the candidate values and target; the number of valid search paths can grow exponentially. There is no single \(O(2^n)\) bound for this version because candidates can be reused.

## 8. Example 4: Check Whether a Path Exists in a Maze

Consider a grid where:

- `0` means an open cell.
- `1` means a blocked cell.
- The start is the top-left cell.
- The destination is the bottom-right cell.

The function checks whether any path exists using up, down, left, and right moves.

### Code

```python
def solve_maze(maze):
    rows = len(maze)
    cols = len(maze[0])

    visited = [[False] * cols for _ in range(rows)]

    def backtrack(row, col):
        # Reject invalid or blocked cells
        if (
            row < 0 or row >= rows
            or col < 0 or col >= cols
            or maze[row][col] == 1
            or visited[row][col]
        ):
            return False

        # Destination reached
        if row == rows - 1 and col == cols - 1:
            return True

        visited[row][col] = True

        directions = [
            (1, 0),   # Down
            (-1, 0),  # Up
            (0, 1),   # Right
            (0, -1)   # Left
        ]

        for dr, dc in directions:
            if backtrack(row + dr, col + dc):
                return True

        return False

    if not maze or not maze[0]:
        return False

    return backtrack(0, 0)


maze = [
    [0, 0, 1],
    [1, 0, 0],
    [0, 0, 0]
]

print(solve_maze(maze))
```

### Important implementation note

In the code above, the dimensions are accessed before the empty-grid check. To handle an empty grid safely, place the check first:

```python
def solve_maze(maze):
    if not maze or not maze[0]:
        return False

    rows = len(maze)
    cols = len(maze[0])

    # Continue with the implementation above.
```

The `visited` matrix prevents revisiting cells and getting stuck in cycles.

**Time complexity:** \(O(R \times C)\), where \(R\) is the number of rows and \(C\) is the number of columns, because each cell is visited at most once.

**Auxiliary space:** \(O(R \times C)\) for `visited` and up to \(O(R \times C)\) recursion depth.

## 9. Example 5: N-Queens Problem

The N-Queens problem asks us to place `N` queens on an `N × N` chessboard so that no two queens attack each other.

Queens cannot share:
- The same column
- The same diagonal

We place one queen per row.

### Code

```python
def solve_n_queens(n):
    result = []
    board = [["."] * n for _ in range(n)]

    columns = set()
    diagonals = set()       # row - col
    anti_diagonals = set()  # row + col

    def backtrack(row):
        if row == n:
            result.append(["".join(line) for line in board])
            return

        for col in range(n):
            diagonal = row - col
            anti_diagonal = row + col

            if (
                col in columns
                or diagonal in diagonals
                or anti_diagonal in anti_diagonals
            ):
                continue

            # Choose
            board[row][col] = "Q"
            columns.add(col)
            diagonals.add(diagonal)
            anti_diagonals.add(anti_diagonal)

            # Explore next row
            backtrack(row + 1)

            # Undo
            board[row][col] = "."
            columns.remove(col)
            diagonals.remove(diagonal)
            anti_diagonals.remove(anti_diagonal)

    backtrack(0)
    return result


solutions = solve_n_queens(4)

for solution in solutions:
    for row in solution:
        print(row)
    print()
```

### Why use sets?

Checking whether a column or diagonal is occupied takes average \(O(1)\) time using a set.

The diagonal identifiers are:

- `row - col`: One diagonal direction.
- `row + col`: The other diagonal direction.

**Time complexity:** Commonly described with an \(O(N!)\)-scale search bound for this backtracking approach; actual work depends on how many placements are rejected.

**Auxiliary space:** \(O(N^2)\) for the board, plus \(O(N)\) for the sets and recursion stack, excluding the returned solutions.

## 10. Pruning

**Pruning** means stopping a search branch as soon as we know it cannot produce a valid solution.

Example:

```python
if remaining < 0:
    return
```

In Combination Sum, this avoids exploring combinations that already exceed the target.

Pruning helps reduce unnecessary work, although the worst-case time complexity may still be exponential.

## 11. Backtracking vs Greedy vs Dynamic Programming

| Technique | Main idea | Typical example |
|---|---|---|
| Backtracking | Explore choices and undo them | N-Queens |
| Greedy | Choose the best-looking local option | Activity selection |
| Dynamic programming | Reuse results of overlapping subproblems | Fibonacci with memoization |

Backtracking is useful when we need to explore multiple possible solutions while rejecting invalid paths.

## 12. Common Mistakes

1. Forgetting to undo a choice after recursion.
2. Storing a mutable list without copying it.
3. Forgetting the base case.
4. Not checking invalid choices early.
5. Generating duplicate combinations by ignoring the `start` index.
6. Forgetting to reset a `used` flag.
7. Confusing a valid partial solution with a complete solution.
8. Assuming backtracking is always efficient. Many backtracking problems have exponential worst-case complexity.

## 13. Practice Problems

### Beginner
- Generate all subsets of a list.
- Generate all permutations of a list.
- Find all combinations of `k` numbers from `1` to `n`.
- Generate all binary strings of length `n`.

### Intermediate
- Combination Sum.
- Palindrome partitioning.
- Word Search in a grid.
- Generate valid parentheses.
- Rat in a Maze.

### Advanced
- N-Queens.
- Sudoku Solver.
- Graph coloring.
- Hamiltonian Path.
- Knight's Tour.

## 14. Interview Questions

**Q1. What is backtracking?**

Backtracking is a problem-solving technique that explores possible choices and reverses a choice when it cannot lead to a valid solution.

**Q2. Why is backtracking often implemented using recursion?**

Recursion naturally represents exploring a choice and then continuing to the next decision. However, backtracking can also be implemented iteratively.

**Q3. What is pruning?**

Pruning stops exploring a branch that cannot lead to a valid solution.

**Q4. Why do we use `pop()` after recursive calls?**

It removes the most recently added choice and restores the previous state.

**Q5. What is the difference between subsets and permutations?**

Subsets concern which elements are selected. Permutations concern the order of selected elements.

**Q6. Is backtracking always exponential?**

No. Its complexity depends on the problem and search space. Many classic backtracking problems have exponential worst-case behavior.

**Q7. How can backtracking be optimized?**

Use pruning, efficient validity checks, sets for fast membership tests, and good variable or choice ordering.

## 15. Final Checklist

Before moving to the next topic, make sure you can:

- [ ] Explain backtracking in your own words.
- [ ] Write the general backtracking template.
- [ ] Generate subsets and permutations without copying code.
- [ ] Explain why `current.copy()` is necessary.
- [ ] Implement Combination Sum.
- [ ] Explain pruning and the undo step.
- [ ] Solve a basic maze problem.
- [ ] Explain how the N-Queens constraints work.
- [ ] Identify time and space complexity.
