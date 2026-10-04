
# Greedy Algorithms in Python

## 1. Introduction

A Greedy Algorithm solves a problem by making the best choice at each step according to a chosen rule.

It makes a locally optimal choice with the hope of reaching a globally optimal solution.

However, **a greedy strategy does not produce the optimal answer for every problem**. We must prove that the strategy works for the particular problem.

Greedy algorithms are commonly used in:
- Activity selection
- Interval scheduling
- Fractional Knapsack
- Minimum Spanning Trees
- Huffman Coding
- Job sequencing
- Shortest-path problems with non-negative edge weights

---

## 2. How Does a Greedy Algorithm Work?

A typical greedy algorithm follows these steps:

1. Identify the choices available.
2. Define a rule for choosing the best available option.
3. Select the option that satisfies the rule.
4. Update the problem state.
5. Repeat until the problem is solved.

Unlike many dynamic programming solutions, greedy algorithms generally do not reconsider earlier choices.

### Simple Example

Suppose you want to select as many activities as possible without overlapping.

A useful strategy is to select the activity that finishes earliest, then choose the next compatible activity.

This strategy works for the standard activity-selection problem.

---

## 3. Activity Selection Problem

### Problem

Given activities with start and finish times, select the maximum number of non-overlapping activities.

An activity can be selected if its start time is greater than or equal to the finish time of the previously selected activity.

### Python Implementation

```python
def activity_selection(activities):
    # Each activity is represented as (start, finish).
    activities = sorted(activities, key=lambda activity: activity[1])

    selected = []
    last_finish = float("-inf")

    for start, finish in activities:
        if start >= last_finish:
            selected.append((start, finish))
            last_finish = finish

    return selected


activities = [
    (1, 3),
    (2, 5),
    (4, 7),
    (1, 8),
    (8, 10),
    (9, 11)
]

print(activity_selection(activities))
# [(1, 3), (4, 7), (8, 10)]
```

### Explanation

1. Sort the activities by finish time.
2. Select the first activity.
3. Consider the remaining activities in finish-time order.
4. Select an activity only if it does not overlap with the last selected activity.

### Complexity

- Time: O(n log n), due to sorting.
- Auxiliary space: O(n) for the sorted copy and selected activities.

### Why Does It Work?

Choosing the activity that finishes earliest leaves as much time as possible for future activities.

This is the key greedy-choice property for the standard activity-selection problem.

---

## 4. Fractional Knapsack Problem

### Problem

You have a knapsack with a limited capacity. Each item has a weight and a value.

You may take a fraction of an item.

The objective is to maximize the total value without exceeding the capacity.

### Greedy Strategy

Choose items in descending order of value per unit weight.

### Python Implementation

```python
def fractional_knapsack(items, capacity):
    # Each item is represented as (value, weight).
    if capacity < 0:
        raise ValueError("Capacity cannot be negative")

    if any(weight <= 0 for value, weight in items):
        raise ValueError("Weights must be positive")

    items = sorted(
        items,
        key=lambda item: item[0] / item[1],
        reverse=True
    )

    total_value = 0.0
    chosen_items = []

    for value, weight in items:
        if capacity == 0:
            break

        fraction = min(1.0, capacity / weight)

        total_value += value * fraction
        capacity -= weight * fraction

        chosen_items.append((value, weight, fraction))

    return total_value, chosen_items


items = [
    (60, 10),
    (100, 20),
    (120, 30)
]

value, chosen = fractional_knapsack(items, 50)

print(value)   # 240.0
print(chosen)
```

### Complexity

- Time: O(n log n), due to sorting.
- Auxiliary space: O(n) for the sorted copy and result list.

### Important Interview Point

The greedy strategy works for **Fractional Knapsack** because items can be divided.

It does not generally solve the **0/1 Knapsack** problem optimally, where each item must be taken entirely or left behind.

---

## 5. Coin Change: When Greedy Works and Fails

### Problem

Given coin denominations, find a way to make a target amount using as few coins as possible.

A common greedy strategy repeatedly chooses the largest coin that does not exceed the remaining amount.

### Python Implementation

```python
def greedy_coin_change(coins, amount):
    if amount < 0:
        raise ValueError("Amount cannot be negative")

    if any(coin <= 0 for coin in coins):
        raise ValueError("Coin values must be positive")

    coins = sorted(set(coins), reverse=True)
    result = []

    for coin in coins:
        while amount >= coin:
            amount -= coin
            result.append(coin)

    if amount != 0:
        return None

    return result


print(greedy_coin_change([25, 10, 5, 1], 63))
# [25, 25, 10, 1, 1, 1]
```

### Complexity

Let `A` be the amount and `C` the number of coin denominations.

- Time: Depends on the number of coins selected; in the worst case, it can be proportional to the amount when the smallest coin is 1.
- Auxiliary space: O(A) in the worst case for the returned coin list.

### A Case Where Greedy Fails

Consider denominations `[1, 3, 4]` and amount `6`.

Greedy chooses:

- 4
- 1
- 1

That requires 3 coins.

The optimal answer is:

- 3
- 3

That requires only 2 coins.

Therefore, greedy coin change is not always optimal. Dynamic programming can solve the general minimum-coin problem for suitable integer inputs.

---

## 6. Job Sequencing with Deadlines

### Problem

Each job has:
- A deadline
- A profit
- A duration of one time unit

A job earns its profit only if it is completed by its deadline.

The goal is to maximize total profit.

### Greedy Strategy

1. Sort jobs by descending profit.
2. For each job, find the latest available time slot that does not exceed its deadline.
3. Schedule the job if a suitable slot exists.

### Python Implementation

```python
def job_sequencing(jobs):
    # Each job is represented as (job_id, deadline, profit).
    if any(deadline < 0 for job_id, deadline, profit in jobs):
        raise ValueError("Deadlines cannot be negative")

    jobs = sorted(jobs, key=lambda job: job[2], reverse=True)

    max_deadline = max(
        (deadline for job_id, deadline, profit in jobs),
        default=0
    )

    slots = [None] * (max_deadline + 1)
    total_profit = 0

    for job_id, deadline, profit in jobs:
        for slot in range(min(deadline, max_deadline), 0, -1):
            if slots[slot] is None:
                slots[slot] = job_id
                total_profit += profit
                break

    schedule = [
        job_id for job_id in slots[1:]
        if job_id is not None
    ]

    return schedule, total_profit


jobs = [
    ("A", 2, 100),
    ("B", 1, 19),
    ("C", 2, 27),
    ("D", 1, 25),
    ("E", 3, 15)
]

schedule, profit = job_sequencing(jobs)

print(schedule)
print(profit)
```

### Complexity

If `n` is the number of jobs and `D` is the maximum deadline:

- Time: O(n log n + nD)
- Auxiliary space: O(D)

The simple implementation scans backward through time slots for each job. More advanced versions can improve scheduling performance with a Disjoint Set Union data structure.

---

## 7. Minimum Number of Platforms

### Problem

Given train arrival and departure times, find the minimum number of platforms required so that no train has to wait for a platform.

### Greedy Strategy

Sort arrivals and departures separately, then process events in chronological order.

For this implementation, a platform is considered occupied until a train's departure time. A departure at time `t` frees a platform for an arrival at the same time `t`.

### Python Implementation

```python
def minimum_platforms(arrivals, departures):
    if len(arrivals) != len(departures):
        raise ValueError("Arrival and departure lists must match")

    if not arrivals:
        return 0

    arrivals = sorted(arrivals)
    departures = sorted(departures)

    platforms_needed = 0
    max_platforms = 0
    i = 0
    j = 0
    n = len(arrivals)

    while i < n:
        if arrivals[i] < departures[j]:
            platforms_needed += 1
            max_platforms = max(
                max_platforms,
                platforms_needed
            )
            i += 1
        else:
            platforms_needed -= 1
            j += 1

    return max_platforms


arrivals = [900, 940, 950, 1100, 1500, 1800]
departures = [910, 1200, 1120, 1130, 1900, 2000]

print(minimum_platforms(arrivals, departures))
```

### Complexity

- Time: O(n log n), due to sorting.
- Auxiliary space: O(n) for sorted copies.

### Important Point

The comparison between arrivals and departures depends on the problem's rules for trains arriving exactly when another departs. If platforms cannot be reused at the same timestamp, the event-ordering rule must be adjusted.

---

## 8. Greedy Graph Algorithms

Greedy strategies are also used in graph algorithms.

### Kruskal's Algorithm

Finds a Minimum Spanning Tree by considering edges in ascending order of weight and accepting an edge if it does not create a cycle.

### Prim's Algorithm

Builds a Minimum Spanning Tree by repeatedly choosing the minimum-weight edge that connects the current tree to a new vertex.

### Dijkstra's Algorithm

Finds shortest paths from a source vertex in a graph with non-negative edge weights by repeatedly processing the closest unprocessed vertex.

Dijkstra's algorithm is not correct for graphs with arbitrary negative edge weights.

Refer to your graph-algorithm notes for implementations of these algorithms.

---

## 9. Greedy vs Dynamic Programming

| Greedy Algorithms | Dynamic Programming |
|---|---|
| Makes a locally optimal choice at each step | Solves and combines smaller subproblems |
| Generally does not reconsider previous choices | Stores or reuses subproblem results |
| Often simpler and uses less memory | May require more memory |
| Works only when the greedy strategy is justified | Useful when overlapping subproblems and optimal substructure are present |
| Example: Fractional Knapsack | Example: 0/1 Knapsack |

### Important Point

Both approaches can solve some optimization problems, but they are not interchangeable.

Always check whether a greedy-choice proof exists before relying on a greedy strategy.

---

## 10. When Should You Use Greedy Algorithms?

Greedy algorithms are worth considering when:

1. A locally optimal choice can be shown to lead to an optimal solution.
2. Choices can be made in a suitable order, often after sorting.
3. The problem has a useful greedy-choice property.
4. The problem has optimal substructure.
5. There is a proof that the greedy decisions do not prevent an optimal result.

Common signs include phrases such as:
- Maximum number of non-overlapping activities
- Minimum number of resources
- Maximum profit under scheduling constraints
- Minimum spanning tree
- Fractional selection

These clues suggest possible greedy solutions, but they do not prove that greedy will work.

---

## 11. Common Mistakes

1. Assuming that choosing the locally best option always gives the global optimum.
2. Applying Fractional Knapsack logic to 0/1 Knapsack.
3. Forgetting to sort the input according to the correct greedy rule.
4. Ignoring edge cases such as empty input or zero capacity.
5. Using Dijkstra's algorithm with negative edge weights.
6. Failing to prove why the greedy choice is safe.
7. Confusing a greedy algorithm with a brute-force or dynamic programming solution.

---

## 12. Interview Questions

### Beginner

1. What is a Greedy Algorithm?
2. What is a locally optimal choice?
3. Does a greedy algorithm always find the optimal solution?
4. Explain the Activity Selection problem.
5. What is the difference between Fractional Knapsack and 0/1 Knapsack?

### Intermediate

6. Solve Activity Selection using Python.
7. Solve Fractional Knapsack.
8. Explain why greedy coin change can fail.
9. Solve Job Sequencing with Deadlines.
10. Find the minimum number of platforms required for trains.

### Advanced

11. Explain the greedy-choice property.
12. Prove why selecting the earliest-finishing activity works.
13. Explain Kruskal's and Prim's algorithms.
14. Explain why Dijkstra's algorithm requires non-negative edge weights.
15. Compare greedy algorithms with dynamic programming.

---

## 13. Coding Practice Checklist

- [ ] Activity Selection
- [ ] Fractional Knapsack
- [ ] Greedy Coin Change
- [ ] Job Sequencing with Deadlines
- [ ] Minimum Number of Platforms
- [ ] Assign Cookies
- [ ] Jump Game
- [ ] Merge Overlapping Intervals
- [ ] Non-overlapping Intervals
- [ ] Minimum Spanning Tree using Kruskal's Algorithm

---

## 14. Key Takeaways

- Greedy algorithms make choices based on a local optimization rule.
- They can be efficient, but they do not work optimally for every problem.
- Sorting is often an important first step.
- A correct greedy strategy needs a justification or proof.
- Fractional Knapsack and standard Activity Selection are classic greedy problems.
- Greedy Coin Change and 0/1 Knapsack show why choosing the locally best option is not always enough.
- Practise explaining both the algorithm and why its strategy works.
