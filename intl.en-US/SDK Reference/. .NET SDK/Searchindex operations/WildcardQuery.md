# WildcardQuery {#concept_d25_4sc_fgb .concept}

## WildcardQuery {#section_b1p_zvc_fgb .section}

You can call this operation to match wildcard characters in a query. You can specify a value to be matched as a string with one or more wildcard characters. An asterisk \(\*\) represents any length of characters. A question mark \(?\) represents any single character. For example, when you query a string "table\*e", Table Store returns query results such as "tablestore". The specified string for matching cannot start with an asterisk \(\*\).

```
/// <summary>
/// Create a WildcardQuery structure. An asterisk (*) represents any length of characters. A question mark (?) represents any single character.
/// </summary>
/// <param name="otsClient"></param>
public static void WildcardQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Set the query type to WildcardQuery. The query value can contain wildcards.
    searchQuery.Query = new WildcardQuery(Keyword_type_col, "*Search*");
    searchQuery.GetTotalCount = true;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true
    };

    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

