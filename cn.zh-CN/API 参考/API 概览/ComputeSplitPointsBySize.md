# ComputeSplitPointsBySize {#reference2074 .reference}

将全表的数据在逻辑上划分成接近指定大小的若干分片，返回这些分片之间的分割点以及分片所在机器的提示。一般用于计算引擎规划并发度等执行计划。

## 请求结构 { .section}

```
message ComputeSplitPointsBySizeRequest {
    required string table_name = 1;
    required int64 split_size = 2; // in 100MB
}

```

table\_name：

-   类型：string。
-   是否必要参数：是
-   要切分的数据所在的表名。

split\_size:

-   类型：int64
-   是否必要参数：是
-   每个分片的近似大小，以百兆为单位。

## 响应消息结构 { .section}

```
message ComputeSplitPointsBySizeResponse {
    required ConsumedCapacity consumed = 1;
    repeated PrimaryKeySchema schema = 2;

    /**
     * Split points between splits, in the increasing order
     *
     * A split is a consecutive range of primary keys,
     * whose data size is about split_size specified in the request.
     * The size could be hard to be precise.
     *
     * A split point is an array of primary-key column w.r.t. table schema,
     * which is never longer than that of table schema.
     * Tailing -inf will be omitted to reduce transmission payloads.
     */
    repeated bytes split_points = 3;

    /**
     * Locations where splits lies in.
     *
     * By the managed nature of TableStore, these locations are no more than hints.
     * If a location is not suitable to be seen, an empty string will be placed.
     */
     message SplitLocation {
         required string location = 1;
         required sint64 repeat = 2;
     }
     repeated SplitLocation locations = 4;
}

```

consumed:

-   类型：ConsumedCapacity
-   本次请求消耗的服务能力单元。

schema:

-   类型：PrimaryKeySchema
-   该表的 Schema，与建表时给出的 Schema 相同。

split\_points:

-   类型：repeated bytes
-   分片之间的分割点。分割点之间保证单调增。每个分割点都是以[Plainbuffer](intl.zh-CN/API 参考/DataType/PlainBuffer.md#)编码的行，并且只有primary-key项。为了减少传输的数据量，分割点最后的-inf不会传输。

locations:

-   类型：repeated SplitLocation
-   分割点所在机器的提示。可以为空。

举个例子，如果有一张表有三列主键，其中首列主键类型为string。调用这个API后得到5个分片，分别为`(-inf,-inf,-inf)`到`("a",-inf,-inf)`、`("a",-inf,-inf)`到`("b",-inf,-inf)`、`("b",-inf,-inf)`到`("c",-inf,-inf)`、`("c",-inf,-inf)`到`("d",-inf,-inf)`和`("d",-inf,-inf)`到`(+inf,+inf,+inf)`。前三个落在"machine-A"，后两个落在"machine-B"。那么，split\_points为（示意）`[("a"),("b"),("c"),("d")]`，而locations为（示意）`"machine-A"*3, "machine-B"*2`。

## 服务能力单元消耗 { .section}

消耗的读服务能力单元数量与分片的数量相同。不消耗写服务能力单元。

