
# Bit Manipulation in Python

## 1. What Is Bit Manipulation?

Bit manipulation means operating directly on the binary representation of numbers.

Computers represent integers using bits:
- `0` represents a binary zero.
- `1` represents a binary one.
- Each bit represents a power of 2 in an unsigned binary number.

For example:

```text
Decimal:  13
Binary:   1101
```

The binary digits represent:

```text
1 × 8 + 1 × 4 + 0 × 2 + 1 × 1 = 13
```

Bit manipulation is useful in:
- Coding interviews
- Efficient flag and permission handling
- Sets represented as bitmasks
- Low-level programming
- Algorithms involving binary representations

## 2. Binary Representation in Python

Python provides `bin()` to convert an integer to its binary representation.

```python
print(bin(5))   # 0b101
print(bin(10))  # 0b1010
print(bin(15))  # 0b1111
```

The prefix `0b` indicates a binary number.

To display the binary digits without the prefix:

```python
print(format(5, "b"))    # 101
print(format(10, "08b")) # 00001010
```

The format `08b` displays the binary number using at least eight digits, padding with leading zeros.

## 3. The Main Bitwise Operators

Python supports six common bitwise operators.

| Operator | Name | Meaning |
|---|---|---|
| `&` | AND | Bit is 1 if both bits are 1 |
| `\|` | OR | Bit is 1 if either bit is 1 |
| `^` | XOR | Bit is 1 if the bits differ |
| `~` | NOT | Inverts the integer's bits |
| `<<` | Left shift | Shifts bits to the left |
| `>>` | Right shift | Shifts bits to the right |

## 4. Bitwise AND (`&`)

AND returns `1` only when both corresponding bits are `1`.

Truth table:

| A | B | A & B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

Example:

```python
a = 12  # 1100
b = 10  # 1010

print(a & b)  # 8
```

Binary calculation:

```text
  1100
& 1010
------
  1000
```

The binary result `1000` equals decimal `8`.

### Common use: Check whether a bit is set

```python
n = 10  # 1010

print(n & (1 << 1))  # 2
print(n & (1 << 2))  # 0
```

A nonzero result means the selected bit is set.

## 5. Bitwise OR (`|`)

OR returns `1` if at least one corresponding bit is `1`.

```python
a = 12  # 1100
b = 10  # 1010

print(a | b)  # 14
```

Binary calculation:

```text
  1100
| 1010
------
  1110
```

### Common use: Set a bit

```python
n = 8       # 1000
position = 1

n = n | (1 << position)

print(n)    # 10
```

The operation sets the bit at position `1` to `1`.

## 6. Bitwise XOR (`^`)

XOR returns `1` when the corresponding bits differ.

| A | B | A ^ B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Example:

```python
a = 12  # 1100
b = 10  # 1010

print(a ^ b)  # 6
```

Binary calculation:

```text
  1100
^ 1010
------
  0110
```

### Important XOR properties

```python
print(5 ^ 0)   # 5
print(5 ^ 5)   # 0
print(5 ^ 3 ^ 3)  # 5
```

Useful properties:

- `a ^ 0 = a`
- `a ^ a = 0`
- XOR is commutative: `a ^ b = b ^ a`
- XOR is associative: `(a ^ b) ^ c = a ^ (b ^ c)`

These properties are frequently used in interview problems.

## 7. Bitwise NOT (`~`)

NOT inverts the bits of an integer.

In Python, integers have arbitrary precision and behave as though they use an infinite two's-complement representation for bitwise operations. Therefore:

```python
print(~5)   # -6
print(~0)   # -1
print(~10)  # -11
```

For Python integers:

```text
~n = -(n + 1)
```

For example:

```text
~5 = -(5 + 1) = -6
```

This differs from simply flipping a fixed eight-bit binary string. To invert a fixed-width representation, first apply a width mask.

## 8. Left Shift (`<<`)

The left-shift operator moves bits to the left.

```python
print(5 << 1)  # 10
print(5 << 2)  # 20
print(3 << 3)  # 24
```

For nonnegative integers, shifting left by `k` positions is equivalent to multiplying by \(2^k\).

```python
n = 7

print(n << 1)  # 14
print(n << 2)  # 28
```

This relationship is useful, but ordinary multiplication may be clearer in everyday application code.

## 9. Right Shift (`>>`)

The right-shift operator moves bits to the right.

```python
print(20 >> 1)  # 10
print(20 >> 2)  # 5
print(13 >> 1)  # 6
```

For nonnegative integers, right shift by `k` positions is equivalent to integer division by \(2^k\), discarding the remainder.

```python
n = 25

print(n >> 1)  # 12
print(n >> 2)  # 6
```

For negative integers, Python's right shift behaves like arithmetic right shift, rounding toward negative infinity.

## 10. Bit Positions and Masks

In most bit-manipulation problems, the rightmost bit is position `0`.

For example:

```text
Number:       13
Binary:       1101
Positions:    3210
```

A mask selects a particular bit.

```python
position = 2
mask = 1 << position

print(mask)  # 4
```

The mask is `0100` in a four-bit display. It selects position `2`.

### Check whether a bit is set

```python
def is_bit_set(n, position):
    return (n & (1 << position)) != 0


print(is_bit_set(13, 0))  # True
print(is_bit_set(13, 1))  # False
print(is_bit_set(13, 2))  # True
```

This function assumes `position` is nonnegative.

### Set a bit

```python
def set_bit(n, position):
    return n | (1 << position)


print(set_bit(8, 1))  # 10
```

### Clear a bit

```python
def clear_bit(n, position):
    return n & ~(1 << position)


print(clear_bit(15, 2))  # 11
```

### Toggle a bit

Toggling changes `0` to `1` and `1` to `0`.

```python
def toggle_bit(n, position):
    return n ^ (1 << position)


print(toggle_bit(8, 1))   # 10
print(toggle_bit(10, 1))  # 8
```

## 11. Check Whether a Number Is Odd or Even

The least significant bit indicates whether a nonnegative integer is odd or even.

```python
def is_odd(n):
    return (n & 1) == 1


print(is_odd(7))   # True
print(is_odd(12))  # False
```

Why does it work?

- Even numbers end in binary `0`.
- Odd numbers end in binary `1`.

For integer parity, this also works with negative Python integers.

## 12. Check Whether a Number Is a Power of Two

A positive power of two has exactly one set bit.

Examples:

```text
1  = 0001
2  = 0010
4  = 0100
8  = 1000
16 = 10000
```

For a positive power of two, `n & (n - 1)` equals zero.

```python
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0


print(is_power_of_two(16))  # True
print(is_power_of_two(18))  # False
print(is_power_of_two(0))   # False
```

Why?

Subtracting one from a power of two clears its only set bit and turns the lower bits into ones. ANDing the two numbers therefore produces zero.

## 13. Remove the Rightmost Set Bit

The expression:

```python
n & (n - 1)
```

clears the rightmost set bit for nonnegative integers.

Example:

```text
n     = 12 = 1100
n - 1 = 11 = 1011

1100
1011
----
1000 = 8
```

Code:

```python
n = 12
print(n & (n - 1))  # 8
```

This operation is useful for counting set bits.

## 14. Count Set Bits

A set bit is a bit whose value is `1`.

For example, binary `1011` contains three set bits.

### Method 1: Using Python's built-in method

```python
n = 11
print(n.bit_count())  # 3
```

For negative integers, `bit_count()` counts the ones in the binary representation of the absolute value. If you need a fixed-width representation, mask the value first.

### Method 2: Using a loop

```python
def count_set_bits(n):
    if n < 0:
        raise ValueError("n must be nonnegative")

    count = 0

    while n > 0:
        count += n & 1
        n >>= 1

    return count


print(count_set_bits(11))  # 3
print(count_set_bits(8))   # 1
print(count_set_bits(0))   # 0
```

**Time complexity:** O(log n) for positive `n`.

**Auxiliary space:** O(1).

### Method 3: Brian Kernighan's Algorithm

```python
def count_set_bits(n):
    if n < 0:
        raise ValueError("n must be nonnegative")

    count = 0

    while n:
        n &= n - 1
        count += 1

    return count


print(count_set_bits(15))  # 4
print(count_set_bits(12))  # 2
```

Each iteration removes one set bit.

**Time complexity:** O(k), where `k` is the number of set bits. The worst case is O(log n) for a positive integer.

**Auxiliary space:** O(1).

## 15. Find the Single Number

Every number in a list appears twice except one number. Find the number that appears once.

Input:

```python
nums = [4, 1, 2, 1, 2]
```

Output:

```text
4
```

### Code

```python
def single_number(nums):
    result = 0

    for num in nums:
        result ^= num

    return result


print(single_number([4, 1, 2, 1, 2]))  # 4
```

XOR cancels pairs because `a ^ a = 0`.

This solution assumes every number except one occurs exactly twice. It uses Python's integer XOR semantics and works with negative integers too.

**Time complexity:** O(n).

**Auxiliary space:** O(1), excluding the input.

## 16. Find Two Unique Numbers

Every number appears twice except for two distinct numbers. Find those two numbers.

```python
def two_unique_numbers(nums):
    xor_all = 0

    for num in nums:
        xor_all ^= num

    # xor_all is the XOR of the two unique numbers.
    rightmost_set_bit = xor_all & -xor_all

    first = 0
    second = 0

    for num in nums:
        if num & rightmost_set_bit:
            first ^= num
        else:
            second ^= num

    return first, second


print(two_unique_numbers([1, 2, 1, 3, 2, 5]))
```

Output order may vary, but the two values are `3` and `5`.

### How it works

1. XOR all numbers. Duplicate pairs cancel.
2. The remaining XOR is the XOR of the two unique numbers.
3. Since the unique numbers differ, their XOR contains at least one set bit.
4. Use that bit to divide the numbers into two groups.
5. Duplicate numbers fall into the same group and cancel. The two unique numbers fall into different groups.

**Time complexity:** O(n).

**Auxiliary space:** O(1).

## 17. Bitmasking

A bitmask represents multiple Boolean states using individual bits in an integer.

For example, three permissions can be represented as:

```text
Read    = 001
Write   = 010
Execute = 100
```

```python
READ = 1 << 0
WRITE = 1 << 1
EXECUTE = 1 << 2

permissions = READ | WRITE

print(bool(permissions & READ))     # True
print(bool(permissions & WRITE))    # True
print(bool(permissions & EXECUTE))  # False

# Add execute permission
permissions |= EXECUTE

# Remove write permission
permissions &= ~WRITE
```

Bitmasking is useful for permissions, flags, and representing subsets of a small set of items.

## 18. Enumerate All Subsets Using Bitmasks

A list of `n` elements has `2^n` subsets.

Each integer from `0` through `2^n - 1` can represent one subset. If bit `i` is set, include element `i`.

```python
def generate_subsets(nums):
    result = []
    n = len(nums)

    for mask in range(1 << n):
        subset = []

        for i in range(n):
            if mask & (1 << i):
                subset.append(nums[i])

        result.append(subset)

    return result


print(generate_subsets([1, 2, 3]))
```

**Time complexity:** O(n × 2^n).

**Auxiliary space:** O(n) for the current subset, excluding the output.

## 19. XOR Swap: Understand, but Prefer Python Assignment

XOR can swap two values mathematically:

```python
a = a ^ b
b = a ^ b
a = a ^ b
```

However, in Python, prefer:

```python
a, b = b, a
```

Tuple assignment is clearer and avoids unnecessary bitwise operations.

## 20. Common Mistakes

1. Confusing logical operators (`and`, `or`, `not`) with bitwise operators (`&`, `|`, `~`).
2. Forgetting that bit positions start at zero.
3. Assuming `~n` produces a nonnegative fixed-width result in Python.
4. Forgetting to exclude zero when checking for a power of two.
5. Using XOR for a single-number problem without verifying the frequency assumptions.
6. Forgetting that a shift count must be nonnegative.
7. Ignoring negative-number behavior when a problem assumes fixed-width integers.
8. Using bit manipulation when ordinary arithmetic or clearer code is more appropriate.

## 21. Practice Problems

### Beginner
- Check whether a number is odd or even.
- Check whether a number is a power of two.
- Count set bits.
- Set, clear, and toggle a bit.
- Find the single number in a list.

### Intermediate
- Find two unique numbers.
- Reverse bits of a fixed-width integer.
- Find the missing number using XOR.
- Generate subsets using bitmasks.
- Check whether a particular bit is set.

### Advanced
- Count bits from `0` to `n`.
- Maximum XOR of two numbers.
- Bitmask dynamic programming.
- Traveling Salesperson Problem using bitmask DP.
- Minimum number of bit flips to convert one integer into another.

## 22. Interview Questions

**Q1. What is bit manipulation?**

It is the technique of operating on the binary representation of integers using bitwise operators.

**Q2. What is the difference between `&` and `and`?**

`&` performs bitwise AND on integers. `and` performs logical evaluation and returns one of its operands according to truthiness.

**Q3. How do you check whether a number is a power of two?**

For a positive integer `n`, check whether `(n & (n - 1)) == 0`.

**Q4. Why is XOR useful for finding a single number?**

Equal values cancel because `a ^ a == 0`, and XOR with zero leaves a value unchanged.

**Q5. How can you set the bit at position `k`?**

Use `n | (1 << k)`.

**Q6. How can you clear the bit at position `k`?**

Use `n & ~(1 << k)`.

**Q7. What is bitmasking?**

It represents multiple Boolean states or a subset using individual bits in an integer.

## 23. Final Checklist

- [ ] Convert integers to binary using `bin()`.
- [ ] Explain AND, OR, XOR, NOT, left shift, and right shift.
- [ ] Set, clear, toggle, and test a bit.
- [ ] Check whether a number is a power of two.
- [ ] Count set bits using two different algorithms.
- [ ] Solve the single-number problem using XOR.
- [ ] Find two unique numbers using bit partitioning.
- [ ] Generate subsets using bitmasks.
- [ ] Explain Python's behavior with negative integers.
- [ ] Analyze time and auxiliary space complexity.
