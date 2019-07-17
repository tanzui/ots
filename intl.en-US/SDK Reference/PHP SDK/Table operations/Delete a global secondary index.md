# Delete a global secondary index {#concept_473414 .concept}

## DropIndex {#section_ft3_b8p_4iz .section}

You can call the DropIndex operation to delete a global secondary index.

 **Method** 

``` {#codeblock_mkz_dq4_ywj}
    /**
     * Delete a global secondary index.
     * @api
     *
     * @param [] $request
     *            The request parameter, which is the table name.
     * @return [] The response.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     * @example "src/examples/DropIndex.php"
     */
    public function dropIndex(array $request)
```

 **Example** 

``` {#codeblock_pce_n77_lgs}
$request = array (
    'table_name' => 'MainTableName',
    'index_name' => 'IndexName'
);
$otsClient->dropIndex( $request );
```

