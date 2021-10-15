# You Don't Know SQL Server

## Escape Char

```sql
Select * From Customers Where Customers Like '%\%%' escape '%'
```

## True / False / Null

dataset = { A, A, B, B, C, N }

|State|Beloning|
|--|--|
|Not Null|A A B B C|
|Null|N|
|Equal A|A A|
|Not Equal A|B B C|

## Compare Execution Plan

1. Select Different Syntax

## Order By With Case

```sql
SELECT * FROM dbo.Customers
order by case when country = 'USA' then 1
			  when country = 'UK' then 2
			  else 5
		end,city
```

[MSDocs - CASE (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/case-transact-sql?view=sql-server-ver15)

## 四捨六入五成雙

ISO 處理 float 的進位方法：

|數值|進位|
|--|--|
|5.3|5|
|5.6|6|
|5.5|6|
|6.5|6|
|7.5|8|

但在 SQL Server 中並非採用「四捨六入五成雙」，而是使用四捨五入的方式。

## ANSI 🆚 TSQL

1. **Convert_Time** vs **GetDate()**
2. **Coalesce** vs **IsNull**
3. **Cast** vs **Convert**

## Add Opesrator 🆚 Concat Function

當相加的項目存在 Null 的時候，Add Operator 會將結果以 Null 回應，Concat Function 則會忽略 Null 當作空白。

## DatePart 🆚 DateName

DarePart 回傳的是 Int
DateName 回傳的是 VarChar 且顯示結果未受 Login User Language 影響其顯示

## Sys.SysLanguages

```sql
-- 取得目前使用者的語系
SELECT @@language

-- 查詢系統中所有的語系資訊
SELECT * from sys.syslanguages

-- 設定目前使用者的語系以及查詢 localize 日期
SET Language N'日本語'
SELECT DateName(Month, Getdate()),
       DateName(WeekDay, Getdate())
```

## 查詢效能

Merge Join > Hash Join > Nested Loop Join

Join > SubQuery (通常而言)

Index Seek > Index Scan

### 

```sql
SET statistics io ON
```

藉由將 STATISTICS IO 設定為 ON 以顯示查詢的 IO 統計資訊

**邏輯讀取** 從資料快取中讀取的頁數
**實體讀取** 從磁碟中讀取的頁數

## CTE

Common Table Expression 解決 Outer Join 查詢上語法設計思考不易的痛點，改善語法的易讀性，而不影響效能 😀

## Outer Join 的 On 有事嗎？

思考用 On 來限制 Outer Join 單方面資料表的作用 😶

## Delete With Select

可以組合複雜的刪除條件，例如刪除符合特定 Join 結果的資料。

```sql
DELETE FROM A
FROM   dbo.customers A
       LEFT OUTER JOIN dbo.orders B
                    ON A.customerid = B.customerid
WHERE  B.orderid IS NULL
```

## Insert Select 🆚 Select Into 🆚 Insert Exec

<dl>
  <dt>Insert Select</dt>
  <dd>將查詢資料新增至已存在的資料表</dd>
  <dt>Insert Exec</dt>
  <dd>將執行 Stored Procedures 的結果新增至已存在的資料表</dd>	
  <dt>Select Into</dt>
  <dd>將查詢資料建立至新資料表</dd>
</dl>

## IDENT_CURRENT

取得資料列自動編號的最新資料值，可以用於查詢最新的資料而不必 Order By。

```sql
Select IDENT_CURRENT('dbo.Employees')
```
