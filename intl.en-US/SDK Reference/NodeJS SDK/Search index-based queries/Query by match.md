# Query by match {#concept_fdg_hwk_2gb .concept}

## MatchAllQuery {#section_fx1_rwk_2gb .section}

You can use MatchAllQuery to query the total number of rows or any number of rows in a table.

```
/**
 * Use MatchAllQuery to query the total number of rows in a table.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: {
            queryType: TableStore.QueryType.MATCH_ALL_QUERY
        },
        getTotalCount: true // If TotalCount is set to true, the total number of rows that meet the filtering conditions is returned. If TotalCount is set to false, the total number of rows that meet the filtering conditions is not returned.
    },
    columnToGet: { //Set RETURN_SPECIFIED to return a specified number of columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data.
        returnType: TableStore.ColumnReturnType.RETURN_SPECIFIED,
        returnNames: ["Col_1", "Col_2", "Col_3"]
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

## MatchQuery {#section_qh2_twk_2gb .section}

You can use MatchQuery to query a table based on approximate matches. For example, you can query data that matches "this is." Table Store returns query results such as "..., this is tablestore," "is this tablestore," "tablestore is cool," "this," and "is."

```

/**
 * Search the Col_Keyword column of the table for data that matches "hangzhou." Table Store returns matched rows and the total number of matched rows.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to MatchQuery.
            queryType: TableStore.QueryType.MATCH_QUERY,
            query: {
                fieldName: "Col_Keyword",
                text: "hangzhou" // Set the value that you want to match.
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

## MatchPhraseQuery {#section_wns_zwk_2gb .section}

You can use MatchPhraseQuery to specify a phrase as a filtering condition. MatchPhraseQuery is similar to MatchQuery. The difference is that MatchPhraseQuery matches the exact phrase as a whole. For example, if you query a phrase "this is," you can obtain query results such as "..., this is tablestore," and "this is a table." However, you cannot obtain query results such as "this table is ..." and "is this a table."

```

/**
 * Search the Col_Text column of the table for data that matches "hangzhou shanghai." 
 * Table Store returns the total number of rows that match the phrase as a whole and matched rows in this query.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: {// Set the query type to MatchPhraseQuery.
            queryType: TableStore.QueryType.MATCH_PHRASE_QUERY,
            query: {
                fieldName: "Col_Text", // Set the field that you want to match.
                text: "hangzhou shanghai" // Set the value that you want to match.
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

