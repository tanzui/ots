# Obtain table names {#concept_473429 .concept}

## ListTable {#section_ooy_tzd_hc9 .section}

API description: [ListTable](../../../../intl.en-US/API Reference/Operations/ListTable.md#)

You can call the ListTable operation to obtain the names of all tables created in the current instance.

**Method**

``` {#codeblock_rv5_wog_rnw .language-php}
    /**
     * Obtain the names of all tables created in the current instance.
     * @api
     * @param [] $request The request parameters, which are empty.
     * @return [] The response. 
     * @throws OTSClientException The exception that is thrown when a parameter check error occurs or the Table Store server returns a verification error.
     * @throws OTSServerException The exception that is thrown when the Table Store server returns an error.
     */
    public function listTable(array $request); 
```

**Request format**

``` {#codeblock_v6w_dtq_gln .language-php}
$result = $client->listTable([]);   
```

**Request format description**

Currently, no parameter is required in the request.

**Response format**

``` {#codeblock_sl4_7r3_dxo .language-php}
    [
        '<string>',
        '<string>',
        '<string>'
    ]        
```

**Response format description**

The result is a list of strings. Each item indicates a table name.

**Example**

The following code provides an example of how to obtain the names of all tables in an instance:

``` {#codeblock_y92_rou_hrv .language-php}
    $result = $otsClient->listTable([]);         
```

