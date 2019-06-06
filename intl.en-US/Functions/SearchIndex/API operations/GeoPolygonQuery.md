# GeoPolygonQuery {#concept_227252 .concept}

You can use GeoPolygonQuery to query data that falls within a geographic polygon area. You can specify the geographic polygon area as a filtering condition in the query. Table Store returns the rows where the value of a field falls within the geographic polygon area.

## Parameters {#section_lkz_z2a_qta .section}

-   fieldName: the name of the target field.
-   points: the coordinate points that compose the geographic polygon.

## Example {#section_39f_4r2_kif .section}

``` {#codeblock_168_4uu_xu3}
/**
 * Search the table for rows where the value of Col_GeoPoint falls within a specified geographic polygon area.
 * @param client
 */
public static void geoPolygonQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoPolygonQuery geoPolygonQuery = new GeoPolygonQuery();  // Set the query type to GeoPolygonQuery.
    geoPolygonQuery.setFieldName("Col_GeoPoint");
    geoPolygonQuery.setPoints(Arrays.asList("0,0","5,5","5,0")); // Specify coordinates for vertices of the geographic polygon.
    searchQuery.setQuery(geoPolygonQuery);

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //Specify Col_GeoPoint as the column that you want to return.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

