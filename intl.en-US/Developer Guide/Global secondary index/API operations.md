# API operations

This topic describes the operations you can use to perform global secondary index.

The following table describes the operations you can use to perform global secondary index.

|API operations|Description|
|--------------|-----------|
|CreateIndex|Create a global secondary index for an existing table.**Note:**

-   You can specify whether an index table created by using CreateIndex includes the existing data of the base table.
-   You can create one or more index tables when you create a base table by using CreateTable. |
|GetRow|Read a row of data from an index table.|
|GetRange|Read data within a specified range from an index table.|
|DeleteIndex|Delete the specified index table of a base table.**Note:** You must delete the index tables before you call the DeleteTable operation to delete the base table. Otherwise, the base table fails to be deleted. |

