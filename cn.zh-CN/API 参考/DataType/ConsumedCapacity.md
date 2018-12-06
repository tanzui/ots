# ConsumedCapacity {#reference186 .reference}

表示一次操作消耗的服务能力单元。

## 数据结构 {#section_sxm_cqq_dfb .section}

```language-xml
message ConsumedCapacity {
    required CapacityUnit capacity_unit = 1;
}

```

capacity\_unit:

-   类型：[CapacityUnit](intl.zh-CN/API 参考/DataType/CapacityUnit.md#) 

-   描述：本次操作消耗的服务能力单元的值。


