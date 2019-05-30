# Filter {#concept_84766_zh .concept}

Table Store filters are used to filter results that the server reads so that the server only returns the rows or columns matching the filtering conditions. Filters are applicable to the column\_filter parameter of the GetRow, BatchGetRow, and GetRange operations.

Currently, Table Store only supports SingleColumnValueFilter and CompositeColumnValueFilter, which are used to determine whether to filter out the data of a row based on reference column values. SingleColumnValueFilter only checks the value of a single reference column, whereas CompositeColumnValueFilter checks the values of multiple reference columns and forms a logical combination of the check results to determine whether to filter out the data of a row.

Note that the filters are used to filter the data that has been read. Therefore, the reference columns used by SingleColumnValueFilter or CompositeColumnValueFilter must be included in the read data. If you specify the columns from which data is read but these columns do not include any reference columns, the filters cannot obtain reference column values. When a reference column does not exist, SingleColumnValueFilter uses the pass\_if\_missing parameter to determine whether the filtering conditions are met. That is, you can select an action even when no reference column exists.

**Format**

```language-php
    'column_filter' => <ColumnFilter>
		
```

The structure of SingleColumnValueFilter is as follows:

```language-php
    [
        'column_name' => '<string>',
        'value' => <ColumnValue>,
        'comparator' => <ComparatorType>
        'pass_if_missing' => true || false
        'latest_version_only' => true || false
    ]
		
```

The structure of CompositeColumnValueFilter is as follows:

```language-php
    [
        'logical_operator' => <LogicalOperator>
        'sub_filters' => [
            <ColumnFilter>,
            <ColumnFilter>,
            <ColumnFilter>,
            // Other conditions
        ]
    ]
		
```

**Format description** 

-   column\_filter: the filtering condition.

    -   SingleColumnValueFilter supports comparison between the value of a column \(which can be a primary key column\) and a constant. It does not support comparison between two columns or between two constants.
        -   column\_name: the column name.
        -   value: the column value.
            -   The column value is in the format of \[Value, Type\]. Type can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types. If the type is BINARY, it must be specified. Otherwise, the type can be ignored.
            -   If the type is not BINARY, the column value can be in the format of \[Value\].
        -   comparator: [ComparatorType](../../../../intl.en-US/API Reference/Data Types/ComparatorType.md#).
            -   EQUAL: expressed by ComparatorTypeConst::CONST\_EQUAL.
            -   NOT\_EQUAL: expressed by ComparatorTypeConst::CONST\_NOT\_EQUAL.
            -   GREATER\_THAN: expressed by ComparatorTypeConst::CONST\_GREATER\_THAN.
            -   GREATER\_EQUAL: expressed by ComparatorTypeConst::CONST\_GREATER\_EQUAL.
            -   LESS\_THAN: expressed by ComparatorTypeConst::CONST\_LESS\_THAN.
            -   LESS\_EQUAL: expressed by ComparatorTypeConst::CONST\_LESS\_EQUAL.
        -   pass\_if\_missing: Attribute columns of a row in Table Store are not fixed. A row may not contain the column involved in comparison. This parameter specifies whether such rows are returned.
            -   A value of true indicates that a row is returned if the specified column does not exist in the row.
            -   A value of false indicates that a row is not returned if the specified column does not exist in the row.
            -   The default value is true.
        -   latest\_version\_only: indicates whether the condition is valid only for the latest version.
            -   A value of true indicates that a row is returned if the value of the latest version for the specified column in the row meets the condition.
            -   A value of false indicates that a row is returned if the value of any version for the specified column in the row meets the condition.
            -   The default value is true.
    -   CompositeColumnValueFilter has a tree structure. It contains the inner node logical\_operator and the leaf node SingleColumnValueFilter.
        -   logical\_operator: [LogicalOperator](../../../../intl.en-US/API Reference/Data Types/LogicalOperator.md#), in the enumeration type.
            -   NOT: expressed by LogicalOperatorConst::CONST\_NOT.
            -   AND: expressed by LogicalOperatorConst::CONST\_AND.
            -   OR: expressed by LogicalOperatorConst::CONST\_OR.
        -   sub\_filters: the sub-node, which can be a SingleColumnValueFilter or CompositeColumnValueFilter.
            -   Two or more sub-nodes can be mounted on the AND or OR operator. Only one sub-node can be mounted on the NOT operator.
    **Example 1**

    The following code provides an example of how to construct a SingleColumnValueFilter:

    ```language-php
        // Set the filter so that a row is returned if the value of Col0 is 0.
        $column_filter = [
            'column_name' => 'Col0',
            'value' => 0,
            'comparator' => ComparatorTypeConst::CONST_EQUAL 
            'pass_if_missing' => false                         // If a row does not contain the Col0 column, the row is not returned.
            'latest_version_only' => true                      // Only the values of the latest version is checked.
        ];
    					
    ```

     **Example 2** 

    The following code provides an example of how to construct a CompositeColumnValueFilter:

    ```language-php
        // The condition composite1 is (Col0 = 0) AND (Col1 > 100).
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
        // The condition composite2 is ( (Col0 = 0) AND (Col1 > 100) ) OR (Col2 <= 10).
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


