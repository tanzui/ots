# Overview {#concept_ogb_g2b_ffb .concept}

Before you use the Global Secondary Index structure, you need to understand the following terms, limits, and notes.

## Terms {#section_l2m_hs1_ffb .section}

|Term|Description|
|:---|:----------|
|Index|You can create an index for some columns in a primary table. The index is read-only.|
|Pre-defined column|Table Store uses a schema-free model. You can write the unlimited number of columns to a row. You do not need to specify a fixed number of attributes in a schema. You can also pre-define some columns and specify their data types when you create a table.|
|Single-column index|You can create an index only for one column.|
|Composite index|You can create an index for multiple columns in a table. A composite index can have Indexed columns 1 and 2.|
|Indexed attribute column|You can map pre-defined columns in a primary table to non-primary key columns in an index.|
|Autocomplete|Table Store automatically adds the primary key column that you have not specified in a primary table to an index when you create the index.|

## Limits {#section_nlx_xz1_ffb .section}

-   You can create a maximum of five indexes in a primary table. You cannot create more indexes if you have created five indexes.
-   An index contains a maximum of four indexed columns. These indexed columns include a combination of primary keys and pre-defined columns of the primary table. If you specify more indexed columns, you cannot create the index.
-   An index contains a maximum of eight attribute columns. If you specify more attribute columns, you cannot create the index.
-   You can specify an indexed column as Integer, String, or Binary type. The constraint of Indexed columns is the same as that for primary keys of the primary table.
-   If an index combines multiple columns, the size limit for the index is the same as that for primary keys of the primary table.
-   When you specify the column of String or Binary type as an attribute column of an index, the limits for the attribute column are the same as those for the attribute column of the primary table.
-   You cannot create an index in a table that has Time To Live \(TTL\) configured. If you want to index a table that has TTL configured, use DingTalk to request technical support.
-   You cannot create an index in a table that has Max Versions configured. If a table has Max Versions configured, you cannot create any index for the table. If you index the table, you cannot use the Max Versions feature.
-   You cannot customize versions when writing data to an indexed primary table. Otherwise, you cannot write data to the primary table.
-   You cannot use the Stream feature in an index.
-   An indexed primary table cannot contain repeated rows that have the same primary key during the same BatchWrite operation. Otherwise, you cannot write data to the primary table.

## Notes {#section_xcy_jz1_ffb .section}

-   Table Store automatically adds the primary key column that you have not specified to the index. When you scan an index, you must specify the range of primary key columns. The range can be from negative infinity to positive infinity. For example, a primary table includes Primary keys `PK0` and `PK1` and Pre-defined column `Defined0`.

    When you create an index for the `Defined0` column, Table Store generates an index that has Primary keys `Defined0`, `PK0`, and `PK1`. When you create an index for the `Defined0` and `PK1` columns, Table Store generates an index that has Primary keys `Defined0`, `PK1`, and `PK0`. When you create an index for the `primary key` columns, Table Store generates an index that has Primary keys `PK1` and `PK0`. When you create an index, you can only specify the column that you want to index. Table Store automatically adds the target columns to the index. For example, a primary table contains Primary keys PK0 and PK1 and Pre-defined column Defined0.

    -   When you create an index for the Defined0 column, Table Store generates the index that has Primary keys Defined0, PK0, and PK1.
    -   When you create an index for the PK1 column, Table Store generates the index that has Primary keys PK1 and PK0.
-   You can specify pre-defined columns as attribute columns in the primary table. When you specify a pre-defined attribute as an indexed attribute column, you can search this index for the attribute value instead of searching the primary table. However, this increases storage costs. If you do not specify a pre-defined attribute as an indexed attribute column, you have to search the primary table. You can choose between these methods based on your requirements.
-   We recommend that you do not specify a column related to the time or date as the first primary key column of an index. This type of column may slow down index updates. We recommend that you hash the column related to the time or date and create an index for the hashed column. To solve related issues, use DingTalk to request technical support.
-   We recommend that you do not define an attribute of low cardinality, even an attribute that contains enumerated values, as the first primary key column of an index. For example, the `gender` attribute restricts the horizontal scalability of the index and leads to poor write performance.

