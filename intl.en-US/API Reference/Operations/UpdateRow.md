# UpdateRow {#reference2046 .reference}

UpdateRow updates the data of the specified row. If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.

## Request message structure: { .section}

```language-protobuf
message UpdateRowRequest {
    required string table_name = 1;
    required bytes row_change = 2;
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}

```

table\_name:

-   Type: String

-   Required parameter: Yes

-   The name of the table whose data is to be updated.


row\_change:

-   Type: Bytes

-   Required parameter: Yes

-   The data to be updated. It is in Plainbuffer format and includes the primary key and attribute column. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).

-   All the attribute columns to be updated for this row. Table Store adds, modifies, or deletes the values for the specified columns based on the content of each UpdateType in row\_change.

-   Columns that exist in the row but does not exist in row\_change are not affected.

-   UpdateType can have the following values:

    -   PUT: The UpdateType value must be a valid attribute column value. This indicates that, if this column does not exist, a new one is added. If the column exists, it is overwritten.

    -   DELETE: The UpdateType value must be null, while the timestamp must be specified. This indicates that, data of the specified version in the column is deleted.

    -   DELETE\_ALL: The UpdateType value and time stamp must be null. This indicates that, data of all versions in the column is deleted.


**Note:** Deleting all attribute columns of a row is not the same as deleting the row. If you want to delete the row, use the DeleteRow operation.

condition:

-   Type: [Condition](reseller.en-US/API Reference/Data Types/Condition.md#) 

-   Required parameter: Yes

-   Determines whether to perform existence check before data updating. It can be one of two values:

    -   IGNORE indicates that the row existence check is not performed.

    -   EXPECT\_EXIST indicates that the row is expected to exist.

-   If this row is expected to exist but does not, the update operation fails and an error is returned. If the existence of the row is ignored, this operation is successful regardless of the row existence.


return\_content:

-   Type: [ReturnContent](reseller.en-US/API Reference/Data Types/ReturnContent.md#) 

-   Required parameter: No

-   The data type after the row is successfully written. Currently, only the primary key can be returned, which is used for the auto-increment function of the primary key column.


## Response message structure: { .section}

```language-protobuf
message UpdateRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}

```

consumed:

-   Type: [ConsumedCapacity](reseller.en-US/API Reference/Data Types/ConsumedCapacity.md#) 

-   The capacity units consumed by this operation.


Capacity unit consumption:

-   When the row does not exist:

    -   If the value of the specified condition check is IGNORE, then the number of consumed write capacity units is the sum of the size of the primary key of the row and the size of the attribute column to be updated divided by 4 KB and rounded up. If UpdateRow contains an attribute column to be deleted, only the column name length is included in the size of the attribute column.

    -   If the value of the specified condition check is EXPECT\_EXIST, then insertion of the row fails, which consumes one write capacity unit and one read capacity unit.

-   When the row exists:

    -   If the value of the specified condition check is IGNORE, then the number of consumed write capacity units is the sum of the size of the primary key of the row and the size of the attribute column to be updated divided by 4 KB and rounded up. If UpdateRow contains an attribute column to be deleted, only the column name length is included in the size of the attribute column.

    -   If the value of the specified condition check is EXPECT\_EXIST, both write capacity units and read capacity units are consumed. The number of consumed write capacity units is the same as that when the value of the specified condition check is IGNORE, and the number of consumed read capacity units is the size of the primary key of the row divided by 4 KB and rounded up.

    For more information about how to calculate the data size, see [Billing](../../../../reseller.en-US/Pricing/Billing items and pricing.md#).

-   If the request times out and the results are undefined, a capacity unit may or may not be consumed.

-   If an internal error code is returned \(HTTP status code: 5XX\), no capacity units are consumed. If other errors are returned, one write capacity unit and one read capacity unit are consumed.


row:

-   Type: Bytes

-   The returned data when return\_content is set.

-   If return\_content is not set or no value is returned, NULL is returned.

-   It is in Plainbuffer format. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


