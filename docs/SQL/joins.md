---
title: Joins
---

## Cartesian Join
also known as *Cartesian Product* or *Cross Join*

Replicates each row from the first table with every row from the second
table. Creates a join between tables by displaying every possible record combination.  
Can be created by two methods:

- Not including a joining condition in a WHERE clause
- Using the JOIN method with the CROSS JOIN keywords

![Cartesian Join](https://i.imgur.com/K7TnIL9.png)

!!! example "Examples"
    ```sql linenums="1"
    SELECT *
    FROM oe.orders, oe.customers;
    ```

??? abstract "Sources"
    - [Oracle Documentation - Cartesian Product](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Joins.html#GUID-70DD48FA-BF46-4479-9C3F-146C5616E440)

---

## Inner Join
also known as *Equijoin*, *Equality Join* or *Simple Join*

Creates a join by using a commonly named and defined column.  
Can be created by two methods:

- Using the WHERE clause
- Using the JOIN method with the `NATURAL JOIN`, `JOIN … ON`, or `JOIN … USING` keywords

**Syntax**
```sql
SELECT column_list
FROM table_1 t1 JOIN table_2 t2 USING (column_1 [, column_2, ... column_n]);
```

!!! info "Notes"
    - The USING clause specifies which columns to test for equality when two tables are joined.

!!! example "Examples"
    ```sql linenums="1"
    SELECT *
    FROM oe.orders o JOIN oe.order_items oi
    ON o.order_id = oi.order_id;
    ```

??? abstract "Sources"
    - [Oracle Documentation - Equijoins](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Joins.html#GUID-3AA5EB23-2D84-4E19-BD7E-E66A3C59D888)

---

## Non-equality Join
Joins tables when there are no equivalent rows in the tables to be joined;  
For example, to match values in one column of a table with a range of values in another table.  
Can be created by two methods:

- Using the WHERE clause
- Using the JOIN method with the `JOIN … ON` keywords

---

## Self-Join
Joins a table to itself.  
Can be created by two methods:

- Using the WHERE clause
- Using the JOIN method with the `JOIN … ON` keywords

??? abstract "Sources"
    - [Oracle Documentation - Self Joins](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Joins.html#GUID-B0F5C614-CBDD-45F6-966D-00BAD6463440)

---

## Outer Join
Includes records of a table in output when there’s no matching record in the other table.  
Can be created by two methods:

- Using the WHERE clause with a (þ) operator
- Using the JOIN method with the OUTER JOIN keywords and the assigned type of LEFT, RIGHT, or FULL

??? abstract "Sources"
    - [Oracle Documentation - Outer Joins](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Joins.html#GUID-29A4584C-0741-4E6A-A89B-DCFAA222994A)