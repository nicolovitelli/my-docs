---
title: Hints
---

# NOCOPY
It's a Hint used to improve performance of OUT and IN OUT parameters in PL/SQL.
There are two methods to pass OUT/IN OUT parameters:

- **By Value**: happens by default: a copy of the variable is created and any modification is done on the copy. Once the Procedure is completed, the data is passed on the original variable.
- **By Reference**: happens with the NOCOPY hint: instead of creating a copy of the variable, modifications are done directly to the original variable.
In normal Procedures you wouldn't notice the difference between the two methods, but the difference can become considerable when working with large or complex datatypes.

**Syntax**
```sql
CREATE [OR REPLACE] PROCEDURE [schema.]procedure_name (parameter_name {OUT | IN OUT} NOCOPY datatype) IS
[declaration section]
BEGIN
	executable_section
[EXCEPTION
	exception_section]
END;
```

!!! example "Examples"
	```sql linenums="1"
	CREATE OR REPLACE PROCEDURE in_out_nocopy (v_number IN OUT NOCOPY NUMBER) IS
	BEGIN
		v_number := v_number * 2;
		dmbs_output.put_line(v_number);
	END;
	```

# PARALLEL ENABLE
This hint enables the Function on which it is declared for parallel execution.

**Syntax**
```sql
CREATE [OR REPLACE] FUNCTION function_name (parameters) RETURN datatype
	PARALLEL_ENABLE
IS
[declaration section]
BEGIN
	executable_section
[EXCEPTION
	exception_section]
END;
```

# DETERMINISTIC
The DETERMINISTIC Clause marks a function to return predictable results.
It is useful for optimizazion purposes: if the function is called multiple times with the same parameters, Oracle can avoid redundant calculations by reusing the previously computed result.

**Syntax**
```sql
CREATE [OR REPLACE] FUNCTION function_name (parameters) RETURN datatype
	DETERMINISTIC
IS
[declaration section]
BEGIN
	executable_section
[EXCEPTION
	exception_section]
END;
```

!!! example "Examples"
    ```sql linenums="1"
    CREATE OR REPLACE FUNCTION square(n NUMBER)
    	RETURN NUMBER
    	DETERMINISTIC
    IS
    BEGIN
    	RETURN n * n;
    END;
    ```

??? abstract "Sources"
    - [Oracle Documentation - PARALLEL ENABLE Clause](https://docs.oracle.com/en/database/oracle/oracle-database/23/lnpls/PARALLEL_ENABLE-clause.html)
    - [Oracle Documentation - DETERMINISTIC Clause](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/DETERMINISTIC-clause.html)