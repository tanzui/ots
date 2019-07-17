# Obtain search indexes of a table {#concept_473364 .concept}

## ListSearchIndex {#section_4rq_v6l_4kc .section}

You can call the ListSearchIndex operation to obtain all search indexes created for a table.

 **Method** 

``` {#codeblock_s9y_g3i_r0b}
     * Obtain all search indexes created for a table. 
     * @api
     *
     * @param [] $request
     *            The request parameter, which is the table name.
     * @return [] The response.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     * @example "src/examples/ListSearchIndex.php" 
     */
    public function listSearchIndex(array $request)
```

 **Example** 

``` {#codeblock_dhr_9u8_v1r}
$request = array(
    'table_name' => 'php_sdk_test',
);
$response = $otsClient->listSearchIndex($request);
```

