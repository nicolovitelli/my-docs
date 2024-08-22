---
title: Basic Concepts
---

## SELECT Statement
```sql
SELECT      [DISTINCT | UNIQUE] (, columnname [AS alias], …)
FROM        tablename
[WHERE      condition]
[GROUP BY   group_by_expression]
[HAVING     group_condition]
[ORDER BY   columnname];
```

**Sources**
- [Oracle Documentation - SELECT](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/SELECT.html)

---

## Column Aliases
Column Aliases can be used to create a temporary name for columns or expressions.

**Syntax**
```sql
column_name [AS] alias_name
```

**Examples**
- `first_name AS Employee`
- `first_name Employee`
- `first_name || ' ' || last_name AS "Employee Details"`
- `first_name "Employee's First Name"`

**Restrictions**
- If *alias_name* contains spaces, you must enclose *alias_name* in quotes.

**Sources**
- [Oracle Documentation - SELECT](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/SELECT.html)

---

## DISTINCT
The DISTINCT clause is used to remove duplicates from the result set.
This clause can only be used within SELECT Statements.

**Syntax**
```sql
SELECT DISTINCT column_name
FROM table_name;
```

**Example**
```sql
SELECT DISTINCT first_name
FROM employees;
```

**Notes about DISTINCT**
- When only one expression is provided in the DISTINCT clause, the query will return the unique values for that expression.
- When more than one expression is provided in the DISTINCT clause, the query will retrieve unique combinations for the expressions listed.
- DISTINCT does **not** ignore NULL values. If there are multiple NULL values, it will return only one NULL.
- UNIQUE is a synonym of DISTINCT.
- You cannot specify DISTINCT if the SELECT list contains LOB columns.

**Sources**
- [Oracle Documentation - SELECT](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/SELECT.html)

---

## Query Processing Order / Order of Execution
```sql
FROM
WHERE
GROUP BY
SELECT
HAVING
ORDER BY
```

**Sources**
- [Oracle SQL and PL/SQL Optimization for Developers - Query Processing Order](https://oracle.readthedocs.io/en/latest/sql/basics/query-processing-order.html)

---

## Alternative Quote Operator (*q*)
**Syntax**
```sql
SELECT  q'{text}', column_1, ..., column_n
FROM    tablename;
```

**Oracle Live SQL**
- [Alternative Quote Operator](https://livesql.oracle.com/apex/livesql/s/pdt89lso0zy6ooir8lidd8j1l)

---

## Operator and Condition Precedence

- \*, /\*
- +, -
- =, !=, <, >, <=, >=
- IS [NOT] NULL, LIKE, [NOT] BETWEEN, [NOT] IN, EXISTS, IS OF
- NOT
- AND
- OR

**Sources**
- [Oracle Documentation - Condition Precedence](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/About-SQL-Conditions.html)
- [Oracle Documentation - Operator Precedence](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/About-SQL-Operators.html)

---

## DESCRIBE
**Syntax**
```sql
DESC[RIBE] [schema.]object;
```
- *object* can be:
	- a table
	- a view
	- a synonym
	- a PL/SQL type
	- a PL/SQL procedure
	- a PL/SQL function
	- a PL/SQL package

**Command Output**\
The description for *object* contains the following information:
- each column's name
- whether or not null values are allowed (NULL or NOT NULL) for each column
- datatype of columns and their precision (and scale, if any, for a numeric column)

**Oracle Live SQL**
- [DESC](https://livesql.oracle.com/apex/livesql/s/pdt89lso3vvk4893eazxegs4p)

**Sources**
- [Oracle Documentation - DESCRIBE](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqpug/DESCRIBE.html)

---

## DEFAULT Clause
This clause lets you specify a value to be assigned to the column if a subsequent INSERT statement omits a value for the column.

**Syntax**
```sql
CREATE TABLE [schema.]table_name (
	column_1 datatype DEFAULT value
);
```
- DEFAULT *value*: *value* must be the same datatype as the column definition (or must be implicitly convertible). 

**Syntax - Apply a DEFAULT value**
```sql
INSERT INTO table_name [column_list]
VALUES ([column_values,] DEFAULT)
```

**Notes about DEFAULT**
- *value* cannot be a subquery.
- *value* can be a Sequence's CURRVAL or NEXTVAL.
	- if the Sequence is dropped, the Database will return an error for all the subsequent INSERTs.
- *value* cannot be an user-defined PL/SQL Function.

**Oracle Live SQL**
- [DEFAULT Clause](https://livesql.oracle.com/apex/livesql/s/b6fdxz904263hde4c8rphu73f)
- [DEFAULT Clause - Using a Sequence](https://livesql.oracle.com/apex/livesql/s/b6fl7rg2dw1cxkdk75e7yqcbe)

**Sources**
- [Oracle Documentation - DEFAULT](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/CREATE-TABLE.html)