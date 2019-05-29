# Query by geographic location {#concept_hty_psc_fgb .concept}

A Searchindex structure supports three geographic location-based query methods: GeoBoundingBoxQuery, GeoDistanceQuery, and GeoPolygonQuery.

## GeoBoundingBoxQuery {#section_lyq_hwc_fgb .section}

You can call this operation to query data that falls within a geographical rectangle that you specify in a query. Table Store returns the rows where the value of an attribute falls within a geographical rectangle.

```
/// <summary>
/// Data in the Geo_type_col column belongs to the GeoPoint type. Specify a geographical rectangle. Set coordinates of its top-left vertex to "10, 0" and coordinates of its bottom-right vertex to "0, 10". Search the Geo_type_col column for data that falls within the geographical rectangle area.
/// </summary>
/// <param name="client"></param>
public static void GeoBoundingBoxQuery(OTSClient client)
{
    SearchQuery searchQuery = new SearchQuery();
    GeoBoundingBoxQuery geoBoundingBoxQuery = new GeoBoundingBoxQuery(); // Set the query type to GeoBoundingBoxQuery.
    geoBoundingBoxQuery.FieldName = Geo_type_col; // Specify the field for indexing.
    geoBoundingBoxQuery.setTopLeft("10,0"); // Specify coordinates for the top-left vertex of the rectangle.
    geoBoundingBoxQuery.setBottomRight("0,10"); // Specify coordinates for the bottom-right vertex of the rectangle.
    searchQuery.Query = geoBoundingBoxQuery;

    SearchRequest searchRequest = new SearchRequest(TableName, IndexName, searchQuery);

    var columnsToGet = new ColumnsToGet();
    columnsToGet.Columns = new List<string> { Geo_type_col }; // Specify the Col_GeoPoint column as the column where Table Store returns query results.
    searchRequest.ColumnsToGet = columnsToGet;

    SearchResponse response = client.Search(searchRequest);
    Console.WriteLine(response.TotalCount);
}
```

## GeoDistanceQuery {#section_kff_jwc_fgb .section}

You can call this operation to query data that falls within a specified distance from a specified central point. Table Store returns the rows where the value of an attribute falls within the distance from the central point.

```
/// <summary> 
/// Search the Geo_type_col column for data that falls within the specified distance from the specified central point.
/// </summary>
/// <param name="client"></param>
public static void GeoDistanceQuery(OTSClient client)
{
    SearchQuery searchQuery = new SearchQuery();
    GeoDistanceQuery geoDistanceQuery = new GeoDistanceQuery(); // Set the query type to GeoDistanceQuery.
    geoDistanceQuery.FieldName = Geo_type_col;
    geoDistanceQuery.CenterPoint = "10,11"; // Specify coordinates for a central point.
    geoDistanceQuery.setDistanceInMeter(10000); // You can specify 10,000 meters as the farthest distance from the central point.
    searchQuery.Query = geoDistanceQuery;

    SearchRequest searchRequest = new SearchRequest(TableName, IndexName, searchQuery);

    ColumnsToGet columnsToGet = new ColumnsToGet();
    columnsToGet.Columns = new List<string>() { Geo_type_col }; // Specify the Col_GeoPoint column as the column where Table Store returns query results.
    searchRequest.ColumnsToGet = columnsToGet;

    SearchResponse response = client.Search(searchRequest);
    Console.WriteLine(response.TotalCount);
}


```

## GeoPolygonQuery {#section_v3g_lwc_fgb .section}

You can call this operation to query data that falls within a polygon area that you specify in a query. Table Store returns the rows where the value of an attribute falls within a polygon area.

```
/// <summary> 
/// Search the Geo_type_col column for data that falls in a specified polygon area.
/// </summary>
/// <param name="client"></param>
public static void GeoPolygonQuery(OTSClient client)
{
    SearchQuery searchQuery = new SearchQuery();
    GeoPolygonQuery geoPolygonQuery = new GeoPolygonQuery(); // Set the query type to GeoPolygonQuery.
    geoPolygonQuery.FieldName = Geo_type_col;
    geoPolygonQuery.Points = new List<string>() { "0,0", "10,0", "10,10" }; // Specify coordinates for vertices of a polygon.
    searchQuery.Query = geoPolygonQuery;

    SearchRequest searchRequest = new SearchRequest(TableName, IndexName, searchQuery);

    ColumnsToGet columnsToGet = new ColumnsToGet();
    columnsToGet.Columns = new List<string>() { Geo_type_col }; // Specify the Col_GeoPoint column as the column where Table Store returns query results.
    searchRequest.ColumnsToGet = columnsToGet;

    SearchResponse response = client.Search(searchRequest);
    Console.WriteLine(response.TotalCount);
}



```

