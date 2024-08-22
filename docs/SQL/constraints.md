---
title: Constraints
---

## UNIQUE Constraint
A UNIQUE constraint prohibits multiple rows from having the same value in the same column or combination of columns but allows some values to be null.

**Composite Unique Key**\
More than one column can be part of a UNIQUE Constraint: that means the combination of the specified columns will be unique.\
A Composite Unique Key can only be specified out of line.

**Syntax**
```sql
-- inline syntax
CREATE TABLE table_name(
	column_name datatype [CONSTRAINT constraint_name] UNIQUE
);

-- out of line syntax
CREATE TABLE table_name(
	column_name datatype,
	[column_name_n datatype,]
	[CONSTRAINT constraint_name] UNIQUE (column_name)
);
```

**Syntax - Composite Unique Key**
```sql
CREATE TABLE table_name(
	column_name_1 datatype,
	[column_name_n datatype,]
	[CONSTRAINT constraint_name] UNIQUE (column_name_1, column_name_2)
);
```

**Notes about UNIQUE**
- When defining a UNIQUE Constraint on one or more columns, Oracle automatically creates an index on the column(s).
- To improve queries performance, it is recommended to create an UNIQUE INDEX instead of a UNIQUE Constraint.
- Despite the UNIQUE constraint allowing multiple NULL values, if only one column of a Composite Unique Key has a NULL value, then the other column must have a different value. See example below:
```sql
CREATE TABLE t (t1 NUMBER, t2 NUMBER, UNIQUE (t1,t2));
INSERT INTO t VALUES (1, NULL);
-- 1 row inserted.
INSERT INTO t VALUES (1, NULL);
-- ORA-00001: unique constraint (SYS_C0046702) violated
```

**Restrictions**
- UNIQUE Constraints cannot be created on the following column datatypes:
	- `LOB`
	- `LONG`
	- `LONG RAW`
	- `VARRAY`
	- `NESTED TABLE`
	- `OBJECT`
	- `REF`
	- `TIMESTAMP WITH TIME ZONE`
	- user-defined type
- A Primary Key column cannot be part of a UNIQUE Constraint.

**Sources**
- [Oracle Documentation - Unique Constraints](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/constraint.html)

---

## NOT NULL Constraint
A NOT NULL constraint prohibits a column from containing nulls.

**Syntax**
```sql
CREATE TABLE table_name(
	column_name_1 datatype [NULL | NOT NULL]
);
```

**Notes about NOT NULL**
- A NOT NULL Constraint must be defined inline. 
- It is possible to specify NULL to explicitly permit a column to contain NULLs.

**Restrictions**
- You cannot specify NOT NULL in a View Constraint. 

**Sources**
- [Oracle Documentation - NOT NULL Constraints](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/constraint.html)

---

## PRIMARY KEY Constraint
A Primary Key constraint designates a column as the Primary Key of a table or view.

A Primary Key constraint cannot:
- contain duplicated values
- contain NULL values

**Syntax**
```sql
CREATE TABLE table_name(
	column_name datatype [CONSTRAINT constraint_name] PRIMARY KEY
);
```

**Syntax - out of line**
```sql
CREATE TABLE table_name(
	column_name datatype,
	[column_name_n datatype]
	[CONSTRAINT constraint_name] PRIMARY KEY (column_name)
);
```

**Notes about PRIMARY KEY**
- Multiple columns can be part of a Primary Key (*Composite Primary Key*).
- If no Index is defined on the PRIMARY KEY columns, then Oracle automatically creates an Unique Index.
	- If an Unique Index is already defined on the PRIMARY KEY columns, then no new index will be created and when the PK Constraint is dropped, the Index will stay on the columns.
	- Otherwise, the related Index will be dropped as well.

**Restrictions**
- A Table or View can have only one Primary Key.
- A Primary Key Constraint cannot be defined on the following column datatypes:
	- `LOB`
	- `LONG`
	- `LONG RAW`
	- `VARRAY`
	- `NESTED TABLE`
	- `BFILE`
	- `REF`
	- `TIMESTAMP WITH TIME ZONE`
	- user-defined type
- A Primary Key column cannot be part of a UNIQUE Constraint.

**Sources**
- [Oracle Documentation - Primary Key Constraints](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/constraint.html)

---

## FOREIGN KEY Constraints
A Foreign Key Constraint (*also called a Referential Integrity Constraint*) designates a column as the Foreign Key and establishes a relationship between that Foreign Key and a specified Primary or Unique key, called the Referenced Key.

The table or view containing the Foreign Key is called the **child** object, and the table or view containing the referenced key is called the **parent** object.

**Syntax**
```sql
CREATE TABLE parent_table_name(
	parent_column_name datatype {PRIMARY KEY | UNIQUE}
);

-- out of line declaration
CREATE TABLE child_table_name(
	child_column_name datatype,
	[column_name_n datatype]
	[CONSTRAINT constraint_name] FOREIGN KEY (child_column_name) REFERENCES parent_table_name (parent_column_name)
);

-- inline declaration
CREATE TABLE child_table_name(
	child_column_name datatype [CONSTRAINT constraint_name] REFERENCES parent_table_name (parent_column_name)
);
```

**Notes about FOREIGN KEY**
- The Foreign Key and the Referenced Key can be in the same table or view.
- Multiple columns can be part of a Foreign Key (*Composite Foreign Key*).
- A single column can be part of more than one Foreign Key.
- A Table can have multiple Foreign Keys.
- A Primary Key can also be a Foreign Key.

**ON DELETE Clause**
The ON DELETE clause lets you determine how Oracle Database automatically maintains referential integrity if you remove a Referenced Primary or Unique Key value:
- `CASCADE`: remove dependent Foreign Key values (*will remove the entire row*)
- `SET NULL`: convert dependent Foreign Key values to NULL
	- This cannot be specified for a Virtual Column

**Restrictions**
- A Foreign Key Constraint cannot be defined on the following column datatypes:
	- `LOB`
	- `LONG`
	- `LONG RAW`
	- `VARRAY`
	- `NESTED TABLE`
	- `BFILE`
	- `REF`
	- `TIMESTAMP WITH TIME ZONE`
	- user-defined type
- The Referenced Unique or Primary Key constraint on the parent table or view must already be defined.
- You cannot define a Foreign Key constraint in a CREATE TABLE statement that contains an `AS subquery` clause.
	- The constraint can be added later with an ALTER TABLE.
- The ON DELETE Clause cannot be specified for a View Constraint.