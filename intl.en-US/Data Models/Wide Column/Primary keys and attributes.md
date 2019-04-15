# Primary keys and attributes {#concept_52626_zh .concept}

In Table Store, tables, rows, primary keys, and attributes are the core components that you work with. A table is a collection of rows, and each row consists of a primary key and attributes. The first column of a primary key is called the partition key.

## Primary keys {#section_alv_j21_4gb .section}

Primary keys are used to uniquely identify each row in a table. A primary key is a combination of one to four attributes. When creating a table, you must specify the composition of the primary key, including the name of each attribute, the data type of each attribute, and the sorted order of attributes. In Table Store, you can specify a data type, such as String, Binary, or Integer, for an attribute.

Table Store indexes data of a table based on the primary key of the table. All rows of the table are sorted in ascending or descending order based on the primary key.

## Partition keys {#section_mcm_v21_4gb .section}

The first column of a primary key is called the partition key. Table store assigns a row of data to the corresponding partitions determined by the range of each row’s partition keys to achieve load balancing. Rows that have the same partition key value belong to the same partition. A partition may store rows with multiple partition key values. Table Store separates a partition or merges multiple partitions based on specific rules. This process is completed automatically.

**Note:** The partition key is used as the minimum partition unit. Data under the same partition key value cannot split further. To prevent partitions from being too large to split, we recommend that the total size of all rows with the same partition key value is less than 10 GB.

## Attributes {#section_hv4_ddh_4gb .section}

A row consists of multiple attributes. The number of attributes for each row is not restricted, which means that each row has a different number of attributes. The value of an attribute of a row can be null. The values of an attribute in multiple rows can be of different data types.

An attribute includes the version property. Multiple versions of attribute values can be retained as required for querying or other uses. Additionally, data in an attribute has its own TTL. For more information, see [Data versions and life cycle](intl.en-US/Data Models/Wide Column/Data versions and time to live.md).

