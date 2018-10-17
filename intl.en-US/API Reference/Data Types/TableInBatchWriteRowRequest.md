# TableInBatchWriteRowRequest {#reference346 .reference}

TableInBatchWriteRowRequest indicates the request information of the table to be written in the BatchWriteRow operation.

## Data structure { .section}

```language-xml
message TableInBatchWriteRowRequest {
    required string table_name = 1;
    repeated RowInBatchWriteRowRequest rows = 2;
}

```

table\_name:

-   Type: string

-   The name of the table.


rows:

-   Type: repeated [RowInBatchWriteRowRequest](reseller.en-US/API Reference/Data Types/RowInBatchWriteRowRequest.md#) 

-   The information of the row to be inserted, updated, or deleted in the table.


## Related operations { .section}

 [BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#) 

