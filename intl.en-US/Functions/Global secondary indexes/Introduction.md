# Introduction {#concept_szt_gcb_ffb .concept}

A global secondary index in Table Store has the following features:

-   Supports asynchronous data synchronization between a table and table indexes. Under normal network conditions, the data synchronization latency is in milliseconds.
-   Supports single-field indexes, compound indexes, and covered indexes. Pre-defined attributes are attributes specified in advance in a table. You can create an index on any pre-defined attribute or on a table primary key. In addition, you can specify a table pre-defined attributes as index attributes or choose not to specify attributes. If you specify pre-defined attributes as the index attributes, you can directly query this index to retrieve data from the base table instead of querying the table. For example, a base table includes three primary keys PK0, PK1, and PK2. Additionally, the table have three pre-defined attributes Defined0, Defined1, and Defined2.
    -   You can create an index on PK2 without specifying an attribute.
    -   You can create an index on PK2 and specify Defined0 as an attribute.
    -   You can create an index on PK3 and PK2 without specifying an attribute.
    -   You can create an index on PK3 and PK2 and specify Defined0 as an attribute.
    -   You can create an index on PK2, PK1, and PK3 and specify Defined0, Defined1, and Defined2 as an attribute.
    -   You can create an index on Defined0 without specifying an attribute.
    -   You can create an index on Define0 and PK1 and specify Defined1 as an attribute.
    -   You can create an index on Define1 and Define0 without specifying an attribute.
    -   You can create an index on Define1 and Define0 and specify Defined2 as an attribute.
-   Supports sparse indexes. You can specify a base table pre-defined attribute as an index attribute. This row will be indexed even when all primary keys exist despite the pre-defined attribute being excluded from the base table row. However, this row will not be indexed when a row excludes one or more indexed attributes. For example, a base table includes three primary keys that are PK0, PK1, and PK2. Additionally, the table have three pre-defined attributes Defined0, Defined1, and Defined2. You can create an index on Defined0 and Defined1, and specify Defined2 as an attribute.
    -   An index will include a row in a base table that excludes the Defined2 attribute and includes pre-defined attributes Defined0 and Defined1.
    -   This row is excluded from the index when a base table row excludes Defined1 but includes the pre-defined attributes Defined0 and Defined2.
-   Supports creating and deleting indexes on an existing base table. In later versions, existing data in a base table will be copied to an index when you create this index on the base table.
-   When you query an index, the query is not automatically performed on the base table of the created index. You need to query the base table. This feature will be supported in later versions.

The Table Store global secondary index feature is now available in China \(Zhangjiakou\) region. You can contact Table Store technical support by DingTalk for a trial or enter the ID 111789671 to join the DingTalk group for further information.

