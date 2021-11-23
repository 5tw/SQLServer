## Column Level

```SQL
CREATE TABLE [dbo].[A2](
	[A] [int] NULL,
	[B] [char](3) NULL
	CONSTRAINT [CK_A2] CHECK  (([B] like '[A-C,FG][0-9][0-9]'))
) ON [PRIMARY]
GO
```
