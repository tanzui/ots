# RowInBatchWriteRowResponse {#reference446 .reference}

RowInBatchWriteRowResponse indicates the write operation result for a row in the response message of the BatchWriteRow operation.

## Data structure {#section_rds_zwq_dfb .section}

```language-xml
message RowInBatchWriteRowResponse {
    required bool is_ok = 1 [default = true];
    optional Error error = 2;
    optional ConsumedCapacity consumed = 3;
}

```

is\_ok:

-   Type: Bool

-   Determines whether the operation for this row is successful. If the value is true, the row is written successfully and error is invalid. If the value is false, the row fails to be written.


error:

-   Type: [Error](reseller.en-US/API Reference/Data Types/Error.md#)

-   The error message for this row operation.


consumed:

-   Type: [ConsumedCapacity](reseller.en-US/API Reference/Data Types/ConsumedCapacity.md#)

-   The capacity units consumed by this row operation.


## Related operations { .section}

 [BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#) 

