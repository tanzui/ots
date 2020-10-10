# Configure conditional update

If you use conditional update, data in the table can be updated only when the conditions are met. If the conditions are not met, the update fails.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Usage notes

When you use the PutRow, UpdateRow, DeleteRow, or BatchWriteRow operation to update data, you can use conditional update to check row existence conditions and column-based conditions. The update is successful only when the conditions are met.

Conditional update includes row existence conditions and column-based conditions.

-   Row existence conditions: include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST.

    When you modify a table, the system first checks the row existence condition. If the row existence condition is not met, the modification fails, and an error is reported.

-   Column-based conditions: include SingleColumnValueCondition and CompositeColumnValueCondition, which are used to determine whether the conditions are met based on the values of one or more columns.
    -   SingleColumnValueCondition supports the comparison between a constant and a column that can be a primary key column. SingleColumnValueCondition does not support the comparison between two columns or two constants.
    -   The inner node of CompositeColumnValueCondition is used to perform logical operations. Subconditions can be SingleColumnValueCondition or CompositeColumnValueCondition.

Conditional update can be used to implement optimistic locking. When you update a row, the value of the specified column is obtained. Assume that Column A has a value of 1. Obtain the value in Column A and set a condition that the value in Column A is 1. Update the value in Column A to 2. If the update fails, the row is updated by another client.

## Limits

Column-based conditions for conditional update support relational operators such as =, !=, \>, \>=, <, and <=, and logical operators such as NOT, AND, and OR. You can specify a maximum of 10 conditions.

## Parameters

You can use PutRow, UpdateRow, DeleteRow, and BatchWriteRow.for conditional update.

```
    'condition' => [
        'row_existence' => <RowExistenceExpectation>
        'column_condition' => <ColumnCondition>
    ]   
```

When only a row existence condition exists, it can be abbreviated:

```
    'condition' => <RowExistenceExpectation>    
```

-   The following code provides an example on how to construct SingleColumnValueCondition:

    ```
        [
            'column_name' => '<string>',
            'value' => <ColumnValue>,
            'comparator' => <ComparatorType>
            'pass_if_missing' => true || false
            'latest_version_only' => true || false
        ]
            
    ```

-   The following code provides an example on how to construct CompositeColumnValueFilter:

    ```
        [
            'logical_operator' => <LogicalOperator>
            'sub_conditions' => [
                <ColumnCondition>,
                <ColumnCondition>,
                <ColumnCondition>,
                // other conditions
            ]
        ]
            
    ```


|Parameter|Description|
|---------|-----------|
|row\_existence|When you modify a table, the system first checks the row existence condition. If the row existence condition is not met, the modification fails, and an error is reported.Row existence conditions include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST. In Tablestore, RowExistenceExpectationConst::CONST\_IGNORE indicates IGNORE, RowExistenceExpectationConst::CONST\_EXPECT\_EXIST indicates EXPECT\_EXIST, and RowExistenceExpectationConst::CONST\_EXPECT\_NOT\_EXIST indicates EXPECT\_NOT\_EXIST.

-   IGNORE: No existence check is performed.
-   EXPECT\_EXIST: The row is expected to exist. If the row exists, the condition is met. If the row does not exist, the condition is not met.
-   EXPECT\_NOT\_EXIST: Expect that the row does not exist. If the row does not exist, the condition is met. If the row exists, the condition is not met. |
|column\_name|The column name.|
|value|The comparison value of the column.The parameter value is in the format of \[Value, Type\]. Type can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types. If the type is BINARY, the type must be specified. Otherwise, the type can be ignored.

If the type is BINARY, you must set both the type and value. |
|comparator|The relational operator that is used to compare column values. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).Relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). In Tablestore, ComparatorTypeConst::CONST\_EQUAL indicates EQUAL \(=\), ComparatorTypeConst::CONST\_NOT\_EQUAL indicates NOT\_EQUAL \(!=\), ComparatorTypeConst::CONST\_GREATER\_THAN indicates GREATER\_THAN \(\>\), ComparatorTypeConst::CONST\_GREATER\_EQUAL indicates GREATER\_EQUAL \(\>=\), ComparatorTypeConst::CONST\_LESS\_THAN indicates LESS\_THAN \(<\), and ComparatorTypeConst::CONST\_LESS\_EQUAL indicates LESS\_EQUAL \(<=\). |
|logical\_operator|The logical operator that is used to combine multiple conditions. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).Logical operators include NOT, AND, and OR. In Tablestore, LogicalOperatorConst::CONST\_NOT indicates NOT, LogicalOperatorConst::CONST\_AND indicates AND, and LogicalOperatorConst::CONST\_OR indicates OR.

Different logical operators require different number of subconditions.

-   When the logical operator is NOT, only one subcondition can be added.
-   When the logical operator is AND or OR, you must add at least two subconditions. |
|pass\_if\_missing|Specifies whether to pass the conditional check when a column does not exist in a row. The type of this parameter value is Boolean. The default value is true, which indicates that if the column does not exist in a row, the conditional check is passed, and the row meets the update conditions.When pass\_if\_missing is set to false, if the column does not exist in a row, the conditional check fails, and the row does not meet the update conditions. |
|latest\_version\_only|Specifies whether to use only the value of the latest version when the comparison value has multiple versions. The type of this parameter value is Boolean. The default value is true, which indicates that if the column has multiple versions of data, only the value of the latest version is used for comparison.When latest\_version\_only is set to false, the comparison values of all versions are used for comparison if the comparison value has multiple versions. In this case, if only the value of a version meets the condition, the conditional check is passed, and the row meets the update conditions. |

## Examples

-   The following code provides an example on how to construct SingleColumnValueCondition:

    ```
        // Set conditional update. If the value of Col0 is 0, the conditional check is passed.
        $column_condition = [
            'column_name' => 'Col0',
            'value' => 0,
            'comparator' => ComparatorTypeConst::CONST_EQUAL 
            'pass_if_missing' => false                         // If a row does not contain the Col0 column, the conditional check fails.
            'latest_version_only' => true                      // Specify that only the latest version is used for comparison.
        ];
            
    ```

-   The following code provides an example on how to construct CompositeColumnValueCondition:

    ```
    // Set condition composite1 to (Col0 == 0) AND (Col1 > 100).
        $composite1 = [
            'logical_operator' => LogicalOperatorConst::CONST_AND,
            'sub_conditions' => [
                [
                    'column_name' => 'Col0',
                    'value' => 0,
                    'comparator' => ComparatorTypeConst::CONST_EQUAL
                ],
                [
                    'column_name' => 'Col1',
                    'value' => 100,
                    'comparator' => ComparatorTypeConst::CONST_GREATER_THAN
                ]
            ]
        ];
        // Set condition composite2 to ( (Col0 == 0) AND (Col1 > 100) ) OR (Col2 <= 10).
        $composite2 = [
            'logical_operator' => LogicalOperatorConst::CONST_OR,
            'sub_conditions' => [
                $composite1,
                [
                    'column_name' => 'Col2',
                    'value' => 10,
                    'comparator' => ComparatorTypeConst::CONST_LESS_EQUAL
                ]
            ]
        ];
    ```

-   The following code provides an example on how to implement optimistic locking by increasing the value of a column:

    ```
        // Read a row of data.
        $request = [
            'table_name' => 'MyTable',
            'primary_key' => [ // The primary key.
                ['PK0', 123],
                ['PK1', 'abc']
            ],
            'max_versions' => 1
        ];
        $response = $otsClient->getRow ($request);
        $columnMap = getColumnValueAsMap($response['attribute_columns']);
        $col0Value = $columnMap['col0'][0][1];
        // Configure conditional update for Col0 to increase the value of Col0 by 1.
        $request = [
            'table_name' => 'MyTable',
            'condition' => [
                'row_existence' => RowExistenceExpectationConst::CONST_EXPECT_EXIST,
                'column_condition' => [                  // If the condition is met, the data is updated.
                    'column_name' => 'col0',
                    'value' => $col0Value,
                    'comparator' => ComparatorTypeConst::CONST_EQUAL
                ]
            ],
            'primary_key' => [ // The primary key.
                ['PK0', 123],
                ['PK1', 'abc']
            ],
            'update_of_attribute_columns'=> [
                'PUT' => [
                    ['col0', $col0Value+1]
                ]
            ]
        ];
        $response = $otsClient->updateRow ($request);
    ```


