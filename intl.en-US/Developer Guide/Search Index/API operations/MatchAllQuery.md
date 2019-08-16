# MatchAllQuery {#concept_227002 .concept}

You can use MatchAllQuery to query the total number of rows or any number of rows in a table.

## Example {#section_e20_ryx_lna .section}

``` {#codeblock_0fc_xlu_i97}
/**
 * Use MatchAllQuery to query the total number of rows in a table.
 * @param client
 */
private static void matchAllQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();

    /**
    * Set the query type to MatchAllQuery.
    */
    searchQuery.setQuery(new MatchAllQuery()); 

    /**
     * In the MatchAllQuery-based query result, the value of TotalCount is the total number of rows in a table. This value is an approximate value when you query a table that contains a large number of rows.
     *To return only the total number of rows without any specific data, you can set Limit to 0. Then, Table Store returns no data in the rows.
     */
    searchQuery.setLimit(0);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    /**
    * Set the total number of matched rows.
    */ 
    searchQuery.setGetTotalCount(true);  
    SearchResponse resp = client.search(searchRequest);
    /**
     * Check whether Table Store returns matched data from all partitions. When the value of isAllSuccess is false, Table Store may fail to query some partitions and return a part of data.
     */
    if (! resp.isAllSuccess()) {
        System.out.println("NotAllSuccess!") ;
    }
    System.out.println("IsAllSuccess: " + resp.isAllSuccess());
    System.out.println("TotalCount: " + resp.getTotalCount()); // The total number of rows.
    System.out.println(resp.getRequestId());
}
			
```

