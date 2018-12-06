# TableInBatchWriteRowResponse {#reference625 .reference}

TableInBatchWriteRowResponse indicates the write results for a table in the BatchWriteRow operation.

## Data structure { .section}

```language-xml
message TableInBatchWriteRowResponse {
    required string table_name = 1;
    repeated RowInBatchWriteRowResponse put_rows = 2;
    repeated RowInBatchWriteRowResponse update_rows = 3;
    repeated RowInBatchWriteRowResponse delete_rows = 4;
}

```

table\_name:

-   Type: string

-   The name of the table.


put\_rows:

-   Type: [RowInBatchWriteRowResponse](reseller.en-US/API Reference/Data Types/RowInBatchWriteRowResponse.md#) 

-   The results of the PutRow operation for the table.


update\_rows:

-   Type: [RowInBatchWriteRowResponse](reseller.en-US/API Reference/Data Types/RowInBatchWriteRowResponse.md#)

-   The results of the UpdateRow operation for the table.


delete\_rows:

-   Type: [RowInBatchWriteRowResponse](reseller.en-US/API Reference/Data Types/RowInBatchWriteRowResponse.md#) 

-   The results of the DeleteRow operation for the table.


## Related operations { .section}

[BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#)

