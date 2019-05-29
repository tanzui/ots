# Sorting and paging {#concept_hpd_5sc_fgb .concept}

## indexSort {#section_jtb_wxc_fgb .section}

The indexSort parameter indicates the presorting method. After you create a Searchindex structure on a table, Table Store presorts data according to the indexSort parameter by default. \(A Searchindex structure with nested fields does not support the indexSort parameter and presorting.\) Table Store returns query results by primary key by default. You can also set the indexSort parameter to specify the sorting method when creating a Searchindex structure. This parameter determines the default sequence in which Table Store returns query results. Table Store returns results by primary key by default.

## Sorting methods {#section_ktb_wxc_fgb .section}

You can specify the sorting method for each query. The Searchindex structure supports the following four sorting methods. By specifying multiple sorting methods, you can sort data by one method and then by another.

**ScoreSort**

Sort data by score. This method applies to correlation-based indexing such as full-text indexing. You must specify the ScoreSort parameter so that you can sort data by score of correlation. Otherwise, query results are returned in the sequence indicated by the indexSort parameter.

```
var searchQuery = new SearchQuery();
searchQuery.Sort = new Sort(new List<ISorter>() { new ScoreSort() });
```

**PrimaryKeySort**

Sort data by primary key.

```
Ascending:
var searchQuery = new SearchQuery();
searchQuery.Sort = new Sort(new List<ISorter>() { new PrimaryKeySort() };

Descending:
var searchQuery = new SearchQuery();
searchQuery.Sort = new Sort(new List<ISorter>() { new PrimaryKeySort(SortOrder.DESC) };


```

**FieldSort**

Sort data by a column.

```
var searchQuery = new SearchQuery();
var fieldSort = new FieldSort("col", SortOrder.ASC);
searchQuery.Sort = new Sort(new List<ISorter>() { fieldSort };
```

Sort data by a column and then by another.

```
var searchQuery = new SearchQuery();
var col1Sort = new FieldSort("col", SortOrder.ASC);
var col2Sort = new FieldSort("co2", SortOrder.ASC);
searchQuery.Sort = new Sort(new List<ISorter>() { col1Sort, col2Sort };
```

**GeoDistanceSort**

Sort data by geographical distance.

```
var searchQuery = new SearchQuery();
var geoDistanceSort = new GeoDistanceSort("geoCol", new List<string>(){"0","0"};
searchQuery.Sort = new Sort(new List<ISorter>() { geoDistanceSort };
```

## Paging methods { .section}

**Set Limit and Offset**

When the total number of rows to be obtained is smaller than or equal to 2,000, we recommend you set the Limit and Offset parameters for paging. \(The total sum of the values of the Limit and Offset parameters must be smaller than or equal to 2,000.\)

```
var searchQuery = new SearchQuery();
searchQuery.Query = new MatchAllQuery();
searchQuery.Limit = 100;
searchQuery.Offset = 100;
```

**Use a token**

If additional data records are available apart from returned results, Table Store will return a NextToken string. You can use the NextToken string to receive query results on the next page. After you use a token, the sorting method is the same as that of the last query. \(The sorting method of the last query may be indicated by the default value of the indexSort parameter or specified by you.\) In this case, you cannot specify the sorting method. In addition, you cannot set the Offset parameter after using a token. You can only retrieve data page by page without skipping.

```
/// <summary>
/// In this example, a token is used for paging. Table Store returns all data records into a list.
/// </summary>
/// <param name="otsClient"></param>
public static SearchResponse ReadMoreRowsWithToken(OTSClient otsClient)
{
    var searchQuery = new SearchQuery();
    searchQuery.Query = new MatchAllQuery();
    
    var request = new SearchRequest(TableName, IndexName, searchQuery);

    var response = otsClient.Search(request);
    var rows = response.Rows;
    while (response.NextToken ! = null) // The value of the NextToken string is null, indicating that all data records are retrieved.
    {
        request.SearchQuery.Token = response.NextToken;
        response = otsClient.Search(request);
        rows.AddRange(response.Rows);
    }

    return response;
}
```

