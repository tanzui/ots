# Query by geographic location {#concept_ixj_2q5_hgb .concept}

Search index-based queries support three geographic location-based query methods: GeoBoundingBoxQuery, GeoDistanceQuery, and GeoPolygonQuery.

## GeoBoundingBoxQuery {#section_ntl_nt5_hgb .section}

You can use GeoBoundingBoxQuery to query data that falls within a rectangular geographical area that you specify in a query. You can specify a rectangular geographical area as a filtering condition. Table Store returns the rows where the value of a field falls within the range of the rectangular geographical area.

```
/**
 * The data type of Col_GeoPoint is GeoPoint. You can obtain the rows where the value of Col_GeoPoint falls within the range of a rectangular geographical area whose top-left vertex is "10,0" and lower-right vertex is "0,10."
 */
func GeoBoundingBoxQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.GeoBoundingBoxQuery{} // Set the query type to GeoBoundingBoxQuery.
	query.FieldName = "Col_GeoPoint"  // Set the field you want to match.
	query.TopLeft = "10,0"  // Specify the coordinates of the top-left vertex.
	query.BottomRight = "0,10" // Specify the coordinates of the lower-right vertex.
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

## GeoDistanceQuery {#section_e3c_pt5_hgb .section}

You can use GeoDistanceQuery to query data that falls within a specified distance from a specified central point. You can specify a central point and a distance from this central point in a query. Table Store returns the rows where the value of a field falls within the distance from the central point.

```
/**
 * Search the table for rows where the value of Col_GeoPoint falls within a specified distance from a specified central point.
 */
func GeoDistanceQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.GeoDistanceQuery{} // Set the query type to GeoDistanceQuery.
	query.FieldName = "Col_GeoPoint"
	query.CenterPoint = "5,5"  // Set the coordinates of a central point.
	query.DistanceInMeter = 10000.0 // You can specify 10,000 as a distance from the central point.
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

## GeoPolygonQuery {#section_zss_pt5_hgb .section}

You can use GeoPolygonQuery to query data that falls within a polygon area that you specify in a query. You can specify a range of a polygon as a filtering condition. Table Store returns the rows where the value of a field falls within the polygon range.

```
/**
 * Search the table for data where the value of Col_GeoPoint falls within the range of the specified polygon.
 */
func GeoPolygonQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
	searchRequest := &tablestore.SearchRequest{}
	searchRequest.SetTableName(tableName)
	searchRequest.SetIndexName(indexName)
	query := &search.GeoPolygonQuery{} // Set the query type to GeoDistanceQuery.
	query.FieldName = "Col_GeoPoint"
	query.Points = []string{"0,0","5,5","5,0"} // Specify the coordinates of vertices of a polygon.
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

