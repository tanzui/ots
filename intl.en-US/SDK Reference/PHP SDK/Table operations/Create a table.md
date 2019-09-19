# Create a table {#concept_473417 .concept}

## CreateTable {#section_u83_9zy_k74 .section}

API description: [CreateTable](../../../../intl.en-US/API Reference/Operations/CreateTable.md#)

You can call the CreateTable operation to create a table. When creating a table in Table Store, you must specify TableMeta and TableOptions. You can also set ReservedThroughput.

After you create the table, the server loads partitions of the table to specified nodes. Therefore, you must wait several seconds before reading data from or writing data to the table. Otherwise, the system throws an exception. Note: If the system throws an exception, the SDK automatically retries the read or write operation.

Method

``` {#codeblock_oes_op0_e9d .language-php}
     /**
     * Create a table. Set the number, names, sequence, and types of primary key columns, reserved read and write throughput, TTL, and Stream options.
     * @api
     * @param [] $request The request parameters.
     * @return [] The response. If the CreateTable operation succeeds, no message is returned. An empty array is returned here to be consistent with other operations.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     */
    public function createTable(array $request);
			
```

Request format

``` {#codeblock_enz_8xw_0s0 .language-php}
$result = $client->createTable([
    'table_meta' => [                  // Required.
        'table_name' => '<string>', 
        'primary_key_schema' => [
            ['<string>', <PrimaryKeyType>], 
            ['<string>', <PrimaryKeyType>],
            ['<string>', <PrimaryKeyType>, <PrimaryKeyOption>]
        ]
    ], 
    'reserved_throughput' => [         // Required.
        'capacity_unit' => [
            'read' => <integer>, 
            'write' => <integer>
        ]
    ],
    'table_options' => [              // Required.
        'time_to_live' => <integer>,   
        'max_versions' => <integer>,    
        'deviation_cell_version_in_sec' => <integer>  
    ],
    'stream_spec' => [
        'enable_stream' => true || false,
        'expiration_time' => <integer>
    ]
]);            
```

Request format description

-   table\_meta: required. The table name and the schema of the primary key.
    -   table\_name: the table name.
    -   primary\_key\_schema: the schema of the primary key of the table.
        -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key schema PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
        -   The first primary key column serves as the partition key. Data with the same partition key value is in the same partition. Therefore, we recommend that you assign no more than 10 GB of data with the same partition key value. Otherwise, individual partitions are too large and cannot be split. We also recommend that you distribute read and write operations evenly among different partitions to facilitate load balancing.
        -   When creating a table, you only need to define the primary key of the table. Attribute columns do not need to be defined. Different rows can have different attribute columns and the names of attribute columns can be specified when data is written. Therefore, Table Store is suitable for the storage of semi-structured data. You can dynamically add new attribute columns during business development.
        -   Each item consists of the primary key name, primary key type \(PrimaryKeyType\), and primary key settings \(PrimaryKeyOption, which is optional\).
        -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, or PrimaryKeyTypeConst::CONST\_BINARY, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), and BINARY types.
        -   PrimaryKeyOption can be set to PrimaryKeyOptionConst::CONST\_PK\_AUTO\_INCR, which indicates the auto-increment column. For more information, see the description about auto-increment primary key columns.
-   reserved\_throughput: required. The reserved read throughput and reserved write throughput, which are related to billing.
    -   capacity\_unit: the reserved read throughput and reserved write throughput. When the reserved read throughput or reserved write throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a pay-as-you-go basis. By default, both the reserved read throughput and reserved write throughput are 0. That is, all traffic is billed on a pay-as-you-go basis by default. To set a value greater than 0, we recommend that you read the documentation about Table Store billing to avoid unexpected fees. You can set the reserved read throughput and reserved write throughput of capacity instances only to 0. These instances do not allow throughput reservation.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
-   table\_options: required. The TTL, MaxVersions, and MaxTimeDeviation configurations of the table.
    -   time\_to\_live: the retention time of data. Unit: seconds.
        -   The latest version of Table Store API supports automatic data expiration. If you do not want data to expire, set time\_to\_live to -1.
        -   Table Store checks whether data has expired based on the data timestamp, current time, and table TTL. If the difference between the current time and the data timestamp is greater than the table TTL, data expires and is cleared by the Table Store server. For more information about the data timestamp, see [Preface](../../../../intl.en-US/hide/Preface.md#).
        -   After the TTL is set, the server checks whether data expires based on the data timestamp. If you specify a timestamp when writing data and the timestamp is significantly different from the actual time, unexpected data expiration may occur. For example, if the specified data timestamp is too small, the data may expire and be deleted as soon as it is written. If the specified timestamp is too large, the data may not expire when required. Therefore, when writing data with the TTL specified, make sure that you set a reasonable timestamp.
    -   max\_versions: the maximum number of versions retained by each attribute column.
        -   The max\_versions parameter is used to specify the maximum number of data versions saved by each attribute column. If a new version causes the total number of versions to exceed the value of max\_versions, the server deletes the earliest version to ensure that the number of versions do not exceed this value.
    -   deviation\_cell\_version\_in\_sec: the maximum deviation allowed between the specified version and the current system time when data is written to the version. Unit: seconds.
        -   Table Store supports multiple versions. By default, the system generates a new version number when writing new data. The version number is a timestamp corresponding to the write time in milliseconds. The TTL feature uses this timestamp to determine if data has expired. In addition, you can specify the data writing timestamp. If you set an extremely small timestamp, which deviates from the current time by more than the TTL set for the table, the written data immediately expires. To prevent immediate data expiration, Table Store provides the deviation\_cell\_version\_in\_sec parameter to limit the permissible deviation between the data writing timestamp and the current system time. The value of this parameter is expressed in seconds. You can specify this parameter when creating a table and modify it by using the UpdateTable operation.
-   stream\_spec: optional. The Stream-related settings.
    -   enable\_stream: specifies whether to enable Stream.
    -   expiration\_time: the expiration time of the Stream data of the table. Unit: hours. Earlier modification records are deleted.

Response format

``` {#codeblock_r6g_qan_hsm .language-php}
    []      
```

Response format description

The response is empty. If an error occurs, the system throws an exception.

Example

The following code provides an example of how to create a table with three primary key columns, set the reserved read and write throughput to 0 and TTL to -1, and then enable Stream.

``` {#codeblock_ms3_zvs_cdh .language-php}
        // Create a schema for the primary key columns, including the number, names, and types of the primary key columns.
        // The first primary key column (partition column) is named PK0 and belongs to the integer type.
        // The second primary key column is named PK1 and belongs to the string type.
        // The third primary key column is named PK2 and belongs to the binary type.
        $result = $client->createTable([
            'table_meta' => [
                'table_name' => 'SampleTable', 
                'primary_key_schema' => [
                    ['PK0', PrimaryKeyTypeConst::CONST_INTEGER], 
                    ['PK1', PrimaryKeyTypeConst::CONST_STRING],
                    ['PK2', PrimaryKeyTypeConst::CONST_BINARY]
                ]
            ], 
            'reserved_throughput' => [
                'capacity_unit' => [
                    'read' => 0, 
                    'write' => 0
                ]
            ],
            'table_options' => [
                'time_to_live' => -1,   
                'max_versions' => 2,    
                'deviation_cell_version_in_sec' => 86400  
            ],
            'stream_spec' => [
                'enable_stream' => true,
                'expiration_time' => 24
            ]
        ]);       
```

