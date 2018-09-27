# RowInBatchWriteRowRequest {#reference618 .reference}

在 BatchWriteRow 操作中，表示要插入、更新和删除的一行信息。

## 数据结构 {#section_pjz_jwq_dfb .section}

```language-xml
message RowInBatchWriteRowRequest {
    required OperationType type = 1;
    required bytes row_change = 2; // encoded as InplaceRowChangeSet
    required Condition condition = 3;
    optional ReturnContent return_content = 4;
} 

```

type:

-   类型：[OperationType](intl.zh-CN/API 参考/DataType/OperationType.md#) 

-   描述：操作类型。


row\_change:

-   类型：bytes

-   描述：行数据，包括主键和属性列，由Plainbuffer编码，详见[Plainbuffer](intl.zh-CN/API 参考/DataType/PlainBuffer.md#)编码。


condition:

-   类型：[Condition](intl.zh-CN/API 参考/DataType/Condition.md#) 

-   描述：条件更新的值，包括行条件检测和属性列检测。


return\_content:

-   类型：[ReturnContent](intl.zh-CN/API 参考/DataType/ReturnContent.md#) 

-   是否必要参数：否

-   写入成功后返回的数据类型，目前仅支持返回主键，主要用于主键列自增功能中。


## 相关操作 { .section}

 [BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#) 

