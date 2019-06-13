# Geographic location-based query {#concept_ibw_d3x_dgb .concept}

Search Index-based queries support three geographic location-based queries: GeoBoundingBoxQuery, GeoDistanceQuery, and GeoPolygonQuery.

## GeoBoundingBoxQuery {#section_ath_hkx_dgb .section}

You can use GeoBoundingBoxQuery to query data that falls within a geographic rectangular area.

You can specify the geographic rectangular area as a filtering condition in the query. Table Store returns the rows where the value of a field falls within the geographic rectangular area.

```
/**
 * The data type of Col_GeoPoint is GeoPoint. You can obtain the rows where the value of Col_GeoPoint falls within the range of a geographic rectangular area. For the geographic rectangular area, the upper-left vertex is "10,0" and the lower-right vertex is "0,10".
 * @param client
 */
public static void geoBoundingBoxQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoBoundingBoxQuery geoBoundingBoxQuery = new GeoBoundingBoxQuery(); // Set the query type to GeoBoundingBoxQuery.
    geoBoundingBoxQuery.setFieldName("Col_GeoPoint"); // Set the name of the field that you want to match.
    geoBoundingBoxQuery.setTopLeft("10,0"); // Specify coordinates for the upper-left vertex of the geographic rectangular area.
    geoBoundingBoxQuery.setBottomRight("0,10"); // Specify coordinates for the lower-right vertex of the geographic rectangular area.
    searchQuery.setQuery(geoBoundingBoxQuery);
    searchQuery.setGetTotalCount(true);

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //Specify Col_GeoPoint as the column that you want to return.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

## GeoDistanceQuery {#section_ttq_3kx_dgb .section}

You can use GeoDistanceQuery to query data that falls within a distance from a central point. You can specify the central point and the distance from this central point in the query. Table Store returns the rows where the value of a field falls within the distance from the central point.

```
/**
 * Search the table for rows where the value of Col_GeoPoint falls within a specified distance from a specified central point.
 * @param client
 */
public static void geoDistanceQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoDistanceQuery geoDistanceQuery = new GeoDistanceQuery();  // Set the query type to GeoDistanceQuery.
    geoDistanceQuery.setFieldName("Col_GeoPoint");
    geoDistanceQuery.setCenterPoint("5,5"); // Specify coordinates for a central point.
    geoDistanceQuery.setDistanceInMeter(10000); // You can specify 10,000 meters or less as the distance from the central point.
    searchQuery.setQuery(geoDistanceQuery);
    searchQuery.setGetTotalCount(true);

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //Specify Col_GeoPoint as the column that you want to return.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

## GeoPolygonQuery {#section_rms_3kx_dgb .section}

You can use GeoPolygonQuery to query data that falls within a geographic polygon area. You can specify the geographic polygon area as a filtering condition in the query. Table Store returns the rows where the value of a field falls within the geographic polygon area.

```

/**
 * Search the table for rows where the value of Col_GeoPoint falls within a specified geographic polygon area.
 * @param client
 */
public static void geoPolygonQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoPolygonQuery geoPolygonQuery = new GeoPolygonQuery();  // Set the query type to GeoPolygonQuery.
    geoPolygonQuery.setFieldName("Col_GeoPoint");
    geoPolygonQuery.setPoints(Arrays.asList("0,0","5,5","5,0")); // Specify coordinates for vertices of a geographic polygon.
    searchQuery.setQuery(geoPolygonQuery);
    searchQuery.setGetTotalCount(true);
    
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //Specify Col_GeoPoint as the column that you want to return.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

