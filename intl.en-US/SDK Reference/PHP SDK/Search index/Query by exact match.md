# Query by exact match {#concept_422892 .concept}

## TermQuery {#section_skd_3yk_1x9 .section}

You can call the TermQuery operation to query data that exactly matches the specified value of a field. When you query a Text string, Table Store tokenizes the string and exactly matches any of the tokens.

For example, Table Store tokenizes text string "tablestore is cool" into "tablestore", "is", and "cool." When you specify any of these tokens as a query string, you can retrieve the query result that contains the token.

 **Example** 

``` {#codeblock_tmh_eoc_ost}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 2,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::TERM_QUERY,
            'query' => array(
                'field_name' => 'keyword',
                'term' => 'keyword'
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
        'return_type' => ColumnReturnTypeConst::RETURN_ALL,
        'return_names' => array('keyword', 'long')
    )
);
$response = $otsClient->search($request);
```

## TermsQuery {#section_grf_g2y_1ka .section}

You can call the TermsQuery operation to query data that exactly matches the specified field values. TermsQuery is similar to TermQuery. The difference is that TermsQuery supports multiple terms. You can retrieve query results that match any of these terms.

 **Example** 

``` {#codeblock_9mj_zyy_l80}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index',
    'search_query' => array(
        'offset' => 0,
        'limit' => 5,
        'get_total_count' => true,
        'query' => array(
            'query_type' => QueryTypeConst::TERMS_QUERY,
            'query' => array(
                'field_name' => 'keyword',
                'terms' => array(
                    "keyword",
                    "php"
                )
            )
        ),
        'sort' => array(
            array(
                'field_sort' => array(
                    'field_name' => 'long',
                    'order' => SortOrderConst::SORT_ORDER_DESC,
                    'mode' => SortModeConst::SORT_MODE_AVG
                )
            )
        )
    ),
    'columns_to_get' => array(
        'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
        'return_names' => array('keyword', 'long')
    )
);
$response = $otsClient->search($request);
```

