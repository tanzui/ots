# ReservedThroughput {#reference229 .reference}

表示一个表设置的预留读/写吞吐量数值。

## 数据结构 {#section_g5f_stq_dfb .section}

```language-xml
message ReservedThroughput {
    required CapacityUnit capacity_unit = 1;
}

```

capacity\_unit:

-   类型：[CapacityUnit](intl.zh-CN/API 参考/DataType/CapacityUnit.md#)

-   描述：表当前的预留读/写吞吐量数值。


## 相关操作 { .section}

[CreateTable](intl.zh-CN/API 参考/API 概览/CreateTable.md#)

[UpdateRow](intl.zh-CN/API 参考/API 概览/UpdateRow.md#)

 [DescribeTable](intl.zh-CN/API 参考/API 概览/DescribeTable.md#) 

