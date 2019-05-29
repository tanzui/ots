# NestedQuery {#concept_whw_ssc_fgb .concept}

## NestedQuery {#section_h5h_qxc_fgb .section}

You can call this operation to query nested objects by specifying the path for the nested attribute and a subquery \(which can be any query\).

Example

```
/// <summary>
/// A sub-document contains two nested columns, namely, nested_1 and nested_2. Search col1_nested.nested_1 for the data with the value of tablestore.
/// </summary>
/// <param name="otsClient"></param> 
public static void NestedQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    searchQuery.GetTotalCount = true;
    var nestedQuery = new NestedQuery();
    nestedQuery.Path = "col1_nested"; // Set the path for the nested attribute.
    TermQuery termQuery = new TermQuery("col1_nested.nested_1",new ColumnValue("tablestore"));// Create a subquery for the NestedQuery structure.
    nestedQuery.Query = termQuery;
    nestedQuery.ScoreMode = ScoreMode.None;

    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true
    };

     var response = otsClient.Search(request);

    Console.WriteLine("Total Count:" + response.TotalCount);
}


```

