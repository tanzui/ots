# Query by a combination of filtering conditions {#concept_422941 .concept}

You can call the BoolQuery operation to query data based on a combination of filtering conditions. A query contains one or more subqueries as filtering conditions. Table Store returns the rows that match the subqueries.

You can combine subqueries in different ways. If you specify subqueries as must\_queries, Table Store returns the result that matches all the subqueries. If you specify subqueries as must\_not\_queries, Table Store returns the result that does not match any of the subqueries.

``` {#codeblock_n6p_mt9_ghs}
/**
 Use the BoolQuery operation to query data based on a combination of filtering conditions. The combination methods are as follows:
 * must_queries: (A AND B) Conditions 1 and 2 must be met at the same time.
 * should_queries: (A OR B) At least condition 1 or condition 2 must be met.
 * filter_queries: (A AND B) The document must completely match all subfilters. filter_queries are similar to must_queries. The difference is that the scores are not calculated in filter_queries.
 * must_not_queries: (! A AND ! B) Neither condition 1 nor condition 2 is met.
 * minimum_should_match: The minimum number of should_queries that must be met. The default value is 1.
 */
```

## must\_queries \(A AND B\) {#section_xls_n9f_cxr .section}

 **Example** 

``` {#codeblock_xoe_93h_zam}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::BOOL_QUERY,
            'query' => array(
                'must_queries' => array(
                    array(
                        'query_type' => QueryTypeConst::TERM_QUERY,
                        'query' => array(
                            'field_name' => 'keyword',
                            'term' => 'keyword'
                        )
                    ),
                    array(
                        'query_type' => QueryTypeConst::RANGE_QUERY,
                        'query' => array(
                            'field_name' => 'long',
                            'range_from' => 100,
                            'include_lower' => true,
                            'range_to' => 101,
                            'include_upper' => false
                        )
                    )
                ),
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'keyword',
                    'order' => SortOrderConst::SORT_ORDER_ASC
                )
            ),
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('keyword', 'long')
    )
);
$response = $otsClient->search($request);
```

## must\_not\_queries \(! A AND ! B\) {#section_oz4_74n_m5u .section}

 **Example** 

``` {#codeblock_i42_obs_yjg}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::BOOL_QUERY,
            'query' => array(
                'must_not_queries' => array(
                    array(
                        'query_type' => QueryTypeConst::TERM_QUERY,
                        'query' => array(
                            'field_name' => 'keyword',
                            'term' => 'keyword'
                        )
                    ),
                    array(
                        'query_type' => QueryTypeConst::RANGE_QUERY,
                        'query' => array(
                            'field_name' => 'long',
                            'range_from' => 100,
                            'include_lower' => true,
                            'range_to' => 101,
                            'include_upper' => false
                        )
                    )
                ),
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'keyword',
                    'order' => SortOrderConst::SORT_ORDER_ASC
                )
            ),
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('keyword', 'long')
    )
);
$response = $otsClient->search($request);
```

## filter\_queries \(A AND B\) {#section_c0y_7sh_v89 .section}

 **Example** 

``` {#codeblock_qo5_yyn_02e}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::BOOL_QUERY,
            'query' => array(
                'filter_queries' => array(
                    array(
                        'query_type' => QueryTypeConst::TERM_QUERY,
                        'query' => array(
                            'field_name' => 'keyword',
                            'term' => 'keyword'
                        )
                    ),
                    array(
                        'query_type' => QueryTypeConst::RANGE_QUERY,
                        'query' => array(
                            'field_name' => 'long',
                            'range_from' => 100,
                            'include_lower' => true,
                            'range_to' => 101,
                            'include_upper' => false
                        )
                    )
                ),
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'keyword',
                    'order' => SortOrderConst::SORT_ORDER_ASC
                )
            ),
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('keyword', 'long')
    )
);
$response = $otsClient->search($request);
```

## should\_queries \(A OR B\) {#section_sbe_v7n_p97 .section}

 **Example** 

``` {#codeblock_to8_peb_fag}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::BOOL_QUERY,
            'query' => array(
                'should_queries' => array(
                    array(
                        'query_type' => QueryTypeConst::TERM_QUERY,
                        'query' => array(
                            'field_name' => 'keyword',
                            'term' => 'keyword'
                        )
                    ),
                    array(
                        'query_type' => QueryTypeConst::RANGE_QUERY,
                        'query' => array(
                            'field_name' => 'long',
                            'range_from' => 100,
                            'include_lower' => true,
                            'range_to' => 101,
                            'include_upper' => false
                        )
                    )
                ),
                'minimum_should_match' => 1
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'keyword',
                    'order' => SortOrderConst::SORT_ORDER_ASC
                )
            ),
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('keyword', 'long')
    )
);
$response = $otsClient->search($request);
```

