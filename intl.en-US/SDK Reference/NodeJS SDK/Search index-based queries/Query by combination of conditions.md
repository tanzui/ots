# Query by combination of conditions {#concept_zwr_nyk_2gb .concept}

## BoolQuery {#section_cww_qyk_2gb .section}

You can use BoolQuery to set a combination of filtering conditions. A BoolQuery contains one or more subqueries as filtering conditions. You can obtain the rows that match the filtering conditions.

You can combine these subqueries in different ways. If you specify these subqueries as mustQueries, Table Store returns the result that meets all filtering conditions. If you specify these subqueries as mustNotQueries, Table Store returns the result that meets none of the filtering conditions.

**Examples**

```
/**
 * Use BoolQuery to query data that matches a combination of conditions. Parameter description
 * mustQueries: Condition 1 and Condition 2 must be met at the same time (A AND B). 
 * shouldQueries: Either Condition 1 or Condition 2 is met, or both of them must be met (A OR B).
 * filterQueries: The document must completely match all subfilters (A AND B). filterQueries are similar to mustQueries. The difference is that the scores are not calculated if filterQueries are used.
 * mustNotQueries: Neither Condition 1 nor Condition 2 is met (!A AND !B).
 * minimumShouldMatch: The minimum number of shouldQueries that must be met. The default value is 1.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10, //You can set Limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
        query: {//6
            queryType: TableStore.QueryType.BOOL_QUERY,
            query: {
                mustQueries: [ //mustQueries, shouldQueries, and mustNotQueries are optional. 
                    { //Condition 1: Use RangeQuery to query data where the value of Col_Long is greater than 3.
                        queryType: TableStore.QueryType.RANGE_QUERY,
                        query: {
                            fieldName: "Col_Long",
                            rangeFrom: 3,
                            includeLower: true
                        }
                    },
                    { //Condition 2: Set MatchQuery to query data where the value of Col_Keyword matches "hangzhou."
                        queryType: TableStore.QueryType.TERM_QUERY,
                        query: {
                            fieldName: "Col_Keyword",
                            term: "hangzhou"
                        }
                    }
                ],
                minimumShouldMatch: 1 //Set minimumShouldMatch to 1.
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

