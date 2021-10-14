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
