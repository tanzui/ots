# ListStream {#reference555 .reference}

获取当前实例下所有表的stream信息。

## 请求结构： { .section}

```language-pb
message ListStreamRequest {
	optional string table_name = 1;
}

```

table\_name:

-   类型：optional string

-   当前stream所属的表名


## 响应消息结构： { .section}

```language-pb
message ListStreamResponse {
    repeated Stream streams = 1;
}

message Stream {
    required string stream_id = 1;
    required string table_name = 2;
    required int64 creation_time = 3;
}

```

stream\_id:

-   类型：required string

-   当前stream的id


table\_name:

-   类型：required string

-   当前stream所属的表名


creation\_time:

-   类型：required int64

-   当前stream enable的时间


