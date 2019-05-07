# List search indexes {#concept_hl1_b45_hgb .concept}

You can call the ListSearchIndex operation to list all search indexes created for a table.

**Examples**

```
func ListSearchIndex(client *tablestore.TableStoreClient, tableName string) {
	request := &tablestore.ListSearchIndexRequest{}
	request.TableName = tableName // Set the table name.
	resp, err := client.ListSearchIndex(request) // Obtain all search indexes created for the table.
	if err ! = nil {
		fmt.Println("error: ", err)
		return
	}
	for _, info := range resp.IndexInfo {
		fmt.Printf("%#v\n", info) // Display the information about search indexes.
	}
	fmt.Println("ListSearchIndex finished, requestId:", resp.ResponseInfo.RequestId)
}
```

