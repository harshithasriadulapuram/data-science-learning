# Python File Handling

File handling allows Python programs to read data from files and write results to files.

## 1. Opening a File

Use `open()` to open a file.

```python
file = open("example.txt", "r", encoding="utf-8")
content = file.read()
print(content)
file.close()
```

Closing a file releases the associated resource. The `with` statement is usually safer because it closes the file automatically.

## 2. Using with open()

```python
with open("example.txt", "r", encoding="utf-8") as file:
    content = file.read()
    print(content)
```

Prefer this pattern for normal file operations.

## 3. File Modes

- `"r"`: Read an existing file; raises `FileNotFoundError` if missing.
- `"w"`: Write; creates a file if needed and overwrites existing content.
- `"a"`: Append to the end; creates a file if needed.
- `"x"`: Create a new file; fails if it already exists.
- `"b"`: Binary mode, such as `"rb"` or `"wb"`.
- `"+"`: Enable both reading and writing with the selected mode.

## 4. Reading a File

```python
with open("example.txt", "r", encoding="utf-8") as file:
    text = file.read()
    print(text)
```

Read line by line:

```python
with open("example.txt", "r", encoding="utf-8") as file:
    for line in file:
        print(line.rstrip())
```

Other methods include `readline()` for one line and `readlines()` for a list of lines.

## 5. Writing to a File

```python
with open("output.txt", "w", encoding="utf-8") as file:
    file.write("Hello, Python!\n")
    file.write("Learning file handling.\n")
```

Remember: `"w"` overwrites existing content.

## 6. Appending to a File

```python
with open("output.txt", "a", encoding="utf-8") as file:
    file.write("This line is appended.\n")
```

Append mode preserves existing content and writes at the end.

## 7. Working with File Paths

```python
from pathlib import Path

path = Path("data") / "example.txt"
print(path.exists())
print(path.name)
print(path.suffix)
```

`pathlib` helps construct paths in a platform-friendly way. Relative paths are resolved from the program's current working directory.

## 8. Handling Exceptions

```python
try:
    with open("missing.txt", "r", encoding="utf-8") as file:
        print(file.read())
except FileNotFoundError:
    print("The file does not exist.")
except PermissionError:
    print("You do not have permission to read this file.")
```

Handle exceptions you can reasonably anticipate. Avoid hiding every error with a broad `except` clause.

## 9. Working with JSON

JSON is commonly used to store structured data.

```python
import json

student = {"name": "Harshitha", "score": 95}

with open("student.json", "w", encoding="utf-8") as file:
    json.dump(student, file, indent=2)

with open("student.json", "r", encoding="utf-8") as file:
    loaded_student = json.load(file)

print(loaded_student["name"])
```

## 10. Working with CSV

CSV files store tabular data. Python provides the `csv` module.

```python
import csv

with open("students.csv", "w", newline="", encoding="utf-8") as file:
    writer = csv.writer(file)
    writer.writerow(["name", "score"])
    writer.writerow(["Harshitha", 95])
```

Read rows:

```python
import csv

with open("students.csv", "r", newline="", encoding="utf-8") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row["name"], row["score"])
```

For larger Data Science workflows, pandas can also read CSV files using `pd.read_csv()`.

## 11. Common Mistakes

- Forgetting to close a file when not using `with`.
- Using `"w"` when you intended to append.
- Opening a file from the wrong working directory.
- Forgetting `encoding="utf-8"` for text files where encoding matters.
- Using `newline=""` incorrectly when working with CSV files.
- Assuming every file exists or is accessible.

## Practice Problems

1. Create a text file and write five lines to it.
2. Read a file and count its lines.
3. Count words and characters in a text file.
4. Append a new entry to an existing file.
5. Copy the contents of one text file to another.
6. Read a JSON file and print selected values.
7. Read a CSV file and calculate the average of a numeric column.
8. Handle a missing file gracefully.

## Key Takeaways

- Use `open()` with the correct mode.
- Prefer `with open()` to manage file resources safely.
- Know the difference between read, write, append, and create modes.
- Use `pathlib` for paths, `json` for JSON data, and `csv` for CSV files.
- Handle expected file errors explicitly.
