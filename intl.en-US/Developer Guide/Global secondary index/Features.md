# Features

This topic describes the features of global secondary index.

-   Data can be asynchronously synchronized from the base table to index tables within milliseconds under normal network conditions.
-   Supports single-column indexes and compound indexes.
-   Supports indexed attribute columns.

    Predefined columns are created when you create a table. You can create an index table based on a combination of any predefined columns and primary key columns. You can also specify predefined columns as attribute columns in the index table or choose not to specify indexed attribute columns.

    If you specify one or more predefined columns of a base table as the attribute columns of an index table,you can query the index table without querying the base table to obtain values of the corresponding predefined columns. For example, a base table includes three primary key columns named PK0, PK1, and PK2 and three predefined columns named Defined0, Defined1, and Defined2.

    -   You can create an index table based on PK2 without specifying an indexed attribute column or setting Defined0 to an indexed attribute column.
    -   You can create an index table based on PK1 and PK2 without specifying an indexed attribute column or setting Defined0 to an indexed attribute column.
    -   You can create an index table based on PK2, PK1, and PK0 and set Defined0, Defined1, and Defined2 to indexed attribute columns.
    -   You can create an index table based on Defined0 without specifying an indexed attribute column.
    -   You can create an index table based on Defined0 and PK1 and set Defined1 to an indexed attribute column.
    -   You can create an index table based on Defined1 and Defined0 without specifying an indexed attribute column or setting Defined2 to an indexed attribute column.
-   Supports sparse indexes.

    You can specify a predefined column as an indexed attribute column. A row is indexed when all indexed columns exist even if the indexed attribute column of the row does not exist in the base table. However, a row is not indexed when the row excludes one or more indexed columns. For example, a base table includes the primary key columns PK0, PK1, and PK2 and the predefined columns Defined0, Defined1, and Defined2. You can create an index table based on Defined0 and Defined1, set Defined0 and Defined1 to primary key columns and set Defined2 to an indexed attribute column.

    -   The index table includes a row in the base table that excludes the Defined2 and includes Defined0 and Defined1.
    -   The index table excludes a row in the base table that includes Defined0 and Defined2 but excludes Defined1.
-   Supports the deletion or creation of index tables for an existing base table.
-   Supports adding the existing data in the base table to a new index table.

