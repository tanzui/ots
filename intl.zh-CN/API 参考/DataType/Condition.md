# Condition {#reference427 .reference}

在 PutRow、UpdateRow 和 DeleteRow 中使用的行判断条件，目前含有 row\_existence 和 column\_condition 两项。

## 数据结构 {#section_t25_tpq_dfb .section}

```language-protobuf
message Condition {
    required RowExistenceExpectation row_existence = 1;
    optional bytes column_condition      = 2;
}

```

row\_existence:

-   类型：[RowExistenceExpectation]() 

-   描述：对该行进行行存在性检查的设置。


column\_condition:

-   类型：bytes

-   描述：对列条件的设置。[Filter]()经过protobuf序列化后的bytes。


## 相关操作 { .section}

[PutRow](intl.zh-CN/API 参考/API 概览/PutRow.md#)

[UpdateRow](intl.zh-CN/API 参考/API 概览/UpdateRow.md#)

[DeleteRow](intl.zh-CN/API 参考/API 概览/DeleteRow.md#)

[BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#)

