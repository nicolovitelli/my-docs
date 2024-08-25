---
title: Transactions
---

A Transaction is a logical, atomic unit of work that contains one or more SQL statements

A Transaction groups SQL statements so that they are either all committed, which means they are applied to the database, or all rolled back, which means they are undone from the database.  
Oracle Database assigns every transaction a unique identifier called a transaction ID.

**ACID**  
All Oracle transactions obey the basic properties of a database transaction, known as ACID properties.  
ACID is an acronym for the following:

- **ATOMICITY**: All tasks of a Transaction are performed or none of them are. There are no partial Transactions.
	- For example, if a Transaction starts updating 100 rows, but the system fails after 20 updates, then the Database rolls back the changes to these 20 rows.
- **CONSISTENCY**: The Transaction takes the Database from one consistent state to another consistent state.
	- For example, in a banking Transaction that debits a savings account and credits a checking account, a failure must not cause the Database to credit only one account, which would lead to inconsistent data.
- **ISOLATION**: The effect of a Transaction is not visible to other Transactions until the Transaction is committed.
	- For example, one user updating the hr.employees table does not see the uncommitted changes to employees made concurrently by another user. Thus, it appears to users as if Transactions are executing serially.
- **DURABILITY**: Changes made by committed Transactions are permanent. After a Transaction completes, the Database ensures through its recovery mechanisms that changes from the Transaction are not lost.

**Structure of a Transaction**  
A Database Transaction consists of one or more statements. Specifically, a Transaction consists of one of the following:

- One or more DML Statements that together constitute an atomic change to the Database;
- One DDL Statement.

**Beginning of a Transaction**  
A Transaction begins when the first executable SQL Statement is encountered.

A Transaction ID is unique to a Transaction and represents the undo segment number, slot, and sequence number.

**End of a Transaction**  
A Transaction ends when any of the following actions occurs:

- A user issues a COMMIT or ROLLBACK statement without a SAVEPOINT clause.
- A user runs a DDL command such as CREATE, DROP, RENAME, or ALTER.
- A user exits normally from most Oracle Database utilities and tools, causing the current transaction to be implicitly committed. The commit behavior when a user disconnects is application-dependent and configurable.
- A client process terminates abnormally, causing the transaction to be implicitly rolled back using metadata stored in the transaction table and the undo segment.

??? abstract "Sources"
    - [Oracle Documentation - Transactions](https://docs.oracle.com/en/database/oracle/oracle-database/21/cncpt/transactions.html)

---

## ROLLBACK
A rollback of an uncommitted Transaction undoes any changes to data that have been performed by SQL Statements within the transaction.  
After a Transaction has been rolled back, the effects of the work done in the Transaction no longer exist.

In rolling back an entire Transaction, without referencing any savepoints, Oracle Database performs the following actions:

- Undoes all changes made by all the SQL Statements in the Transaction
- Releases all the locks of data held by the Transaction
- Erases all savepoints in the Transaction
- Ends the transaction

**Syntax**
```sql
ROLLBACK;
```

??? abstract "Sources"
    - [Oracle Documentation - ROLLBACK](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/ROLLBACK.html)

---

## COMMIT
A commit ends the current Transaction and makes permanent all changes performed in the Transaction.

When a Transaction commits, the following actions occur:

- Oracle Database releases locks held on rows and tables
- Oracle Database deletes savepoints.
- Oracle Database marks the transaction complete.

After a Transaction commits, users can view the changes.

**Syntax**
```sql
COMMIT;
```

??? abstract "Sources"
    - [Oracle Documentation - COMMIT](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/COMMIT.html)

---

## FLASHBACK TABLE
This Statement is used to restore an earlier state of a Table in the event of human or application error.  
The time in the past to which the Table can be flashed back is dependent on the amount of undo data in the system.

**Syntax**
```sql
FLASHBACK TABLE
	[schema.]table_name
	[, [schema.]table_name]
TO
	BEFORE DROP [RENAME TO table_name] |
	{RESTORE POINT rp_name | SCN number_value | TIMESTAMP date_value ENABLE | DISABLE TRIGGERS}
;
```

- *schema*: the schema containing the Table to be restored.
- *table_name*: the table(s) that needs to be restored.
- `#!sql TO BEFORE DROP`: retrieve from the Recycle Bin a Table that has been dropped. Tables dropped with the PURGE clause cannot be recovered.
- `#!sql RENAME TO table_name`: specify this clause if you want to rename the Table after it is recovered.
- `#!sql TO RESTORE POINT rp_name`: specify a restore point to which you want to flash back the Table.
- `#!sql TO SCN number_value`: specify the System Change Number (SCN) corrensponding the point in time to which you want to return the Table.
- `#!sql TO_TIMESTAMP date_value`: specify a timestamp value corresponding to the poin in time to which you want to return the Table.
- `#!sql ENABLE | DISABLE TRIGGERS`: by default, Oracle disables all the Triggers related to the Table during the Flashback operation (they get re-enabled after the operation is done). Specify ENABLE if you want to override this option and keep them active.

!!! info "Notes"
    - You cannot roll back a FLASHBACK TABLE Statement. However, you can issue another FLASHBACK TABLE to undo a previous flashback.
    - During a Flashback operation, Oracle Database locks the Table until the Flashback is done. The operation is done in a single Transaction.
    - If you're issuing a FLASHBACK TABLE on multiple Tables, all of them must succeed to complete the operation, otherwise the Statement will be reverted.
    - `#!sql FLASHBACK TABLE TO BEFORE DROP` does not recover referential constraints.
    - If the Table is recovered from the Recycle Bin, then the Database also retrieves:
	    - Indexes (except for Bitmap Join and Domain Indexes)
	    - Triggers
	    - Constraints
    - You cannot perform a FLASHBACK TABLE on a Materialized View.
    - FLASHBACK TABLE might require to enable a Table's row movement:
	    - To check if it is enabled: `#!sql SELECT table_name,row_movement FROM all_tables WHERE table_name = your_table_name;`
	    - To enable it: `#!sql ALTER TABLE [schema.]table_name ENABLE ROW MOVEMENT;`

!!! warning "Privilege Restrictions"
    - In your Schema: `#!sql GRANT FLASHBACK ON [schema.]table_name TO user_name;`
    - In another's user Schema: `#!sql GRANT FLASHBACK ANY TABLE TO user_name;`

??? abstract "Sources"
    - [Oracle Documentation - FLASHBACK TABLE](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/FLASHBACK-TABLE.html)

---

## LOCK TABLE
Use this Statement to lock:

- one or more Tables
- Table Partitions
- Table Subpartitions

A Locked Table remains locked until you either commit your Transaction or roll it back.  
A Locked Table does not prevent other users from querying the Table.

**Syntax**
```sql
LOCK TABLE [schema.]object_name
	[
		PARTITION {(partition_name) | FOR (partition_key_value_1 [, partition_key_value_n]) | 
		SUBPARTITION (subpartition_name) | FOR (subpartition_key_vale_1 [, subpartition_key_value_n])
	]
IN lockmode MODE
[NO WAIT |  WAIT number_value]
```

- *object_name*; specify the Table or View you want to lock.
	- specifying a View causes the Database to lock the base Tables of the View.
- IN *lockmode* MODE: can be one of the following:
	- `#!sql ROW SHARE`: permits concurrent access to the Locked Table but prohibits users from locking the entire Table for exclusive access.
	- `#!sql ROW EXCLUSIVE`: same as ROW SHARE, but prohibits locking in SHARE mode.
	- `#!sql SHARE UPDATE`: synonym of ROW SHARE.
	- `#!sql SHARE`: permits concurrent queries but prohibits updates to the Locked Table.
	- `#!sql SHARE ROW EXCLUSIVE`: allows other users to look at the Table but prohibits locking it in SHARE mode or from updating rows.
	- `#!sql EXCLUSIVE`: permits queries on the Locked Table but prohibits any other activity on it.
- `#!sql NOWAIT`: immediately return an error message if the Table is already locked.
- `#!sql WAIT number_value`: wait for *number_value* (in seconds) to acquire a DML Lock.

!!! info "Notes"
    - If you specify neither NOWAIT nor WAIT, the Database waits indefinitely until the Table is available.

!!! warning "Privilege Restrictions"
    - `#!sql GRANT LOCK ANY TABLE TO user_name;`

??? abstract "Sources"
    - [Oracle Documentation - LOCK TABLE](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/LOCK-TABLE.html)