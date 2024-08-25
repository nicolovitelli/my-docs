---
title: Subprograms
---

A PL/SQL subprogram is a PL/SQL block that can be invoked repeatedly.
A PL/SQL subprogram can be created:

- inside a Package (which is called a package subprogram)
- at schema level (which is called a standalone subprogram)
- inside another PL/SQL subprogram (which is called a nested subprogram)

**Syntax to invoke a subprogram**
```sql
subprogram_name [([parameter_1 [, parameter_n]...])]
```

!!! info "Notes"
    - Subprograms are divided in three sections:
        - optional declarative part: here you can declare and define Variables, Cursors, etc.
        - executable part: contains one or more statements that will be executed.
        - optional exception-handling part: code that handles exceptions.
    - Stored subprogram = package subprogram, standalone subprogram or named subprograms.
    - Stored subprograms are stored in the database server.

??? abstract "Sources"
    - [Oracle Documentation - PL/SQL Subprograms](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/plsql-subprograms.html)