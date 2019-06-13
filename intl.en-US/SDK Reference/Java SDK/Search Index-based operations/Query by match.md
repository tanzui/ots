# Query by match {#concept_qfq_13x_dgb .concept}

## MatchAllQuery {#section_ay3_bjx_dgb .section}

You can use MatchAllQuery to query the total number of rows or any number of rows in a table.

```
/**
 * Use MatchAllQuery to query the total number of rows in a table.
 * @param client
 */
private static void matchAllQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setQuery(new MatchAllQuery()); // Set the query type to MatchAllQuery.
    /**
     * In the MatchAllQuery-based query result, the value of TotalCount is the total number of rows in a table.
     *To return only the total number of rows without any specific data, you can set Limit to 0. Then, Table Store returns no data in the rows.
     */
    searchQuery.setLimit(0);
    searchQuery.setGetTotalCount(true);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    SearchResponse resp = client.search(searchRequest);
    /**
     * Check whether Table Store returns matched data from all partitions. When the value of isAllSuccess is false, Table Store may fail to query some partitions and return a part of data.
     */
    if (! resp.isAllSuccess()) {
        System.out.println("NotAllSuccess!") ;
    }
    System.out.println("IsAllSuccess: " + resp.isAllSuccess());
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of rows.
    System.out.println(resp.getRequestId());
}
```

## MatchQuery {#section_w5j_3jx_dgb .section}

You can use MatchQuery to query a table based on approximate matches. For example, you can search "this is". Table Store returns query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is".

```
/**
 * Search the table for rows where the value of Col_Keyword matches "hangzhou". Table Store returns matched rows and the total number of matched rows.
 * @param client
 */
private static void matchQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    MatchQuery matchQuery = new MatchQuery(); // Set the query type to MatchQuery.
    matchQuery.setFieldName("Col_Keyword"); // Set the name of the field that you want to match.
    matchQuery.setText("hangzhou"); // Set the value that you want to match.
    searchQuery.setQuery(matchQuery);
    searchQuery.setOffset(0); // Set Offset to 0.
    searchQuery.setLimit(20); // Set Limit to 20 to return a maximum of 20 rows.
    searchQuery.setGetTotalCount(true);
    searchQuery.setSort(new Sort(Arrays.asList(new ScoreSort()))); // Sort the result by match score.
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount());
    System.out.println("Row: " + resp.getRows()); // If you do not set columnsToGet, Table Store only returns primary keys by default.

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

## MatchPhraseQuery {#section_zcx_ljx_dgb .section}

You can use MatchPhraseQuery to search matched phrases. MatchPhraseQuery is similar to MatchQuery. The difference is that MatchPhraseQuery matches a phrase as a whole. For example, if you search the phrase "this is", you can obtain query results such as "..., this is tablestore" and "this is a table". You cannot obtain query results such as "this table is ..." and "is this a table".

```
/**
 * Search the table for rows where the value of Col_Text matches "hangzhou shanghai". Table Store returns the total number of rows that match the phrase as a whole and matched rows in this query.
 * @param client
 */
private static void matchPhraseQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    MatchPhraseQuery matchPhraseQuery = new MatchPhraseQuery(); // Set the query type to MatchPhraseQuery.
    matchPhraseQuery.setFieldName("Col_Text"); // Set the name of the field that you want to match.
    matchPhraseQuery.setText("hangzhou shanghai"); // Set the value that you want to match.
    searchQuery.setQuery(matchPhraseQuery);
    searchQuery.setOffset(0); // Set Offset to 0.
    searchQuery.setLimit(20); // Set Limit to 20 to return up to 20 rows.
    searchQuery.setGetTotalCount(true);
    searchQuery.setSort(new Sort(Arrays.asList(new ScoreSort()))); // Sort the result by match score.
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

