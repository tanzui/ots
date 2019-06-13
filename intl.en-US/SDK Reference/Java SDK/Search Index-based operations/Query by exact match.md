# Query by exact match {#concept_orp_zhx_dgb .concept}

## TermQuery {#section_dc2_dxt_lgb .section}

You can use TermQuery to query data that exactly matches the specified value of a field. When a table contains a TEXT string, Table Store tokenizes the string and exactly matches any of the tokens.

For example, Table Store tokenizes Text string "tablestore is cool" into "tablestore", "is", and "cool". When you specify any of these tokens as a query string, you can retrieve the query result that contains the token.

**Example**

```
/**
 * Search the table for rows where the value of Col_Keyword exactly matches "hangzhou".
 * @param client
 */
private static void termQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    TermQuery termQuery = new TermQuery(); // Set the query type to TermQuery.
    termQuery.setFieldName("Col_Keyword"); // Set the name of the field that you want to match.
    termQuery.setTerm(ColumnValue.fromString("hangzhou")); // Set the value that you want to match.
    searchQuery.setQuery(termQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("Row: " + resp.getRows());
    // You can check whether NextToken is null. If not, you can use NextToken to continue reading data.
}
```

## TermsQuery {#section_tny_fxt_lgb .section}

You can use TermsQuery to query data that exactly matches the specified field values. TermQuery is similar to TermsQuery. The difference is that TermsQuery supports multiple terms. You can retrieve query results that match any of these terms.

```
/**
 *  Search the table for rows where the value of Col_Keyword exactly matches "hangzhou" or "shanghai".
 * TermsQuery supports multiple terms. You can retrieve query results that match any of these terms.
 * @param client
 */
private static void termsQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    TermsQuery termsQuery = new TermsQuery(); // Set the query type to TermQuery.
    termsQuery.setFieldName("Col_Keyword"); // Set the name of the field that you want to match.
    termsQuery.setTerms(Arrays.asList(ColumnValue.fromString("hangzhou"),
            ColumnValue.fromString("shanghai"))); // Set the value that you want to match.
    searchQuery.setQuery(termsQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("Row: " + resp.getRows());
    // You can check whether NextToken is null. If not, you can use NextToken to continue reading data.
}
```

