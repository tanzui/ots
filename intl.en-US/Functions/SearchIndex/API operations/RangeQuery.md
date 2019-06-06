# RangeQuery {#concept_227247 .concept}

You can use RangeQuery to query data that falls within a specified range. When a table contains a TEXT string, Table Store tokenizes the string and matches any of the tokens that falls within the specified range.

## Parameters {#section_uhu_tq0_jp8 .section}

-   fieldName: the name of the target field.
-   from: the value of the start position.
-   to: the value of the end position.
-   includeLow: specifies whether the query result includes the value of the from parameter. This is a parameter of Boolean type.
-   includeUpper: specifies whether the query result includes the value of the to parameter. This is a parameter of Boolean type.

## Example {#section_77x_e1d_65h .section}

``` {#codeblock_uje_5jc_gkv}
/**
 * Search the table for rows where the value of Col_Long is greater than 3. Table Store sorts these rows by Col_Long in descending order.
 * @param client
 */
private static void rangeQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    RangeQuery rangeQuery = new RangeQuery(); // Set the query type to RangeQuery.
    rangeQuery.setFieldName("Col_Long");  // Set the name of the target field.
    rangeQuery.greaterThan(ColumnValue.fromLong(3));  // Specify the range of the value of the field. The required value is larger than 3.
    searchQuery.setQuery(rangeQuery);

    // Sort the result by Col_Long in descending order.
    FieldSort fieldSort = new FieldSort("Col_Long");
    fieldSort.setOrder(SortOrder.DESC);
    searchQuery.setSort(new Sort(Arrays.asList((Sort.Sorter)fieldSort)));

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());

    /**
     * You can specify a value for SearchAfter to start a new query. For example, you can set SearchAfter to 5 and sort the result by Col_Long in descending order. Then, you retrieve the rows that follow the row whose Col_Long is equal to 5. This is similar to the method where you specify that the value of Col_Long is smaller than 5.
     */
    searchQuery.setSearchAfter(new SearchAfter(Arrays.asList(ColumnValue.fromLong(5))));
    searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());    
}
```

