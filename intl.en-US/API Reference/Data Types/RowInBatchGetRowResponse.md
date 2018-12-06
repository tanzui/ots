# RowInBatchGetRowResponse {#reference649 .reference}

RowInBatchGetRowResponse indicates a data row in the response message of the BatchGetRow operation.

## Data structure {#section_jb1_tvq_dfb .section}

```language-xml
message RowInBatchGetRowResponse {
    required bool is_ok = 1 [default = true];
    optional Error error = 2;
    optional ConsumedCapacity consumed = 3;
    optional bytes row = 4; 
	optional bytes next_token = 5;
}

```

is\_ok:

-   Type: Bool

-   Determines whether the operation for this row is successful. If the value is true, the row is read successfully and error is invalid. If the value is false, this row fails to be read and row is invalid.


error:

-   Type: [Error](reseller.en-US/API Reference/Data Types/Error.md#)

-   The error message for this row operation.


consumed:

-   Type: [ConsumedCapacity](reseller.en-US/API Reference/Data Types/ConsumedCapacity.md#) 

-   The capacity units consumed by this row operation.


row:

-   Type: Bytes

-   The read data is encoded in Plainbuffer format. For more information, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).

-   If this row does not exist, null is returned.


next\_token:

-   Type: Bytes

-   It indicates the starting position of a wide row to be read during the next operation of which is unavailable currently.


## Related operations { .section}

 [BatchGetRow](reseller.en-US/API Reference/Operations/BatchGetRow.md#) 

