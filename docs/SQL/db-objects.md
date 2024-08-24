---
title: Other Database Objects
---

## CREATE INDEX
Indexes are optional structures associated with Tables that allow SQL queries to execute more quickly against a Table.

**Syntax**
```sql
CREATE [UNIQUE] INDEX index_name
	ON table_name (column_1, column_2, ... column_n)
	[COMPUTE STATISTICS];
```

- `UNIQUE`: Indicates that the combination of values in the indexed columns must be unique.
- *index_name*: the name to assign to the index.
- *table_name*: the name of the table in which to create the index.
- `COMPUTE STATISTICS`: tells Oracle to collect stats during the creation of the index. They are then used by the optimizer to choose a "plan of execution" when SQL Statements are executed.

!!! info "Notes"
    - Indexes are logically and physically independent of the data in the associated table. Being independent structures, they require storage space.
    - You can create or drop an index without affecting the base tables, database applications, or other indexes.
    - The database automatically maintains indexes when you insert, update, and delete rows of the associated table.
    - If you drop an index, all applications continue to work. However, access to previously indexed data might be slower.
    - To create an index in your own schema, you must have the CREATE ANY INDEX system privilege.
    - LONG columns cannot be indexed.
    - By default, Oracle creates B-Tree Indexes.

??? abstract "Sources"
    - [Oracle Documentation - CREATE INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/CREATE-INDEX.html)

---

## ALTER INDEX
```sql
ALTER INDEX index_name
	RENAME TO new_index_name;
```

??? abstract "Sources"
    - [Oracle Documentation - ALTER INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/ALTER-INDEX.html)

---

## DROP INDEX
```sql
DROP INDEX index_name;
```

??? abstract "Sources"
    - [Oracle Documentation - DROP INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/DROP-INDEX.html)

---

## Synonyms
A Synonym is an alternative name for objects such as Tables, Views, Sequences, etc.  
You can perform tasks such as creating Synonyms, using Synonyms, and dropping Synonyms.

Synonyms allow underlying obejcts to be renamed or moved, where only the Synonym must be redefined and applications based on the synonym continue to function without modification.

!!! warning "Privilege Restrictions"
    - (Private Synonym) in your schema: `CREATE SYNONYM` System Privilege
    - (Private Synonym) in another's user schema: `CREATE ANY SYNONYM` System Privilege

!!! question "Data Dictionary"
    - `*_SYNONYMS`

??? abstract "Sources"
    - [Oracle Documentation - Managing Synonyms](https://docs.oracle.com/en/database/oracle/oracle-database/21/admin/managing-views-sequences-and-synonyms.html)

---

## CREATE SYNONYM
```sql
CREATE [OR REPLACE] [PUBLIC] SYNONYM [schema.]synonym_name
	FOR [schema.]object_name;
```

- `OR REPLACE`: allows you to recreate the Synonym if it already exists.
- `PUBLIC`: if specified, the Synonym is a Public Synonym and accessible to all users, otherwise it'll be a Private Synonym.
- *schema*: schema on which the Synonym will be created.
- *object_name*: the name of the object for which you are creating the Synonym. 
	- can be one of the following:
		- Table
		- View
		- Sequence
		- Stored Procedure
		- Function
		- Package
		- Materialized View
		- User-defined Object
		- Synonym

??? abstract "Sources"
    - [Oracle Documentation - CREATE SYNONYM](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/CREATE-SYNONYM.html#GUID-A806C82F-1171-478E-A910-F9C6C42739B2)

---

## DROP SYNONYM
```sql
DROP [PUBLIC] SYNONYM synonym_name;
```

??? abstract "Sources"
    - [Oracle Documentation - DROP SYNONYM](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/DROP-SYNONYM.html)

---

## Views
A View is a logical representation of a Table or combination of Tables. In essence, a View is a stored query.

!!! info "Notes"
    - A View derives its data from the Tables on which it is based. These Tables are called base Tables.
    - All operations performed on a View actually affect the base Table of the View.
    - You can use Views in almost the same way as Tables. You can query, update, insert into, and delete from Views.

!!! warning "Restrictions on DML Operations"  
    - You need the appropriate privigiles on the base Tables to run DML Statements.
    - DML Operations are not allowed on Views with SET or DISTINCT operators, GROUP BY clause, Group Functions or Subqueries.
    - If a View is defined with WITH CHECK OPTION, DML Operations are not allowed if the View cannot select the row from the base Table.
    - If a NOT NULL column that does not have a DEFAULT clause is omitted from the View, then a row cannot be inserted into the base Table using the View.
    - INSERT and UPDATE Operations are not allowed on Views with expressions such as DECODE.
    - Any DML Operation on a Join View can modify only one underlying base Table at a time.

**Key-Preserved Tables**  
A Table is key-preserved if every key of the Table can also be a key of the result of the Join that is based on the Table.

??? abstract "Sources"
    - [Oracle Documentation - Managing Views](https://docs.oracle.com/en/database/oracle/oracle-database/21/admin/managing-views-sequences-and-synonyms.html)

---

## CREATE VIEW
**Syntax**
```sql
CREATE [OR REPLACE] VIEW view_name AS
query
[WITH CHECK OPTION];
```

- `WITH CHECK OPTION`: can be given for an updatable View to prevent inserts to rows for which the WHERE clause in the SELECT Statement is not true.

!!! warning "Privilege Restrictions"
    - In your schema: `CREATE VIEW` System Privilege
    - In another's user schema: `CREATE ANY VIEW` System Privilege

??? abstract "Sources"
    - [Oracle Documentation - CREATE VIEW](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/CREATE-VIEW.html)

---

## WITH CHECK OPTION
Usually you can insert rows in a Table through a View even if the rows you're adding violate the View's WHERE condition. (*Example 1*)  
To prevent that and make it so the rows you try to insert are valid for the View as well, you can add the WITH CHECK OPTION. (*Example 2*)

**Example 1**
```sql linenums="1"
CREATE TABLE t (t1 number);

CREATE OR REPLACE VIEW vw AS SELECT * FROM t WHERE t1 > 0;

INSERT INTO vw VALUES (0);

SELECT * FROM t;
-- returns 1 rows

SELECT count(1) FROM vw;
-- returns 0
```

**Example 2**
```sql linenums="1"
CREATE OR REPLACE VIEW vw AS SELECT * FROM t WHERE t1 > 0 WITH CHECK OPTION;

INSERT INTO vw VALUES (0);
-- returns ORA-01402: view WITH CHECK OPTION where-clause violation
```

??? abstract "Sources"
    - [Oracle Documentation - CREATE VIEW](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/CREATE-VIEW.html)

---

## Sequences
A Sequence is a database object from which multiple users may generate unique integers.  
It can be used to automatically generate primary key values.

When a Sequence number is generated, the Sequence is incremented, independent of the transaction committing or rolling back.

If two users concurrently increment the same Sequence, then the Sequence numbers each user acquires may have gaps, because Sequence numbers are being generated by the other user.

**Syntax**
```sql
CREATE SEQUENCE [schema.]sequence_name
[INCREMENT BY | START WITH integer]
[NOMAXVALUE | MAXVALUE integer]
[NOMINVALUE | MINVALUE integer]
[CYCLE | NOCYCLE]
[NOCACHE | CACHE integer]
[ORDER | NOORDER]
;
```

- *schema*: specify the scema to contain the Sequence.
- *sequence_name*: specify the name of the Sequence to be created.
- `#!sql INCREMENT BY`: specify the interval between Sequence numbers.	
- `#!sql START WITH`: specify the first Sequence number to be generated.
- `#!sql MAXVALUE integer`: specify the maximum value the Sequence can generate.
- `#!sql NOMAXVALUE integer`: indicates a minimum value of 1 for an ascending Sequence or -(10^27 -1) for descending Sequence.
- `#!sql CYCLE`: indicates that the Sequence continues to generate values after reaching either its maximum or minimum value.
- `#!sql NOCYCLE`: indicates that the Sequence cannot generate more value after reaching its maximum or minimum value.
- `#!sql CACHE`: specify how many values of the Sequence the Database preallocates and keeps in memory for faster access.
- `#!sql NOCACHE`: indicates that values of the Sequence are not preallocated.
- `#!sql ORDER`: guarantees that Sequence numbers are generated in order of request.
- `#!sql NOORDER`: does not guarantee Sequence numbers to be generated in order of request.

**Sequence's Default Options**

- `#!sql INCREMENT BY 1`
- `#!sql START WITH ?`
- `#!sql NOMAXVALUE`
- `#!sql NOCYCLE`
- `#!sql CACHE 20`
- `#!sql NOORDER`

!!! info "Notes"
    - There is **no ROLLBACK** in the Sequence values.
    - `#!sql INCREMENT BY` cannot be 0.
    - `#!sql MAXVALUE` must be:
	- Equal to or greater than `#!sql START WITH`. 
	- Greater than `#!sql MINVALUE`
    - After an ascending Sequence reaches its maximum value, it generates its minimum value.
    - After a descending Sequence reaches its minimum, it generates its maximum value.
    - Mininum value of `#!sql CACHE` is 2.
    - CACHE value must be less than the value determined by this formula: `#!sql CEIL((MAXVALUE-MINVALUE)/ABS(INCREMENT))`
    - If a system failure occurs, then all cached Sequence values that have not been used in committed DML statements are lost.
    - Guaranteeing order is usually not important for Sequences used to generate Primary Keys.

!!! warning "Privilege Restrictions"
    - In your schema: `CREATE SEQUENCE` System Privilege
    - In another's user schema: `CREATE ANY SEQUENCE` System Privilege

!!! question "Data Dictionary"
    - `*_SEQUENCES`

??? abstract "Sources"
    - [Oracle Documentation - Managing Sequences](https://docs.oracle.com/en/database/oracle/oracle-database/21/admin/managing-views-sequences-and-synonyms.html)