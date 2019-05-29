# PrefixQuery {#concept_lbm_msc_fgb .concept}

You can call this operation to query data that matches a prefix that you specify in a query. When a table contains a TEXT string, Table Store tokenizes the string and matches any of the tokens with the specified prefix.

```
/// <summary>
/// Create a PrefixQuery structure.
/// </summary>
/// <param name="otsClient"></param>
public static void PrefixQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to PrefixQuery, the query range to Keyword_type_col, and the prefix to Search.
    searchQuery.Query = new PrefixQuery(Keyword_type_col, "Search");
    searchQuery.GetTotalCount = true;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true // Table Store returns all columns of query results.
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

