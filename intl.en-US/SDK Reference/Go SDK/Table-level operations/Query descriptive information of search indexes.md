# Query descriptive information of search indexes {#concept_ajd_c45_hgb .concept}

You can call the DescribeSearchIndex operation to query the descriptive information of a search index, such as its field information and index configurations.

**Examples**

```
func DescribeSearchIndex(client *tablestore.TableStoreClient, tableName string, indexName string) {
	request := &tablestore.DescribeSearchIndexRequest{}
	request.TableName = tableName  // Set the table name.
	request.IndexName = indexName  // Set the search index name.
	resp, err := client.DescribeSearchIndex(request)
	if err ! = nil {
		fmt.Println("error: ", err)
		return
	}
	fmt.Println("FieldSchemas:")
	for _, schema := range resp.Schema.FieldSchemas {
		fmt.Printf("%s\n", schema) // Display the schema information of the fields in the search index.
	}
	fmt.Println("DescribeSearchIndex finished, requestId: ", resp.ResponseInfo.RequestId)
}
```

