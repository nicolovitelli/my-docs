---
title: ACCESSIBLE BY Clause
---

The ACCESSIBLE BY clause restricts access to a unit or subprogram by other units.
This clause can be used by the following statements:

- `#!sql ALTER TYPE`
- `#!sql CREATE FUNCTION`
- `#!sql CREATE PROCEDURE`
- `#!sql CREATE PACKAGE`
- `#!sql CREATE TYPE`
- `#!sql CREATE TYPE BODY`

**Syntax**
```sql
ACCESSIBLE BY (
	[FUNCTION | PROCEDURE | PACKAGE | TRIGGER | TYPE]
	[schema.]unit_name
)
```

- *unit_name*: specifies a stored PL/SQL unit that can invoke the entity.
	- it's not required for the *unit_name* to exist.
- type of object (Function, Procedure, Package, Trigger, Type) is optional but is recommended to avoid ambiguity (*it's possible to have a Trigger with the same name as a Function*).

!!! example "Examples"
    ```sql linenums="1"
    CREATE OR REPLACE PROCEDURE private_procedure
    	ACCESSIBLE BY (PROCEDURE trusted_procedure)
    IS
    BEGIN 
    	dbms_output.put_line('executed private_procedure');
    END;
    /
    -- Procedure PRIVATE_PROCEDURE compiled
    
    CREATE OR REPLACE PROCEDURE trusted_procedure
    IS 
    BEGIN 
    	dbms_output.put_line('executed trusted_procedure');
    	private_procedure;	
    	dbms_output.put_line('called private_procedure');
    END;
    /
    -- Procedure TRUSTED_PROCEDURE compiled
    
    EXEC trusted_procedure;
    -- executed trusted_procedure
    -- executed private_procedure
    -- called private_procedure
    
    EXEC private_procedure;
    -- PLS-00904: insufficient privilege to access object PRIVATE_PROCEDURE
    
    BEGIN
        private_procedure;
    END;
    -- PLS-00904: insufficient privilege to access object PRIVATE_PROCEDURE
    ```

??? abstract "Sources"
    - [Oracle Documentation - ACCESSIBLE BY Clause](https://docs.oracle.com/en/database/oracle/oracle-database/23/lnpls/ACCESSIBLE-BY-clause.html)