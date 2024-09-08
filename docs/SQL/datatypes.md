---
title: Datatypes
---

## Character Dataypes
- `#!sql CHAR`
- `#!sql VARCHAR2`
- `#!sql NCHAR`
- `#!sql NVARCHAR2`

---

## CHAR
The CHAR data type specifies a fixed-length character string in the database character set.

**Syntax**
```sql
CHAR[(size [BYTE | CHAR])]
```

- *size*: integer number which represents the size in BYTE or CHAR.
- `#!sql BYTE | CHAR`: BYTE indicates the column will have byte length semantics. CHAR indicates that the column will have character semantics. 

!!! info "Notes"
    - Default and minimum *size* is 1.
    - Maximum *size* is 2000 Bytes or Characters.
    - If neither BYTE nor CHAR are specified, then the Database uses the value of [`#!sql NLS_LENGTH_SEMANTICS`](/sql/data-dictionary/#nls_length_semantics).
    - If you insert a value that is shorter than the column length, then Oracle blank-pads the value to column length.

??? abstract "Sources"
    - [Oracle Documentation - CHAR Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Data-Types.html#GUID-85E0A0DD-9E90-4AE1-9AD5-93C89FDCFC49)

---

## VARCHAR2
Variable-length character string having maximum length *size* bytes or characters.

Oracle stores a character value in a VARCHAR2 column exactly as you specify it, without any blank-padding.

**Syntax**
```sql
VARCHAR2(size [BYTE | CHAR])
```

- *size*: integer number which represents the size in BYTE or CHAR. 
- `#!sql BYTE | CHAR`: BYTE indicates the column will have byte length semantics. CHAR indicates that the column will have character semantics.

!!! info "Notes"
    - Minimum *size* is 1.
    - Maximum *size* is 32767 for Bytes or 4000 for Characters.
    - If neither BYTE nor CHAR are specified, then the Database uses the value of `#!sql NLS_LENGTH_SEMANTICS`.

??? abstract "Sources"
    - [Oracle Documentation - VARCHAR2 Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Data-Types.html#GUID-0DC7FFAA-F03F-4448-8487-F2592496A510)

---

## NCHAR
The NCHAR data type specifies a fixed-length character string in the national character set (which can be `AL16UTF16` or `UTF8`).

**Syntax**
```sql
NCHAR[(size)]
```

- *size*: integer number which represents the column size. 

!!! info "Notes"
    - Default and minimum *size* is 1.
    - Maximum *size* is 1000 characters for `AL16UTF16` or 2000 for `UTF8`.
    	- However, the maximum lenght of any character value that can be stored into an NCHAR column is 2000 bytes.
    - When specifying *size*, you're specifying code points. 1 code point is equivalent to 2 bytes in `AL16UTF16` and from 1 to 3 bytes in `UTF8`.
    - If you insert a value that is shorter than the column length, then Oracle blank-pads the value to column length.
    - This datatype is generally used for storing characters in multiple languages.

!!! example "Examples"
    - [Oracle Live SQL - CHAR vs. NCHAR](https://livesql.oracle.com/apex/livesql/s/b7luuw1hmfkjr6rew4aek1wfb)

??? abstract "Sources"
    - [Oracle Documentation - NCHAR Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Data-Types.html#GUID-FE15E51B-52C6-45D7-9883-4DF47716A17D)

---

## NVARCHAR2
The NVARCHAR2 data type specifies a variable-length character string in the national character set (which can be `AL16UTF16` or `UTF8`).

**Syntax**
```sql
NVARCHAR2(size)
```

- *size*: integer number which represents the column size. 

!!! info "Notes"
    - Minimum *size* is 1.
    - Maximum *size* is:
        - 1363 -- if `MAX_STRING_SIZE = EXTENDED` and national character set is AL16UTF16.
        - 2000 -- if `MAX_STRING_SIZE = STANDARD` and national character set is AL16UTF16.
        - 32767 -- if `MAX_STRING_SIZE = EXTENDED` and national character set is UTF8.
        - 4000 -- if `MAX_STRING_SIZE = STANDARD`and national character set is UTF8.

??? abstract "Sources"
    - [Oracle Documentation - NVARCHAR2 Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Data-Types.html#GUID-CC15FC97-BE94-4FA4-994A-6DDF7F1A9904)

---

## Number Datatypes
- `#!sql NUMBER`
- `#!sql FLOAT`
- `#!sql BINARY_FLOAT`
- `#!sql BINARY_DOUBLE`

---

## NUMBER
The NUMBER data type stores zero as well as positive and negative fixed numbers.

**Syntax**
```sql
NUMBER[(precision [,scale])]
```

- *precision*: maximum number of significant decimal digits.
- *scale*: number of digits from the decimal point to the least significant digit.

!!! info "Notes"
    - The *precision* can range from 1 to 38.
    - The *scale* can range from -84 to 127.
    - If not specified, the *scale* is 0.
    - Numbers can go from $1.0$ x $10^{-130}$ to $1.0$ x $10^{126}$.
    - If a value exceeds the precision, then Oracle returns an error. If a value exceeds the scale, then Oracle rounds it.
    - The absence of *precision* and *scale* designators specifies the maximum range and precision for an Oracle number.

!!! example "Examples"
    ```
    NUMBER      → 123.89 → 123.89
    NUMBER(3)   → 123.89 → 124
    NUMBER(3,2) → 123.89 → ORA-01438: value larger than specified precision allowed for this column
    NUMBER(5,2) → 123.89 → 123.89
    NUMBER(6,1) → 123.89 → 123.9 
    ```

??? abstract "Sources"
    - [Oracle Documentation - NUMBER Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Data-Types.html#GUID-9401BC04-81C4-4CD5-99E7-C5E25C83F608)

---

## FLOAT Data Type
The FLOAT data type is a subtype of NUMBER.  
It can be specified with or without precision, which has the same definition it has for NUMBER.

**Syntax**
```sql
FLOAT[(precision)]
```

- *precision*: maximum number of significant decimal digits.

!!! info "Notes"
    - Oracle recommends that you use the `#!sql BINARY_FLOAT` and `#!sql BINARY_DOUBLE` data types instead.

---

## Datetime Datatypes
- `#!sql DATE`
- `#!sql TIMESTAMP`
- `#!sql INTERVAL YEAR TO MONTH`
- `#!sql INTERVAL DAY TO SECOND`

---

## ANSI Datatypes
- `#!sql CHARACTER`
- `#!sql CHAR VARYING`
- `#!sql NCHAR VARYING`
- `#!sql VARCHAR`
- `#!sql NATIONAL CHARACTER`, `#!sql NATIONAL CHAR`
- `#!sql NUMERIC`, `#!sql DECIMAL`, `#!sql DEC`
- `#!sql INTEGER`, `#!sql INT`, `#!sql SMALLINT`
- `#!sql FLOAT`
- `#!sql DOUBLE PRECISION`
- `#!sql REAL`

---

## Other Datatypes
- `#!sql LONG`
- `#!sql LONG RAW`
- `#!sql RAW`
- `#!sql BLOB`
- `#!sql CLOB`
- `#!sql NCLOB`
- `#!sql BFILE`
- `#!sql ROWID`
- `#!sql UROWID`