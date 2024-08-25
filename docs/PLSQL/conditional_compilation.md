---
title: Conditional Compilation
---

Conditional Compilation in PL/SQL is generally used for code that needs to be ran on multiple Oracle DB versions.  
Main constructs are `$IF`, `$THEN`, `$ELSE`, `$ELSIF`, `$END`, `$DEBUG` (to check if debug mode is on) `$ERROR` (for displaying errors) and `$WARNING` (for displaying warnings).

!!! example "Examples"
    ```sql
    declare
    	v_version varchar2(10);
    begin
    	$if dbms_db_version.version = 1 2 $then
    		v_version := 'oracle 12c';
    	$elsif dbms_db_version.version = 11 $then
    		v_version := 'oracle 11g';
    	$else
    		v_version := 'other';
    	$end;
    
    	dbms_output.put_line('database version: ' || v_version);
    end;
    ```
    
    - [Oracle Live SQL - Examples of Conditional Compilation](https://livesql.oracle.com/apex/livesql/file/content_CI282MWSUR5SFOOALWJICI6QF.html)

??? abstract "Sources"
    - [Oracle-Base - Conditional Compilation in Oracle 10g](https://oracle-base.com/articles/10g/conditional-compilation-10gr2)
    - [Oracle Book - PL/SQL Conditional Compilation](https://www.oracle.com/technetwork/database/features/plsql/overview/plsql-conditional-compilation-133587.pdf)