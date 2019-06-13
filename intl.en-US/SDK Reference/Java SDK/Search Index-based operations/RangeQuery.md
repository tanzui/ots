# RangeQuery {#concept_a23_c3x_dgb .concept}

You can use RangeQuery to query data that falls within a specified range. When a table contains a TEXT string, Table Store tokenizes the string and matches any of the tokens that falls within the specified range.

**Example**

```
/**
 * Search the table for rows where the value of Col_Long is greater than 3. Table Store sorts these rows by Col_Long in descending order.
 * @param client
 */
private static void rangeQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    RangeQuery rangeQuery = new RangeQuery(); // Set the query type to RangeQuery.
    rangeQuery.setFieldName("Col_Long");  // Set the name of the field that you want to match.
    rangeQuery.greaterThan(ColumnValue.fromLong(3));  // Specify the range of the value of the field. The required value is larger than 3.
    searchQuery.setGetTotalCount(true);
    searchQuery.setQuery(rangeQuery);

    // Sort the result by Col_Long in descending order.
    FieldSort fieldSort = new FieldSort("Col_Long");
    fieldSort.setOrder(SortOrder.DESC);
    searchQuery.setSort(new Sort(Arrays.asList((Sort.Sorter)fieldSort)));

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

