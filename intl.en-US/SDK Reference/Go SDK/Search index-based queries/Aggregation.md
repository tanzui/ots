# Aggregation

You can use aggregation to obtain the minimum value, maximum value, sum, average, count, and distinct count, and to group by field value, range, geographical distance, filter, and nesting. You can also use multiple aggregations to query data.

## Minimum value

The aggregation method used to return the minimum value of a field. This method is equivalent to the SQL MIN function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported for this field.|
    |Missing|The default value for the field used for the aggregation operation on a row when the field value is not specified.

    -   If the Missing parameter is not specified, the row is ignored.
    -   If the Missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    ```
    /**
     * The price of each product is listed in the product table. Query the minimum price among the products produced in Zhejiang.
     * The equivalent SQL statement: SELECT min(column_price) FROM product where place_of_production = "Zhejiang".
     */
    func min(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.TermQuery{"place_of_production", "Zhejiang"}).
                SetLimit(0).    // If you want to obtain only the aggregate result, you can set the number of matched results to return to 0 to reduce the response time.
                Aggregation(search.NewMinAggregation("min_agg_1", "column_price").Missing(0.00)))
    
        searchResponse, err := client.Search(searchRequest)    // Execute the query.
        aggResults := searchResponse.AggregationResults        // Obtain all aggregate results.
        agg1, err := aggResults.Min("min_agg_1")        // Obtain the aggregate result of min_agg_1.
        if err != nil {
            panic(err)
        }
        if agg1.HasValue() {        // Specify whether the aggregate result of min_agg_1 has a value.
            fmt.Println(agg1.Value)    // Display the aggregate result.
        }
    }
    ```


## Maximum value

The aggregation method used to return the maximum value of a field. This method is equivalent to the SQL MAX function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported for this field.|
    |Missing|The default value for the field used for the aggregation operation on a row when the field value is not specified.

    -   If the Missing parameter is not specified, the row is ignored.
    -   If the Missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    ```
    /**
     * The price of each product is listed in the product table. Query the maximum price among the products produced in Zhejiang.
     * The equivalent SQL statement: SELECT max(column_price) FROM product where place_of_production = "Zhejiang".
     */
    func max(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.TermQuery{"place_of_production", "Zhejiang"}).
                SetLimit(0).    // If you want to obtain only the aggregate result, you can set the number of matched results to return to 0 to reduce the response time.
                Aggregation(search.NewMaxAggregation("max_agg_1", "column_price").Missing(0.00)))
    
        searchResponse, err := client.Search(searchRequest)    // Execute the query.
        aggResults := searchResponse.AggregationResults        // Obtain all aggregate results.
        agg1, err := aggResults.Max("max_agg_1")        // Obtain the aggregate result of max_agg_1.
        if err != nil {
            panic(err)
        }
        if agg1.HasValue() {        // Specify whether the aggregate result of max_agg_1 has a value.
            fmt.Println(agg1.Value)    // Display the aggregate result.
        }
    }
    ```


## Sum

The aggregation method used to return the sum of all rows for a numeric field. This method is equivalent to the SQL SUM function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported for this field.|
    |Missing|The default value for the field used for the aggregation operation on a row when the field value is not specified.

    -   If the Missing parameter is not specified, the row is ignored.
    -   If the Missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    ```
    
    /**
     * The sales volume of each product is listed in the product table. Query the total number of the sold products that are produced in Zhejiang. Set the value of Missing to 10.
     * The equivalent SQL statement: SELECT sum(column_price) FROM product where place_of_production="Zhejiang".
     */
    func sum(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.TermQuery{"place_of_production", "Zhejiang"}).
                SetLimit(0).    // If you want to obtain only the aggregate result, you can set the number of matched results to return to 0 to reduce the response time.
                Aggregation(search.NewSumAggregation("sum_agg_1", "column_price").Missing(0.00)))
    
        searchResponse, err := client.Search(searchRequest)    // Execute the query.
        aggResults := searchResponse.AggregationResults        // Obtain all aggregate results.
        agg1, err := aggResults.Sum("sum_agg_1")        // Obtain the aggregate result of sum_agg_1.
        if err != nil {
            panic(err)
        }
        fmt.Println(agg1.Value)    // Display the aggregate result.
    }
    ```


## Average

The aggregation method used to return the average value of all rows for a numeric field. This method is equivalent to the SQL AVG function.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported for this field.|
    |Missing|The default value for the field used for the aggregation operation on a row when the field value is not specified.

    -   If the Missing parameter is not specified, the row is ignored.
    -   If the Missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    ```
    
    /**
     * The sales volume of each product is listed in the product table. Query the average price of the products produced in Zhejiang.
     * The equivalent SQL statement: SELECT avg(column_price) FROM product where place_of_production = "Zhejiang".
     */
    func avg(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.TermQuery{"place_of_production", "Zhejiang"}).
                SetLimit(0).    // If you want to obtain only the aggregate result, you can set the number of matched results to return to 0 to reduce the response time.
                Aggregation(search.NewAvgAggregation("avg_agg_1", "column_price").Missing(0.00)))
    
        searchResponse, err := client.Search(searchRequest)    // Execute the query.
        aggResults := searchResponse.AggregationResults        // Obtain all aggregate results.
        agg1, err := aggResults.Avg("avg_agg_1")        // Obtain the aggregate result of avg_agg_1.
        if err != nil {
            panic(err)
        }
        if agg1.HasValue() { // Specify whether the aggregate result of agg1 has a value.
            fmt.Println(agg1.Value)    // Display the aggregate result.
        }
    }
    ```


## Count

The aggregation method used to return the total number of values for a field. This method is equivalent to the SQL COUNT function.

**Note:** The current count operation does not support the COUNT \(\*\) function. To count the rows in an index or the matched rows in a query, use the query operation and set the setGetTotalCount parameter to true in the query.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, KEYWORD, and GEOPOINT data types are supported for this field.|

-   Examples

    ```
    
    /**
     * Each type of punishment records of merchants is recorded in the merchant table. Query the number of merchants in Zhejiang who have punishment records. If merchants have no punishment records, the merchants do not have a value for the specified field.
     * The equivalent SQL statement: SELECT count(column_history) FROM product where place_of_production="Zhejiang".
     */
    func count(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.TermQuery{"place_of_production", "Zhejiang"}).
                SetLimit(0).    // If you want to obtain only the aggregate result, you can set the number of matched results to return to 0 to reduce the response time.
                Aggregation(search.NewCountAggregation("count_agg_1", "column_price")))
    
        searchResponse, err := client.Search(searchRequest)    // Execute the query.
        aggResults := searchResponse.AggregationResults        // Obtain all aggregate results.
        agg1, err := aggResults.Count("count_agg_1")        // Obtain the aggregate result of count_agg_1.
        if err != nil {
            panic(err)
        }
        fmt.Println(agg1.Value)    // Display the aggregate result.
    }
    ```


## Distinct count

The aggregation method used to return the number of distinct values for a field. This method is equivalent to the SQL COUNT\(distinct\) function.

**Note:** The number of distinct values is approximate.

-   When the number of values is smaller than 10,000, the calculated result is a number close to an exact value.
-   When the number of values reaches 100 million, the error rate is about 2%.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, KEYWORD, and GEOPOINT data types are supported for this field.|
    |Missing|The default value for the field used for the aggregation operation on a row when the field value is not specified.

    -   If the Missing parameter is not specified, the row is ignored.
    -   If the Missing parameter is set, the value of this parameter is used as the field value of the row. |

-   Examples

    ```
    /**
     * Query the number of distinct provinces from which all products come.
     * The equivalent SQL statement: SELECT count(distinct column_place) FROM product.
     */
    func distinctCount(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.TermQuery{"place_of_production", "Zhejiang"}).
                SetLimit(0).    // If you want to obtain only the aggregate result, you can set the number of matched results to return to 0 to reduce the response time.
                Aggregation(search.NewDistinctCountAggregation("distinct_count_agg_1", "column_price").Missing(0.00)))
    
        searchResponse, err := client.Search(searchRequest)    // Execute the query.
        aggResults := searchResponse.AggregationResults        // Obtain all aggregate results.
        agg1, err := aggResults.DistinctCount("distinct_count_agg_1")        // Obtain the aggregate result of distinct_count_agg_1.
        if err != nil {
            panic(err)
        }
        fmt.Println(agg1.Value)    // Display the aggregate result.
    }
    ```


## Group by field value

The method used to group query results based on field values. The same values are grouped together. The value of each group and the number of corresponding values are returned.

**Note:** The calculated number may be slightly different from the actual number when the number of values in a group is too large.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG, DOUBLE, BOOLEAN, and KEYWORD data types are supported for this field.|
    |Size|The number of returned groups.|
    |GroupBySorters|The sorting rules for items in a group. By default, group items are sorted in descending order. When you set multiple sorting rules, data is sorted based on the order in which the rules are added. Supported parameters:     -   Sort by value in ascending alphabetical order
    -   Sort by value in descending alphabetical order
    -   Sort by count of rows in ascending order
    -   Sort by count of rows in descending order
    -   Sort based on values obtained from the sub-aggregation in ascending order
    -   Sort based on values obtained from the sub-aggregation in descending order |
    |SubAggregation and SubGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.     -   Scenario

Query the number of products in each category and the maximum and minimum product prices in each category.

    -   Method

First group query results by product category, and then add two sub-aggregations to obtain the maximum and minimum product prices in each category.

    -   Result
        -   Fruits: 5. The maximum price is CNY 15. The minimum price is CNY 3.
        -   Toiletries: 10. The maximum price is CNY 98. The minimum price is CNY 1.
        -   Electronic devices: 3. The maximum price is CNY 8,699. The minimum price is CNY 2,300.
        -   Other products: 15. The maximum price is CNY 1,000. The minimum price is CNY 80. |

-   Examples

    ```
    /**
     * Query the number of products and the maximum and minimum product prices in each category.
     * Example of returned results: Fruits: 5. The minimum price is CNY 3, and the maximum price is CNY 15. Toiletries: 10. The minimum price is CNY 1, and the maximum price is CNY 98. Electronic devices: 3. The minimum price is CNY 2,300, and the maximum price is CNY 8,699. Other products: 15. The minimum price is CNY 80,
     and the maximum price is CNY 1,000.
     */
    func GroupByField(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.MatchAllQuery{}).    // Match all rows.
                SetLimit(0).
                GroupBy(search.NewGroupByField("group1", "column_type").    // Group by value for a field in each category.
                    SubAggregation(search.NewMinAggregation("min_price", "column_price")).    // The minimum product price in each category.
                    SubAggregation(search.NewMaxAggregation("max_price", "column_price"))))    // The maximum product price in each category.
    
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        groupByResults := searchResponse.GroupByResults    // Obtain all aggregate results.
        group, err := groupByResults.GroupByField("group1")
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
    
        for _, item := range group.Items {    // Traverse the returned groups.
            // Display the value of each group and the number of rows in each group.
            fmt.Println("\tkey: ", item.Key, ", rowCount: ", item.RowCount) 
    
            // Display the minimum prices.
            minPrice, _ := item.SubAggregations.Min("min_price")
            if minPrice.HasValue() {
                fmt.Println("\t\tmin_price: ", minPrice.Value)
            }
    
            // Display the maximum prices.
            maxPrice, _ := item.SubAggregations.Max("max_price")
            if maxPrice.HasValue() {
                fmt.Println("\t\tmax_price: ", maxPrice.Value)
            }
        }
    }
    ```


## Group by range

The method used to group query results based on value ranges of a field. Field values that fall within a specified range are grouped together. The number of values in each range is returned.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the LONG and DOUBLE data types are supported for this field.|
    |Range\(fromInclusive float64, toExclusive float64\)|The value ranges for grouping. The range can start from NegInf and end with Inf. |
    |SubAggregation and SubGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results. For example, after you group query results by sales volume and then by province, you can obtain which province has the largest proportion in a specified range of sales volume. You must specify GroupByField in GroupByRange to execute this query. |

-   Examples

    ```
    /**
     * Query the sales of each range by grouping [NegInf,1000), [1000,5000), and [5000,Inf) when you calculate the sales.
     */
    func GroupByRange(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.MatchAllQuery{}).    // Match all rows.
                SetLimit(0).
                GroupBy(search.NewGroupByRange("group1", "column_number").
                    Range(search.NegInf, 1000).
                    Range(1000, 5000).
                    Range(5000, search.Inf)))
    
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        groupByResults := searchResponse.GroupByResults    // Obtain all aggregate results.
        group, err := groupByResults.GroupByRange("group1")
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        for _, item := range group.Items {    // Traverse the returned groups.
            fmt.Println("\t[", item.From, ", ", item.To, "), rowCount: ", item.RowCount)    // Display the number of rows in each group.
        }
    }
    ```


## Group by geographical distance

The method used to group query results based on their geographical distances to a central point. Distances that fall within a specified range are grouped together. The number of items in each range is returned.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |FieldName|The name of the field used to perform the aggregation operation. Only the GEOPOINT data type is supported for this field.|
    |CenterPoint\(latitude float64, longitude float64\)|The longitude and latitude of the central point. latitude indicates the latitude of the central point. longitude indicates the longitude of the central point. |
    |Range\(fromInclusive float64, toExclusive float64\)|The ranges for grouping. Unit: m. The range can start from NegInf and end with Inf. |
    |SubAggregation and SubGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.|

-   Examples

    ```
    
    /**
     * Group people based on their geographical distances to a Wanda Plaza to obtain the number of people in each distance range. Group the distances based on ranges [NegInf, 1000), [1000, 5000), [5000, Inf). The unit is meters. Find the number of people within these ranges from Wanda Plaza [NegInf, 1000), [1000, 5000), [5000, Inf). The unit is in meters.
     */
    func GroupByGeoDistance(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.MatchAllQuery{}).    // Match all rows.
                SetLimit(0).
                GroupBy(search.NewGroupByGeoDistance("group1", "Col_GeoPoint", search.GeoPoint{Lat: 30.137817, Lon:120.08681}).
                    Range(search.NegInf, 1000).
                    Range(1000, 5000).
                    Range(5000, search.Inf)))
    
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        groupByResults := searchResponse.GroupByResults    // Obtain all aggregate results.
        group, err := groupByResults.GroupByGeoDistance("group1")
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        for _, item := range group.Items {    // Traverse the returned groups.
            fmt.Println("\t[", item.From, ", ", item.To, "), rowCount: ", item.RowCount)    // Display the number of rows in each group.
        }
    }
    ```


## Group by filter

The method used to filter the query results and group them together to obtain the number of items that match each filter. Results are returned in the order in which the filters are added.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |Name|The unique name specified for the aggregation operation. You can query results of the aggregation operation based on the name.|
    |Query|The filters for the query. Results are returned in the order in which the filters are added.|
    |SubAggregation and SubGroupBy|The sub-aggregation. The sub-aggregation operation is performed based on the grouping results.|

-   Examples

    ```
    /**
     * Add the following three filters to obtain the number of items that match each filter: The sales volume exceeds 100, the production place is Zhejiang, and the description contains Hangzhou.
     */
    func GroupByFilter(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.MatchAllQuery{}).    // Match all rows.
                SetLimit(0).
                GroupBy(search.NewGroupByFilter("group1").
                    Query(&search.RangeQuery{
                        FieldName: "number",
                        From: 100,
                        IncludeLower: true}).
                    Query(&search.TermQuery{
                        FieldName: "place",
                        Term:      "Zhejiang",
                    }).
                    Query(&search.MatchQuery{
                        FieldName: "description",
                        Text: "Hangzhou",
                    })))
    
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        groupByResults := searchResponse.GroupByResults    // Obtain all aggregate results.
        group, err := groupByResults.GroupByFilter("group1")
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        for _, item := range group.Items {    // Traverse the returned groups.
            fmt.Println("\trowCount: ", item.RowCount)    // Display the number of rows in each group.
        }
    }
    ```


## Nesting

GroupBy supports nesting. You can add sub-aggregations and to a GroupBy.

GroupBy can contain endless levels for nesting. However, to ensure the performance and facilitate GroupBy operations, you are allowed only to configure a small number of levels for nesting.

-   GroupBy + SubGroupBy: Group items by province and then by city to obtain data for each city in each province.
-   GroupBy + SubAggregation: Group items by province to obtain the maximum value of a metric for each province.

**Note:** GroupBy can contain endless levels for nesting. However, to ensure the performance and facilitate GroupBy operations, you are allowed only to configure a small number of levels for nesting. For more information, see [Limits](/intl.en-US/Developer Guide/Search Index/Limits.md).

Examples

```
/**
 * Perform nesting-based aggregation.
 * Two aggregations and one GroupByRange are added to the outermost level of GroupByField.
 */
func NestedSample(client *tablestore.TableStoreClient, tableName string, indexName string) {
    searchRequest := &tablestore.SearchRequest{}

    searchRequest.
        SetTableName(tableName).    // Set the name of the table.
        SetIndexName(indexName).    // Set the name of the search index.
        SetSearchQuery(search.NewSearchQuery().
            SetQuery(&search.MatchAllQuery{}).    // Match all rows.
            SetLimit(0).
            GroupBy(search.NewGroupByField("group1", "field1").
                SubAggregation(search.NewMinAggregation("sub_agg1", "sub_field1")).
                SubAggregation(search.NewMaxAggregation("sub_agg2", "sub_field2")).
                SubGroupBy(search.NewGroupByRange("sub_group1", "sub_field3").
                    Range(search.NegInf, 3).
                    Range(3, 5).
                    Range(5, search.Inf))))

    searchResponse, err := client.Search(searchRequest)
    if err != nil {
        fmt.Printf("%#v", err)
        return
    }
    groupByResults := searchResponse.GroupByResults    // Obtain all aggregate results.
    group, err := groupByResults.GroupByField("group1")
    if err != nil {
        fmt.Printf("%#v", err)
        return
    }

    for _, item := range group.Items {    // Traverse the returned groups.
        // Display the value of each group and the number of rows in each group.
        fmt.Println("\tkey: ", item.Key, ", rowCount: ", item.RowCount) 

        // Obtain the aggregate results of sub_agg1.
        subAgg1, _ := item.SubAggregations.Min("sub_agg1")
        if subAgg1.HasValue() {
            fmt.Println("\t\tsub_agg1: ", subAgg1.Value)
        }

        // Obtain the aggregate results of sub_agg2.
        subAgg2, _ := item.SubAggregations.Max("sub_agg2")
        if subAgg2.HasValue() {
            fmt.Println("\t\tsub_agg2: ", subAgg2.Value)
        }

        // Obtain the aggregate results of sub_group1.
        subGroup, _ := item.SubGroupBys.GroupByRange("sub_group1")
        for _, item := range subGroup.Items {
            fmt.Println("\t\t[", item.From, ", ", item.To, "), rowCount: ", item.RowCount)
        }
    }
}
```

## Multiple aggregations

You can perform multiple aggregation operations.

**Note:** Implementing multiple complex aggregation operations at the same time may increase response time.

-   Example 1

    ```
    func MultipleAggregations(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.MatchAllQuery{}).    // Match all rows.
                SetLimit(0).
                Aggregation(search.NewAvgAggregation("agg1", "Col_Long")).                // Calculate the average value for the Col_Long field.
                Aggregation(search.NewDistinctCountAggregation("agg2", "Col_Long")).    // Calculate the number of distinct values for the Col_Long field.
                Aggregation(search.NewMaxAggregation("agg3", "Col_Long")).                // Calculate the maximum value for the Col_Long field.
                Aggregation(search.NewSumAggregation("agg4", "Col_Long")).                // Calculate the sum for the Col_Long field.
                Aggregation(search.NewCountAggregation("agg5", "Col_Long")))            // Calculate the rows for the Col_Long field.
    
        // Return all columns.
        searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
            ReturnAll: true,
        })
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        aggResults := searchResponse.AggregationResults    // Obtain all aggregate results.
    
        // Obtain the average value obtained from the aggregation operation.
        agg1, err := aggResults.Avg("agg1")        // Obtain the average value for agg1.
        if err != nil {
            panic(err)
        }
        if agg1.HasValue() {                            // Specify whether the aggregate result of agg1 has a value.
            fmt.Println("(avg) agg1: ", agg1.Value)    // Display the average value of the Col_Long field.
        } else {
            fmt.Println("(avg) agg1: no value")        // Display rows that contain no values for the Col_Long field.
        }
    
        // Obtain the distinct count obtained from the aggregation operations.
        agg2, err := aggResults.DistinctCount("agg2")    // Obtain the number of distinct values for agg2.
        if err != nil {
            panic(err)
        }
        fmt.Println("(distinct) agg2: ", agg2.Value)        // Display the number of distinct values for the Col_Long field.
    
        // Display the maximum value obtained from the aggregation operation.
        agg3, err := aggResults.Max("agg3")        // Obtain the maximum value for agg3.
        if err != nil {
            panic(err)
        }
        if agg3.HasValue() {
            fmt.Println("(max) agg3: ", agg3.Value)    // Display the maximum value for the Col_Long field.
        } else {
            fmt.Println("(max) agg3: no value")        // Display rows that contain no values for the Col_Long field.
        }
    
        // Obtain the sum obtained from the aggregation operation.
        agg4, err := aggResults.Sum("agg4")        // Obtain the sum for agg4.
        if err != nil {
            panic(err)
        }
        fmt.Println("(sum) agg4: ", agg4.Value)        // Display the sum for the Col_Long field.
    
        // Obtain the aggregate result of the count.
        agg5, err := aggResults.Count("agg5")        // Obtain the count for agg5.
        if err != nil {
            panic(err)
        }
        fmt.Println("(count) agg6: ", agg5.Value)    // Display the number of values for the Col_Long field.
    }
    ```

-   Example 2

    ```
    func MultipleAggregationsAndGroupBysSample(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
    
        searchRequest.
            SetTableName(tableName).    // Set the name of the table.
            SetIndexName(indexName).    // Set the name of the search index.
            SetSearchQuery(search.NewSearchQuery().
                SetQuery(&search.MatchAllQuery{}).    // Match all rows.
                SetLimit(0).
                Aggregation(search.NewAvgAggregation("agg1", "Col_Long")).                // Calculate the average value for the Col_Long field.
                Aggregation(search.NewDistinctCountAggregation("agg2", "Col_Long")).    // Calculate the number of distinct values for the Col_Long field.
                Aggregation(search.NewMaxAggregation("agg3", "Col_Long")).                // Calculate the maximum value for the Col_Long field.
                GroupBy(search.NewGroupByField("group1", "Col_Keyword").    // Perform the GroupByField operation for the Col_Keyword field.
                    GroupBySorters([]search.GroupBySorter{}).    // Specify the order in which the groups are returned.
                    Size(2).                                // Return only the first two groups.
                    SubAggregation(search.NewAvgAggregation("sub_agg1", "Col_Long")).    // Perform the sub-aggregation operation for each group.
                    SubGroupBy(search.NewGroupByField("sub_group1", "Col_Keyword2"))).    // Perform the sub-aggregation operation for each group.
                GroupBy(search.NewGroupByRange("group2", "Col_Long").        // Perform the GroupByRange operation for the Col_Long field.
                    Range(search.NegInf, 3).            // The first group contains rows whose values in the Col_Long field are within the (NegInf, 3) range.
                    Range(3, 5).            // The second group contains rows whose values in the Col_Long field are within the [3, 5) range.
                    Range(5, search.Inf)))            // The third group contains rows whose values in the Col_Long field are within the [5, Inf) range.
    
        // Return all columns.
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
    
        aggResults := searchResponse.AggregationResults    // Obtain all aggregate results.
        // Obtain the average value obtained from the aggregation operation.
        agg1, err := aggResults.Avg("agg1")        // Obtain the average value for agg1.
        if err != nil {
            panic(err)
        }
        if agg1.HasValue() {                            // Specify whether the aggregate result of agg1 has a value.
            fmt.Println("(avg) agg1: ", agg1.Value)    // Display the average value of the Col_Long field.
        } else {
            fmt.Println("(avg) agg1: no value")        // Display rows that contain no values for the Col_Long field.
        }
    
        // Obtain the distinct count obtained from the aggregation operations.
        agg2, err := aggResults.DistinctCount("agg2")    // Obtain the number of distinct values for agg2.
        if err != nil {
            panic(err)
        }
        fmt.Println("(distinct) agg2: ", agg2.Value)        // Display the number of distinct values for the Col_Long field.
    
        // Display the maximum value obtained from the aggregation operation.
        agg3, err := aggResults.Max("agg3")        // Obtain the maximum value for agg3.
        if err != nil {
            panic(err)
        }
        if agg3.HasValue() {
            fmt.Println("(max) agg3: ", agg3.Value)    // Display the maximum value for the Col_Long field.
        } else {
            fmt.Println("(max) agg3: no value")        // Display rows that contain no values for the Col_Long field.
        }
    
        groupByResults := searchResponse.GroupByResults    // Obtain all aggregate results.
        // Obtain the GroupByField values obtained from the aggregation operation.
        group1, err := groupByResults.GroupByField("group1")    // Obtain the GroupBy result of group1, which is of the GroupByField type.
        if err != nil {
            panic(err)
        }
        fmt.Println("group1: ")
        for _, item := range group1.Items {    // Traverse the returned groups.
            //item
            fmt.Println("\tkey: ", item.Key, ", rowCount: ", item.RowCount)    // Display the number of rows in each group.
    
            // Obtain the sub-aggregation result of the average value.
            subAgg1, err := item.SubAggregations.Avg("sub_agg1")    // Obtain the sub-aggregation result of sub_agg1.
            if err != nil {
                panic(err)
            }
            if subAgg1.HasValue() {    // If sub_agg1 obtains the average value for the Col_Long field, the HasValue() parameter value is true.
                fmt.Println("\t\tsub_agg1: ", subAgg1.Value)    // Display the average value for the Col_Long field obtained by using sub-aggregation.
            }
    
            // Obtain the GroupByField values from the aggregation operation.
            subGroup1, err := item.SubGroupBys.GroupByField("sub_group1")    // Obtain the sub-GroupBy result of sub_group1.
            if err != nil {
                panic(err)
            }
            fmt.Println("\t\tsub_group1")
            for _, subItem := range subGroup1.Items {    // Traverse the GroupBy result of sub_group1.
                fmt.Println("\t\t\tkey: ", subItem.Key, ", rowCount: ", subItem.RowCount)    // Display the number of rows of sub_group1.
                tablestore.Assert(subItem.SubAggregations.Empty(), "")
                tablestore.Assert(subItem.SubGroupBys.Empty(), "")
            }
        }
    
        // Obtain the GroupByRange values from the aggregation operation.
        group2, err := groupByResults.GroupByRange("group2")    // Obtain the GroupBy result of group2, which is of the GroupByRange type.
        if err != nil {
            panic(err)
        }
        fmt.Println("group2: ")
        for _, item := range group2.Items {    // Traverse the returned groups.
            fmt.Println("\t[", item.From, ", ", item.To, "), rowCount: ", item.RowCount)    // Display the number of rows in each group.
        }
    }
    ```


