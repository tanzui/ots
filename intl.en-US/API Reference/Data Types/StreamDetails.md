# StreamDetails {#reference498 .reference}

StreamDetails indicates the stream of a table.

## Data structure {#section_agf_1yq_dfb .section}

```language-xml
message StreamDetails {
    required bool enable_stream = 1;
    optional string stream_id = 2;
    optional int32 expiration_time = 3;
    optional int64 last_enable_time = 4;
}

```

enable\_stream:

-   Type: Required bool

-   Determines whether the stream is enabled for the table.


stream\_id:

-   Type: Optional string

-   The ID of the stream of the table.


expiration\_time:

-   Type: Optional int32

-   The expiration time of the stream of the table.


last\_enable\_time:

-   Type: Optional int64

-   The time for enabling the stream of the table.


## Related operations { .section}

 [DescribeTable](reseller.en-US/API Reference/Operations/DescribeTable.md#) 

