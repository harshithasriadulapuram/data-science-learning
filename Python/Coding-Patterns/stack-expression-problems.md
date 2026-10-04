
# Stack-Based Expression Problems — Interview Practice

## 1. Why Are Stacks Used in Expression Problems?

A stack follows **LIFO (Last In, First Out)**.

Expression problems often require processing the most recently opened parenthesis, operator, or operand first.

Common applications include:

- Checking balanced parentheses.
- Evaluating postfix expressions.
- Evaluating prefix expressions.
- Converting infix expressions to postfix.
- Simplifying mathematical expressions.
- Finding the next operation according to precedence.

---

## 2. Valid Parentheses

Given a string containing `()`, `{}`, and `[]`, determine whether the brackets are correctly balanced.

### Example

```python
print(is_valid("()[]{}"))  # True
print(is_valid("([{}])"))  # True
print(is_valid("(]"))      # False
print(is_valid("([)]"))    # False
```

### Solution

```python
def is_valid(s):
    stack = []
    pairs = {
        ")": "(",
        "]": "[",
        "}": "{"
    }

    for char in s:
        if char in "([{":
            stack.append(char)

        elif char in pairs:
            if not stack or stack[-1] != pairs[char]:
                return False

            stack.pop()

    return not stack
```

### Explanation

1. Push opening brackets onto the stack.
2. For a closing bracket, check whether the stack is nonempty and the top contains its matching opening bracket.
3. If they match, pop the opening bracket.
4. If they do not match, return `False`.
5. At the end, the stack must be empty.

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

---

## 3. Evaluate a Postfix Expression

In postfix notation, the operator comes after its operands.

Example:

`2 3 + 4 *`

This represents `(2 + 3) * 4`, which equals `20`.

### Solution

```python
def evaluate_postfix(tokens):
    stack = []

    for token in tokens:
        if token not in {"+", "-", "*", "/"}:
            stack.append(int(token))
            continue

        if len(stack) < 2:
            raise ValueError("Invalid postfix expression")

        right = stack.pop()
        left = stack.pop()

        if token == "+":
            result = left + right
        elif token == "-":
            result = left - right
        elif token == "*":
            result = left * right
        else:
            if right == 0:
                raise ZeroDivisionError("Division by zero")

            # Truncate toward zero, as in common
            # programming interview expression problems.
            result = int(left / right)

        stack.append(result)

    if len(stack) != 1:
        raise ValueError("Invalid postfix expression")

    return stack[0]


print(evaluate_postfix(["2", "3", "+", "4", "*"]))
# 20

print(evaluate_postfix(["8", "3", "-"]))
# 5
```

### Key Idea

When an operator appears:

1. Pop the right operand.
2. Pop the left operand.
3. Perform `left operator right`.
4. Push the result.

**Important:** Operand order matters for subtraction and division.

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

This implementation assumes integer operands and the four basic operators. It is designed for programming-interview inputs, not arbitrary mathematical syntax.

---

## 4. Evaluate a Prefix Expression

In prefix notation, the operator comes before its operands.

Example:

`* + 2 3 4`

This represents `(2 + 3) * 4`, which equals `20`.

### Solution

```python
def evaluate_prefix(tokens):
    stack = []

    for token in reversed(tokens):
        if token not in {"+", "-", "*", "/"}:
            stack.append(int(token))
            continue

        if len(stack) < 2:
            raise ValueError("Invalid prefix expression")

        left = stack.pop()
        right = stack.pop()

        if token == "+":
            result = left + right
        elif token == "-":
            result = left - right
        elif token == "*":
            result = left * right
        else:
            if right == 0:
                raise ZeroDivisionError("Division by zero")

            result = int(left / right)

        stack.append(result)

    if len(stack) != 1:
        raise ValueError("Invalid prefix expression")

    return stack[0]


print(evaluate_prefix(["*", "+", "2", "3", "4"]))
# 20

print(evaluate_prefix(["-", "8", "3"]))
# 5
```

### Why Reverse the Tokens?

Reading a prefix expression from right to left allows us to process the right operand before the left operand. We then pop the left operand followed by the right operand.

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

---

## 5. Infix, Prefix, and Postfix Notation

| Notation | Example | Meaning |
|---|---|---|
| Infix | `2 + 3` | Operator between operands |
| Prefix | `+ 2 3` | Operator before operands |
| Postfix | `2 3 +` | Operator after operands |

Infix notation is familiar to humans but requires operator precedence and parentheses to determine evaluation order.

Prefix and postfix expressions can be evaluated using stacks without requiring parentheses to determine that order.

---

## 6. Operator Precedence

For the basic operators:

1. Parentheses have the highest priority.
2. Multiplication and division generally have higher priority than addition and subtraction.
3. Operators with equal precedence are generally evaluated left to right for `+`, `-`, `*`, and `/`.

For example:

`2 + 3 * 4 = 14`

But:

`(2 + 3) * 4 = 20`

A correct expression evaluator must handle precedence and parentheses.

---

## 7. Convert Infix to Postfix

Use an operator stack to delay operators until their operands have been processed.

For simplicity, this implementation accepts **single-character operands** consisting of letters or digits and the operators `+`, `-`, `*`, `/`, `^`, and parentheses. It ignores whitespace.

```python
def infix_to_postfix(expression):
    output = []
    stack = []

    precedence = {
        "+": 1,
        "-": 1,
        "*": 2,
        "/": 2,
        "^": 3
    }

    right_associative = {"^"}
    valid_operators = set(precedence)

    for char in expression:
        if char.isspace():
            continue

        if char.isalnum():
            output.append(char)

        elif char == "(":
            stack.append(char)

        elif char == ")":
            while stack and stack[-1] != "(":
                output.append(stack.pop())

            if not stack:
                raise ValueError("Mismatched parentheses")

            stack.pop()

        elif char in valid_operators:
            while (
                stack
                and stack[-1] in valid_operators
                and (
                    precedence[stack[-1]] > precedence[char]
                    or (
                        precedence[stack[-1]] == precedence[char]
                        and char not in right_associative
                    )
                )
            ):
                output.append(stack.pop())

            stack.append(char)

        else:
            raise ValueError(f"Unsupported character: {char}")

    if "(" in stack:
        raise ValueError("Mismatched parentheses")

    while stack:
        output.append(stack.pop())

    return "".join(output)


print(infix_to_postfix("A+B*C"))
# ABC*+

print(infix_to_postfix("(A+B)*C"))
# AB+C*
```

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

**Limitation:** This example does not tokenize multi-digit numbers or handle unary operators such as `-5`. Also, exponentiation is right-associative; this implementation handles that rule.

---

## 8. Simplify a Mathematical Expression: Core Idea

When simplifying expressions containing parentheses, a stack can preserve the operators and context encountered earlier.

For example:

`(a + b) * c`

The parentheses indicate that `a + b` must be evaluated before multiplication by `c`.

A complete infix evaluator must handle:

- Tokenization.
- Operator precedence.
- Associativity.
- Parentheses.
- Unary operators.
- Invalid expressions.
- Division rules.

For production applications, use a properly designed parser rather than evaluating untrusted input with Python's `eval()`.

---

## 9. Common Interview Mistakes

1. Popping operands in the wrong order.
2. Forgetting to check whether the stack contains enough operands.
3. Ignoring division-by-zero errors.
4. Assuming all operands are single digits.
5. Ignoring operator precedence.
6. Mishandling mismatched parentheses.
7. Forgetting to check that exactly one result remains after expression evaluation.
8. Using `eval()` on untrusted input.

---

## 10. Practice Problems

### Beginner

- Valid Parentheses.
- Evaluate Reverse Polish Notation.
- Baseball Game.
- Remove Outermost Parentheses.

### Intermediate

- Infix to Postfix Conversion.
- Evaluate Prefix Expression.
- Basic Calculator.
- Basic Calculator II.
- Decode String.

### Advanced

- Basic Calculator III.
- Expression Add Operators.
- Different Ways to Add Parentheses.
- Build an Expression Tree.

---

## 11. Interview Checklist

Before coding, ask:

1. Which notation does the input use: infix, prefix, or postfix?
2. Should I scan left to right or right to left?
3. When an operator appears, which operand should be popped first?
4. Do I need to maintain operator precedence?
5. Are parentheses present?
6. Can operands contain multiple digits or decimals?
7. What should happen for invalid input or division by zero?

### Final Takeaway

Use a stack to manage the order of operations and recently encountered symbols. For postfix expressions, scan left to right; for prefix expressions, scan right to left. Infix expressions require additional handling for precedence and parentheses.
