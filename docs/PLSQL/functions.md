---
title: Functions
---

The CREATE FUNCTION statement creates or replaces a standalone Function or a call specification.

**Syntax**
```sql
CREATE [OR REPLACE] FUNCTION [schema.]function_name
	[(parameter [IN | OUT | IN OUT] parameter_datatype [, parameter])]
	RETURN return_datatype
IS | AS
	[declaration_section]
BEGIN
	executable_section
[EXCEPTION
	exception_section]
END [function_name];
```

- `#!sql OR REPLACE`: recreates the function if it exists
- *schema*: name of the schema containing the function (default: current schema)
- *function_name*: name of the function to be created
- *parameter*: list of parameters passed to the function
- `#!sql IN/OUT/IN OUT`:
	- `#!sql IN`: the parameter's value can be read by the function but cannot be changed.
	- `#!sql OUT`: the parameter's value cannot be read by the function but can be changed.
	- `#!sql IN OUT`: the parameter's can be read and changed by the function.
- `#!sql RETURN datatype`: the return datatype of the function. Cannot specify a length, precision or scale.

!!! info "Notes"
    - Every Function must return one or more values.
    - Parameters are always optional.

!!! example "Examples"
    ```sql linenums="1"
    CREATE OR REPLACE FUNCTION is_odd (in_number IN NUMBER)
    RETURN boolean
    IS
    BEGIN
        IF (in_number / 2) != 0 THEN
            RETURN TRUE;
        ELSE
            RETURN FALSE;
        END IF;
    END;
    /
    -- Function IS_ODD compiled
    
    BEGIN
        dbms_output.put_line(
            CASE WHEN IS_ODD(2)
                THEN '2 is an odd number'
                ELSE '2 is not an odd number'
            END
        );
    END;
    -- 2 is an odd number
    
    SELECT
        CASE WHEN IS_ODD(2)
                THEN '2 is an odd number'
                ELSE '2 is not an odd number'
            END
    FROM dual;
    -- ORA-00920: invalid relational operator
    ```

!!! warning "Privilege Restrictions"
    - In your Schema: `#!sql GRANT CREATE PROCEDURE TO user_name;`
    - In another's user Schema: `#!sql GRANT CREATE ANY PROCEDURE TO user_name;`

??? abstract "Sources"
    - [Oracle Documentation - CREATE FUNCTION Statement](https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/CREATE-FUNCTION-statement.html)