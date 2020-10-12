# Wildcard query

You can use asterisks \(\*\) and question marks \(?\) to search for data that matches wildcards. An asterisk \(\*\) represents any number of characters. A question mark \(?\) represents any single character. The matched value can start with asterisks \(\*\) or question marks \(?\). For example, if you search for string "table\*e", you can retrieve query results such as "tablestore".

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Create search indexes.md).

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|indexName|The name of the search index.|
|offset|Specifies the position from which the current query starts.|
|limit|Specifies the maximum number of items that the current query returns.|
|queryType|The query type. Set the query type to TableStore.QueryType.WILDCARD\_QUERY.|
|fieldName|The column name.|
|value|The string that contains wildcards. The string cannot exceed 10 bytes in length.|
|getTotalCount|Specifies whether to return the total number of matched rows. The default value is false, which indicates that the total number of rows in the table is not returned. If getTotalCount is set to true, the query performance is affected. |
|columnToGet|Specifies whether to return all columns. -   If you set returnType to TableStore.ColumnReturnType.RETURN\_SPECIFIED, you must specify the columns to be returned.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_ALL, all columns are returned.
-   If you set returnType to TableStore.ColumnReturnType.RETURN\_NONE, only the primary key columns are returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword matches "table*e".
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, // You can set limit to 0 if you do not need the specific data but the number of matched rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to TableStore.QueryType.WILDCARD_QUERY.
            queryType: TableStore.QueryType.WILDCARD_QUERY,
            query: {
                fieldName: "Col_Keyword",
                value: "table*e" // wildcardQuery can contain wildcards.
            }
        },
        getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the filter conditions is returned. If TotalCount is set to false, the total number of rows that meet the filter conditions is not returned.
    },
    columnToGet: { // Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
        returnType: TableStore.ColumnReturnType.RETURN_ALL
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

