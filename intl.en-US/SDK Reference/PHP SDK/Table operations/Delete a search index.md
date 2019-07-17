# Delete a search index {#concept_473399 .concept}

## DeleteSearchIndex {#section_spv_8xe_pxu .section}

You can call the DeleteSearchIndex operation to delete a specified search index from a specified table.

 **Method** 

``` {#codeblock_rg2_z9p_8d3}
     * Delete a search index.
     * @api
     *
     * @param [] $request
     *            The request parameter, which is the table name.
     * @return [] The response.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     * @example "src/examples/DeleteSearchIndex.php"
     */
    public function deleteSearchIndex(array $request)
```

 **Example** 

``` {#codeblock_3pw_243_jtz}
$request = array(
    'table_name' => 'php_sdk_test',
    'index_name' => 'php_sdk_test_search_index'
);
$response = $otsClient->deleteSearchIndex($request);
```

