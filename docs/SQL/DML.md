---
title: DML Statements
---

Data Manipulation Language (DDL) statements access and manipulate data in existing schema objects.

DML Statements do not implicitly commit the current transaction.

The DML Statements are:
- `CALL`
- `DELETE`
- `EXPLAIN PLAN`
- `INSERT`
- `LOCK TABLE`
- `MERGE`
- `SELECT`
- `UPDATE`

**Sources**
- [Oracle Documentation - Data Manipulation Language (DML) Statements](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/Types-of-SQL-Statements.html#GUID-2E008D4A-F6FD-4F34-9071-7E10419CA24D)

---

## INSERT
INSERT allows to add rows to a Table, the base table of a View or a partition of a partitioned Table.

There are two types of INSERTs, Conventional and Direct-Path:
- **Conventional**: Oracle Database reuses free space in the Table into which you are inserting and maintains integrity constraints.
- **Direct-Path**: Oracle Database appends the inserted data after existing data in the Table. Data is written directly into data files.
	- attempting to execute multiple Direct-Path INSERTs in one Transaction returns an error
	- Direct-Path INSERTs cannot be executed on Tables that have Triggers or Referential Integrity Constraints
	
**Syntax**
```sql
INSERT [hint]
INTO {[schema.]table_name | subquery}
[PARTITION (partition_name)]
	[column_1, column_n]
{VALUES (column_1_value, column_n_value) | subquery};
```
- *hint*: specify a comment that passes instructions to the optimizer on choosing an execution plan for the statement.
- *schema*: specify the schema containing the Table, View or Materialized View.
- *table_name* | *subquery*: specify the name of the Object or the Subquery into which rows are to be inserted.
- PARTITION (*partition_name*): specify the name or partition key value of the partition or subpartition within *table_name*.
- *column_1, column_n*: optional column list.
- VALUES (*column_1_value, column_n_value*) | *subquery*}: values you're inserting into the column list. Instead of specifying the values manually, you can also use a Subquery to insert rows into a table.

**Notes about INSERT**
- Using the `APPEND` hint activates Direct-Path INSERT
	- see more details about [Hints](SQL/hints)
- If the column list is omitted, then the VALUES clause must specify values for all columns in the Table.
- If the specified *partition_name* does not exist, the database returns an error.
- If a View was created using the `WITH CHECK OPTION` clause, then you can insert into the View only rows that satisfy the defining query of the View.
- You cannot specify `DEFAULT` in the VALUES clause when inserting into a View.
- *column_1_value* can be a Subquery, but must return only 1 row.

**Oracle Live SQL**
- [INSERT Statement](https://livesql.oracle.com/apex/livesql/s/b6d9mx1ujyjenzyvihp4qhsfw)
- [Inserting Data through a Subquery](https://livesql.oracle.com/apex/livesql/s/b6d95r3aqqtb09ivlkl1zsdjg)

**Sources**
- [Oracle Documentation - INSERT](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/INSERT.html)

---

## INSERT ALL
**Unconditional INSERT ALL**\
When using an unconditional INSERT ALL statement, each row produced by the driving query results in a new row in each of the tables listed in the INTO clauses.

**Unconditional INSERT ALL Syntax**
```sql
INSERT ALL
INTO table_1 (column_a, column_b) VALUES (column_1, column_2)
	INTO table_2 (column_c, column_d) VALUES (column_1, column_2)
	INTO table_3 (column_e, column_f) VALUES (column_1, column_2)
SELECT column_1[, column_n]
FROM source_table;
```

**Conditional INSERT ALL**\
In a conditional INSERT ALL statement, conditions can be added to the INTO clauses, which means the total number of rows inserted may be less that the number of source rows multiplied by the number of INTO clauses.

**Conditional INSERT ALL Syntax**
```sql
INSERT ALL
	WHEN condition THEN
		INTO table_1 (column_a, column_b) VALUES (column_1, column_2)
	WHEN condition THEN
		INTO table_2 (column_c, column_d) VALUES (column_1, column_2)
	WHEN condition THEN
		INTO table_3 (column_e, column_f) VALUES (column_1, column_2)
SELECT column_1, column_2
FROM source_table;
```

**Restrictions**
- Multitable inserts can only be performed on Tables, not on views or Materialized Views.
- Sequences cannot be used in the multitable insert statement. It is considered a single statement, so only one sequence value will be generated and used for all rows.

**Oracle Live SQL**
- [Unconditional INSERT ALL](https://livesql.oracle.com/apex/livesql/s/o9ninb4mh0btszce7to6vvo2q)
- [Conditional INSERT ALL](https://livesql.oracle.com/apex/livesql/s/o9nn25s98zpbyva2s8qe5fbqc)

**Sources**
- [Oracle Documentation - INSERT: multi_table_insert](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/INSERT.html#GUID-903F8043-0254-4EE9-ACC1-CB8AC0AF3423)

---

## MERGE
The MERGE Statement selects data from one or more source tables and updates or inserts it into a target table.

The MERGE statement allows you to specify a condition to determine whether to update data from or insert data into the target table.

**Syntax**
```sql
MERGE INTO target_table
USING source_table
ON (search_condition)
	WHEN MATCHED THEN
		UPDATE SET column_1 = value_1, column_2 = value_2, ...
		WHERE [condition]
		[DELETE WHERE condition]
	WHEN NOT MATCHED THEN
		INSERT (column_1, column_2, ...)
		VALUES (value_1, value_2, ...)
		WHERE [condition];
```
- *target_table*: Table on which you want to update or insert into.
- *source_table*: Source of data to be updated or inserted.
- *search_condition*: Search condition upon which the merge operation either updates or inserts.

For each row in the target table, Oracle evaluates the search condition:
- If the result is true, then Oracle updates the row with the corresponding data from the source table.
- In case the result is false for any rows, then Oracle inserts the corresponding row from the source table into the target table.

**Example**
```sql
MERGE INTO author a
USING customers c
ON (a.authorid = TO_CHAR(c.customer#))
    WHEN MATCHED THEN
        UPDATE SET a.lname = c.lastname, a.fname = c.firstname
    WHEN NOT MATCHED THEN
        INSERT (a.authorid, a.lname, a.fname)
        VALUES (c.customer#, c.lastname, c.firstname);
```
For each row in Author:
- If authorid and customer# are equal, then update the Author's lastname (a.lname) and first name (a.fname) with the Customer's last name (c.lastname) and first name (c.firstname).
- Otherwise inserts a new row in Author with Customer's data.

**Sources**
- [Oracle Documentation - MERGE](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/MERGE.html)