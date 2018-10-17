# GetRange {#reference3923 .reference}

GetRange reads data within the specified primary key range.

## Request structure: { .section}

```language-protobuf
message GetRangeRequest {
    required string table_name = 1;
    required Direction direction = 2;
    repeated string columns_to_get = 3;  // If it is not specified, all columns are read
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional int32 limit = 6;
    required bytes inclusive_start_primary_key = 7; // The Plainbuffer encoding is binary
    required bytes exclusive_end_primary_key = 8; // The Plainbuffer encoding is binary
    optional bytes filter = 10;
    optional string start_column = 11;
    optional string end_column = 12;
}

```

table\_name:

-   Type: String

-   Required parameter: Yes

-   The name of the table containing the data to be read.


direction:

-   Type: [Direction](reseller.en-US/API Reference/Data Types/Direction.md#) 

-   Required parameter: Yes

-   The query direction.

-   If the query direction is FORWARD, the value of inclusive\_start\_primary must be smaller than that of exclusive\_end\_primary, and the rows in the response are sorted from the smallest to the largest primary keys.
-   If the query direction is BACKWARD, the value of inclusive\_start\_primary must be larger than that of exclusive\_end\_primary, and the rows in the response are sorted from the largest to the smallest primary keys.

columns\_to\_get:

-   Type: Repeated string

-   Required parameter: No

-   The names of all columns to be returned. If it is null, all columns of each row in the read results are returned.

-   If a duplicate column name is given, the returned results only include this column once.

-   In columns\_to\_get, the maximum number of strings is 128.


time\_range:

-   Type: [TimeRange](reseller.en-US/API Reference/Data Types/TimeRange.md#) 

-   Required parameter: Either time\_range or max\_versions is required.

-   The range of time stamps to read versions of data.

-   The minimum and maximum values of the time stamp are 0 and INT64. MAX, respectively.

-   To query data of a time range, specify start\_time and end\_time.

-   To query data of a specific time stamp, specify specific\_time.

-   Example: If the value of time\_range is \(100, 200\), the time stamp of the returned column data must be within \[100, 200\).


max\_versions:

-   Type: int32

-   Required parameter: Either time\_range or max\_versions is required.

-   The maximum number of versions of data to be returned.

-   Example: If the value of max\_versions is 2, data of a maximum of two versions is returned for each column.


limit:

-   Type: int32

-   Required parameter: No

-   The maximum number of rows of data to be returned. If the number of rows queried exceeds this value, the response includes a breakpoint to record the position where reading ended in the operation so that the next read can start at this position. This value must be greater than 0.

-   Whether or not this value is set, Table Store returns a maximum of 5,000 data rows and the total data size never exceeds 4 MB.


inclusive\_start\_primary\_key:

-   Type: Bytes

-   Required parameter: Yes

-   The row that contains the starting primary key for the range to be read. If this row exists, the response returns it. It is encoded in Plainbuffer format. For more information, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


exclusive\_end\_primary\_key:

-   Type: Bytes

-   Required parameter: Yes

-   The row that contains the ending primary key for the range to be read. The response does not contain the row whether the row exists or not. It is encoded in Plainbuffer format. For more information, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).

-   For GetRange, Column in inclusive\_start\_primary\_key or exclusive\_end\_primary\_key can be specialized to INF\_MIN or INF\_MAX. If Column is set to INF\_MIN, the value of the Column is always smaller than that of other Columns. If Column is set to INF\_MAX, its value is always larger than other Columns.


filter:

-   Type: Bytes

-   Required parameter: No

-   The filtering condition expression.

-   It indicates the binary data after the [Filter](reseller.en-US/API Reference/Data Types/筛选.md#) is serialized in Protobuf format.


start\_column:

-   Type: String

-   Required parameter: No

-   The starting column to be read, which is used for reading a wide row.

-   The returned results contain the current starting column.

-   The column names are sorted lexicographically.

-   Example: If a table contains columns “a”, “b”, and “c” and the value of start\_column is “b”, the reading starts from column “b”, and columns “b” and “c” are returned.


end\_column:

-   Type: String

-   Required parameter: No

-   The ending column to be read, which is used for reading a wide row.

-   The returned results do not contain the current ending column.

-   The column names are sorted lexicographically.

-   Example: If a table contains columns “a”, “b”, and “c” and the value of end\_column is “b”, the reading ends at column “b”, and column “a” is returned.


## Response message structure: { .section}

```language-protobuf
message GetRangeResponse {
    required ConsumedCapacity consumed = 1;
    required bytes rows = 2; 
    optional bytes next_start_primary_key = 3; 
}

```

consumed:

-   Type: [ConsumedCapacity](reseller.en-US/API Reference/Data Types/ConsumedCapacity.md#) 

-   The capacity units consumed by this operation.


rows:

-   Type: Bytes

-   Rows are encoded in Plainbuffer format. For more information, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).

-   All data read. If the direction in the request is FORWARD, all rows are sorted from the smallest to the largest primary keys. If the direction in the request is BACKWARD, all rows are sorted from the largest to the smallest primary keys.

-   primary\_key\_columns and attribute\_columns for each row only contain the columns specified in columns\_to\_get. This means their order may not be consistent with that of columns\_to\_get in the request, nor may the order of primary\_key\_columns be consistent with the order specified when the table was created.

-   If the specified columns\_to\_get in the request does not contain any primary key column, the primary key is within the query range. However, the row that does not contain any attribute column in columns\_to\_get is not contained in the response message.


next\_start\_primary\_key:

-   Type: Bytes

-   The breakpoint information for this GetRange operation. It is encoded in Plainbuffer format. For more information, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).

-   If it is null, the response message for this GetRange operation contains all data in the request range.

-   If it is not null, the response message for this GetRange operation includes only the data in the range \[inclusive\_start\_primary\_key, next\_start\_primary\_key\). If you want the remaining data, you must use next\_start\_primary\_key as inclusive\_start\_primary\_key, and retain exclusive\_end\_primary\_key in the original request to perform the subsequent GetRange operation.


**Note:** In Table Store, the number of data rows in the response message for a GetRange operation cannot exceed 5,000, and the size cannot exceed 4 MB. Furthermore, the volume of returned data cannot exceed the reserved read throughput that currently remains. Even if no limit is set in the GetRange request, a next\_start\_primary\_key may still appear in the response. Therefore, when using GetRange, you must check whether the response has a next\_start\_primary\_key to be processed.

Capacity unit consumption:

-   The number of read capacity units consumed by the GetRange operation is the sum of the size of primary keys of all data rows in the query range, and the size of actually read attribute columns divided by 4 KB, and then rounded up. For more information about how to calculate the data size, see [Billing](../../../../reseller.en-US/Pricing/Billing items and pricing.md#).

-   If the request times out and the results are undefined, a capacity unit may or may not be consumed.

-   If an internal error code is returned \(HTTP status code: 5XX\), no capacity units are consumed. If other errors are returned, one read capacity unit is consumed.


