# Configure conditional update

If you use conditional update, data in the table can be updated only when the conditions are met. If the conditions are not met, the update fails.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
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
|RowExistenceExpectation|When you modify a table, the system first checks the row existence condition. If the row existence condition is not met, the modification fails and an error is reported.Row existence conditions include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST. In Tablestore, RowExistenceExpectation\_IGNORE indicates IGNORE, RowExistenceExpectation\_EXPECT\_EXIST indicates EXPECT\_EXIST, and RowExistenceExpectation\_EXPECT\_NOT\_EXIST indicates EXPECT\_NOT\_EXIST.

-   IGNORE: No existence check is performed.
-   EXPECT\_EXIST: The row is expected to exist. If the row exists, the condition is met. If the row does not exist, the condition is not met.
-   EXPECT\_NOT\_EXIST: Expect that the row does not exist. If the row does not exist, the condition is met. If the row exists, the condition is not met. |
|column\_name|The column name.|
|column\_value|The comparison value of the column.|
|comparator|The relational operator that is used to compare column values. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).Relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). In Tablestore, CT\_EQUAL indicates EQUAL \(=\), CT\_NOT\_EQUAL indicates NOT\_EQUAL \(!=\), CT\_GREATER\_THAN indicates GREATER\_THAN \(\>\), CT\_GREATER\_EQUAL indicates GREATER\_EQUAL \(\>=\), CT\_LESS\_THAN indicates LESS\_THAN \(<\), and CT\_LESS\_EQUAL indicates LESS\_EQUAL \(<=\). |
|combinator|The logical operator that is used to combine multiple conditions. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).Logical operators include NOT, AND, and OR. In Tablestore, LO\_NOT indicates NOT, LO\_AND indicates AND, and LO\_OR indicates OR.

Different logical operators require different number of subconditions.

-   When the logical operator is NOT, only one subcondition can be added.
-   When the logical operator is AND or OR, you must add at least two subconditions. |
|pass\_if\_missing|Specifies whether to pass the conditional check when a column does not exist in a row. The type of this parameter value is Boolean. The default value is True, which indicates that if the column does not exist in a row, the conditional check is passed, and the row meets the update conditions.When pass\_if\_missing is set to False, if the column does not exist in a row, the conditional check fails, and the row does not meet the update conditions. |
|latest\_version\_only|Specifies whether to use only the value of the latest version when the comparison value has multiple versions. The type of this parameter value is Boolean. The default value is True, which indicates that if the column has multiple versions of data, only the value of the latest version is used for comparison.When latest\_version\_only is set to False, the comparison values of all versions are used for comparison if the comparison value has multiple versions. In this case, if only the value of a version meets the condition, the conditional check is passed, and the row meets the update conditions. |

## Examples

Update a row based on the specified primary key. If the specified row exists and value of the age column in the specified row is 20, the update succeeds. Otherwise, the update fails.

```
    primary_key = [('gid',1), ('uid',"101")]
    update_of_attribute_columns = {
        'PUT' : [('name','David'), ('address','Hongkong')],
        'DELETE' : [('address', None, 1488436949003)],
        'DELETE_ALL' : [('mobile'), ('age')],
        'INCREMENT' : [('counter', -1)]
    }
    row = Row(primary_key, update_of_attribute_columns)

    # Specify that the update succeeds only when the following two conditions are met. Otherwise, the update fails.
    # 1 The specified row exists.
    # 2 The value of the age column in the specified row is 20.
    condition = Condition(RowExistenceExpectation.EXPECT_EXIST, SingleColumnCondition("age", 20, ComparatorType.EQUAL)) # Update the row only when this row exists.
    consumed, return_row = client.update_row(table_name, row, condition)
```

