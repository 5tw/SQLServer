
### Setup

```sql
sp_configure 'clr enabled', 1
GO
RECONFIGURE
GO

-- Register assembly
USE AdventureWorks

CREATE ASSEMBLY Utilities
FROM 'C:\Temp\Utilities.dll'
WITH PERMISSION_SET = Safe
GO
```

![](https://i.imgur.com/8uc1VzP.png)

```sql
CREATE FUNCTION dbo.GetOSVersion()
RETURNS NVARCHAR(50) 
AS EXTERNAL NAME Utilities.UserDefinedFunctions.GetOSVersion 
GO

-- Test the managed user-defined function
SELECT dbo.GetOSVersion()
GO
```

```sql
-- Create managed user-defined type
CREATE TYPE Point 
EXTERNAL NAME Utilities.Point
GO

-- Test the managed user-defined type
DECLARE @P Point
SET @P = '1,5'
SELECT @P.X AS X, @P.Y AS Y
GO

-- Create table using managed user-defined type
CREATE TABLE dbo.Points 
(ID int IDENTITY(1,1) PRIMARY KEY, PointValue Point)
GO

INSERT INTO dbo.Points (PointValue) VALUES (CONVERT(Point, '3,4'));
INSERT INTO dbo.Points (PointValue) VALUES (CONVERT(Point, '1,5'));
INSERT INTO dbo.Points (PointValue) VALUES (CAST ('1,99' AS Point));

SELECT	ID, 
		PointValue.X AS X, 
		PointValue.Y AS Y, 
		PointValue.ToString() AS String  
FROM	dbo.Points
GO
```

### Design SQL Server CLR Objects

Create Projects

![](https://i.imgur.com/E6BuDqF.png)

Create Item

![](https://i.imgur.com/OSq0du5.png)

## AdventureWorks Utilities

```sql
USE AdventureWorks

CREATE ASSEMBLY AWorksUtilities
FROM 'C:\Temp\AWorksUtilities.dll'
WITH PERMISSION_SET = Safe
GO
```

```sql
Use Master

CREATE ASYMMETRIC KEY AdventureWorks_Login
FROM EXECUTABLE FILE = 'C:\Source\AWorksUtilities.dll'

CREATE LOGIN AWorksCLR 
FROM ASYMMETRIC KEY AdventureWorks_Login

GRANT EXTERNAL ACCESS ASSEMBLY TO AWorksCLR;
```

**Test Assembly | IPAddress**

```sql
CREATE TYPE IPAddress
EXTERNAL NAME AWorksUtilities.IPAddress
GO

-- Test managed user-defined type
DECLARE @ip dbo.IPAddress
SET @ip = '127.0.0.1'

SELECT @ip.ToString() AS StringValue
SELECT @ip.A AS A, @ip.B AS B, @ip.C AS C, @ip.D AS D
SELECT @ip.Ping() AS PingValue
```

![](https://i.imgur.com/wHgG8Hp.png)

### Test Assembly | Concatenate**

```sql
Use AdventureWorks
GO
CREATE AGGREGATE Concatenate(@input nvarchar(4000))
RETURNS nvarchar(4000)
EXTERNAL NAME AWorksUtilities.Concatenate
GO

-- Test aggregate function
SELECT	AccountNumber, 
		dbo.Concatenate(SalesOrderNumber) AS Orders 
FROM 	Sales.SalesOrderHeader 
GROUP BY AccountNumber
GO
```

![](https://i.imgur.com/FYof3Pz.png)
