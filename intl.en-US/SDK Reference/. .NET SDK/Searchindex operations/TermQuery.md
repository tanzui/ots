# TermQuery {#concept_p1l_xrc_fgb .concept}

## TermQuery {#section_qy1_t5c_fgb .section}

You can call this operation to query data that exactly matches the specified value of an attribute. When you query a TEXT string, Table Store tokenizes the string and exactly matches any of the tokens. For example, Table Store tokenizes a TEXT string "tablestore is cool" into "tablestore", "is", and "cool". When you specify any of these tokens as a query string, you can retrieve query results that contain "tablestore is cool".

```
/// <summary>
/// Search the Keyword_type_col column for data that exactly matches the query string SearchIndex.
/// </summary>
/// <param name="otsClient"></param>
public static void TermQuery(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    // Table Store returns the total count of query results.
    searchQuery.GetTotalCount = true;
    // Set the query type to TermQuery, the query range to Keyword_type_col, and the query string to SearchIndex.
    searchQuery.Query = new TermQuery(Keyword_type_col, new ColumnValue("SearchIndex"));

    var request = new SearchRequest(TableName, IndexName, searchQuery);
    request.ColumnsToGet = new ColumnsToGet()
    {
        ReturnAll = true // Table Store returns all columns of query results.
    };
    var response = otsClient.Search(request);
    // Table Store returns the NextToken string to check whether there are additional available results.
```

