
# Database Constraints and Referential Integrity

## 1. What Are Database Constraints?

Constraints are rules enforced by a database to help keep stored data valid and consistent.

Common SQL constraints include:

- `NOT NULL`
- `UNIQUE`
- `PRIMARY KEY`
- `FOREIGN KEY`
- `CHECK`
- `DEFAULT`

## 2. NOT NULL

A `NOT NULL` constraint prevents a column from storing `NULL`.

```sql
CREATE TABLE employees (
    employee_id INT,
    employee_name VARCHAR(100) NOT NULL
);
```

This prevents an employee row from having a missing name.

Remember: an empty string (`''`) is not the same as `NULL`.

## 3. UNIQUE

A `UNIQUE` constraint prevents duplicate values according to the database's uniqueness rules.

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE
);
```

This prevents duplicate non-null email values in common SQL implementations.

The treatment of `NULL` values in unique constraints varies between database systems.

## 4. PRIMARY KEY

A primary key uniquely identifies each row.

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL
);
```

A primary key:
- Must uniquely identify each row.
- Cannot contain `NULL`.
- Can consist of one column or multiple columns.

A table has only one primary-key constraint, although that constraint can contain multiple columns.

## 5. Composite Primary Key

A composite primary key consists of two or more columns.

```sql
CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id)
);
```

The combination of `student_id` and `course_id` must be unique.

This design assumes a student can enroll in a particular course only once. If multiple enrollment attempts or terms must be recorded, the key may need another column.

## 6. FOREIGN KEY

A foreign key establishes a relationship between tables and helps prevent references to nonexistent parent rows.

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100) NOT NULL
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
);
```

An order cannot reference a customer ID that does not exist in `customers`, unless the foreign-key value is `NULL` and the column permits it.

Foreign keys can reference a primary key or another suitable unique key.

## 7. Referential Integrity

**Referential integrity** means that relationships between related tables remain valid.

For example:
- A valid order should reference an existing customer.
- A department referenced by an employee should exist.
- Deleting a parent row must follow the foreign-key rules configured for its children.

Foreign keys are a primary way to enforce referential integrity.

## 8. ON DELETE Actions

A foreign key can define what happens when a referenced parent row is deleted.

### RESTRICT or NO ACTION

Prevent deletion when dependent rows would violate the relationship. Exact timing and behavior can vary by database.

```sql
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id)
ON DELETE RESTRICT
```

### CASCADE

Delete dependent child rows when the referenced parent row is deleted.

```sql
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id)
ON DELETE CASCADE
```

Use cascading deletes carefully. Deleting one customer could delete all of that customer's orders.

### SET NULL

Set the foreign-key column to `NULL` when the parent row is deleted, if the column allows `NULL`.

```sql
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id)
ON DELETE SET NULL
```

Choose the action based on business requirements rather than convenience.

## 9. CHECK Constraint

A `CHECK` constraint enforces a condition on column values.

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    price DECIMAL(10, 2) NOT NULL CHECK (price >= 0),
    stock INT NOT NULL CHECK (stock >= 0)
);
```

This helps prevent negative prices and stock values.

Important: `CHECK` behavior, including how `NULL` interacts with the condition, varies by database. `NOT NULL` is needed when a value must be present.

## 10. DEFAULT Constraint

A `DEFAULT` supplies a value when an insert omits the column or explicitly uses `DEFAULT`, depending on the database syntax.

```sql
CREATE TABLE tasks (
    task_id INT PRIMARY KEY,
    status VARCHAR(20) DEFAULT 'pending'
);
```

Example:

```sql
INSERT INTO tasks (task_id)
VALUES (1);
```

The new task receives the default status of `pending`.

A default does not generally replace an explicitly supplied `NULL`.

## 11. UNIQUE vs PRIMARY KEY

| Feature | PRIMARY KEY | UNIQUE |
|---|---|---|
| Main purpose | Identify each row | Prevent duplicate values |
| Number per table | One constraint | Multiple constraints allowed |
| NULL values | Not allowed | Depends on database rules |
| Typical example | `employee_id` | `email` |

## 12. Example: Complete Relational Design

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    salary DECIMAL(10, 2) NOT NULL CHECK (salary >= 0),
    department_id INT,
    employment_status VARCHAR(20) DEFAULT 'active',
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
        ON DELETE SET NULL
);
```

This schema combines several constraints to protect data quality.

The `department_id` in `employees` is nullable, allowing an employee to remain after their department is deleted.

## 13. Common Mistakes

- Assuming `UNIQUE` always treats `NULL` the same way across databases.
- Forgetting `NOT NULL` when a value is mandatory.
- Using `ON DELETE CASCADE` without understanding its impact.
- Assuming a foreign key automatically creates every useful index.
- Assuming `CHECK` constraints behave identically across all database versions.
- Using application validation alone when the database can enforce the same integrity rule.

## 14. Interview Questions

1. What is a database constraint?
2. Explain primary key vs. unique key.
3. What is referential integrity?
4. What is the difference between a primary key and a foreign key?
5. What is a composite primary key?
6. Explain `ON DELETE CASCADE`, `RESTRICT`, and `SET NULL`.
7. What is the purpose of a `CHECK` constraint?
8. Does `DEFAULT` replace an explicitly inserted `NULL`?
9. Can a table contain multiple foreign keys?
10. Why should important data-integrity rules be enforced in the database?

## 15. Practice Tasks

1. Create a `students` table with a primary key and a non-null name.
2. Add a unique constraint to a user's email.
3. Create `orders` with a foreign key to `customers`.
4. Prevent negative product prices using `CHECK`.
5. Test what happens when deleting a parent row with dependent records.
6. Design a many-to-many relationship using a junction table with a composite primary key.
7. Explain which constraints would protect a bank account table from invalid data.

## Key Takeaways

- Constraints enforce rules that protect data quality.
- Primary keys uniquely identify rows.
- Foreign keys establish valid relationships between tables.
- Referential integrity prevents invalid references.
- Cascading actions must be chosen carefully.
- Database-specific behavior should be checked before relying on a constraint.
