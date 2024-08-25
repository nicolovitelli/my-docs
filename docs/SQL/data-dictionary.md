---
title: Data Dictionary
---

The Data Dictionary is a read-only set of tables that provides information about the database.  
A data dictionary contains:

- The definitions of all schema objects in the database (tables, views, indexes, clusters, synonyms, sequences, procedures, functions, packages, triggers, and so on)
- How much space has been allocated for, and is currently used by, the schema objects
- Default values for columns
- Integrity constraint information
- The names of Oracle users
- Privileges and roles each user has been granted
- Auditing information, such as who has accessed or updated various schema objects
- Other general database information

The data dictionary is structured in tables and views, just like other database data.

---

## Prefixes of Data Dictionary Views
- `USER_` → Objects owned by the current user accessing the view.
- `ALL_` → Objects owned by any user in the Database to which the current user has privileges.
- `DBA_` → All objects in the Database.
- `V_$` (views) or `V$` (public synonyms) → Dynamic Pperformance Views, each of which has a public synonym counterpart. Stores information about the local database instance.
- `GV_$` (views) or `GV$` (public synonyms) → Global Dynamic Performance Views.

---

## Dynamic Performance Views

Dynamic Performance Views display information about current database activity in real time.  
They are maintaned automatically by the system and are available for querying with some limitations.

Dynamic Performance Views start with the prefix V\_$ or their public synonym counterpart starts with V$.

**List of Dynamic Performance Views**

- `V$DATABASE`: includes information about the Database itself, including the database name, the date created, etc.
- `V$INSTANCE`: includes the instance name, host name, etc.
- `V$PARAMETER`: current settings for system parameters such as NLS_LANGUAGE, NLS_DATE_LANGUAGE, NLS_CURRENCY, etc.
- `V$SESSION`: current settings for each individual user session, showing active connections, login times, etc.
- `V$RESERVED_WORDS`: current list of reserved words, including information indicating whether the keyword is always reserved, and if not, under what circumstances it is reserved.
- `V$OBJECT_USAGE`: useful for monitoring the usage of INDEX objects
- `V$TIMEZONE_NAMES`: includes two columns:
	- `TZNAME`: time zone region
	- `TZABBREV`: time zone abbreviation

??? abstract "Sources"
    - [Oracle Documentation - Overview of the Dynamic Performance Views](https://docs.oracle.com/en/database/oracle/oracle-database/21/cncpt/data-dictionary-and-dynamic-performance-views.html)

---

## Checking Privileges in the Data Dictionary
Privileges can be inspected using the following views:

- `USER_SYS_PRIVS`: System privileges granted to the current user
- `USER_TAB_PRIVS`: Granted privileges on objects for which the user is the owner, grantor or grantee.
- `USER_ROLE_PRIVS`: Roles granted to the current user.
- `DBA_SYS_PRIVS`: System privileges granted to users and roles.
- `DBA_TAB_PRIVS`: All grants on objects in the Database.
- `DBA_ROLE_PRIVS`: Role granted to users and roles.
- `ROLE_SYS_PRIVS`: System privileges granted to roles.
- `ROLE_TAB_PRIVS`: Table privileges granted to roles.
- `SESSION_PRIVS`: Session privileges that the user currenlty has set.

---

## USER_CATALOG
The USER_CATALOG view displays a summary listing of tables, views, synonym, and sequences owned by the user.  
There are two columns in USER_CATALOG:

- `TABLE_TYPE`: indicates the Database Object (SEQUENCE, TABLE, VIEW, etc.).
- `TABLE_NAME`: name of the Table.

??? abstract "Sources"
    - [Oracle Documentation - USER_CATALOG](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/USER_CATALOG.html)

---

## USER_OBJECTS
The USER_OBJECTS view contain information about all objects owned by the user.

??? abstract "Sources"
    - [Oracle Documentation - USER_OBJECTS](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/USER_OBJECTS.html)

---

## USER_SYNONYMS
The USER_SYNONYMS View describes the private synonyms (synonyms owned by the current user).  
Its columns (except for OWNER) are the same as those in ALL_SYNONYMS.

??? abstract "Sources"
    - [Oracle Documentation - USER_SYNONYMS](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/USER_SYNONYMS.html)

---

## USER_TABLES
The USER_TABLES view shows detailed information about the Tables owned by current session account.

Some of the columns include:

- `TABLE_NAME`: Name of the Table.
- `STATUS`: Indicates whether the Table is currently valid and therefore available for use.
- `ROW_MOVEMENT`: Indicates whether ROW MOVEMENT has been enabled for the Table.
- `AVG_ROW_LEN`: Average length of the rows currently stored in the Table.

??? abstract "Sources"
    - [Oracle Documentation - USER_TABLES](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/USER_TABLES.html)

---

## ALL_TABLES
The ALL_TABLES view shows detailed information about the Tables to which the current User has privileges, regardless of owner.

??? abstract "Sources"
    - [Oracle Documentation - ALL_TABLES](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/ALL_TABLES.html)

---

## DBA_TABLES
The DBA_TABLES view shows detailed information about the Tables in the entire Database, regardless of owner or Table privileges.

??? abstract "Sources"
    - [Oracle Documentation - DBA_TABLES](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DBA_TABLES.html)

---

## ALL_CONSTRAINTS
ALL_CONSTRAINTS describes constraint definitions on tables accessible to the current user.

**Columns**

- `OWNER`: owner of the constraint
- `CONSTRAINT_NAME`: constraint name
- `CONSTRAINT_TYPE`: type of constraint (full list below)
- `TABLE_NAME`: table on which the constraint is defined
- `SEARCH_CONDITION`: text of search condition for a CHECK constraint
- `R_OWNER`: owner of the parent constraint
- `R_CONSTRAINT_NAME`: name of parent constraint (only for Foreign Keys)
- `DELETE_RULE`: delete rule for a referential constraint (CASCADE, SET NULL, NO ACTION)
- `STATUS`: constraint status (ENABLED, DISABLED)
- `DEFERRABLE`: indicates if the constraint is DEFERRABLE or NOT DEFERRABLE
- `DEFERRED`: indicates if the constraint was initially DEFERRED or not (IMMEDIATE)
- `VALIDATED`:
	- if STATUS=ENABLED, possible values are:
		- VALIDATED: all data in the table obeys the constraint
		- NOT VALIDATED: alla data in the table may not obey the constraint
	- if STATUS=DISABLED, possible values are:
		- VALIDATED: all data obeys the constraint but the unique index on the constraint has been dropped.
		- NOT VALIDATED: all data may not obey the constraint
- `GENERATED`: indicates if the constraint name is user-generated (USER NAME) or system-generated (GENERATED NAME)
- `BAD`: indicates whether this constraint specifies a century in an ambiguous manner (BAD) or not (NULL).
	- to resolve the issue, rewrite the constraint using the TO_DATE function with a four-digit year
- `RELY`: [...]
- `LAST_CHANGE`: when the constraint was last enabled or disabled
- `INDEX_OWNER`: name of the user owning the index
- `INDEX_NAME`: name of the index (only for UNIQUE and Primary Key constraints)
- `INVALID`: indicates if the constraint is INVALID or not (NULL).
- `VIEW_RELATED`: indicated if the constraint depends on a view (DEPEND ON VIEW) or not
- `ORIGIN_CON_ID`: [...]

**Constraint Types**

- C → CHECK Constraint
- P → PRIMARY KEY Constraint
- U → UNIQUE Key
- R → Referential Integrity
- V → WITH CHECK OPTION, on a View
- O → WITH READ ONLY, on a View
- H → Hash Expression
- F → Constraint that involves a REF Column
- S → Supplemental Logging

??? abstract "Sources"
    - [Oracle Documentation - ALL_CONSTRAINTS](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_CONSTRAINTS.html)

---

## Get existing Constraints on a Table
```sql linenums="1"
SELECT 
	constraint_name
	, constraint_type
	, search_condition
	, r_constraint_name
	, delete_rule
	, status
FROM all_constraints
WHERE UPPER(table_name) = UPPER('tablename');
```

??? abstract "Sources"
    - [Oracle Documentation - ALL_CONSTRAINTS](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_CONSTRAINTS.html)

---

## Get Primary Key details about Table
```sql linenums="1"
SELECT
	cols.table_name
	, cols.column_name
	, cols.position
	, cons.status, cons.owner
FROM
	all_constraints cons
	INNER JOIN all_cons_columns cols
		ON cons.constraint_name = cols.constraint_name
		AND cons.owner = cols.owner
WHERE cons.constraint_type = 'P'
AND UPPER(cols.table_name) = UPPER('table_name')
ORDER BY cols.table_name, cols.position;
```

??? abstract "Sources"
    - [Oracle Documentation - ALL_CONSTRAINTS](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/ALL_CONSTRAINTS.html)
    - [Oracle Documentation - ALL_CONS_COLUMNS](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/ALL_CONS_COLUMNS.html)

---

## Get current Date format
```sql linenums="1"
SELECT *
FROM nls_session_parameters
WHERE parameter = 'NLS_DATE_FORMAT';
```

??? abstract "Sources"
    - [Oracle Documentation - NLS_SESSION_PARAMETERS](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/NLS_SESSION_PARAMETERS.html)

---

## Get List of updatable Columns on a View
```sql linenums="1"
SELECT 
	table_name
	, column_name
	, updatable
	, insertable
	, deletable
FROM USER_UPDATABLE_COLUMNS
WHERE table_name= 'view_name'; 
```

??? abstract "Sources"
    - [Oracle Documentation - USER_UPDATABLE_COLUMNS](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/USER_UPDATABLE_COLUMNS.html)

---

## NLS_LENGTH_SEMANTICS
**Syntax**
```sql
SELECT PARAMETER, VALUE 
FROM NLS_SESSION_PARAMETERS 
WHERE PARAMETER = 'NLS_LENGTH_SEMANTICS';
```

!!! example "Examples"
    ```sql linenums="1"
    
    SELECT PARAMETER, VALUE 
    FROM NLS_SESSION_PARAMETERS 
    WHERE PARAMETER = 'NLS_LENGTH_SEMANTICS';
    
    /*
    PARAMETER 			 | VALUE
    -------------------- | -----
    NLS_LENGTH_SEMANTICS | BYTE
    */
    ```

??? abstract "Sources"
    - [Oracle Documentation - NLS_LENGTH_SEMANTICS](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/NLS_LENGTH_SEMANTICS.html)

---

## Updating NLS_LENGTH_SEMANTICS
**Syntax**
```sql linenums="1"
-- for the current session
ALTER SESSION SET NLS_LENGTH_SEMANTICS = '{CHAR | BYTE}';

-- for the Database
ALTER SYSTEM SET NLS_LENGTH_SEMANTICS = '{CHAR | BYTE}' SCOPE = {BOTH | SPFILE};
```

- `#!sql CHAR | BYTE`: any new [Character column](/sql/datatypes/#character-dataypes) will use CHAR or BYTE by default.
- `#!sql BOTH`: makes the change for the running istance and in the parameter file (so it persists across database restarts)
- `#!sql SPFILE`: makes the change after the next database restart.

!!! info "Notes"
    - This change only affects new Tables and not existing ones.

!!! warning "Privilege Restrictions"
    - `#!sql ALTER SESSION`:
    	- no Privilege is required to change the value for the current session.
    - `#!sql ALTER SYSTEM`:
    	- `#!sql GRANT ALTER SYSTEM TO user_name;`

??? abstract "Sources"
    - [Oracle Documentation - ALTER SESSION](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-SESSION.html)
    - [Oracle Documentation - ALTER SYSTEM](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-SYSTEM.html)