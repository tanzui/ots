# GeoPolygonQuery {#concept_227252 .concept}

地理多边形查询。根据一个多边形范围条件查询表中的数据，当一个地理位置点落在指定的多边形内时，满足查询条件。

## 参数 {#section_lkz_z2a_qta .section}

-   fieldName：字段名。
-   points：组成多边形的距离坐标点。

## 示例 {#section_39f_4r2_kif .section}

``` {#codeblock_168_4uu_xu3}
/**
 * 查询表中Col_GeoPoint这一列的值在一个给定多边形范围内的数据。
 * @param client
 */
public static void geoPolygonQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoPolygonQuery geoPolygonQuery = new GeoPolygonQuery();  // 设置查询类型为GeoPolygonQuery
    geoPolygonQuery.setFieldName("Col_GeoPoint");
    geoPolygonQuery.setPoints(Arrays.asList("0,0","5,5","5,0")); // 设置多边形的顶点
    searchQuery.setQuery(geoPolygonQuery);

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //设置返回Col_GeoPoint这一列
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
    System.out.println("Row: " + resp.getRows());
}
```

