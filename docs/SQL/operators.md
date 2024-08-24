---
title: Comparison Operators
---

## SET COMPARISON OPERATORS
- < ANY ( ): Less than maximum
- \> ANY ( ): Greater than minimum
	- Example: `x > ANY (1, 2, 3)` → `x > 1 OR x > 2 OR x > 3 → x > 1`
- = ANY ( ): Equivalent to IN
	- Example: `x = ANY (1, 2, 3)` → `x IN (1, 2, 3) → x = 1 OR x = 2 OR x = 3`
- \> ALL ( ): Greater than maximum
	- Example: `x > ALL (1,2,3)` → `x > 1 AND x > 2 AND x > 3 → x > 3`
- < ALL ( ): Less than mininum

---

## ANY Operator
The ANY Operator is used to compare a value to a list of values or result set returned by a subquery.

**Syntax**
```sql
operator comparison_op ANY ({expr_1, expr_n | subquery})
```

- *comparison_op*: the comparison operator (=, !=, >, >=, etc.)
- *expr_1, expr_n*: values you're comparing to *operator*.
- *subquery*: subquery returning one or more rows. 

!!! info "Notes"
    - If *subquery* returns no rows, the main query will return no rows as well.

!!! example "Examples"
    ```sql linenums="1"
    SELECT *
    FROM tablename
    WHERE c > ANY (v1, v2, v3);
    ```
    
    -- would be equivalent to:
    
    SELECT *
    FROM tablename
    WHERE c > v1
        OR c > v2
        OR c > v2;
    ```

---

## INTERSECT Operator
The INTERSECT operator compares the result of two queries and returns the distinct rows that are output by both queries.

INTERSECT does not ignore NULL values.

![intersect](https://i.imgur.com/gp8xApQ.png)

**Syntax**
```sql
SELECT column_list_1
FROM tablename_1
INTERSECT
SELECT column_list_2
FROM tablename_2;
```

!!! warning "Restrictions"
    - The number and order of columns must be the same in the two queries.
    - The data type of the corresponding columns must be in the same data type group such as numeric or character.

??? abstract "Sources"
    - [Oracle Documentation - The Set Operators](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/The-UNION-ALL-INTERSECT-MINUS-Operators.html)

---

## MINUS Operator
The MINUS operator is used to return all rows in the first SELECT statement that are not returned by the second SELECT statement.

![minus](https://i.imgur.com/pToKBoe.png)

**Syntax**
```sql
SELECT column_list_1
FROM tablename_1
MINUS
SELECT column_list_2
FROM tablename_2;
```

!!! warning "Restrictions"
    - The number and order of columns must be the same in the two queries.
    - The data type of the corresponding columns must be in the same data type group such as numeric or character.

??? abstract "Sources"
    - [Oracle Documentation - The Set Operators](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/The-UNION-ALL-INTERSECT-MINUS-Operators.html)

---

## UNION & UNION ALL Operators

The UNION & UNION ALL operators are set operators that combines result sets of two or more SELECT statements into a single result set.

![union](https://i.imgur.com/iPgeR08.png)

**Syntax**
```sql
SELECT column_list_1
FROM tablename_1
UNION [ALL]
SELECT column_list_2
FROM tablename_2;
```

!!! info "Notes"
    - UNION ALL returns all records including duplicates, while UNION returns only unique records.

??? abstract "Sources"
    - [Oracle Documentation - The Set Operators](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/The-UNION-ALL-INTERSECT-MINUS-Operators.html)