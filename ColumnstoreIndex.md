# Columnstore Index

USE AdventureWorksDW

```SQL
SET STATISTICS TIME ON
GO

SELECT SalesTerritoryRegion
		,p.EnglishProductName
		,d.WeekNumberOfYear
		,d.CalendarYear
		,SUM(fi.SalesAmount) Revenue
		,AVG(OrderQuantity) AverageQuantity
		,STDEV(UnitPrice) PriceStandardDeviation
		,SUM(TaxAmt) TotalTaxPayable
FROM dbo.FactInternetSales as fi
INNER JOIN dbo.DimProduct as p ON fi.ProductKey = p.ProductKey
INNER JOIN dbo.DimDate as d ON fi.OrderDate = d.FullDateAlternateKey
INNER JOIN dbo.DimSalesTerritory as st on fi.SalesTerritoryKey = st.SalesTerritoryKey
	AND fi.OrderDate BETWEEN '1/1/2007' AND '12/31/2007'
GROUP BY SalesTerritoryRegion, d.CalendarYear, d.WeekNumberOfYear, p.EnglishProductName
ORDER BY SalesTerritoryRegion, SUM(fi.SalesAmount) desc;
```

### Check Performance with Index

```plain
 SQL Server Execution Times:
   CPU time = 2749 ms,  elapsed time = 927 ms.
```

![](https://i.imgur.com/cksIQgA.png)

### Without Index

```plain
 SQL Server Execution Times:
   CPU time = 2563 ms,  elapsed time = 906 ms.
```

### NonClustered ColumnstoreIndex

![](https://i.imgur.com/iEdv5FS.png)

Choose All columns

![](https://i.imgur.com/kfY46sZ.png)

```plain
 SQL Server Execution Times:
   CPU time = 359 ms,  elapsed time = 255 ms.
```

### Clustered ColumnstoreIndex

![](https://i.imgur.com/CcFYR1s.png)

```plain
 SQL Server Execution Times:
   CPU time = 342 ms,  elapsed time = 267 ms.
```

### Is It Possible with multiple Columnstore Index ?

![](https://i.imgur.com/AeNSl1k.png)
