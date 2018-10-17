# Table-level operations {#reference3764 .reference}

The SDK offers table operation interfaces, such as CreateTable, ListTable, UpdateTable, DescribeTable, and DeleteTable.

**Note:** The following operation examples are based on synchronous interfaces. See [Asynchronous interfaces](reseller.en-US/SDK Reference/C++ SDK/Asynchronous interface.md#) for more information on how to operate asynchronous interfaces.

## Create a table \(CreateTable\) {#section_bbz_flf_2fb .section}

You must always specify the name and primary key of the table you want to create. The primary key contains between 1 and 4 primary key columns, each of which has a name and type.

In Table Store tables, you can set auto increments in primary key columns. See [Primary key column auto-increment](reseller.en-US/SDK Reference/C++ SDK/Primary key column auto-increment.md#) for more information.

**Example**

In this example, the table name is simple\_create\_delete\_table. The primary key only has one primary key column, named pkey, and is of an integer type \(kPKT\_Integer\).

```language-cpp
CreateTableRequest req;
{
    // immutable configurations of the table
    TableMeta& meta = req.mutableMeta();
    meta.mutableTableName() = "simple_create_delete_table";
    {
        // with exactly one integer primary key column
        Schema& schema = meta.mutableSchema();
        PrimaryKeyColumnSchema& pkColSchema = schema.append();
        pkColSchema.mutableName() = "pkey";
        pkColSchema.mutableType() = kPKT_Integer;
    }
}
CreateTableResponse resp;
Optional<OTSError> res = client.createTable(resp, req);

```

**Note:** Detailed code is available at [createTable@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

**Variable parameters**

You can set several variable parameters for the data table. Variable parameters can be set when creating tables, or changed using [Update table](#).

Variable parameters include the following:

|Variable parameter|Name|Default value|
|:-----------------|:---|:------------|
|mutableTimeToLive\(\)| [Time to live]() |-1 \(meaning data never expires\)|
|mutableMaxVersions\(\)| [Max Versions]() |1|
|mutableMaxTimeDeviation\(\)| [Max Version Offset]() |86,400s \(or one day\)|
|mutableReservedThroughput\(\)| [Reserved read/write throughput]() |0 \(all read/write charged on a Pay-As-You-Go basis\)|

The following is an example of setting the reserved read/write throughput when creating a table:

```language-cpp
CreateTableRequest req;
{
    // immutable configurations of the table
    TableMeta& meta = req.mutableMeta();
    meta.mutableTableName() = "create_table_with_reserved_throughput";
    {
        // with exactly one integer primary key column
        Schema& schema = meta.mutableSchema();
        PrimaryKeyColumnSchema& pkColSchema = schema.append();
        pkColSchema.mutableName() = "pkey";
        pkColSchema.mutableType() = kPKT_Integer;
    }
}
{
    TableOptions& opts = req.mutableOptions();
    {
        // 0 reserved read capacity-unit, 1 reserved write capacity-unit
        CapacityUnit cu(0, 1);
        opts.mutableReservedThroughput().reset(util::move(cu));
    }
}
CreateTableResponse resp;
Optional<OTSError> res = client.createTable(resp, req);

```

## List a table name \(ListTable\) { .section}

Used to get the names of all tables created in the current instance.

**API**

`SyncClient::listTable()` is used to list all tables under the instance.

```language-cpp
SyncClient* client = ... ;
ListTableRequest req;
ListTableResponse resp;
Optional<OTSError> res = client->listTable(resp, req);

```

**Example**

Used to get the names of all tables under the instance.

```language-cpp
const IVector<string>& xs = resp.tables();
for(int64_t i = 0; i < xs.size(); ++i) {
    cout << xs[i] << endl;
}

```

**Note:** Detailed code is available at [listTable@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

## Update a table \(UpdateTable\) {#update_table .section}

Updates the [variable parameters](#UpdatePara) of a specified table.

**Example**

Update the reserved throughput.

```language-cpp
UpdateTableRequest req;
req.mutableTable() = "YourTable";
UpdateTableResponse resp;
{
    TableOptions& opts = req.mutableOptions();
    {
        // 0 reserved read capacity-unit, 1 reserved write capacity-unit
        CapacityUnit cu(0, 1);
        opts.mutableReservedThroughput().reset(util::move(cu));
    }
}
Optional<OTSError> res = client.updateTable(resp, req);

```

**Note:** Detailed code is available at [updateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

## Query the table information \(DescribeTable\) { .section}

You can query the following table information through the `describeTable()` interface:

|Information item|Description|
|:---------------|:----------|
|Table status|Includes:-   kTS\_Active: The table can offer normal read and write services.
-   kTS\_Inactive: The table cannot be read or written, but table data is reserved. This status usually occurs during a primary-backup table switch.
-   - kTS\_Loading: The table is being created. The table cannot be read or written.
-   kTS\_Unloading: The table is being deleted. The table cannot be read or written.
-   kTS\_Updating: The variable table parameters are being updated. The table cannot be read or written.

|
|Table meta|See [Create a table](#CreateTable).|
|Variable table parameters|See [Variable parameters](#UpdatePara).|
|Split points between shards|A Table Store table is split horizontally into several shards. Split points can be obtained through this interface.**Note:** Table Store can be automatically split and merged in the background according to the load. As such, the split points received by this interface are guaranteed to reflect past shards, but not necessarily match what is currently going on.

|

**Example**

```language-cpp
DescribeTableRequest req;
req.mutableTable() = "YourTable";
DescribeTableResponse resp;
Optional<OTSError> res = client.describeTable(resp, req);

```

**Note:** Detailed code is available at [describeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

## Delete a table \(DeleteTable\) { .section}

Deletes specified tables under this instance. The only requirement is to specify the table name.

**Example**

```language-cpp
DeleteTableRequest req;
req.mutableTable() = "YourTable";
DeleteTableResponse resp;
Optional<OTSError> res = client.deleteTable(resp, req);

```

**Note:** Detailed code is available at [deleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

