# PutRow {#reference1838 .reference}

PutRow inserts data in the specified row. If the row does not exist, a new row is added. If the row exists, the original row is overwritten.

## Request message structure: { .section}

```language-protobuf
message PutRowRequest {
    required string table_name = 1;
    required bytes row = 2; // The Plainbuffer encoding is binary
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}

```

table\_name:

-   Type: String

-   Required parameter: Yes

-   The name of the table to write data to.


row:

-   Type: Bytes

-   Required parameter: Yes

-   The data to be written into the row. It is in Plainbuffer format and includes the primary key and attribute column. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


condition:

-   Type: [Condition](reseller.en-US/API Reference/Data Types/Condition.md#) 

-   Required parameter: Yes

-   Determines whether to perform row existence check before writing data. It can be one of three values:

    -   IGNORE indicates that the row existence check is not performed.

    -   EXPECT\_EXIST indicates that the row is expected to exist.

    -   EXPECT\_NOT\_EXIST indicates that the row is not expected to exist.

-   If the row is not expected to exist but it does, or conversely the row is expected to exist but it does not, insertion fails and an error is returned.


return\_content:

-   Type: [ReturnContent](reseller.en-US/API Reference/Data Types/ReturnContent.md#) 

-   Required parameter: No

-   The data type after the row is successfully written. Currently, only the primary key can be returned, which is used for the auto-increment function of the primary key column.


## Response message structure: { .section}

```language-protobuf
message PutRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}

```

consumed:

-   Type: [ConsumedCapacity](reseller.en-US/API Reference/Data Types/ConsumedCapacity.md#) 

-   The capacity units consumed by this operation.


Capacity unit consumption:

-   When the row does not exist:

    -   If the value of the specified condition check is IGNORE, then the number of consumed write capacity units is the sum of the size of the primary key of the row and the size of the attribute column to be inserted divided by 4 KB and rounded up.

    -   If the value of the specified condition check is EXPECT\_NOT\_EXIST, both write capacity units and read capacity units are consumed. The number of consumed write capacity units is the sum of the size of the primary key of the row and the size of the attribute column to be inserted divided by 4 KB and rounded up, and the number of consumed read capacity units is the size of the primary key of the row divided by 4 KB and rounded up.

    -   If the value of the specified condition check is EXPECT\_EXIST, then insertion of the row fails, which consumes one write capacity unit and one read capacity unit.

-   When the row exists:

    -   If the value of the specified condition check is IGNORE, then the number of consumed write capacity units is the sum of the size of the primary key of the row and the size of the attribute column to be inserted divided by 4 KB and rounded up.

    -   If the value of the specified condition check is EXPECT\_EXIST, both write capacity units and read capacity units are consumed. The number of consumed write capacity units is the sum of the size of the primary key of the row and the size of the attribute column to be inserted divided by 4 KB and rounded up, and the number of consumed read capacity units is the size of the primary key of the row divided by 4 KB and rounded up.

    -   If the value of the specified condition check is EXPECT\_NOT\_EXIST, then insertion of the row fails, which consumes one write capacity unit and one read capacity unit.

-   Conditional Update:

    -   If the operation succeeds, the consumed capacity units are calculated according to the preceding method.

    -   If the operation fails, one write capacity unit and one read capacity unit are consumed.

    For more information about how to calculate the data size, see [Billing](../../../../reseller.en-US/Pricing/Billing items and pricing.md#).

-   If an internal error code is returned \(HTTP status code: 5XX\), no capacity units are consumed. If other errors are returned, one write capacity unit is consumed.

-   If the request times out and the results are undefined, a capacity unit may or may not be consumed.


row:

-   Type: Bytes

-   The returned data when return\_content is set.

-   If return\_content is not set or no value is returned, NULL is returned.

-   It is in Plainbuffer format. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


