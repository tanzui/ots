# TermQuery {#concept_227006 .concept}

TermQuery采用完整精确匹配的方式查询表中的数据，类似于字符串匹配，但是对于分词字符串类型，只要分词后有词条可以精确匹配即可。比如某个分词字符串类型的字段，值为“tablestore is cool”，假设分词后为“tablestore”、“is”、“cool”三个词条，则查询“tablestore”、“is”、“cool”时都满足查询条件。

## 参数 {#section_xma_sn2_pre .section}

-   fieldName：字段名。
-   term：查询词，该词不会被分词，会被当做完整次去匹配。

## 示例 {#section_qha_b3w_tel .section}

``` {#codeblock_me9_h8c_asb}
/**
 * 查询表中Col_Keyword这一列精确匹配"hangzhou"的数据。
 * @param client
 */
private static void termQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    TermQuery termQuery = new TermQuery(); // 设置查询类型为TermQuery
    termQuery.setFieldName("Col_Keyword"); // 设置要匹配的字段
    termQuery.setTerm(ColumnValue.fromString("hangzhou")); // 设置要匹配的值
    searchQuery.setQuery(termQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // 设置返回所有列
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    System.out.println("TotalCount: " + resp.getTotalCount()); // 匹配到的总行数，非返回行数
    System.out.println("Row: " + resp.getRows());
}
			
```

