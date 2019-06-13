# Table Store HBase Client 支持的功能 {#concept_50126_zh .concept}

本文主要为您介绍Table Store HBase Client 支持的功能和操作。

## 表格存储与 HBase 的 API 区别 {#section_dc5_l5m_cfb .section}

作为 NoSQL 数据库服务，表格存储为您屏蔽了数据表分裂、Dump、Compact、Region Server 等底层相关的细节，您只需要关心数据的使用。因此，虽然与HBase在[数据模型](../../../../cn.zh-CN/数据模型/前言.md#)及功能上相近，Table Store Hbase Client 与原生的 HBase API 仍然有一些区别。

## 支持的功能 {#section_mq2_dzm_cfb .section}

-   CreateTable

    表格存储不支持列族（ColumnFamily），所有的数据可以认为是在同一个 ColumnFamily 之内，所以表格存储的 TTL 及 Max Versions 都是数据表级别的，支持如下相关功能：

    |功能|支持情况|
    |:-|:---|
    |family max version|支持表级别 max version，默认为 1|
    |family min version|不支持|
    |family ttl|支持表级别 TTL|
    |is/set ReadOnly|通过 RAM 子账号支持|
    |预分区|不支持|
    |blockcache|不支持|
    |blocksize|不支持|
    |BloomFilter|不支持|
    |column max version|不支持|
    |cell ttl|不支持|
    |控制参数|不支持|

-   Put

    |功能|支持情况|
    |:-|:---|
    |一次写入多列数据|支持|
    |指定一个时间戳|支持|
    |如果不写时间戳，默认用系统时间|支持|
    |单行 ACL|不支持|
    |ttl|不支持|
    |Cell Visibility|不支持|
    |tag|不支持|

-   Get

    表格存储保证数据的强一致性，在数据写入 API 收到 HTTP 200 状态码（OK）的回复时，数据即被持久化到所有的备份上，这些数据能够马上被 Get 读到。

    |功能|支持情况|
    |:-|:---|
    |读取一行数据|支持|
    |读取一个列族里面的所有列|支持|
    |读取特定列的数据|支持|
    |读取特定时间戳的数据|支持|
    |读取特定个数版本的数据|支持|
    |TimeRange|支持|
    |ColumnfamilyTimeRange|不支持|
    |RowOffsetPerColumnFamily|支持|
    |MaxResultsPerColumnFamily|不支持|
    |checkExistenceOnly|不支持|
    |closestRowBefore|支持|
    |attribute|不支持|
    |cacheblock:true|支持|
    |cacheblock:false|不支持|
    |IsolationLevel:READ\_COMMITTED|支持|
    |IsolationLevel:READ\_UNCOMMITTED|不支持|
    |IsolationLevel:STRONG|支持|
    |IsolationLevel:TIMELINE|不支持|

-   Scan

    表格存储保证数据的强一致性，在数据写入 API 收到 HTTP 200 状态码（OK）的回复时，数据即被持久化到所有的备份上，这些数据能够马上被 Scan 读到。

    |功能|支持情况|
    |:-|:---|
    |指定 start、stop 确定扫描范围|支持|
    |如果不指定扫描范围，默认扫描全局|支持|
    |prefix filter|支持|
    |读取逻辑同 Get|支持|
    |逆序读|支持|
    |caching|支持|
    |batch|不支持|
    |maxResultSize，返回数据量大小的限制|不支持|
    |small|不支持|
    |batch|不支持|
    |cacheblock:true|支持|
    |cacheblock:false|不支持|
    |IsolationLevel:READ\_COMMITTED|支持|
    |IsolationLevel:READ\_UNCOMMITTED|不支持|
    |IsolationLevel:STRONG|支持|
    |IsolationLevel:TIMELINE|不支持|
    |allowPartialResults|不支持|

-   Batch

    |功能|支持情况|
    |:-|:---|
    |Get|支持|
    |Put|支持|
    |Delete|支持|
    |batchCallback|不支持|

-   Delete

    |功能|支持情况|
    |:-|:---|
    |删除整行|支持|
    |删除特定列的所有版本|支持|
    |删除特定列的特定版本|支持|
    |删除特定列族|不支持|
    |指定时间戳时，deleteColumn 会删除等于这个时间戳的版本|支持|
    |指定时间戳时，deleteFamily 和 deleteColumns 会删除小于等于这个时间戳的所有版本|不支持|
    |不指定时间戳时，deleteColumn 会删除最近的版本|不支持|
    |不指定时间戳时，deleteFamily 和 deleteColumns 会删除当前系统时间的版本|不支持|
    |addDeleteMarker|不支持|

-   checkAndXXX

    |功能|支持情况|
    |:-|:---|
    |CheckAndPut|支持|
    |checkAndMutate|支持|
    |CheckAndDelete|支持|
    |检查列的值是否满足条件，满足则删除|支持|
    |如果不指定值，则表示缺省|支持|
    |跨行，检查 A 行，执行 B 行|不支持|

-   exist

    |功能|支持情况|
    |:-|:---|
    |判断一行或多行是否存在，不返回内容|支持|

-   Filter

    |功能|支持情况|
    |:-|:---|
    |ColumnPaginationFilter|不支持 columnOffset 和 count|
    |SingleColumnValueFilter| 支持：LongComparator，BinaryComparator，ByteArrayComparable

 不支持： RegexStringComparator，SubstringComparator，BitComparator

 |


## 不支持的方法 {#section_7w4_qgg_qvj .section}

-   Namespaces

    表格存储上使用\_\_实例\_\_对数据表进行管理。实例是表格存储最小的计费单元，用户可以在[表格存储控制台](https://ots.console.aliyun.com)上进行实例的管理，所以不再支持如下 Namespaces 相关的操作：

    -   createNamespace\(NamespaceDescriptor descriptor\)
    -   deleteNamespace\(String name\)
    -   getNamespaceDescriptor\(String name\)
    -   listNamespaceDescriptors\(\)
    -   listTableDescriptorsByNamespace\(String name\)
    -   listTableNamesByNamespace\(String name\)
    -   modifyNamespace\(NamespaceDescriptor descriptor\)
-   Region 管理

    表格存储中数据存储和管理的基本单位为[数据分区]()，表格存储会自动地根据数据分区的数据大小、访问情况进行分裂或者合并，所以不支持 HBase 中 Region 管理相关的方法。

-   Snapshots

    表格存储目前不支持 Snapshots，所以暂时不支持 Snapshots 相关的方法。

-   Table 管理

    表格存储会自动对 Table 下的数据分区进行分裂、合并及 Compact 等操作，所以不再支持如下方法：

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

    表格存储暂时不支持协处理器，所以不支持如下方法：

    -   coprocessorService\(\)
    -   coprocessorService\(ServerName serverName\)
    -   getMasterCoprocessors\(\)
-   Distributed procedures

    表格存储不支持 Distributed procedures，所以不支持如下方法：

    -   execProcedure\(String signature, String instance, Map props\)
    -   execProcedureWithRet\(String signature, String instance, Map props\)
    -   isProcedureFinished\(String signature, String instance, Map props\)
-   Increment 与 Append

    暂不支持原子增减和原子 Append。


