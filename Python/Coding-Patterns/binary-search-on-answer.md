
# Binary Search on the Answer in Python

## 1. What Is Binary Search on the Answer?

Binary Search on the Answer is a technique used when a problem asks us to find the smallest or largest value satisfying a condition.

Instead of searching for an element in a sorted array, we search through a range of possible answers.

Common examples include:

- Finding the minimum eating speed needed to finish bananas within a deadline.
- Finding the minimum capacity needed to ship packages within a given number of days.
- Finding the minimum time required to complete a task.
- Finding the maximum minimum distance between placed objects.
- Finding the smallest maximum workload assigned to workers.

The key requirement is that the condition must be **monotonic**: once it becomes true or false, it must not switch back and forth as the candidate answer increases.

## 2. Understanding Monotonicity

Suppose we want to find the minimum capacity that allows all packages to be shipped within five days.

Imagine testing capacities in increasing order:

```text
Capacity:  5  6  7  8  9  10  11
Feasible:  F  F  F  T  T  T   T
```

Once a capacity is sufficient, every larger capacity is also sufficient.

We can binary-search for the first `True`.

For a minimum feasible answer:

```text
False False False True True True
                  ^
             First True
```

For a maximum feasible answer:

```text
True True True False False False
          ^
      Last True
```

## 3. General Template: Find the Minimum Feasible Answer

Use this pattern when you need the smallest value for which a condition is true.

```python
def first_true(low, high, feasible):
    while low < high:
        mid = low + (high - low) // 2

        if feasible(mid):
            high = mid
        else:
            low = mid + 1

    return low
```

### How it works

1. Calculate the midpoint.
2. If the midpoint is feasible, the answer could be the midpoint or something smaller.
3. Otherwise, the answer must be larger.
4. Continue until `low == high`.

At termination, `low` is the first feasible value, assuming the search bounds contain a feasible answer.

## 4. General Template: Find the Maximum Feasible Answer

Use this pattern when you need the largest value for which a condition is true.

```python
def last_true(low, high, feasible):
    while low < high:
        mid = low + (high - low + 1) // 2

        if feasible(mid):
            low = mid
        else:
            high = mid - 1

    return low
```

The upper midpoint is important. Without it, the algorithm can get stuck when `low` and `high` differ by one.

This template assumes `low` is feasible and the feasible region ends before or at `high`.

## 5. Problem 1: Koko Eating Bananas

### Problem statement

Koko has piles of bananas. She can eat at most one pile per hour at a chosen speed `k`. If a pile contains more bananas than she can eat in one hour, she continues that pile during the next hour.

Find the minimum integer speed that lets her finish all piles within `h` hours.

Example:

```python
piles = [3, 6, 7, 11]
h = 8
```

Output:

```text
4
```

### Step 1: Define the search range

The minimum speed is `1`.

The maximum speed needed is the largest pile because eating faster than the largest pile cannot reduce the time below one hour per pile.

```python
low = 1
high = max(piles)
```

### Step 2: Calculate the required hours

For each pile, the hours needed at speed `speed` are:

```python
(pile + speed - 1) // speed
```

This performs integer ceiling division.

### Step 3: Implement the solution

```python
def min_eating_speed(piles, h):
    low = 1
    high = max(piles)

    while low < high:
        speed = low + (high - low) // 2

        hours = 0
        for pile in piles:
            hours += (pile + speed - 1) // speed

        if hours <= h:
            high = speed
        else:
            low = speed + 1

    return low


print(min_eating_speed([3, 6, 7, 11], 8))
```

Output:

```text
4
```

### Complexity

Let `N` be the number of piles and `M` be the largest pile.

- Time: `O(N log M)`
- Auxiliary space: `O(1)`

## 6. Problem 2: Capacity to Ship Packages Within D Days

### Problem statement

Given package weights in their original order, find the minimum ship capacity needed to deliver every package within `days` days.

Packages cannot be reordered, and each day's total weight cannot exceed the ship's capacity.

Example:

```python
weights = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
days = 5
```

Output:

```text
15
```

### Approach

The minimum possible capacity is the heaviest package.

The maximum possible capacity is the sum of all weights, which lets us ship everything in one day.

For a candidate capacity, simulate loading packages in order and count the days required.

```python
def ship_within_days(weights, days):
    low = max(weights)
    high = sum(weights)

    while low < high:
        capacity = low + (high - low) // 2

        required_days = 1
        current_load = 0

        for weight in weights:
            if current_load + weight > capacity:
                required_days += 1
                current_load = 0

            current_load += weight

        if required_days <= days:
            high = capacity
        else:
            low = capacity + 1

    return low


print(ship_within_days(
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    5
))
```

Output:

```text
15
```

### Complexity

Let `N` be the number of packages and `S` the sum of their weights.

- Time: `O(N log S)`
- Auxiliary space: `O(1)`

## 7. Problem 3: Split Array to Minimize the Largest Sum

### Problem statement

Split an array into exactly `k` non-empty contiguous subarrays so that the largest subarray sum is as small as possible.

Example:

```python
nums = [7, 2, 5, 10, 8]
k = 2
```

Output:

```text
18
```

One optimal split is:

```text
[7, 2, 5] and [10, 8]

Sums: 14 and 18
Largest sum: 18
```

### Solution

```python
def split_array(nums, k):
    low = max(nums)
    high = sum(nums)

    while low < high:
        limit = low + (high - low) // 2

        subarrays = 1
        current_sum = 0

        for num in nums:
            if current_sum + num > limit:
                subarrays += 1
                current_sum = 0

            current_sum += num

        if subarrays <= k:
            high = limit
        else:
            low = limit + 1

    return low


print(split_array([7, 2, 5, 10, 8], 2))
```

Output:

```text
18
```

The feasibility check asks whether the array can be split into at most `k` subarrays without any subarray exceeding `limit`. For non-negative numbers, this greedy check determines whether the candidate limit is feasible.

### Complexity

- Time: `O(N log S)`, where `S` is the sum of the array.
- Auxiliary space: `O(1)`.

## 8. Problem 4: Aggressive Cows

### Problem statement

Given stall positions and a number of cows, place the cows so that the minimum distance between any two placed cows is as large as possible.

Example:

```python
stalls = [1, 2, 4, 8, 9]
cows = 3
```

Output:

```text
3
```

One valid placement is at positions `1`, `4`, and `8`. The minimum distance is `3`.

### Solution

```python
def aggressive_cows(stalls, cows):
    stalls.sort()

    low = 1
    high = stalls[-1] - stalls[0]

    def can_place(distance):
        count = 1
        last_position = stalls[0]

        for position in stalls[1:]:
            if position - last_position >= distance:
                count += 1
                last_position = position

                if count >= cows:
                    return True

        return count >= cows

    while low < high:
        mid = low + (high - low + 1) // 2

        if can_place(mid):
            low = mid
        else:
            high = mid - 1

    return low


print(aggressive_cows([1, 2, 4, 8, 9], 3))
```

Output:

```text
3
```

### Complexity

Let `N` be the number of stalls and `D` the distance between the first and last stall.

- Time: `O(N log N + N log D)`
- Auxiliary space: `O(1)` excluding sorting-related space.

## 9. Problem 5: Minimum Days to Make Bouquets

### Problem statement

Each flower has a day on which it blooms. Find the minimum day needed to make `m` bouquets, each containing `k` adjacent bloomed flowers.

Example:

```python
bloom_day = [1, 10, 3, 10, 2]
m = 3
k = 1
```

Output:

```text
3
```

Three flowers have bloomed by day `3`, so three single-flower bouquets can be made.

If `m * k > len(bloom_day)`, the answer is impossible.

### Solution

```python
def min_days(bloom_day, m, k):
    if m * k > len(bloom_day):
        return -1

    low = min(bloom_day)
    high = max(bloom_day)

    def can_make(day):
        bouquets = 0
        consecutive = 0

        for bloom in bloom_day:
            if bloom <= day:
                consecutive += 1

                if consecutive == k:
                    bouquets += 1
                    consecutive = 0
            else:
                consecutive = 0

        return bouquets >= m

    while low < high:
        mid = low + (high - low) // 2

        if can_make(mid):
            high = mid
        else:
            low = mid + 1

    return low


print(min_days([1, 10, 3, 10, 2], 3, 1))
```

Output:

```text
3
```

### Complexity

Let `N` be the number of flowers and `D` be the range of bloom days.

- Time: `O(N log D)`
- Auxiliary space: `O(1)`

## 10. How to Recognize This Pattern

Look for these clues in an interview problem:

- The problem asks for a minimum capacity, speed, time, or maximum workload.
- The answer is an integer within a known range.
- You can write a function such as `can_finish(x)`, `can_ship(x)`, or `is_possible(x)`.
- If a candidate answer works, larger or smaller candidates consistently work too.
- Testing every possible answer would be too slow.

The most important question is:

**Can I efficiently check whether a candidate answer is feasible?**

If yes, and feasibility is monotonic, binary search on the answer may be appropriate.

## 11. Common Mistakes

1. Using binary search without proving monotonicity.
2. Choosing incorrect lower or upper bounds.
3. Using `mid = (low + high) // 2` in the maximum-feasible template without the upper midpoint.
4. Forgetting that the feasibility function must match the problem's constraints.
5. Confusing "at most `k` groups" with "exactly `k` groups." Check the problem's assumptions.
6. Forgetting impossible cases.
7. Using floating-point division when integer arithmetic is sufficient.
8. Failing to ensure that the search range contains a valid answer.

## 12. Practice Questions

Solve these in order:

1. Koko Eating Bananas.
2. Capacity to Ship Packages Within D Days.
3. Split Array Largest Sum.
4. Minimum Days to Make Bouquets.
5. Aggressive Cows.
6. Allocate Books.
7. Minimum Speed to Arrive on Time.
8. Find the Smallest Divisor Given a Threshold.
9. Magnetic Force Between Two Balls.
10. Minimize Maximum Distance to Gas Station.

## 13. Final Interview Checklist

Before considering this pattern mastered, make sure you can:

- Identify whether the answer space is monotonic.
- Define the smallest and largest possible answers.
- Implement a correct feasibility function.
- Decide whether to find the first true or last true.
- Explain the time complexity in terms of the search range and the feasibility check.
- Handle impossible inputs and boundary cases.

**Key takeaway:** Binary Search on the Answer reduces a large search over candidate values to logarithmically many feasibility checks.
