# 迁移较早版本的 HBase {#concept_50166_zh .concept}

Table Store HBase Client 目前支持 HBase Client 1.0.0 及以上版本的 API。

HBase Client 1.0.0 版本相对于之前版本有一些较大的变化，这些变化是不兼容的。

为了协助一些使用老版本 HBase 的用户能方便地使用表格存储，本节我们将介绍 HBase 1.0 相较于旧版本的一些较大变化，以及如何使其兼容。

## Connection 接口 {#section_kjj_3jn_cfb .section}

HBase 1.0.0 及以上的版本中废除了 HConnection 接口，并推荐使用`org.apache.hadoop.hbase.client.ConnectionFactory`类，创建一个实现 Connection 接口的类，用 ConnectionFactory 取代已经废弃的 ConnectionManager 和 HConnectionManager。

创建一个 Connection 的代价比较大，但 Connection 是线程安全的。使用时可以在程序中只生成一个 Connection 对象，多个线程可以共享这一个对象。

HBase 1.0.0 及以上的版本中，用户需要管理 Connection 的生命周期，并在使用完以后将它 close。

最新的代码如下所示：

```language-java
Connection connection = ConnectionFactory.createConnection(config);
// ...
connection.close();

```

## TableName 类 { .section}

1.0.0 之前版本的 HBase 中，用户在创建表时可以使用 String 类型的表名，但是 1.0.0 之后需要使用类 `org.apache.hadoop.hbase.TableName`。

最新的代码如下所示：

```language-java
String tableName = "MyTable";
// or byte[] tableName = Bytes.toBytes("MyTable");
TableName tableNameObj = TableName.valueOf(tableName);

```

## Table，BufferedMutator 和 RegionLocator 接口 { .section}

从 HBase Client 1.0.0 开始，HTable 接口已经废弃，取而代之的是 Table、BufferedMutator 和 RegionLocator 三个接口。

-    `org.apache.hadoop.hbase.client.Table`：用于操作单张表的读写等请求

-    `org.apache.hadoop.hbase.client.BufferedMutator`：用于异步批量写，对应于旧版本 HTableInterface 接口中的`setAutoFlush(boolean)` 

-    `org.apache.hadoop.hbase.client.RegionLocator`：表分区信息


Table、BufferedMutator 和 RegionLocator 三个接口都不是线程安全的，但比较轻量，可以为每个线程创建一个对象。

## Admin 接口 { .section}

从 HBase Client 1.0.0 开始，HBaseAdmin 类被新接口`org.apache.hadoop.hbase.client.Admin`取代。由于表格存储是一个云服务，大多数运维类接口都是自动处理的，所以 Admin 接口中的众多接口都不会被支持，具体区别请参见[表格存储和 HBase 的区别](intl.zh-CN/产品功能/HBase 支持/表格存储和 HBase 的区别.md#)。

通过 Connection 实例创建 Admin 实例：

```language-java
Admin admin = connection.getAdmin();

```

