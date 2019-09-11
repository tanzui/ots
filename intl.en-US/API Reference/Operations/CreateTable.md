# CreateTable {#reference997 .reference}

CreateTable creates a table based on the given table structure information.

## Request structure: {#section_ozh_yo3_7me .section}

``` {#codeblock_wsw_wj2_zwr .language-pb}
message CreateTableRequest {
    required TableMeta table_meta = 1;
    required ReservedThroughput reserved_throughput = 2; 
    optional TableOptions table_options = 3;
    optional StreamSpecification stream_spec = 5;
}
			
```

table\_meta:

-   Type: [TableMeta](intl.en-US/API Reference/Data Types/TableMeta.md#)

-   Required parameter: Yes

-   The structure information of the table to be created. The table\_name must be unique within the instance. In primary\_key, the number of ColumnSchema must range from one to four, the ColumnSchema names must comply with [Naming conventions and data types](../../../../intl.en-US/Developer Guide/Wide column model/Naming conventions and data types.md#), and the type value can only be STRING, INTEGER, or BINARY.

-   Once a table is created, its Schema cannot be modified.


reserved\_throughput:

-   Type: [ReservedThroughput](intl.en-US/API Reference/Data Types/ReservedThroughput.md#)

-   Required parameter: Yes

-   The initial reserved read/write throughput for the table to be created. The maximum reserved throughput is 5,000 for each table.

-   The reserved read/write throughput settings for a table can be modified dynamically by using the UpdateTable operation.


table\_options:

-   Type: [TableOptions](intl.en-US/API Reference/Data Types/TableOptions.md#)

-   Required parameter: Yes

-   Sets TimeToLive and MaxVersions.


stream\_spec:

-   Type: [StreamSpecification](intl.en-US/API Reference/Data Types/StreamSpecification.md#)

-   Required parameter: No

-   Specifies whether to enable Stream-related attributes.


## Response message structure: {#section_k58_k9e_lqq .section}

``` {#codeblock_7ul_jl5_pyb .language-pb}
message CreateTableResponse {
}
			
```

## Note: {#section_ast_6jx_f1v .section}

-   No read/write operation can be performed on a table immediately after it is created. Generally, a read/write operation can be performed on a new table one minute after it is created.

-   A single instance can contain up to 64 tables.


