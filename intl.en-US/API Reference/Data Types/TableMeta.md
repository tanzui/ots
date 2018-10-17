# TableMeta {#reference284 .reference}

TableMeta indicates the structure information of a table.

## Data structure { .section}

```language-xml
message TableMeta {
    required string table_name = 1;
    repeated PrimaryKeySchema primary_key = 2;
}

```

table\_name:

-   Type: string

-   The name of the table.


primary\_key:

-   Type: repeated [PrimaryKeySchema](reseller.en-US/API Reference/Data Types/PrimaryKeySchema.md#) 

-   All primary key columns for the table.


## Related operations { .section}

 [CreateTable](reseller.en-US/API Reference/Operations/CreateTable.md#)

 [DescribeTable](reseller.en-US/API Reference/Operations/DescribeTable.md#) 

