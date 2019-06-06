# Features {#concept_ly5_2cg_ffb .concept}

## Core features {#section_bgj_93e_31i .section}

 **Query based on non-primary key columns** 

The original table only supports the query based on complete primary key columns or prefixes of primary key columns. The query based on non-primary key columns is not available in some scenarios. However, Search Index supports the query by non-primary key columns. You can create a Search Index structure for the required column and search data by using the value of the column.

 **Bool query** 

The bool query of Search Index is applicable to order scenarios. In these scenarios, a table may contain dozens of fields. You cannot determine the combination of fields that you want to query when you create a table. Even if you determine the combination of required fields, hundreds of combinations may be available. If you use a relational database, you need to create hundreds of indexes. Also, if you miss a combination method, you cannot query the corresponding data.

However, by using Table Store, you only need to create a Search Index structure that includes the possibly required field names. Therefore, you can freely combine these fields in a query. Search Index also supports multiple logical operators, such as AND, OR, and NOT.

 **Geographic location-based query** 

With the popularization of mobile devices, geographic location data is more and more important. The data is increasingly used in most applications, such as WeChat Moments, Weibo, food delivery, sports, and Internet of Vehicles \(IoV\). These applications provide geographic location data, so they must support query features.

Search Index supports the query based on geographic location data as follows:

-   Near: queries points within a specified radius based on an origin, such as the People Nearby feature in Discover of WeChat.
-   Within: specifies a rectangle or polygon area to query points within this area.

Based on these query features, you can use Table Store to easily query geographic location data, and do not need other databases or search systems.

 **Full-text search** 

Search Index can tokenize data and support full-text search. The system tokenizes data in two ways: single word and max word.

**Note:** Table Store does not support relevance searches.

-   Single-word tokenization: the system tokenizes Chinese characters by words, and English words by spaces. English words are case-insensitive. Letters are not segmented from numbers if the letters are spliced with the numbers. For example, the system tokenizes "Mercedes-Benz E300L" into three words: mercedes, benz, and e300l.
-   Max-word tokenization: the system tokenizes sentences into as many semantic words or meaningful words as possible.
    -   For example, the system tokenizes "Mercedes-Benz E300L" into these words: mercedes-benz, mercedes, benz, e300l, e, 300, and l.

The tokenization involves two factors: data expansion rate and recall rate. A finer data granularity results in a higher recall rate and a higher data expansion rate. As a result, the corresponding query occupies more space.

 **Fuzzy query** 

Search Index provides the query based on wildcards. This feature is similar to the LIKE operator in relational databases. You can specify characters and wildcards such as question marks \(`?`\) or asterisks \(`*`\) to query data in the same way as the LIKE operator.

 **Prefix query** 

Search Index provides the prefix query. This feature is applicable to Chinese, English, and other languages. For example, in the query based on the prefix "apple", Table Store may return words such as "apple6s" and "applexr".

 **Nested query** 

In addition to a flat structure, online data such as pictures and labels have some complex multi-layered structures. For example, a database stores a large number of pictures, and each picture has multiple elements, such as houses, cars, or people. Each element in a picture has a unique score. The score is evaluated according to the size and position of an element in a picture. Therefore, each picture has multiple labels. Each label has a name and a weighted score. You can use the nested query based on the conditions or field names of the labels.

The following example shows the JSON data format in a query:

``` {#codeblock_fvg_unv_slz}
{
 "tags": [
   {
      "name": "car",
      "score": 0.78
   },
   {
      "name": "tree",
      "score": 0.24
   }
 ]
}
```

By using the nested query, you can effectively store and query data of multi-layered logical relationships. This query facilitates the modeling of complex data.

 **Cardinality** 

Search Index supports the cardinality feature for query results. The cardinality allows you to specify the highest frequency of occurrence of an attribute value to achieve high cardinality. For example, when you search for a laptop on an e-commerce platform, the first page may display the computers of a certain brand. This is not a user-friendly search. However, the cardinality feature of Table Store can avoid this issue.

 **Sorting** 

A table alphabetically sorts data based on primary keys. To sort data by other fields, you need to use the sorting feature of Search Index. Table Store supports multiple types of sorting, such as forward sorting, reverse sorting, single-field sorting, and multi-field sorting, so you can easily sort global data. By default, the system returns results in the order of primary keys in the table. You can use this method to sort global data.

 **Total number of rows** 

You can specify the number of rows that the system returns for the current request when you use Search Index. If you do not specify any query condition for Search Index, the system returns the total number of rows where you have created indexes. When you stop writing new data to a table and create indexes on all attributes, the system returns the total number of rows in the table. This feature applies to data verification and data management.

## SQL {#section_vup_lar_tam .section}

Table Store does not support SQL statements and operators. But most of these SQL features can match similar features of Search Index, as shown in the following table.

|SQL|Search Index|Supported|
|---|------------|---------|
|SHOW|API: DescribeSearchIndex|Yes|
|SELECT|Parameter: ColumnsToGet|Yes|
|FROM|Parameter: index name|Supported in a single index and not supported in multiple indexes|
|WHERE|Query: a variety of queries such as TermQuery|Yes|
|ORDER BY|Parameter: sort|Yes|
|LIMIT|Parameter: limit|Yes|
|DELETE|API: Query followed by DeleteRow|Yes|
|LIKE|Query: wildcard query|Yes|
|AND|Parameter: operator = and|Yes|
|OR|Parameter: operator = or|Yes|
|NOT|Query: bool query|Yes|
|BETWEEN|Query: range query|Yes|
|NULL|ExistQuery|Yes|

