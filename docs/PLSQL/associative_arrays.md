---
title: Associative Arrays
---

An Associative Array is a set of key-value pairs:

- Each key is unique and it is used to find the associate value.
- The index's datatype can be a String (VARCHAR2, CHAR, etc) or PLS_INTEGER.
- Indexes are stored in sorted order, not creation order.
- For String Indexes, their sort is determined by the parameters NLS_SORT and NLS_COMP.
- You can use any value that can be converted to char through the TO_CHAR function as index datatype.
- It is not recommended to use `#!sql TO_CHAR(SYSDATE)` as index datatype because if the value of NLS_DATE_FORMAT changes, then the Associative Array's indexes change too.

An Associative Array...

- is empty (but not null) until you populate it
- can hold unspecified number of elements
- does not need disk space or network operations
- cannot be manipulated with DML statements
- can be defined only in PL/SQL Blocks

**Syntax**
```sql
TYPE type_name IS TABLE OF element_type [NOT NULL] INDEX BY key_type;
```

- *type_name*: name of the collection.
- *element_type*: datatype of the elements stored.
- `#!sql NOT NULL`: every element in the array must have a value.
- *key_type*: datatype of the index.

You must create a variable of the Associative Array datatype to insert or search for values:

```sql
TYPE type_name IS TABLE OF element_type [NOT NULL] INDEX BY key_type;
variable_name type_name;
```

!!! example "Examples"
    ```sql linenums="1"
    DECLARE
       TYPE emp_table_type IS TABLE OF VARCHAR2(100) INDEX BY PLS_INTEGER;
       emp_table emp_table_type;
    BEGIN
       emp_table(1) := 'John Doe';
       emp_table(2) := 'Jane Smith';
       emp_table(3) := 'Robert Brown';
       
       DBMS_OUTPUT.PUT_LINE(emp_table(1));
    END;
    /
        -- OUTPUT: John Doe
    ```

??? abstract "Sources"
    - [Oracle Documentation - Associative Arrays](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/plsql-collections-and-records.html#GUID-8060F01F-B53B-48D4-9239-7EA8461C2170)