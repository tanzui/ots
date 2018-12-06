# StreamDetails {#reference498 .reference}

表示一个表的stream信息。

## 数据结构 {#section_agf_1yq_dfb .section}

```language-xml
message StreamDetails {
    required bool enable_stream = 1;
    optional string stream_id = 2;
    optional int32 expiration_time = 3;
    optional int64 last_enable_time = 4;
}

```

enable\_stream:

-   类型：required bool

-   描述：该表是否打开stream


stream\_id:

-   类型：optional string

-   描述：该表的stream的id


expiration\_time:

-   类型：optional int32

-   描述：该表的stream的过期时间


last\_enable\_time:

-   类型：optional int64

-   描述：该stream的打开的时间


## 相关操作 { .section}

 [DescribeTable](intl.zh-CN/API 参考/API 概览/DescribeTable.md#) 

