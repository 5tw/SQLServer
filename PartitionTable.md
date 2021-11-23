### Restore Database

[AdventureWorks](https://docs.microsoft.com/zh-tw/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms)

### Create FileGroup 

```SQL
Use master;
GO
```

We need 4 filegroups, number by partition function **plus 1**

```SQL
-- Add filegroup 1
ALTER DATABASE AdventureWorks
ADD FILEGROUP FileGroup1;
GO
-- Add file to filegroup 1
ALTER DATABASE AdventureWorks 
ADD FILE 
(
    NAME = AdventureWorks1,
    FILENAME = '.\MSSQL\DATA\AdventureWorks1.ndf',
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
)
TO FILEGROUP FileGroup1;
```

### Create Partition Function

```SQL
USE AdventureWorks;
GO

CREATE PARTITION FUNCTION YearlyPartitionFunction (datetime) 
AS RANGE LEFT 
FOR VALUES ('2005-12-31 00:00:00.000', '2006-12-31 00:00:00.000',  '2007-12-31 00:00:00.000');
GO
```

### Create Partitino Scheme

```SQL
CREATE PARTITION SCHEME OrdersByYear 
AS PARTITION YearlyPartitionFunction 
TO (FileGroup1, FileGroup2, FileGroup3, FileGroup4);
```

### Create Partition Table

```SQL
CREATE TABLE Sales.SalesOrderHeader_Partitioned1
(
	SalesOrderID int NOT NULL,
	RevisionNumber tinyint NOT NULL DEFAULT (0),
	OrderDate datetime NOT NULL DEFAULT GETDATE(),
	DueDate datetime NOT NULL,
	ShipDate datetime NULL,
	[Status] tinyint NOT NULL DEFAULT(1),
	OnlineOrderFlag dbo.Flag NOT NULL DEFAULT (1),
	SalesOrderNumber nvarchar(25),
	PurchaseOrderNumber dbo.OrderNumber NULL,
	AccountNumber dbo.AccountNumber NULL,
	CustomerID int NOT NULL,
	SalesPersonID int NULL,
	TerritoryID int NULL,
	BillToAddressID int NOT NULL,
	ShipToAddressID int NOT NULL,
	ShipMethodID int NOT NULL,
	CreditCardID int NULL,
	CreditCardApprovalCode varchar(15) NULL,
	CurrencyRateID int NULL,
	SubTotal money NOT NULL DEFAULT(0.00),
	TaxAmt money NOT NULL DEFAULT (0.00),
	Freight money NOT NULL DEFAULT (0.00),
	TotalDue money,
	Comment nvarchar(128) NULL,
	rowguid uniqueidentifier ROWGUIDCOL NOT NULL DEFAULT NEWID(),
	ModifiedDate datetime NOT NULL DEFAULT GETDATE()
) 
ON OrdersByYear(OrderDate);
GO
```

### Insert Data To Partition Table

```SQL
INSERT INTO Sales.SalesOrderHeader_Partitioned
		(SalesOrderID, RevisionNumber, OrderDate, DueDate, ShipDate, [Status], OnlineOrderFlag, SalesOrderNumber, PurchaseOrderNumber,
		AccountNumber, CustomerID, SalesPersonID, TerritoryID, BillToAddressID, ShipToAddressID, ShipMethodID, CreditCardID, CreditCardApprovalCode,
		CurrencyRateID, SubTotal, TaxAmt, Freight, TotalDue, Comment, rowguid, ModifiedDate)
SELECT	SalesOrderID, RevisionNumber, OrderDate, DueDate, ShipDate, [Status], OnlineOrderFlag, SalesOrderNumber, PurchaseOrderNumber,
		AccountNumber, CustomerID, SalesPersonID, TerritoryID, BillToAddressID, ShipToAddressID, ShipMethodID, CreditCardID, CreditCardApprovalCode,
		CurrencyRateID, SubTotal, TaxAmt, Freight, TotalDue, Comment, rowguid, ModifiedDate
FROM	Sales.SalesOrderHeader
```

![](https://i.imgur.com/124AwLy.png)

### Check Partitino Table

```SQL
-- Count of rows per year - the partitions should match this
SELECT	DISTINCT DATEPART(YEAR, OrderDate) AS [Year], COUNT(*) AS TotalOrders
FROM	Sales.SalesOrderHeader_Partitioned
GROUP	BY DATEPART(YEAR, OrderDate)
ORDER	BY 1

-- Count of rows per partition - the numbers should be the same as the query above
SELECT	s.name AS SchemaName,
		t.name AS TableName,
		COALESCE(f.name, d.name) AS [FileGroup], 
		SUM(p.rows) AS [RowCount],
		SUM(a.total_pages) AS DataPages
FROM	sys.tables AS t
INNER	JOIN sys.indexes AS i ON i.object_id = t.object_id
INNER	JOIN sys.partitions AS p ON p.object_id = t.object_id AND p.index_id = i.index_id
INNER	JOIN sys.allocation_units AS a ON a.container_id = p.partition_id
INNER	JOIN sys.schemas AS s ON s.schema_id = t.schema_id
LEFT	JOIN sys.filegroups AS f ON f.data_space_id = i.data_space_id
LEFT	JOIN sys.destination_data_spaces AS dds ON dds.partition_scheme_id = i.data_space_id AND dds.destination_id = p.partition_number
LEFT	JOIN sys.filegroups AS d ON d.data_space_id = dds.data_space_id
WHERE	t.[type] = 'U' AND i.index_id IN (0, 1) AND t.name LIKE 'SalesOrderHeader_Partitioned'
GROUP	BY s.NAME, COALESCE(f.NAME, d.NAME), t.NAME, t.object_id
ORDER	BY t.name
go
```

![](https://i.imgur.com/BdwIVJQ.png)

### Partition Function Split & Merge

```SQL
-- SPLIT

ALTER PARTITION SCHEME OrdersByYear NEXT USED FileGroup1
GO
ALTER PARTITION FUNCTION YearlyPartitionFunction() SPLIT RANGE ('2008-12-31 00:00:00.000')

ALTER PARTITION FUNCTION YearlyPartitionFunction() SPLIT RANGE ('2009-12-31 00:00:00.000')

--MERGE

ALTER PARTITION FUNCTION YearlyPartitionFunction()
MERGE RANGE ('2008-12-31 00:00')

ALTER PARTITION FUNCTION YearlyPartitionFunction()
MERGE RANGE ('2007-12-31 00:00')

ALTER PARTITION FUNCTION YearlyPartitionFunction()
MERGE RANGE ('2006-12-31 00:00')

ALTER PARTITION FUNCTION YearlyPartitionFunction()
MERGE RANGE ('2005-12-31 00:00')

ALTER PARTITION SCHEME OrdersByYear NEXT USED FileGroup1
GO
ALTER PARTITION FUNCTION YearlyPartitionFunction() SPLIT RANGE ('2005-12-31 00:00:00.000')

ALTER PARTITION SCHEME OrdersByYear NEXT USED FileGroup2
GO
ALTER PARTITION FUNCTION YearlyPartitionFunction() SPLIT RANGE ('2006-12-31 00:00:00.000')
go
ALTER PARTITION SCHEME OrdersByYear NEXT USED FileGroup3
GO
ALTER PARTITION FUNCTION YearlyPartitionFunction() SPLIT RANGE ('2007-12-31 00:00:00.000')
GO
```

![](https://i.imgur.com/Da0Dtr4.png)

### Partition Table Switch

```SQL

DECLARE @p int = $PARTITION.YearlyPartitionFunction('2006-12-31 00:00');
select @p

ALTER TABLE Sales.SalesOrderHeader_Partitioned1
SWITCH partition 2 TO Sales.SalesOrderHeader_Partitioned PARTITION 2
GO
```

![](https://i.imgur.com/lU4RAeX.png)

![](https://i.imgur.com/joNamv4.png)
