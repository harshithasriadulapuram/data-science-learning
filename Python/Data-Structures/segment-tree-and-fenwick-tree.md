
# Segment Trees and Fenwick Trees in Python

## 1. Why Do We Need These Data Structures?

Suppose we have an array:

```python
arr = [2, 4, 6, 8, 10]
```

We want to calculate the sum of elements between two indices repeatedly.

For example:
- Sum from index 1 to 3 = 4 + 6 + 8 = 18
- Sum from index 0 to 4 = 30

A simple loop calculates each range sum in O(n) time.

If the array receives frequent updates and range queries, repeated loops can become expensive.

**Segment Trees and Fenwick Trees** support efficient range queries and updates.

Common applications include:
- Competitive programming
- Range sum queries
- Frequency counting
- Dynamic arrays
- Interval-based calculations

## 2. What Is a Segment Tree?

A Segment Tree is a tree-based data structure that divides an array into segments.

Each node stores information about a segment, such as:
- Sum
- Minimum
- Maximum
- Greatest common divisor

For a range-sum Segment Tree, each node stores the sum of the elements in its interval.

Example array:

`[2, 4, 6, 8]`

The root stores the total sum: `20`.

Its children represent smaller segments:
- Left segment `[2, 4]`, sum = `6`
- Right segment `[6, 8]`, sum = `14`

The tree divides these segments until each leaf represents one array element.

## 3. Segment Tree Operations

A Segment Tree commonly supports:

1. Build: Create the tree from an array.
2. Query: Calculate information over a range.
3. Update: Change one array element and update affected nodes.

For a tree with `n` elements:
- Build: O(n)
- Range query: O(log n)
- Point update: O(log n)
- Space: O(n)

These bounds assume a standard Segment Tree and supported associative operations such as sum, minimum, or maximum.

## 4. Implement a Segment Tree for Range Sum

This implementation uses zero-based, inclusive query ranges.

```python
class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)

        if self.n > 0:
            self._build(arr, 0, 0, self.n - 1)

    def _build(self, arr, node, start, end):
        if start == end:
            self.tree[node] = arr[start]
            return

        mid = (start + end) // 2

        self._build(arr, 2 * node + 1, start, mid)
        self._build(arr, 2 * node + 2, mid + 1, end)

        self.tree[node] = (
            self.tree[2 * node + 1]
            + self.tree[2 * node + 2]
        )

    def query(self, left, right):
        if self.n == 0:
            raise ValueError("Cannot query an empty array")

        if left < 0 or right >= self.n or left > right:
            raise ValueError("Invalid query range")

        return self._query(
            0, 0, self.n - 1, left, right
        )

    def _query(self, node, start, end, left, right):
        # No overlap
        if right < start or end < left:
            return 0

        # Complete overlap
        if left <= start and end <= right:
            return self.tree[node]

        # Partial overlap
        mid = (start + end) // 2

        left_sum = self._query(
            2 * node + 1, start, mid, left, right
        )

        right_sum = self._query(
            2 * node + 2, mid + 1, end, left, right
        )

        return left_sum + right_sum

    def update(self, index, value):
        if index < 0 or index >= self.n:
            raise IndexError("Index out of range")

        self._update(
            0, 0, self.n - 1, index, value
        )

    def _update(self, node, start, end, index, value):
        if start == end:
            self.tree[node] = value
            return

        mid = (start + end) // 2

        if index <= mid:
            self._update(
                2 * node + 1, start, mid, index, value
            )
        else:
            self._update(
                2 * node + 2, mid + 1, end, index, value
            )

        self.tree[node] = (
            self.tree[2 * node + 1]
            + self.tree[2 * node + 2]
        )


arr = [2, 4, 6, 8, 10]
st = SegmentTree(arr)

print(st.query(1, 3))  # 18
print(st.query(0, 4))  # 30

st.update(2, 100)
print(st.query(1, 3))  # 112
```

### How the query works

A query compares the requested range with the segment represented by each node.

- **No overlap:** Return `0`, the identity value for addition.
- **Complete overlap:** Return the stored segment sum.
- **Partial overlap:** Query both children and add their results.

For a minimum Segment Tree, the no-overlap identity would instead be positive infinity. For a maximum Segment Tree, it would be negative infinity.

## 5. What Is a Fenwick Tree?

A **Fenwick Tree**, also called a **Binary Indexed Tree (BIT)**, is a data structure designed primarily for efficient prefix-sum queries and point updates.

Instead of storing a full tree of intervals, it stores partial sums in an array.

Its key operations are:
- Add a value to an array element.
- Calculate the sum from index `0` through index `i`.
- Calculate the sum between two indices.

For standard point updates and prefix sums:
- Build by repeated updates: O(n log n)
- Point update: O(log n)
- Prefix sum: O(log n)
- Range sum: O(log n)
- Space: O(n)

A linear-time Fenwick Tree construction is also possible.

## 6. The Lowbit Operation

Fenwick Trees use this expression:

```python
i & -i
```

It extracts the lowest set bit of a positive integer.

For example:

```python
i = 12
print(i & -i)  # 4
```

In binary:

```text
12 = 1100
 4 = 0100
```

The result helps determine how far an update or query should move through the tree.

## 7. Implement a Fenwick Tree

This implementation uses one-based indexing internally and zero-based indexing for its public methods.

```python
class FenwickTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (self.n + 1)

        for index, value in enumerate(arr):
            self.add(index, value)

    def add(self, index, delta):
        """Add delta to the element at index."""
        if index < 0 or index >= self.n:
            raise IndexError("Index out of range")

        i = index + 1

        while i <= self.n:
            self.tree[i] += delta
            i += i & -i

    def prefix_sum(self, index):
        """Return the sum from index 0 through index."""
        if index < 0:
            return 0

        if index >= self.n:
            raise IndexError("Index out of range")

        total = 0
        i = index + 1

        while i > 0:
            total += self.tree[i]
            i -= i & -i

        return total

    def range_sum(self, left, right):
        """Return the inclusive sum from left through right."""
        if (
            left < 0
            or right >= self.n
            or left > right
        ):
            raise ValueError("Invalid query range")

        return (
            self.prefix_sum(right)
            - self.prefix_sum(left - 1)
        )


arr = [2, 4, 6, 8, 10]
bit = FenwickTree(arr)

print(bit.prefix_sum(2))   # 12
print(bit.range_sum(1, 3)) # 18

bit.add(2, 94)  # Change 6 to 100 by adding 94
print(bit.range_sum(1, 3)) # 112
```

### Important distinction: update by delta

The Fenwick Tree's `add(index, delta)` method **adds** a change to an element; it does not replace the element directly.

To replace an existing value, first determine the difference between the new and old values, then call `add(index, difference)`.

For example, replacing `6` with `100` requires adding `100 - 6 = 94`.

## 8. Segment Tree vs Fenwick Tree

| Feature | Segment Tree | Fenwick Tree |
|---|---|---|
| Point updates | O(log n) | O(log n) |
| Range-sum queries | O(log n) | O(log n) |
| Prefix-sum queries | O(log n) | O(log n) |
| Typical space | O(n) | O(n) |
| Implementation | More complex | Simpler |
| Range minimum/maximum | Supported with suitable node data | Not generally supported by the basic BIT |
| Lazy range updates | Possible with extensions | Specialized techniques required |

### Which should you choose?

Use a **Fenwick Tree** when you mainly need point updates and prefix or range sums.

Use a **Segment Tree** when you need more flexible range queries, such as minimum, maximum, or other mergeable information. More advanced variants can also support range updates.

## 9. Common Interview Questions

1. What is a Segment Tree?
2. What is a Fenwick Tree?
3. Why is a Fenwick Tree also called a Binary Indexed Tree?
4. What does `i & -i` calculate?
5. What are the time complexities of building, querying, and updating a Segment Tree?
6. How does a Fenwick Tree calculate a prefix sum?
7. How do you calculate a range sum using two prefix sums?
8. What is the difference between a Segment Tree and a Fenwick Tree?
9. What is lazy propagation in a Segment Tree?
10. Why is a Segment Tree more flexible for range minimum queries?

## 10. Practice Problems

### Beginner
1. Build a Segment Tree for range-sum queries.
2. Implement point updates in a Segment Tree.
3. Build a Fenwick Tree for prefix sums.
4. Calculate inclusive range sums with a Fenwick Tree.

### Intermediate
5. Implement a Segment Tree for range minimum queries.
6. Count the frequency of values using a Fenwick Tree.
7. Find prefix sums after multiple point updates.
8. Compare both data structures on the same input.

### Advanced
9. Implement lazy propagation for range updates.
10. Use a Fenwick Tree to count inversions in an array.
11. Use a Fenwick Tree for frequency-based order statistics.
12. Solve a range-query problem from a coding platform.

## Key Takeaways

- Segment Trees and Fenwick Trees support efficient range queries and updates.
- Segment Trees offer greater flexibility for different query operations.
- Fenwick Trees are often simpler and more compact for prefix and range sums.
- The Fenwick Tree lowbit expression is `i & -i`.
- Choosing the right data structure depends on the operations your problem requires.
