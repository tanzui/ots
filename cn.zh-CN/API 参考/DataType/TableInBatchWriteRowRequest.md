# TableInBatchWriteRowRequest {#reference346 .reference}

在 BatchWriteRow 操作中，表示要写入的一个表的请求信息。

## 数据结构 { .section}

```language-xml
message TableInBatchWriteRowRequest {
    required string table_name = 1;
    repeated RowInBatchWriteRowRequest rows = 2;
}

```

table\_name:

-   类型：string

-   描述：该表的表名。


rows:

-   类型：repeated [RowInBatchWriteRowRequest](intl.zh-CN/API 参考/DataType/RowInBatchWriteRowRequest.md#) 

-   描述：该表中请求插入、更新和删除的行信息。


## 相关操作 { .section}

 [BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#) 

