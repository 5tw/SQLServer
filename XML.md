## FOR XML

```sql
USE AdeventureWorks
```

SQL Data to XML Format

### FOR XML RAW

```sql
USE AdventureWorks
SELECT ProductID, Name, ListPrice
FROM Production.Product
FOR XML RAW
```

![](https://i.imgur.com/3GfAoRO.png)

```sql
SELECT ProductID, Name, ListPrice
FROM Production.Product
FOR XML RAW('Product'), ELEMENTS
```

### FOR XML AUTO

![](https://i.imgur.com/t9d5y8o.png)

```sql
SELECT ProductID, Name, ListPrice
FROM Production.Product Product
FOR XML AUTO
```

![](https://i.imgur.com/EAqh2TK.png)

### FOR XML PATH

```sql
SELECT ProductID AS "@ProductID",
       Name AS "*",
       Size AS "Description/@Size",
       Color AS "Description/text()"
FROM Production.Product
ORDER BY Name
FOR XML PATH('Product')
```

![](https://i.imgur.com/gYPLCZw.png)

## OPENXML

Read XML To SQL Server

### Setup

```sql
DECLARE @doc xml
SET @doc = '<?xml version="1.0" ?>
            <SalesInvoice InvoiceID="1000" CustomerID="123" OrderDate="2004-03-07">
              <Items>
                <Item ProductCode="12" Quantity="2" UnitPrice="12.99"><ProductName>Bike</ProductName></Item>
                <Item ProductCode="41" Quantity="1" UnitPrice="17.45"><ProductName>Helmet</ProductName></Item>
                <Item ProductCode="2" Quantity="1" UnitPrice="2.99"><ProductName>Water Bottle</ProductName></Item>
              </Items>
            </SalesInvoice>'

DECLARE @docHandle int
EXEC sp_xml_preparedocument @docHandle OUTPUT, @doc
```

### Read Attribute or Element

```sql
SELECT * FROM
OPENXML(@docHandle, '/SalesInvoice/Items/Item', 3)
WITH
(	ProductCode	int,
	Quantity	int,
	UnitPrice	float,
	ProductName nvarchar(20))
```

```sql
SELECT * FROM
OPENXML(@docHandle, '/SalesInvoice/Items/Item', 1)
WITH
(	InvoiceID	int '../../@InvoiceID',
	CustomerID	int '../../@CustomerID',
	OrderDate	datetime '../../@OrderDate',
	ProductCode	int,
	Quantity	int,
	UnitPrice	float,
	ProductName nvarchar(20) './ProductName')

EXEC sp_xml_removedocument @docHandle
GO
```

![](https://i.imgur.com/w8r1cuu.png)

