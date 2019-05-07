# Query by exact match {#concept_prg_zp5_hgb .concept}

## TermQuery {#section_vcw_mq5_hgb .section}

You can use TermQuery to query data that exactly matches the specified value of a field. When you query a Text string, Table Store tokenizes the string and exactly matches any of the tokens. For example, Table Store tokenizes a Text string "tablestore is cool" into "tablestore," "is," and "cool." When you specify any of these tokens as a query string, you retrieve the query result that contains the token.

```
/**
 * Search the Col_Keyword column of the table for data that exactly matches "hangzhou."
 */
func TermQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.TermQuery{} // Set the query type to TermQuery.
	query.FieldName = "Col_Keyword" // Set the field that you want to match.
	query.Term = "hangzhou" // Set the value that you want to match.
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

## TermsQuery {#section_adm_pq5_hgb .section}

You can use TermsQuery to query data that exactly matches the specified field values. TermQuery is similar to TermsQuery. Their difference is that TermsQuery supports multiple terms. You can retrieve query results that match all these terms.

```
/**
 * Search the Col_Keyword column of the table for data that matches "hangzhou" or "tablestore."
 */
func TermsQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.TermsQuery{}   // Set the query type to TermsQuery.
	query.FieldName = "Col_Keyword" // Set the fields that you want to match.
	terms := make([]interface{}, 0)
	terms = append(terms, "hangzhou")
	terms = append(terms, "tablestore")
	query.Terms = terms // Set the values that you want to match.
	searchQuery := search.NewSearchQuery()
	searchQuery.SetQuery(query)
	searchQuery.SetLimit(100)
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

