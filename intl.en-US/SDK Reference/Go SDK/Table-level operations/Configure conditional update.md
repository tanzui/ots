# Configure conditional update

If you use conditional update, data in the table can be updated only when the conditions are met. If the conditions are not met, the update fails.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Usage notes

When you use the PutRow, UpdateRow, DeleteRow, or BatchWriteRow operation to update data, you can use conditional update to check row existence conditions and column-based conditions. The update is successful only when the conditions are met.

Conditional update includes row existence conditions and column-based conditions.

-   Row existence conditions: include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST.

    When you modify a table, the system first checks the row existence condition. If the row existence condition is not met, the modification fails, and an error is reported.

-   Column-based conditions: include SingleColumnCondition and CompositeColumnCondition, which are used to determine whether the conditions are met based on the values of one or more columns.
    -   SingleColumnCondition supports the comparison between a constant and a column that can be a primary key column. SingleColumnCondition does not support the comparison between two columns or two constants.
    -   The inner node of CompositeColumnCondition is used to perform logical operations. Subconditions can be SingleColumnCondition or CompositeColumnCondition.

Conditional update can be used to implement optimistic locking. When you update a row, the value of the specified column is obtained. Assume that Column A has a value of 1. Obtain the value in Column A and set a condition that the value in Column A is 1. Update the value in Column A to 2. If the update fails, the row is updated by another client.

## Limits

Column-based conditions for conditional update support relational operators such as =, !=, \>, \>=, <, and <=, and logical operators such as NOT, AND, and OR. You can specify a maximum of 10 conditions.

## Parameters

|Parameter|Description|
|---------|-----------|
|RowExistenceExpectation|When you modify a table, the system first checks the row existence condition. If the row existence condition is not met, the modification fails, and an error is reported.Row existence conditions include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST. In Tablestore, RowExistenceExpectation\_IGNORE indicates IGNORE, RowExistenceExpectation\_EXPECT\_EXIST indicates EXPECT\_EXIST, and RowExistenceExpectation\_EXPECT\_NOT\_EXIST indicates EXPECT\_NOT\_EXIST.

-   IGNORE: No existence check is performed.
-   EXPECT\_EXIST: The row is expected to exist. If the row exists, the condition is met. If the row does not exist, the condition is not met.
-   EXPECT\_NOT\_EXIST: Expect that the row does not exist. If the row does not exist, the condition is met. If the row exists, the condition is not met. |
|ColumnName|The column name.|
|ColumnValue|The comparison value of the column.|
|Comparator|The relational operator that is used to compare column values. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).Relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). In Tablestore, CT\_EQUAL indicates EQUAL \(=\), CT\_NOT\_EQUAL indicates NOT\_EQUAL \(!=\), CT\_GREATER\_THAN indicates GREATER\_THAN \(\>\), CT\_GREATER\_EQUAL indicates GREATER\_EQUAL \(\>=\), CT\_LESS\_THAN indicates LESS\_THAN \(<\), and CT\_LESS\_EQUAL indicates LESS\_EQUAL \(<=\). |
|Operator|The logical operator that is used to combine multiple conditions. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).Logical operators include NOT, AND, and OR. In Tablestore, LO\_NOT indicates NOT, LO\_AND indicates AND, and LO\_OR indicates OR.

Different logical operators require different number of subconditions.

-   When the logical operator is NOT, only one subcondition can be added.
-   When the logical operator is AND or OR, you must add at least two subconditions. |
|FilterIfMissing|Specifies whether to pass the conditional check when a column does not exist in a row. The type of this parameter value is Boolean. The default value is true, which indicates that if the column does not exist in a row, the conditional check is passed, and the row meets the update conditions.When FilterIfMissing is set to false, if the column does not exist in a row, the conditional check fails, and the row does not meet the update conditions. |
|LatestVersionsOnly|Specifies whether to use only the value of the latest version when the comparison value has multiple versions. The type of this parameter value is Boolean. The default value is true, which indicates that if the column has multiple versions of data, only the value of the latest version is used for comparison.When LatestVersionsOnly is set to false, the comparison values of all versions are used for comparison if the comparison value has multiple versions. In this case, if only the value of a version meets the condition, the conditional check is passed, and the row meets the update conditions. |

## Examples

-   Example 1

    Update a row based on the specified primary key. If the specified row exists, the update succeeds. Otherwise, the update fails.

    ```
        updateRowRequest := new(tablestore.UpdateRowRequest)
        updateRowChange := new(tablestore.UpdateRowChange)
        updateRowChange.TableName = tableName
        updatePk := new(tablestore.PrimaryKey)
        updatePk.AddPrimaryKeyColumn("pk1", "pk1value1")
        updatePk.AddPrimaryKeyColumn("pk2", int64(2))
        updatePk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
        updateRowChange.PrimaryKey = updatePk
        updateRowChange.DeleteColumn("col1")            // Delete the col1 column.
        updateRowChange.PutColumn("col2", int64(77))    // Add the col2 column whose value is 77.
        updateRowChange.PutColumn("col4", "newcol3")    // Add the col4 column whose value is "newcol3".
        // Expect that the specified row exists.
        updateRowChange.SetCondition(tablestore.RowExistenceExpectation_EXPECT_EXIST)
        updateRowRequest.UpdateRowChange = updateRowChange
        _, err := client.UpdateRow(updateRowRequest)
    ```

-   Example 2

    Delete a row based on the specified primary key. If the specified row exists and the value of col2 is 3, the update succeeds. Otherwise, the update fails.

    ```
        deleteRowReq := new(tablestore.DeleteRowRequest)
        deleteRowReq.DeleteRowChange = new(tablestore.DeleteRowChange)
        deleteRowReq.DeleteRowChange.TableName = tableName
        deletePk := new(tablestore.PrimaryKey)
        deletePk.AddPrimaryKeyColumn("pk1", "pk1value1")
        deletePk.AddPrimaryKeyColumn("pk2", int64(2))
        deletePk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
        deleteRowReq.DeleteRowChange.PrimaryKey = deletePk
    
        // Expect that the specified row exists.
        deleteRowReq.DeleteRowChange.SetCondition(tablestore.RowExistenceExpectation_EXPECT_EXIST)
        // Expect that the value in the col2 column is 3.
        clCondition1 := tablestore.NewSingleColumnCondition("col2", tablestore.CT_EQUAL, int64(3))
        deleteRowReq.DeleteRowChange.SetColumnCondition(clCondition1)
        _, err := client.DeleteRow(deleteRowReq)
    ```


