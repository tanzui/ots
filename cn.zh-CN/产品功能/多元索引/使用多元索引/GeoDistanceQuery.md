# GeoDistanceQuery {#concept_227251 .concept}

地理距离查询。根据一个中心点和距离条件查询表中的数据，当一个地理位置点到指定的中心点的距离不超过指定的值时，满足查询条件。

## 参数 {#section_lb0_4s6_lf8 .section}

-   fieldName：字段名。
-   centerPoint：中心地理坐标点，是一个经纬度值。
-   distanceInMeter：Double类型，距离中心点的距离，单位是米。

## 示例 {#section_9n1_ki1_dno .section}

``` {#codeblock_1uu_5zl_mgy}
/**
 * 查询表中Col_GeoPoint这一列的值距离中心点不超过一定距离的数据。
 * @param client
 */
public static void geoDistanceQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoDistanceQuery geoDistanceQuery = new GeoDistanceQuery();  // 设置查询类型为GeoDistanceQuery
    geoDistanceQuery.setFieldName("Col_GeoPoint");
    geoDistanceQuery.setCenterPoint("5,5"); // 设置中心点
    geoDistanceQuery.setDistanceInMeter(10000); // 设置到中心点的距离条件，不超过10000米
    searchQuery.setQuery(geoDistanceQuery);

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  //设置返回Col_GeoPoint这一列
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
    System.out.println("Row: " + resp.getRows());
}
```

