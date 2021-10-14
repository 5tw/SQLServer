# You Don't Know SQLServer

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

Not Null : A A B B C
Null : N
A : A A
Not A: B B C

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

**Convert_Time** vs **GetDate()**
**Coalesce** vs **IsNull**
**Cast** vs **Convert**

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


