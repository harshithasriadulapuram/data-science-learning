
# Dynamic Programming — Patterns and Examples

## 1. What Is Dynamic Programming?

Dynamic Programming (DP) is an algorithmic technique that solves problems by breaking them into smaller subproblems and reusing their results.

DP is useful when a problem has:

1. **Overlapping subproblems:** The same subproblems are solved repeatedly.
2. **Optimal substructure:** An optimal solution can be constructed from solutions to smaller subproblems.

## 2. Two Approaches to DP

### A. Memoization (Top-Down)

Memoization uses recursion and stores previously calculated results.

```python
def fibonacci(n, memo=None):
    if memo is None:
        memo = {}

    if n <= 1:
        return n

    if n in memo:
        return memo[n]

    memo[n] = fibonacci(n - 1, memo) + fibonacci(n - 2, memo)
    return memo[n]


print(fibonacci(10))  # 55
```

**Time complexity:** O(n)  
**Space complexity:** O(n), including the recursion stack and memoization storage.

### B. Tabulation (Bottom-Up)

Tabulation solves smaller subproblems first and builds toward the final answer.

```python
def fibonacci(n):
    if n <= 1:
        return n

    dp = [0] * (n + 1)
    dp[1] = 1

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]


print(fibonacci(10))  # 55
```

**Time complexity:** O(n)  
**Space complexity:** O(n).

## 3. How to Solve a DP Problem

Follow these steps:

1. Define the state: What does `dp[i]` represent?
2. Identify the base cases.
3. Write the recurrence relation.
4. Choose memoization or tabulation.
5. Determine the correct iteration order.
6. Return the required state.
7. Analyze time and space complexity.
8. Optimize space if previous states are all that you need.

---

## Pattern 1: One-Dimensional DP

### Problem 1: Climbing Stairs

**Problem:** You can climb either one or two steps at a time. Find the number of distinct ways to reach step `n`.

**State:** `dp[i]` is the number of ways to reach step `i`.

**Recurrence:**

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

**Python solution:**

```python
def climb_stairs(n):
    if n <= 2:
        return n

    prev2 = 1
    prev1 = 2

    for _ in range(3, n + 1):
        current = prev1 + prev2
        prev2 = prev1
        prev1 = current

    return prev1


print(climb_stairs(5))  # 8
```

**Complexity:** O(n) time and O(1) auxiliary space.

### Problem 2: House Robber

**Problem:** Given the money in each house, find the maximum amount you can rob without robbing adjacent houses.

**State:** `dp[i]` is the maximum amount obtainable from the first `i` houses.

**Recurrence:**

```text
dp[i] = max(dp[i - 1], dp[i - 2] + nums[i - 1])
```

**Python solution:**

```python
def house_robber(nums):
    prev2 = 0
    prev1 = 0

    for money in nums:
        current = max(prev1, prev2 + money)
        prev2 = prev1
        prev1 = current

    return prev1


print(house_robber([2, 7, 9, 3, 1]))  # 12
```

**Complexity:** O(n) time and O(1) auxiliary space.

---

## Pattern 2: Coin Change

### Problem 3: Minimum Coins

**Problem:** Given coin denominations and a target amount, find the minimum number of coins required. You can use each denomination unlimited times.

Return `-1` if the amount cannot be formed.

**State:** `dp[a]` is the minimum number of coins needed to form amount `a`.

**Python solution:**

```python
def coin_change(coins, amount):
    dp = [float("inf")] * (amount + 1)
    dp[0] = 0

    for current_amount in range(1, amount + 1):
        for coin in coins:
            if coin <= current_amount:
                dp[current_amount] = min(
                    dp[current_amount],
                    dp[current_amount - coin] + 1
                )

    if dp[amount] == float("inf"):
        return -1

    return dp[amount]


print(coin_change([1, 2, 5], 11))  # 3
print(coin_change([2], 3))        # -1
```

**Complexity:** O(amount × number of denominations) time and O(amount) space.

### Problem 4: Coin Change — Number of Combinations

**Problem:** Count the distinct combinations that form the target amount, assuming unlimited coins of each denomination.

```python
def coin_change_combinations(coins, amount):
    dp = [0] * (amount + 1)
    dp[0] = 1

    for coin in coins:
        for current_amount in range(coin, amount + 1):
            dp[current_amount] += dp[current_amount - coin]

    return dp[amount]


print(coin_change_combinations([1, 2, 5], 5))  # 4
```

**Why is the coin loop outside?**

Processing one denomination at a time avoids counting different orders of the same combination as separate answers.

**Complexity:** O(amount × number of denominations) time and O(amount) space.

---

## Pattern 3: Two-Dimensional DP

### Problem 5: Longest Common Subsequence (LCS)

**Problem:** Find the length of the longest subsequence common to two strings. Characters must remain in their original order but do not need to be adjacent.

**State:** `dp[i][j]` is the LCS length for the first `i` characters of `text1` and the first `j` characters of `text2`.

**Transition:**

- If the current characters match, add one to the diagonal state.
- Otherwise, take the maximum of skipping a character from either string.

```python
def longest_common_subsequence(text1, text2):
    m = len(text1)
    n = len(text2)

    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(
                    dp[i - 1][j],
                    dp[i][j - 1]
                )

    return dp[m][n]


print(longest_common_subsequence("abcde", "ace"))  # 3
```

**Complexity:** O(m × n) time and O(m × n) space.

### Problem 6: Longest Increasing Subsequence (LIS)

**Problem:** Find the length of the longest strictly increasing subsequence.

```python
def longest_increasing_subsequence(nums):
    if not nums:
        return 0

    dp = [1] * len(nums)

    for i in range(len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)


print(longest_increasing_subsequence([10, 9, 2, 5, 3, 7, 101, 18]))
# 4
```

**State:** `dp[i]` is the length of the longest increasing subsequence ending at index `i`.

**Complexity:** O(n²) time and O(n) space.

An optimized LIS algorithm using binary search can achieve O(n log n) time.

---

## Pattern 4: 0/1 Knapsack

### Problem 7: Maximum Value Within Capacity

**Problem:** Each item has a weight and a value. Choose items to maximize total value without exceeding the capacity. Each item can be selected at most once.

```python
def knapsack(weights, values, capacity):
    dp = [0] * (capacity + 1)

    for weight, value in zip(weights, values):
        for current_capacity in range(capacity, weight - 1, -1):
            dp[current_capacity] = max(
                dp[current_capacity],
                dp[current_capacity - weight] + value
            )

    return dp[capacity]


print(knapsack([1, 3, 4, 5], [1, 4, 5, 7], 7))  # 9
```

**Why iterate backward?**

Backward iteration ensures each item contributes at most once. Forward iteration can accidentally reuse the same item multiple times.

**Complexity:** O(n × capacity) time and O(capacity) space.

---

## 4. Common DP Patterns

| Pattern | Example Problems |
|---|---|
| Fibonacci-style DP | Fibonacci, Climbing Stairs |
| Take or skip | House Robber |
| Unbounded choices | Coin Change |
| Two-string DP | LCS, Edit Distance |
| Subsequences | LIS |
| Capacity-based DP | 0/1 Knapsack, Subset Sum |
| Grid DP | Unique Paths, Minimum Path Sum |
| State transitions | Best Time to Buy and Sell Stock |

## 5. Common Interview Mistakes

- Defining `dp[i]` without a clear meaning.
- Forgetting base cases.
- Using the wrong loop order.
- Confusing subsequences with substrings.
- Reusing an item accidentally in 0/1 Knapsack.
- Counting coin permutations instead of combinations.
- Ignoring empty input or impossible targets.
- Giving time complexity without explaining the state transitions.

## 6. Interview Practice Checklist

### Beginner
- [ ] Fibonacci Number
- [ ] Climbing Stairs
- [ ] Min Cost Climbing Stairs
- [ ] House Robber

### Intermediate
- [ ] Coin Change
- [ ] Coin Change II
- [ ] Unique Paths
- [ ] Minimum Path Sum
- [ ] Longest Common Subsequence
- [ ] Longest Increasing Subsequence

### Advanced
- [ ] 0/1 Knapsack
- [ ] Partition Equal Subset Sum
- [ ] Edit Distance
- [ ] Longest Palindromic Subsequence
- [ ] Best Time to Buy and Sell Stock with Cooldown

## 7. Final Revision

Before an interview, make sure you can explain:

1. Overlapping subproblems and optimal substructure.
2. Memoization versus tabulation.
3. How to define a DP state.
4. How to derive a recurrence relation.
5. Why iteration order matters.
6. How to optimize space.
7. How to calculate time and space complexity.
