# DescribeTable {#reference897 .reference}

查询指定表的结构信息和预留读/写吞吐量设置信息。

## 请求结构： { .section}

```language-pb
message DescribeTableRequest {
    required string table_name = 1;
}

```

table\_name:

-   类型：string

-   是否必要参数：是

-   需要查询的表名。


## 响应消息结构： { .section}

```language-pb
message DescribeTableResponse {
    required TableMeta table_meta = 1;
    required ReservedThroughputDetails reserved_throughput_details = 2;
    required TableOptions table_options = 3;
    optional StreamDetails stream_details = 5;
    repeated bytes shard_splits = 6;
}

```

table\_meta:

-   类型: [TableMeta](cn.zh-CN/API 参考/DataType/TableMeta.md#) 

-   该表的Schema，与建表时给出的Schema相同。


reserved\_throughput\_details:

-   类型：[ReservedThroughputDetails](cn.zh-CN/API 参考/DataType/ReservedThroughputDetails.md#) 

-   该表的预留读/写吞吐设置信息除了包含当前的预留读/写吞吐设置值之外，还包含了最近一次更新该表的预留读/写吞吐设置的时间和当日已下调预留读/写吞吐的次数。


table\_options:

-   类型：[TableOptions](cn.zh-CN/API 参考/DataType/TableOptions.md#) 

-   当前最新的table\_options参数值。


StreamSpecification:

-   类型:[StreamSpecification](cn.zh-CN/API 参考/DataType/StreamSpecification.md#)

-   是否必要参数：否

-   描述是否打开Stream相关的属性。


shard\_splits:

-   类型：bytes

-   当前表所有分区的分裂点。


