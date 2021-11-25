### Identity

```sql
SELECT USER, CURRENT_USER, SYSTEM_USER
```

![](https://i.imgur.com/dx6D40R.png)


### Create Login

```SQL
USE MASTER
DROP LOGIN U1

CREATE LOGIN U1 WITH PASSWORD = '123qaz!@#'
```

### CREATE DB & SP

```SQL
-- create Marketing database
CREATE DATABASE Marketing
GO

USE Marketing
GO

CREATE TABLE dbo.MarketingTable
(data nvarchar(10))
GO

INSERT INTO dbo.MarketingTable
VALUES
('Some data')

-- create Sales database
CREATE DATABASE Sales
GO

USE Sales
GO

CREATE USER U1 FROM LOGIN U1
GO

CREATE PROCEDURE dbo.GetMarketingDataUnsigned
WITH EXECUTE AS 'U1'
AS
SELECT * FROM Marketing.dbo.MarketingTable
GO

CREATE PROCEDURE dbo.GetMarketingDataSigned
WITH EXECUTE AS 'U1'
AS
SELECT * FROM Marketing.dbo.MarketingTable
GO
```

### Verify StoredProcedures

```SQL
EXEC GetMarketingDataUnsigned

EXEC GetMarketingDataSigned
```

```plain
Msg 916, Level 14, State 1, Procedure GetMarketingDataUnsigned, Line 4 [Batch Start Line 42]
The server principal "U1" is not able to access the database "Marketing" under the current security context.
```

在自己的資料庫使用憑照簽章 StoredProcedures，再到對方資料庫去使用憑證建立一致的使用者。

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'HelloWordCert!@#'
GO

CREATE CERTIFICATE MyCert 
   WITH SUBJECT = 'CERT', 
   EXPIRY_DATE = '12/31/2030'
GO

ADD SIGNATURE TO dbo.GetMarketingDataSigned
BY CERTIFICATE MyCert
GO

BACKUP CERTIFICATE MyCert TO FILE = 'C:\temp\MyCert.cer'

USE Marketing
CREATE CERTIFICATE MyCert
FROM FILE = 'C:\temp\MyCert.cer'

CREATE USER U1
FROM CERTIFICATE MyCert

GRANT AUTHENTICATE TO U1

GRANT SELECT ON dbo.MarketingTable TO U1
```


```SQL
USE Sales
EXEC dbo.GetMarketingDataUnsigned
```

``plain
Msg 916, Level 14, State 1, Procedure GetMarketingDataUnsigned, Line 5 [Batch Start Line 82]
The server principal "U1" is not able to access the database "Marketing" under the current security context.
```

```SQL
EXEC dbo.GetMarketingDataSigned
```

**It Works.**
