# Delete search indexes {#concept_h4h_d45_hgb .concept}

You can call the DeleteSearchIndex operation to delete a search index.

**Examples**

```

func DeleteSearchIndex(client *tablestore.TableStoreClient, tableName string, indexName string) {
	request := &tablestore.DeleteSearchIndexRequest{}
	request.TableName = tableName // Set the table name.
	request.IndexName = indexName // Set the search index name. 
	resp, err := client.DeleteSearchIndex(request) // Call a client to delete the specified search index.

	if err ! = nil {
		fmt.Println("error: ", err)
		return
	}
	fmt.Println("DeleteSearchIndex finished, requestId: ", resp.ResponseInfo.RequestId)
}
```

