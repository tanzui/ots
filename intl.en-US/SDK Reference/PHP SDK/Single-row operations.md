# Single-row operations {#concept_31759_zh .concept}

The Table Store SDK provides the PutRow, GetRow, UpdateRow, and DeleteRow operations for single-row operations.

## PutRow {#section_nw3_42k_2fb .section}

[PutRow](../../../../intl.en-US/API Reference/Operations/PutRow.md#)

You can call this operation to insert a row of data. If the row does not exist, this operation inserts a row. If the row exists, this operation overwrites it. \(This means that all columns and column values of all versions of the existing row are deleted\).

When performing PutRow, you can use conditional update to set conditions for the existence of the row or conditions for values in certain columns of the row. For more information, see [Conditional update](intl.en-US/SDK Reference/PHP SDK/Conditional update.md#).

 **Operation** 

``` {#codeblock_rf2_5d3_htt .language-php}
    /**
     * Write a row of data. If the row already exists, the data of the row is overwritten. The number of the consumed capacity units (CUs) is returned.
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function putRow(array $request); 
			
```

 **Request format** 

``` {#codeblock_nbw_8aq_q09 .language-php}
$result = $client->putRow([
    'table_name' => '<string>', // REQUIRED
    'condition' => [
        'row_existence' => <RowExistence>,   
        'column_condition' => <ColumnCondition>
    ],
    'primary_key' => [                              // REQUIRED
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ],  
    'attribute_columns' => [         // REQUIRED
            ['<string>', <ColumnValue>], 
            ['<string>', <ColumnValue>, <ColumnType>],
            ['<string>', <ColumnValue>, <ColumnType>, <integer>]
    ],
    'return_content' => [
        'return_type' => <ReturnType>
    ]
]);

			
```

 **Request format description** 

-   table\_name: required. This parameter specifies the name of the table.
-   condition: For more information, see [Conditional update](intl.en-US/SDK Reference/PHP SDK/Conditional update.md#).
    -   row\_existence: the row existence condition.
    -   column\_condition: the column-based condition.
-   primary\_key: required. This parameter specifies the primary key value of the row.
    -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
    -   Each item consists of the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType, which is optional\).
    -   The value of PrimaryKeyValue can be an integer or a string. This parameter can be set to null for an auto-increment primary key column.
    -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, PrimaryKeyTypeConst::CONST\_BINARY, or PrimaryKeyTypeConst::CONST\_PK\_AUTO\_INCR, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, and PK\_AUTO\_INCR types. PK\_AUTO\_INCR indicates the auto-increment column. For more information, see Auto-increment primary key column. If the type is INTEGER or STRING, it can be ignored. Otherwise, the type must be specified.
-   attribute\_columns: required. This parameter specifies the column attribute value.
    -   Each item consists of the attribute name, attribute value \(ColumnValue\), column type \(ColumnType, which is optional\), and timestamp \(which is optional\).
    -   ColumnType can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types. If the type is BINARY, it must be specified. Otherwise, the type can be ignored or set to null.
    -   The timestamp is a 64-bit integer that represents the version of an attribute. This parameter is optional. If this parameter is not specified, the server time is used.
-   return\_content: the content to be returned.
    -   return\_type: Currently, only this parameter is required.
        -   A value of ReturnTypeConst::CONST\_PK indicates that the value of the auto-increment primary key column is returned.

 **Result format** 

``` {#codeblock_18t_a1f_5rz .language-php}
[
    'consumed' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ]
    ],
    'primary_key' => [ 
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ],  
    'attribute_columns' => []
]
			
```

 **Result format description** 

-   consumed: the number of CUs consumed by this operation.
    -   capacity\_unit: the number of read and write CUs consumed.
        -   read: the number of read CUs consumed.
        -   write: the number of write CUs consumed.
-   primary\_key: the primary key value, which is consistent with that in the request. If you set the return type to return the value of the auto-increment primary key, this primary key value is returned.
-   attribute\_columns: the attribute value, which is the same as that in the request. It is currently empty.

 **Example 1** 

The following code provides an example of how to write a row with 10 attribute columns and write one version of data for each column. The version number \(timestamp\) is specified by the server.

``` {#codeblock_apb_yvm_wf0 .language-php}
$attr = array();
for($i = 0; $i < 10; $i++) {
    $attr[] = ['Col'. $i, $i]; 
}
$request = [
    'table_name' => 'MyTable',
    'condition' => RowExistenceExpectationConst::CONST_IGNORE, // condition can be set to IGNORE, EXPECT_EXIST, or EXPECT_NOT_EXIST.
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'attribute_columns' => $attr
];
$response = $otsClient->putRow ($request);
			
```

 **Example 2** 

The following code provides an example of how to write a row with 10 attribute columns and write three versions of data for each column. The version number \(timestamp\) is specified by the client.

``` {#codeblock_vy1_ltu_9cu .language-php}
$attr = array();
$timestamp = getMicroTime();
for($i = 0; $i < 10; $i++) {
    for($j = 0; $j < 3; $j++) {
        $attr[] = ['Col'. $i, $j, null, $timestamp+$j];
    }
}
$request = [
    'table_name' => 'MyTable',
    'condition' => RowExistenceExpectationConst::CONST_IGNORE, // condition can be set to IGNORE, EXPECT_EXIST, or EXPECT_NOT_EXIST.
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'attribute_columns' => $attr
];
$response = $otsClient->putRow ($request);
			
```

 **Example 3** 

The following code provides an example of how to write data if the target row does not exist:

``` {#codeblock_57a_hjk_6e6 .language-php}
$attr = array();
$timestamp = getMicroTime();
for($i = 0; $i < 10; $i++) {
    for($j = 0; $j < 3; $j++) {
        $attr[] = ['Col'. $i, $j, null, $timestamp+$j];
    }
}
$request = [
    'table_name' => 'MyTable',
    'condition' => RowExistenceExpectationConst::CONST_EXPECT_NOT_EXIST, // Configure the condition so that data is written if the target row does not exist.
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'attribute_columns' => $attr
];
$response = $otsClient->putRow ($request);
			
```

 **Example 4** 

The following code provides an example of how to write data if the target row already exists and the value of Col0 is greater than 100:

``` {#codeblock_uk8_0la_be3 .language-php}
$attr = array();
$timestamp = getMicroTime();
for($i = 0; $i < 10; $i++) {
    for($j = 0; $j < 3; $j++) {
        $attr[] = ['Col'. $i, $j, null, $timestamp+$j];
    }
}
$request = [
    'table_name' => 'MyTable',
    'condition' => [
        'row_existence' => RowExistenceExpectationConst::CONST_EXPECT_EXIST,// Configure the condition so that data is written if the target row exists.
        'column_condition' => [                  // If the condition is met, the data is updated.
            'column_name' => 'Col0',
            'value' => 100,
            'comparator' => ComparatorTypeConst::CONST_GREATER_THAN
        ]
    ,
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'attribute_columns' => $attr
];
$response = $otsClient->putRow ($request);
			
```

## GetRow {#section_xn9_re1_xab .section}

[OperationsSummary](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#)

The GetRow operation specifies the table name and the primary key of a row. The two possible reading results are as follows:

-   If the row exists, the primary key columns and attribute columns of the row are returned.
-   If the row does not exist, no row is returned and no error is reported.

 **Operation** 

``` {#codeblock_g23_0si_cco .language-php}
    /**
     * Read a row of data.
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function getRow(array $request);
			
```

 **Request format** 

``` {#codeblock_zis_9nt_e2t .language-php}
$result = $client->getRow([
    'table_name' => '<string>',                     // REQUIRED
    'primary_key' => [                              // REQUIRED
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ], 
    'max_versions' => <integer>,
    'time_range' => [
        'start_time' => <integer>,
        'end_time' => <integer>,
        'specific_time' => <integer>
    ],
    'start_column' => '<string>',
    'end_column' => '<string>',
    'token' => '<string>',
    'columns_to_get' => [
        '<string>',
        '<string>',
        //...   
    ],
    'column_filter' =>  <ColumnCondition>
]);
			
```

 **Request format description** 

-   table\_name: required. This parameter specifies the name of the table.
-   primary\_key: required. This parameter specifies the primary key value of the row.
    -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
    -   Each item consists of the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType, which is optional\).
    -   The value of PrimaryKeyValue can be an integer or a string.
    -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, or PrimaryKeyTypeConst::CONST\_BINARY, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), and BINARY types. If the type is INTEGER or STRING, it can be ignored. Otherwise, the type must be specified.
-   max\_versions: the maximum number of versions from which data is read.
-   time\_range: the range of versions from which data is read. For more information, see [TimeRange](../../../../intl.en-US/API Reference/Data Types/TimeRange.md#).
    -   start\_time: the start timestamp, in milliseconds. The minimum and maximum values of the timestamp are 0 and INT64. MAX, respectively. To query data within a range, specify start\_time and end\_time. The range is a left-closed right-open interval.
    -   end\_time: the end timestamp, in milliseconds. The minimum and maximum values of the timestamp are 0 and INT64. MAX, respectively.
    -   specific\_time: the specific timestamp. To query data at a specific time, specify specific\_time. You can set either specific\_time or \[start\_time, end\_time\). The unit of this parameter is millisecond. The minimum and maximum values of the timestamp are 0 and INT64. MAX, respectively.
-   You must specify at least one of max\_versions and time\_range.
    -   If you only specify max\_versions, data of up to the specified number of versions is returned from the latest to the earliest.
    -   If you only specify time\_range, all data within the range is returned.
    -   If you specify both max\_versions and time\_range, data of up to the specified number of versions within the version range is returned from the latest to the earliest.
-   columns\_to\_get: the set of columns to be read. If you do not specify this parameter, all columns are read.
-   start\_column: the start column to be read, which is used for reading wide rows. The returned result includes the start column. The column names are sorted lexicographically. Assume that a table contains columns "a", "b", and "c". If the value of start\_column is "b", the reading starts from column "b", and columns "b" and "c" are returned.
-   end\_column: the end column to be read, which is used for reading wide rows. The returned result does not include the end column. The column names are sorted lexicographically. Assume that a table contains columns "a", "b", and "c". If the value of end\_column is "b", the reading ends at column "b", and column "a" is returned.
-   token: the starting position of a wide row to be read next time. This parameter is currently unavailable.
-   column\_filter: the filtering condition. Only rows meeting the condition are returned. This parameter is similar to column\_condition in condition. For more information, see [Filter](intl.en-US/SDK Reference/PHP SDK/Filter.md#).

 **Result format** 

``` {#codeblock_l0t_nlp_h5e .language-php}
[
    'consumed' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ]
    ],
    'primary_key' => [ 
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ],  
    'attribute_columns' => [
            ['<string>', <ColumnValue>, <ColumnType>, <integer>]
            ['<string>', <ColumnValue>, <ColumnType>, <integer>]
            ['<string>', <ColumnValue>, <ColumnType>, <integer>]
    ],
    'next_token' => '<string>'
]
			
```

 **Result format description** 

-   consumed: the number of CUs consumed by this operation.
    -   capacity\_unit: the number of read and write CUs consumed.
        -   read: the number of read CUs consumed.
        -   write: the number of write CUs consumed.
-   primary\_key: the primary key value, which is consistent with that in the request. If you set the return type to return the value of the auto-increment primary key, this primary key value is returned.
-   attribute\_columns: the attribute value.
    -   Each item consists of the attribute name, attribute value \(ColumnValue\), attribute type \(ColumnType\), and timestamp.
    -   ColumnType can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types.
    -   The timestamp is a 64-bit integer that represents the version of an attribute.
    -   The attributes in the returned result are sorted by attribute name lexicographically in ascending order. The versions of the attributes are sorted by timestamp in descending order.
    -   The order of the columns may be inconsistent with that of columns\_to\_get in the request.
-   next\_token: the starting position of a wide row to be read next time. This parameter is currently unavailable. \(This parameter is currently unavailable.\)
-   If the row does not exist, the values of primary\_key and attribute\_columns are empty lists \[\].

 **Example 1** 

The following code provides an example of how to read the latest version of a column in a row:

``` {#codeblock_mpj_ifm_9zm .language-php}
$request = [
    'table_name' => 'MyTable',
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'max_versions' => 1,                     // Set this parameter so that the latest version is read.
    'columns_to_get' => ['Col0']             // Set the column to be read.
];
$response = $otsClient->getRow ($request);
			
```

Note:

-   If you query a row of data, the system returns the data in all columns of the row. You can specify columns\_to\_get to enable the system to return the data in specific columns. For example, if you set columns\_to\_get to Col0 and Col1, the system only returns the data in Col0 and Col1.

-   Conditional filtering is supported. For example, you can configure the system to return results only when the value of Col0 is greater than 24.

-   When both columns\_to\_get and column\_filter are specified, the system first returns results based on the value of columns\_to\_get, and then filters the returned columns based on the value of column\_filter.

-   When a specified column does not exist, pass\_if\_missing determines the next action. The default value is true, indicating that the condition is valid if the column does not exist.


 **Example 2** 

The following code provides an example of how to set a filter:

``` {#codeblock_oe3_sx9_5ss .language-php}
$request = [
    'table_name' => 'MyTable',
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'max_versions' => 1,                     // Set this parameter so that the latest version is read.
    'column_filter' => [                     // Set the filter so that a row is returned if the value of Col0 is 0.
        'column_name' => 'Col0',
        'value' => 0,
        'comparator' => ComparatorTypeConst::CONST_EQUAL,
        'pass_if_missing' => false          // If a row does not contain the Col0 column, the row is not returned.
    ]
];
$response = $otsClient->getRow ($request);
			
```

## UpdateRow {#section_dyw_xej_sa5 .section}

[UpdateRow](../../../../intl.en-US/API Reference/Operations/UpdateRow.md#)

You can call this operation to update a single row of data. If no row exists, a new row is added.

The update operations include writing a column, deleting a column, and deleting a version of a column.

An update operation may be performed in any of the following scenarios:

-   Write a column value without specifying the version. Table Store automatically adds a version number to make sure that the version number is incremented.
-   Write a column value with the specified version. If the column does not have the column value of this version, the data is inserted. Otherwise, the existing value is overwritten.
-   Delete the column values of the specified version.
-   Delete the column values of all versions for the entire column.

When performing UpdateRow, you can use conditional update to set conditions for the existence of the row or conditions for values in certain columns of the row. For more information, see [Conditional update](intl.en-US/SDK Reference/PHP SDK/Conditional update.md#).

 **Operation** 

``` {#codeblock_bvy_0cg_t0t .language-php}
    /**
     * Update a row of data.
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function updateRow(array $request);
			
```

 **Request format** 

``` {#codeblock_gj3_fe6_9bv .language-php}
$result = $client->updateRow([
    'table_name' => '<string>', // REQUIRED
    'condition' => [
        'row_existence' => <RowExistence>,
        'column_condition' => <ColumnCondition>
    ],
    'primary_key' => [                              // REQUIRED
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ], 
    'update_of_attribute_columns' => [         // REQUIRED
        'PUT' => [
            ['<string>', <ColumnValue>], 
            ['<string>', <ColumnValue>, <ColumnType>],
            ['<string>', <ColumnValue>, <ColumnType>, <integer>]
        ],
        'DELETE' => [
            ['<string>', <integer>], 
            ['<string>', <integer>], 
            ['<string>', <integer>], 
            ['<string>', <integer>]
        ],
        'DELETE_ALL' => [
            '<string>',
            '<string>',
            '<string>',
            '<string>'
        ],        
    ],
    'return_content' => [
        'return_type' => <ReturnType>
    ]
]);
			
```

 **Request format description** 

-   table\_name: required. This parameter specifies the name of the table.
-   condition: the condition for the operation to take effect. For more information, see [Conditional update](intl.en-US/SDK Reference/PHP SDK/Conditional update.md#).
    -   row\_existence: the row existence condition.
    -   column\_condition: the column-based condition.
-   primary\_key: required. This parameter specifies the primary key value of the row.
    -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
    -   Each item consists of the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType, which is optional\).
    -   The value of PrimaryKeyValue can be an integer or a string.
    -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, or PrimaryKeyTypeConst::CONST\_BINARY, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), and BINARY types. If the type is INTEGER or STRING, it can be ignored. Otherwise, the type must be specified.
-   update\_of\_attribute\_columns: the modification on the row attributes. \(This parameter is required. If none of the operations is performed, the corresponding item can be ignored.\) The update operations are as follows:
    -   PUT: indicates that if this column does not exist, a new column is added. If the column exists, it is overwritten. The format is the same as that of attribute\_columns in PutRow.
        -   Each item consists of the attribute name, attribute value \(ColumnValue\), column type \(ColumnType, which is optional\), and timestamp \(which is optional\).
        -   ColumnType can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types. If the type is BINARY, it must be specified. Otherwise, the type can be ignored or set to null.
        -   The timestamp is a 64-bit integer that represents the version of an attribute. This parameter is optional. If this parameter is not specified, the server time is used.
    -   DELETE: indicates that data of the specified version in the column is deleted. The timestamp must be specified.
        -   Each item consists of the attribute name and timestamp.
        -   The timestamp is a 64-bit integer that represents the attribute of the specified version.
    -   DELETE\_ALL: indicates that data of all versions in the column is deleted. Note: Deleting all attribute columns of a row is not the same as deleting the row. To delete the row, use the DeleteRow operation.
        -   You only need to specify the attribute name.
-   return\_content: the content to be returned.
    -   return\_type: Currently, only this parameter is required.
        -   A value of ReturnTypeConst::CONST\_PK indicates that the value of the auto-increment primary key column is returned.

 **Result format** 

``` {#codeblock_0p8_th0_bom .language-php}
[
    'consumed' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ]
    ],
    'primary_key' => [ 
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ],  
    'attribute_columns' => []
]
			
```

 **Result format description** 

-   consumed: the number of CUs consumed by this operation.
    -   capacity\_unit: the number of read and write CUs consumed.
        -   read: the number of read CUs consumed.
        -   write: the number of write CUs consumed.
-   primary\_key: the primary key value, which is consistent with that in the request. If you set the return type to return the value of the auto-increment primary key, this primary key value is returned.
-   attribute\_columns: the attribute value, which is the same as that in the request. It is currently empty.

 **Example 1** 

The following code provides an example of how to update several columns, delete the specified version of a column, and delete the specified column:

``` {#codeblock_bo7_t2k_rfg .language-php}
$request = [
    'table_name' => 'MyTable',
    'condition' => RowExistenceExpectationConst::CONST_IGNORE,
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'update_of_attribute_columns' => [
        'PUT' => [                       // Update several columns.
            ['Col0', 100],
            ['Col1', 'Hello'],
            ['Col2', 'a binary', ColumnTypeConst::CONST_BINARY],
            ['Col3', 100, null, 1526418378526]
        ],
        'DELETE' => [                    // Delete a version of a column.
            ['Col10', 1526418378526]
        ],
        'DELETE_ALL' => [
            'Col11'                      // Delete a column.
        ]
    ]
];
$response = $otsClient->updateRow($request);
			
```

Note:

-   UpdateRow also supports conditional statements.

 **Example 2** 

The following code provides an example of how to set update conditions:

``` {#codeblock_6cm_2w4_2h8 .language-php}
$request = [
    'table_name' => 'MyTable',
    'condition' => RowExistenceExpectationConst::CONST_IGNORE,
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'condition' => [
        'row_existence' => RowExistenceExpectationConst::CONST_EXPECT_EXIST, // Update the row if it exists.
        'column_filter' => [                                                 // Update the row when the value of Col0 is greater than 100.
            'column_name' => 'Col0',
            'value' => 100,
            'comparator' => ComparatorTypeConst::CONST_GREATER_THAN
        ]
    ],    
    'update_of_attribute_columns' => [
        'PUT' => [                       // Update several columns.
            ['Col0', 100],
            ['Col1', 'Hello'],
            ['Col2', 'a binary', ColumnTypeConst::CONST_BINARY],
            ['Col3', 100, null, 1526418378526]
        ],
        'DELETE' => [                    // Delete a version of a column.
            ['Col10', 1526418378526]
        ],
        'DELETE_ALL' => [
            'Col11'                      // Delete a column.
        ]
    ]
];
			
```

## DeleteRow {#section_kx3_d3v_mdc .section}

[DeleteRow](../../../../intl.en-US/API Reference/Operations/DeleteRow.md#)

You can call this operation to delete a row. No error is reported regardless of whether the specified row exists.

When performing DeleteRow, you can use conditional update to set conditions for the existence of the row or conditions for values in certain columns of the row. For more information, see [Conditional update](intl.en-US/SDK Reference/PHP SDK/Conditional update.md#).

 **Operation** 

``` {#codeblock_rbw_r6v_04h .language-php}
    /**
     * Delete a row of data.
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function deleteRow(array $request);
			
```

 **Request format** 

``` {#codeblock_s9y_e50_0i4 .language-php}
$result = $client->deleteRow([
    'table_name' => '<string>', // REQUIRED
    'condition' => [
        'row_existence' => <RowExistence>,
        'column_condition' => <ColumnCondition>
    ],
    'primary_key' => [                              // REQUIRED
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ],
    'return_content' => [
        'return_type' => <ReturnType>
    ]
]);
			
```

 **Request format description** 

-   table\_name: required. This parameter specifies the name of the table.
-   condition: the condition for the operation to take effect. For more information, see [Conditional update](intl.en-US/SDK Reference/PHP SDK/Conditional update.md#).
    -   row\_existence: the row existence condition.
    -   column\_condition: the column-based condition.
-   primary\_key: required. This parameter specifies the primary key value of the row.
    -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
    -   Each item consists of the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType, which is optional\).
    -   The value of PrimaryKeyValue can be an integer or a string.
    -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, or PrimaryKeyTypeConst::CONST\_BINARY, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), and BINARY types. If the type is INTEGER or STRING, it can be ignored. Otherwise, the type must be specified.
-   return\_content: the content to be returned.

    -   return\_type: Currently, only this parameter is required.

        -   A value of ReturnTypeConst::CONST\_PK indicates that the value of the auto-increment primary key column is returned.


 **Result format** 

``` {#codeblock_sx0_cx9_blj .language-php}
[
    'consumed' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ]
    ],
    'primary_key' => [
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ],
    'attribute_columns' => []
]
			
```

 **Result format description** 

-   consumed: the number of CUs consumed by this operation.
    -   capacity\_unit: the number of read and write CUs consumed.
        -   read: the number of read CUs consumed.
        -   write: the number of write CUs consumed.
-   primary\_key: the primary key value, which is consistent with that in the request. If you set the return type to return the value of the auto-increment primary key, this primary key value is returned.
-   attribute\_columns: the attribute value, which is the same as that in the request. It is currently empty.

 **Example 1** 

The following code provides an example of how to delete a row of data:

``` {#codeblock_n2m_jue_yam .language-php}
$request = [
    'table_name' => 'MyTable',
    'condition' => RowExistenceExpectationConst::CONST_IGNORE,
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ],
    'return_content' => [
        'return_type' => ReturnTypeConst::CONST_PK     // Set return_type to ReturnTypeConst::CONST_PK so that the value of the auto-increment primary key column is returned.
    ]
];
$response = $otsClient->deleteRow($request);
			
```

 **Example 2** 

The following code provides an example of how to set deletion conditions:

``` {#codeblock_b1c_5vr_n3k .language-php}
$request = [
    'table_name' => 'MyTable',
    'condition' => [
        'row_existence' => RowExistenceExpectationConst::CONST_EXPECT_EXIST, // Delete the row if it exists.
        'column_filter' => [          // Delete the row when the value of Col0 is greater than 100.
            'column_name' => 'Col0',
            'value' => 100,
            'comparator' => ComparatorTypeConst::CONST_GREATER_THAN
        ],
    ],
    'primary_key' => [ // The primary key.
        ['PK0', 123],
        ['PK1', 'abc']
    ]
];
$response = $otsClient->deleteRow ($request);
			
```

