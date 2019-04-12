# ListSearchIndex {#concept_vk4_qds_dgb .concept}

You can call this operation to retrieve the list of all Searchindex structures under a specified table.

**Example**

```
private static List<SearchIndexInfo> listSearchIndex(SyncClient client) { 
    ListSearchIndexRequest request = new ListSearchIndexRequest();
    request.setTableName(TABLE_NAME); // Set the name of the table.
    return client.listSearchIndex(request).getIndexInfos(); // Return all Searchindex structures under the specified table.
}

```

