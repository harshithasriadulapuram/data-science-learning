
# Greedy Algorithms — Interview Practice

## 1. What Is a Greedy Algorithm?

A greedy algorithm builds a solution step by step by choosing the option that looks best at the current stage.

It makes a **locally optimal choice** in the hope of obtaining a globally optimal solution.

Greedy algorithms are efficient for many problems, but they do not always produce the optimal answer.

### Common Applications

- Activity selection
- Interval scheduling
- Fractional knapsack
- Job sequencing
- Minimum spanning trees
- Huffman coding
- Gas station problems
- Jump Game

## 2. When Should You Use Greedy?

A greedy approach is suitable when you can justify that local choices lead to a globally optimal solution.

Two useful properties are:

1. **Greedy-choice property:** A globally optimal solution can be obtained by making an appropriate locally optimal choice.
2. **Optimal substructure:** An optimal solution contains optimal solutions to its remaining subproblems.

Do not assume a greedy solution works merely because it seems intuitive. Prove it or find a counterexample.

---

## Pattern 1: Activity Selection

### Problem

Given activities with start and finish times, select the maximum number of mutually non-overlapping activities.

### Strategy

Sort activities by finishing time and always select the next compatible activity that finishes earliest.

```python
def activity_selection(activities):
    # Each activity is (start, finish).
    activities.sort(key=lambda activity: activity[1])

    selected = []
    last_finish = float("-inf")

    for start, finish in activities:
        if start >= last_finish:
            selected.append((start, finish))
            last_finish = finish

    return selected


activities = [
    (1, 3),
    (2, 4),
    (3, 5),
    (0, 6),
    (5, 7),
    (8, 9),
]

print(activity_selection(activities))
# [(1, 3), (3, 5), (5, 7), (8, 9)]
```

**Time complexity:** O(n log n) for sorting.

**Why does it work?** Choosing the compatible activity that finishes earliest leaves as much time as possible for subsequent activities.

---

## Pattern 2: Fractional Knapsack

### Problem

Each item has a weight and value. Maximize total value without exceeding the capacity. Unlike 0/1 Knapsack, you may take a fraction of an item.

### Strategy

Sort items by value-to-weight ratio in descending order.

```python
def fractional_knapsack(items, capacity):
    # Each item is (value, weight).
    items = sorted(
        items,
        key=lambda item: item[0] / item[1],
        reverse=True,
    )

    total_value = 0.0

    for value, weight in items:
        if capacity == 0:
            break

        taken_weight = min(weight, capacity)
        total_value += taken_weight * (value / weight)
        capacity -= taken_weight

    return total_value


items = [(60, 10), (100, 20), (120, 30)]

print(fractional_knapsack(items, 50))  # 240.0
```

**Time complexity:** O(n log n).

**Important:** This greedy strategy works for Fractional Knapsack, but not generally for 0/1 Knapsack.

---

## Pattern 3: Minimum Number of Coins

### Problem

Given coin denominations and a target amount, find a representation using as few coins as possible.

A greedy strategy repeatedly takes the largest coin that does not exceed the remaining amount.

```python
def greedy_coin_change(coins, amount):
    coins = sorted(coins, reverse=True)
    result = []

    for coin in coins:
        count, amount = divmod(amount, coin)
        result.extend([coin] * count)

    if amount != 0:
        return None

    return result


print(greedy_coin_change([25, 10, 5, 1], 41))
# [25, 10, 5, 1]
```

**Time complexity:** O(n log n + k), where `n` is the number of denominations and `k` is the number of returned coins.

**Limitation:** Greedy coin selection does not work for every coin system.

For example, with denominations `[1, 3, 4]` and amount `6`, greedy returns `[4, 1, 1]`, using three coins. The optimal solution is `[3, 3]`, using two coins.

For arbitrary denominations, consider Dynamic Programming.

---

## Pattern 4: Job Sequencing with Deadlines

### Problem

Each job takes one unit of time and has a deadline and profit. Schedule jobs to maximize profit, completing each job by its deadline.

### Strategy

Sort jobs by profit in descending order and place each job in the latest available slot before its deadline.

```python
def job_sequencing(jobs):
    # Each job is (job_id, deadline, profit).
    jobs = sorted(jobs, key=lambda job: job[2], reverse=True)

    max_deadline = max((job[1] for job in jobs), default=0)
    slots = [None] * (max_deadline + 1)
    total_profit = 0

    for job_id, deadline, profit in jobs:
        for slot in range(min(deadline, max_deadline), 0, -1):
            if slots[slot] is None:
                slots[slot] = job_id
                total_profit += profit
                break

    scheduled_jobs = [job for job in slots[1:] if job is not None]
    return scheduled_jobs, total_profit


jobs = [
    ("A", 2, 100),
    ("B", 1, 19),
    ("C", 2, 27),
    ("D", 1, 25),
    ("E", 3, 15),
]

print(job_sequencing(jobs))
# (['C', 'A', 'E'], 142)
```

**Time complexity:** O(n log n + nD), where `D` is the maximum deadline.

This implementation assumes each job takes exactly one unit of time.

---

## Pattern 5: Jump Game

### Problem

Each array element indicates the maximum number of steps you can jump forward from that position. Determine whether you can reach the last index.

### Strategy

Track the farthest index reachable so far.

```python
def can_jump(nums):
    farthest = 0

    for i, jump in enumerate(nums):
        if i > farthest:
            return False

        farthest = max(farthest, i + jump)

        if farthest >= len(nums) - 1:
            return True

    return len(nums) <= 1


print(can_jump([2, 3, 1, 1, 4]))  # True
print(can_jump([3, 2, 1, 0, 4]))  # False
```

**Time complexity:** O(n).

The algorithm fails when it encounters an index that cannot be reached.

---

## Pattern 6: Gas Station

### Problem

You are given `gas[i]` and `cost[i]`. Determine a starting station from which you can complete the circular route, or return `-1` if impossible.

```python
def gas_station(gas, cost):
    if len(gas) != len(cost):
        raise ValueError("gas and cost must have equal lengths")

    if not gas:
        return -1

    total_tank = 0
    current_tank = 0
    start = 0

    for i in range(len(gas)):
        difference = gas[i] - cost[i]

        total_tank += difference
        current_tank += difference

        if current_tank < 0:
            start = i + 1
            current_tank = 0

    return start if total_tank >= 0 else -1


print(gas_station([1, 2, 3, 4, 5], [3, 4, 5, 1, 2]))
# 3
```

**Time complexity:** O(n).

A solution exists only if the total available gas is at least the total cost. When a candidate start fails, no station within that failed segment can be a valid start.

---

## Pattern 7: Minimum Number of Arrows to Burst Balloons

### Problem

Each balloon is represented by an interval `[start, end]`. Find the minimum number of arrows needed to burst every balloon. One arrow can burst all balloons it intersects.

```python
def min_arrows(points):
    if not points:
        return 0

    points = sorted(points, key=lambda interval: interval[1])
    arrows = 1
    arrow_position = points[0][1]

    for start, end in points[1:]:
        if start > arrow_position:
            arrows += 1
            arrow_position = end

    return arrows


print(min_arrows([[10, 16], [2, 8], [1, 6], [7, 12]]))
# 2
```

**Time complexity:** O(n log n).

Sorting by the earliest finishing endpoint allows each arrow to cover as many compatible intervals as possible.

---

## 3. Greedy vs Dynamic Programming

| Greedy | Dynamic Programming |
|---|---|
| Commits to a choice at each step | Compares or combines subproblem results |
| Often uses less memory | May require a table or memoization |
| Needs a proof that the choices are safe | Can evaluate multiple possible choices |
| Example: Fractional Knapsack | Example: 0/1 Knapsack |

## 4. Complexity Summary

| Problem | Time Complexity |
|---|---|
| Activity Selection | O(n log n) |
| Fractional Knapsack | O(n log n) |
| Greedy Coin Change | O(n log n + k) |
| Job Sequencing | O(n log n + nD) |
| Jump Game | O(n) |
| Gas Station | O(n) |
| Minimum Arrows | O(n log n) |

## 5. Interview Practice Problems

### Beginner
- [ ] Assign Cookies
- [ ] Lemonade Change
- [ ] Best Time to Buy and Sell Stock II
- [ ] Can Place Flowers

### Intermediate
- [ ] Jump Game
- [ ] Gas Station
- [ ] Non-overlapping Intervals
- [ ] Minimum Number of Arrows to Burst Balloons
- [ ] Partition Labels
- [ ] Queue Reconstruction by Height

### Advanced
- [ ] Job Sequencing with Deadlines
- [ ] Candy
- [ ] Minimum Number of Refueling Stops
- [ ] Huffman Coding
- [ ] Create a Minimum Cost to Connect Ropes solution

## 6. Common Interview Questions

**Q1. What is a greedy algorithm?**

An algorithm that makes a locally optimal choice at each step.

**Q2. Does greedy always give the optimal solution?**

No. The strategy must be justified for the specific problem.

**Q3. Why does earliest-finish-time scheduling work?**

It leaves the greatest possible remaining time for compatible activities.

**Q4. Why does Fractional Knapsack use value-to-weight ratio?**

Because items can be divided, so selecting the highest value per unit of weight maximizes the value obtained for each unit of capacity.

**Q5. Why does greedy fail for some coin systems?**

Choosing the largest available coin may prevent the use of a combination that achieves the target with fewer coins.

## 7. Final Revision Checklist

- [ ] Explain greedy-choice property and optimal substructure.
- [ ] Solve activity selection.
- [ ] Implement Fractional Knapsack.
- [ ] Explain why greedy can fail for coin change.
- [ ] Solve Jump Game.
- [ ] Solve Gas Station.
- [ ] Solve interval scheduling problems.
- [ ] Justify why each greedy choice is safe.
