# Primary key and attribute {#concept_52626_zh .concept}

## Primary Key {#section_llr_yyf_cfb .section}

-   A primary key is the unique identifier of each row in a table. It consists of one to four columns.

-   When a table is created, the primary key must be defined. To define a primary key, the column name and data type of each primary key column, and the fixed sequence of the primary key columns, must be provided.

-   The data types of the primary key column can only be String, Integer, and Binary. For a primary key column of String or Binary data type, the size of the column value must not exceed 1 KB.


## Attribute { .section}

Data of rows are stored in the attribute columns. The number of attribute columns for each row is unlimited.

Version

When the number of versions written to an attribute column exceeds the value of Max Versions, the data of earlier versions is discarded so that the number of remaining versions is equal to the Max Versions value.

When writing data, you can specify the attribute columns’ versions. If you do not specify any versions, the server generates the versions of the attribute columns based on the current timestamp \(expressed by the number of milliseconds that have elapsed since 01/01/1970 00:00:00 UTC\). You can specify the maximum number of versions per column, or the version range, to limit the data that can be read from each row. A version number also indicates the time when data is generated. The version is expressed by the number of milliseconds that have elapsed since 01/01/1970 00:00:00 UTC. For a data table where the TTL is set to 86400 \(one day\), the data in the attribute column where the version is 1468944000000 \(20/07/2016 00:00:00 UTC\) expires at 21/07/2016 00:00:00 UTC and be automatically deleted.

You can specify the maximum number of versions per column or the version range to limit the data that can be read from each row.

**Note:** 

-   During TTL comparison and Max Version Offset calculation, versions in milliseconds must be converted to seconds by dividing by 1000.
-   If versions are determined by the server, data is cleared after the time \(in seconds\) indicated when TTL has elapsed since the data was written.
-   To prevent invalid written data, the system denies the writing of expired data. For example, at 21/07/2016 00:00:00, the data in which the version is earlier than 1468944000000 \(20/07/2016 00:00:00 UTC\) cannot be written to a data table where the TTL is 86400.
-   To prevent writing errors, the system requires that the attribute column version to which the data is written is within range of \[Data written time – The value of Max Version Offset, Data written time + The value of Max Version Offset\).

## Column naming conventions { .section}

The primary key and attribute columns follow the same naming conventions, in which:

-   Only uppercase letters, lowercase letters, digits, and underscores \(\_\) are permitted.

-   The first character must be an uppercase letter, lowercase letter, or underscore \(\_\).

-   All characters are case sensitive.

-   Each name can be 1 to 255 characters in length.


## Data types of column values { .section}

Table Store supports five data types of column values, as shown in the following table.

|Data type|Definition|Permitted as primary key|Size limitation|
|:--------|:---------|:-----------------------|:--------------|
|String|UTF-8, can be empty|Yes|For a primary key column, not greater than 1 KB. For an attribute column, see [Limits](../../../../intl.en-US/Limits/Limits.md).|
|Interger|64 bit, Integer|Yes|8 Bytes.|
|Double|64 bits, Double|No|8 Bytes.|
|Boolean|True/False|No|1 Byte.|
|Binary|Can be empty|Yes|For a primary key column, not greater than 1 KB. For an attribute column, see [Limits](../../../../intl.en-US/Limits/Limits.md).|

## Partition key { .section}

The first primary key column is also called a partition key. Table Store checks the range where the partition key value of each row is located, and automatically allocates the data in this row to the corresponding partition and machine to achieve load balancing.

Rows with the same partition key value belong to the same partition. A partition may contain multiple partition key values. The partition key value is the smallest partition unit. The data of the same partition key value cannot be split. To avoid a situation where the partition is too large to be split, we recommend that the total data volume of all rows under a single partition key value does not exceed 10 GB.

To improve load balancing, Table Store splits and merges partitions according to specific rules. This process is automated without application intervention.

