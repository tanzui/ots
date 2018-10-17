# GetStreamRecord {#reference788 .reference}

GetStreamRecord obtains the incremental content of the current shard.

## Request structure: { .section}

```language-pb
message GetStreamRecordRequest {
    required string shard_iterator = 1;
    optional int32 limit = 2;
}

```

shard\_iterator:

-   Type: required string

-   The iterator for reading the current shard.


## Response message structure: { .section}

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

-   Type: repeated StreamRecord

-   The record entry for reading the current shard.


shard\_iterator:

-   Type: required string

-   The iterator for reading the current shard in the next operation.


consumed:

-   Type: [ConsumedCapacity](reseller.en-US/API Reference/Data Types/ConsumedCapacity.md#) 

-   The value of capacity units consumed by this operation.

-   The sum of data in all rows of a table is divided by 4 KB, and then rounded up. Stream read CUs are equivalent to the rounded up value. For more information about how to calculate a row’s data volume, see [Data storage](../../../../reseller.en-US/Pricing/Data storage.md#).


