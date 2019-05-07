# Query by match {#concept_psk_1q5_hgb .concept}

## MatchAllQuery {#section_z1h_3r5_hgb .section}

You can use MatchAllQuery to query the total number of rows or any number of rows in a table.

```
/**
 * Use MatchAllQuery to query the total number of rows in a table.
 */
func MatchAllQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.MatchAllQuery{} // Set the query type to MatchAllQuery.
	searchQuery := search.NewSearchQuery()
	searchQuery.SetQuery(query) 
	searchQuery.SetGetTotalCount(true) 
	searchQuery.SetLimit(0) // Set Limit to 0 to indicate that no data is returned.
	searchRequest.SetSearchQuery(searchQuery)
	searchResponse, err := client.Search(searchRequest)
	if err ! = nil { // Check whether the invocation succeeds.
		fmt.Printf("%#v", err) 
		return
	}
	fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess)
	fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of rows in the table.
}
```

## MatchQuery {#section_qrb_kr5_hgb .section}

You can use MatchQuery to query a table based on approximate matches. For example, you can query data that matches "this is." Table Store returns query results such as "..., this is tablestore," "is this tablestore," "tablestore is cool," "this," and "is."

```

/**
 * Search the Col_Keyword column of the table for data that matches "hangzhou." Table Store returns matched rows and the total number of matched rows.
 */
func MatchQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.MatchQuery{} // Set the query type to MatchQuery.
	query.FieldName = "Col_Keyword" // Set the field that you want to match.
	query.Text = "hangzhou" // Set the value that you want to match.
	searchQuery := search.NewSearchQuery()
	searchQuery.SetQuery(query)
	searchQuery.SetGetTotalCount(true) 
	searchQuery.SetOffset(0) // Set Offset to 0.
	searchQuery.SetLimit(20) // Set Limit to 20 to return up to 20 rows.
	searchRequest.SetSearchQuery(searchQuery)
	searchResponse, err := client.Search(searchRequest)
	if err ! = nil { // Check whether the invocation succeeds.
		fmt.Printf("%#v", err)
		return
	}
	fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
	fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
	fmt.Println("RowCount: ", len(searchResponse.Rows)) // Check the total number of returned rows.
	for _, row := range searchResponse.Rows {
		jsonBody, err := json.Marshal(row)
		if err ! = nil {
			panic(err)
		}
		fmt.Println("Row: ", string(jsonBody)) // If ColumnsToGet is not specified, only primary key columns that match the filtering condition are returned.
	}
	// Set ColumnsToGet to true to return all columns that match the filtering condition.
	searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
		ReturnAll:true,
	})
	searchResponse, err = client.Search(searchRequest)
	if err ! = nil {
		fmt.Printf("%#v", err)
		return
	}
	fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
	fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
	fmt.Println("RowCount: ", len(searchResponse.Rows))
	for _, row := range searchResponse.Rows {
		jsonBody, err := json.Marshal(row)
		if err ! = nil {
			panic(err)
		}
		fmt.Println("Row: ", string(jsonBody))
	}
}
```

## MatchPhraseQuery {#section_bfg_lr5_hgb .section}

You can use MatchPhraseQuery to specify a phrase as a filtering condition. MatchPhraseQuery is similar to MatchQuery. The difference is that MatchPhraseQuery matches a phrase as a whole. For example, if you query a phrase "this is," you can obtain query results such as "..., this is tablestore," and "this is a table." However, you cannot obtain query results such as "this table is ..." and "is this a table."

```
/**
 * Search the Col_Text column of the table for data that matches "hangzhou shanghai." Table Store returns the total number of rows that match the phrase as a whole and matched rows in this query.
 */
func MatchPhraseQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.MatchPhraseQuery{} // Set the query type to MatchPhraseQuery.
	query.FieldName = "Col_Text"  // Set the field that you want to match.
	query.Text = "hangzhou shanghai"  // Set the value that you want to match.
	searchQuery := search.NewSearchQuery()
	searchQuery.SetQuery(query)
	searchQuery.SetGetTotalCount(true) 
	searchQuery.SetOffset(0) // Set Offset to 0.
	searchQuery.SetLimit(20) // Set Limit to 20 to return up to 20 rows.
	searchRequest.SetSearchQuery(searchQuery)
	searchResponse, err := client.Search(searchRequest)
	if err ! = nil {
		fmt.Printf("%#v", err)
		return
	}
	fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
	fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
	fmt.Println("RowCount: ", len(searchResponse.Rows))
	for _, row := range searchResponse.Rows {
		jsonBody, err := json.Marshal(row)
		if err ! = nil {
			panic(err)
		}
		fmt.Println("Row: ", string(jsonBody))
	}
	// Set ColumnsToGet to true to return all columns that match the filtering condition.
	searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
		ReturnAll:true,
	})
	searchResponse, err = client.Search(searchRequest)
	if err ! = nil {
		fmt.Printf("%#v", err)
		return
	}
	fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
	fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
	fmt.Println("RowCount: ", len(searchResponse.Rows))
	for _, row := range searchResponse.Rows {
		jsonBody, err := json.Marshal(row)
		if err ! = nil {
			panic(err)
		}
		fmt.Println("Row: ", string(jsonBody))
	}
}
```

