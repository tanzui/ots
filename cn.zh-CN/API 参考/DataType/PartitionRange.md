# PartitionRange {#reference348 .reference}

分区的范围信息。

```language-protobuf
message PartitionRange {
    required bytes begin = 1; // encoded as SQLVariant
    required bytes end = 2; // encoded as SQLVariant
}

```

begin:

-   类型：bytes

-   描述：分区的起始键，分区的区间为前闭后开，包含起始键。Plainbuffer格式，编码详见[PlainBuffer](intl.zh-CN/API 参考/DataType/PlainBuffer.md#)。


end:

-   类型：bytes

-   描述：分区的结束键，分区的区间为前闭后开，不包含结束键。Plainbuffer格式，编码详见[PlainBuffer](intl.zh-CN/API 参考/DataType/PlainBuffer.md#)。


