
### Create Database & Setup Filestream File Group

![](https://i.imgur.com/aHqQNC0.png)

![](https://i.imgur.com/lNcd7Jf.png)

### Create Table

**Memory Optimized**

```sql
USE MemDemo
GO
CREATE TABLE dbo.MemoryTable
(id INTEGER NOT NULL PRIMARY KEY NONCLUSTERED HASH WITH (BUCKET_COUNT = 1000000),
 date_value DATETIME NULL)
WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA);
```

**Disk Table**

```sql
CREATE TABLE dbo.DiskTable
(id INTEGER NOT NULL PRIMARY KEY NONCLUSTERED,
 date_value DATETIME NULL);
```

### Performance Comparision

**Memory Optimized**

```sql
BEGIN TRAN
	DECLARE @Memid int = 1
	WHILE @Memid <= 500000
	BEGIN
		INSERT INTO dbo.MemoryTable VALUES (@Memid, GETDATE())
		SET @Memid += 1
	END
COMMIT;
```

**Disk**

```sql
BEGIN TRAN
	DECLARE @Diskid int = 1
	WHILE @Diskid <= 500000
	BEGIN
		INSERT INTO dbo.DiskTable VALUES (@Diskid, GETDATE())
		SET @Diskid += 1
	END
COMMIT;
```

Disk About 10 Secs, Memory Optimized About 5 Secs.

### Check Memory Optimized Stats

```sql
SELECT o.Name, m.*
FROM
  sys.dm_db_xtp_table_memory_stats m
JOIN sys.sysobjects o
  ON m.object_id = o.id
```

![](https://i.imgur.com/ooTIL6s.png)

### Native StoredProcedures

In-memory Table work best with Native StoredProcedures.

```sql
CREATE PROCEDURE dbo.InsertData
	WITH NATIVE_COMPILATION, SCHEMABINDING, EXECUTE AS OWNER
AS
BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = 'us_english')
	DECLARE @Memid int = 1
	WHILE @Memid <= 500000
	BEGIN
		INSERT INTO dbo.MemoryTable VALUES (@Memid, GETDATE())
		SET @Memid += 1
	END
END;
```

### Perf Test

```sql
DELETE FROM dbo.MemoryTable
EXEC dbo.InsertData;
```

About 1 Secs Finish !
