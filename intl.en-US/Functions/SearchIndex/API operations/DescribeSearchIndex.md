# DescribeSearchIndex {#concept_226980 .concept}

You can call this operation to query the details of a Search Index structure. To use the Search Index feature for a table, you must create a Search Index structure in the table. One table can contain multiple Search Index structures.

## Description {#section_w9z_z89_rfc .section}

Name: DescribeSearchIndex

Parameters:

-   TableName: the name of the target table where you request the details of the Search Index structure.
-   IndexName: the name of the target index.

## Example {#section_9an_z05_hqv .section}

``` {#codeblock_yqf_8q4_fpw}
private static DescribeSearchIndexResponse describeSearchIndex(SyncClient client) {
    DescribeSearchIndexRequest request = new DescribeSearchIndexRequest();
    request.setTableName(TABLE_NAME); // Set the name of the table.
    request.setIndexName(INDEX_NAME); // Set the name of the index.
    DescribeSearchIndexResponse response = client.describeSearchIndex(request);
    System.out.println(response.jsonize()); // Display the details of the response.
    return response;
}
```

