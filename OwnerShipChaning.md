### Setup

```sql
DROP TABLE IF EXISTS dbo.T1
DROP TABLE IF EXISTS U3.T1
DROP SCHEMA IF EXISTS U3
DROP USER IF EXISTS U1
DROP USER IF EXISTS U2
DROP USER IF EXISTS U3
DROP PROC IF EXISTS SP
DROP PROC IF EXISTS SP2

CREATE TABLE T1
  (
      C1 INT,
      C2 INT
  )

Insert T1 VALUES(1, 1)
CREATE USER U1 WITHOUT LOGIN
GO
```

```sql
CREATE OR ALTER proc SP
AS
  SELECT C1 FROM T1
GO
GRANT EXEC ON SP TO U1
EXEC('SELECT User;Exec SP') AS USER = 'U1'
```

![](https://i.imgur.com/ungc6RI.png)

```sql
EXEC('SELECT User,* FROM T1') AS USER = 'U1'
```

```plain
EXEC('SELECT User,* FROM T1') AS USER = 'U1'
```

```plain
Msg 229, Level 14, State 5, Line 53
The SELECT permission was denied on the object 'T1', database 'MarketDev', schema 'dbo'.
```
![](https://i.imgur.com/Eiii8JG.png)

```sql
EXEC sp_table_privileges 'T1'  
```

![](https://i.imgur.com/uUPjvjE.png)

### Change Table Authorization

```sql
CREATE USER U2 WITHOUT LOGIN
ALTER Authorization on T1 TO U2
EXEC('SELECT User;Exec SP') AS USER = 'U1'
```

```plain
Msg 229, Level 14, State 5, Procedure SP, Line 3 [Batch Start Line 58]
The SELECT permission was denied on the object 'T1', database 'MarketDev', schema 'dbo'.
```

![](https://i.imgur.com/GkWpCxa.png)

```sql
ALTER Authorization on T1 TO dbo
EXEC('SELECT User;Exec SP') AS USER = 'U1'
```

**It works again.**

```sql
CREATE SCHEMA U3
CREATE USER U3 WITHOUT LOGIN
ALTER AUTHORIZATION ON SCHEMA::[U3] TO [U3]
```

### Change Table Schema

```sql
ALTER SCHEMA U3 TRANSFER dbo.T1;  
```

```sql
EXEC('SELECT User;Exec SP') AS USER = 'U1'
```

```plain
Msg 208, Level 16, State 1, Procedure SP, Line 3 [Batch Start Line 71]
Invalid object name 'dbo.T1'.
```

```sql
CREATE OR ALTER proc SP2
AS
  SELECT C1 FROM U3.T1
GO
GRANT EXEC ON SP2 TO U1
EXEC('SELECT User;Exec SP2') AS USER = 'U1'
```

Even schema is not **dbo**, Ownership Chaning still works.

![](https://i.imgur.com/qZntp7j.png)

```sql
EXEC sp_table_privileges 'T1'  
```

![](https://i.imgur.com/NF4Ib7b.png)

### Grant Permissions to dbo After Authorization by U2

```sql
GRANT SELECT ON T1 TO dbo
```

```plain
Cannot grant, deny, or revoke permissions to sa, dbo, entity owner, information_schema, sys, or yourself.
```









