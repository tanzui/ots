# Condition {#reference427 .reference}

Condition specifies the row judgment conditions in PutRow, UpdateRow, and DeleteRow. It currently contains row\_existence and column\_condition.

## Data structure {#section_t25_tpq_dfb .section}

```language-protobuf
message Condition {
    required RowExistenceExpectation row_existence = 1;
    optional bytes column_condition      = 2;
}

```

row\_existence:

-   Type: [RowExistenceExpectation]() 

-   The row existence check settings for this row.


column\_condition:

-   Type: Bytes

-   The column condition settings. It indicates the bytes after the [Filter]() is serialized in Protobuf format.


## Related operations { .section}

[PutRow](reseller.en-US/API Reference/Operations/PutRow.md#)

[UpdateRow](reseller.en-US/API Reference/Operations/UpdateRow.md#)

[DeleteRow](reseller.en-US/API Reference/Operations/DeleteRow.md#)

[BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#)

