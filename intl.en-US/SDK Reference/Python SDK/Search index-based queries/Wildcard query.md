# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) to search for data that matches wildcards. An asterisk \(\*\) represents any number of characters. A question mark \(?\) represents any single character. The matched value can start with asterisks \(\*\) or question marks \(?\). For example, if you search for string "table\*e", you can retrieve query results such as "tablestore".

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|query|The query type. Set the query type to WildcardQuery.|
|field\_name|The column name.|
|value|The string that contains wildcards. The string cannot exceed 10 bytes in length.|
|table\_name|The name of the table.|
|index\_name|The name of the search index.|
|limit|Specifies the maximum number of items that the current query returns.|
|get\_total\_count|Specifies whether to return the total number of matched rows. Default value: False. If get\_total\_count is set to true, the query performance is affected. |
|ColumnsToGet|Specifies whether to return all columns. -   If you set return\_type to ColumnReturnType.SPECIFIED, you must specify the columns to be returned.
-   If you set return\_type to ColumnReturnType.ALL, all columns are returned.
-   If you set return\_type to ColumnReturnType.NONE, only the primary key columns are returned. |

## Examples

```
query = WildcardQuery('k', 'key00*')
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

