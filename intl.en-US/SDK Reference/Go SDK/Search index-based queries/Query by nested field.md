# Query by nested field {#concept_yhp_gq5_hgb .concept}

## NestedQuery {#section_gzn_s55_hgb .section}

You cannot directly use a Nested field to query data. You must wrap it in a nested query. To implement the nested query, you must specify a subquery \(which can be any query\) and the path to the nested field.

**Examples**

```
/**
 * For example, the Nested columns contain nested_1 and nested_2. You need to search the col_nested.nested_1 column for data that matches "tablestore."
 */
func NestedQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.NestedQuery{ // Set the query type to NestedQuery.
		Path: "col_nested", // Set the path to the nested field.
		Query: &search.TermQuery{ // Create the subquery of NestedQuery.
			FieldName: "col_nested.nested_1", // Set the field that is prefixed with col_nested.
			Term:      "tablestore",          // Set the value you want to match.
		},
		ScoreMode: search.ScoreMode_Avg,
	}
	searchQuery := search.NewSearchQuery()
	searchQuery.SetQuery(query)
	searchRequest.SetSearchQuery(searchQuery)
	// Set ColumnsToGet to true to return all columns that match the filtering condition.
	searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
		ReturnAll: true,
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

