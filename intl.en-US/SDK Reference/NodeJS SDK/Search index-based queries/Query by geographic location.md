# Query by geographic location {#concept_r1n_qxk_2gb .concept}

Search index-based queries support three geographic location-based query methods: GeoBoundingBoxQuery, GeoDistanceQuery, and GeoPolygonQuery.

## GeoBoundingBoxQuery {#section_nfh_byk_2gb .section}

You can use GeoBoundingBoxQuery to query data that falls within a rectangular geographical area that you specify in a query. You can specify a rectangular geographical area as a filtering condition. Table Store returns the rows where the value of a field falls within the range of the rectangular geographical area.

**Examples**

```
/**
 * The data type of Col_GeoPoint is GeoPoint. You can obtain the rows that meet the following conditions:
 * The value of Col_GeoPoint falls within the range of a rectangular geographical area whose coordinates of the top-left vertex are "10,0" and coordinates of the lower-right vertex are "0,10."
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to GeoBoundingBoxQuery.
            queryType: TableStore.QueryType.GEO_BOUNDING_BOX_QUERY,
            query: {
                fieldName: "Col_GeoPoint", // Set the field you want to match.
                topLeft: "10,0", // Specify the coordinates of the top-left vertex.
                bottomRight: "0,10" // Specify the coordinates of the lower-right vertex.
            }
        },
        getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the filtering conditions is returned. If TotalCount is set to false, the total number of rows that meet the filtering conditions is not returned.
    },
    columnToGet: { //Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
        returnType: TableStore.ColumnReturnType.RETURN_ALL
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

## GeoDistanceQuery {#section_mff_fyk_2gb .section}

You can use GeoDistanceQuery to query data that falls within a specified distance from a specified central point. You can specify a central point and a distance from this central point in a query. Table Store returns the rows where the value of a field falls within the distance from the central point.

**Examples**

```
/**
 * Search the table for rows where the value of Col_GeoPoint falls within a specified distance from a specified central point.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to GeoDistanceQuery.
            queryType: TableStore.QueryType.GEO_DISTANCE_QUERY,
            query: {
                fieldName: "Col_GeoPoint",
                centerPoint: "1,1", // Set the coordinates of a central point.
                distance: 10000 // You can specify 10,000 as a distance from the central point.
            }
        },
        getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the filtering conditions is returned. If TotalCount is set to false, the total number of rows that meet the filtering conditions is not returned.
    },
    columnToGet: { //Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
        returnType: TableStore.ColumnReturnType.RETURN_ALL
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

## GeoPolygonQuery {#section_w35_kyk_2gb .section}

You can use GeoPolygonQuery to query data that falls within a polygon area that you specify in a query. You can specify a range of a polygon as a filtering condition. Table Store returns the rows where the value of a field falls within the polygon range.

**Examples**

```
/**
 * Search the table for data where the value of Col_GeoPoint falls within the range of the specified polygon.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to GeoPolygonQuery.
            queryType: TableStore.QueryType.GEO_POLYGON_QUERY,
            query: {
                fieldName: "Col_GeoPoint",
                points: ["0,0","5,5","5,0"] // Specify the coordinates of vertices of a polygon.
            }
        },
        getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the filtering conditions is returned. If TotalCount is set to false, the total number of rows that meet the filtering conditions is not returned.
    },
    columnToGet: { //Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
        returnType: TableStore.ColumnReturnType.RETURN_ALL
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

