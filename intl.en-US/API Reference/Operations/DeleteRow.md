# DeleteRow {#reference1493 .reference}

DeleteRow deletes a data row.

## Request message structure: { .section}

```language-pb
message DeleteRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2; // The Plainbuffer encoding is binary
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}

```

table\_name:

-   Type: String
-   Required parameter: Yes
-   The name of the table whose data is to be updated.

primary\_key:

-   Type: Bytes

-   Required parameter: Yes

-   The primary key of the row to be deleted, which is in Plainbuffer format. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


condition:

-   Type: [Condition](reseller.en-US/API Reference/Data Types/Condition.md#) 

-   Required parameter: Yes

-   Determines whether to perform existence check before data updating. It can be one of two values:

    -   IGNORE: indicates that the row existence check is not performed.

    -   EXPECT\_EXIST: indicates that the row is expected to exist.

-   If this row is expected to exist but does not, the delete operation fails and an error is returned. If the existence of the row is ignored, this operation is successful regardless of the row’s existence.


return\_content:

-   Type: [ReturnContent](reseller.en-US/API Reference/Data Types/ReturnContent.md#) 

-   Required parameter: No

-   The data type after the row is successfully written. Currently, only the primary key can be returned, which is used for the auto-increment function of the primary key column.


## Response message structure: { .section}

```language-pb
message DeleteRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}

```

consumed:

-   Type: [ConsumedCapacity](reseller.en-US/API Reference/Data Types/ConsumedCapacity.md#) 

-   The capacity units consumed by this operation.


Capacity unit consumption:

-   When the row does not exist:

    -   If the value of the specified condition check is IGNORE, then the number of consumed write capacity units is the size of the primary key of the row divided by 4 KB and rounded up.

    -   If the value of the specified condition check is EXPECT\_EXIST, then deletion of the row fails and one write capacity unit and one read capacity unit are consumed.

-   When the row exists:

    -   If the value of the specified condition check is IGNORE, then the number of consumed write capacity units is the size of the primary key of the row divided by 4 KB and rounded up.

    -   If the value of the specified condition check is EXPECT\_EXIST, both write capacity units and read capacity units are consumed. The number of consumed write capacity units and the number of consumed read capacity units are the size of the primary key of the row divided by 4 KB and rounded up, respectively.

    For more information about how to calculate the data size, see [Billing](../../../../reseller.en-US/Pricing/Billing items and pricing.md#).

-   If an internal error code is returned \(HTTP status code: 5XX\), this operation does not consume capacity units. If other errors are returned, one write capacity unit is consumed.

-   If the request times out and the results are undefined, a capacity unit may or may not be consumed.


row:

-   Type: bytes

-   The returned data when return\_content is set.

-   If return\_content is not set or no value is returned, NULL is returned.

-   It is in Plainbuffer format. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


