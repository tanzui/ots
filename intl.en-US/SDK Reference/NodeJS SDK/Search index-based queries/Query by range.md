# Query by range {#concept_cmj_4xk_2gb .concept}

## RangeQuery {#section_mh2_rxk_2gb .section}

You can use RangeQuery to specify a range as a filtering condition. When a table contains a Text string, Table Store tokenizes the string and matches any of the tokens that falls within the specified range.

**Examples**

```

/**
 * Search the table for rows where the value range of Col_Long is [1, 10). Table Store sorts these rows by Col_Long in descending order.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to RangeQuery.
            queryType: TableStore.QueryType.RANGE_QUERY,
            query: {
                fieldName: "Col_Long",
                rangeFrom: 1,
                includeLower: true, // >= 1
                rangeTo: 10,
                includeUpper: false // < 10
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

