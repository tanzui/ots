# CapacityUnit {#reference288 .reference}

表示一次操作消耗服务能力单元的值或是一个表的预留读/写吞吐量的值。

## 数据结构 {#section_i4f_snq_dfb .section}

```language-protobuf
message CapacityUnit {
    optional int32 read = 1;
    optional int32 write = 2;
}

```

read:

-   类型：int32

-   描述：本次操作消耗的读服务能力单元或该表的预留读吞吐量。


write:

-   类型：int32

-   描述：本次操作消耗的写服务能力单元或该表的预留写吞吐量。


## 相关操作 { .section}

[UpdateRow](intl.zh-CN/API 参考/API 概览/UpdateRow.md#)

[BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#)

