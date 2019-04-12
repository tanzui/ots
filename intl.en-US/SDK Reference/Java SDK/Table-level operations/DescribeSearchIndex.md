# DescribeSearchIndex {#concept_thd_r2s_dgb .concept}

You can call this operation to obtain the details of the Searchindex structure of a specified primary table. The details include Searchindex fields and index configurations.

**Example**

```
private static DescribeSearchIndexResponse describeSearchIndex(SyncClient client) {
    DescribeSearchIndexRequest request = new DescribeSearchIndexRequest();
    request.setTableName(TABLE_NAME); // Set the name of the table.
    request.setIndexName(INDEX_NAME); // Set the name of the index.
    DescribeSearchIndexResponse response = client.describeSearchIndex(request);
    System.out.println(response.jsonize()); // Display the details of the response.
    return response;
}
```

