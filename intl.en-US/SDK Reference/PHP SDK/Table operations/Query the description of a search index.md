# Query the description of a search index {#concept_473397 .concept}

## DescribeSearchIndex {#section_0mh_xdx_yxh .section}

You can call the DescribeSearchIndex operation to query the description of a search index for a table, including fields and configurations of the search index.

 **Method** 

``` {#codeblock_tt1_6mg_1oo}
    /**
     * Obtain the description of a search index in a table.
     * @api
     *
     * @param [] $request
     *            The request parameter, which is the table name.
     * @return [] The response.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     * @example "src/examples/DescribeSearchIndex.php" 20
     */
    public function describeSearchIndex(array $request)
```

 **Example** 

``` {#codeblock_t7k_ka9_88n}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index'
);
$response = $otsClient->describeSearchIndex($request);
```

