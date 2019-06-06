# TermsQuery {#concept_227243 .concept}

This query is similar to TermQuery, but supports multiple terms. This query is also similar to the SQL IN operator.

## Parameters {#section_5f5_90k_vy8 .section}

fieldName: the name of the target field.

terms: the target terms. Table Store returns the data in a row when the system matches one term in the row.

## Example {#section_fnl_vsd_wim .section}

``` {#codeblock_9oz_a8x_8rj}
/**
 * Search the table for rows where the value of Col_Keyword exactly matches "hangzhou" or "xi'an".
 * @param client
 */
private static void termQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    TermsQuery termsQuery = new TermsQuery(); // Set the query type to TermsQuery.
    termsQuery.setFieldName("Col_Keyword"); // Set the name of the field that you want to match.
    termsQuery.addTerm(ColumnValue.fromString("hangzhou")); // Set the value that you want to match.
    termsQuery.addTerm(ColumnValue.fromString("xi'an")); // Set the value that you want to match.
    searchQuery.setQuery(termsQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

