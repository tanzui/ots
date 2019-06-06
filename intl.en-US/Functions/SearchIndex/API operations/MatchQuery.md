# MatchQuery {#concept_227004 .concept}

You can use MatchQuery to query data in the fields of Text type in full-text search scenarios. Table Store tokenizes the value of Text type in the index and the target value that you specify for the MatchQuery type based on your configuration. Therefore, Table Store can match tokenized terms in a query.

For example, the title field value in a row is "Hangzhou West Lake Scenic Area". Table Store tokenizes the value into "Hangzhou", "West", "Lake", "Scenic", and "Area". If you specify the target term as "Lake Scenic" in MatchQuery, Table Store returns this row in the query result.

## Parameters {#section_ld8_6rj_kwk .section}

-   fieldName: the name of the target field.
-   text: the target term. Table Store tokenizes this term into multiple terms.
-   minimumShouldMatch: the minimum number of terms that the value of the fieldName field in a row contains when Table Store returns this row in the query result.
-   operator: the operator used in a logical operation. The default operator OR specifies that Table Store returns the row when some of the tokens of the field value in the row match the target term. The operator AND specifies that Table Store returns the row only when all tokens of the field value in the row match the target term.

## Example {#section_5un_5ax_4ua .section}

``` {#codeblock_j31_jcz_buy}
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
    searchQuery.setLimit(20); // Set Limit to 20 to return 20 rows or fewer.
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

