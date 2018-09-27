# TableInBatchWriteRowResponse {#reference625 .reference}

在 BatchWriteRow 操作中，表示对一个表进行写入的结果。

## 数据结构 { .section}

```language-xml
message TableInBatchWriteRowResponse {
    required string table_name = 1;
    repeated RowInBatchWriteRowResponse put_rows = 2;
    repeated RowInBatchWriteRowResponse update_rows = 3;
    repeated RowInBatchWriteRowResponse delete_rows = 4;
}

```

table\_name:

-   类型：string

-   描述：该表的表名。


put\_rows:

-   类型：[RowInBatchWriteRowResponse](intl.zh-CN/API 参考/DataType/RowInBatchWriteRowResponse.md#) 

-   描述：该表中 PutRow 操作的结果。


update\_rows:

-   类型：[RowInBatchWriteRowResponse](intl.zh-CN/API 参考/DataType/RowInBatchWriteRowResponse.md#)

-   描述：该表中 UpdateRow 操作的结果。


delete\_rows:

-   类型：[RowInBatchWriteRowResponse](intl.zh-CN/API 参考/DataType/RowInBatchWriteRowResponse.md#) 

-   描述：该表中 DeleteRow 操作的结果。


## 相关操作 { .section}

[BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#)

