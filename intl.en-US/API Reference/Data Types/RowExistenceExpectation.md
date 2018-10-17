# RowExistenceExpectation {#reference283 .reference}

RowExistenceExpectations includes conditions for row existence in enumeration type. The conditions include:

-   IGNORE, which indicates that the row existence check is not performed.

-   EXPECT\_EXIST, which indicates that the row is expected to exist.

-   EXPECT\_NOT\_EXIST, which indicates that this row is not expected to exist.


## Enumeration value list {#section_gr2_mvq_dfb .section}

```language-protobuf
enum RowExistenceExpectation {
    IGNORE = 0;
    EXPECT_EXIST = 1;
    EXPECT_NOT_EXIST = 2;
}

```

## Related operations { .section}

 [PutRow](reseller.en-US/API Reference/Operations/PutRow.md#) 

 [UpdateRow](reseller.en-US/API Reference/Operations/UpdateRow.md#) 

 [DeleteRow](reseller.en-US/API Reference/Operations/DeleteRow.md#) 

 [BatchWriteRow](reseller.en-US/API Reference/Operations/BatchWriteRow.md#) 

