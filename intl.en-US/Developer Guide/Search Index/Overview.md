# Overview {#concept_gmr_nyf_ffb .concept}

You can use the multiple efficient index schemas of search index to solve complex query problems in big data scenarios.

A table in Table Store is a distributed NoSQL data schema. Such tables can support storage and read/write of large-scale data, such as monitoring data and log data. Originally, Table Store only supports queries based on primary key columns, such as reading data in a single row and within a specified range. Other types of queries were not available, such as queries based on non-primary key columns and the bool query.

To resolve this issue, Table Store has provided the search index feature. Based on inverted indexes and column-oriented storage, search index supports multiple queries, including but not limited to:

-   Query based on non-primary key columns
-   Bool query
-   Full-text search
-   Query by geographical location
-   Prefix query
-   Fuzzy query
-   Nested query

## Index differences {#section_elw_gbg_ffb .section}

Aside from queries based on primary key columns in the primary table, Table Store provides two index schemas for accelerated queries: global secondary index and search index. The following table describes the differences among the three indexes.

|Index type|Description|Scenario|
|----------|-----------|--------|
|Table|A table is similar to a big map. Tables only support queries based on primary key columns.| -   You can specify the complete primary key columns.
-   You can specify the prefixes of primary key columns.

 |
|Global secondary index|You can create one or more global secondary indexes and issue query requests against these indexes. This way, you can perform queries based on the primary key columns of these indexes.| -   You can determine the required columns in advance, and only a few columns are required.
-   You can specify the complete primary key columns or the prefixes of primary key columns.

 |
|Search index|Search index uses inverted indexes, Bkd-trees, and column-oriented storage to meet various query scenarios.|All query and analysis scenarios that the table and the global secondary index do not support.|

## Precautions {#section_gs7_x2x_m3y .section}

 **Index synchronization** 

If you have created a search index for a table, data is written to the table first. When the write is successful, success message is immediately returned to the user. At the same time, another asynchronous thread reads the newly written data from the table and writes the data to the search index. This is an asynchronous process.

The asynchronous data synchronization between a table and search index does not affect the write performance of Table Store. The indexing latency is within seconds, most of which are within 10 seconds. You can view the indexing latency in the Table Store console in real time.

 **TTL** 

You cannot create a search index in a table where you have specified the time to live \(TTL\) parameter.

 **max versions** 

You cannot create a search index in a table where you have specified the max versions parameter.

You can customize the timestamp whenever you write data to an attribute column that allows only one version. If you first write a major version number and then a minor version number, the index of the major version number may be overwritten by the index of the minor version number.

## Features {#section_pep_gmp_t1t .section}

Search index can solve complex query problems in big data scenarios. Other systems such as databases and search engines can also solve data query problems. The differences between Table Store and databases and search engines are illustrated as follows:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21708/156586025955765_en-US.png)

Table Store can provide all features of databases and search engines, except for join operations, transactions, and relevance of search results. Table Store also has high data reliability of databases and supports advanced queries of search engines. Therefore, Table Store can replace the common `database plus search engine architecture`. If you do not need join operations, transactions, and relevance of search results, we recommend that you use search index of Table Store.

