# MatchPhraseQuery {#concept_227005 .concept}

与MatchQuery类似，但是分词后的多个Term的位置关系会被考虑，分词后的多个Term必须在行数据中以同样的顺序和位置存在才算命中。

比如字段值是：“杭州西湖风景区”，Query中是：“杭州风景区”，如果是MatchQuery，则可以命中该行数据，但是如果是MatchPhraseQuery，则不能命中该行数据，因为“杭州”和“风景区”在Query中的距离是0，但是在行数据中的距离是2（西湖两个字导致间隔距离是2）。

## 参数 {#section_ya5_o51_z0b .section}

-   fieldName：字段名。
-   text：查询词，会被分词器分词多个Term，然后再去查询。

## 示例 {#section_46b_i86_41d .section}

``` {#codeblock_qz3_hwt_t3f}
/**
 * 查询表中Col_Text这一列的值能够匹配"hangzhou shanghai"的数据，匹配条件为短语匹配(要求短语完整的按照顺序匹配)，返回匹配到的总行数和一些匹配成功的行。
 * @param client
 */
private static void matchPhraseQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    MatchPhraseQuery matchPhraseQuery = new MatchPhraseQuery(); // 设置查询类型为MatchPhraseQuery
    matchPhraseQuery.setFieldName("Col_Text"); // 设置要匹配的字段
    matchPhraseQuery.setText("hangzhou shanghai"); // 设置要匹配的值
    searchQuery.setQuery(matchPhraseQuery);
    searchQuery.setOffset(0); // 设置offset为0
    searchQuery.setLimit(20); // 设置limit为20，表示最多返回20行数据
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount());
    System.out.println("Row: " + resp.getRows()); // 默认只返回主键

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // 设置返回所有列
    searchRequest.setColumnsToGet(columnsToGet);

    resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
    System.out.println("Row: " + resp.getRows());
}
```

