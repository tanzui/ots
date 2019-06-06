# MatchPhraseQuery {#concept_227005 .concept}

This query is similar to MatchQuery, but evaluates the positional relationship between multiple tokens. Table Store exactly matches the order and position of these tokens in the target row.

For example, the field value is "Hangzhou West Lake Scenic Area". If you specify the target term as "Hangzhou Scenic Area" in Query, Table Store returns the row that contains this target term when you use MatchQuery. However, when you use MatchPhraseQuery, Table Store does not return the row that contains this target term. The distance between "Hangzhou" and "Scenic Area" in Query is 0. But the distance in the field is 2, because the two words "West" and "Lake" exist between "Hangzhou" and "Scenic Area".

## Parameters {#section_ya5_o51_z0b .section}

-   fieldName: the name of the target field.
-   text: the target term. Table Store tokenizes this term into multiple terms before the query.

## Example {#section_46b_i86_41d .section}

``` {#codeblock_qz3_hwt_t3f}
/**
 * Search the table for rows where the value of Col_Text matches "hangzhou shanghai." Table Store returns the total number of rows that match the phrase as a whole and matched rows in this query.
 * @param client
 */
private static void matchPhraseQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    MatchPhraseQuery matchPhraseQuery = new MatchPhraseQuery(); // Set the query type to MatchPhraseQuery.
    matchPhraseQuery.setFieldName("Col_Text"); // Set the field that you want to match.
    matchPhraseQuery.setText("hangzhou shanghai"); // Set the value that you want to match.
    searchQuery.setQuery(matchPhraseQuery);
    searchQuery.setOffset(0); // Set Offset to 0.
    searchQuery.setLimit(20); // Set Limit to 20 to return 20 rows or fewer.
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount());
    System.out.println("Row: " + resp.getRows()); // Return primary keys only by default.

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

