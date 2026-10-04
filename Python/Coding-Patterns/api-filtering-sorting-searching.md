
# API Filtering, Sorting, and Searching

## 1. Overview

Filtering, sorting, and searching help clients retrieve the records they need from an API.

Consider an API that returns thousands of products. A client might want to:

- Filter products by category.
- Sort products by price.
- Search product names.
- Combine multiple conditions.

These operations are usually performed on the server rather than downloading every record to the client.

## 2. Filtering

Filtering returns records that satisfy specified conditions.

Example request:

```http
GET /api/products?category=electronics
```

Filter by category and availability:

```http
GET /api/products?category=electronics&available=true
```

### Common filtering operators

| Operator | Meaning | Example |
|---|---|---|
| `eq` | Equal to | `price[eq]=100` |
| `gt` | Greater than | `price[gt]=100` |
| `gte` | Greater than or equal to | `price[gte]=100` |
| `lt` | Less than | `price[lt]=500` |
| `lte` | Less than or equal to | `price[lte]=500` |
| `in` | Matches one of several values | `category[in]=books,electronics` |

The exact query syntax depends on the API's design.

Example:

```http
GET /api/products?price[gte]=100&price[lte]=500
```

This requests products priced between 100 and 500, inclusive.

## 3. Sorting

Sorting determines the order of returned records.

Ascending order:

```http
GET /api/products?sort=price
```

Descending order:

```http
GET /api/products?sort=-price
```

Sort by multiple fields:

```http
GET /api/products?sort=category,-price
```

In this example, products are ordered by category first and then by price in descending order within each category.

Always document the supported fields and the default sort order.

## 4. Searching

Searching finds records matching a query.

Example:

```http
GET /api/products?search=laptop
```

A search might match product names, descriptions, or other selected fields.

Filtering and searching are different:

- Filtering applies structured conditions, such as category or price.
- Searching matches a text query or other search expression.

For large datasets, consider database indexes or a dedicated search engine where appropriate.

## 5. Simple Flask Implementation

Install Flask:

```bash
pip install flask
```

Create `app.py`:

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

products = [
    {
        "id": 1,
        "name": "Laptop",
        "category": "electronics",
        "price": 700
    },
    {
        "id": 2,
        "name": "Keyboard",
        "category": "electronics",
        "price": 50
    },
    {
        "id": 3,
        "name": "Python Book",
        "category": "books",
        "price": 30
    },
    {
        "id": 4,
        "name": "Monitor",
        "category": "electronics",
        "price": 200
    }
]


@app.get("/api/products")
def get_products():
    result = products.copy()

    # Filtering
    category = request.args.get("category")

    if category:
        result = [
            product for product in result
            if product["category"].lower() == category.lower()
        ]

    # Text searching
    search = request.args.get("search", "").strip().lower()

    if search:
        result = [
            product for product in result
            if search in product["name"].lower()
        ]

    # Minimum and maximum price filters
    min_price = request.args.get("min_price")
    max_price = request.args.get("max_price")

    try:
        if min_price is not None:
            minimum = float(min_price)
            result = [
                product for product in result
                if product["price"] >= minimum
            ]

        if max_price is not None:
            maximum = float(max_price)
            result = [
                product for product in result
                if product["price"] <= maximum
            ]

    except ValueError:
        return jsonify({
            "error": "Prices must be valid numbers"
        }), 400

    # Sorting
    sort_by = request.args.get("sort", "name")
    order = request.args.get("order", "asc").lower()

    allowed_fields = {"id", "name", "category", "price"}

    if sort_by not in allowed_fields:
        return jsonify({
            "error": "Unsupported sort field"
        }), 400

    if order not in {"asc", "desc"}:
        return jsonify({
            "error": "Order must be asc or desc"
        }), 400

    result.sort(
        key=lambda product: product[sort_by],
        reverse=(order == "desc")
    )

    return jsonify({
        "count": len(result),
        "products": result
    })


if __name__ == "__main__":
    app.run(debug=True)
```

**Note:** This is a small in-memory learning example. A production API should usually filter, search, and sort in the database, validate inputs carefully, and avoid exposing unsupported fields.

## 6. Test the API

Run the application:

```bash
python app.py
```

Try these URLs in your browser or an API client:

Filter by category:

```text
http://127.0.0.1:5000/api/products?category=electronics
```

Search product names:

```text
http://127.0.0.1:5000/api/products?search=lap
```

Filter by price:

```text
http://127.0.0.1:5000/api/products?min_price=100&max_price=500
```

Sort by descending price:

```text
http://127.0.0.1:5000/api/products?sort=price&order=desc
```

Combine filtering, searching, and sorting:

```text
http://127.0.0.1:5000/api/products?category=electronics&min_price=100&sort=price&order=desc
```

## 7. Security and Performance

- Allowlist permitted filter and sort fields.
- Validate numeric values, dates, and enumerated options.
- Use parameterized database queries or a safe ORM.
- Never concatenate untrusted input directly into SQL.
- Limit expensive search operations.
- Use appropriate database indexes.
- Define stable default sorting.
- Combine these features with pagination for large datasets.
- Apply authorization before returning protected records.
- Set reasonable limits on the number and complexity of filters.

## 8. Interview Questions

1. What is API filtering?
2. How is filtering different from searching?
3. How do you implement ascending and descending sorting?
4. Why should sort fields be allowlisted?
5. How would you combine filtering and pagination?
6. What is the role of database indexes?
7. Why are parameterized SQL queries important?
8. How would you validate invalid query parameters?
9. How would you support filtering by a date range?
10. How would you design an API for searching millions of records?

## 9. Practice Tasks

- [ ] Add a filter for product category.
- [ ] Add minimum and maximum price filters.
- [ ] Implement searching by product name.
- [ ] Support ascending and descending sorting.
- [ ] Reject invalid sort fields and invalid prices.
- [ ] Add automated tests for combined query parameters.
- [ ] Refactor the example to use a database query instead of an in-memory list.

## Key Takeaway

Well-designed filtering, sorting, and searching make APIs more useful and efficient. Validate query parameters, restrict supported fields, and let the database handle large datasets whenever possible.
