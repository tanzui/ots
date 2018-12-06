# Filter {#reference427 .reference}

列判断条件，适用于条件更新（Conditional Update）和过滤器（Filter）功能。

## 数据结构 {#section_s3n_brq_dfb .section}

```language-protobuf
message Filter {
    required FilterType type = 1;
    required bytes filter = 2;  
}

```

type:

-   类型：[FilterType](intl.zh-CN/API 参考/DataType/FilterType.md#) 

-   描述：列条件类型。


filter:

-   类型：bytes

-   描述：[CompositeColumnValueFilter](intl.zh-CN/API 参考/DataType/CompositeColumnValueFilter.md#) 、[ColumnPaginationFilter](intl.zh-CN/API 参考/DataType/ColumnPaginationFilter.md#)或者[SingleColumnValueFilter](intl.zh-CN/API 参考/DataType/SingleColumnValueFilter.md#)类型的条件语句通过 Protobuf 序列化后的二进制数据。


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


