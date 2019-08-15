# ExistQuery {#concept_995063 .concept}

ExistQuery is also called a null query. It is usually used in queries for sparse data to determine whether a column of a row has a value. For example, ExistQuery is used to query the rows in which the value of the address column is not null.

**Note:** If you want to query whether a column contains null values, you must use ExistQuery and the bool query with the must\_not clause.

## Parameter {#section_gyz_gdb_ze3 .section}

fieldName: the column name

## Examples {#section_nam_xtd_s4e .section}

``` {#codeblock_ytr_ijy_wbt}
/**
 * Use ExistQuery to query the rows in which the value of the address column is not null.
 * @param client
 */
private static void termsQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    ExistsQuery existQuery = new ExistsQuery(); // Set the query type to ExistsQuery.
    existQuery.setFieldName("address");
    searchQuery.setQuery(termsQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);

    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

