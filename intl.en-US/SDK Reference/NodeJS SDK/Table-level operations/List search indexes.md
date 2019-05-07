# List search indexes {#concept_odb_4vk_2gb .concept}

## ListSearchIndex {#section_sfq_4vk_2gb .section}

You can call the ListSearchIndex operation to list all search indexes created for a table. Examples

```
client.listSearchIndex({
    tableName: TABLE_NAME // Set the table name.
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});


```

