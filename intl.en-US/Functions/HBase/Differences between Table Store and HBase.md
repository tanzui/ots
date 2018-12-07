# Differences between Table Store and HBase {#concept_50220_zh .concept}

This topic introduces features of Table Store HBase Client and explains restricted and supported functions when compared with HBase. Features are listed as follows.

## Table {#section_gvd_wfn_cfb .section}

Table Store only supports single ColumnFamilies, that is, it does not support multi-ColumnFamilies.

## Row and Cell { .section}

-   Table Store does not support ACL settings.
-   Table Store does not support Cell Visibility settings.
-   Table Store does not support Tag settings.

## GET { .section}

Table Store only supports single ColumnFamilies. Therefore, it does not support ColumnFamily related APIs, including:

-   setColumnFamilyTimeRange\(byte\[\] cf, long minStamp, long maxStamp\)
-   setMaxResultsPerColumnFamily\(int limit\)
-   setRowOffsetPerColumnFamily\(int offset\)

## SCAN { .section}

Similar to GET, Table Store does not support ColumnFamily related APIs and cannot be used to set partial optimization APIs, including:

-   setBatch\(int batch\)
-   setMaxResultSize\(long maxResultSize\)
-   setAllowPartialResults\(boolean allowPartialResults\)
-   setLoadColumnFamiliesOnDemand\(boolean value\)
-   setSmall\(boolean small\)

## Batch { .section}

Table Store does not support BatchCallback.

## Mutations and Deletions { .section}

-   Table Store does not support deletion of the specified ColumnFamily.
-   Table Store does not support deletion of the versions with the latest timestamp.
-   Table Store does not support deletion of all versions earlier than the specified timestamp.

## Increment and Append { .section}

Table Store does not support Increment or Append features.

## Filter { .section}

-   Table Store supports ColumnPaginationFilter.
-   Table Store supports FilterList.
-   Table Store partially supports SingleColumnValueFilter, and supports only BinaryComparator.
-   Table Store does not support other Filters.

## Optimization { .section}

Some of the HBase APIs involve access and storage optimization. These APIs are not opened currently:

-   blockcache: The default value is "true", which cannot be modified.
-   blocksize: The default value is "64 KB", which cannot be modified.
-   IsolationLevel: The default value is "READ\_COMMITTED", which cannot be modified.
-   Consistency: The default value is "STRONG", which cannot be modified.

## Admin { .section}

The `org.apache.hadoop.hbase.client.Admin` APIs of HBase are used for management and control, most of which are not required in Table Store.

As Table Store is a cloud service, it automatically performs operations such as operation and maintenance, management, and control, which does not need to be concerned. Table Store currently does not support a few of APIs.

-   CreateTable

    Table Store only supports single ColumnFamilies. Therefore, you can create only one ColumnFamily when creating a table. The ColumnFamily supports the MaxVersions and TimeToLive parameters.

-   Maintenance task

    In Table Store, the following APIs related to task maintenance are automatically processed:

    -   abort\(String why, Throwable e\)
    -   balancer\(\)
    -   enableCatalogJanitor\(boolean enable\)
    -   getMasterInfoPort\(\)
    -   isCatalogJanitorEnabled\(\)
    -   rollWALWriter\(ServerName serverName\) -runCatalogScan\(\)
    -   setBalancerRunning\(boolean on, boolean synchronous\)
    -   updateConfiguration\(ServerName serverName\)
    -   updateConfiguration\(\)
    -   stopMaster\(\)
    -   shutdown\(\)
-   Namespaces

    In Table Store, the instance name is similar to Namespaces in HBase. Therefore, it does not support Namespaces related APIs, including:

    -   createNamespace\(NamespaceDescriptor descriptor\)
    -   modifyNamespace\(NamespaceDescriptor descriptor\)
    -   getNamespaceDescriptor\(String name\)
    -   listNamespaceDescriptors\(\)
    -   listTableDescriptorsByNamespace\(String name\)
    -   listTableNamesByNamespace\(String name\)
    -   deleteNamespace\(String name\)
-   Region

    Table Store automatically performs Region related operations. Therefore, it does not support the following APIs:

    -   assign\(byte\[\] regionName\)
    -   closeRegion\(byte\[\] regionname, String serverName\)
    -   closeRegion\(ServerName sn, HRegionInfo hri\)
    -   closeRegion\(String regionname, String serverName\)
    -   closeRegionWithEncodedRegionName\(String encodedRegionName, String serverName\)
    -   compactRegion\(byte\[\] regionName\)
    -   compactRegion\(byte\[\] regionName, byte\[\] columnFamily\)
    -   compactRegionServer\(ServerName sn, boolean major\)
    -   flushRegion\(byte\[\] regionName\)
    -   getAlterStatus\(byte\[\] tableName\)
    -   getAlterStatus\(TableName tableName\)
    -   getCompactionStateForRegion\(byte\[\] regionName\)
    -   getOnlineRegions\(ServerName sn\)
    -   majorCompactRegion\(byte\[\] regionName\)
    -   majorCompactRegion\(byte\[\] regionName, byte\[\] columnFamily\)
    -   mergeRegions\(byte\[\] encodedNameOfRegionA, byte\[\] encodedNameOfRegionB, boolean forcible\)
    -   move\(byte\[\] encodedRegionName, byte\[\] destServerName\)
    -   offline\(byte\[\] regionName\)
    -   splitRegion\(byte\[\] regionName\)
    -   splitRegion\(byte\[\] regionName, byte\[\] splitPoint\)
    -   stopRegionServer\(String hostnamePort\)
    -   unassign\(byte\[\] regionName, boolean force\)

## Snapshots { .section}

Table Store does not support Snapshots related APIs.

## Replication { .section}

Table Store does not support Replication related APIs.

## Coprocessors { .section}

Table Store does not support Coprocessors related APIs.

## Distributed procedures { .section}

Table Store does not support Distributed procedures related APIs.

## Table Management { .section}

Table Store automatically performs Table related operations, which does not need to be concerned. Therefore, Table Store does not support the following APIs:

-   compact\(TableName tableName\)
-   compact\(TableName tableName, byte\[\] columnFamily\)
-   flush\(TableName tableName\)
-   getCompactionState\(TableName tableName\)
-   majorCompact\(TableName tableName\)
-   majorCompact\(TableName tableName, byte\[\] columnFamily\)
-   modifyTable\(TableName tableName, HTableDescriptor htd\)
-   split\(TableName tableName\)
-   split\(TableName tableName, byte\[\] splitPoint\)

## Restrictions { .section}

As Table Store is a cloud service, to guarantee the optimal overall performance, some parameters are restricted and cannot be reconfigured. For more information about the restrictions, see [Limits](../../../../intl.en-US/Limits/Limits.md#).

