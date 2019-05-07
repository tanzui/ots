# Query by combination of conditions {#concept_hhl_fq5_hgb .concept}

## BoolQuery {#section_dw2_f55_hgb .section}

You can use BoolQuery to set a combination of filtering conditions. A BoolQuery contains one or more subqueries as filtering conditions. You can obtain the rows that match the filtering conditions. You can combine these subqueries in different ways. If you specify these subqueries as mustQueries, Table Store returns the result that meets all filtering conditions. If you specify these subqueries as mustNotQueries, Table Store returns the result that meets none of the filtering conditions. You can configure the following parameters for BoolQuery:

```
/**
 * The document must exactly match all subqueries.
 */
List<Query> mustQueries;
/**
 * The document must not match any subquery.
 */
List<Query> mustNotQueries;
/**
 * The document must completely match all subfilters. The filter is similar to the query. The difference is that the score is not calculated if the filter is used.
 */
List<Query> filterQueries;
/**
 * The document must match the number of filtering conditions that is specified by minimumShouldMatch. The higher relevance results in higher scores. 
 */
List<Query> shouldQueries;
/**
 * Set minimumShouldMatch. The default value is 1.
 */
Integer minimumShouldMatch;
```

BoolQuery and Subqueries of BoolQuery can be a query of any type. Therefore, you can use BoolQuery to implement a complex combination of queries.

Examples

```
/**
 * Use BoolQuery to query data that matches a combination of conditions.
 */
func BoolQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)

	/**
	 * Condition 1: Use RangeQuery to query data where the value of Col_Long is greater than 3.
	 */
	rangeQuery := &search.RangeQuery{}
	rangeQuery.FieldName = "Col_Long"
	rangeQuery.GT(3)

	/**
	 * Condition 2: Set MatchQuery to query data where the value of Col_Keyword matches "hangzhou."
	 */
	matchQuery := &search.MatchQuery{}
	matchQuery.FieldName = "Col_Keyword"
	matchQuery.Text = "hangzhou"

	{
		/**
		 * Create a BoolQuery where Condition 1 and Condition 2 must be met.
		 */
		boolQuery := &search.BoolQuery{
			MustQueries: []search.Query{
				rangeQuery,
				matchQuery,
			},
		}
		searchQuery := search.NewSearchQuery()
		searchQuery.SetQuery(boolQuery)
		searchRequest.SetSearchQuery(searchQuery)
		searchResponse, err := client.Search(searchRequest)
		if err ! = nil {
			fmt.Printf("%#v", err)
			return
		}
		fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
		fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
		fmt.Println("RowCount: ", len(searchResponse.Rows))
	}
	{
		/**
		 * Create a BoolQuery where at least either of the conditions must be met.
		 */
		boolQuery := &search.BoolQuery{
			ShouldQueries: []search.Query{
				rangeQuery,
				matchQuery,
			},
			MinimumShouldMatch: proto.Int32(1),
		}
		searchQuery := search.NewSearchQuery()
		searchQuery.SetQuery(boolQuery)
		searchRequest.SetSearchQuery(searchQuery)
		searchResponse, err := client.Search(searchRequest)
		if err ! = nil {
			fmt.Printf("%#v", err)
			return
		}
		fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
		fmt.Println("TotalCount: ", searchResponse.TotalCount) // Check the total number of matched rows.
		fmt.Println("RowCount: ", len(searchResponse.Rows))
	}
}


```

