
# Database Normalization and Denormalization

## 1. What Is Database Normalization?

**Normalization** is the process of organizing database tables to reduce unnecessary data duplication and prevent data anomalies.

Consider this table:

| student_id | student_name | course_id | course_name |
|---|---|---|---|
| 1 | Anu | C101 | Python |
| 2 | Ravi | C101 | Python |
| 3 | Priya | C102 | SQL |

The course name is repeated whenever multiple students enroll in the same course.

Normalization separates students, courses, and enrollments into related tables.

## 2. Why Is Normalization Important?

Normalization helps prevent three common anomalies.

### Insertion Anomaly

You cannot insert information about a course until a student enrolls in it, if both pieces of information are stored in the same table.

### Update Anomaly

If a course name changes, multiple rows may need updating. Missing one row leaves inconsistent data.

### Deletion Anomaly

Deleting the last student enrolled in a course might accidentally delete the only stored information about that course.

## 3. First Normal Form (1NF)

A table is in **First Normal Form** when each cell contains a single value and repeating groups are avoided.

### Not in 1NF

| student_id | student_name | skills |
|---|---|---|
| 1 | Anu | Python, SQL |
| 2 | Ravi | Java, Python |

The `skills` column contains multiple values in a single cell.

### A 1NF Design

| student_id | student_name | skill |
|---|---|---|
| 1 | Anu | Python |
| 1 | Anu | SQL |
| 2 | Ravi | Java |
| 2 | Ravi | Python |

This satisfies the basic atomic-value requirement, though it is not necessarily the best final schema.

## 4. Second Normal Form (2NF)

A table is in **Second Normal Form** if:
- It is in 1NF.
- Every non-key attribute depends on the entire candidate key, not just part of a composite key.

Consider:

`enrollments(student_id, course_id, student_name, course_name, grade)`

Assume the composite primary key is `(student_id, course_id)`.

Dependencies:

- `student_id → student_name`
- `course_id → course_name`
- `(student_id, course_id) → grade`

`student_name` depends only on part of the composite key. `course_name` also depends only on part of it.

Split the table into:

**Students**

| student_id | student_name |
|---|---|
| 1 | Anu |
| 2 | Ravi |

**Courses**

| course_id | course_name |
|---|---|
| C101 | Python |
| C102 | SQL |

**Enrollments**

| student_id | course_id | grade |
|---|---|---|
| 1 | C101 | A |
| 2 | C102 | B |

Now, student and course details are stored separately.

## 5. Third Normal Form (3NF)

A table is in **Third Normal Form** if:
- It is in 2NF.
- Non-key attributes do not depend transitively on a candidate key.

Consider:

`employees(employee_id, department_id, department_name)`

Dependencies:

- `employee_id → department_id`
- `department_id → department_name`

Therefore, `employee_id` determines `department_name` indirectly through `department_id`.

Separate the data into:

**Employees**

| employee_id | department_id |
|---|---|
| 101 | D1 |
| 102 | D2 |

**Departments**

| department_id | department_name |
|---|---|
| D1 | Engineering |
| D2 | Analytics |

Now department details are stored in one place.

## 6. Boyce-Codd Normal Form (BCNF)

BCNF is a stronger version of 3NF.

A relation satisfies BCNF when every non-trivial functional dependency `X → Y` has `X` as a superkey.

In simple terms, every determinant must be a superkey.

BCNF handles certain dependency problems that can remain in tables satisfying 3NF.

Remember: BCNF is stricter than 3NF, and a decomposition into BCNF can sometimes make dependency enforcement more complicated.

## 7. SQL Example: Normalized Schema

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department_id INT,
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);
```

Benefits:
- Department names are stored once.
- Employees reference departments using a foreign key.
- Referential integrity helps prevent references to nonexistent departments.

## 8. What Is Denormalization?

**Denormalization** deliberately introduces redundancy or precomputed data to improve read performance or simplify frequent queries.

For example, an analytics table might store:

| order_id | customer_id | customer_name | order_total |
|---|---|---|---|
| 501 | 10 | Anu | 1500 |
| 502 | 10 | Anu | 800 |

The customer name is repeated. This can make reporting queries simpler, but changing the customer's name may require updating multiple rows.

Denormalization should be intentional and supported by a clear performance or reporting requirement.

## 9. Normalization vs. Denormalization

| Feature | Normalization | Denormalization |
|---|---|---|
| Main goal | Reduce redundancy | Improve read efficiency |
| Data duplication | Usually reduced | May be introduced |
| Updates | Often easier to keep consistent | May require updating multiple copies |
| Reads | May require more joins | May need fewer joins |
| Storage | Often more efficient | May use more storage |
| Common use | Transactional systems | Reporting and analytics systems |

Neither approach is always better. Choose based on data integrity, query patterns, workload, and measured performance.

## 10. When Should You Use Each?

### Prefer Normalization When

- Many records are inserted or updated.
- Data consistency is important.
- The same facts would otherwise be duplicated.
- You are designing a transactional application.

### Consider Denormalization When

- Read-heavy queries are demonstrably slow.
- Reporting queries require expensive repeated joins.
- A data warehouse or analytical workload benefits from precomputed summaries.
- You can manage the additional consistency requirements.

Do not denormalize just because joins seem complicated. Measure the performance problem first.

## 11. Interview Questions

1. What is database normalization?
2. Explain insertion, update, and deletion anomalies.
3. What is the difference between 1NF, 2NF, and 3NF?
4. What is a partial dependency?
5. What is a transitive dependency?
6. What is BCNF, and how does it differ from 3NF?
7. What is denormalization?
8. Why might a data warehouse use denormalized tables?
9. Does normalization always improve query performance?
10. How do primary keys and foreign keys help maintain data integrity?

## 12. Practice Tasks

1. Identify the anomalies in a table that stores student, course, and instructor details together.
2. Convert a table with comma-separated skills into a relational design.
3. Normalize an employee table containing department details.
4. Explain why a composite key can create partial dependencies.
5. Design normalized customer, order, and order-item tables.
6. Describe a reporting scenario in which denormalization might be useful.

## Key Takeaways

- Normalization organizes data to reduce redundancy and anomalies.
- 1NF requires atomic values and no repeating groups.
- 2NF removes partial dependencies on composite candidate keys.
- 3NF removes problematic transitive dependencies of non-key attributes.
- BCNF requires every determinant in a non-trivial functional dependency to be a superkey.
- Denormalization can improve read performance but introduces trade-offs.
