
# Bit Manipulation — Interview Practice

## 1. What Is Bit Manipulation?

Bit manipulation involves working directly with the binary representation of integers using bitwise operators.

Computers represent integers using bits: `0` and `1`.

For example:

```text
Decimal 5 = Binary 0101
Decimal 3 = Binary 0011
```

Bit manipulation is useful in coding interviews, optimization, bitmasking, and low-level programming.

## 2. Bitwise Operators in Python

| Operator | Name | Example |
|---|---|---|
| `&` | AND | `5 & 3` gives `1` |
| `\|` | OR | `5 \| 3` gives `7` |
| `^` | XOR | `5 ^ 3` gives `6` |
| `~` | NOT | `~5` gives `-6` |
| `<<` | Left shift | `5 << 1` gives `10` |
| `>>` | Right shift | `5 >> 1` gives `2` |

### AND (`&`)

A bit is `1` only when both corresponding bits are `1`.

```python
print(5 & 3)  # 1
```

```text
  0101
& 0011
------
  0001
```

### OR (`|`)

A bit is `1` when at least one corresponding bit is `1`.

```python
print(5 | 3)  # 7
```

### XOR (`^`)

A bit is `1` when the corresponding bits differ.

```python
print(5 ^ 3)  # 6
```

Important properties:

```python
x ^ x == 0
x ^ 0 == x
```

### NOT (`~`)

In Python, integers have conceptually unlimited sign extension, so:

```python
print(~5)  # -6
```

For non-negative integers, `~x == -x - 1`.

### Left Shift (`<<`)

Shifts bits to the left. For non-negative integers, shifting left by `k` positions is equivalent to multiplying by `2**k`.

```python
print(5 << 1)  # 10
print(5 << 2)  # 20
```

### Right Shift (`>>`)

Shifts bits to the right.

```python
print(8 >> 1)  # 4
print(8 >> 2)  # 2
```

For non-negative integers, shifting right by `k` positions is equivalent to integer division by `2**k`.

## 3. Check Whether a Number Is Odd or Even

The last binary bit determines whether a non-negative integer is odd or even.

```python
def is_odd(n):
    return (n & 1) == 1


print(is_odd(7))  # True
print(is_odd(8))  # False
```

## 4. Check Whether the K-th Bit Is Set

Assume bit positions start at zero from the right.

```python
def is_bit_set(n, k):
    return (n & (1 << k)) != 0


print(is_bit_set(10, 1))  # True
print(is_bit_set(10, 2))  # False
```

For `10`, binary is `1010`, so bit positions 1 and 3 are set.

## 5. Set the K-th Bit

Setting a bit changes it to `1` without changing the other bits.

```python
def set_bit(n, k):
    return n | (1 << k)


print(set_bit(8, 1))  # 10
```

## 6. Clear the K-th Bit

Clearing a bit changes it to `0`.

```python
def clear_bit(n, k):
    return n & ~(1 << k)


print(clear_bit(10, 1))  # 8
```

## 7. Toggle the K-th Bit

Toggling changes `0` to `1` or `1` to `0`.

```python
def toggle_bit(n, k):
    return n ^ (1 << k)


print(toggle_bit(10, 1))  # 8
print(toggle_bit(10, 0))  # 11
```

## 8. Count Set Bits

A set bit is a bit whose value is `1`.

### Method 1: Built-in Function

```python
def count_set_bits(n):
    if n < 0:
        raise ValueError("Expected a non-negative integer")

    return n.bit_count()


print(count_set_bits(13))  # 3
```

### Method 2: Brian Kernighan's Algorithm

The expression `n & (n - 1)` removes the rightmost set bit.

```python
def count_set_bits(n):
    if n < 0:
        raise ValueError("Expected a non-negative integer")

    count = 0

    while n:
        n &= n - 1
        count += 1

    return count


print(count_set_bits(13))  # 3
```

This takes O(k) time, where `k` is the number of set bits.

## 9. Check Whether a Number Is a Power of Two

A positive power of two has exactly one set bit.

Examples: `1`, `2`, `4`, `8`, `16`.

```python
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0


print(is_power_of_two(16))  # True
print(is_power_of_two(18))  # False
```

The `n > 0` condition is necessary because zero also satisfies `n & (n - 1) == 0`.

## 10. Find the Single Number

Every element appears twice except one. Find the element that appears once.

XOR cancels equal pairs because `x ^ x == 0`.

```python
def single_number(nums):
    result = 0

    for num in nums:
        result ^= num

    return result


print(single_number([4, 1, 2, 1, 2]))  # 4
```

**Time complexity:** O(n)  
**Auxiliary space:** O(1).

## 11. Find Two Single Numbers

Every element appears twice except two distinct elements. Find those two elements.

```python
def two_single_numbers(nums):
    xor_all = 0

    for num in nums:
        xor_all ^= num

    # Isolate one set bit that differs between the two numbers.
    distinguishing_bit = xor_all & -xor_all

    first = 0
    second = 0

    for num in nums:
        if num & distinguishing_bit:
            first ^= num
        else:
            second ^= num

    return first, second


print(two_single_numbers([1, 2, 1, 3, 2, 5]))
# (3, 5) or (5, 3)
```

**Time complexity:** O(n)  
**Auxiliary space:** O(1).

## 12. Find the Missing Number

An array contains distinct numbers from `0` to `n`, with one number missing.

```python
def missing_number(nums):
    result = len(nums)

    for i, num in enumerate(nums):
        result ^= i ^ num

    return result


print(missing_number([3, 0, 1]))  # 2
```

XOR cancels matching values, leaving the missing number.

**Time complexity:** O(n)  
**Auxiliary space:** O(1).

## 13. Generate All Subsets Using Bitmasks

Each bit represents whether an element is included in a subset.

For `n` elements, there are `2**n` possible subsets.

```python
def generate_subsets(nums):
    n = len(nums)
    result = []

    for mask in range(1 << n):
        subset = []

        for i in range(n):
            if mask & (1 << i):
                subset.append(nums[i])

        result.append(subset)

    return result


print(generate_subsets([1, 2]))
# [[], [1], [2], [1, 2]]
```

**Time complexity:** O(n × 2**n)  
**Space complexity:** O(n × 2**n) for storing all subsets.

## 14. Useful Bit Manipulation Tricks

| Goal | Expression |
|---|---|
| Check odd | `n & 1` |
| Check whether bit `k` is set | `n & (1 << k)` |
| Set bit `k` | `n | (1 << k)` |
| Clear bit `k` | `n & ~(1 << k)` |
| Toggle bit `k` | `n ^ (1 << k)` |
| Remove rightmost set bit | `n & (n - 1)` |
| Isolate rightmost set bit | `n & -n` |
| Check power of two | `n > 0 and (n & (n - 1)) == 0` |

These expressions assume non-negative integers where the problem requires it.

## 15. Interview Practice Problems

### Beginner
- [ ] Check whether a number is odd or even using bits.
- [ ] Count the set bits of an integer.
- [ ] Check whether a number is a power of two.
- [ ] Set, clear, and toggle a bit.
- [ ] Find the single number in an array.

### Intermediate
- [ ] Find the missing number.
- [ ] Find two numbers that appear once.
- [ ] Reverse bits of a fixed-width integer.
- [ ] Compute the Hamming distance between two integers.
- [ ] Generate all subsets using bitmasks.

### Advanced
- [ ] Count bits for every number from `0` to `n`.
- [ ] Find the maximum XOR of two numbers in an array.
- [ ] Solve a bitmask dynamic programming problem.
- [ ] Find the maximum product of word lengths using bitmasks.

## 16. Common Interview Questions

**Q1. Why does `n & (n - 1)` remove the rightmost set bit?**

Subtracting one flips the rightmost set bit to zero and changes the trailing zeros to ones. AND-ing the two values clears that bit and preserves the bits to its left.

**Q2. Why does XOR help find a single number?**

XOR is associative and commutative, and `x ^ x == 0`. Equal pairs cancel, leaving the unpaired number.

**Q3. Why does a power of two have one set bit?**

Positive powers of two have binary representations containing exactly one `1`.

**Q4. What is a bitmask?**

A bitmask is an integer whose bits represent Boolean flags or choices.

**Q5. Does Python use fixed-width integers?**

Python integers have arbitrary precision. Problems involving 32-bit or 64-bit values may require explicit masking and handling of negative values.

## 17. Final Revision Checklist

- [ ] Explain all six bitwise operators.
- [ ] Convert decimal numbers to binary.
- [ ] Check, set, clear, and toggle bits.
- [ ] Count set bits using Brian Kernighan's algorithm.
- [ ] Explain XOR cancellation.
- [ ] Solve the single-number and missing-number problems.
- [ ] Generate subsets using bitmasks.
- [ ] Analyze time and space complexity.
