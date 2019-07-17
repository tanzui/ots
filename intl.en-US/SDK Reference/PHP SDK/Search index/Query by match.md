# Query by match {#concept_422893 .concept}

## MatchAllQuery {#section_y8l_6zg_0g3 .section}

You can call the MatchAllQuery operation to query the total number of rows or any number of rows in a table.

 **Example** 

``` {#codeblock_hgu_jmj_7jc}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 10,
        'get_total_count' => true,
        'collapse' => array(
            'field_name' => 'keyword'
        ),
        'query' => array(
            'query_type' => QueryTypeConst::MATCH_ALL_QUERY
        ),
//        'sort' => array(// Set a specific sorting method if required.
//            array(
//                'field_sort' => array(
//                    'field_name' => 'keyword',
//                    'order' => SortOrderConst::SORT_ORDER_ASC
//                )
//            ),
//        ),
        'token' => null,
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('col1', 'col2')
    )
);
$response = $otsClient->search($request);
```

## MatchQuery {#section_hfs_arv_3j4 .section}

You can call the MatchQuery operation to query a table based on approximate matches. For example, if you search for the phrase "this is", you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is."

 **Example** 

``` {#codeblock_1nj_ifj_7ql}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::MATCH_QUERY,
            'query' => array(
                'field_name' => 'text',
                'text' => 'ots text php keyword',
//                'operator' => QueryOperatorConst::PBAND,
                'operator' => QueryOperatorConst::PBOR,// Use minimum_should_match with OR.
                'minimum_should_match' => 3
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
        'return_names' => array('text')
    )
);
$response = $otsClient->search($request);
```

## MatchPhraseQuery {#section_znk_rni_n52 .section}

You can call the MatchPhraseQuery operation to search matched phrases. MatchPhraseQuery is similar to MatchQuery. The difference is that MatchPhraseQuery matches a phrase as a whole. For example, if you search for the phrase "this is", you can obtain query results such as "..., this is tablestore" and "this is a table." You cannot obtain query results such as "this table is ..." and "is this a table."

 **Example** 

``` {#codeblock_lnl_m9e_fi3}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::MATCH_PHRASE_QUERY,
            'query' => array(
                'field_name' => 'text',
                'text' => 'text keyword'
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
        'return_names' => array('text')
    )
);
$response = $otsClient->search($request);
```

