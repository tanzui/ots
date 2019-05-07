# Query by exact match {#concept_f4f_gwk_2gb .concept}

## TermQuery {#section_yyf_jwk_2gb .section}

You can use TermQuery to query data that exactly matches the specified value of a field. When you query a Text string, Table Store tokenizes the string and exactly matches any of the tokens.

For example, Table Store tokenizes a Text string "tablestore is cool" into "tablestore," "is," and "cool." When you specify any of these tokens as a query string, you retrieve the query result that contains the token.

```


/**
 * Search the Col_Keyword column of the table for data that exactly matches "hangzhou."
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to TermQuery.
            queryType: TableStore.QueryType.TERM_QUERY,
            query: {
                fieldName: "Col_Keyword",
                term: "hangzhou"
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

## TermsQuery {#section_rgf_nwk_2gb .section}

You can use TermsQuery to query data that exactly matches the specified field values. TermQuery is similar to TermsQuery. The difference is that TermsQuery supports multiple terms. You can retrieve query results that match any of these terms.

```

/**
 * Search the Col_Keyword column of the table for data that matches "hangzhou" or "shanghai."
 * TermsQuery supports multiple terms. You can retrieve query results that match any of these terms.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: { // Set the query type to TermsQuery.
            queryType: TableStore.QueryType.TERMS_QUERY,
            query: {
                fieldName: "Col_Keyword",
                terms: ["hangzhou", "shanghai"]
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

