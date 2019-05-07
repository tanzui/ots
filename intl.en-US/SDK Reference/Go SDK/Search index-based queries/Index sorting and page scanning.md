# Index sorting and page scanning {#concept_inv_hq5_hgb .concept}

## Index sorting {#section_hcz_y55_hgb .section}

The matched data is sorted based on the IndexSort field value when search index-based queries are used. IndexSort is unsuitable for sorting of nested fields. The default IndexSort field value is the name of the primary key column. You can define the IndexSort field value when you create an index. IndexSort determines the default return order when search index-based queries are used. If the IndexSort field value is not set, the result is returned based on the order of the primary key column.

## Specify a sorting method {#section_icz_y55_hgb .section}

You can specify a sorting method for each query. Search index-based queries support the following sorting methods. You can also use multiple sorting methods and prioritize the methods as needed.

**ScoreSort**

You can use ScoreSort to sort the query result by score. ScoreSort is applicable to scenarios such as full-text indexing. Note that ScoreSort must be set to sort the query result by relevance. Otherwise, the query result is returned based on the IndexSort field value.

```
	searchQuery := search.NewSearchQuery()
	searchQuery.SetSort(&search.Sort{
		[]search.Sorter{
			&search.ScoreSort{ 
				Order: search.SortOrder_DESC.Enum(), //Sort the query result in descending order of scores.
			},
		},
	})
```

**PrimaryKeySort**

You can use PrimaryKeySort to sort the query result based on the order of the primary key column.

```
searchQuery := search.NewSearchQuery()
	searchQuery.SetSort(&search.Sort{
		[]search.Sorter{
			&search.PrimaryKeySort{
				Order: search.SortOrder_ASC.Enum(),
			},
		},
	})
```

**FieldSort**

You can use FieldSort to sort the query result based on the order of a specified column.

```
	// Sort the query result by Col_Long in descending order.
	searchQuery.SetSort(&search.Sort{
		[]search.Sorter{
			&search.FieldSort{
				FieldName: "Col_Long",
				Order:     search.SortOrder_DESC.Enum(),
			},
		},
	})
```

Prioritize columns to sort the query result.

```
searchQuery.SetSort(&search.Sort{
		[]search.Sorter{
			&search.FieldSort{
				FieldName: "col1",
				Order:     search.SortOrder_ASC.Enum(),
			},
			&search.FieldSort{
				FieldName: "col2",
				Order:     search.SortOrder_DESC.Enum(),
			},
		},
	})
```

**GeoDistanceSort**

You can use GeoDistanceSort to sort the query result by geographical location.

```
	searchQuery.SetSort(&search.Sort{
		[]search.Sorter{
			&search.GeoDistanceSort{
				FieldName: "location",   // Set the name of the geographical location field.
				Points:    []string{"40,-70"}, // Set the coordinates of a central point. 
			},
		},
	})
```

## Page scanning {#section_gxm_yv5_hgb .section}

**Use Limit and Offset**

When the total number of rows to be obtained is smaller than 2,000, you can set Limit and Offset to scan no more than 2,000 pages and return the result.

```
searchQuery := search.NewSearchQuery()
searchQuery.SetLimit(10)
searchQuery.SetOffset(10) 
```

**Use a token**

If the operation does not complete reading the data that meets the filtering conditions, the server returns NextToken. You can use NextToken to continue reading the subsequent data. The sorting method cannot be set if a token is used. When the token is used, the sorting method used is the same as the previous request, regardless of the default sorting algorithm used by the system or the sorting algorithm defined by the user. Additionally, you cannot set Offset when a token is used. Data is scanned page by page, which results in a slow query.

```
/**
 * Use a token to read data by page.
 * If SearchResponse returns NextToken, you can use this token to initiate the next query.
 * All data that matches the filtering condition is returned until the NextToken field value is nil.
 */
func QueryRowsWithToken(client *tablestore.TableStoreClient, tableName string, indexName string) {
	querys := []search.Query{
		&search.MatchAllQuery{},
		&search.TermQuery{
			FieldName: "Col_Keyword",
			Term:      "tablestore",
		},
	}
	for _, query := range querys {
		fmt.Printf("Test query: %#v\n", query)
		searchRequest := &tablestore.SearchRequest{}
		searchRequest.SetTableName(tableName)
		searchRequest.SetIndexName(indexName)
		searchQuery := search.NewSearchQuery()
		searchQuery.SetQuery(query)
		searchQuery.SetLimit(10)
		searchQuery.SetGetTotalCount(true)
		searchRequest.SetSearchQuery(searchQuery)
		searchResponse, err := client.Search(searchRequest)
		if err ! = nil {
			fmt.Printf("%#v", err)
			return
		}
		rows := searchResponse.Rows
		requestCount := 1
		for searchResponse.NextToken ! = nil {
			searchQuery.SetToken(searchResponse.NextToken)
			searchResponse, err = client.Search(searchRequest)
			if err ! = nil {
				fmt.Printf("%#v", err)
				return
			}
			requestCount++
			for _, r := range searchResponse.Rows {
				rows = append(rows, r)
			}
		}
		fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess)
		fmt.Println("TotalCount: ", searchResponse.TotalCount)
		fmt.Println("RowsSize: ", len(rows))
		fmt.Println("RequestCount: ", requestCount)
	}
}
```

