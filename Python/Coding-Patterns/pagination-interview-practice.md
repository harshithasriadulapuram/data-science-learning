# Pagination Interview Practice in Python

## 1. What Is Pagination?

Pagination divides a large collection of results into smaller, manageable pages.

For example, instead of returning 10,000 database records in one response, an API might return 20 records per page.

Pagination helps:
- Reduce response size.
- Improve perceived response time.
- Limit memory usage.
- Make large result sets easier to navigate.

## 2. Offset-Based Pagination

Offset pagination uses a limit and an offset.

- limit: maximum number of records to return.
- offset: number of records to skip.

### Example

```python
def paginate(items, page, page_size):
    if page < 1:
        raise ValueError("Page must be at least 1")

    if page_size < 1:
        raise ValueError("Page size must be positive")

    start = (page - 1) * page_size
    end = start + page_size

    return items[start:end]


numbers = list(range(1, 26))

print(paginate(numbers, page=1, page_size=10))
# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

print(paginate(numbers, page=2, page_size=10))
# [11, 12, 13, 14, 15, 16, 17, 18, 19, 20]
```

In a database query, offset pagination often corresponds to LIMIT and OFFSET.

```sql
SELECT id, name
FROM products
ORDER BY id
LIMIT 20 OFFSET 40;
```

This retrieves up to 20 rows after skipping the first 40 ordered rows.

## 3. Calculate the Number of Pages

```python
def total_pages(total_items, page_size):
    if total_items < 0:
        raise ValueError("Total items cannot be negative")

    if page_size <= 0:
        raise ValueError("Page size must be positive")

    return (total_items + page_size - 1) // page_size


print(total_pages(101, 20))  # 6
print(total_pages(100, 20))  # 5
print(total_pages(0, 20))    # 0
```

Integer arithmetic avoids the need to convert the result of a division into a floating-point number.

## 4. Cursor-Based Pagination

Cursor pagination uses a value marking where to continue, such as the last returned record's ID.

Suppose records are ordered by increasing ID.

```sql
SELECT id, name
FROM products
WHERE id > 100
ORDER BY id
LIMIT 20;
```

This retrieves the next batch after ID 100.

A real API may encode the cursor in an opaque token instead of exposing a database ID directly.

### Python Example

```python
def next_page(items, last_id=None, page_size=3):
    if page_size <= 0:
        raise ValueError("Page size must be positive")

    filtered = [
        item for item in items
        if last_id is None or item["id"] > last_id
    ]

    filtered.sort(key=lambda item: item["id"])
    return filtered[:page_size]


items = [
    {"id": 1, "name": "A"},
    {"id": 2, "name": "B"},
    {"id": 3, "name": "C"},
    {"id": 4, "name": "D"},
    {"id": 5, "name": "E"}
]

first = next_page(items, page_size=3)
print(first)

second = next_page(items, last_id=3, page_size=3)
print(second)
```

This example filters and sorts an in-memory list for clarity. A database index and an appropriate query are more efficient for large datasets.

## 5. Offset vs Cursor Pagination

| Feature | Offset-based | Cursor-based |
|---|---|---|
| Navigation | Easy to jump to a page number | Usually moves forward or backward from a cursor |
| Deep pagination | Can become expensive | Often more efficient with a suitable index |
| Data changes | Inserts and deletes can shift page boundaries | Usually more stable with a deterministic cursor |
| Implementation | Simple | Requires cursor management |
| Best fit | Small or moderately sized lists | Large datasets and continuously changing feeds |

Cursor pagination is not automatically consistent under every data change. The ordering and cursor design matter.

## 6. Stable Ordering Matters

Always define a deterministic order.

For example, ordering only by a timestamp may be insufficient if many records share the same timestamp.

Use a unique tie-breaker:

```sql
SELECT id, created_at, name
FROM products
ORDER BY created_at, id
LIMIT 20;
```

For cursor pagination, the cursor must represent the complete ordering position, such as the pair (created_at, id).

## 7. Common API Response Format

A paginated response might look like this:

```json
{
  "items": [
    {"id": 1, "name": "Product A"},
    {"id": 2, "name": "Product B"}
  ],
  "page": 1,
  "page_size": 20,
  "total_items": 100,
  "total_pages": 5
}
```

For cursor pagination, the response might instead include:

```json
{
  "items": [],
  "next_cursor": "opaque-token",
  "has_more": true
}
```

These are illustrative response shapes, not a required standard.

## 8. Performance Considerations

- Enforce a maximum page size.
- Use indexed columns for ordering and filtering.
- Avoid expensive total-count queries when the count is not needed.
- Prefer cursor pagination for deep traversal of large datasets.
- Decide how concurrent inserts and deletes should affect traversal.
- Validate page numbers, limits, and cursor tokens.

## 9. Interview Questions

1. What is pagination and why is it useful?
2. How do limit and offset work?
3. What are the limitations of offset pagination?
4. What is cursor-based pagination?
5. Why should ordering be deterministic?
6. How would you paginate millions of database records?
7. Why might a total-count query be expensive?
8. How should an API validate page size?
9. How can concurrent inserts affect pagination?
10. When would you choose offset pagination over cursor pagination?

## 10. Practice Tasks

- [ ] Paginate a Python list.
- [ ] Calculate total pages.
- [ ] Write a LIMIT/OFFSET query.
- [ ] Implement cursor pagination with a unique ID.
- [ ] Handle invalid page sizes.
- [ ] Explain stable ordering and cursor design.
- [ ] Design a paginated REST API response.
