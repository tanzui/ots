# GeoBoundingBoxQuery {#concept_227250 .concept}

You can use GeoBoundingBoxQuery to query data that falls within a geographic rectangular area. You can specify the geographic rectangular area as a filtering condition in the query. Table Store returns the rows where the value of a field falls within the geographic rectangular area.

## Parameters {#section_4rk_t7j_sw3 .section}

-   fieldName: the name of the target field.
-   topLeft: coordinates of the upper-left corner of the geographic rectangular area.
-   bottomRight: coordinates in the lower-right corner of the geographic rectangular area. You can use the upper-left corner and lower-right corner to determine a unique geographic rectangular area.

## Example {#section_plw_3er_8q5 .section}

``` {#codeblock_7bc_7gm_ocb}
/**
 * The data type of Col_GeoPoint is Geopoint. You can obtain the rows where the value of Col_GeoPoint falls within a geographic rectangular area. For the geographic rectangular area, the upper-left vertex is "10,0" and the lower-right vertex is "0,10".
 * @param client
 */
public static void geoBoundingBoxQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoBoundingBoxQuery geoBoundingBoxQuery = new GeoBoundingBoxQuery(); // Set the query type to GeoBoundingBoxQuery.
    geoBoundingBoxQuery.setFieldName("Col_GeoPoint"); // Set the name of the field that you want to match.
    geoBoundingBoxQuery.setTopLeft("10,0"); // Specify coordinates for the upper-left vertex of the geographic rectangular area.
    geoBoundingBoxQuery.setBottomRight("0,10"); // Specify coordinates for the lower-right vertex of the geographic rectangular area.
    searchQuery.setQuery(geoBoundingBoxQuery);

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //Specify Col_GeoPoint as the column that you want to return.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

