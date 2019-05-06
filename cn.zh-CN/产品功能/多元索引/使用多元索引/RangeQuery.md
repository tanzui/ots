# RangeQuery {#concept_227247 .concept}

根据范围条件查询表中的数据，对于分词字符串类型\(TEXT\)，只要分词后的词条中有词条满足范围条件即可。

## 参数 {#section_uhu_tq0_jp8 .section}

-   fieldName：字段名
-   from：其实位置的值
-   to：结束位置的值
-   includeLow：Boolean值，结果中是否需要包括from值
-   includeUpper：Boolean值，结果中是否需要包括to值

## 示例 {#section_77x_e1d_65h .section}

``` {#codeblock_uje_5jc_gkv}
/**
 * 查询表中Col_Long这一列大于3的数据，结果按照Col_Long这一列的值逆序排序。
 * @param client
 */
private static void rangeQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    RangeQuery rangeQuery = new RangeQuery(); // 设置查询类型为RangeQuery
    rangeQuery.setFieldName("Col_Long");  // 设置针对哪个字段
    rangeQuery.greaterThan(ColumnValue.fromLong(3));  // 设置该字段的范围条件，大于3
    searchQuery.setQuery(rangeQuery);

    // 设置按照Col_Long这一列逆序排序
    FieldSort fieldSort = new FieldSort("Col_Long");
    fieldSort.setOrder(SortOrder.DESC);
    searchQuery.setSort(new Sort(Arrays.asList((Sort.Sorter)fieldSort)));

    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
    System.out.println("Row: " + resp.getRows());

    /**
     * 设置SearchAfter后重新查询，设置SearchAfter=5，则按照Col_Long逆序顺序后，从Col_Long=5之后的行开始返回（相当于设置条件为小于5）。
     */
    searchQuery.setSearchAfter(new SearchAfter(Arrays.asList(ColumnValue.fromLong(5))));
    searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
    System.out.println("Row: " + resp.getRows());    
}
```

