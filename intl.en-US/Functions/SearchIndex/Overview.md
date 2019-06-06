# Overview {#concept_gmr_nyf_ffb .concept}

Search Index is a new index feature of Table Store. You can use multiple efficient index structures of Search Index to perform complex query operations.

## Features {#section_mmb_y1g_ffb .section}

A table in Table Store is a typical distributed NoSQL data structure. The table can efficiently support large-scale data storage and read/write scenarios, such as monitoring data and log data. However, Table Store only supported the query based on primary keys, for example, reading data in a single row or within a specified range. Other types of queries were not available, for example, the query based on non-primary key columns and the bool query.

To solve this issue, Table Store has provided the Search Index feature. Based on inverted indexes and column stores, Search Index can support multiple queries, including but not limited to:

-   Query based on non-primary key columns
-   Bool query
-   Full-text search
-   Geographic location-based query
-   Prefix query
-   Fuzzy query
-   Nested query

## Index differences {#section_elw_gbg_ffb .section}

In addition to the query based on primary keys in a primary table, Table Store provides two types of index structures to accelerate queries: Global Secondary Index and Search Index.

 **Table** 

A table is similar to a big map. The table only supports the query based on primary keys in the same way as a map. Therefore, the table is suitable for the following query scenarios:

-   You can determine the complete primary keys.
-   You can determine the prefixes of primary keys.

 **Global Secondary Index** 

When you use the Global Secondary Index structure, you have to create one or more new tables. Table Store queries data based on primary key columns in the new table. In this way, you can extend the query scope to more columns. Therefore, the Global Secondary Index structure is suitable for the following scenarios:

-   You can determine the target columns in advance, and the number of target columns is small.
-   You can determine the complete primary key columns or the prefixes of primary key columns.

 **Search Index** 

The Search Index structure uses inverted indexes, Bkd-trees, and column stores, and supports multiple types of queries. The Search Index structure is applicable to the following scenarios:

-   All query and analysis scenarios that the table and the Global Secondary Index structure do not support.

**Note:** For more information about index comparison, see [Description of Table Store indexes](https://yq.aliyun.com/articles/692837).

## Restrictions {#section_gs7_x2x_m3y .section}

 **Synchronize indexes** 

After you enable the Search Index feature for a table, incremental data written to the table is synchronized to the table, and then Table Store indicates the successful write operation in the response. Meanwhile, an asynchronous thread reads the incremental data from the table and writes the incremental data to the Search Index structure. This asynchronous process has a latency of a few seconds, mostly within 10 seconds. The Table Store console provides the time when you last created the index, so you can check the latency of creating the index in real time.

The asynchronous process of creating a Search Index structure does not affect the write performance of Table Store.

 **TTL** 

You cannot create a Search Index structure in a table where you have specified the Time To Live \(TTL\) parameter.

 **Max Versions** 

You cannot create a Search Index structure in a table where you have specified the Max Versions parameter.

You can customize the timestamp whenever writing data to an attribute that allows only one version. If you first write a major version number and then a minor version number, the index of the major version number may be overwritten with the index of the minor version number.

## Features {#section_pep_gmp_t1t .section}

The Search Index feature is used to support queries in big data. Some other systems such as databases and search engines can also provide similar queries. The differences between Table Store and the databases and search systems are listed as follows:

![](images/45919_en-US.png)

-   Compared with databases, Table Store does not support join operations and transactions.
-   Compared with search systems, Table Store does not support user-defined relevance.

Except these features, Table Store supports all other features of the databases and search systems. If your business does not require join operations, transactions, and user-defined relevance, you can use the Search Index feature of Table Store.

Table Store also supports high data reliability of databases and the advanced query features of the search systems. Therefore, this service can work as an alternative of a common architecture that combines `databases and search systems`.

