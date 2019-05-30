# Multi-row operations {#concept_31760_zh .concept}

The Table Store SDK provides multi-row operations, such as BatchGetRow, BatchWriteRow, and GetRange.

## BatchGetRow {#section_ihj_rfk_2fb .section}

[API description](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#)

You can call this operation to read several rows of data from one or more tables.

The BatchGetRow operation is a set of GetRow operations. Each operation is executed, returns results, and computes the consumed capacity units \(CUs\) independently.

Compared to the execution of a large number of GetRow operations, the use of the BatchGetRow operation can effectively reduce the request response time and increase the data read rate.

The parameters of the BatchGetRow operation are the same as those of the GetRow operation. Note that BatchGetRow uses the same parameter conditions for all rows. For example, if columns\_to\_get is set to colA, only the value of colA is read for all the rows.

Similar to the use of the BatchWriteRow operation, when using the BatchGetRow operation, you must check the response. If the operation fails for some rows, the system may not throw an exception but stores information about the failed rows in BatchGetRowResponse.

 **Operation** 

```language-php
    /**
     * Read the specified rows of data.
     * Description: https://help.aliyun.com/document_detail/27310.html
     * Note that if the BatchGetRow operation fails for some rows, the system does not throw an exception but stores information about the failed rows in $response. For more information, see the example of handling the return result of BatchGetRow.
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function batchGetRow(array $request);  
			
```

 **Request format** 

```language-php
$result = $client->batchGetRow([
    'tables' => [                                            // REQUIRED
        [
            'table_name' => '<string>',                      // REQUIRED
            'primary_keys' => [                              // REQUIRED
                [
                    ['<string>', <PrimaryKeyValue>], 
                    ['<string>', <PrimaryKeyValue>],
                    ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
                ], 
                // Other primary keys
            ]
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
        ],
        // Other tables
    ]
]);

			
```

 **Request format description** 

-   Difference from GetRow
    -   The primary\_key parameter is changed to the primary\_keys parameter, so that multiple rows can be read simultaneously.
    -   Hierarchies are created for tables, so that multiple tables can be read simultaneously.
-   The tables parameter is organized in the unit of tables, which specifies the information about the rows to be read for subsequent operations on tables.
    -   table\_name: required. This parameter specifies the name of the table.
    -   primary\_keys: required. The value is a list of primary key information of the rows. Each item in the list contains information about a primary key column. The structure of each primary key column is as follows:
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

```language-php
[
    'tables' => [
        [
            'table_name' => '<string>',
            'rows' => [
                [
                    'is_ok' => true || false,
                    'error' => [
                        'code' => '<string>',
                        'message' => '<string>',
                    ]
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
                ],
                // Other rows
            ]
        ],
        // Other tables
    ]
]
			
```

 **Result format description** 

-   [API description](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#) \(This section describes scenarios where reading all rows fails or reading partial rows fails. Make sure that you have read this section.\)
-   MaxCompute

-   The tables parameter is organized in the unit of tables. The tables correspond to requests one by one.
    -   table\_name: the name of the table.
    -   is\_ok: indicates whether the row operation is successful. A value of true indicates that the row is read. In this case, error is invalid. A value of false indicates that the row fails to be read. In this case, consumed, primary\_key, and attribute\_columns are invalid.
    -   error: the error information in the response when the operation fails.
        -   code: the error code for the operation on the current row.
        -   message: the error message for the operation on the current row.
    -   consumed: the number of CUs consumed by this operation.
        -   capacity\_unit: the number of read and write CUs consumed.
            -   read: the number of read CUs consumed.
            -   write: the number of write CUs consumed.
    -   primary\_key: the primary key value, which is consistent with that in the request.
    -   attribute\_columns: the attribute value.
        -   Each item consists of the attribute name, attribute value \(ColumnValue\), attribute type \(ColumnType\), and timestamp.
        -   ColumnType can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types.
        -   The timestamp is a 64-bit integer that represents the version of an attribute.
        -   The attributes in the returned result are sorted by attribute name lexicographically in ascending order. The versions of the attributes are sorted by timestamp in descending order.
        -   The order of the columns may be inconsistent with that of columns\_to\_get in the request.
    -   next\_token: the starting position of a wide row to be read next time. This parameter is currently unavailable.

 **Example** 

The following code provides an example of how to read 30 rows of data at a time:

```language-php
    // Read data from three tables, 10 rows per table.
    $tables = array();
    for($i = 0; $i < 3; $i++) {
        $primary_keys = array();
        for($j = 0; $j < 10; $j++) {
            $primary_keys[] = [
                ['pk0', $i],
                ['pk1', $j]
            ];
        }
        $tables[] = [
            'table_name' => 'SampleTable' . $i,
            'max_versions' => 1,
            'primary_keys' => $primary_keys
        ];
    }
    $request = [
        'tables' => $tables
    ];
    $response = $otsClient->batchGetRow ($request);

    // Process each table that is returned.
    foreach ($response['tables'] as $tableData) {
      print "Handling table {$tableData['table_name']} ...\n";

      // Determine how to process each row of data in this table.
      foreach ($tableData['rows'] as $rowData) {

        if ($rowData['is_ok']) {

          // Process the data that is read.
            $row = json_encode($rowData['primary_key']);
            print "Handling row: {$row}\n";

        } else {

          // Display the error information.
          print "Error: {$rowData['error']['code']} {$rowData['error']['message']}\n";
        }
      }
    }
			
```

For more information, see the sample documents listed in the following table.

|Document|Description|
|:-------|:----------|
|[BatchGetRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow1.php)|Describes how to use BatchGetRow to read multiple rows from a table.|
|[BatchGetRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow2.php)|Describes how to use BatchGetRow to read multiple rows from multiple tables.|
|[BatchGetRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow3.php)|Describes how to use BatchGetRow to read multiple rows from a table with the specified columns.|
|[BatchGetRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow4.php)|Describes how to use BatchGetRow to process returned results.|
|[BatchGetRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRowWithColumnFilter.php)|Describes how to use BatchGetRow with conditional filtering.|

## BatchWriteRow { .section}

[API description](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#)

You can call this operation to insert, modify, or delete several rows of data in one or more tables.

The BatchWriteRow operation is a set of PutRow, UpdateRow, and DeleteRow operations. Each operation is executed, returns results, and computes the consumed capability units \(CUs\) independently.

The BatchWriteRow operation allows you to perform multiple write operations with a single request. These write operations include PutRow, UpdateRow, and DeleteRow. This operation also allows you to write data to multiple tables at one time.

The process for constructing a BatchWriteRow operation is the same as that for constructing a PutRow, UpdateRow, or DeleteRow operation. You can also set update conditions.

When calling the BatchWriteRow operation, you must check the response. During batch writing, some rows may be written while other rows may fail to be written. If the operation fails for some rows, the system may not throw an exception but stores the index and error messages of the failed rows in BatchWriteRowResponse. Some failed operations may be ignored if you do not check them. In some situations, the BatchWriteRow operation may throw an exception. For example, if the server detects that incorrect parameters exist in some operations, it may throw a parameter error exception. If an exception is thrown, none of the operations in the request is completed.

 **Operation** 

```language-php
    /**
     * Write, update, or delete the specified rows of data.
     * Description: https://help.aliyun.com/document_detail/27311.html
     * Note that if the BatchWriteRow operation fails for some rows, the system does not throw an exception but stores information about the failed rows in $response. For more information, see the example of handling the return result of BatchWriteRow.
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function batchWriteRow(array $request);  
			
```

 **Request format** 

```language-php


$result = $client->batchWriteRow([
    'tables' => [                                            // REQUIRED
        [
            'table_name' => '<string>',                      // REQUIRED
            'operation_type' => <OperationType>,
            'condition' => [
                'row_existence' => <RowExistence>,   
                'column_condition' => <ColumnCondition>
            ],
            'primary_key' => [                               // REQUIRED
                ['<string>', <PrimaryKeyValue>], 
                ['<string>', <PrimaryKeyValue>],
                ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
            ], 
            'attribute_columns' => [                        // REQUIRED in PUT
                    ['<string>', <ColumnValue>], 
                    ['<string>', <ColumnValue>, <ColumnType>],
                    ['<string>', <ColumnValue>, <ColumnType>, <integer>]
            ],
            'update_of_attribute_columns' => [               // REQUIRED in UPDATE
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
        ],
        // Other tables
    ]
]);

			
```

 **Request format description** 

-   This operation is a combination of PutRow, UpdateRow, and DeleteRow.
    -   The operation\_type parameter is added to distinguish operation types.
    -   Hierarchies are created for tables, so that multiple tables can be processed simultaneously.
-   The tables parameter is organized in the unit of tables, which specifies the information about the rows to be written, modified, or deleted for subsequent operations on tables.

    -   table\_name: required. This parameter specifies the name of the table.
    -   condition: For more information, see [Conditional update](intl.en-US/SDK Reference/PHP SDK/Conditional update.md#).
        -   row\_existence: the row existence condition.
        -   column\_condition: the column-based condition.
    -   operation\_type: the operation type. This parameter can be set to OperationTypeConst::CONST\_PUT, OperationTypeConst::CONST\_UPDATE, or OperationTypeConst::CONST\_DELETE, which respectively indicate the PUT, UPDATE, and DELETE types.
        -   If the operation type is PUT, primary\_key and attribute\_columns are valid.
        -   If the operation type is UPDATE, primary\_key and update\_of\_attribute\_columns are valid.
        -   If the operation type is DELETE, primary\_key is valid.
    -   return\_content: the content to be returned.

        -   return\_type: Currently, only this parameter is required.

            -   A value of ReturnTypeConst::CONST\_PK indicates that the value of the auto-increment primary key column is returned.


 **Result format** 

```language-php
  [
      'tables' => [
          [
              'table_name' => '<string>',
              'rows' => [
                  [
                      'is_ok' => true || false,
                      'error' => [
                          'code' => '<string>',
                          'message' => '<string>',
                      ]
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
                  ],
                  // Other rows
              ]
          ],
          // Other tables
      ]
  ]
			
```

 **Result format description** 

-   [API description](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#) \(This section describes scenarios where processing all rows fails or processing partial rows fails. Make sure that you have read this section.\)
-   The tables parameter is organized in the unit of tables. The tables correspond to requests one by one.
    -   table\_name: the name of the table.
    -   is\_ok: indicates whether the row operation is successful. A value of true indicates that the row is written. In this case, error is invalid. A value of false indicates that the row fails to be written.
    -   error: the error information in the response when the operation fails.
        -   code: the error code for the operation on the current row.
        -   message: the error message for the operation on the current row.
    -   consumed: the number of CUs consumed by this operation.
        -   capacity\_unit: the number of read and write CUs consumed.
            -   read: the number of read CUs consumed.
            -   write: the number of write CUs consumed.
    -   primary\_key: the primary key value, which is consistent with that in the request. If you set the return type to return the value of the auto-increment primary key, this primary key value is returned.
    -   attribute\_columns: the attribute value. It is currently empty.

 **Example** 

The following code provides an example of how to import 30 rows of data at a time:

```language-php
    // Insert data into three tables, 10 rows per table.
    $tables = array();
    for($i = 0; $i < 3; $i++) {
        $rows = array();
        for($j = 0; $j < 10; $j++) {
            $rows[] = [
                'operation_type' => OperationTypeConst::CONST_PUT,            // The operation type is PUT.
                'condition' => RowExistenceExpectationConst::CONST_IGNORE,
                'primary_key' => [
                    ['pk0', $i],
                    ['pk1', $j]
                ],
                'attribute_columns' => [
                    ['Col0', 4],
                    ['Col2', 'Beijing']
                ]
            ];
        }
        $tables[] = [
            'table_name' => 'SampleTable' . $i,
            'rows' => $rows
        ];
    }
    $request = [
        'tables' => $tables
    ];
    $response = $otsClient->batchWriteRow ($request);
    // Process each table that is returned.
    foreach ($response['tables'] as $tableData) {
      print "Handling table {$tableData['table_name']} ...\n";

      // Process the result returned by PutRow in this table.
      $putRows = $tableData['rows'];

      foreach ($putRows as $rowData) {

        if ($rowData['is_ok']) {
          // Data is successfully written.
          print "Capacity Unit Consumed: {$rowData['consumed']['capacity_unit']['write']}\n";
        } else {

          // Display the error information.
          print "Error: {$rowData['error']['code']} {$rowData['error']['message']}\n";
        }
      }
    }
			
```

For more information, see the sample documents listed in the following table.

|Document|Description|
|:-------|:----------|
|[BatchWriteRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow1.php)|Describes how to perform multiple PUT operations in BatchWriteRow.|
|[BatchWriteRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow2.php)|Describes how to perform multiple UPDATE operations in BatchWriteRow.|
|[BatchWriteRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow3.php)|Describes how to perform multiple DELETE operations in BatchWriteRow.|
|[BatchWriteRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow4.php)|Describes how to simultaneously perform the UPDATE, PUT, and DELETE operations in BatchWriteRow.|
|[BatchWriteRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRowWithColumnFilter.php)|Describes how to use BatchWriteRow with conditional update.|

## GetRange { .section}

[API description](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#)

You can call this operation to read data within the specified primary key range.

This operation is used to read data within a range. In Table Store tables, all rows are sorted by primary key. The primary key of a table is sequentially composed of all primary key columns. Therefore, do not assume that the rows are sorted by a certain primary key column.

The GetRange operation allows you to read data in a forward or backward direction based on a given range. You can also limit the number of rows to be read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops and the read rows and the next primary key are returned. If not all rows are read, you can initiate a request to start from where the last operation left off and read the remaining rows based on the next primary key returned by the previous operation.

 **Operation** 

```language-php
    /**
     * Read data between the start primary key and the end primary key.
     * Note that the server may truncate this range. You need to determine whether to call the GetRange operation again based on next_start_primary_key in the response.
     * You can specify the maximum number of rows to be read.
     * When specifying the start and end primary keys, you can use INF_MIN and INF_MAX to represent the minimum and maximum values. For more information, see the following code example.
     * Description: https://help.aliyun.com/document_detail/27309.html
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function getRange(array $request); 
			
```

 **Request format** 

```language-php


$result = $client->getRange([
    'table_name' => '<string>',                                     // REQUIRED
    'inclusive_start_primary_key' => [                              // REQUIRED
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ], 
    'exclusive_end_primary_key' => [                                // REQUIRED
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ], 
    'direction' => <Direction>,                                     // REQUIRED
    'limit' => <Direction>,
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

-   Difference from GetRow
    -   The primary\_key parameter is changed to the inclusive\_start\_primary\_key and exclusive\_end\_primary\_key parameters. The range is a left-closed right-open interval.
    -   The direction parameter is added to indicate the direction.
    -   The limit parameter is added to limit the number of returned rows.
-   table\_name: required. This parameter specifies the name of the table.
-   inclusive\_start\_primary\_key: the primary key from which the read starts. If the specified row exists, it is included in the response. This parameter is required.
    -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
    -   Each item consists of the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType, which is optional\).
    -   The value of PrimaryKeyValue can be an integer or a string.
    -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, or PrimaryKeyTypeConst::CONST\_BINARY, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), and BINARY types. If the type is INTEGER or STRING, it can be ignored. Otherwise, the type must be specified.
    -   A primary key column can also be of the INF\_MIN or INF\_MAX type. INF\_MIN columns are always smaller than other columns, while INF\_MAX columns are always larger than other columns.
    -   If PrimaryKeyType is set to PrimaryKeyTypeConst::CONST\_INF\_MIN or PrimaryKeyTypeConst::CONST\_INF\_MAX, you can set PrimaryKeyValue to null.
-   exclusive\_end\_primary\_key: the primary key at which the read ends. No matter whether the specified row exists, it is excluded from the response. This parameter is required.
    -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
    -   Each item consists of the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType, which is optional\).
    -   The value of PrimaryKeyValue can be an integer or a string.
    -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, or PrimaryKeyTypeConst::CONST\_BINARY, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), and BINARY types. If the type is INTEGER or STRING, it can be ignored. Otherwise, the type must be specified.
    -   A primary key column can also be of the INF\_MIN or INF\_MAX type. INF\_MIN columns are always smaller than other columns, while INF\_MAX columns are always larger than other columns.
    -   If PrimaryKeyType is set to PrimaryKeyTypeConst::CONST\_INF\_MIN or PrimaryKeyTypeConst::CONST\_INF\_MAX, you can set PrimaryKeyValue to null.
-   direction: required. This parameter specifies the order of this query. If direction is set to DirectionConst::CONST\_FORWARD \(the forward direction\), the value of inclusive\_start\_primary must be smaller than that of exclusive\_end\_primary. All rows in the response are sorted by primary key in ascending order. If direction is set to DirectionConst::CONST\_BACKWARD \(the backward direction\), the value of inclusive\_start\_primary must be greater than that of exclusive\_end\_primary. All rows in the response are sorted by primary key in descending order.
-   limit: the maximum number of rows to be returned.
    -   If the number of rows queried exceeds this value, the response includes a breakpoint to record the position where the read ends in this operation, so that the next read can start from this position. This value must be greater than 0.
    -   Whether or not this value is set, Table Store returns a maximum of 5,000 rows of data, and the total data size does not exceed 4 MB.
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

```language-php
[
    'consumed' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ]
    ],
    'next_start_primary_key' => [ 
        ['<string>', <PrimaryKeyValue>], 
        ['<string>', <PrimaryKeyValue>],
        ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
    ], 
    'rows' => [
        [
            'primary_key' => [ 
                ['<string>', <PrimaryKeyValue>], 
                ['<string>', <PrimaryKeyValue>],
                ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
            ],  
            'attribute_columns' => [
                    ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                    ['<string>', <ColumnValue>, <ColumnType>, <integer>]
                    ['<string>', <ColumnValue>, <ColumnType>, <integer>]
            ]
        ],
        // Other rows
    ]
]
			
```

 **Result format description** 

-   [API description](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#)
-   consumed: the number of CUs consumed by this operation.
    -   capacity\_unit: the number of read and write CUs consumed.
        -   read: the number of read CUs consumed.
        -   write: the number of write CUs consumed.
-   rows
    -   If direction in the request is set to DirectionConst::CONST\_FORWARD, all rows are sorted by primary key in ascending order. If direction in the request is set to DirectionConst::CONST\_BACKWARD, all rows are sorted by primary key in descending order.
    -   The attribute\_columns parameter for each row only contains the columns specified in columns\_to\_get. The order of the columns may be inconsistent with that of columns\_to\_get in the request.
    -   If the specified columns\_to\_get in the request does not contain any primary key column, the primary key is within the query range. However, a row that does not contain any attribute column in columns\_to\_get is not included in the response.
    -   primary\_key: the primary key value, which is consistent with that in the request. If you set the return type to return the value of the auto-increment primary key, this primary key value is returned.
    -   attribute\_columns: the attribute value.
        -   Each item consists of the attribute name, attribute value \(ColumnValue\), attribute type \(ColumnType\), and timestamp.
        -   ColumnType can be set to ColumnTypeConst::CONST\_INTEGER, ColumnTypeConst::CONST\_STRING, ColumnTypeConst::CONST\_BINARY, ColumnTypeConst::CONST\_BOOLEAN, or ColumnTypeConst::CONST\_DOUBLE, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, BOOLEAN, and DOUBLE types.
        -   The timestamp is a 64-bit integer that represents the version of an attribute.
        -   The attributes in the returned result are sorted by attribute name lexicographically in ascending order. The versions of the attributes are sorted by timestamp in descending order.
-   next\_start\_primary\_key: the breakpoint of this GetRange operation.
    -   Its format is the same as that of primary\_key.
    -   If this parameter is not specified, the response for this GetRange operation contains all data within the request range.
    -   If this parameter is specified, the response for this GetRange operation includes only the data in the range \[inclusive\_start\_primary\_key, next\_start\_primary\_key\). To obtain the remaining data, set inclusive\_start\_primary\_key to the value of next\_start\_primary\_key, and retain the value of exclusive\_end\_primary\_key in the original request to perform the subsequent GetRange operation.

 **Example** 

The following code provides an example of how to read data within the specified range:

```language-php
    // Read data with UID within the range [1, 4).
    // The complete primary key is required for defining the range boundary. If the queried range does not involve the range of values in a column, set the column to be infinitely great or small.
    $startPK = [
        ['PK0', 1], 
        ['PK1', null, PrimaryKeyTypeConst::CONST_INF_MIN]  // INF_MIN indicates the minimum value.
    ];
    // The complete primary key is required for defining the range boundary. If the queried range does not involve the range of values in a column, set the column to be infinitely great or small.
    $endPK = [
        ['PK0', 4], 
        ['PK1', null, PrimaryKeyTypeConst::CONST_INF_MAX]  // INF_MAX indicates the maximum value.
    ];
    $request = [
        'table_name' => 'SampleTable',
        'max_versions' => 1,                          // Set this parameter so that the latest version is read.
        'direction' => DirectionConst::CONST_FORWARD, // Query data in the forward direction.
        'inclusive_start_primary_key' => $startPK,    // The start primary key.
        'exclusive_end_primary_key' => $endPK,        // The end primary key.
        'limit' => 10                                 // A maximum of 10 rows are returned.
    ];
    $response = $otsClient->getRange ($request);
    print "Read CU Consumed: {$response['consumed']['capacity_unit']['read']}\n";

    foreach ($response['rows'] as $rowData) {
      // Process each row of data.
    }
			
```

For more information, see the sample documents listed in the following table.

|Document|Description|
|:-------|:----------|
|[GetRange1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange1.php)|Describes how to use GetRange.|
|[GetRange2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange2.php)|Describes how to use GetRange to obtain the specified columns.|
|[GetRange3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange3.php)|Describes how to use GetRange to obtain the specified number of rows.|
|[GetRangeWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRangeWithColumnFilter.php)|Describes how to use GetRange with conditional filtering.|

