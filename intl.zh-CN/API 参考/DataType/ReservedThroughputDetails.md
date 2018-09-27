# ReservedThroughputDetails {#reference614 .reference}

表示一个表的预留读/写吞吐量信息。

## 数据结构 {#section_pg1_f5q_dfb .section}

```language-xml
message ReservedThroughputDetails {
    required CapacityUnit capacity_unit = 1;
    required int64 last_increase_time = 2;
    optional int64 last_decrease_time = 3;
    required int32 number_of_decreases_today = 4;
}

```

capacity\_unit:

-   类型：[CapacityUnit](intl.zh-CN/API 参考/DataType/CapacityUnit.md#) 

-   描述：该表的预留读写吞吐量的数值。


last\_increase\_time:

-   类型：int64

-   描述：最近一次上调该表的预留读/写吞吐量设置的时间，使用 UTC 秒数表示。


last\_decrease\_time:

-   类型：int64

-   描述：最近一次下调该表的预留读/写吞吐量设置的时间，使用 UTC 秒数表示。


number\_of\_decreases\_today:

-   类型：int32

-   描述：本个自然日内已下调该表的预留读/写吞吐量设置的次数。


## 相关操作 { .section}

[UpdateTable](intl.zh-CN/API 参考/API 概览/UpdateTable.md#) 

[DescribeTable](intl.zh-CN/API 参考/API 概览/UpdateTable.md#)

