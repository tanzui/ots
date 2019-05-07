# Query by wildcard character {#concept_xwl_dq5_hgb .concept}

## WildcardQuery {#section_bbq_zs5_hgb .section}

You can use WildcardQuery to match wildcard characters in a query. You can specify a value you want to match as a string that consists of one or more wildcard characters. An asterisk \(\*\) represents any length of characters. A question mark \(?\) represents any single character. For example, when you specify a string "table\*e" as a filtering condition, you can retrieve query results such as "tablestore." The string specified as a filtering condition cannot start with an asterisk \(\*\).

```
/**
 * Search the Col_Keyword column of the table for data that matches "hang*u."
 */
func WildcardQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.WildcardQuery{} // Set the query type to WildcardQuery.
	query.FieldName = "Col_Keyword"
	query.Value = "hang*u"
	searchQuery := search.NewSearchQuery()
	searchQuery.SetQuery(query)
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

