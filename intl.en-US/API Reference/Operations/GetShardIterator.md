# GetShardIterator {#reference433 .reference}

GetShardIterator obtains the start iterator for reading the record information of the current shard.

## Request structure: { .section}

```language-pb
message GetShardIteratorRequest {
    required string stream_id = 1;
    required string shard_id = 2;
}

```

stream\_id:

-   Type: required string

-   The ID of the current stream.


shard\_id:

-   Type: required string

-   The ID of the current shard.


## Response message structure: { .section}

```language-pb
message GetShardIteratorResponse {
    required string shard_iterator = 1;
}

```

shard\_iterator:

-   Type: required string

-   The start iterator for reading the record of the current shard.


