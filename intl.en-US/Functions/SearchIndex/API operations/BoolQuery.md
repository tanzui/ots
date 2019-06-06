# BoolQuery {#concept_227249 .concept}

You can use BoolQuery to query data based on a combination of filtering conditions. This query contains one or more subqueries as filtering conditions. Table Store returns the rows that match the subqueries.

You can combine these subqueries in different ways. If you specify these subqueries as mustQueries, Table Store returns the result that matches all these subqueries. If you specify these subqueries as mustNotQueries, Table Store returns the result that matches none of these subqueries.

## Parameters {#section_h2n_qwn_naa .section}

-   mustQueries: specifies the subqueries that the query result must match. This is a list of multiple fields of Query type.
-   mustNotQueries: specifies the subqueries that the query result must not match. This is a list of multiple fields of Query type.
-   shouldQueries: specifies the subqueries that the query result can or cannot match. If the query result match the subqueries, the overall relevance score is higher.
-   minimumShouldMatch: specifies the minimum number of shouldQueries subqueries that the query result must match.

## Example {#section_q29_njy_7a0 .section}

``` {#codeblock_ap3_0f8_7ec}
/**
 * Use BoolQuery to query data that matches a combination of conditions.
 * @param client
 */
public static void boolQuery(SyncClient client) {
    /**
     * Condition 1: use RangeQuery to query data where the value of Col_Long is greater than 3.
     */
    RangeQuery rangeQuery = new RangeQuery();
    rangeQuery.setFieldName("Col_Long");
    rangeQuery.greaterThan(ColumnValue.fromLong(3));

    /**
     * Condition 2: use MatchQuery to query data where the value of Col_Keyword matches "hangzhou".
     */
    MatchQuery matchQuery = new MatchQuery(); // Set the query type to MatchQuery.
    matchQuery.setFieldName("Col_Keyword"); // Set the name of the field that you want to match.
    matchQuery.setText("hangzhou"); // Set the value that you want to match.

    SearchQuery searchQuery = new SearchQuery();
    {
        /**
         * Create a query of BoolQuery type where the result meets Conditions 1 and 2 at the same time.
         */
        BoolQuery boolQuery = new BoolQuery();
        boolQuery.setMustQueries(Arrays.asList(rangeQuery, matchQuery));
        searchQuery.setQuery(boolQuery);
        SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
        System.out.println("Row: " + resp.getRows());
    }

    {
        /**
         * Create a query of BoolQuery type where the result meets at least one of Conditions 1 and 2.
         */
        BoolQuery boolQuery = new BoolQuery();
        boolQuery.setShouldQueries(Arrays.asList(rangeQuery, matchQuery));
        boolQuery.setMinimumShouldMatch(1); // Specify that the result meets at least one of the conditions.
        searchQuery.setQuery(boolQuery);
        SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
        System.out.println("Row: " + resp.getRows());
    }
}
```

