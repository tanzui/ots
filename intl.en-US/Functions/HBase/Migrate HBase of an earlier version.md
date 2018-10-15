# Migrate HBase of an earlier version {#concept_50166_zh .concept}

Table Store HBase Client supports APIs of HBase Client 1.0.0 and later versions.

Compared with earlier versions, HBase Client 1.0.0 has big changes which are incompatible with HBase Client of earlier versions.

If you use an HBase Client from version 0.x.x \(that is, an earlier version than 1.0.0\), this topic explains how to integrate your HBase Client version with Table Store.

## Connection APIs {#section_kjj_3jn_cfb .section}

HBase 1.0.0 and later versions cancel the HConnection APIs, and instead use the `org.apache.hadoop.hbase.client.ConnectionFactory` series to provide the Connection APIs and replace ConnectionManager and HConnectionManager with ConnectionFactory.

Creating a Connection API has relatively high cost, however, Connection APIs guarantee thread safety. When using a Connection API, you can generate only one Connection object in the program. Multiple threads can then share this object.

You also need to manage the Connection lifecycle, and close it after use.

The latest code is as follows:

```language-java
Connection connection = ConnectionFactory.createConnection(config);
// ...
connection.close();

```

## TableName series { .section}

In HBase version 1.0.0 and earlier, you can use a String-type name when creating a table. For later HBase versions, you can use the `org.apache.hadoop.hbase.TableName`.

The latest code is as follows:

```language-java
String tableName = "MyTable";
// or byte[] tableName = Bytes.toBytes("MyTable");
TableName tableNameObj = TableName.valueOf(tableName);

```

## Table, BufferedMutator, and RegionLocator APIs { .section}

From HBase Client v1.0.0, the HTable APIs are replaced with the Table, BufferedMutator, and RegionLocator APIs.

-    `org.apache.hadoop.hbase.client.Table`: Used to operate reading, writing, and other requests of a single table.

-    `org.apache.hadoop.hbase.client.BufferedMutator`: Used for asynchronous batch writing. This API corresponds to `setAutoFlush(boolean)` of the HTableInterface API of the earlier versions.

-    `org.apache.hadoop.hbase.client.RegionLocator`: Indicates the table partition information.


The Table, BufferedMutator, and RegionLocator APIs do not guarantee thread safety. However, they are lightweight and can be used to create an object for each thread.

## Admin APIs { .section}

From HBase Client v1.0.0, HBaseAdmin APIs are replaced by `org.apache.hadoop.hbase.client.Admin`. As Table Store is a cloud service, and most operation and maintenance APIs are automatically processed, most Admin APIs are not supported. For more information, see [Differences between Table Store and HBase](reseller.en-US/Functions/HBase/Differences between Table Store and HBase.md#).

Use the Connection instance to create an Admin instance:

```language-java
Admin admin = connection.getAdmin();

```

