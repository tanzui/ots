# RowInBatchWriteRowResponse {#reference446 .reference}

在 BatchWriteRow 操作的返回消息中，表示一行写入操作的结果。

## 数据结构 {#section_rds_zwq_dfb .section}

```language-xml
message RowInBatchWriteRowResponse {
    required bool is_ok = 1 [default = true];
    optional Error error = 2;
    optional ConsumedCapacity consumed = 3;
}

```

is\_ok:

-   类型：bool

-   描述：该行操作是否成功。若为 true，则该行写入成功，error 无效；若为 false，则该行写入失败。


error:

-   类型：[Error](intl.zh-CN/API 参考/DataType/Error.md#)

-   描述：该行操作的错误信息。


consumed:

-   类型：[ConsumedCapacity](intl.zh-CN/API 参考/DataType/ConsumedCapacity.md#)

-   描述：该行操作消耗的服务能力单元。


## 相关操作 { .section}

 [BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#) 

