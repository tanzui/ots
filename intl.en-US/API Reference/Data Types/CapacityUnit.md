# CapacityUnit {#reference288 .reference}

CapacityUnit indicates the value of the capacity units consumed in an operation, or a table’s reserved read/write throughput.

## Data structure {#section_i4f_snq_dfb .section}

```language-protobuf
message CapacityUnit {
    optional int32 read = 1;
    optional int32 write = 2;
}

```

read:

-   Type: int32

-   The read capacity units consumed by this operation or the reserved read throughput for this table.


write:

-   Type: int32

-   The write capacity units consumed by this operation or the reserved write throughput for this table.


## Related operations { .section}

[UpdateRow](reseller.en-US/API Reference/Operations/UpdateRow.md#)

[BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#)

