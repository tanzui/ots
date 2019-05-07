# Query by range {#concept_hwl_cq5_hgb .concept}

## RangeQuery {#section_s25_rs5_hgb .section}

You can use RangeQuery to specify a range as a filtering condition. When a table contains a Text string, Table Store tokenizes the string and matches any of the tokens that falls within the specified range.

```
/**
 * Search the table for rows where the value of Col_Long is greater than 3. Table Store sequences these rows by Col_Long in descending order.
 */
func RangeQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	searchQuery := search.NewSearchQuery()
	rangeQuery := &search.RangeQuery{} // Set the query type to RangeQuery.
	rangeQuery.FieldName = "Col_Long" // Set the field you want to match.
	rangeQuery.GT(3) // Specify the range of the field value, which is greater than 3.
	searchQuery.SetQuery(rangeQuery)
	// Sort the query result by Col_Long in descending order.
	searchQuery.SetSort(&search.Sort{
		[]search.Sorter{
			&search.FieldSort{
				FieldName: "Col_Long",
				Order:     search.SortOrder_DESC.Enum(),
			},
		},
	})
	searchRequest.SetSearchQuery(searchQuery)
	searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
		ReturnAll:true,
	})
	searchResponse, err := client.Search(searchRequest)
	if err ! = nil {
		fmt.Printf("%#v", err)
		return
	}
	fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
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

