# GetStreamRecord {#reference788 .reference}

读取当前shard的增量内容。

## 请求结构： { .section}

```language-pb
message GetStreamRecordRequest {
    required string shard_iterator = 1;
    optional int32 limit = 2;
}

```

shard\_iterator:

-   类型：required string

-   当前shard读取的iterator


## 响应消息结构： { .section}

```language-pb
message GetStreamRecordResponse {
    message StreamRecord {
        required ActionType action_type = 1;
        required bytes record = 2;
    }
    repeated StreamRecord stream_records = 1;
    optional raw_string next_shard_iterator = 2;
    optional ConsumedCapacity consumed = 3;
}

```

StreamRecord:

-   类型：repeated StreamRecord

-   读取当前shard记录的record entry


shard\_iterator:

-   类型：required string

-   下次读取此shard的iterator


consumed:

-   类型：[ConsumedCapacity](intl.zh-CN/API 参考/DataType/ConsumedCapacity.md#) 

-   本次操作消耗的服务能力单元。

-   读取Stream数据时CU的计算是根据读取所有行总大小除以4KB向上取整。行的数据大小计算方式请参见[数据存储](../../../../intl.zh-CN/产品定价/数据存储量.md#)。


