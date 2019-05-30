# Conditional update {#concept_84767_zh .concept}

The conditional update feature updates table data only when the specified conditions are met. If the conditions are not met, the update fails. Conditional update is applicable to the condition parameter of the PutRow, UpdateRow, DeleteRow, and BatchWriteRow operations.

Judgment conditions are divided into row existence conditions and column-based conditions.

-   The row existence conditions include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST, which are respectively expressed by RowExistenceExpectationConst::CONST\_IGNORE, RowExistenceExpectationConst::CONST\_EXPECT\_EXIST, and RowExistenceExpectationConst::CONST\_EXPECT\_NOT\_EXIST. When a table needs to be updated, the system first checks the row existence conditions. If a row existence condition is not met, the row is not updated and the system throws an exception.

-   Column-based conditions currently include SingleColumnValueCondition and CompositeColumnValueCondition. They are used to make condition-based judgments based on the values of one or more columns, similar to the conditions used by Table Store filters.


Conditional update can be used to implement optimistic locking. When a row needs to be updated, the system first obtains the value of a column. Assume that column A has a value of 1. Set the condition "Column A = 1", update the row, and set column A to 2. If the update fails, the row has been updated by another client.

**Format**

```language-php
    'condition' => [
        'row_existence' => <RowExistenceExpectation>
        'column_condition' => <ColumnCondition>
    ]

```

The structure of SingleColumnValueCondition is as follows:

```language-php
    [
        'column_name' => '<string>',
        'value' => <ColumnValue>,
        'comparator' => <ComparatorType>
        'pass_if_missing' => true || false
        'latest_version_only' => true || false
    ]

```

The structure of CompositeColumnValueCondition is as follows:

```language-php
    [
        'logical_operator' => <LogicalOperator>
        'sub_conditions' => [
            <ColumnCondition>,
            <ColumnCondition>,
            <ColumnCondition>,
            // Other conditions
        ]
    ]

```

Alternatively, when only a row existence condition exists \(in most cases\), the format can be as follows:

```language-php
    'condition' => <RowExistenceExpectation>

```

**Format description**

-   row\_existence: the row existence condition.
    -   The row existence conditions include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST,
    -   which are respectively expressed by RowExistenceExpectationConst::CONST\_IGNORE, RowExistenceExpectationConst::CONST\_EXPECT\_EXIST, and RowExistenceExpectationConst::CONST\_EXPECT\_NOT\_EXIST.
    -   When a table needs to be updated, the system first checks the row existence conditions. If a row existence condition is not met, the row is not updated and the system throws an exception.
-   column\_condition: the column-based condition.

    -   SingleColumnValueCondition supports comparison between the value of a column \(which can be a primary key column\) and a constant. It does not support comparison between two columns or between two constants.
        -   column\_name: the column name.
        -   value: the column value.
            -   The column value is in the format of \[Value, Type\]. Type can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types. If the type is BINARY, it must be specified. Otherwise, the type can be ignored.
            -   If the type is not BINARY, the column value can be in the format of \[Value\].
        -   comparator: [the comparison type](https://help.aliyun.com/document_detail/35160.html).
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
    -   CompositeColumnValueCondition has a tree structure. It contains the inner node logical\_operator and the leaf node SingleColumnValueCondition.

        -   logical\_operator: [the logical operator](https://help.aliyun.com/document_detail/35162.html), in the enumeration type.
            -   NOT: expressed by LogicalOperatorConst::CONST\_NOT.
            -   AND: expressed by LogicalOperatorConst::CONST\_AND.
            -   OR: expressed by LogicalOperatorConst::CONST\_OR.
        -   sub\_conditions: the sub-node, which can be a SingleColumnValueCondition or CompositeColumnValueCondition.

            -   Two or more sub-nodes can be mounted on the AND or OR operator. Only one sub-node can be mounted on the NOT operator.


**Example 1**

The following code provides an example of how to construct a SingleColumnValueCondition:

```language-php
    // Set the filter so that a row is returned if the value of Col0 is 0.
    $column_condition = [
        'column_name' => 'Col0',
        'value' => 0,
        'comparator' => ComparatorTypeConst::CONST_EQUAL 
        'pass_if_missing' => false                         // If a row does not contain the Col0 column, the row is not returned.
        'latest_version_only' => true                      // Only the values of the latest version is checked.
    ];

```

**Example 2**

The following code provides an example of how to construct a CompositeColumnValueCondition:

```
// The condition composite1 is (Col0 = 0) AND (Col1 > 100).
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
    // The condition composite2 is ( (Col0 = 0) AND (Col1 > 100) ) OR (Col2 <= 10).
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

**Example 3**

You can use conditional update to implement optimistic locking. The following code provides an example of how to use conditional update to update a column with an incremented value.

```
// Read a row.
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
    // Configure conditional update for Col0 to increment the value of Col0 by 1.
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

