# Introduction {#concept_89879_zh .concept}

The Wide Column model differs from the relational model in the following aspects:

-   The characteristics of Wide Column are: three-dimensional \(rows, columns, and time\), schema-free, wide columns, multi-version data, and TTL management.

-   The characteristics of the relational model are : two-dimensional \(rows and columns\) and fixed schema.


![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20271/153968041511642_en-US.png)

The Wide Column model consists of the following parts:

-   Primary key: Every row has a primary key with a multi-column structure \(1-4 columns\). The primary key is defined as a fixed schema, and is used primarily to uniquely distinguish a row of data.

-   Partition key: The first column of the primary key is called a partition key. The partition key is used to partition the table by range. Every partition is distributively dispatched to services on different machines. Within the same partition key, we provide cross-row transactions. For more information, see [Primary key and attribute](intl.en-US/Data Models/Wide Column/Basic concepts/Primary key and attribute.md#).

-   Attribute column: In one row, with the exception of the primary key, all other columns are attribute columns. Attribute columns correspond to many values. Different values correspond to different versions, and each row stores an unlimited number of attribute columns.

-   Version: Each value corresponds to a different version that acts as a timestamp to define the time to live of that data.

-   Data type: Table Store allows many different data types, including String, Binary, Double, Integer and Boolean.

-   Time To Live \(TTL\): Each table defines the amount of time a data can be stored before being deleted. For example, if the TTL is defined as one month, the data written into the table more than a month ago will be cleared automatically. The write time of the data is determined by the version number. This write time is usually taken from the server time, but it can also be determined by the time specified by the application. For more information, see [Data versions and Time To Live](intl.en-US/Data Models/Wide Column/Basic concepts/Data versions and Time To Live.md#).

-   Max versions: Each table defines the maximum number of version data that can be stored in a column, which is used to control the number of versions in each column. If the number of versions in an attribute column exceeds the value in max versions, the earliest version is deleted.


