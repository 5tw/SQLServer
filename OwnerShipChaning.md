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

結論：真正會打破 Ownership Chaining 的是 **GRANTEE**，Table Owner 與 Schema 是不是 dbo 並不重要，重要的是 GRANTEE 是不是 dbo。

如果是的話，User 只要有 EXEC 對於 StoredProcedures 的權限，StoredProcedures 下面所需的關聯物件如果 GRANTEE 為 dbo 則可以正常使用。

驗證：

SCHEMA: **Weired**
SCHEMA Owner: **Weired1**
StoredProcedure: SP_W
SP_W Owner: **Weired2**
U1 with Execute to SP_W


```sql
CREATE USER Weired1 WITHOUT LOGIN
CREATE SCHEMA Weired
ALTER AUTHORIZATION ON SCHEMA::[Weired] TO [Weired1]

CREATE TABLE Weired.T2
  (
      C1 INT,
      C2 INT
  )
Insert Weired.T2 VALUES(1, 1)
GO
```

此時 T2 的 GRANTEE 是屬於 Weired1

![](https://i.imgur.com/j6quB49.png)

```sql
CREATE PROC SP_W
AS
	SELECT * FROM Weired.T2
GO
GRANT EXEC ON SP_W TO U1
EXEC('SELECT User;Exec SP_W') AS USER = 'U1'
```

**U1 執行 SP_W 會因權限不足而失敗**

```plain
Msg 229, Level 14, State 5, Procedure SP_W, Line 3 [Batch Start Line 114]
The SELECT permission was denied on the object 'T2', database 'MarketDev', schema 'Weired'.
```

將 T2 的 GRANTEE 授權給 dbo

```sql
ALTER Authorization on Weired.T2 TO dbo
EXEC sp_table_privileges 'T2'
```

![](https://i.imgur.com/N5JNA2j.png)


```sql
EXEC('SELECT User;Exec SP_W') AS USER = 'U1'
```

![](https://i.imgur.com/8F9WiJc.png)

這次就可以順利查到結果囉，結論重點是 StoredProcedures 所使用到的物件之 GRANTEE 是否屬於 dbo，如果不是問題可大。而這個原因是來自於 SCHEMA Owner 不是 dbo，因此最好將 SCHEMA Owner 都保持在 dbo 上，除非這個 SCHEMA 有強烈的排他使用性，刻意要打破 Owership Chaining。


