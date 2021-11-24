USE AdventureWorksLT

### Create Indexed View

```SQL
CREATE VIEW vw_IndexedView
WITH SCHEMABINDING
AS
SELECT          SalesLT.Product.ProductID, SalesLT.Product.Name, SalesLT.Product.Color, SalesLT.SalesOrderHeader.SalesOrderID, 
                            SalesLT.SalesOrderHeader.RevisionNumber, SalesLT.SalesOrderHeader.OrderDate, 
                            SalesLT.SalesOrderDetail.SalesOrderDetailID, SalesLT.SalesOrderDetail.OrderQty
FROM              SalesLT.Product INNER JOIN
                            SalesLT.SalesOrderDetail ON SalesLT.Product.ProductID = SalesLT.SalesOrderDetail.ProductID INNER JOIN
                            SalesLT.SalesOrderHeader ON SalesLT.SalesOrderDetail.SalesOrderID = SalesLT.SalesOrderHeader.SalesOrderID
GO

CREATE UNIQUE CLUSTERED INDEX IX_IndexedView ON vw_IndexedView(SalesOrderID,SalesOrderDetailID)
GO
```

![](https://i.imgur.com/bmqh4jL.png)

![](https://i.imgur.com/UroiFl9.png)

### Check Disk Usage

![](https://i.imgur.com/piCk85g.png)
