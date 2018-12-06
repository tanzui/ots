# TableInBatchGetRowResponse {#reference332 .reference}

在 BatchGetRow 操作的返回消息中，表示一个表的数据。

## 数据结构 { .section}

```language-xml
message TableInBatchGetRowResponse {
    required string table_name = 1;
    repeated RowInBatchGetRowResponse rows = 2;
}

```

table\_name:

-   类型：string

-   描述：该表的表名。


rows:

-   类型：repeated [RowInBatchGetRowResponse](intl.zh-CN/API 参考/DataType/RowInBatchGetRowResponse.md#) 

-   描述：该表中读取到的全部行数据。


## 相关操作 { .section}

 [BatchGetRow](intl.zh-CN/API 参考/API 概览/BatchGetRow.md#) 

