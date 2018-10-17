# Auto-increment function of the primary key column {#concept_47745_zh .concept}

If you set a primary key column as an auto-increment column, you do not need to enter this column when writing data in a row. Instead, Table Store automatically generates the primary key value, which is unique in the partition key, and which increases progressively.

## Features {#section_cz5_rjl_cfb .section}

Table Store, in conjunction with the auto-increment function of an primary key column, has the following features:

-   The system architecture exclusive to Table Store and the implementation through an auto-increment primary key column make sure that the value generated for the auto-incrementing column is unique and strictly incrementing.
-   The automatically generated auto-increment column value is a 64-bit signed long integer.
-   The level of the partition key increases progressively.
-   The auto-increment function is a table level. The tables with an auto-increment column and the tables without an auto-increment column can be created in the same instance.

If the auto-increment primary key column is set, the conditional update logic is not changed. See the following table for more information.

|API|IGNORE|EXPECT\_EXIST|EXPECT\_NOT\_EXIST|
|:--|:-----|:------------|:-----------------|
|PutRow: The row exists.|Fail|Succeed|Fail|
|PutRow: The row does not exist.|Succeed|Fail|Fail|
|UpdateRow: The row exists.|Fail|Succeed|Fail|
|UpdateRow: The row does not exist.|Succeed|Fail|Fail|
|DeleteRow: The row exists.|Fail|Fail|Fail|
|DeleteRow: The row does not exist.|Succeed|Succeed|Fail|

## Limits {#section_vmn_mjl_cfb .section}

Table Store Auto-increment function of the primary key column mainly has the following restrictions:

-   Table Store supports multiple primary keys. The first primary key is a partition key that cannot be set as an auto-increment column. However, one of other primary keys can be set as an auto-increment column.
-   Only one primary key per table can be set as an auto-increment column.
-   The attribute column cannot be set as an auto-increment column.
-   The auto-increment column can only be set at the time the table is created. The existing table cannot set the auto-increment column.

## Interface { .section}

-   CreateTable
    -   Set a column as an auto-incrementing column during table creation. For more information, see [Primary key column auto-increment](../../../../reseller.en-US/SDK Reference/Java SDK/Primary key column auto-increment.md#).
    -   After table creation, you cannot configure the auto-incrementing feature of the table.
-   UpdateTable

    You cannot change the auto-increment attribute of a table by using UpdateTable.

-   PutRow/UpdateRow/BatchWriteRow
    -   When writing the table, you do not need to set specific values for the column that you want to set as auto-incrementing. You only need to set a placeholder, for example, AUTO\_INCREMENT. For more information, see [Primary key column auto-increment](../../../../reseller.en-US/SDK Reference/Java SDK/Primary key column auto-increment.md#).
    -   You can set ReturnType in ReturnContent as RT\_PK, that is, to return the complete primary key value, which can be used in the GetRow query.
-   GetRow/BatchGetRow

    GetRow requires a complete primary key column, which can be obtained by setting ReturnType in PutRow, UpdateRow, or BatchWriteRow as RT\_PK.

-   Other interfaces

    Not changed


## Usage { .section}

 [Java SDK: Auto-increment of the primary key column](../../../../reseller.en-US/SDK Reference/Java SDK/Primary key column auto-increment.md#) 

## Billing { .section}

The auto-increment function of primary key columns does not affect the existing billing logic. Returned data of the primary key column does not consume additional read CUs.

