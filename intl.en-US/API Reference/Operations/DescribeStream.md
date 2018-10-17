# DescribeStream {#reference1406 .reference}

DescribeStream obtains information about the shards of the current stream.

## Request structure: { .section}

```language-pb
message DescribeStreamRequest {
    required string stream_id = 1;
    optional string inclusive_start_shard_id = 2;
    optional int32 shard_limit = 3;
}

```

stream\_id:

-   Type: required string

-   The ID of the current stream.


inclusive\_start\_shard\_id:

-   Type: required string

-   The ID of the start shard in a query.


shard\_limit:

-   Type: required string

-   The upper limit of the returned shard quantity in a single query.


## Response message structure: { .section}

```language-pb
message DescribeStreamResponse {
    required string stream_id = 1;
    required int32 expiration_time = 2;
    required string table_name = 3;
    required int64 creation_time = 4;
    required StreamStatus stream_status = 5;
    repeated StreamShard shards = 6;
    optional string next_shard_id = 7;
}

message StreamShard {
    required string shard_id = 1;
    optional string parent_id = 2;
    optional string parent_sibling_id = 3;
}

```

stream\_id:

-   Type: required string

-   The ID of the current stream.


expiration\_time:

-   Type: required int32

-   The expiration time of the stream.


table\_name:

-   Type: required string

-   The name of the table for which the current stream is enabled.


creation\_time:

-   Type: required int32

-   The time when the current stream is enabled.


stream\_status:

-   Type: required StreamStatus

-   The status of the current stream, which can be enabling or active.


shards:

-   Type: required StreamShard

-   The information about the streamShard, including the shard ID, parent shard ID, and information about the neighbor shard of the parent shard \(applicable when the parent shard is merged\).


next\_shard\_id:

-   Type: optional string

-   The start ID of the next shard in a paging query.


Note:

Data reading for the parent shard must be completed before data of the current shard is read.

