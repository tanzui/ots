# Query by prefix {#concept_zlw_hwk_2gb .concept}

## PrefixQuery {#section_yxx_hxk_2gb .section}

You can use PrefixQuery to specify a prefix as a filtering condition. When a table contains a Text string, Table Store tokenizes the string and matches any of the tokens with the specified prefix.

**Examples**

```


/**
 * Search the Col_Keyword column of the table for data with the prefix that is an exact match of "hang," such as "hangzhou."
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to PrefixQuery.
            queryType: TableStore.QueryType.PREFIX_QUERY,
            query: {
                fieldName: "Col_Keyword",
                prefix: "hang" //Specify the prefix, such as "hangzhou" or "hangzhoushi."
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

