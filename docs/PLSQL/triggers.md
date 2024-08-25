---
title: Triggers
---

A Trigger is like a stored procedure that Oracle Database invokes automatically whenever a specified event occurs.  
The main difference with Constraints is that Triggers apply to new data only.

**Types of Triggers**

- **DML Trigger**: a Trigger created on a Table or View, which is composed by DML Statements.
  - **INSTEAD OF Trigger**: a Trigger that replaces the original triggering DML Statement.
- **System Trigger**: a Trigger created on a Schema or Database, which is composed by DDL or Database Operation Statements.
- **Conditional Trigger**: a DML or System Trigger that has a WHEN clause.

??? abstract "Sources"
    - [Oracle Documentation - PL/SQL Triggers](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/plsql-triggers.html)

## DML Trigger
A DML Trigger fires at exactly one of these timing points:

- before the triggering statement runs (*BEFORE Statement Trigger*)
- after the triggering statement runs (*AFTER Statement Trigger*)
- before each row that the triggering statement affects (*BEFORE each row Trigger*)
- after each row that the triggering statemet affects (*AFTER each row Trigger*)

**DML Trigger Syntax**
```sql
CREATE [OR REPLACE] TRIGGER [schema.]trigger_name
  {BEFORE | AFTER} {INSERT | UPDATE [OF column [, column]] | DELETE [OR INSERT | UPDATE [OF column [, column]] | DELETE]}
  ON [schema.]object_name 
  [FOR EACH ROW]
  [FOLLOWS | PRECEDES [schema.]trigger_name]
  [ENABLE | DISABLE]
  [WHEN (condition)]
[DECLARE section]
BEGIN
  ...
[EXCEPTION section]
END;
```

- `#!sql OR REPLACE`: re-creates the Trigger if it exists.
- *schema*: schema on which the Trigger will be created (default is current schema).
- *trigger_name*: name of the Trigger.
- `#!sql BEFORE`: Trigger is fired before running the triggering event.
- `#!sql AFTER`: Trigger is fired after running the triggering event.
- `#!sql INSERT`: Trigger is fired whenever an INSERT Statement adds a row to *object_name*.
- `#!sql DELETE`: Trigger is fired whenever a DELETE Statement removes a row from *object_name*.
- `#!sql UPDATE [OF column [, column]]`: Trigger is fired whenever an UPDATE Statement changes a value in a specified column.
  - if no column is specified, then the Trigger is fired whenever any column changes.
  - if you specify a column, then that column's value cannot be changed in the Trigger Body.
- *object_name*: Table or View on which the Trigger is defined.
- `#!sql FOR EACH ROW`: creates the Trigger as a Row Trigger. The Trigger is fired for each row that is affected.
- `#!sql FOLLOWS | PRECEDES schema.trigger_name`: indicates if the Trigger should be fired after (FOLLOWS) or before (PRECEDES) another Trigger.
- `#!sql ENABLE | DISABLE`: creates the Trigger in an Enabled/Disabled state. Default is ENABLE.
- `#!sql WHEN (condition)`: specifies a SQL condition that the Database evaluates for each row that the triggering statement affects. Trigger Body will run only when *condition* is true.

!!! info "Notes"
    - If a Trigger produces compilation errors, then it is still created, but it fails on execution.
        - Query to check if compiled Trigger has errors: `#!sql SELECT * FROM all_errors WHERE type = 'TRIGGER';` 
    - If a Trigger has a `#!sql WHEN (condition)` clause, then you must also specify `FOR EACH ROW`.
    - `#!sql WHEN (condition)` cannot include a Subquery or PL/SQL Expression (*i.e. invokation of Functions, Procedures*).
    - `#!sql WHEN (condition)` does not need semicolons (*:*) for OLD and NEW variables.

!!! example "Examples"
    - [Oracle Live SQL - DML Trigger - Example 1](https://livesql.oracle.com/apex/livesql/s/b5i6t7ln4g7ogj6ojzj6e9iv7)
    - [Oracle Live SQL - DML Trigger - Example 2](https://livesql.oracle.com/apex/livesql/s/b5i9m8id0gl61p17rda3ml8ts)

??? abstract "Sources"
    - [Oracle Documentation - DML Triggers](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/plsql-triggers.html#GUID-E76C8044-6942-4573-B7DB-3502FB96CF6F)

---

## Conditional Predicates
The Trigger can determine which event is happening thanks to Conditional Predicates.  
They can be used in Boolean Expressions (IF, CASE, etc.):

- `#!sql INSERTING`: an INSERT Statement
- `#!sql UPDATING`: an UPDATE Statement
- `#!sql UPDATING ('column')`: an UPDATE Statement that affected the specified column
- `#!sql DELETING`: a DELETE Statement\

!!! example "Examples"
    ```sql linenums="1"
    IF INSERTING THEN
      [PL/SQL Statements]
    END IF;
    
    CASE
      WHEN INSERTING THEN
        [PL/SQL Statements]
      WHEN UPDATING THEN
        [PL/SQL Statements]
      WHEN DELETING THEN
        [PL/SQL Statements]
    END CASE;
    ```

---

## Correlation Names
(*also know as Pseudorecords*)  
Some Trigger Bodies can also use Correlation Names, which by default are:

- `#!sql :NEW.column_name`: represents the new value
- `#!sql :OLD.column_name`: represents the old value that is being replaced

| Triggering Statement | OLD.column_name Value | NEW.column_name Value |
|----------------------|-----------------------|-----------------------|
| `#!sql INSERT`       | NULL                  | Post-insert value     |
| `#!sql UPDATE`       | Pre-update value      | Post-update value     |
| `#!sql DELETE`       | Pre-delete value      | NULL                  |

!!! info "Notes"
    - A Trigger cannot change the OLD values.
        - This operation raises the `#!sql ORA-04085` error.
    - A Trigger cannot change NEW values if the Triggering Statement is DELETE.
        - This operation raises the `#!sql ORA-04084` error.
    - An AFTER Trigger cannot change NEW values, because the Triggering Statement runs before the Trigger fires.
        - This operation raises `#!sql ORA-04084` error.
    - If a Statement triggers both a BEFORE and an AFTER Trigger, and the BEFORE changes a NEW value, then the AFTER Trigger can see that change.

---

## INSTEAD OF Trigger
An INSTEAD OF Trigger is a DML Trigger created on a noneditioning View.  
The Database fires the INSTEAD OF Trigger instead of running the triggering DML Statement.

INSTEAD OF Triggers are always row-level Triggers. They can also use the OLD and NEW variables, but cannot change them.

**Syntax**
```sql
CREATE [OR REPLACE] TRIGGER [schema.]trigger_name
  INSTEAD OF {INSERT | UPDATE | DELETE [OR INSERT | UPDATE | DELETE]}
  ON view_name
  FOR EACH ROW
  [FOLLOWS | PRECEDES [schema.]trigger_name]
  [ENABLE | DISABLE]
[DECLARE section]
BEGIN
  ...
[EXCEPTION section]
END;
```

- `#!sql OR REPLACE`: re-creates the Trigger if it exists.
- *schema*: schema on which the Trigger will be created (default is current schema).
- *trigger_name*: name of the Trigger.
- `#!sql DELETE`: the Trigger is fired whenever a row is deleted from the Table on which the View is defined.
- `#!sql INSERT`: the Trigger is fired whenever a new row is inserted into the Table on which the View is defined.
- `#!sql UPDATE`: the Trigger is fired whenever a row is updated on the Table on which the View is defined.
- `#!sql ON view_name`: View on which the Trigger is defined. 
- `#!sql FOR EACH ROW`: mandatory for INSTEAD OF Triggers. The Trigger is fired for each row that is affected.
- `#!sql FOLLOWS | PRECEDES schema.trigger_name`: indicates if the Trigger should be fired after (FOLLOWS) or before (PRECEDES) another Trigger.
- `#!sql ENABLE | DISABLE`: creates the Trigger in an Enabled/Disables state. Default is ENABLE.

??? abstract "Sources"
    - [Oracle Documentation - INSTEAD OF DML Triggers](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/plsql-triggers.html#GUID-E76C8044-6942-4573-B7DB-3502FB96CF6F)

---

## System Triggers
A System Trigger is created on either a schema or the database.  
Its triggering event is composed of either DDL Statements or Database Operation Statements.

A System Trigger fires at exactly one of these timing points:

- Before the Triggering Statement runs (*BEFORE Trigger*)
- After the Triggering Statement runs (*AFTER Trigger*)
- Instead of the triggering CREATE Statement (*INSTEAD OF CREATE Trigger*)

**DDL Statements**  
These are the DDL Statements that are part of System Triggers:

- `#!sql ALTER`
- `#!sql ANALYZE`
- `#!sql ASSOCIATE STATISTICS`
- `#!sql AUDIT`
- `#!sql COMMENT`
- `#!sql CREATE`
- `#!sql DISASSOCIATE STATISTICS`
- `#!sql DROP`
- `#!sql GRANT`
- `#!sql NOAUDIT`
- `#!sql RENAME`
- `#!sql REVOKE`
- `#!sql TRUNCATE`
- `#!sql DDL`
    - causes the Trigger to fire whenever any of the preceding DDL Statements is issued
  
**Database Operation Statements**  
Thesere are the Database Operation Statements that are part of System Triggers:

- `#!sql AFTER STARTUP`
- `#!sql BEFORE SHUTDOWN`
- `#!sql AFTER DB_ROLE_CHANGE`
- `#!sql AFTER SERVERERROR`
- `#!sql AFTER LOGON`
- `#!sql BEFORE LOGOFF`
- `#!sql AFTER SUSPEND`
- `#!sql AFTER CLONE`
- `#!sql BEFORE UNPLUG`
- `#!sql [BEFORE | AFTER] SET CONTAINER`

??? abstract "Sources"
    - [Oracle Documentation - System Triggers](https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/plsql-triggers.html#GUID-FE23FCE8-DE36-41EF-80A9-6B4B49E80E5B)
    - [Oracle Documentation - List of DDL Statements](https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/CREATE-TRIGGER-statement.html#GUID-AF9E33F1-64D1-4382-A6A4-EC33C36F237B__CIHBDEFD)
    - [Oracle Documentation - List of Database Events](https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/CREATE-TRIGGER-statement.html#GUID-AF9E33F1-64D1-4382-A6A4-EC33C36F237B__CIHBABAH)

---

## DROP TRIGGER Statement
**Syntax**
```sql
DROP TRIGGER [schema.]trigger_name;
```

!!! warning "Privilege Restrictions"
    - `#!sql GRANT DROP ANY TRIGGER TO user_name;`

??? abstract "Sources"
    - [Oracle Documentation - DROP TRIGGER Statement](https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/DROP-TRIGGER-statement.html)
