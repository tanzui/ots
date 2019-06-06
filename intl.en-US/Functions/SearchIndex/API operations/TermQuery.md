# TermQuery {#concept_227006 .concept}

You can use TermQuery to query data that exactly matches the specified value of a field. When a table contains a Text string, Table Store tokenizes the string and exactly matches any of the tokens. For example, Table Store tokenizes Text string "tablestore is cool" into "tablestore," "is," and "cool". When you specify any of these tokens as a query string, you can retrieve the query result that contains the token.

## Parameters {#section_xma_sn2_pre .section}

-   fieldName: the name of the target field.
-   term: the target term. Table Store does not tokenize this term, but exactly matches the whole term.

## Example {#section_qha_b3w_tel .section}

``` {#codeblock_me9_h8c_asb}
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
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
			
```

