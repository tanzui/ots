# CreateIndex {#concept_gfq_nhh_1gb .concept}

You can call this operation to create a global secondary index table in an existing primary table.

## Description {#section_o4t_rhh_1gb .section}

|Parameter|Definition|Description|
|:--------|:---------|:----------|
|IndexName|The name of the index table.|None|
|List<String\> primaryKey|The type of the index table.|Table Store only supports `IT_GLOBAL_INDEX`.|
|List<String\> definedColumns|The attribute columns in the index table.|This is a group of columns predefined in the primary table.|
|IndexType|The type of the index table.|Table Store only supports `IT_GLOBAL_INDEX`.|
|IndexUpdateMode|The mode of updating the specified index table.|Table Store only supports `IUM_ASYNC_INDEX`.|

## Example {#section_hlj_qhh_1gb .section}

```
    private static void createIndex(SyncClient client) {
        IndexMeta indexMeta = new IndexMeta(INDEX2_NAME); // The name of the index table that you want to create.
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_1); // Add a primary key column to the index table.
        indexMeta.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2); // Add a primary key column to the index table.
        indexMeta.addDefinedColumn(DEFINED_COL_NAME_2); // Add an attribute column to the index table.
        CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, false);
        client.createIndex(request);
    }

}
```

