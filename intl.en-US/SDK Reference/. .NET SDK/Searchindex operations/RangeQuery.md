# RangeQuery {#concept_z3q_nsc_fgb .concept}

## RangeQuery {#section_kwn_5vc_fgb .section}

You can call this operation to query data that falls within a range that you specify in a query. When a table contains a TEXT string, Table Store tokenizes the string and matches any of the tokens that falls within the specified range.

```

/// <summary>
/// Create a RangeQuery structure. Specify a range. Search for data that falls within the range.
/// </summary>
/// <param name="otsClient"></param>
public static void RangeQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    searchQuery.GetTotalCount = true;
    var rangeQuery = new RangeQuery(Long_type_col, new ColumnValue(0), new ColumnValue(6));
    // The value includes the lower boundary value, which is greater than or equal to 0.
    rangeQuery.IncludeLower = true;
    searchQuery.Query = rangeQuery;
    var request = new SearchRequest(TableName, IndexName, searchQuery);
    var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}
```

