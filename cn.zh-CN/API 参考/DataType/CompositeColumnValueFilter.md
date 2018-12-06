# CompositeColumnValueFilter {#reference427 .reference}

多个组合条件，比如 column\_a \> 5 AND column\_b = 10 等。适用于 ConditionUpdate 和 Filter 功能。

## 数据结构 {#section_a3v_1pq_dfb .section}

```language-protobuf
message CompositeColumnValueFilter {
    required LogicalOperator combinator = 1;
    repeated Filter sub_filters = 2;
}

```

combinator:

-   类型：[LogicalOperator](intl.zh-CN/API 参考/DataType/LogicalOperator.md#) 

-   描述：逻辑操作符。


sub\_filter:

-   类型：[Filter](intl.zh-CN/API 参考/DataType/Filter.md#)

-   描述：子条件表达式。


## 相关操作 { .section}

-   ConditionUpdate

     [PutRow](intl.zh-CN/API 参考/API 概览/PutRow.md#) 

     [UpdateRow](intl.zh-CN/API 参考/API 概览/UpdateRow.md#) 

     [DeleteRow](intl.zh-CN/API 参考/API 概览/DeleteRow.md#)

     [BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#) 

-   Filter

     [GetRow](intl.zh-CN/API 参考/API 概览/GetRow.md#) 

     [GetRange](intl.zh-CN/API 参考/API 概览/GetRange.md#) 

     [BatchGetRow](intl.zh-CN/API 参考/API 概览/BatchGetRow.md#)


