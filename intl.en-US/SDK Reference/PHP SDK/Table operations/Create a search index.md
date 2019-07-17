# Create a search index {#concept_473363 .concept}

## CreateSearchIndex {#section_faz_rk8_x4m .section}

If a table needs to support complex queries, you must create a search index for the table. When creating a search index, you must specify the table\_name, index\_name, and index\_meta parameters.

-   table\_name: the name of the table for which the search index is created.
-   index\_name: the name of the search index to be created.
-   index\_meta: the index schema, including the index\_setting, field\_schemas, and index\_sort parameters.
    -   field\_schemas: the list of FieldSchemas. Each FieldSchema contains the following parameters:
        -   \(Required\) field\_fame: String. The name of the field to be indexed, that is, the column name. The field can be a primary key column or an attribute column.
        -   \(Required\) field\_type: FieldTypeConst::XXX. For more information, see the description of field types for a search index.
        -   \(Optional\) is\_array: Boolean. Specifies whether the column is an array. A value of true indicates that the column is an array. Data written to the column must be a JSON array, such as \["a","b","c"\]. You do not need to explicitly specify this parameter for nested columns because they are arrays.
        -   \(Optional\) index: Boolean. Specifies whether to index the column.
        -   \(Optional\) index\_options: the index configuration options.
        -   \(Optional\) analyzer: the tokenizer.
        -   \(Optional\) enable\_sort\_and\_agg: Boolean. Specifies whether to enable sorting and statistics collection.
        -   \(Optional\) store: Boolean. Specifies whether to store the values of the field in the search index. A value of true indicates that you can read the values of the field directly from the search index without the need to query the table. This optimizes query performance.
    -   \(Optional\) index\_setting:
        -   \(Optional, advanced feature\) routing\_fields: the custom routing fields. You can specify some primary key columns as routing fields. Table Store distributes data that is written to a search index to different partitions based on the specified routing fields. The data with the same routing field values is distributed to the same partition.
    -   \(Optional\) index\_sort: specifies how the index is sorted. The index is sorted in the same way as the primary key of the table by default.
        -   sorters: The array of sorting methods. Valid values: primary\_key\_sort and field\_sort.
            -   primary\_key\_sort:
                -   order: Default value: SortOrderConst::SORT\_ORDER\_ASC.
            -   field\_sort: specifies a field for index sorting.
                -   field\_name: the field for index sorting.
                -   order: Default value: SortOrderConst::SORT\_ORDER\_ASC.

 **Method** 

``` {#codeblock_7v7_f1r_hns}
    /**
     * Create a search index.
     * @api
     *
     * @param [] $request
     *            The request parameters, including the table name and index configuration.
     * @return [] The response.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     * @example "src/examples/CreateSearchIndex.php"
     */
    public function createSearchIndex(array $request)
```

 **Example** 

``` {#codeblock_ct5_e0e_mjq}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'schema' => array(
        'field_schemas' => array(
            array(
                'field_name' => 'keyword',
                'field_type' => FieldTypeConst::KEYWORD,
                'index' => true,
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'text',
                'field_type' => FieldTypeConst::TEXT,
                'analyzer' => 'single_word',
                'index' => true,
                'enable_sort_and_agg' => false,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'geo',
                'field_type' => FieldTypeConst::GEO_POINT,
                'index' => true,
                'index_options' => 'DOCS',
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'long',
                'field_type' => FieldTypeConst::LONG,
                'index' => true,
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'double',
                'field_type' => FieldTypeConst::DOUBLE,
                'index' => true,
                'enable_sort_and_agg' => true,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'boolean',
                'field_type' => FieldTypeConst::BOOLEAN,
                'index' => true,
                'enable_sort_and_agg' => false,
                'store' => true,
                'is_array' => false
            ),
            array(
                'field_name' => 'array',
                'field_type' => FieldTypeConst::KEYWORD,
                'index' => true,
                'enable_sort_and_agg' => false,
                'store' => true,
                'is_array' => true
            ),
            array(
                'field_name' => 'nested',
                'field_type' => FieldTypeConst::NESTED,
                'index' => false,
                'enable_sort_and_agg' => false,
                'store' => false,
                'field_schemas' => array(
                    array(
                        'field_name' => 'nested_keyword',
                        'field_type' => FieldTypeConst::KEYWORD,
                        'index' => false,
                        'enable_sort_and_agg' => false,
                        'store' => false,
                        'is_array' => false
                    )
                )
            ),
        ),
        'index_setting' => array(
            'routing_fields' => array("pk1")
        ),
//        "index_sort" => array(// You cannot specify this parameter for nested columns.
//            array(
//                'field_sort' => array(
//                    'field_name' => 'keyword',
//                    'order' => SortOrderConst::SORT_ORDER_ASC,
//                    'mode' => SortModeConst::SORT_MODE_AVG,
//                )
//            ),
//            array(
//                'pk_sort' => array(
//                    'order' => SortOrderConst::SORT_ORDER_ASC
//                )
//            ),
//        )
    )
);
$response = $otsClient->createSearchIndex();
```

