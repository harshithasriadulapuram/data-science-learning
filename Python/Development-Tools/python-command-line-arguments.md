
# Python Command-Line Arguments

## 1. What Are Command-Line Arguments?

Command-line arguments are values passed to a program when it is executed from a terminal.

They allow users to provide input without changing the source code.

For example:

```bash
python app.py Harshitha
```

Here, `Harshitha` is an argument passed to the program.

Command-line arguments are useful for:
- Running scripts with different inputs.
- Passing file paths and configuration values.
- Automating data-processing tasks.
- Building command-line applications.
- Running ML training scripts with different parameters.

## 2. Access Arguments Using `sys.argv`

Python's `sys` module provides access to command-line arguments through `sys.argv`.

```python
import sys

print(sys.argv)
```

Save this as `app.py` and run:

```bash
python app.py Harshitha 25
```

Example output:

```text
['app.py', 'Harshitha', '25']
```

Important points:
- `sys.argv` is a list of strings.
- `sys.argv[0]` is usually the script name.
- `sys.argv[1]` is the first user-supplied argument.
- `sys.argv[2]` is the second user-supplied argument.

## 3. Create a Simple Program With `sys.argv`

```python
import sys


def main() -> None:
    if len(sys.argv) < 2:
        print("Usage: python app.py <name>")
        return

    name = sys.argv[1]
    print(f"Hello, {name}!")


if __name__ == "__main__":
    main()
```

Run:

```bash
python app.py Harshitha
```

Output:

```text
Hello, Harshitha!
```

If the argument is missing, the program prints a usage message instead of accessing a nonexistent list element.

## 4. Convert Argument Types

Command-line arguments are strings. Convert them when you need numbers or other types.

```python
import sys


def main() -> None:
    if len(sys.argv) != 3:
        print("Usage: python app.py <number1> <number2>")
        return

    try:
        first = float(sys.argv[1])
        second = float(sys.argv[2])
    except ValueError:
        print("Please provide valid numbers.")
        return

    print("Sum:", first + second)


if __name__ == "__main__":
    main()
```

Run:

```bash
python app.py 10 20
```

Output:

```text
Sum: 30.0
```

The `try` and `except` blocks handle invalid numeric input.

## 5. Use `argparse` for Real Applications

For more than a few arguments, Python's built-in `argparse` module is usually a better choice.

It supports:
- Named options.
- Required arguments.
- Default values.
- Type conversion.
- Automatic help messages.
- Input validation.

Example:

```python
import argparse


def main() -> None:
    parser = argparse.ArgumentParser(
        description="Calculate the sum of two numbers."
    )

    parser.add_argument(
        "first",
        type=float,
        help="First number",
    )
    parser.add_argument(
        "second",
        type=float,
        help="Second number",
    )

    args = parser.parse_args()

    print("Sum:", args.first + args.second)


if __name__ == "__main__":
    main()
```

Run:

```bash
python app.py 10 20
```

Output:

```text
Sum: 30.0
```

Display help:

```bash
python app.py --help
```

`argparse` automatically generates usage instructions and help text.

## 6. Positional and Optional Arguments

### Positional arguments

These are identified by their position.

```python
parser.add_argument("filename")
```

Usage:

```bash
python app.py data.csv
```

### Optional arguments

These are identified by flags such as `--name`.

```python
parser.add_argument(
    "--environment",
    default="development",
    help="Application environment",
)
```

Usage:

```bash
python app.py --environment production
```

Optional arguments can have defaults, and they need not appear in a particular position relative to other optional arguments.

## 7. Boolean Flags

Use `action="store_true"` for an optional flag that enables a feature.

```python
import argparse


parser = argparse.ArgumentParser()

parser.add_argument(
    "--verbose",
    action="store_true",
    help="Enable verbose output",
)

args = parser.parse_args()

if args.verbose:
    print("Verbose mode enabled")
```

Run:

```bash
python app.py --verbose
```

Without the flag, `args.verbose` is `False`. With the flag, it is `True`.

## 8. Restrict Allowed Values

Use `choices` when only certain values are valid.

```python
import argparse


parser = argparse.ArgumentParser()

parser.add_argument(
    "--model",
    choices=["linear", "random_forest", "gradient_boosting"],
    default="random_forest",
)

args = parser.parse_args()

print("Selected model:", args.model)
```

Run:

```bash
python train.py --model gradient_boosting
```

This is useful when building ML scripts that allow users to choose a model.

## 9. Practical Example: Data Processing Script

Imagine a script that processes a CSV file and allows the user to select an output path.

```python
import argparse
from pathlib import Path


def main() -> None:
    parser = argparse.ArgumentParser(
        description="Configure a CSV processing task."
    )

    parser.add_argument(
        "--input",
        type=Path,
        required=True,
        help="Path to the input CSV file",
    )
    parser.add_argument(
        "--output",
        type=Path,
        default=Path("output.csv"),
        help="Path for the output CSV file",
    )

    args = parser.parse_args()

    if not args.input.is_file():
        parser.error(f"Input file does not exist: {args.input}")

    print("Input file:", args.input)
    print("Output file:", args.output)


if __name__ == "__main__":
    main()
```

Run:

```bash
python process_data.py --input data.csv --output cleaned_data.csv
```

This example validates the input file and displays the selected paths. It does not perform the actual CSV transformation yet.

## 10. Common Mistakes

### Mistake 1: Assuming arguments are numbers

Arguments arrive as strings. Use `type=int`, `type=float`, or explicit conversion.

### Mistake 2: Accessing a missing `sys.argv` element

Check the number of arguments before indexing the list.

### Mistake 3: Writing a custom parser for everything

Prefer `argparse` for structured command-line interfaces.

### Mistake 4: Ignoring invalid inputs

Validate file paths, numeric values, and permitted choices.

### Mistake 5: Passing secrets on the command line

Command-line arguments may appear in shell history or process listings. Avoid passing passwords and API keys this way; use an appropriate secret-management mechanism instead.

## 11. Interview Questions

**Q1. What is `sys.argv`?**

A list of strings containing the script name and command-line arguments.

**Q2. What is `argparse`?**

A standard-library module for creating command-line interfaces and parsing arguments.

**Q3. What is the difference between positional and optional arguments?**

Positional arguments are identified by their position, while optional arguments are generally identified by flags such as `--output`.

**Q4. Why use `type=float` in `argparse`?**

It converts the supplied value into a floating-point number and reports an error when conversion fails.

**Q5. How do you display help for an `argparse` program?**

Run the program with `--help`.

**Q6. Why should secrets not be passed as command-line arguments?**

They may be exposed through shell history, process information, or logs.

## 12. Practice Tasks

- [ ] Print all command-line arguments using `sys.argv`.
- [ ] Write a program that greets a user by name.
- [ ] Create a calculator accepting two numbers.
- [ ] Rewrite the calculator using `argparse`.
- [ ] Add an optional `--verbose` flag.
- [ ] Restrict a model argument using `choices`.
- [ ] Create a script accepting input and output file paths.
- [ ] Explain the difference between positional and optional arguments.

## Key Takeaways

- `sys.argv` provides basic access to command-line arguments.
- `argparse` is suitable for structured command-line applications.
- Arguments are strings unless converted or parsed into another type.
- Validate user input and provide useful error messages.
- Avoid passing sensitive credentials through command-line arguments.
