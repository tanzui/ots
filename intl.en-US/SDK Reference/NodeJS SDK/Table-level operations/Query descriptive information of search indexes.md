# Query descriptive information of search indexes {#concept_k5h_rvk_2gb .concept}

## DescribeSearchIndex {#section_zqc_svk_2gb .section}

You can call the DescribeSearchIndex operation to query the descriptive information of a search index, such as its field information and index configurations. Examples

```
client.describeSearchIndex({
    tableName: TABLE_NAME, // Set the table name.
    indexName: INDEX_NAME  // Set the search index name.
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

