# Sorting and paging {#concept_slf_n3x_dgb .concept}

## IndexSort {#section_lly_qlx_dgb .section}

Table Store sorts matched data based on the IndexSort field value in Search Index-based queries. Indexes of NESTED type does not support IndexSort. The default IndexSort field value is the name of a primary key column. You can define the IndexSort field when you create an index.

IndexSort determines the default order of the data that Table Store returns in Search Index-based queries. If you do not specify the IndexSort field, Table Store returns the query result in the order of primary key columns.

## Specify a sorting method {#section_zbx_tlx_dgb .section}

You can use Sorter to specify a sorting method for each query. Search Index-based queries support the following sorting methods. You can also use multiple sorting methods and prioritize each method as needed.

**ScoreSort**

You can use ScoreSort to sort the query result by score. ScoreSort is applicable to relevance scenarios such as full-text indexing. You must set ScoreSort to sort the query result by relevance. Otherwise, Table Store returns the query result based on the IndexSort field.

```

SearchQuery searchQuery = new SearchQuery();
searchQuery.setSort(new Sort(Arrays.asList(new ScoreSort())));
```

**PrimaryKeySort**

You can use PrimaryKeySort to sort the query result by primary key column.

```
Forward order:
SearchQuery searchQuery = new SearchQuery();
searchQuery.setSort(new Sort(Arrays.asList(new PrimaryKeySort())));

Inverted order:
SearchQuery searchQuery = new SearchQuery();
searchQuery.setSort(new Sort(Arrays.asList(new PrimaryKeySort(SortOrder.DESC))));
```

**FieldSort**

You can use FieldSort to sort the query result by a specified column.

```
SearchQuery searchQuery = new SearchQuery();
searchQuery.setSort(new Sort(Arrays.asList(new FieldSort("col", SortOrder.ASC))));
```

Prioritize columns to sort the query result.

```
SearchQuery searchQuery = new SearchQuery();
searchQuery.setSort(new Sort(Arrays.asList(
    new FieldSort("col1", SortOrder.ASC), new FieldSort("col2", SortOrder.ASC))));
```

**GeoDistanceSort**

You can use GeoDistanceSort to sort the query result by geographic location.

```
SearchQuery searchQuery = new SearchQuery();
// Sort the result by the distance from the value of the geo column of GEOPOINT type to the "0,0" point.
Sort.Sorter sorter = new GeoDistanceSort("geo", Arrays.asList("0, 0"));
searchQuery.setSort(new Sort(Arrays.asList(sorter)));
```

## Paging {#section_yy2_pmx_dgb .section}

**Use LIMIT and OFFSET**

When the total number of target rows is less than 2,000, to scan 2,000 pages or fewer and return the result, you can set LIMIT and OFFSET in the following way: LIMIT+OFFSET <= 2000.

```
SearchQuery searchQuery = new SearchQuery();
searchQuery.setQuery(new MatchAllQuery());
searchQuery.setLimit(100);
searchQuery.setOffset(100);
```

**Use a token**

If Table Store does not complete reading all required data, Table Store returns NextToken. You can use NextToken to continue reading the subsequent data. You cannot set the sorting method if you use a token. When you use the token, the sorting method is the same as that used in the previous request. The system sorts data based on the IndexSort field by default or based on the method that you have specified. Also, you cannot set Offset if you use a token. In this case, the system scans data page by page. This results in a slow query.

```


/**
 * If you use a token for paging, in this example, the system reads all data and places the data in a list.
 * @param client
 */
private static void readMoreRowsWithToken(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setQuery(new MatchAllQuery());
    searchQuery.setGetTotalCount(true);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    SearchResponse resp = client.search(searchRequest);
    if (! resp.isAllSuccess()) {
        throw new RuntimeException("not all success");
    }
    List<Row> rows = resp.getRows();
    while (resp.getNextToken()! =null) { //The system stops reading when NextToken is null. This indicates that the system reads all data.
        //Set Token in this request to the NextToken value in the previous response.
        searchRequest.getSearchQuery().setToken(resp.getNextToken()); 
        resp = client.search(searchRequest);
        if (! resp.isAllSuccess()) {
            throw new RuntimeException("not all success");
        }
        rows.addAll(resp.getRows());
    }
    System.out.println("RowSize: " + rows.size());
    System.out.println("TotalCount: " + resp.getTotalCount());
}
```

