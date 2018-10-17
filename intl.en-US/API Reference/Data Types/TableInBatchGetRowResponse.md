# TableInBatchGetRowResponse {#reference332 .reference}

TableInBatchGetRowResponse indicates data in a table in the messages returned by the BatchGetRow operation.

## Data structure { .section}

```language-xml
message TableInBatchGetRowResponse {
    required string table_name = 1;
    repeated RowInBatchGetRowResponse rows = 2;
}

```

table\_name:

-   Type: string

-   The name of the table.


rows:

-   Type: repeated [RowInBatchGetRowResponse](reseller.en-US/API Reference/Data Types/RowInBatchGetRowResponse.md#) 

-   All row data read in the table.


## Related operations { .section}

 [BatchGetRow](reseller.en-US/API Reference/Operations/BatchGetRow.md#) 

