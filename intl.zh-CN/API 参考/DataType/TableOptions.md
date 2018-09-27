# TableOptions {#reference543 .reference}

表的参数值，包括TimeToLive，最大版本数等。

## 数据结构 { .section}

```language-protobuf
message TableOptions {
    optional int32 time_to_live = 1; // 可以动态更改
    optional int32 max_versions = 2; // 可以动态更改
    optional int64 deviation_cell_version_in_sec = 5; // 可以动态修改
}

```

time\_to\_live:

-   类型：int32

-   描述：本张表中保存的数据的存活时间，单位秒。


max\_versions:

-   类型：int32

-   描述：本张表保留的最大版本数。


deviation\_cell\_version\_in\_sec:

-   类型：int64

-   描述：最大版本偏差。目的主要是为了禁止写入与预期较大的数据，比如设置deviation\_cell\_version\_in\_sec为1000，当前timestamp如果为10000，那么允许写入的timestamp范围为\[10000 - 1000, 10000 + 1000\]。


