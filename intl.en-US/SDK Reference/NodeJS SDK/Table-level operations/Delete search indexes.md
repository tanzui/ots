# Delete search indexes {#concept_l13_5vk_2gb .concept}

## DeleteSearchIndex {#section_uxw_5vk_2gb .section}

You can call the DeleteSearchIndex operation to delete a search index. Examples

```
client.deleteSearchIndex({
    tableName: TABLE_NAME, // Set the table name.
    indexName: INDEX_NAME  // Set the search index name.
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});
```

