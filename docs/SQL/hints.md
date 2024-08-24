---
title: Hints
---

Hints are comments in a SQL statement that pass instructions to the Oracle Database optimizer.  
Hints can only be used in SELECT, UPDATE, INSERT, MERGE or DELETE Statements.

**Syntax**
```sql
DML_keyword /*+ hint_1 [hint_n] */
DML_statement
```

- *DML_keyword*: must be one of the following: SELECT, UPDATE, INSERT, MERGE, DELETE.
- *hint_1, \[hint_n]*: name of the hint being used. Multiple Hints can be used in one statement.

??? abstract "Sources"
    - [Oracle Documentation - Hints](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Comments.html#GUID-D316D545-89E2-4D54-977F-FC97815CD62E)

---

## PARALLEL Hint
The PARALLEL hint instructs the optimizer to use the specified number of concurrent servers for a parallel operation.

**Syntax**
```sql
DML_keyword /*+ PARALLEL[(degree | DEFAULT | MANUAL)]
DML_statement
```

- *DML_keyword*: must be one of the following: SELECT, UPDATE, INSERT, MERGE, DELETE
- *degree*: degree of parallelism used for the statement.
- `#!sql DEFAULT`: the optimizer calculates a degree of parallelism equal to the number of CPUs availables times the value of the `#!sql PARALLEL_THREADS_PER_CPU` parameter.
- `#!sql MANUAL`: the optimize uses the degree of parallelism specified in the Object.

!!! info "Notes"
    - Query to get the number of CPUs availables and the `#!sql PARALLEL_THREADS_PER_CPU` parameter's value:
    ```sql linenums="1"
    SELECT name, value
    FROM v$parameter
    WHERE UPPER(name) IN ('CPU_COUNT','PARALLEL_THREADS_PER_CPU')
    ```

??? abstract "Sources"
    - [Oracle Documentation - PARALLEL Hint](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Comments.html#GUID-D25225CE-2DCE-4D9F-8E82-401839690A6E)

---

## APPEND Hint
The APPEND hint instructs the optimizer to use direct-path INSERT with the subquery syntax of the INSERT statement.

**Syntax**
```sql
INSERT /*+ APPEND */
INTO table_name
subquery;
```

- *table_name*: table on which you're inserting data.
- *subquery*: SELECT Statement from which you're getting data.

??? abstract "Sources"
    - [Oracle Documentation - APPEND Hint](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Comments.html#GUID-562DD503-2F99-448E-B044-737BE726B58A)