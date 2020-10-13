# Configure an auto-increment primary key column

**Note:** Tablestore SDK for PHP V4.0.0 and later allow you to create auto-increment primary key columns.

## Prerequisites

The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).

## Procedure

1.  When you create a table, you cannot set the partition key to an auto-increment primary key column.

    An auto-increment primary key column can only be an integer column. The generated value for an auto-increment primary key column is a 64-bit signed long integer.

2.  When you write data to a table, you do not need to specify a specific value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to placeholders.

    If you want to obtain the value of the auto-increment column after data is written to the table, you can set ReturnType to ReturnTypeConst::CONST\_PK.

    When you query data, you must specify the values of all primary key columns. To obtain a complete primary key value, you can set ReturnType to ReturnTypeConst::CONST\_PK in PutRow, UpdateRow, or BatchWriteRow.


## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

1.  Create a table

    To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to PrimaryKeyOptionConst::CONST\_PK\_AUTO\_INCR.

    ```
    function createTable($otsClient) 
    {
        $request = [
            'table_meta' => [
                'table_name' => 'table_name',       // Specify the table name.
                'primary_key_schema' => [
                    ['PK_1', PrimaryKeyTypeConst::CONST_STRING],    // The name of the first primary key column (partition key) is PK_1. The type of the first primary key column is STRING.
                    ['PK_2', PrimaryKeyTypeConst::CONST_INTEGER, PrimaryKeyOptionConst::CONST_PK_AUTO_INCR]
                    // The name of the second primary key column (partition key) is PK_2. The type of the second primary key column is INTEGER. Set this column to the primary key column.
                ]
            ],
            'reserved_throughput' => [
                'capacity_unit' => [         // Set the reserved read throughput to 0 capacity units (CUs) and reserved write throughput to 0 CUs.
                    'read' => 0,
                    'write' => 0
                ]
            ],
            'table_options' => [
                'time_to_live' => -1,             // Specify that data never expires.
                'max_versions' => 1,              // Save only one version.
                'deviation_cell_version_in_sec' => 86400   // Set the max version offset of data. Unit: seconds.
            ]
        ];
        $otsClient->createTable($request);
    }
                        
    ```

2.  Write data

    When you write data to a table, you do not need to specify a value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to PrimaryKeyTypeConst::CONST\_PK\_AUTO\_INCR.

    ```
    function putRow($otsClient)
    {
        $row = [
            'table_name' => 'table_name',
            'primary_key' => [
                ['PK_1', 'Hangzhou'],                      // Set the primary key name and value that are of the list type.
                ['PK_2', null, PrimaryKeyTypeConst::CONST_PK_AUTO_INCR]    // The auto-increment primary key column. You do not need to specify the value. Instead, you need only to set the value of the auto-increment column to PrimaryKeyTypeConst::CONST_PK_AUTO_INCR. Tablestore automatically generates this value.
            ],
            'attribute_columns' => [              // Set the attribute column that is of the list type.
                ['name', 'John'],                  // [The attribute name, attribute value, attribute type, and timestamp]. Ignore the parameters that are not specified.
                ['age', 20],
                ['address', 'Alibaba'],
                ['product', 'OTS'],
                ['married', false]
            ],
            'return_content' => [
                'return_type' => ReturnTypeConst::CONST_PK     // Set return_type to ReturnTypeConst::CONST_PK to return the value of the auto-increment primary key column.
            ]
        ];
        $ret = $otsClient->putRow($row);
        print_r($ret);
    
        $primaryKey = $ret['primary_key'];                  // The obtained primary key value can be passed to operations such as GetRow, UpdateRow, and DeleteRow.
        return $primaryKey;
    }
    
                        
    ```


