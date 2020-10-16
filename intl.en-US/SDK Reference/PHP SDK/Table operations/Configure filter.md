# Configure filter

Tablestore provides filter to filter the results on the server and return the rows that match the filter conditions. After results are filtered, only rows of data that matches the filter condition are returned.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Usage notes

When you query data by calling the GetRow, BatchGetRow, or GetRange operation, you can use the filter to return only the rows that meet the filter conditions.

Filters include SingleColumnValueFilter and CompositeColumnValueFilter.

-   SingleColumnValueFilter: determines whether to filter a row based only on the values of a reference column.
-   CompositeColumnValueFilter: determines whether to filter a row by combining the filter conditions of values of multiple reference columns.

## Limits

-   The filter conditions support relational operators such as =, !=, \>, \>=, <, and <=, and logical operators such as NOT, AND, and OR. You can specify a maximum of 10 conditions.
-   Reference columns in filters must be included in the read result. If the specified columns from which data is read do not include reference columns, the filter cannot query the values of reference columns.
-   You can use filters by calling the GetRow, BatchGetRow, and GetRange operations, which does not change the native semantics or limited items of these operations.

    When you use GetRange, the number of rows scanned cannot exceed 5,000, or the data scanned cannot exceed 4 MB in size.

    If the scanned 5,000 rows or 4 MB does not match the filter conditions, the rows in the response are empty. However, next\_start\_primary\_key may not be empty. In this case, you need to use next\_start\_primary\_key to continue reading the data until next\_start\_primary\_key is empty.


## Parameters

Filters are applicable to the column\_filter parameter of the GetRow, BatchGetRow, and GetRange operations.

```
    'column_filter' => <ColumnFilter>
```

-   The following code provides an example on how to create SingleColumnValueFilter:

    SingleColumnValueFilter supports comparison between the value of a column that can be a primary key column and a constant. SingleColumnValueFilter does not support the comparison between two columns or two constants.

    ```
        [
            'column_name' => '<string>',
            'value' => <ColumnValue>,
            'comparator' => <ComparatorType>
            'pass_if_missing' => true || false
            'latest_version_only' => true || false
        ]
            
    ```

-   The following code provides an example on how to create CompositeColumnValueFilter:

    CompositeColumnValueFilter has a tree structure. It contains the logical\_operator inner node and the SingleColumnValueFilter leaf node.

    ```
        [
            'logical_operator' => <LogicalOperator>
            'sub_filters' => [
                <ColumnFilter>,
                <ColumnFilter>,
                <ColumnFilter>,
                // other conditions
            ]
        ]
            
    ```


|Parameter|Description|
|---------|-----------|
|column\_name|The name of the reference column in the filter.|
|value|The comparison value of the reference column in the filter.The parameter value is in the format of \[Value, Type\]. Type can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types. If the type is BINARY, the type must be specified. Otherwise, the type can be ignored.

If the type is BINARY, you must set both the type and value. |
|comparator|The relational operator in the filter. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).Relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). In Tablestore, ComparatorTypeConst::CONST\_EQUAL indicates EQUAL \(=\), ComparatorTypeConst::CONST\_NOT\_EQUAL indicates NOT\_EQUAL \(!=\), ComparatorTypeConst::CONST\_GREATER\_THAN indicates GREATER\_THAN \(\>\), ComparatorTypeConst::CONST\_GREATER\_EQUAL indicates GREATER\_EQUAL \(\>=\), ComparatorTypeConst::CONST\_LESS\_THAN indicates LESS\_THAN \(<\), and ComparatorTypeConst::CONST\_LESS\_EQUAL indicates LESS\_EQUAL \(<=\). |
|logical\_operator|The logical operator in the filter. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).Logical operators include NOT, AND, and OR. In Tablestore, LogicalOperatorConst::CONST\_NOT indicates NOT, LogicalOperatorConst::CONST\_AND indicates AND, and LogicalOperatorConst::CONST\_OR indicates OR. |
|pass\_if\_missing|Specifies whether to return a row if the row does not contain the reference column. The type of this parameter value is Boolean. The default value is true, which indicates that if the reference column does not exist in a row, the row is returned.When pass\_if\_missing is set to false, if the reference column does not exist in a row, the row is not returned. |
|latest\_version\_only|Specifies whether to use only the value of the latest version for comparison when the reference column contains data of multiple versions. The type of this parameter value is Boolean. The default value is true, which indicates that if the reference column has multiple versions of data, only the value of the latest version is used for comparison.When latest\_version\_only is set to false, if a reference column has multiple versions of data, the value of all versions in the column is used for comparison. In this case, if only the value of one version meets the conditions, the row is returned. |
|sub\_filters|Child nodes can be SingleColumnValueFilter or CompositeColumnValueFilter.Different logical operators of the inner nodes require different number of child nodes that can be attached.

-   When the logical operator of an inner node is NOT, only one child node can be attached.
-   When the logical operator of an inner node is AND or OR, multiple child nodes can be attached. |

## Examples

-   The following code provides an example on how to construct SingleColumnValueFilter:

    ```
        // Specify that the row is returned when the value of Col0 is 0.
        $column_filter = [
            'column_name' => 'Col0',
            'value' => 0,
            'comparator' => ComparatorTypeConst::CONST_EQUAL 
            'pass_if_missing' => false                         / If Col0 does not exist, the row that contains this column is not returned.
            'latest_version_only' => true                      // Specify that only the latest version is used for comparison.
        ];
                        
    ```

-   The following code provides an example on how to construct CompositeColumnValueFilter:

    ```
        // Set condition composite1 to (Col0 == 0) AND (Col1 > 100).
        $composite1 = [
            'logical_operator' => LogicalOperatorConst::CONST_AND,
            'sub_filters' => [
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
            'sub_filters' => [
                $composite1,
                [
                    'column_name' => 'Col2',
                    'value' => 10,
                    'comparator' => ComparatorTypeConst::CONST_LESS_EQUAL
                ]
            ]
        ];  
                        
    ```


