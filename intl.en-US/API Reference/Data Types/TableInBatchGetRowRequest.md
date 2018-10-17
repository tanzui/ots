# TableInBatchGetRowRequest {#reference1854 .reference}

TableInBatchGetRowRequest indicates the request information of the table to be read in the BatchGetRow operation.

## Data structure { .section}

```language-protobuf
message TableInBatchGetRowRequest {
    required string table_name = 1;
    repeated bytes primary_key = 2; // Plainbuffer encoding
    repeated bytes token = 3;
    repeated string columns_to_get = 4;  // If it is not specified, all columns are read
    optional TimeRange time_range = 5;
    optional int32 max_versions = 6;
    optional bool cache_blocks = 7 [default = true]; // Whether the read data enters the BlockCache
    optional bytes filter = 8;
    optional string start_column = 9;
    optional string end_column = 10;
}


```

table\_name:

-   Type: String

-   The name of the table.


primary\_key:

-   Type: Repeated bytes

-   Required parameter: Yes

-   All primary key columns in the row, including the primary key names and values. The primary key columns are encoded in Plainbuffer format. For more information, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


token:

-   Type: Repeated bytes

-   Required parameter: No

-   It specifies the starting position of a wide row to be read next time, which is unavailable currently.


columns\_to\_get:

-   Type: Repeated string

-   The names of all columns to be returned from this table.


time\_range:

-   Type: [TimeRange](reseller.en-US/API Reference/Data Types/TimeRange.md#) 

-   Required parameter: Either time\_range or max\_versions must exist, or both.

-   The range of time stamps to read versions of data.

-   The time stamp is in the unit of millisecond. The minimum and maximum values of the time stamp is 0 and INT64. MAX, respectively.

-   To query data of a time range, specify start\_time and end\_time.

-   To query data of a specific time stamp, specify specific\_time.

-   Example: If the value of time\_range is \(100, 200\), the time stamp of the returned column data must be within \[100, 200\).


max\_versions:

-   Type: int32

-   Required parameter: Either of max\_versions and time\_range must exist at least.

-   The maximum number of versions of data to be returned.

-   Example: If the value of max\_versions is 2, data of a maximum of two versions is returned for each column.


cache\_blocks:

-   Type: Bool

-   Required parameter: No

-   Whether the read data enters the BlockCache.

-   The default value is true.

-   This parameter cannot be set to false currently.


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


## Related operations { .section}

[BatchGetRow](reseller.en-US/API Reference/Operations/BatchGetRow.md#)

