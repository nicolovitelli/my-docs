---
title: Managing Users
---

## GRANT
Use this Statement to grant:

- System Privileges to Users and Roles
- Roles to Users, Roles and Program Unites
- Object Privileges for a particular Object to Users and Roles

**Syntax - System Privileges**
```sql
GRANT
	role_name |
	ALL PRIVILEGES |
	system_privilege_name
TO
	{user_name | grantee_role_name | PUBLIC} |
	user_name IDENTIFIED BY password
[WITH {ADMIN | DELEGATE} OPTION]
;
```

- *role_name*: specify the role you want to grant.
- `#!sql ALL PRIVILEGES`: if you want to grant all of the System Privileges
	- the System Privileges `#!sql SELECT ANY DICTIONARY`,`#!sql ALTER DATABASE LINK`,`#!sql ALTER PUBLIC DATABASE LINK` are not included.
- *system_privilege_name*: specify the System Privilege you want to grant.
- *user_name*: specify the user that will be granted the System Privileges.
- *grantee_role_name*: specify the role that will be granted the System Privileges.
- `#!sql PUBLIC`: specify PUBLIC to grant the privileges to all users.
- `#!sql user_name IDENTIFIED BY password`: if *user_name* exists, resets their password, otherwise creates the user with that password.
- `#!sql WITH ADMIN OPTION`: enables the grantee to:
	- grant the privilege or role to another user or role
	- revoke the privilege or role from another user or role
	- alter the privilege or role to change the authorization needed to access it
	- drop the privilege or role
	- grant the privilege or role to a program unit in the grantee's schema
	- revoke the privilege or role from a program unit in the grantee's schema
- `#!sql WITH DELEGATE OPTION`: enables the grantee to:
	- grant the privilege or role to a program unit in the grantee's schema
	- revoke the privilege or role from a program unit in the grantee's schema

**Syntax - Object Privileges**
```sql
GRANT
	object_privilege_1 [, object_privilege_n] |
	ALL [PRIVILEGES]
	[(column_1 [, column_n])]
ON
	[schema.]object_name |
	USER user_1 [, user_n]
TO
	{user_name | grantee_role_name | PUBLIC}
[WITH HIERARCHY OPTION]
[WITH GRANT OPTION]
;
```

- *object_privilege_1*, *object_privilege_n*: specify the Object Privilege(s) you want to grant.
- `#!sql ALL [PRIVILEGES]`: specify ALL to grant all the privileges for *object_name*.
- \[(*column_1* \[, *column_n*])]: specify the Table or View column on which privileges are to be granted. Can only be specified for INSERT, REFERENCES or UPDATE Object Privileges.
- *object_name*: specify the schema object on which the privileges are to be granted.
	- Must be one of the following:
		- Table
		- View
		- Materialized View
		- Sequence
		- Procedure
		- Function
		- Package
		- User-defined Type
		- Synonym
		- Directory
- `#!sql ON USER user_1 [, user_n]`: specify the database user you want to grant privileges to.
	- You cannot specify PUBLIC as *user*.
- `#!sql WITH HIERARCHY OPTION`: grants the specified Object Privileges on all subobjects of *object_name*, such as subviews created under a View.
- `#!sql WITH GRANT OPTION`: enables the grantee to grant the Object Privileges to other users and roles.

!!! info "Notes"
    - A user, role or PUBLIC cannot appear more that once in the grantee clause.
    - An Object Privilege cannot appear more than once in the list of privileges to be granted. 
    - To remove the `#!sql WITH ADMIN OPTION` or `#!sql WITH DELEGATE OPTION` from an user or role, you must revoke the privilege or role and the grant again.
    - You cannot grant a role to itself.
    - You cannot grant privileges directly to a single Partition of a Partitioned Table.
    - For Object Privileges, `#!sql WITH ADMIN OPTION` cannot be granted to roles.

!!! warning "Privilege Restrictions"
    - `#!sql GRANT ANY PRIVILEGE TO user_name;`
    - `#!sql WITH ADMIN OPTION` Clause

!!! question "Data Dictionary"
    - `#!sql *_COL_PRIVS`

??? abstract "Sources"
    - [Oracle Documentation - List of System Privileges](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/GRANT.html#GUID-20B4E2C0-A7F8-4BC8-A5E8-BE61BDC41AC3__BABEFFEE)
    - [Oracle Documentation - List of Object Privileges](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/GRANT.html#GUID-20B4E2C0-A7F8-4BC8-A5E8-BE61BDC41AC3__BGBCIIEG)
    - [Oracle Documentation - GRANT](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/GRANT.html)

---

## WITH GRANT OPTION vs. WITH ADMIN OPTION

**WITH GRANT OPTION**  
*Only for OBJECT PRIVILEGES*

- The owner of an object can grant it to another user by specifying the WITH GRANT OPTION clause in the GRANT statement.
- The new grantee can then grant the same level of access to other users or roles.

!!! info "Notes"
    - You cannot grant WITH GRANT OPTION to a role.
    - If you revoke access to a user who had been granted access to an object WITH GRANT OPTION and that user had granted access to another user, both sets of grants will be revoked (cascade effect).

**WITH ADMIN OPTION**  
*Only for SYSTEM PRIVILEGES*  
Specify WITH ADMIN OPTION to enable the grantee to:

- Grant the role to another user or role, unless the role is a GLOBAL role
- Revoke the role from another user or role
- Alter the role to change the authorization needed to access it
- Drop the role

To revoke the ADMIN OPTION on a Sytem Privilege or role from a user, you must revoke the privilege or role from the user altogether and then grant the privilege or role to the user without the ADMIN OPTION.

---

## GRANT SELECT vs GRANT READ
- `#!sql GRANT SELECT` allows users to lock Tables through the `#!sql SELECT .. FOR UPDATE` statement.
- `#!sql GRANT READ` only allows SELECT statement, preventing the table's lock.

```sql
GRANT READ ON <schema.><object> TO <user>;
```