# StreamSpecification {#reference310 .reference}

StreamSpecification indicates the stream of a table.

## Data structure {#section_d4f_syq_dfb .section}

```language-xml
message StreamSpecification {
    required bool enable_stream = 1;
    optional int32 expiration_time = 2;
}

```

enable\_stream:

-   Type: Bool

-   Determines whether the stream is enabled for the table.


expiration\_time:

-   Type: int32

-   The expiration time of the stream of the table.


## Related operations { .section}

 [CreateTable](reseller.en-US/API Reference/Operations/CreateTable.md#) 

 [DescribeTable](reseller.en-US/API Reference/Operations/DescribeTable.md#) 

 [UpdateTable](reseller.en-US/API Reference/Operations/UpdateTable.md#) 

