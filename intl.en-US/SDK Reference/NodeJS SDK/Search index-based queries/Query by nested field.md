# Query by nested field {#concept_z5n_4yk_2gb .concept}

## NestedQuery {#section_ugj_xyk_2gb .section}

You cannot directly use a Nested field to query data. You must wrap the Nested field in a nested query. To implement the nested query, you must specify a subquery \(which can be any query\) and the path of the nested field.

**Examples**

```
/**
 * Search the Col_Nested.Sub_Col_Keyword column for data that matches "hangzhou."
 * Col_Nested: '[{Sub_Col_Keyword: "Hangzhou"},{Sub_Col_Keyword: "Shanghai"}]'
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to NestedQuery.
            queryType: TableStore.QueryType.NESTED_QUERY,
            query: {
                path: "Col_Nested",
                query: {
                    queryType: TableStore.QueryType.MATCH_ALL_QUERY,
                    query: {
                        fieldName: "Col_Nested.Sub_Col_Keyword",
                        term: "Hangzhou"
                    }
                },
            }
        },
        getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the filtering conditions is returned. If TotalCount is set to false, the total number of rows that meet the filtering conditions is not returned.
    },
    columnToGet: { //Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
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

