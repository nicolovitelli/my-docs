---
title: Comparison Operators
---

## SET COMPARISON OPERATORS
- < ANY ( ): Less than maximum
- \> ANY ( ): Greater than minimum
	- Example: x > ANY (1, 2, 3) → x > 1 OR x > 2 OR x > 3 → x > 1
- = ANY ( ): Equivalent to IN
	- Example: x = ANY (1, 2, 3) → x IN (1, 2, 3) → x = 1 OR x = 2 OR x = 3
- \> ALL ( ): Greater than maximum
	- Example: x > ALL (1,2,3) → x > 1 AND x > 2 AND x > 3 → x > 3
- < ALL ( ): Less than mininum

---

## ANY Operator
The ANY Operator is used to compare a value to a list of values or result set returned by a subquery.

The ANY Operator must be preceded by a comparison operator (=, !=, >, >=, etc.)

**Syntax**
```sql
operator ANY (v1, v2, v3)
```
or
```sql
operator ANY (subquery)
```

**Example**
```sql
SELECT *
FROM tablename
WHERE c > ANY (v1, v2, v3);
```

would be equivalent to:

```sql
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

**Syntax**
```sql
SELECT column_list_1
FROM tablename_1
INTERSECT
SELECT column_list_2
FROM tablename_2;
```

**Rules**
- The number and order of columns must be the same in the two queries.
- The data type of the corresponding columns must be in the same data type group such as numeric or character.
![intersect](https://i.imgur.com/gp8xApQ.png)

**Sources**
- [Oracle Documentation - The Set Operators](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/The-UNION-ALL-INTERSECT-MINUS-Operators.html)

---

## MINUS Operator
The MINUS operator is used to return all rows in the first SELECT statement that are not returned by the second SELECT statement.

**Syntax**
```sql
SELECT column_list_1
FROM tablename_1
MINUS
SELECT column_list_2
FROM tablename_2;
```

**Rules**
- The number and order of columns must be the same in the two queries.
- The data type of the corresponding columns must be in the same data type group such as numeric or character.
![minus](https://i.imgur.com/pToKBoe.png)

**Sources**
- [Oracle Documentation - The Set Operators](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/The-UNION-ALL-INTERSECT-MINUS-Operators.html)

---

## UNION & UNION ALL Operators

The UNION & UNION ALL operators are set operators that combines result sets of two or more SELECT statements into a single result set.

**Syntax**
```sql
SELECT column_list_1
FROM tablename_1
UNION [ALL]
SELECT column_list_2
FROM tablename_2;
```

**Differences between UNION and UNION ALL**
- UNION ALL returns all records including duplicates, while UNION returns only unique records.
![union](https://i.imgur.com/iPgeR08.png)

**Sources**
- [Oracle Documentation - The Set Operators](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/The-UNION-ALL-INTERSECT-MINUS-Operators.html)