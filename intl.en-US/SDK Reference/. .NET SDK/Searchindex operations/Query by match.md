# Query by match {#concept_mh4_1sc_fgb .concept}

## MatchAllQuery {#section_uyn_x5c_fgb .section}

You can call this operation to check the total number of rows in a table or check some rows in the table.

```
/// <summary>
/// Search all rows to obtain the number of rows that meet the conditions.
/// </summary>
/// <param name="otsClient"></param>
public static void MatchAllQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    searchQuery.Query = new MatchAllQuery();
    searchQuery.GetTotalCount = true; // Set the GetTotalCount parameter to true to obtain the number of rows that meet the conditions.
    /*
    * In a MatchAllQuery result set, the value of TotalCount is the total number of rows in a table. This value is an approximate value when you query a table with a large number of rows.
    * If you only want to query TotalHit, set Limit to 0 to specify that no row of data is returned.
    */
    searchQuery.Limit = 0;
    var request = new SearchRequest(TableName, IndexName, searchQuery);

    var response = otsClient.Search(request);
    // Check whether Table Store returns matched data from all partitions. When the value of isAllSuccess is false, Table Store may fail to query some partitions and return only a part of data.
    Console.WriteLine("IsAllSuccess:" + response.IsAllSuccess);
    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

## MatchQuery {#section_hdt_2vc_fgb .section}

You can call this operation to query a table based on approximate matches. For example, you specify a query string "this is". Table Store returns query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is".

```

/// <summary>
/// Create a fuzzy match based on a phrase or adjacent query. Table Store returns query results in a specified column.
/// </summary>
/// <param name="otsClient"></param>
public static void MatchQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to MatchQuery, the query range to Text_type_col, and the query string to SearchIndex.
    searchQuery.Query = new MatchQuery(Text_type_col, "SearchIndex");
    searchQuery.GetTotalCount = true;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        Columns = new List<string>() { Long_type_col, Text_type_col, Keyword_type_col }
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

## MatchPhraseQuery {#section_fjp_3vc_fgb .section}

You can call this operation to query data that matches a phrase. MatchPhraseQuery is similar to MatchQuery, only that Table Store matches a phrase as an entity for MatchPhraseQuery. For example, you specify a query string "this is". Table Store returns query results such as "..., this is tablestore" and "this is a table", but does not return query results such as "this table is..." and "is this a table".

```
/// <summary>
/// MatchPhraseQuery is similar to MatchQuery, only that Table Store matches words in a phrase for MatchQuery, but matches a phrase as an entity for MatchPhraseQuery.
/// </summary>
/// <param name="otsClient"></param>
public static void MatchPhraseQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to MatchPhraseQuery.
    searchQuery.Query = new MatchPhraseQuery(Text_type_col, "TableStore SearchIndex");
    searchQuery.GetTotalCount = true;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        Columns = new List<string>() { Long_type_col, Text_type_col, Keyword_type_col }
    };
    
    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

