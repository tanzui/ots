# Billing rules {#concept_nnw_b1f_zgb .concept}

To use secondary indexes, index tables are needed. Therefore, additional storage space is required to store index tables. When the system inserts data to a primary table, it may also need to write the index tables created on the primary table at the same time. During this process, read and write CUs are consumed. This topic describes the billing rules for secondary indexes.

**Note:** Capacity units \(CUs\) are read and write throughput units. They are the smallest units used to measure the costs of read and write operations For example, when the system reads 4 KB from one row per second, one read CU is consumed.

To use secondary indexes, index tables are needed. Therefore, additional storage space is required to store index tables. When the system inserts data to a primary table, it may also need to write the index tables created on the primary table at the same time. During this process, read and write CUs are consumed.

Secondary index billing includes the following parts: the number of read and write CUs consumed to write index tables, the amount of data stored in the index tables, and the amount of data that is read from the index tables.

|Billing item|Description|
|------------|-----------|
|Data storage|The storage space used to store a primary table and its index tables.|
|Read CUs consumed to write index tables|The number of CUs that are consumed by read operations to delete, insert, or update index rows.|
|Write CUs consumed to write index tables|The number of CUs that are consumed to insert or update index rows.|
|CUs consumed by regular read operations|The number of CUs that are consumed to read data from a primary table or index tables using an API.|
|CUs consumed by regular write operations|The number of CUs that are consumed to insert data to a primary table using an API.|

Billing rules for storing, writing, and reading an index table:

-   The billing rules for storing and reading an index table are the same as those of a primary table. For more information, see [Billing items and pricing](../../../../../intl.en-US/Pricing/Billing items and pricing.md#).
-   CUs are consumed based on the following rules when the system writes an index table:
    -   Write CUs are consumed only when an index row is inserted or updated.
    -   Read CUs are consumed when an index row is deleted, updated, or inserted. The number of read CUs equals the amount of data read from the corresponding indexed columns in the primary table.

## Calculate the number of read CUs consumed to write index tables {#section_j2w_xcf_zgb .section}

When you create secondary indexes on the primary table, read CUs are consumed based on the following rules:

-   When you use the PUT operation to insert a data row to the primary table:
    -   The PUT operation does not insert data to the indexed attribute columns in the primary table, which means that no index row is inserted. In this case, one read CU is consumed.
    -   The PUT operation inserts data to the indexed attribute columns in the primary table, which means that new index rows are inserted. In this case, one read CU is consumed.
-   When you use the PUT operation to overwrite a row in the primary table:
    -   The PUT operation does not update the indexed attribute columns in the primary table. In this case, one read CU is consumed.
    -   The PUT operation updates the indexed attribute columns in the primary table. In this case, the read CUs are consumed as follows:

        Divide the total amount of data read from the indexed attribute columns by four, excluding primary key columns. The number of consumed CUs equals the calculated value rounded up to the nearest integer. If the total amount is 0 KB, one CU is consumed.

-   When you use the UPDATE operation to insert a data row to the primary table:
    -   If the UPDATE operation does not insert data to the indexed columns in the primary table, no read CU is consumed.
    -   If the UPDATE operation inserts data to the indexed columns in the primary table, one read CU is consumed.
-   When you use the UPDATE operation to update a row in the primary table:
    -   If the UPDATE operation does not insert data to the indexed attribute columns in the primary table, no read CU is consumed.
    -   If the UPDATE operation inserts data to the indexed attribute columns in the primary table, read CUs are consumed based on the following rules:

        Divide the total amount of data read from the indexed columns by four, excluding the primary key columns. The number of consumed CUs equals the calculated value rounded up to the nearest integer. If the total amount is 0 KB, one CU is consumed.

-   When you use the Delete operation to delete a row in the primary table, read CUs are consumed based on the following rules:

    Divide the total amount of data read from the indexed columns by four, excluding the primary key columns. The number of consumed CUs equals the calculated value rounded up to the nearest integer. If the total amount is 0 KB, one CU is consumed.

-   If the primary table uses primary key auto increment, inserting data to the primary table does not consume any read CUs. Updating a row in a primary table that uses primary key auto increment consumes read CUs. CUs are calculated based on the same rules as those of the UPDATE operation.

    **Note:** We recommend that you use primary key auto increment to insert data to a primary table to decrease the number of CUs that are consumed by index tables.

    For primary tables that do not use primary key auto increment, one read CU is consumed if a read operation is performed on the indexed columns, even if no data is retrieved. For primary tables that use primary key auto increment, no read operation is performed on the indexed columns when you insert data. Therefore, no read CU is consumed.


## Calculate the number of write CUs {#section_cm5_ycf_zgb .section}

When you insert data to the primary table and create secondary indexes, write CUs are consumed. Write CUs are consumed based on the following rules:

-   If you insert a row to the primary table and no data in the index table is updated, no write CUs are consumed.
-   If you insert a row to the primary table and a new index row is inserted to the index table, write CUs are consumed. The number of the write CUs is determined by the size of the inserted index row.
-   If you insert a row to the primary table and an index row is deleted from the index table, write CUs are consumed. The number of the write CUs is determined by the size of the deleted index row.
-   If you insert a row to the primary table and an index row in the index table is updated, write CUs are consumed. The number of the write CUs is determined by the size of the updated index row.
-   If you insert a row to the primary table, an index row is deleted from the index table, and another index row is inserted to the index table, write CUs are consumed. The number of the write CUs is determined by the total size of the deleted and inserted index rows.

The detailed rules are as follows:

-   When you use the PUT operation to insert a data row to a primary table:
    -   The PUT operation does not insert data to the indexed attribute columns in the primary table, which means that no index row is inserted. In this case, no read CU is consumed.
    -   The PUT operation inserts data to the indexed attribute columns in the primary table, which means that new index rows are inserted. The write CUs consumed for each index table are calculated as follows:

        Divide the total amount of data in the inserted index row by four. The number of consumed CUs equals the calculated value rounded up to the nearest integer.

-   When you use the PUT operation to overwrite a row in the primary table:
    -   The PUT operation only updates the indexed primary key columns in the primary table. In this case, no write CUs are consumed.
    -   The PUT operation updates the indexed columns in the primary table. The write CUs are consumed based on the following rules:

        All indexes updated by the PUT operation consume a certain number of write CUs, except sparse indexes.

-   When you use the UPDATE operation to insert a data row to the primary table:
    -   If the UPDATE operation does not insert data to the indexed columns in the primary table, no write CUs are consumed.
    -   If the UPDATE operation inserts data to the indexed columns in the primary table, the write CUs consumed for each index table are calculated as follows:
        -   If the UPDATE operation inserts a new index row, write CUs are consumed. Divide the total size of the data in the index row by four. The number of consumed CUs equals the calculated value rounded up to the nearest integer.
        -   If no index row is inserted, no write CUs are consumed.
-   When you use the UPDATE operation to update a row in the primary table:
    -   If the UPDATE operation does not update the indexed attribute columns, no write CUs are consumed.
    -   If the UPDATE operation updates the indexed attribute columns, write CUs consumed for each index table are calculated based on the following rules:

        -   If the index table already contains an index row created based on the row to be updated, delete CUs are consumed. The number of the delete CUs is determined by the size of the indexed primary keys in the deleted index row.
        -   If a new index row is inserted based on the updated row, write CUs are consumed. The number of the write CUs is determined by the size of the indexed primary keys in the inserted index row.
        -   If the UPDATE operation only updates the attribute data in the existing index row but no new index row is inserted, update CUs are consumed.
        Divide the total amount of data in the index row by four. The number of consumed CUs equals the calculated value rounded up to the nearest integer.

-   When you use the DELETE operation to delete a row in the primary table, write CUs are consumed based on the following rules:

    If an index table already contains an index row created based on the row to be deleted, write CUs are consumed. Divide the total amount of the data in the corresponding indexed columns by four, excluding the primary key columns. The consumed write CUs equal the calculated value rounded up to the nearest integer.

-   If you insert data to a primary table that uses primary key auto increment, write CUs are consumed. The write CUs are calculated based on the same rules as those of the PUT operation. If you update a row in a primary table that uses primary key auto increment, write CUs are consumed. The write CUs are calculated based on the same rules as those of the UPDATE operation.

## Measure index table size {#section_yy2_1df_zgb .section}

The size of an index table is measured based on the same rule as that of a primary table. The size of an index table equals the total size of all rows. The total size of the rows equals the total size of primary keys and attribute data. For more information, see [Data storage](../../../../../intl.en-US/Pricing/Data storage.md#).

## Calculate the number of CUs consumed to read an index table {#section_ol5_1df_zgb .section}

When you use an SDK, the console, or other methods, such as a DLA, to read an index table, read CUs are consumed. The number of read CUs are calculated based on the same rules as those of reading a primary table.

## Examples {#section_nml_cdf_zgb .section}

The following example uses a primary table that has two index tables to describe how CUs are consumed under different conditions.

The primary table Table contains two primary key columns PK0 and PK1, and three predefined columns Col0, Col1, and Col2. Two index tables, Index0 and Index1, are created on the primary table. Index0 contains three primary keys Col0, PK0, and PK1 and one attribute column Col2. Index1 contains four primary keys Col1, Col0, PK0, and PK1, and no attribute columns. Use the UPDATE operation to update PK0 and PK1.

-   If the target row does not exist in the primary table:
    -   Updating Col3 does not consume read or write CUs.
    -   Updating Col1 consumes the following CUs:
        -   One read CU
        -   No write CUs
    -   Updating Col0 and Col1 consumes the following CUs:
        -   One read CU
        -   Index0 consumes write CUs. The number of the write CUs is determined by the total amount of data inserted to Col0, PK0, and PK1. Index1 consumes write CUs. The number of the write CUs is determined by the total amount of data inserted to Col0, Col1, PK0, and PK1.
-   If the target row already exists in the primary table:
    -   Updating Col3 does not consume read or write CUs.
    -   Updating Col2 consumes the following CUs:
        -   Read CUs are consumed. The number of the read CUs is determined by the amount of data read from Col0. If the UPDATE operation inserts data to Col0, one CU is consumed.
        -   For Index0, if the UPDATE operation insets data to Col0, Index0 does not consume write CUs. If the UPDATE operation updates the data in Col0, Index0 consumed write CUs. The number of the write CUs is determined by the total amount of data inserted to Col0, PK0, PK1, and Col2. Index1 does not consume write CUs.
    -   Updating Col1 consumes the following CUs:
        -   Read CUs are consumed. The number of the read CUs is determined by the amount of data read from Col0 and Col1. If the total amount is 0 KB, one CU is consumed.
        -   Index0 does not consume write CUs. For Index1, if an index row is inserted, write CUs are consumed. The number of the write CUs is determined by the amount of data read from Col0 and inserted to Col1, PK0, and PK1. For Index1, if no data in Col0 is updated, no index row is inserted and no write CUs are consumed. If the data in Col0 and Col1 is updated, write CUs are consumed to delete the corresponding index row. The number of write CUs is determined by the total amount of data read from Col0, Col1, PK0, and PK1.

