# BoolQuery {#concept_227249 .concept}

复合查询条件可以包含一个或者多个子查询条件，根据子查询条件是否满足来判断一行数据是否满足查询条件。

可以设置多种组合方式，比如设置多个子查询条件为mustQueries，则要求这些子查询条件必须都满足；设置多个子查询条件为mustNotQueries时，要求这些子查询条件都不能满足。

## 参数 {#section_h2n_qwn_naa .section}

-   mustQuerys：多个Query列表，必须要满足的条件。
-   mustNotQuerys：多个Query列表，必须不能满足的条件。
-   shouldQuerys：可以满足，也可以不满足，如果满足则整体的相关性分数更高。
-   minimumShouldMatch：至少要有多少个should query满足才算命中这行数据。

## 示例 {#section_q29_njy_7a0 .section}

``` {#codeblock_ap3_0f8_7ec}
/**
 * 通过BoolQuery进行复合条件查询。
 * @param client
 */
public static void boolQuery(SyncClient client) {
    /**
     * 查询条件一：RangeQuery，Col_Long这一列的值要大于3
     */
    RangeQuery rangeQuery = new RangeQuery();
    rangeQuery.setFieldName("Col_Long");
    rangeQuery.greaterThan(ColumnValue.fromLong(3));

    /**
     * 查询条件二：MatchQuery，Col_Keyword这一列的值要匹配"hangzhou"
     */
    MatchQuery matchQuery = new MatchQuery(); // 设置查询类型为MatchQuery
    matchQuery.setFieldName("Col_Keyword"); // 设置要匹配的字段
    matchQuery.setText("hangzhou"); // 设置要匹配的值

    SearchQuery searchQuery = new SearchQuery();
    {
        /**
         * 构造一个BoolQuery，设置查询条件是必须同时满足"条件一"和"条件二"
         */
        BoolQuery boolQuery = new BoolQuery();
        boolQuery.setMustQueries(Arrays.asList(rangeQuery, matchQuery));
        searchQuery.setQuery(boolQuery);
        SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
        System.out.println("Row: " + resp.getRows());
    }

    {
        /**
         * 构造一个BoolQuery，设置查询条件是至少满足"条件一"和"条件二"中的一个条件
         */
        BoolQuery boolQuery = new BoolQuery();
        boolQuery.setShouldQueries(Arrays.asList(rangeQuery, matchQuery));
        boolQuery.setMinimumShouldMatch(1); // 设置最少满足一个条件
        searchQuery.setQuery(boolQuery);
        SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
        System.out.println("Row: " + resp.getRows());
    }
}
```

