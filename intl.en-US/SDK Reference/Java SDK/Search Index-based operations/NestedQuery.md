# NestedQuery {#concept_z2f_l3x_dgb .concept}

You can use NestedQuery to query data based on Nested fields. You cannot use Nested fields directly to query data. Subqueries must be nested in NestedQuery. In this query, you need to specify a subquery that can be any type of query and the path of a Nested field.

**Example**

```

/**
 * The NESTED column contains nested_1 and nested_2. You need to search the col_nested.nested_1 column for data that matches "tablestore".
 * @param client
 */
private static void nestedQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    NestedQuery nestedQuery = new NestedQuery(); // Set the query type to NestedQuery.
    nestedQuery.setPath("col_nested"); // Set the path of the NESTED field.
    TermQuery termQuery = new TermQuery(); // Specify a subquery for NestedQuery.
    termQuery.setFieldName("col_nested.nested_1"); // Set the name of the field that you want to match. The field name must contain the prefix of the Nested column.
    termQuery.setTerm(ColumnValue.fromString("tablestore")); // Set the value that you want to match.
    nestedQuery.setQuery(termQuery);
    nestedQuery.setScoreMode(ScoreMode.None);
    searchQuery.setQuery(nestedQuery);
    searchQuery.setGetTotalCount(true);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

