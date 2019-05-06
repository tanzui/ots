# MatchAllQuery {#concept_227002 .concept}

MatchAllQuery可以匹配所有行，常用于查询表中数据总行数，或者随机返回几条数据。

## 示例 {#section_e20_ryx_lna .section}

``` {#codeblock_0fc_xlu_i97}
/**
 * 通过MatchAllQuery查询表中数据的总行数
 * @param client
 */
private static void matchAllQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();

    /**
    * 设置查询类型为MatchAllQuery
    */
    searchQuery.setQuery(new MatchAllQuery()); 

    /**
     * MatchAllQuery结果中的TotalCount可以表示表中数据的总行数(数据量很大时为估计值)，
     * 如果只为了取行数，但不需要具体数据，可以设置limit=0，即不返回任意一行数据。
     */
    searchQuery.setLimit(0);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    /**
    * 设置返回命中的总行数。
    */ 
    searchRequest.setGetTotalCount(true);  
    SearchResponse resp = client.search(searchRequest);
    /**
     * 判断返回的结果是否是完整的，当isAllSuccess为false时，代表可能有部分节点查询失败，返回的是部分数据
     */
    if (!resp.isAllSuccess()) {
        System.out.println("NotAllSuccess!");
    }
    System.out.println("IsAllSuccess: " + resp.isAllSuccess());
    System.out.println("TotalCount: " + resp.getTotalCount()); // 总行数
    System.out.println(resp.getRequestId());
}
			
```

