# RowInBatchWriteRowRequest {#reference618 .reference}

RowInBatchWriteRowRequest indicates the information of the row to be written, updated, or deleted in the BatchWriteRow operation.

## Data structure {#section_pjz_jwq_dfb .section}

```language-xml
message RowInBatchWriteRowRequest {
    required OperationType type = 1;
    required bytes row_change = 2; // encoded as InplaceRowChangeSet
    required Condition condition = 3;
    optional ReturnContent return_content = 4;
} 

```

type:

-   Type: [OperationType](reseller.en-US/API Reference/Data Types/OperationType.md#) 

-   The operation type.


row\_change:

-   Type: Bytes

-   The row data, which includes the primary key columns and attribute columns. The columns are encoded in Plainbuffer format. For more information, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


condition:

-   Type: [Condition](reseller.en-US/API Reference/Data Types/Condition.md#) 

-   The value of conditional update, including the row existence condition and column-based condition.


return\_content:

-   Type: [ReturnContent](reseller.en-US/API Reference/Data Types/ReturnContent.md#) 

-   Required parameter: Yes

-   The data type after the row is successfully written. Currently, only the primary key can be returned, which is used for the auto-increment function of the primary key column.


## Related operations { .section}

 [BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#) 

