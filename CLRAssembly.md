
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
