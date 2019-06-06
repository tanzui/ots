# GeoDistanceQuery {#concept_227251 .concept}

You can use GeoDistanceQuery to query data that falls within a distance from a central point. You can specify the central point and the distance from this central point in the query. Table Store returns the rows where the value of a field falls within the distance from the central point.

## Parameters {#section_lb0_4s6_lf8 .section}

-   fieldName: the name of the target field.
-   centerPoint: the central coordinate point that consists of latitude and longitude values.
-   distanceInMeter: the distance from the central point. This is a value of Double type. Unit: meters.

## Example {#section_9n1_ki1_dno .section}

``` {#codeblock_1uu_5zl_mgy}
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

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //Specify Col_GeoPoint as the column that you want to return.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

