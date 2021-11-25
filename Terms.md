Seek

Scan

ColumnStore Index

Clustered Index

NonClustered Index

Query Store

Database Engine Tuning Advisor

Excution Plans

Heaps

Composite Indexes

Temporal Tables

Temorary Tables

Index Fragmentation

Covering Index

Fill Factor

Pad Index

Constraints

Dynamic Management Views

Catalog Views


### 資料庫物件

|Name|Type|Usage|
|---|---|---|
|Indexed View|View|實體化的 View，增加查詢效率，以儲存空間為成本|
|Partitino View|View|利用 UNION ALL 將 View 分散在多資料庫、伺服器|
|Simple Select|StoredProcedures|相較於 View 支援 Order By|
|Parametered|StoredProcedures|可以使用參數的方式傳入資料|
|Result|StoredProcedures|可以將結果保存並進行回傳|
|Cross DB|StoredProcedures|需要注意 Permissions 的問題|
|Scalar|Function|可以回傳單一數值，需要注意 Loop 效能問題|
|Table-Valued|Function|可以將結果回傳為 Table，只能單一 Select|
|Multistatements Table-Valued|Function|自行定義空白 Table 並使用多組 Select 填充資料後回傳 Table|
|Update Trigger|Trigger|在資料被更新時觸發，進行資料的修改，可用於修改編輯時間等|
|Instead Of Trigger|Trigger|修改欄位而非刪除資料|
