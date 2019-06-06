# ListSearchIndex {#concept_226985 .concept}

You can call this operation to retrieve the list of all Search Index structures associated with an instance or a table.

## Description {#section_9rz_f8l_6gc .section}

Name: ListSearchIndex

Parameter:

-   TableName: the name of the target table. If you do not specify this optional parameter, Table Store returns the list of all indexes on the instance. If you specify a table, Table Store returns the list of all Search Index structures associated with the table.

## Example {#section_x8l_8pw_g0u .section}

``` {#codeblock_6h7_4sz_70c}
private static List<SearchIndexInfo> listSearchIndex(SyncClient client) { 
    ListSearchIndexRequest request = new ListSearchIndexRequest();
    request.setTableName(TABLE_NAME); // Set the name of the table.
    return client.listSearchIndex(request).getIndexInfos(); // Return all Search Index structures of the specified table.
}
```

