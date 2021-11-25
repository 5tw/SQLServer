### StoredProcedures With Parameters & Output

```sql
CREATE PROCEDURE AddNumbers
   @N1 smallint,
   @N2 smallint,
   @SUM smallint OUTPUT
AS
   SET @SUM = @N1 + @N2
GO

Declare @Ans int
EXEC AddNumbers 3, 5, @Ans Output
SELECT @ANS
```
