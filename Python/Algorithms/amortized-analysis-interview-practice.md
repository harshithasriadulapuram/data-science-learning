
# Amortized Analysis — Python Interview Practice

## 1. What Is Amortized Analysis?

Amortized analysis calculates the average cost per operation over a sequence of operations, even when individual operations have different costs.

It provides a guarantee over the sequence; it is not the same as assuming a random input or calculating an average over random cases.

### Example: Python list append

Appending an element to a Python list is usually O(1).

However, when the underlying storage is full, Python may need to allocate a larger storage area and copy existing elements. That individual append can take O(n).

Despite these occasional expensive operations, the amortized cost of appending to a dynamic array is O(1).

## 2. Why Do We Need It?

Consider performing 1,000 append operations on a list.

Most appends are inexpensive. A few may require resizing and copying existing elements.

If we analyzed only the most expensive individual append, we might incorrectly conclude that every append costs O(n).

Amortized analysis considers the total work across the complete sequence.

## 3. Dynamic Array Example

A dynamic array grows its capacity when the existing storage is insufficient.

The following is a simplified educational implementation. It demonstrates the idea; Python's actual list allocation strategy is implementation-dependent.

```python
class DynamicArray:
    def __init__(self):
        self.capacity = 1
        self.size = 0
        self.data = [None] * self.capacity

    def append(self, value):
        if self.size == self.capacity:
            self._resize()

        self.data[self.size] = value
        self.size += 1

    def _resize(self):
        new_capacity = self.capacity * 2
        new_data = [None] * new_capacity

        for i in range(self.size):
            new_data[i] = self.data[i]

        self.data = new_data
        self.capacity = new_capacity

    def get(self, index):
        if index < 0 or index >= self.size:
            raise IndexError("Index out of range")
        return self.data[index]

    def __len__(self):
        return self.size


array = DynamicArray()

for number in range(10):
    array.append(number)

print(len(array))       # 10
print(array.get(3))     # 3
print(array.capacity)   # 16
```

### Complexity

| Operation | Worst-case cost of one operation | Amortized cost |
|---|---|---|
| Append | O(n) when resizing | O(1) |
| Read by index | O(1) | O(1) |
| Resize | O(n) | Not applicable as a separate public operation |

The capacity sequence in this example is 1, 2, 4, 8, 16, and so on.

## 4. Aggregate Method

The aggregate method calculates the total cost of a sequence and divides it by the number of operations.

Suppose a dynamic array doubles its capacity whenever it becomes full.

For n append operations:

- Ordinary insertions perform constant work each.
- Resizing copies approximately 1 + 2 + 4 + 8 + ... elements.
- This geometric sum is O(n).
- The total cost of n appends is therefore O(n).

The amortized cost per append is:

Total cost / Number of operations

O(n) / n = O(1)

Therefore, append has O(1) amortized time complexity under geometric resizing.

## 5. Accounting Method

The accounting method assigns an amortized charge to each operation.

Some operations pay more than their immediate cost. The extra amount is stored as credit and used to pay for future expensive operations.

For dynamic array insertion, imagine charging a small constant amount per append. Some of that charge accumulates as credit to help pay for future resizing.

The important principle is that accumulated credit must never become negative.

This method is useful when you want to explain how inexpensive operations can collectively pay for occasional expensive ones.

## 6. Potential Method

The potential method defines a potential function that represents stored credit in the data structure.

Amortized cost is:

Amortized cost = Actual cost + Change in potential

In mathematical notation:

\[
\widehat{c_i} = c_i + \Phi_i - \Phi_{i-1}
\]

Where:

- \(c_i\) is the actual cost of operation i.
- \(\Phi_i\) is the potential after operation i.
- \(\Phi_{i-1}\) is the potential before operation i.
- \(\widehat{c_i}\) is the amortized cost.

The potential function is selected to reflect stored capacity or work that can pay for future operations.

You generally do not need to derive a complicated potential function in an entry-level interview unless specifically asked.

## 7. Amortized Analysis vs Average-Case Analysis

| Amortized analysis | Average-case analysis |
|---|---|
| Analyzes a sequence of operations | Analyzes expected cost under an input distribution |
| Does not require random inputs | Usually requires assumptions about input probabilities |
| Controls the total cost across a sequence | Computes expected cost for an operation or input |
| Example: dynamic array append | Example: expected search cost under a probability model |

Do not use these terms interchangeably.

## 8. Other Useful Examples

### Stack with push and pop

A standard stack implemented using a Python list supports:

- `append(value)`: O(1) amortized time.
- `pop()`: O(1) amortized time when removing from the end.

A single append can still trigger resizing.

### Queue using two stacks

A queue can be implemented using two stacks.

Each element is moved from the input stack to the output stack at most once, then removed from the output stack.

For n queue operations, the total transfer work is O(n). Thus, enqueue and dequeue operations can have O(1) amortized time under the standard two-stack implementation.

One individual dequeue may transfer many elements, so its worst-case cost can be O(n).

### Union-Find / Disjoint Set Union

Union-Find with path compression and union by rank or size has an amortized complexity involving the inverse Ackermann function, commonly written as O(α(n)) per operation in standard analyses.

For practical input sizes, this is extremely close to constant time. The precise bound applies to sequences of operations under the stated implementation and analysis assumptions.

## 9. Common Interview Questions

### Q1. What is amortized time complexity?

It is the cost per operation guaranteed over a sequence of operations, based on the total cost of that sequence.

### Q2. Why is Python list append O(1) amortized?

Most appends require constant work. Occasional resizing operations are more expensive, but geometric growth spreads their total cost across many appends.

### Q3. Does amortized O(1) mean every operation takes constant time?

No. An individual operation can take O(n), while the sequence still has O(1) amortized cost per operation.

### Q4. Is amortized analysis the same as average-case analysis?

No. Amortized analysis provides a sequence-based bound; average-case analysis depends on a probability distribution over inputs.

### Q5. Which method can be used for amortized analysis?

The aggregate method, accounting method, and potential method are three standard approaches.

### Q6. Why does geometric resizing help?

Doubling capacity makes the total number of copied elements across many resizes proportional to the number of appended elements.

### Q7. Is every list operation O(1)?

No. Access by index is O(1), append is O(1) amortized, but inserting at the beginning or removing from the beginning generally requires shifting elements and takes O(n).

## 10. Practice Problems

For each question, explain both the individual worst-case cost and the amortized cost where applicable.

1. Why is dynamic array append amortized O(1)?
2. What happens when a dynamic array runs out of capacity?
3. Why is inserting at index zero in a Python list O(n)?
4. Explain amortized analysis using the aggregate method.
5. Explain the difference between amortized and average-case complexity.
6. Why can one queue dequeue take O(n) in a two-stack queue?
7. What is the purpose of the potential function?
8. Why does doubling capacity lead to a geometric sum?
9. What are the aggregate, accounting, and potential methods?
10. Describe a real data structure operation that has occasional expensive steps.

## 11. Final Checklist

- [ ] Explain amortized analysis in your own words.
- [ ] Distinguish amortized cost from worst-case cost.
- [ ] Distinguish amortized analysis from average-case analysis.
- [ ] Derive the geometric sum for dynamic array resizing.
- [ ] Explain all three standard amortized analysis methods.
- [ ] Describe one example beyond dynamic arrays.
- [ ] State complexity bounds with their assumptions.

**Interview tip:** A strong answer does not merely say "append is O(1)." Say that it is O(1) amortized because occasional O(n) resizing costs are spread across a sequence of appends.
