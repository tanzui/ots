# CreateTable {#reference997 .reference}

根据给定的表结构信息创建相应的表。

## 请求结构： { .section}

```language-pb
message CreateTableRequest {
    required TableMeta table_meta = 1;
    required ReservedThroughput reserved_throughput = 2; 
    optional TableOptions table_options = 3;
    optional StreamSpecification stream_spec = 5;
}

```

table\_meta:

-   类型：[TableMeta](intl.zh-CN/API 参考/DataType/TableMeta.md#) 

-   是否必要参数：是

-   将要创建的表的结构信息，其中 table\_name 应在本实例范围内唯一；primary\_key 的中 ColumnSchema 的个数应在 1~4 个范围内；primary\_key 中的 ColumnSchema 的 name 应符合[表名命名规范](../../../../intl.zh-CN/产品功能/表格存储的表.md#section_wml_skg_cfb)，type 取值只能为 STRING， INTEGER或BINARY。

-   建表成功后，表的 Schema 将不能修改。


reserved\_throughput:

-   类型：[ReservedThroughput](intl.zh-CN/API 参考/DataType/ReservedThroughput.md#)

-   是否必要参数：是

-   将要创建的表的初始预留读/写吞吐量设定，任何表的预留读吞吐量与预留写吞吐量均不能超过 5000。

-   表的预留读/写吞吐量设定可以通过 UpdateTable 进行动态更改。


table\_options:

-   类型：[TableOptions](intl.zh-CN/API 参考/DataType/TableOptions.md#) 

-   是否必要参数：是

-   主要设置TimeToLive和最大版本数。


stream\_spec:

-   类型: [StreamSpecification](intl.zh-CN/API 参考/DataType/StreamSpecification.md#) 

-   是否必要参数：否

-   描述是否打开Stream相关的属性。


## 响应消息结构： { .section}

```language-pb
message CreateTableResponse {
}

```

## 注意事项： { .section}

-   创建成功的表并不能立刻提供读写服务。一般来讲，在建表成功后一分钟左右，即可对新创建的表进行读写操作。

-   单个实例下不能超过 64 个表，如果需要提高单实例下表数目的上限，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。


