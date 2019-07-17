# Create a global secondary index {#concept_473410 .concept}

## CreateIndex {#section_aay_x0n_ul3 .section}

You can call the CreateIndex operation to create a global secondary index for an existing table.

 **Method** 

``` {#codeblock_1be_qpb_1gf}
    /**
     * Create a global secondary index.
     * @api
     *
     * @param [] $request
     *            The request parameter, which is the table name.
     * @return [] The response.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     * @example "src/examples/CreateIndex.php" 20
     */
    public function createIndex(array $request)
```

 **Example** 

``` {#codeblock_jyr_t4a_m8y}
$request = array(
    'table_name' => 'MainTableName',
    'index_meta' => array(
        'name' => self::$indexName1,
        'primary_key' => array('col1'),
        'defined_column' => array('col2')
    )
);
$this->otsClient->createIndex($request);
```

## CreateTable {#section_84u_7gx_pj0 .section}

You can call the CreateTable operation to create a global secondary index when creating a table.

 **Example** 

``` {#codeblock_ikv_1lf_rpx}
$request = array (
    'table_meta' => array (
        'table_name' => self::$tableName, // The table name is testGlobalTableName.
        'primary_key_schema' => array (
            array('PK0', PrimaryKeyTypeConst::CONST_INTEGER),
            array('PK1', PrimaryKeyTypeConst::CONST_STRING)
        ),
        'defined_column' => array(
            array('col1', DefinedColumnTypeConst::DCT_STRING),
            array('col2', DefinedColumnTypeConst::DCT_INTEGER)
        )
    ),

    'reserved_throughput' => array (
        'capacity_unit' => array (
            'read' => 0, // Set both the reserved read throughput and reserved write throughput to 0.
            'write' => 0
        )
    ),
    'table_options' => array(
        'time_to_live' => -1,   // The data lifecycle. Unit: seconds. A value of -1 indicates that data never expires.
        'max_versions' => 1,    // The maximum number of data versions.
        'deviation_cell_version_in_sec' => 86400   // The valid data version offset. Unit: seconds.
    ),
    'index_metas' => array(
        array(
            'name' => self::$indexName1,
            'primary_key' => array('col1'),
            'defined_column' => array('col2')
        ),
        array(
            'name' => self::$indexName2,
            'primary_key' => array('PK1'),
            'defined_column' => array('col1', 'col2')
        )
    )
);
$this->otsClient->createTable($request);
```

