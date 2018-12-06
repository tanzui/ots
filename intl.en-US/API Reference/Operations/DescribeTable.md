# DescribeTable {#reference897 .reference}

DescribeTable queries the structure information and the reserved read/write throughput value of the specified table.

## Request structure: { .section}

```language-pb
message DescribeTableRequest {
    required string table_name = 1;
}

```

table\_name:

-   Type: String

-   Required parameter: Yes

-   The name of the table to be queried.


## Response message structure: { .section}

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

-   Type: [TableMeta](reseller.en-US/API Reference/Data Types/TableMeta.md#) 

-   The table’s schema, which is the same as the Schema given at the time of table creation.


reserved\_throughput\_details:

-   Type: [ReservedThroughputDetails](reseller.en-US/API Reference/Data Types/ReservedThroughputDetails.md#) 

-   The table’s reserved read/write throughput settings information. In addition to the current reserved read/write throughput settings, it also contains the time these settings were last updated and the number of times they have been lowered for the current date.


table\_options:

-   Type: [TableOptions](reseller.en-US/API Reference/Data Types/TableOptions.md#) 

-   The latest value of the table\_options parameter.


StreamSpecification:

-   Type: [StreamSpecification](reseller.en-US/API Reference/Data Types/StreamSpecification.md#)

-   Required parameter: No

-   Specifies whether to enable Stream-related attributes.


shard\_splits:

-   Type: bytes

-   The split points of all shards in the current table.


