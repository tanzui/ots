# CreateTable {#concept_thw_qnz_zfb .concept}

You can call this operation to create a table. Specify TableMeta and TableOptions when creating the table. You can also specify ReservedThroughput as needed.

**Note:** After you create the table, Table Store loads splits of the table to a specified node. The table is available for reading and writing within a few seconds. An exception may occur if you try to read from or write to the table immediately after creating the table.

## Parameters {#section_xny_3tz_zfb .section}

-   **TableMeta**

    TableMeta includes the TableName and List parameters.

    |Parameter|Definition|Description|
    |:--------|:---------|:----------|
    |TableName|The name of the target table.|None|
    |List|The definition of primary keys in the target table.|     -   Table Store supports multiple primary key columns. The primary key columns are sorted in the order that you added them to the table. For example, PRIMARY KEY \(A, B, C\) and PRIMARY KEY \(A, C, B\) are two different primary key structures. Table Store sorts rows according to their total primary key values.
    -   The value of the first primary key column serves as the partition key. Table Store saves data with the same partition key to the same partition. We recommend that the same partition key be 10 GB or less. Otherwise, the partition cannot be split due to the excessive size. We also recommend that you evenly distribute read and write operations among different partition keys to load balance.
    -   You do not need to define attribute columns. Data columns in each row can be different. You can specify the name of an attribute column when writing data to the column.
 |

-   **TableOptions**

    TableOptions includes the TTL, MaxVersions, and MaxTimeDeviation parameters.

    |Parameter|Definition|Description|
    |:--------|:---------|:----------|
    |TTL|Time To Live, the life span of data.|     -   Unit: seconds.
    -   If you do not want data to expire, set TTL to -1.
    -   Table Store checks whether data has expired based on the data timestamp, current system time, or table TTL. Data expires and is deleted if `current system time - data timestamp > table TTL`. For more information about timestamps, see [Preface](../../../../../intl.en-US/Data Models/Preface.md#).
    -   When you write data with TTL, make sure that you specify a valid timestamp. We recommend that you set MaxTimeDeviation to specify the timestamp.
 |
    |MaxTimeDeviation|The maximum time deviation between the timestamp of written data and current system time.|     -   The system generates a timestamp for new data by default. The system checks whether data is expired based on the timestamp and TTL. You can specify the timestamp of written data. When the deviation between the timestamp you have specified and current system time is greater than the specified TTL, the written data expires immediately. To avoid this problem, you can set MaxTimeDeviation.
    -   Unit: seconds.
    -   You can specify this parameter when creating a table or by using the UpdateTable operation.
 |
    |MaxVersions|The maximum number of versions that each attribute column can contain.|If the number of versions of an attribute column is more than the value of MaxVersions, Table Store only keeps the latest version that you have specified for MaxVersions.|

-   **ReservedThroughput**

    The configuration of reserved read and write throughput of a table.

    -   Table Store calculates billing according to the reserved read and write throughput you have specified in the ReservedThroughput field.
    -   If ReservedThroughtput is higher than 0, Table Store calculates billing based on the reserved throughput and duration. The actual read and write throughput more than the reserved quota follows the Pay-As-You-Go billing method. For more information, see [Billing items and pricing](../../../../../intl.en-US/Pricing/Billing items and pricing.md#) to avoid unexpected fees.
    -   The default value 0 indicates that the Pay-As-You-Go billing method is fully applicable to Table Store.
    -   You can set the reserved read and write throughput of capacity instances only to 0. These instances do not allow reserved throughput.

## Example {#section_mwm_yq5_cgb .section}

```
private static void createTable(SyncClient client) {
        TableMeta tableMeta = new TableMeta(TABLE_NAME);
        tableMeta.addPrimaryKeyColumn(new PrimaryKeySchema(PRIMARY_KEY_NAME, PrimaryKeyType.STRING)); // Add a primary key column to the primary table.
        int timeToLive = -1; // The time when data is expired. Unit: seconds. A value of -1 indicates that the data is never expired. For example, to keep the data valid for one year, set the parameter to 365 * 24 * 3600.
        int maxVersions = 3; // The maximum number of versions for each column. A value of 3 indicates that each column contains a maximum of three of the latest versions.
        TableOptions tableOptions = new TableOptions(timeToLive, maxVersions);
        CreateTableRequestEx request = new CreateTableRequestEx(tableMeta, tableOptions);
        request.setReservedThroughput(new ReservedThroughput(new CapacityUnit(0, 0))); // Set the reserved read and write throughput. Set the parameter to 0 only for capacity instances, and to non-zero values for high-performance instances.
        client.createTable(request);
    }
```

