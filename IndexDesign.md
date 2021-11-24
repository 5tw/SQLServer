USE AdventureWorksLT

```SQL
SELECT * FROM [SalesLT].[Address]
```

DROP ALL RELATED INDEX

```SQL
SELECT * FROM [SalesLT].[Address]
WHERE CITY = 'London'
```

![](https://i.imgur.com/alN1bZi.png)

Create INDEX_CITY

TEST WHERE CITY = London, ...

![](https://i.imgur.com/UyPVPzJ.png)

**Even with Index, SQL Server don't use it.**

Check Statistics

![](https://i.imgur.com/EddPOUh.png)

Diff City Condition SQL Server will decide whether or not to use Index

WHERE City = 'Austin'

![](https://i.imgur.com/t9cRwca.png)

**Table Scan** VS **Index Scan** **VS Index Seek**

```SQL
SELECT City FROM [SalesLT].[Address]
WHERE City = 'London'
```

It works with Index, Not to use "*".

![](https://i.imgur.com/kL92j4D.png)

Additional Column, I don't use Index again.

![](https://i.imgur.com/LEMi4Ck.png)

Change The Index, Ordinal is important !

![](https://i.imgur.com/gdRW4G0.png)

It works again.

![](https://i.imgur.com/eFmO5Ij.png)

Include Functions

![](https://i.imgur.com/PmoIlcj.png)

![](https://i.imgur.com/B2rSiJT.png)

Under PK Index Conditions, **Index Scan still mean It don't use Index.**

![](https://i.imgur.com/JoX0sa8.png)



