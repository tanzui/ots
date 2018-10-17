# Error {#reference309 .reference}

Error can indicate an error message in the response returned when an operation fails, and indicate an error for an individual row request in the BatchGetRow and BatchWriteRow operations.

## Data structure {#section_j5n_mqq_dfb .section}

```language-xml
Error {
    required string code = 1;
    optional string message = 2;
}

```

code:

-   Type: string

-   The error code for the current individual row operation. For more information, see [Error messages]().


message:

-   Type: string

-   The error message for the current individual row operation. For more information, see [Error messages]().


## Related operations { .section}

[BatchGetRow](reseller.en-US/API Reference/Operations/BatchGetRow.md#)

[BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#)

