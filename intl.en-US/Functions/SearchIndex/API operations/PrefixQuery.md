# PrefixQuery {#concept_227245 .concept}

You can use PrefixQuery to query data that matches a specified prefix. When a table contains a TEXT string, Table Store tokenizes the string and matches any of the tokens with the specified prefix.

## Parameters {#section_ku4_75c_el9 .section}

-   fieldName: the name of the target field.
-   prefix: the value of the specified prefix.

## Example {#section_kvs_3e2_hg6 .section}

``` {#codeblock_787_wro_lzd}
/**
 * Search the table for rows where the value of Col_Keyword contains the prefix that exactly matches "hangzhou".
 * @param client
 */
private static void prefixQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    PrefixQuery prefixQuery = new PrefixQuery(); // Set the query type to PrefixQuery.
    prefixQuery.setFieldName("Col_Keyword");
    prefixQuery.setPrefix("hangzhou");
    searchQuery.setQuery(prefixQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
```

