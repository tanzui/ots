# Table-level operations {#OTS_SDK_0012 .concept}

The Table Store SDK provides the following table-level operation interfaces: CreateTable, ListTable, UpdateTable, DescribeTable, and DeleteTable.

## CreateTable {#createtable .section}

When creating tables in Table store, you must specify the table’s TableMeta and TableOptions. You can also set the ReservedThroughput.

After creating a table, the server must partition and load the table to a certain node. It takes several seconds. During this time, you cannot perform read or write operations because the system will throw an exception.

-   TableMeta

    TableMeta includes the table name and table’s primary key schema.

    -   TableName: The name of the table.
    -   List : The table’s primary key schema.
        -   The table’s primary key can include multiple primary key columns. The primary key columns are sorted by the order they were added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows according to their total primary key values.
        -   The value of the first primary key column serves as the partition key. Data with the same partition key is in the same partition. Therefore, we recommend that you have no more than 10 G of data with the same partition key. Otherwise, individual partitions are too large \(and cannot be split\). Additionally, we also recommend that you distribute read/write access evenly across the different partitions because this helps with load balancing.
        -   When creating a table, you only need to define the table’s primary key. Attribute columns do not have to be defined. Different rows can have different data columns and the names of attribute columns can be specified when writing data. Therefore, Table Store is suitable for the storage of semi-structured data. You can dynamically add new data columns based on your requirements.
-   TableOptions

    TableOptions includes the table’s TTL, MaxVersions, and MaxTimeDeviation configurations.

    -   TTL \(TimeToLive\): The data survival time. Unit: second.
        -   Table Store’s latest API version supports automatic data expiration. If you do not want data to expire, set TTL to -1.
        -   Whether or not data have expired is determined based on the Data Timestamp, Current Time, and Table TTL values. If the difference between Current Time and Data Timestamp is greater than Table TTL, the data expires and is cleared by the Table Store server. For more information about data timestamps, see [Data Model](../../../../reseller.en-US/Data Models/Preface.md#) Concepts.
        -   After the TTL is set, because data expiration involves the data timestamp, if you specify a timestamp when writing data and it is significantly different from the actual time, this may lead to unexpected data expiration. For example, if the specified data timestamp is too small, the data may expire and be deleted as soon as they are written. If the specified timestamp is too large, the data may not expire when required. Therefore, when using the TTL function, make sure that you set a reasonable timestamp during data writing.
    -   MaxVersions: The maximum number of versions retained by each attribute column.

        Table Store’s new API version supports data models with multiple versions. MaxVersions is used to specify the maximum number of data versions saved by each attribute column. If a new version causes the total number of versions to exceed the MaxVersions limit, the server deletes the version with the lowest version number so that the total number of versions remains below the limit.

    -   MaxTimeDeviation: This specifies the maximum deviation allowed between the specified version and the current system time when writing data for a version. Unit: second.

        Table Store supports multiple versions. By default, the system generates a new version number when writing new data. The version number is a timestamp corresponding to the write time in milliseconds. The TTL function uses this timestamp to determine if data have expired. In addition, you can specify the write data timestamp. Therefore, if you set an extremely small timestamp, which deviates from the current time by more than the table’s TTL, the written data immediately expires. To prevent this, the table has the MaxTimeDeviation setting, which uses the unit of seconds to limit the permissible deviation between the data written timestamp and the current system time. You can specify this value when creating a table and modify it using the UpdateTable API.

-   ReservedThroughtput

    This parameter configures the table’s reserved read/write throughput, which affects billing. When the reserved throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a Pay-As-You-Go basis. By default, the reserved throughput is 0 and all traffic is billed on a Pay-As-You-Go basis. If you want to set a value greater than 0, we recommend that you check Table Store [Billing](../../../../reseller.en-US/Pricing/Billing items and pricing.md#) to avoid unexpected fees.

    For container instances, the reserved throughput can only be set to 0. These instances do not permit throughput reservation.

    **Example**

    ```
    private static void createTable(SyncClient client) {
            TableMeta tableMeta = new TableMeta(TABLE_NAME);
            tableMeta.addPrimaryKeyColumn(new PrimaryKeySchema(PRIMARY_KEY_NAME, PrimaryKeyType.STRING));
            // The data expiration time in seconds; -1 indicates data do not expire. To set the expiration time to one year, use 365 x 24 x 3600
            int timeToLive = -1; 
            // The max number of versions to save. If it is set to 3, each column will only save the three latest versions.
            int maxVersions = 3; 
            TableOptions tableOptions = new TableOptions(timeToLive, maxVersions);
            CreateTableRequestEx request = new CreateTableRequestEx(tableMeta, tableOptions);
            // Set reserved read/write throughput
             request.setReservedThroughput(new ReservedThroughput(new CapacityUnit(0, 0)));
            client.createTable(request);
        }
    ```


## ListTable {#section_gkk_m4l_cz .section}

Obtains the names of all tables under the current instance.

**Example**

```
private static void listTable(SyncClient client) {
    ListTableResponse response = client.listTable();
    System.out.println("The list of tables is as follows:");
    for (String tableName : response.getTableNames()) {
        System.out.println(tableName);
    }
}
```

## UpdateTable {#section_b2s_44l_cz .section}

Table Store allows you to update tables’ ReservedThroughput and TableOptions.

For ReservedThroughput and TableOptions, see the preceding [CreateTable](#) section. Currently, the interval between attempts to change the ReservedThroughput is a minimum of 1 minute.

**Example**

In this example, a table’s TTL and MaxVersions are updated.

```
private static void updateTable(SyncClient client) {
    int timeToLive = -1;
    int maxVersions = 5; //Updates the MaxVersions value to 5.
    TableOptions tableOptions = new TableOptions(timeToLive, maxVersions);
    UpdateTableRequest request = new UpdateTableRequest(TABLE_NAME);
    request.setTableOptionsForUpdate(tableOptions);
    client.updateTable(request);
}
```

## DescribeTable {#section_ord_q4l_cz .section}

The DescribeTable API allows you to query tables’ TableMeta, TableOptions, and ReservedThroughputDetails. TableMeta and TableOptions have already be described in the [CreateTable](#) section. Besides containing the reserved throughput values, ReservedThroughputDetails also shows the last time the values were increased or decreased.

**Example**

In this example, the descriptive information of a table is obtained.

```
private static void describeTable(SyncClient client) {
    DescribeTableRequest request = new DescribeTableRequest(TABLE_NAME);
    DescribeTableResponse response = client.describeTable(request);
    TableMeta tableMeta = response.getTableMeta();
    System.out.println("Table name:" + tableMeta.getTableName());
    System.out.println("Table primary key:");
    for (PrimaryKeySchema primaryKeySchema : tableMeta.getPrimaryKeyList()) {
        System.out.println(primaryKeySchema);
    }
    TableOptions tableOptions = response.getTableOptions();
    System.out.println("Table TTL:" + tableOptions.getTimeToLive());
    System.out.println("Table MaxVersions:" + tableOptions.getMaxVersions());
    ReservedThroughputDetails reservedThroughputDetails = response.getReservedThroughputDetails();
    System.out.println("Table's reserved read throughput:"
            + reservedThroughputDetails.getCapacityUnit().getReadCapacityUnit());
    System.out.println("Table's reserved write throughput:"
            + reservedThroughputDetails.getCapacityUnit().getWriteCapacityUnit());
}
```

## DeleteTable {#section_ur3_s4l_cz .section}

In this example, a specified table under an instance is deleted.

**Example**

In this example, a table is deleted.

```
private static void deleteTable(SyncClient client) {
    DeleteTableRequest request = new DeleteTableRequest(TABLE_NAME);
    client.deleteTable(request);
}
```

