# DeleteIndex {#concept_tm4_qfh_1gb .concept}

You can call this operation to delete a global secondary index table that you specify in a primary table. This operation does not affect other indexes.

## Example {#section_fpd_wfh_1gb .section}

```
    private static void deleteIndex(SyncClient client) {
        DeleteIndexRequest request = new DeleteIndexRequest(TABLE_NAME, INDEX_NAME); // The name of the index table that you want to delete and the name of the corresponding primary table.
        client.deleteIndex(request);
    }
```

