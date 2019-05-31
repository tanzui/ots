# Table operations {#concept_31758_zh .concept}

The Table Store SDK provides CreateTable, ListTable, DeleteTable, UpdateTable, DescribeTable, and ComputeSplitsBySize for table operations.

## CreateTable {#section_shf_4ck_2fb .section}

[CreateTable](../../../../intl.en-US/API Reference/Operations/CreateTable.md#)

You can call this operation to create a table. When creating a table in Table Store, you must specify TableMeta and TableOptions. You can also set ReservedThroughput.

After you create the table, the server loads splits of the table to a specified node. Therefore, you must wait several seconds before reading from or writing to the table. Otherwise, the system throws an exception. Note: If the system throws an exception, the SDK automatically retries the read or write operation.

**Operation**

```language-php
     /**
     * Create a table. Set the number, name, sequence, and type of primary key columns, reserved read/write throughput, time to live (TTL), and Stream options.
     * Description: https://help.aliyun.com/document_detail/27312.html
     * @api
     * @param [] $request Request parameters.
     * @return [] The response is empty. If the CreateTable operation succeeds, no message is returned. An empty array is returned here to be consistent with other operations.
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function createTable(array $request);
			
```

**Request format**

```language-php



$result = $client->createTable([
    'table_meta' => [                  // REQUIRED
        'table_name' => '<string>', 
        'primary_key_schema' => [
            ['<string>', <PrimaryKeyType>], 
            ['<string>', <PrimaryKeyType>],
            ['<string>', <PrimaryKeyType>, <PrimaryKeyOption>]
        ]
    ], 
    'reserved_throughput' => [         // REQUIRED
        'capacity_unit' => [
            'read' => <integer>, 
            'write' => <integer>
        ]
    ],
    'table_options' => [              // REQUIRED
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

**Request format description**

-   table\_meta: required. This parameter specifies the table name and schema of the primary key.
    -   table\_name: the name of the table.
    -   primary\_key\_schema: the schema of the primary key of the table.
        -   The primary key of a table can include multiple primary key columns. The primary key columns are sorted by the order they are added. For example, the primary key structure PRIMARY KEY \(A, B, C\) is different from PRIMARY KEY \(A, C, B\). Table Store sorts rows based on the values of all primary key columns.
        -   The first primary key column serves as the partition key. Data with the same partition key value is in the same partition. Therefore, we recommend that you have no more than 10 GB of data with the same partition key value. Otherwise, individual partitions are too large and cannot be split. We also recommend that you distribute read and write operations evenly among different partitions to facilitate load balancing.
        -   When creating a table, you only need to define the primary key of the table. Attribute columns do not need to be defined. Different rows can have different data columns and the names of attribute columns can be specified when data is written. Therefore, Table Store is suitable for the storage of semi-structured data. You can dynamically add new data columns during business development.
        -   Each item consists of the primary key name, primary key type \(PrimaryKeyType\), and primary key settings \(PrimaryKeyOption, which is optional\).
        -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, or PrimaryKeyTypeConst::CONST\_BINARY, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), and BINARY types.
        -   PrimaryKeyOption can be set to PrimaryKeyOptionConst::CONST\_PK\_AUTO\_INCR, which indicates the auto-increment column. For more information, see Auto-increment primary key column.
-   reserved\_throughput: required. This parameter specifies the reserved read/write throughput, which is related to billing.
    -   capacity\_unit: When the reserved read/write throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a Pay-As-You-Go basis. By default, the reserved read/write throughput is 0 and all traffic is billed on a Pay-As-You-Go basis. If you want to set a value greater than 0, we recommend that you read the documentation about Table Store billing to avoid unexpected fees. You can set the reserved read/write throughput of capacity instances only to 0. These instances do not allow you to reserve read/write throughput.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
-   table\_options: required. This parameter contains the TTL, MaxVersions, and MaxTimeDeviation configurations of the table.
    -   time\_to\_live: the retention time of data, in seconds.
        -   The latest API version of Table Store supports automatic data expiration. If you do not want data to expire, set time\_to\_live to -1.
        -   Table Store checks whether data has expired based on the data timestamp, current time, and table TTL. If the difference between the current time and the data timestamp is greater than the table TTL, data expires and is cleared by the Table Store server. For more information about the data timestamp, see [Preface](../../../../intl.en-US/Data Models/Preface.md#).
        -   After the TTL is set, the server checks whether data expires based on the data timestamp. If you specify a timestamp when writing data and the timestamp is significantly different from the actual time, unexpected data expiration may occur. For example, if the specified data timestamp is too small, the data may expire and be deleted as soon as it is written. If the specified timestamp is too large, the data may not expire when required. Therefore, when writing data with the TTL specified, make sure that you set a reasonable timestamp.
    -   max\_versions: the maximum number of versions retained by each attribute column.
        -   The latest API version of Table Store supports data models of multiple versions. For more information, see [Preface](../../../../intl.en-US/Data Models/Preface.md#). The max\_versions parameter is used to specify the maximum number of data versions saved by each attribute column. If a new version causes the total number of versions to exceed the value of max\_versions, the server deletes the earliest version to ensure that the number of versions do not exceed this value.
    -   deviation\_cell\_version\_in\_sec: the maximum deviation allowed between the specified version and the current system time when data is written to the version, in seconds.
        -   Table Store supports multiple versions. By default, the system generates a new version number when writing new data. The version number is a timestamp corresponding to the write time in milliseconds. The TTL feature uses this timestamp to determine if data has expired. In addition, you can specify the data writing timestamp. If you set an extremely small timestamp, which deviates from the current time by more than the TTL set for the table, the written data immediately expires. To prevent immediate data expiration, Table Store provides the deviation\_cell\_version\_in\_sec parameter to limit the permissible deviation between the data writing timestamp and the current system time. The value of this parameter is in seconds. You can specify this parameter when creating a table and modify it by using the UpdateTable operation.
-   stream\_spec: optional. This parameter specifies the Stream-related settings.
    -   enable\_stream: indicates whether to enable Stream.
    -   expiration\_time: the expiration time of the Stream data of the table, in hours. Earlier modification records are deleted.

**Result format**

```language-php
    []
			
```

**Result format description**

The response is empty. If an error occurs, the system throws an exception.

**Example**

The following code provides an example of how to create a table with three primary key columns. Set the reserved read/write throughput to 0 and TTL to -1, and enable Stream.

```language-php
        // Create a schema for the primary key columns, including the number, names, and types of the primary key columns
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

## ListTable { .section}

You can call this operation to obtain the names of all tables created in the current instance.

**Operation**

```language-php
    /**
     * Obtain the names of all tables created in the current instance.
     * Description: https://help.aliyun.com/document_detail/27313.html
     * @api
     * @param [] $request Request parameters, which are empty.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function listTable(array $request);
			
```

**Request format**

```language-php
$result = $client->listTable([]);
			
```

**Request format description**

Currently, no parameter is required in the request.

**Result format**

```language-php
    [
        '<string>',
        '<string>',
        '<string>'
    ]
			
```

**Result format description**

The result is a list of strings. Each item indicates a table name.

**Example**

The following code provides an example of how to obtain the names of all tables in an instance:

```language-php
    $result = $otsClient->listTable([]);
			
```

## UpdateTable { .section}

You can call this operation to update ReservedThroughput, TableOptions, and StreamSpecification of a table.

For more information about ReservedThroughput, TableOptions, and StreamSpecification, see the description of the CreateTable operation. Currently, the interval between attempts to modify the value of ReservedThroughput is a minimum of 1 minute.

**Operation**

```language-php
    /**
     * Update a table, including the reserved read/write throughput, configuration information, and Stream configuration of the table.
     * This operation can be used to increase or decrease the reserved read/write throughput.
     * Description: https://help.aliyun.com/document_detail/27315.html
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function updateTable(array $request);
			
```

**Request format**

```language-php



$result = $client->updateTable([
    'table_name' => '<string>',         // REQUIRED
    'reserved_throughput' => [         
        'capacity_unit' => [
            'read' => <integer>, 
            'write' => <integer>
        ]
    ],
    'table_options' => [ 
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

 **Request format description** 

-   The difference between UpdateTable and CreateTable is the TableMeta parameter. Except TableMeta, all other parameters are defined and set in the same way as those of CreateTable. Except table\_name, all other parameters are optional.
-   table\_name: required. This parameter specifies the name of the table.
-   reserved\_throughput: optional. This parameter specifies the reserved read/write throughput, which is related to billing.
    -   capacity\_unit: When the reserved read/write throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a Pay-As-You-Go basis. By default, the reserved read/write throughput is 0 and all traffic is billed on a Pay-As-You-Go basis. If you want to set a value greater than 0, we recommend that you read the documentation about Table Store billing to avoid unexpected fees. You can set the reserved read/write throughput of capacity instances only to 0. These instances do not allow you to reserve read/write throughput.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
-   table\_options: optional. This parameter contains the TTL, MaxVersions, and MaxTimeDeviation configurations of the table.
    -   time\_to\_live: the retention time of data, in seconds.
        -   The latest API version of Table Store supports automatic data expiration. If you do not want data to expire, set time\_to\_live to -1.
        -   Table Store checks whether data has expired based on the data timestamp, current time, and table TTL. If the difference between the current time and the data timestamp is greater than the table TTL, data expires and is cleared by the Table Store server. For more information about the data timestamp, see [Preface](../../../../intl.en-US/Data Models/Preface.md#).
        -   After the TTL is set, the server checks whether data expires based on the data timestamp. If you specify a timestamp when writing data and the timestamp is significantly different from the actual time, unexpected data expiration may occur. For example, if the specified data timestamp is too small, the data may expire and be deleted as soon as it is written. If the specified timestamp is too large, the data may not expire when required. Therefore, when writing data with the TTL specified, make sure that you set a reasonable timestamp.
    -   max\_versions: the maximum number of versions retained by each attribute column.
        -   The latest API version of Table Store supports data models of multiple versions. For more information, see [Preface](../../../../intl.en-US/Data Models/Preface.md#). The max\_versions parameter is used to specify the maximum number of data versions saved by each attribute column. If a new version causes the total number of versions to exceed the value of max\_versions, the server deletes the earliest version to ensure that the number of versions do not exceed this value.
    -   deviation\_cell\_version\_in\_sec: the maximum deviation allowed between the specified version and the current system time when data is written to the version, in seconds.
        -   Table Store supports multiple versions. By default, the system generates a new version number when writing new data. The version number is a timestamp corresponding to the write time in milliseconds. The TTL feature uses this timestamp to determine if data has expired. In addition, you can specify the data writing timestamp. If you set an extremely small timestamp, which deviates from the current time by more than the TTL set for the table, the written data immediately expires. To prevent immediate data expiration, Table Store provides the deviation\_cell\_version\_in\_sec parameter to limit the permissible deviation between the data writing timestamp and the current system time. The value of this parameter is in seconds. You can specify this parameter when creating a table and modify it by using the UpdateTable operation.
-   stream\_spec: optional. This parameter specifies the Stream-related settings.
    -   enable\_stream: indicates whether to enable Stream.
    -   expiration\_time: the expiration time of the Stream data of the table, in hours. Earlier modification records are deleted.

 **Result format** 

```language-php
[
    'capacity_unit_details' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ],
        'last_increase_time' => <integer>,
        'last_decrease_time' => <integer>
    ],
    'table_options' => [
        'time_to_live' => <integer>,
        'max_versions' => <integer>,
        'deviation_cell_version_in_sec => <integer>
    ],
    'stream_details' => [
        'enable_stream' => true || false,
        'stream_id' => '<string>',
        'expiration_time' => <integer>,
        'last_enable_time' => <integer>
    ]
]

			
```

 **Result format description** 

-   capacity\_unit\_details: the reserved read/write throughput configuration, which is related to billing.
    -   capacity\_unit: When the reserved read/write throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a Pay-As-You-Go basis. By default, the reserved read/write throughput is 0 and all traffic is billed on a Pay-As-You-Go basis. If you want to set a value greater than 0, we recommend that you read the documentation about Table Store billing to avoid unexpected fees. You can set the reserved read/write throughput of capacity instances only to 0. These instances do not allow you to reserve read/write throughput.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
    -   last\_increase\_time: the last time the reserved read/write throughput configuration was increased for this table, expressed in UTC time to the second.
    -   last\_decrease\_time: the last time the reserved read/write throughput configuration was decreased for this table, expressed in UTC time to the second.
-   table\_options: the TTL, MaxVersions, and MaxTimeDeviation configurations of the table, which are the same as those in the request.
-   stream\_details: the Stream information of the table.
    -   enable\_stream: indicates whether Stream is enabled for the table.
    -   stream\_id: the Stream ID of the table.
    -   expiration\_time: the expiration time of the Stream data of the table, in hours. Earlier modification records are deleted.
    -   last\_enable\_time: the last time that Stream was enabled.

 **Examples** 

The following code provides an example of how to update the read CU and write CU of a table to 1 and 2, respectively:

```language-php
    $result = $client->updateTable([
        'table_name' => 'SampleTable',
        'reserved_throughput' => [         
            'capacity_unit' => [
                'read' => 1,            // The number of read or write CUs consumed can be separately updated.
                'write' => 2
            ]
        ]
    ]);
			
```

The following code provides an example of how to set the TTL of a table to one day \(86400s\), retain two versions, and set the maximum deviation to 10s:

```language-php
    $result = $client->updateTable([
        'table_name' => 'SampleTable',
        'table_options' => [ 
            'time_to_live' => 86400,   
            'max_versions' => 2,    
            'deviation_cell_version_in_sec' => 10  
        ]
    ]);
			
```

The following code provides an example of how to enable Stream for the table and set the expiration time to 24 hours:

```language-php
    $result = $client->updateTable([
        'table_name' => 'SampleTable',
        'stream_spec' => [
            'enable_stream' => true,
            'expiration_time' => 24
        ]
    ]);
			
```

## DescribeTable { .section}

You can call this operation to query TableMeta, TableOptions, ReservedThroughputDetails, and StreamDetails of a table. TableMeta and TableOptions have already been described in the CreateTable section. Besides containing the reserved read/write throughput, ReservedThroughputDetails shows the last time the throughput was increased or decreased. StreamDetails shows the detailed information about Stream.

 **Operation** 

```language-php
    /**
     * Obtain information about a table, including the primary key design and the reserved read/write throughput.
     * Description: https://help.aliyun.com/document_detail/27316.html
     * @api
     * @param [] $request Request parameters.
     * @return [] Response. 
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function describeTable(array $request); 
			
```

 **Request format** 

```language-php
$result = $client->describeTable([
     'table_name' => '<string>', // REQUIRED
]);
			
```

 **Request format description** 

-   table\_name: the name of the table.

 **Result format** 

```language-php
[
    'table_meta' => [
        'table_name' => '<string>',
        'primary_key_schema' => [
            ['<string>', <PrimaryKeyType>],
            ['<string>', <PrimaryKeyType>, <PrimaryKeyOption>]
        ]
    ],
    'capacity_unit_details' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ],
        'last_increase_time' => <integer>,
        'last_decrease_time' => <integer>
    ],
    'table_options' => [
        'time_to_live' => <integer>,
        'max_versions' => <integer>,
        'deviation_cell_version_in_sec => <integer>
    ],
    'stream_details' => [
        'enable_stream' => true || false,
        'stream_id' => '<string>',
        'expiration_time' => <integer>,
        'last_enable_time' => <integer>
    ]
]

			
```

 **Result format description** 

-   table\_meta: the table name and schema of the primary key, which are the same as those specified when the table is created.
-   capacity\_unit\_details: the reserved read/write throughput configuration, which is related to billing.
    -   capacity\_unit: When the reserved read/write throughput is greater than 0, you are charged based on the reserved volume and duration. Traffic that exceeds the reserved volume is billed on a Pay-As-You-Go basis. By default, the reserved read/write throughput is 0 and all traffic is billed on a Pay-As-You-Go basis. If you want to set a value greater than 0, we recommend that you read the documentation about Table Store billing to avoid unexpected fees. You can set the reserved read/write throughput of capacity instances only to 0. These instances do not allow you to reserve read/write throughput.
        -   read: the reserved read throughput.
        -   write: the reserved write throughput.
    -   last\_increase\_time: the last time the reserved read/write throughput configuration was increased for this table, expressed in UTC time to the second.
    -   last\_decrease\_time: the last time the reserved read/write throughput configuration was decreased for this table, expressed in UTC time to the second.
-   table\_options: the TTL, MaxVersions, and MaxTimeDeviation configurations of the table.
    -   time\_to\_live: the retention time of data, in seconds.
        -   The latest API version of Table Store supports automatic data expiration. If you do not want data to expire, set time\_to\_live to -1.
        -   Table Store checks whether data has expired based on the data timestamp, current time, and table TTL. If the difference between the current time and the data timestamp is greater than the table TTL, data expires and is cleared by the Table Store server. For more information about the data timestamp, see [Preface](../../../../intl.en-US/Data Models/Preface.md#).
        -   After the TTL is set, the server checks whether data expires based on the data timestamp. If you specify a timestamp when writing data and the timestamp is significantly different from the actual time, unexpected data expiration may occur. For example, if the specified data timestamp is too small, the data may expire and be deleted as soon as it is written. If the specified timestamp is too large, the data may not expire when required. Therefore, when writing data with the TTL specified, make sure that you set a reasonable timestamp.
    -   max\_versions: the maximum number of versions retained by each attribute column.
        -   The latest API version of Table Store supports data models of multiple versions. For more information, see [Preface](../../../../intl.en-US/Data Models/Preface.md#). The max\_versions parameter is used to specify the maximum number of data versions saved by each attribute column. If a new version causes the total number of versions to exceed the value of max\_versions, the server deletes the earliest version to ensure that the number of versions do not exceed this value.
    -   deviation\_cell\_version\_in\_sec: the maximum deviation allowed between the specified version and the current system time when data is written to the version, in seconds.
        -   Table Store supports multiple versions. By default, the system generates a new version number when writing new data. The version number is a timestamp corresponding to the write time in milliseconds. The TTL feature uses this timestamp to determine if data has expired. In addition, you can specify the data writing timestamp. If you set an extremely small timestamp, which deviates from the current time by more than the TTL set for the table, the written data immediately expires. To prevent immediate data expiration, Table Store provides the deviation\_cell\_version\_in\_sec parameter to limit the permissible deviation between the data writing timestamp and the current system time. The value of this parameter is in seconds. You can specify this parameter when creating a table and modify it by using the UpdateTable operation.
-   stream\_details: the Stream information of the table.
    -   enable\_stream: indicates whether Stream is enabled for the table.
    -   stream\_id: the Stream ID of the table.
    -   expiration\_time: the expiration time of the Stream data of the table, in hours. Earlier modification records are deleted.
    -   last\_enable\_time: the last time that Stream was enabled.

 **Example** 

The following code provides an example of how to obtain the descriptive information of a table:

```language-php
    $result = $client->describeTable([
         'table_name' => 'mySampleTable',
    ]);
    var_dump($result);
			
```

## DeleteTable { .section}

You can call this operation to delete a specified table from an instance.

 **Operation** 

```language-php
     /**
     * Delete a table based on the table name.
     * Description: https://help.aliyun.com/document_detail/27314.html
     * @api
     * @param [] $request Request parameters.
     * @return [] The response is empty. If the DeleteTable operation succeeds, no message is returned. An empty array is returned here to be consistent with other operations.
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function deleteTable(array $request);
			
```

 **Request format** 

```language-php
$result = $client->deleteTable([
     'table_name' => '<string>', // REQUIRED
]);
			
```

 **Request format description** 

table\_name: the name of the table.

 **Result format** 

```language-php
    []
			
```

 **Result format description** 

The response is empty. If an error occurs, the system throws an exception.

 **Example** 

The following code provides an example of how to delete a table:

```language-php
$result = $otsClient->deleteTable([
    'table_name' => 'MyTable'
]);
			
```

## ComputeSplitsBySize { .section}

You can call this operation to logically split data in a table into several partitions whose sizes are close to the specified size, and return the split points between the partitions and prompt about hosts where the partitions reside. This operation is generally used for execution plans on compute engines, such as concurrency plans.

 **Operation** 

```language-php
    /**
     * Logically split data in a table into several partitions whose sizes are close to the specified size, and return the split points between the partitions and prompt about hosts where the partitions reside.
     * This operation is generally used for execution plans on compute engines, such as concurrency plans.
     * Description: https://help.aliyun.com/document_detail/53813.html
     * @api
     * @param [] $request Request parameters.
     * @return [] Response.
     * @throws OTSClientException Indicates that a parameter check error occurs or the server returns a verification error.
     * @throws OTSServerException Indicates that the Table Store server returns an error.
     */
    public function computeSplitPointsBySize(array $request)
			
```

 **Request format** 

```language-php



$result = $client->ComputeSplitsBySize([
    'table_name' => '<string>', // REQUIRED
    'split_size' => <integer>   // REQUIRED
]);

			
```

 **Request format description** 

-   table\_name: the name of the table.
-   split\_size: the approximate size of each split, in megabytes.

 **Result format** 

```language-php
[
    'consumed' => [
        'capacity_unit' => [
            'read' => <integer>,
            'write' => <integer>
        ]
    ],
    'primary_key_schema' => [
        ['<string>', <PrimaryKeyType>],
        ['<string>', <PrimaryKeyType>, <PrimaryKeyOption>]
    ]
    'splits' => [
        [ 
            'lower_bound' => [ 
                ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>],
                ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
            ],
            'upper_bound' => [
                ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>],
                ['<string>', <PrimaryKeyValue>, <PrimaryKeyType>]
            ],
            'location' => '<string>'
        ],
        // ...
    ]
]
			
```

 **Result format description** 

-   consumed: the number of CUs consumed by this operation.
    -   capacity\_unit: the number of read and write CUs consumed.
        -   read: the number of read CUs consumed.
        -   write: the number of write CUs consumed.
-   primary\_key\_schema: the schema of the primary key of the table, which is the same as that specified when the table is created.
-   splits: the split points between partitions.
    -   lower\_bound: the minimum value in the range of the primary key. Note: This parameter value can be used by GetRange.
        -   Each item contains the primary name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType\).
        -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, PrimaryKeyTypeConst::CONST\_BINARY, PrimaryKeyTypeConst::CONST\_INF\_MIN, or PrimaryKeyTypeConst::CONST\_INF\_MAX, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, INF\_MIN\(-inf\), and INF\_MAX\(inf\) types.
    -   upper\_bound: the maximum value in the range of the primary key. The format is the same as that of lower\_bound.
    -   location: the prompt about the hosts where the split points reside. This parameter can be null.

 **Example** 

The following code provides an example of how to specify the size to split data:

```language-php
    $result = $client->ComputeSplitsBySize([
        'table_name' => 'MyTable', 
        'split_size' => 1
    ]);
    foreach($result['splits'] as $split) {
        print_r($split['location']);    
        print_r($split['lower_bound']);    
        print_r($split['upper_bound']);    
    }
			
```

