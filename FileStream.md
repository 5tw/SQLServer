## Setup

### Enable FileStream

![](https://i.imgur.com/sqOGZXP.png)

### Enable FileStream Access Level

![](https://i.imgur.com/o6bLdaG.png)

### Create Database With FileStream Filegroup

![](https://i.imgur.com/H0KZpDW.png)

![](https://i.imgur.com/lfPOOcu.png)

**Set DirectoryName & Non-Transacted Access**

![](https://i.imgur.com/c1fjrlo.png)

### Create FileTable

![](https://i.imgur.com/dQS9vlL.png)

```sql
CREATE TABLE dbo.FSTable AS FILETABLE
  WITH
  (
    FILETABLE_DIRECTORY = 'FSDir',
    FILETABLE_COLLATE_FILENAME = database_default
  )
GO
```

![](https://i.imgur.com/pBafaZr.png)

### Upload File


