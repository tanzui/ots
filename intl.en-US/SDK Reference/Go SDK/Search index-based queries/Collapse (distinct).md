# Collapse \(distinct\)

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Precautions

-   If you use the collapse feature, you must implement paging by specifying offset and size instead of next\_token.
-   If you aggregate and collapse a result set at the same time, the result set is aggregated before it is collapsed.
-   If you collapse the query results, the total number of groups of the returned results is determined by the sum of the offset and size values. A maximum of 10,000 groups can be returned.
-   The total number of rows returned in the results indicates the number of returned rows before you use the collapse feature. After the result set is collapsed, you cannot query the total number of the returned groups.

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|Specifies the name of the column based on which the result set is collapsed. Only columns whose values are of the LONG, DOUBLE, and KEYWORD data types are supported.|

## Examples

```
func QueryWithCollapse(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}
    searchRequest.SetTableName(tableName)       // Set the name of the table.
    searchRequest.SetIndexName(indexName)       // Set the name of the search index.

    searchQuery := search.NewSearchQuery()
    searchQuery.SetQuery(&search.MatchQuery{    // Create a query condition.
        FieldName:          "user_id",
        Text:               "00002",
    })
    searchQuery.SetCollapse(&search.Collapse{
        FieldName: "product_name",              // Use the product_name field for collapse.
    })
    searchQuery.SetOffset(0)
    searchQuery.SetLimit(100)
    searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{ReturnAll:true}) // Return all columns.
    searchRequest.SetSearchQuery(searchQuery)

    searchResponse, err := client.Search(searchRequest)   // Execute the query.
    if err ! = nil {
        fmt.Println("Failed to search with error:", err)
        return
    }
    for _, row := range searchResponse.Rows {           // Display the rows returned.
        jsonBody, err := json.Marshal(row)
        if err ! = nil {
            panic(err)
        }
        fmt.Println("Row: ", string(jsonBody))
    }
}
```

