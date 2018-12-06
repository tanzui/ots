# ActionType {#reference260 .reference}

ActionType specifies the operation type in the response message of the GetStreamRecord operation. Operation types include:

-   PUT\_ROW, which indicates that the operation type is PutRow.

-   UPDATE\_ROW, which indicates that the operation type is UpdateRow.

-   DELETE\_ROW, which indicates that the operation type is DeleteRow.


## Enumeration value list {#section_z4g_knq_dfb .section}

```language-xml
enum ActionType {
    PUT_ROW = 1;
    UPDATE_ROW = 2;
    DELETE_ROW = 3;
}

```

## Related operations { .section}

[GetStreamRecord](reseller.en-US/API Reference/Operations/GetStreamRecord.md#)

