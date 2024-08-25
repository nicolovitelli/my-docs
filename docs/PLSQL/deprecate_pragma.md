---
title: DEPRECATE Pragma
---

This pragma marks as Deprecated PL/SQL objects. The compiler shows warning if the program uses deprecated objects.  
The DEPRECATE pragma can only appear in the Declaration Section and can be applied to:

- Subprograms (Procedure/Function)
- Packages
- Variables
- Constants
- Types
- Subtypes
- Exceptions
- Cursors

**Syntax**
```sql
PRAGMA DEPRECATE (pls_identifier [,character_literal]);
```

- *pls_identifer*: name of the object that is being deprecated.
- *character_literal*: optional warning message.

!!! example "Examples"
    ```sql linenums="1"
    -- deprecation of Package
    create package pack1 as  
	    pragma deprecate (pack1);
	    procedure my_procedure;
    end;
    -- warnings will be issued for any reference to this package and its procedures.
    
    -- deprecation of Variable
    create package pack2 as  
	    my_variable number := 1;
	    pragma deprecate(my_variable, 'unused variable');
    end;
    ```

??? abstract "Sources"
    - [Oracle Documentation - DEPRECATE Pragma](https://docs.oracle.com/en/database/oracle/oracle-database/23/lnpls/DEPRECATE-pragma.html)