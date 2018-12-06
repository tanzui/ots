# TableInBatchGetRowRequest {#reference1854 .reference}

在 BatchGetRow 操作中，表示要读取的一个表的请求信息。

## 数据结构 { .section}

```language-protobuf
message TableInBatchGetRowRequest {
    required string table_name = 1;
    repeated bytes primary_key = 2; //Plainbuffer编码
    repeated bytes token = 3;
    repeated string columns_to_get = 4;  // 不指定则读出所有的列
    optional TimeRange time_range = 5;
    optional int32 max_versions = 6;
    optional bool cache_blocks = 7 [default = true]; // 本次读出的数据是否进入BlockCache
    optional bytes filter = 8;
    optional string start_column = 9;
    optional string end_column = 10;
}


```

table\_name:

-   类型：string

-   描述：该表的表名。


primary\_key:

-   类型：repeated bytes

-   是否必要参数：是

-   该行全部的主键列，包含主键名和主键值，由Plainbuffer编码，详见[Plainbuffer](intl.zh-CN/API 参考/DataType/PlainBuffer.md#)编码。


token:

-   类型：repeated bytes

-   是否必要参数：否

-   宽行读取时指定下一次读取的起始位置，暂不可用。


columns\_to\_get:

-   类型：repeated string

-   描述：该表中需要返回的全部列的列名。


time\_range:

-   类型：[TimeRange](intl.zh-CN/API 参考/DataType/TimeRange.md#) 

-   是否必要参数：和max\_versions必须至少存在一个。

-   读取数据的版本时间戳范围。

-   时间戳的单位是毫秒，取值最小值为0，最大值为INT64.MAX。

-   若要查询一个范围，则指定start\_time和end\_time。

-   若要查询一个特定时间戳，则指定specific\_time。

-   例子：如果指定的time\_range为\(100, 200\)，则返回的列数据的时间戳必须位于\[100, 200\)范围内，前闭后开区间。


max\_versions:

-   类型：int32

-   是否必要参数：和time\_range必须至少存在一个。

-   读取数据时，返回的最多版本个数。

-   例子：如果指定max\_versions为2，则每一列最多返回2个版本的数据。


cache\_blocks:

-   类型：bool

-   是否必要参数：否

-   本次读出的数据是否进入BlockCache。

-   默认值：true

-   当前暂不支持设置为false。


filter:

-   类型：bytes

-   是否必要参数：否

-   过滤条件表达式。

-   [Filter](intl.zh-CN/API 参考/DataType/Filter.md#)经过protobuf序列化后的二进制数据。


start\_column:

-   类型：string

-   是否必要参数：否

-   指定读取时的起始列，主要用于宽行读。

-   返回的结果中包含当前起始列。

-   列的顺序按照列名的字典序排序。

-   例子：如果一张表有"a"，"b"，"c"三列，读取时指定start\_column为“b”，则会从"b"列开始读，返回"b"，"c"两列。


end\_column:

-   类型：string

-   是否必要参数：否

-   指定读取时的结束列，主要用于宽行读。

-   返回的结果中不包含当前结束列。

-   列的顺序按照列名的字典序排序。

-   例子：如果一张表有"a"，"b"，"c"三列，读取时指定end\_column为“b”，则读到"b"列时会结束，返回"a"列。


## 相关操作 { .section}

[BatchGetRow](intl.zh-CN/API 参考/API 概览/BatchGetRow.md#)

