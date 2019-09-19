# Table Store HBase Client supported functions {#concept_50126_zh .concept}

## API support differences between Table Store and HBase {#section_dc5_l5m_cfb .section}

Table Store and HBase, while similar in terms of data model and functions, have different APIs. The following sections detail differences between Table Store HBase Client APIs and HBase APIs.

## Functions supported by Table Store HBase Client APIs: {#section_mq2_dzm_cfb .section}

-   CreateTable

    Table Store does not support ColumnFamily as all data can be considered to be in the same ColumnFamily. This means that TTL and Max Versions of Table Store are at the table-level. Therefore, Table Store has some support for the following functions:

    |Functions|Supported or Not|
    |:--------|:---------------|
    |family max version|Table-level Max Versions supported. Default value: 1|
    |family min version|Unsupported|
    |family ttl|Table-level TTL supported|
    |is/set ReadOnly|Supported through the sub-account of RAM|
    |Pre-partitioning|Unsupported|
    |blockcache|Unsupported|
    |blocksize|Unsupported|
    |BloomFilter|Unsupported|
    |column max version|Unsupported|
    |cell ttl|Unsupported|
    |Control parameter|Unsupported|

-   Put

    |Functions|Supported or Not|
    |:--------|:---------------|
    |Writes multiple columns of data at a time|Supported|
    |Specifies a timestamp|Supported|
    |Uses the system time by default if no timestamp is specified|Supported|
    |Single-row ACL|Unsupported|
    |ttl|Unsupported|
    |Cell Visibility|Unsupported|
    |tag|Unsupported|

-   Get

    Table Store guarantees high data consistency. If the HTTP 200 status code \(OK\) is returned after data is written to an API, the data is permanently written to all copies, and can be read immediately by Get.

    |Functions|Supported or Not|
    |:--------|:---------------|
    |Reads a row of data|Supported|
    |Reads all columns in a ColumnFamily|Supported|
    |Reads data from a specified column|Supported|
    |Reads data with a specified timestamp|Supported|
    |Reads data of a specified number of versions|Supported|
    |TimeRange|Supported|
    |ColumnfamilyTimeRange|Unsupported|
    |RowOffsetPerColumnFamily|Supported|
    |MaxResultsPerColumnFamily|Unsupported|
    |checkExistenceOnly|Unsupported|
    |closestRowBefore|Supported|
    |attribute|Unsupported|
    |cacheblock:true|Supported|
    |cacheblock:false|Unsupported|
    |IsolationLevel:READ\_COMMITTED|Supported|
    |IsolationLevel:READ\_UNCOMMITTED|Unsupported|
    |IsolationLevel:STRONG|Supported|
    |IsolationLevel:TIMELINE|Unsupported|

-   Scan

    Table Store guarantees high data consistency. If the HTTP 200 status code \(OK\) is returned after data is written to an API, the data is permanently written to all copies, which can be read immediately by Scan.

    |Functions|Supported or Not|
    |:--------|:---------------|
    |Determines a scanning range based on the specified start and stop|Supported|
    |Globally scans data if no scanning range is specified|Supported|
    |prefix filter|Supported|
    |Reads data using the same logic as Get|Supported|
    |Reads data in reverse order|Supported|
    |caching|Supported|
    |batch|Unsupported|
    |maxResultSize, indicating the maximum size of the returned data volume|Unsupported|
    |small|Unsupported|
    |batch|Unsupported|
    |cacheblock:true|Supported|
    |cacheblock:false|Unsupported|
    |IsolationLevel:READ\_COMMITTED|Supported|
    |IsolationLevel:READ\_UNCOMMITTED|Unsupported|
    |IsolationLevel:STRONG|Supported|
    |IsolationLevel:TIMELINE|Unsupported|
    |allowPartialResults|Unsupported|

-   Batch

    |Functions|Supported or Not|
    |:--------|:---------------|
    |Get|Supported|
    |Put|Supported|
    |Delete|Supported|
    |batchCallback|Unsupported|

-   Delete

    |Functions|Supported or Not|
    |:--------|:---------------|
    |Deletes a row|Supported|
    |Deletes all versions of the specified column|Supported|
    |Deletes the specified version of the specified column|Supported|
    |Deletes the specified ColumnFamily|Unsupported|
    |When a timestamp is specified, deleteColumn deletes the versions that are equal to the timestamp|Supported|
    |When a timestamp is specified, deleteFamily and deleteColumn delete the versions that are earlier than or equal to the timestamp|Unsupported|
    |When no timestamp is specified, deleteColumn deletes the latest version|Unsupported|
    |When no timestamp is specified, deleteFamily and deleteColumn delete the version of the current system time|Unsupported|
    |addDeleteMarker|Unsupported|

-   checkAndXXX

    |Functions|Supported or Not|
    |:--------|:---------------|
    |CheckAndPut|Supported|
    |checkAndMutate|Supported|
    |CheckAndDelete|Supported|
    |Checks whether the value of a column meets the conditions. If yes, checkAndXXX deletes the column.|Supported|
    |Uses the default value if no value is specified|Supported|
    |Checks row A and executes row B.|Unsupported|

-   Exist

    |Functions|Supported or Not|
    |:--------|:---------------|
    |Checks whether one or more rows exist and does not return any content|Supported|

-   Filter

    |Functions|Supported or Not|
    |:--------|:---------------|
    |ColumnPaginationFilter|columnOffset and count unsupported|
    |SingleColumnValueFilter| Supported: LongComparator, BinaryComparator, and ByteArrayComparable

 Unsupported: RegexStringComparator, SubstringComparator, and BitComparator

 |


## Functions not supported by Table Store HBase Client APIs {#section_7fc_p7n_6q5 .section}

-   Namespaces

    Table Store uses instances to manage a data table. An instance is the minimum billing unit in Table Store. You can manage instances in the [Table Store console](https://ots.console.aliyun.com). Therefore, the following features are not supported:

    -   createNamespace\(NamespaceDescriptor descriptor\)
    -   deleteNamespace\(String name\)
    -   getNamespaceDescriptor\(String name\)
    -   listNamespaceDescriptors\(\)
    -   listTableDescriptorsByNamespace\(String name\)
    -   listTableNamesByNamespace\(String name\)
    -   modifyNamespace\(NamespaceDescriptor descriptor\)
-   Region management

    [Data partition]() is the basic unit for data storage and management in Table Store. Table Store automatically splits or merges the data partitions based on their data volumes and access conditions. Therefore, Table Store does not support features related to Region management in HBase.

-   Snapshots

    Table Store does not support Snapshots, or related featurs of Snapshots.

-   Table management

    Table Store automatically splits, merges, and compacts data partitions in tables. Therefore, the following features are not supported:

    -   getTableDescriptor\(TableName tableName\)
    -   compact\(TableName tableName\)
    -   compact\(TableName tableName, byte\[\] columnFamily\)
    -   flush\(TableName tableName\)
    -   getCompactionState\(TableName tableName\)
    -   majorCompact\(TableName tableName\)
    -   majorCompact\(TableName tableName, byte\[\] columnFamily\)
    -   modifyTable\(TableName tableName, HTableDescriptor htd\)
    -   split\(TableName tableName\)
    -   split\(TableName tableName, byte\[\] splitPoint\)
-   Coprocessors

    Table Store does not support the coprocessor. Therefore, the following features are not supported:

    -   coprocessorService\(\)
    -   coprocessorService\(ServerName serverName\)
    -   getMasterCoprocessors\(\)
-   Distributed procedures

    Table Store does not support Distributed procedures. Therefore, the following features are not supported:

    -   execProcedure\(String signature, String instance, Map props\)
    -   execProcedureWithRet\(String signature, String instance, Map props\)
    -   isProcedureFinished\(String signature, String instance, Map props\)
-   Increment and Append

    Table Store does not support atomic increase/decrease or atomic Append.


