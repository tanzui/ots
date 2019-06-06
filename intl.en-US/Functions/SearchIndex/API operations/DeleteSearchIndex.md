# DeleteSearchIndex {#concept_226986 .concept}

You can call this operation to delete a Search Index structure.

## Description {#section_yjt_r9h_qtq .section}

Name: DeleteSearchIndex

Parameters:

-   TableName: the name of the target table where you delete the Search Index structure.
-   IndexName: the name of the target index that you want to delete.

## Example {#section_vkh_k74_3mh .section}

``` {#codeblock_r02_3sk_sz2}
private static void deleteSearchIndex(SyncClient client) {
    DeleteSearchIndexRequest request = new DeleteSearchIndexRequest();
    request.setTableName(TABLE_NAME); // Set the name of the table.
    request.setIndexName(INDEX_NAME); // Set the name of the index.
    client.deleteSearchIndex(request); // Use client to delete the target Search Index structure.
}
```

