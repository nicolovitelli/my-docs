---
title: Advanced Concepts
---

## Arithmetic Operations on Dates

**Rules**

- Operations between DATE value and Binary Operators
	- \+ Addition: adds days to a DATE value; DATE value must be at the left-side.
	- \- Subtraction: subtracts days to a DATE value; DATE value must be at the left-side.
	- \* Multiplication: NOT ALLOWED
	- / Division: NOT ALLOWED
- Operations between two DATE values
	- \+ Addition: NOT ALLOWED
	- \- Subtraction: return number of days between the two Dates
	- \* Multiplication: NOT ALLOWED
	- / Division: NOT ALLOWED

Supposing SYSDATE is **28-NOV-22**:

- `SYSDATE - 1` → 27-NOV-22
- `1 - SYSDATE` → `ORA-00932: inconsistent datatypes`
- `SYSDATE + 1` → 29-NOV-22
- `1 + SYSDATE` → 29-NOV-2022
- `SYSDATE * 1` → `ORA-00932: inconsistent datatypes`
- `1 * SYSDATE` → `ORA-00932: inconsistent datatypes`
- `SYSDATE / 1` → `ORA-00932: inconsistent datatypes`
- `1 / SYSDATE` → `ORA-00932: inconsistent datatypes`
- `SYSDATE - TO_DATE('27-NOV-2022')` → 1
	- Calculates number of days between two dates.

??? abstract "Sources"
    - [Oracle Documentation - Datetime/Interval Arithmetic](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Data-Types.html#GUID-E405BBC7-DA9A-4DF2-9F22-E60CB9EC0705)

---

## RRRR vs. YYYY
- **YYYY**: first two digits will always be current century (99 → 2099).
- **RRRR**: first two digits depend on range:
	- years in range 00 to 49 are assumed to be in current century (21 → 2021)
	- years in range 50 to 99 are assumed to be in the previous century (99 → 1999)

There's no difference when working with 4-digits years.  
"2022" will always be 2022 no matter if you're using YYYY or RRRR as format mask.

---

## NLS_DATE_FORMAT
`NLS_DATE_FORMAT` specifies the default date format to use with the `TO_CHAR` and `TO_DATE` functions.  
The default value of this parameter is determined by `NLS_TERRITORY`.

The value of this parameter can be any valid date format mask, and the value must be surrounded by double quotation marks.  
For example:
```sql
NLS_DATE_FORMAT = "MM/DD/YYYY"
```

**Current Date Mask**
```sql linenums="1"
SELECT VALUE
FROM NLS_SESSION_PARAMETERS
WHERE PARAMETER = 'NLS_DATE_FORMAT';
```

??? abstract "Sources"
    - [Oracle Documentation - NLS_DATE_FORMAT](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/NLS_DATE_FORMAT.html)

---

## Substitution Variables

**Syntax**
```sql
-- Definining a new Substitution Variables:
DEFINE variable_name = variable_value;

-- Confirming the variable definition:
DEFINE variable_name;

-- Listing all Substitution Variables:
DEFINE;

-- Deleting a Substitution Variables:
UNDEFINE variable_name;

-- Referencing to a Substitution Variables:
SELECT &variable_name_1
FROM &variable_name_2;
```

**Notes about Substitution Variables**

- You can use Substitution Variables anywhere in SQL and SQL\*Plus commands.
- When SQL\*Plus encounters an undefined Substitution Variables in a command, SQL\*PLUS prompts you for the value.


**SET Command**

- After you enter a value at the prompt, SQL\*Plus lists the line containing the Substitution Variable twice: once before substituing the value you enter and once after substitution.
- You can suppress this listing by setting the `SET` command variable `VERIFY` to `OFF`.

**Example**

![sv-ex1](https://i.imgur.com/7WfjnkR.png)

**Difference between "&" and "&&"**  
If a single ampersand prefix is used with an undefined variable, the value you enter at the prompt is not stored. Immediately after the value is substituted in the statement the variable is discarded and remains undefined.\
If the variable is referenced twice, even in the same statement, then you are prompted twice.

**Example**\
![sv-ex2](https://i.imgur.com/QX29uX0.png)

If a double ampersand reference causes SQL\*Plus to prompt you for a value, then SQL\*Plus defines the variable as that value (that is, the value is stored until you exit).  
Any subsequent reference to the variable (even in the same command) using either "&" or "&&" substitutes the newly defined value.  
SQL\*Plus will not prompt you again:

**Example**

![sv-ex3](https://i.imgur.com/SCYfLFH.png)

??? abstract "Sources"
    - [Oracle Documentation - Using Substitution Variables](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqpug/using-substitution-variables-sqlplus.html)
    - [Oracle Documentation - DEFINE](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqpug/DEFINE.html)
    - [Oracle Documentation - UNDEFINE](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqpug/UNDEFINE.html)
    - [Oracle Documentation - SET VERIFY](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqpug/SET-system-variable-summary.html#GUID-74CA1665-165D-4C0D-BBB2-681BD3485211)

---

## PIVOT
The `PIVOT` clause allows you to write a cross-tabulation query. This means that you can aggregate your results and rotate rows into columns.

**Syntax**
```sql
SELECT * FROM
(
	SELECT column1, column2
	FROM tables
	WHERE condition
)
PIVOT
(
	aggregate_function(column2)
	FOR column2
	IN (expression1, expression2, ... expression_n) | subquery
)
ORDER BY expression [ASC | DESC];
```

**Example**
```sql linenums="1"
-- Given the data from this query:
SELECT * FROM CO.CUSTOMER_ORDER_PRODUCTS;

-- Executing the following query returns the total number of Orders Cancelled and Completed for each Customer:
SELECT * FROM
(
SELECT CUSTOMER_ID, FULL_NAME, ORDER_STATUS
FROM CO.CUSTOMER_ORDER_PRODUCTS
)
PIVOT
(
    COUNT(ORDER_STATUS)
    FOR ORDER_STATUS IN ('CANCELLED', 'COMPLETE')
)
ORDER BY FULL_NAME;
```

??? example "Oracle Live SQL"
    - [PIVOT](https://livesql.oracle.com/apex/livesql/s/o9mqx91fxkq3wxpgdcemy4jko)

??? abstract "Sources"
    - [Oracle Documentation - SELECT: pivot_clause](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/SELECT.html#GUID-CFA006CA-6FF1-4972-821E-6996142A51C6)

---

## WITH FUNCTION
`WITH_FUNCTION` clause allows to use temporary functions without physically creating them.

**Syntax**
```sql
WITH FUNCTION <function_name> (<parameters>) RETURN <datatype>
IS
	[declaration_statements]
BEGIN
	<function_statements>
	RETURN <variable>;
END;
<SELECT statement>
```

**Example**
```sql linenums="1"
WITH FUNCTION get_value (in_value IN number) RETURN NUMBER
IS
	final_value NUMBER := 2;
BEGIN
	final_value := in_value * final_value;
	RETURN final_value;
END;
SELECT get_value(2)
FROM dual;
```