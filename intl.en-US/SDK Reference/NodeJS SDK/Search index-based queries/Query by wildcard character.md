# Query by wildcard character {#concept_cgj_pxk_2gb .concept}

## WildcardQuery {#section_c2r_5xk_2gb .section}

You can use WildcardQuery to match wildcard characters in a query. You can specify a value you want to match as a string that consists of one or more wildcard characters.

An asterisk \(\*\) represents any length of characters. A question mark \(?\) represents any single character. For example, when you specify a string "table\*e" as a filtering condition, you can retrieve query results such as "tablestore." The string specified as a filtering condition cannot start with an asterisk \(\*\).

**Examples**

```
/**
 * Search the Col_Keyword column of the table for data that matches "hang*u."
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to WildcardQuery.
            queryType: TableStore.QueryType.WILDCARD_QUERY,
            query: {
                fieldName: "Col_Keyword",
                value: "table*e" //Set wildcard characters.
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

