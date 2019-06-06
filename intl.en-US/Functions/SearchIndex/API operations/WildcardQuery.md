# WildcardQuery {#concept_227248 .concept}

You can use WildcardQuery to query data that matches wildcard characters. You can specify a value you want to match as a string that consists of one or more wildcard characters. An asterisk \(\*\) is interpreted as a number of characters or an empty string. A question mark \(?\) is interpreted as any single character. For example, when you search the string "table\*e", you can retrieve query results such as "tablestore".

## Parameters {#section_82a_s75_kjx .section}

-   fieldName: the name of the target field.
-   value: the value that contains one or more wildcard characters. Table Store supports two types of wildcard characters: asterisk \(\*\) and question mark \(?\). The value cannot start with an asterisk \(\*\) and the length of the value can be 10 bytes or less.

## Example {#section_kr4_qaz_6od .section}

``` {#codeblock_t0i_gmf_pn2}
/**
 * Search the table for rows where the value of Col_Keyword matches "hang*u".
 * @param client
 */
private static void wildcardQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    WildcardQuery wildcardQuery = new WildcardQuery(); // Set the query type to WildcardQuery.
    wildcardQuery.setFieldName("Col_Keyword");
    wildcardQuery.setValue("hang*u"); //Specify a string that contains one or more wildcard characters in wildcardQuery.
    searchQuery.setQuery(wildcardQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set columnsToGet to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);

    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

