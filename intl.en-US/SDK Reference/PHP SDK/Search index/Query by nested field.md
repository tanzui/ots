# Query by nested field {#concept_422968 .concept}

You can call the NestedQuery operation to query data based on a nested field. You must use the NestedQuery operation to wrap the nested field before using the nested field to query data. In NestedQuery, you must specify a subquery of any type and the path of the nested field.

**Example**

``` {#codeblock_x7a_nna_pzs}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::NESTED_QUERY,
            'score_mode' => ScoreModeConst::SCORE_MODE_AVG,
            'query' => array(
                'path' => "nested",
                'query' => array(
                    'query_type' => QueryTypeConst::TERM_QUERY,
                    'query' => array(
                        'field_name' => 'nested.nested_keyword',
                        'term' => 'sub'
                    )
                )
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'nested.nested_long',
                    'order' => SortOrderConst::SORT_ORDER_DESC,
                    'nested_filter' => array(
                        'path' => "nested",
                        'query' => array(
                            'query_type' => QueryTypeConst::TERM_QUERY,
                            'query' => array(
                                'field_name' => 'nested.nested_keyword',
                                'term' => 'sub'
                            )
                        )
                    )
                )
            ),
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('nested')
    )
);
$response = $otsClient->search($request);
```

