---
title: Subqueries
---

## Correlated Subqueries
A Correlated Subquery is a subquery whose some clauses refer to the column expressions in the outer query.

**Notes about Correlated Subqueries**
- The Correlated Subquery executes **after** the outer query returns the row.
- Each row returned by the outer query is evaluated for the results returned by the Correlated Subquery.

**Example**
```sql
SELECT product_id, product_name, list_price
FROM products p
WHERE list_price >
(SELECT AVG(list_price)
FROM products
WHERE category_id = p.category_id);
```

---

## Single-Row Subqueries
A Single-Row Subquery returns zero or one rows to the outer SQL Statement.\
A Single-Row Subquery can be placed in a WHERE clause, HAVING clause, or a FROM clause of a SELECT Statement.

**Oracle Live SQL**
- [Single-Row Subqueries](https://livesql.oracle.com/apex/livesql/s/pb4jwoij2ybxb0yw6aubabdm0)

---

## Multiple-Row Subqueries
A Multiple-Row Subquery returns more than one row to the outer SQL Statement.\
Despite it's called "Multiple-Row Subquery", it can still return 0 or 1 row(s).\
You can use operators such as IN, ANY, or ALL.

**Oracle Live SQL**
- [Multiple-Row Subqueries](https://livesql.oracle.com/apex/livesql/s/pb4lqu6aifvzjlft3ffaz2u7u)
