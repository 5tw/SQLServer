**View Encryption Is One Way Encryption, without any methods to decryption.**

USE AdventureWorksLT

```SQL
CREATE VIEW SalesLT.vw_CustomerOrders
WITH ENCRYPTION
AS
	SELECT C.CustomerID, C.FirstName, C.LastName, O.OrderDate, O.SubTotal, O.TotalDue 
FROM SalesLT.Customer AS C
	INNER JOIN SalesLT.SalesOrderHeader as O
ON C.CustomerID =O.CustomerID;
```

Check View

```SQL
SELECT OBJECT_DEFINITION(OBJECT_ID(N'SalesLT.vw_CustomerOrders',N'V'));
GO
exec sp_helptext 'SalesLT.vw_CustomerOrders'
GO
```

![](https://i.imgur.com/i5xE5bk.png)

Without any methods to decryption, But You can Alter or ReCreate It again, If you have source script.

```SQL
ALTER VIEW SalesLT.vw_CustomerOrders
AS
	SELECT C.CustomerID, C.FirstName, C.LastName, O.OrderDate, O.SubTotal, O.TotalDue 
FROM SalesLT.Customer AS C
	INNER JOIN SalesLT.SalesOrderHeader as O
ON C.CustomerID =O.CustomerID;
GO
```

![](https://i.imgur.com/IBo6U49.png)
