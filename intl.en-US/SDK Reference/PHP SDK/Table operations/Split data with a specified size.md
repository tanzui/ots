# Split data with a specified size {#concept_473551 .concept}

## ComputeSplitsBySize {#section_sqi_xvz_tu0 .section}

API description: [ComputeSplitPointsBySize](../../../../intl.en-US/API Reference/Operations/ComputeSplitPointsBySize.md#)

You can call the ComputeSplitsBySize operation to logically split data in a table into several partitions whose sizes are close to the specified size. This operation returns the split points between partitions and prompt about hosts where the partitions reside. This operation is generally used for execution plans on computing engines, such as concurrency plans.

 **Method** 

``` {#codeblock_jvv_stu_nao .language-php}
    /**
     * Logically split data in a table into several partitions whose sizes are close to the specified size, and return the split points between partitions and prompt about hosts where the partitions reside.
     * This operation is generally used for execution plans on computing engines, such as concurrency plans.
     * @api
     * @param [] $request The request parameters.
     * @return [] The response.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     */
    public function computeSplitPointsBySize(array $request)
			
```

 **Request format** 

``` {#codeblock_yjd_w1m_d59 .language-php}
$result = $client->ComputeSplitsBySize([
    'table_name' => '<string>', // Required.
    'split_size' => <integer>   // Required.
]);     
```

 **Request format description** 

-   table\_name: the table name.
-   split\_size: the approximate size of each partition. Unit: megabytes.

 **Response format** 

``` {#codeblock_q2h_uge_x2o .language-php}
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

 **Response format description** 

-   consumed: the number of CUs consumed by this operation.
    -   capacity\_unit: the number of read and write CUs consumed.
        -   read: the number of read CUs consumed.
        -   write: the number of write CUs consumed.
-   primary\_key\_schema: the schema of the primary key of the table, which is the same as that specified when the table was created.
-   splits: the split points between partitions.
    -   lower\_bound: the minimum value in the range of the primary key. Note: This parameter value can be used by GetRange.
        -   Each item contains the primary key name, primary key value \(PrimaryKeyValue\), and primary key type \(PrimaryKeyType\).
        -   PrimaryKeyType can be set to PrimaryKeyTypeConst::CONST\_INTEGER, PrimaryKeyTypeConst::CONST\_STRING, PrimaryKeyTypeConst::CONST\_BINARY, PrimaryKeyTypeConst::CONST\_INF\_MIN, or PrimaryKeyTypeConst::CONST\_INF\_MAX, which respectively indicate the INTEGER, STRING \(UTF-8 encoded string\), BINARY, INF\_MIN\(-inf\), and INF\_MAX\(inf\) types.
    -   upper\_bound: the maximum value in the range of the primary key. The format is the same as that of lower\_bound.
    -   location: the prompt about the hosts where the partitions reside. This parameter can be null.

 **Example** 

The following code provides an example of how to split data with a specified size:

``` {#codeblock_rvr_4bg_x4r .language-php}
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

