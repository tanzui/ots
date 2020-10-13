# Auto-increment function of the primary key column

This topic describes how to create an auto-increment primary key column. An auto-increment primary key column cannot be the partition key. If you write data to a table that contains an auto-increment primary key column, you do not need to specify a value for the auto-increment primary key column because Tablestore generates a value for the auto-increment primary key column. The generated value for the auto-increment primary key column is unique, and all values in auto-increment primary key columns increase sequentially in a partition.

## Feature

The auto-increment function of the primary key column has the following features:

-   The values in auto-increment primary key columns are unique and increase sequentially but not always continuously in a partition.
-   The data type of the value in an auto-increment primary key column is 64-bit signed long integer.
-   You can create an auto-increment primary key column for a table. Tables that contain and do not contain auto-increment primary key columns can be in the same instance.

**Note:** The auto-increment function of the primary key column does not affect the rule of conditional update. For more information about conditional update, see [Conditional update](/intl.en-US/Developer Guide/Wide Column model/Conditional update.md).

## Limits

The auto-increment function of the primary key column has the following limits:

-   You can create at most one auto-increment primary key column for a table. An auto-increment primary key column cannot be the partition key.
-   You can create an auto-increment primary key column only when you create a table. You cannot create an auto-increment primary key column for an existing table.
-   An auto-increment primary key column can only be an integer column. The generated value for an auto-increment primary key column is a 64-bit signed long integer.
-   An auto-increment column cannot be an attribute column.

## Operations

The following table describes the operations for the auto-increment function of the primary key column.

|Operation|Description|
|---------|-----------|
|CreateTable|When you create a table, you cannot set the partition key to be an auto-increment primary key column. If you set the partition key to be an auto-increment primary key column, values in the column cannot be generated automatically.|
|UpdateTable|After a table is created, you cannot use the UpdateTable operation to change a primary key column to an auto-increment primary key column.|
|PutRow|When you write data to a table, you need only to specify a placeholder for the auto-increment primary key column but not to specify a value for the auto-increment primary key column. Tablestore generates a value for the auto-increment primary key column. You can set ReturnType to RT\_PK to obtain a complete primary key value and use the value in GetRow to query data. |
|UpdateRow|
|BatchWriteRow|
|GetRow|You must use a complete primary key value when you use GetRow. To obtain a complete primary key value, you can set ReturnType to RT\_PK in PutRow, UpdateRow, or BatchWriteRow.|
|BatchGetRow|

## Usage

You can use the following SDKs to implement the auto-increment function of the primary key column:

-   SDK for Java: [The auto-increment function of the primary key column](/intl.en-US/SDK Reference/Java SDK/Table/Configure an auto-increment primary key column.md)
-   SDK for PHP: [The auto-increment function of the primary key column](/intl.en-US/SDK Reference/PHP SDK/Table operations/Configure an auto-increment primary key column.md)

## Billing methods

The implementation of the auto-increment function of the primary key column does not affect the current billing rules. Values of returned primary key columns does not consume additional read CUs.

