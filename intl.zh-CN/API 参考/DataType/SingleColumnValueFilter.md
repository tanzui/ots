# SingleColumnValueFilter {#reference836 .reference}

单个条件，比如 column\_a \> 5 等。适用于 ConditionUpdate 和 Filter 功能。

## 数据结构 {#section_qcg_nxq_dfb .section}

```language-protobuf
message SingleColumnValueFilter {
    required ComparatorType comparator = 1;
    required string column_name = 2;
    required bytes column_value = 3;
    required bool filter_if_missing = 4;
    required bool latest_version_only = 5; 
}
 

```

comparator:

-   类型：[ComparatorType](intl.zh-CN/API 参考/DataType/ComparatorType.md#) 

-   描述：比较类型。


column\_name:

-   类型：string

-   描述：列名称。


column\_value:

-   类型：bytes

-   描述：列值经过[Plainbuffer](intl.zh-CN/API 参考/DataType/PlainBuffer.md#)编码后的值。


filter\_if\_missing:

-   类型：bool

-   描述：当某行的这一列不存在时，设置条件是否过滤。比如条件是 column\_a\>0，filter\_if\_missing 是 true，当某一行没有列 column\_a 时，这一行的条件判断就会通过。


latest\_version\_only:

-   类型：bool

-   描述：是否只对最新版本有效。如果为true，则表示只检测最新版本的值是否满足条件；如果是false，则会检测所有版本的值是否满足条件。


## 相关操作 {#section_zbd_mxq_dfb .section}

-   ConditionUpdate

     [PutRow](intl.zh-CN/API 参考/API 概览/PutRow.md#) 

     [UpdateRow](intl.zh-CN/API 参考/API 概览/UpdateRow.md#) 

     [DeleteRow](intl.zh-CN/API 参考/API 概览/DeleteRow.md#)

     [BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#) 

-   Filter

     [GetRow](intl.zh-CN/API 参考/API 概览/GetRow.md#) 

     [GetRange](intl.zh-CN/API 参考/API 概览/GetRange.md#) 

     [BatchGetRow](intl.zh-CN/API 参考/API 概览/BatchGetRow.md#)


