# Query by prefix {#concept_hmk_bq5_hgb .concept}

## PrefixQuery {#section_hh3_gs5_hgb .section}

You can use PrefixQuery to specify a prefix as a filtering condition. When a table contains a Text string, Table Store tokenizes the string and matches any of the tokens with the specified prefix.

```
/**
 * Search the Col_Keyword column of the table for data with the prefix that is an exact match of "hangzhou."
 */
func PrefixQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.PrefixQuery{} // Set the query type to PrefixQuery.
	query.FieldName = "Col_Keyword" // Set the field that you want to match.
	query.Prefix = "hangzhou" // Specify the prefix.
	searchQuery := search.NewSearchQuery()
	searchQuery.SetQuery(query)
	searchQuery.SetGetTotalCount(true)
	searchRequest.SetSearchQuery(searchQuery)
	// Set ColumnsToGet to true to return all columns that match the filtering condition.
	searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
		ReturnAll:true,
	})
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
}
```

