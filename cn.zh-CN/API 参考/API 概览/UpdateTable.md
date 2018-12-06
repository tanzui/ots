# UpdateTable {#reference1270 .reference}

更新指定表的预留读吞吐量或预留写吞吐量设置，新设定将于更新成功一分钟内生效。

## 请求结构： { .section}

```language-pb
message UpdateTableRequest {
    required string table_name = 1;
    optional ReservedThroughput reserved_throughput = 2;
    optional TableOptions table_options = 3;
    optional StreamSpecification stream_spec = 4;
}

```

table\_name:

-   类型：string

-   是否必要参数：是

-   需要更改预留读写吞吐量设置的表的表名。


reserved\_throughput:

-   类型：[ReservedThroughput](intl.zh-CN/API 参考/DataType/ReservedThroughput.md#) 

-   是否必要参数: 是

-   将要更改的表的预留读/写吞吐量设定，该设定将于一分钟后生效。

-   可以只更改表的预留读吞吐量的设置或只更改表的预留写吞吐量的设置，也可以一并更改。

-   capacity\_unit 中 read 和 write 应至少有一个非空，否则请求失败，返回错误。


table\_options:

-   类型：[TableOptions](intl.zh-CN/API 参考/DataType/TableOptions.md#) 

-   是否必要参数：是

-   主要设置TimeToLive和最大版本数。


StreamSpecification

-   类型：[StreamSpecification](intl.zh-CN/API 参考/DataType/StreamSpecification.md#) 

-   是否必要参数：否

-   描述是否打开Stream等Stream相关的属性。


## 响应消息结构： { .section}

```language-pb
message UpdateTableResponse {
    required ReservedThroughputDetails reserved_throughput_details = 1;
    required TableOptions table_options = 2;
}

```

capacity\_unit\_details:

-   类型：[ReservedThroughputDetails](intl.zh-CN/API 参考/DataType/ReservedThroughputDetails.md#) 

-   更新后，该表的预留读/写吞吐量设置信息除了包含当前的预留读/写吞吐量设置值之外，还包含了最近一次更新该表的预留读/写吞吐量设置的时间和当日已下调预留读/写吞吐量的次数。


注意事项：

-   调整每个表预留读/写吞吐量的最小时间间隔为 2 分钟，如果本次 UpdateTable 操作距上次不到 2 分钟将被拒绝。

-   每个自然日（UTC 时间 00:00:00 到第二天的 00:00:00）内每个表上调和下调预留读写吞吐量次数不限。


table\_options:

-   类型：[TableOptions](intl.zh-CN/API 参考/DataType/TableOptions.md#) 

-   修改后，最新的table\_options参数值。


