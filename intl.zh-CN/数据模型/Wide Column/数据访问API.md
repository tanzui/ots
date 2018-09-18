# 数据访问API {#concept_89881_zh .concept}

Wide Column 模型在数据操作层面，提供两类数据访问 API：Data API 和 Stream API。

## Data API {#section_l1c_xgg_cfb .section}

关于 Data API 的详细文档，请参考[表格存储的数据操作](../../../../intl.zh-CN/.md#)。Data API 是标准的数据 API，提供数据的在线读写，包括：

-   PutRow：新插入一行，如果存在相同行，则覆盖。

-   UpdateRow：更新一行，可增加、删除一行中的属性列，或者更新已经存在的属性列的值。如果该行 不存在，则新插入一行。

-   DeleteRow：删除一行。

-   BatchWriteRow：批量更新多张表的多行数据。

-   GetRow：读取一行数据。

-   GetRange：范围扫描数据，可正序扫描或者逆序扫描。

-   BatchGetRow：批量查询多张表的多行数据。


## Stream API { .section}

在关系模型数据库中没有对数据库内增量数据定义标准的 API，但是在传统关系数据库的很多应用场景 中，增量数据（Binlog）的用途不可忽视。Table Store将增量数据的能力挖掘出来后可以在技术架 构上实现：

-   异构数据源复制：MySQL 数据增量同步到 NoSQL，做冷数据存储。
-   对接流计算：可实时对MySQL内数据做统计，做一些大屏类应用。
-   对接搜索系统：可将搜索系统扩展为 MySQL 的二级索引，增强 MySQL 内数据的检索能力。

Table Store挖掘了增量数据存在的价值，并提供了标准的[Stream API](../../../../intl.zh-CN/.md#)。Steam API提供的接口如下：

-   ListStream：获取表的 Stream 信息，如 StreamID。
-   DescribeStream：获取 Stream 的详细信息，如 Shard 列表、Shard 结构树等。
-   GetShardIterator：获取 Shard 当前增量数据的 Iterator。
-   GetStreamRecord：根据 ShardIterator 获取 Shard 内的增量数据。

Table Store Stream 的实现会比 MySQL Binlog 复杂得多。因为 Table Store 本身是一个分布式的架构， Stream 也是一个分布式的增量数据消费框架。Stream 的数据消费必须保序获取，Stream 的 Shard 与 Table Store 内部表的分区一一对应，且表的分区会存在分裂和合并。

