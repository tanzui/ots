# Delete a table {#concept_473548 .concept}

## DeleteTable {#section_sut_6rw_7bx .section}

API description: [DeleteTable](../../../../intl.en-US/API Reference/Operations/DeleteTable.md#)

You can call the DeleteTable operation to delete a specified table from the current instance.

 **Method** 

``` {#codeblock_ei2_lv1_acn .language-php}
     /**
     * Delete a table based on the table name.
     * @api
     * @param [] $request The request parameters.
     * @return [] The response. If the DeleteTable operation succeeds, no message is returned. An empty array is returned here to be consistent with other operations.
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     */
    public function deleteTable(array $request);            
```

 **Request format** 

``` {#codeblock_500_h2g_3om .language-php}
$result = $client->deleteTable([
     'table_name' => '<string>', // Required.
]);           
```

 **Request format description** 

table\_name: the table name.

 **Response format** 

``` {#codeblock_y7e_bi4_pgn .language-php}
    []    
```

 **Response format description** 

The response is empty. If an error occurs, the system throws an exception.

 **Example** 

The following code provides an example of how to delete a table:

``` {#codeblock_65t_2v9_0dj .language-php}
$result = $otsClient->deleteTable([
    'table_name' => 'MyTable'
]);        
```

