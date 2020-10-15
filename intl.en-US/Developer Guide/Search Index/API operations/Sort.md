# Sort

You can specify IndexSort when you create a search index and specify a sorting method when you query data. If you do not specify the sorting method for the search, the system determines the sorting method based on the default IndexSort value.

## Index sorting

The matched data is sorted based on the IndexSort field value when search indexes are used in a query. Search indexes of the NEST type do not support IndexSort. Tablestore returns the query result based on the default IndexSort field value, which is the order of primary key columns. You can specify the IndexSort field when you create a search index. If you do not specify the IndexSort field, Tablestore returns the query result based on the order of primary key columns.

**Note:** Search indexes of the NEST type do not support IndexSort.

## Specify a sorting method

Sorting can be enabled only for fields for which enableSortAndAgg is set to true.

You can specify a sorting method for each query. Search index-based queries support the following sorting methods: You can also specify multiple sorting methods based on different priorities.

-   ScoreSort

    You can use ScoreSort to sort the query result based on the BM25-based keyword relevance score. ScoreSort is applicable to scenarios such as full-text search.

    **Note:** You must set ScoreSort to sort the query result by keyword relevance score. Otherwise, the query result is sorted based on the value of the IndexSort field.

    ```
    
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(new ScoreSort())));
    ```

-   PrimaryKeySort

    You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(new PrimaryKeySort()))); // Ascending order.
    //searchQuery.setSort(new Sort(Arrays.asList(new PrimaryKeySort(SortOrder.DESC)))); // Descending order.
    ```

-   FieldSort

    You can use FieldSort to sort the query result based on a specified column.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(new FieldSort("col", SortOrder.ASC))));
    ```

    You can sort the query result based on the value of multiple columns.

    ```
    SearchQuery searchQuery = new SearchQuery();
    searchQuery.setSort(new Sort(Arrays.asList(
        new FieldSort("col1", SortOrder.ASC), new FieldSort("col2", SortOrder.ASC))));
    ```

-   GeoDistanceSort

    You can use GeoDistanceSort to sort the query result by geographical location.

    ```
    SearchQuery searchQuery = new SearchQuery();
    // Sort the result based on the distance from the value in the GEOPOINT geo column to the coordinates (0,0).
    Sort.Sorter sorter = new GeoDistanceSort("geo", Arrays.asList("0, 0"));
    searchQuery.setSort(new Sort(Arrays.asList(sorter)));
    ```


