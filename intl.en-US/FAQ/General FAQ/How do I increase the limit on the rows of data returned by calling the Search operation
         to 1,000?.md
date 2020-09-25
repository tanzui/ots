# How do I increase the limit on the rows of data returned by calling the Search operation to 1,000?

This topic describes how to increase the limit on the rows of data returned by calling the Search operation to 1,000.

To increase the number of results returned by calling the Search operation in a query, if only data in search indexes is queried, the limit is automatically increased to 1,000. If data in tables must be queried, the limit is 100.

## Procedure

You can use the following operations to increase the limit on the rows of data returned by calling the Search operation to 1,000:

1.  When you create a search index, you must set the value of store to true for the specified column.
    -   By default, the value of store is true when you create a search index in the console. You do not need to set this parameter.
    -   When you use SDKs to create a search index, you can set the store parameter in the FieldSchema parameter to true for the specified column.
2.  When you call the Search operation to query data, you must set the ColumnsToGet parameter of SearchRequest.

    The ColumnsToGet parameter returns only columns for which the store parameter is set. Columns that of ARRAY, GEOPOINT, or NEST are excluded. The limit is automatically increased to 1,000.

    **Note:** If the ColumnsToGet parameter includes the columns that of ARRAY, GEOPOINT, or NEST, the table is still queried when you call the Search operation to query data. The limit is 100.


## Examples

In this example, Tablestore SDK for Java is used to describe how to set the ColumnsToGet parameter. The configuration method of ColumnsToGet is similar to that of SDKs in other languages. You need to modify only the ColumnsToGet parameter in SearchRequest.

```
SearchQuery searchQuery = new SearchQuery();
searchQuery.setQuery(new MatchQuery());
searchQuery.setLimit(1000);

SearchRequest searchRequest = new SearchRequest(tableName, indexName, searchQuery);
ColumnsToGet columnsToGet = new ColumnsToGet();
columnsToGet.setReturnAll(false);
columnsToGet.setColumns(Arrays.asList("field_1", "field_2", "field_3")); // Set the name of the returned column. The data type of the returned column cannot be ARRAY, GEOPOINT, or NEST. Otherwise, the table is queried.
searchRequest.setColumnsToGet(columnsToGet);
SearchResponse response = client.search(searchRequest);

// In Tablestore SDK for Java V5.6.1 and later, you can set the returnAllColumnsFromIndex parameter in ColumnsToGet to query the attribute column in all search indexes.

ColumnsToGet columnsToGet = new ColumnsToGet();
columnsToGet.setReturnAllFromIndex(true);
searchRequest.setColumnsToGet(columnsToGet);
SearchResponse response = client.search(searchRequest);
```

