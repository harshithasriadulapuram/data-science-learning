
# Dynamic Programming (DP) in Python

## 1. What Is Dynamic Programming?

Dynamic Programming (DP) is an algorithmic technique that solves complex problems by breaking them into smaller subproblems and storing their results so that the same work does not need to be repeated.

DP is especially useful when a problem has:

1. **Overlapping subproblems:** The same smaller problems are solved repeatedly.
2. **Optimal substructure:** An optimal solution can be constructed from solutions to smaller subproblems.

Common applications include:
- Fibonacci numbers
- Climbing stairs
- Knapsack problems
- Coin change
- Longest common subsequence
- Longest increasing subsequence
- Grid path problems
- Edit distance

## 2. Why Do We Need Dynamic Programming?

Consider the Fibonacci sequence:

F(0) = 0

F(1) = 1

F(n) = F(n - 1) + F(n - 2)

### Recursive solution

```python
def fibonacci(n):
    if n <= 1:
        return n

    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(6))  # 8
```

This solution repeatedly calculates the same Fibonacci values.

For example, `fibonacci(5)` calculates `fibonacci(3)` more than once.

**Time complexity:** O(2^n) as a simple upper bound.

**Auxiliary space:** O(n) for the recursion stack.

We can improve this by storing previously calculated results.

## 3. Two Main Approaches to DP

### A. Memoization — Top-Down DP

Memoization uses recursion and stores results in a cache.

```python
from functools import lru_cache


@lru_cache(maxsize=None)
def fibonacci(n):
    if n <= 1:
        return n

    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(6))  # 8
```

Each Fibonacci value is calculated only once.

**Time complexity:** O(n).

**Auxiliary space:** O(n) for the cache and recursion stack.

### B. Tabulation — Bottom-Up DP

Tabulation solves smaller subproblems first and builds toward the final answer iteratively.

```python
def fibonacci(n):
    if n <= 1:
        return n

    dp = [0] * (n + 1)
    dp[1] = 1

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]


print(fibonacci(6))  # 8
```

**Time complexity:** O(n).

**Auxiliary space:** O(n).

### Memoization vs Tabulation

| Feature | Memoization | Tabulation |
|---|---|---|
| Approach | Top-down | Bottom-up |
| Uses recursion | Yes | Usually no |
| Stores results | Cache | DP table |
| Computes | Usually only needed states | Usually all states in the table |
| Risk | Recursion depth limits | Usually avoids recursion depth limits |

## 4. The DP Problem-Solving Framework

Follow these steps when solving a DP problem.

1. **Define the state:** What does `dp[i]` or `dp[i][j]` represent?
2. **Find the recurrence relation:** How can a state be calculated from smaller states?
3. **Set the base cases:** What are the simplest known answers?
4. **Choose the computation order:** Which states must be solved first?
5. **Return the answer:** Identify the state containing the requested result.
6. **Analyze complexity:** Count the states and work per state.

A common pattern is:

```python
def solve(n):
    dp = [0] * (n + 1)

    # Initialize base cases
    dp[0] = 0

    for i in range(1, n + 1):
        # Calculate dp[i] from earlier states
        pass

    return dp[n]
```

The exact initialization and recurrence depend on the problem.

## 5. Example 1: Climbing Stairs

You are climbing a staircase with `n` steps. You can climb either one or two steps at a time.

Find the number of distinct ways to reach the top.

For `n = 3`, the possible sequences are:

- 1 + 1 + 1
- 1 + 2
- 2 + 1

Answer: `3`.

### Deriving the recurrence

To reach step `n`, the last move must have come from either:

- Step `n - 1`, using one step.
- Step `n - 2`, using two steps.

Therefore:

`dp[n] = dp[n - 1] + dp[n - 2]`

Base cases:

- `dp[0] = 1`: There is one way to remain at the starting position.
- `dp[1] = 1`: There is one way to reach the first step.

### Code

```python
def climb_stairs(n):
    if n < 0:
        return 0

    dp = [0] * (n + 1)
    dp[0] = 1

    for i in range(1, n + 1):
        one_step = dp[i - 1]
        two_steps = dp[i - 2] if i >= 2 else 0

        dp[i] = one_step + two_steps

    return dp[n]


print(climb_stairs(3))  # 3
print(climb_stairs(5))  # 8
```

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 6. Space Optimization

In the climbing-stairs problem, each state depends only on the previous two states. We do not need to keep the entire DP table.

```python
def climb_stairs(n):
    if n < 0:
        return 0

    previous_two = 1
    previous_one = 1

    for _ in range(2, n + 1):
        current = previous_one + previous_two
        previous_two = previous_one
        previous_one = current

    return previous_one


print(climb_stairs(5))  # 8
```

**Time complexity:** O(n).

**Auxiliary space:** O(1).

Space optimization is possible when older states are no longer needed.

## 7. Example 2: Minimum Cost to Climb Stairs

You are given the cost of stepping on each stair. You can start at index `0` or index `1`, and each move advances one or two indices.

Find the minimum cost to reach beyond the last stair.

```python
def min_cost_climbing_stairs(cost):
    previous_two = 0
    previous_one = 0

    for i in range(2, len(cost) + 1):
        current = min(
            previous_one + cost[i - 1],
            previous_two + cost[i - 2]
        )

        previous_two = previous_one
        previous_one = current

    return previous_one


print(min_cost_climbing_stairs([10, 15, 20]))  # 15
```

**Time complexity:** O(n).

**Auxiliary space:** O(1).

The key difference from the previous problem is that we use `min()` because we want the cheapest route, not the number of routes.

## 8. Example 3: House Robber

You cannot rob two adjacent houses. Each element represents the money in one house. Find the maximum amount you can rob.

Input:

```python
nums = [2, 7, 9, 3, 1]
```

Output:

```text
12
```

The optimal choice is `2 + 9 + 1 = 12`.

### Recurrence

At house `i`, we can:

- Skip it and keep the best answer through house `i - 1`.
- Rob it and add its value to the best answer through house `i - 2`.

Therefore:

`dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])`

### Code

```python
def rob(nums):
    previous_two = 0
    previous_one = 0

    for money in nums:
        current = max(previous_one, previous_two + money)
        previous_two = previous_one
        previous_one = current

    return previous_one


print(rob([2, 7, 9, 3, 1]))  # 12
print(rob([]))               # 0
```

**Time complexity:** O(n).

**Auxiliary space:** O(1).

## 9. Example 4: Coin Change — Minimum Number of Coins

Given coin denominations and a target amount, find the minimum number of coins required to make that amount.

Each denomination can be used unlimited times.

Input:

```python
coins = [1, 2, 5]
amount = 11
```

Output:

```text
3
```

One optimal solution is `5 + 5 + 1`.

### Code

```python
def coin_change(coins, amount):
    if amount < 0:
        return -1

    # amount + 1 is larger than any possible optimal count
    # when all denominations are positive integers.
    impossible = amount + 1
    dp = [impossible] * (amount + 1)
    dp[0] = 0

    for current_amount in range(1, amount + 1):
        for coin in coins:
            if coin <= current_amount:
                dp[current_amount] = min(
                    dp[current_amount],
                    dp[current_amount - coin] + 1
                )

    if dp[amount] == impossible:
        return -1

    return dp[amount]


print(coin_change([1, 2, 5], 11))  # 3
print(coin_change([2], 3))         # -1
```

This implementation assumes every coin denomination is a positive integer.

**Time complexity:** O(amount × number of coin denominations).

**Auxiliary space:** O(amount).

### Understanding the DP state

`dp[x]` represents the minimum number of coins needed to make amount `x`.

For every amount, try each usable coin and reuse the best answer for the remaining amount.

## 10. Example 5: 0/1 Knapsack

You have items with weights and values, and a bag with limited capacity. Each item can be selected at most once.

Find the maximum total value that fits inside the bag.

### Code

```python
def knapsack(weights, values, capacity):
    n = len(weights)

    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        weight = weights[i - 1]
        value = values[i - 1]

        for current_capacity in range(capacity + 1):
            # Do not select the item
            dp[i][current_capacity] = dp[i - 1][current_capacity]

            # Select the item if it fits
            if weight <= current_capacity:
                dp[i][current_capacity] = max(
                    dp[i][current_capacity],
                    value + dp[i - 1][current_capacity - weight]
                )

    return dp[n][capacity]


weights = [1, 3, 4, 5]
values = [1, 4, 5, 7]
capacity = 7

print(knapsack(weights, values, capacity))  # 9
```

### Understanding the state

`dp[i][c]` is the maximum value obtainable using the first `i` items with capacity `c`.

For each item, compare:

- Excluding the item.
- Including the item, if it fits.

We use the previous row when including an item. This ensures the item is not selected more than once.

**Time complexity:** O(n × capacity).

**Auxiliary space:** O(n × capacity).

This is pseudo-polynomial time because the complexity depends on the numeric capacity, not just the number of input items.

## 11. Example 6: Longest Common Subsequence (LCS)

The Longest Common Subsequence problem finds the length of the longest sequence that appears in both strings in the same relative order. The characters do not need to be adjacent.

Input:

```python
text1 = "abcde"
text2 = "ace"
```

Output:

```text
3
```

The longest common subsequence is `"ace"`.

### Code

```python
def longest_common_subsequence(text1, text2):
    m = len(text1)
    n = len(text2)

    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = 1 + dp[i - 1][j - 1]
            else:
                dp[i][j] = max(
                    dp[i - 1][j],
                    dp[i][j - 1]
                )

    return dp[m][n]


print(longest_common_subsequence("abcde", "ace"))  # 3
```

**Time complexity:** O(m × n).

**Auxiliary space:** O(m × n).

### Important distinction

A subsequence preserves relative order but can skip elements.

A substring must contain consecutive characters.

## 12. Example 7: Longest Increasing Subsequence (LIS)

Find the length of the longest strictly increasing subsequence.

Input:

```python
nums = [10, 9, 2, 5, 3, 7, 101, 18]
```

Output:

```text
4
```

One valid subsequence is `[2, 3, 7, 101]`.

### DP solution

```python
def length_of_lis(nums):
    if not nums:
        return 0

    n = len(nums)
    dp = [1] * n

    for i in range(n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)


print(length_of_lis([10, 9, 2, 5, 3, 7, 101, 18]))  # 4
```

`dp[i]` represents the length of the longest increasing subsequence that ends at index `i`.

**Time complexity:** O(n²).

**Auxiliary space:** O(n).

An optimized LIS algorithm can achieve O(n log n) time using binary search.

## 13. Example 8: Unique Paths in a Grid

A robot starts at the top-left corner and must reach the bottom-right corner. It can move only right or down.

Find the number of unique paths in an `m × n` grid.

### Code

```python
def unique_paths(m, n):
    if m <= 0 or n <= 0:
        return 0

    dp = [[1] * n for _ in range(m)]

    for row in range(1, m):
        for col in range(1, n):
            dp[row][col] = (
                dp[row - 1][col] +
                dp[row][col - 1]
            )

    return dp[m - 1][n - 1]


print(unique_paths(3, 7))  # 28
```

Each cell can be reached from the cell above or the cell to the left.

**Time complexity:** O(m × n).

**Auxiliary space:** O(m × n).

## 14. Example 9: Partition Equal Subset Sum

Given a list of positive integers, determine whether it can be divided into two subsets with equal sums.

Input:

```python
nums = [1, 5, 11, 5]
```

Output:

```text
True
```

The subsets can be `[11]` and `[1, 5, 5]`.

### Key idea

If the total sum is odd, an equal partition is impossible.

Otherwise, find whether any subset adds up to half of the total sum.

### Code

```python
def can_partition(nums):
    total = sum(nums)

    if total % 2 != 0:
        return False

    target = total // 2
    dp = [False] * (target + 1)
    dp[0] = True

    for num in nums:
        # Traverse backwards so each number is used at most once.
        for current_sum in range(target, num - 1, -1):
            dp[current_sum] = (
                dp[current_sum] or
                dp[current_sum - num]
            )

    return dp[target]


print(can_partition([1, 5, 11, 5]))  # True
print(can_partition([1, 2, 3, 5]))   # False
```

**Time complexity:** O(n × target).

**Auxiliary space:** O(target).

Traversing the DP array backward is essential here. Traversing forward could allow the same number to be reused during the same iteration.

## 15. How to Recognize DP Problems

Look for these clues:

- The problem asks for the minimum or maximum possible result.
- It asks how many ways something can be done.
- It asks whether a target sum or condition is achievable.
- The same subproblems appear repeatedly.
- A choice can be made using results from smaller states.

These clues do not guarantee that DP is the best approach, but they are useful signals.

## 16. Common DP Mistakes

1. Defining the DP state unclearly.
2. Forgetting base cases.
3. Writing the wrong recurrence relation.
4. Calculating states in the wrong order.
5. Confusing subsequences with substrings.
6. Using a forward loop in 0/1 knapsack when backward traversal is required for a one-dimensional DP array.
7. Optimizing space before understanding the full DP table.
8. Ignoring impossible states.
9. Giving a complexity analysis that ignores the size of the DP table.

## 17. Backtracking vs Dynamic Programming

| Feature | Backtracking | Dynamic Programming |
|---|---|---|
| Main approach | Explore choices | Reuse subproblem results |
| Common implementation | Recursion | Memoization or tabulation |
| State reuse | Not necessarily | Central to DP |
| Typical example | N-Queens | Coin Change |
| Worst-case time | Often exponential | Often polynomial or pseudo-polynomial, depending on the problem |

Some problems can use both techniques. Memoization can also improve a recursive search when the same state is reached repeatedly.

## 18. Practice Problems

### Beginner
- Fibonacci Number
- Climbing Stairs
- Min Cost Climbing Stairs
- House Robber
- Counting Bits

### Intermediate
- Coin Change
- Maximum Subarray
- Unique Paths
- Partition Equal Subset Sum
- Longest Increasing Subsequence
- Longest Common Subsequence
- Word Break

### Advanced
- 0/1 Knapsack
- Edit Distance
- Coin Change II
- Best Time to Buy and Sell Stock
- Matrix Chain Multiplication
- Distinct Subsequences
- Regular Expression Matching

## 19. Interview Questions

**Q1. What is dynamic programming?**

Dynamic programming solves problems by breaking them into subproblems and storing their answers to avoid repeated computation.

**Q2. What are overlapping subproblems?**

They occur when the same smaller problem is needed multiple times.

**Q3. What is optimal substructure?**

An optimal solution can be built from optimal solutions to appropriate smaller subproblems.

**Q4. What is memoization?**

A top-down technique that caches the results of recursive calls.

**Q5. What is tabulation?**

A bottom-up technique that fills a table iteratively.

**Q6. When can space be optimized?**

When the current state depends on only a limited number of previous states, older states may be discarded.

**Q7. What is the difference between 0/1 knapsack and unbounded knapsack?**

In 0/1 knapsack, each item can be selected at most once. In unbounded knapsack, an item can be selected repeatedly.

**Q8. Is every recursive problem a DP problem?**

No. DP is especially useful when subproblems overlap and their results can be reused.

## 20. Final Checklist

- [ ] Explain overlapping subproblems and optimal substructure.
- [ ] Distinguish memoization from tabulation.
- [ ] Write Fibonacci using both approaches.
- [ ] Derive the climbing-stairs recurrence.
- [ ] Solve House Robber using constant extra space.
- [ ] Solve minimum Coin Change.
- [ ] Understand the 0/1 Knapsack table.
- [ ] Explain LCS and LIS.
- [ ] Solve a grid-path problem.
- [ ] Explain why backward iteration matters in subset-sum DP.
- [ ] Analyze time and space complexity.
- [ ] Solve a new DP problem by defining its state and recurrence before coding.
