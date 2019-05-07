# Index sorting and page scanning {#concept_j33_pyk_2gb .concept}

## Index sorting {#section_u5c_5bl_2gb .section}

The matched data is sorted based on the IndexSort field value when search index-based queries are used. IndexSort does not support nested queries. The default IndexSort field is the name of the primary key column. You can define the IndexSort field when you create an index. IndexSort determines the default return order when search index-based queries are used. If the IndexSort field is not set, the result is returned based on the order of primary key columns.

## Specify a sorting method {#section_v5c_5bl_2gb .section}

You can specify a sorting method for each query. Search index-based queries support the following sorting methods. You can also use multiple sorting methods and prioritize each method as needed.

**ScoreSort**

You can use ScoreSort to sort the query result by score. ScoreSort is applicable to scenarios such as full-text indexing. Note: ScoreSort must be set to sort the query result by relevance. Otherwise, the query result is returned based on the IndexSort field. Set index sorting fields:

```
{
    sorters: [
        {
            scoreSort: {
                order: TableStore.SortOrder.SORT_ORDER_ASC
            }
        }
    ]
}
```

**PrimaryKeySort**

You can use PrimaryKeySort to sort the query result based on the order of primary key columns. Set index sorting fields:

```

{
    sorters: [
        {
            primaryKeySort: {
                order: TableStore.SortOrder.SORT_ORDER_DESC // The query result is sorted in descending order.
                //order: TableStore.SortOrder.SORT_ORDER_ASC // The query result is sorted in ascending order.
            }
        }
    ]
}
```

**FieldSort**

You can use FieldSort to sort the query result by column. Set index sorting fields:

```
{
    sorters: [
        {
            fieldSort: {
                fieldName: "Col_Keyword",
                order: TableStore.SortOrder.SORT_ORDER_DESC
            }
        }
    ]
}
```

Prioritize columns to sort the query result. Set index sorting fields:

```
{
    sorters: [
        {
            fieldSort: {
                fieldName: "Col_Keyword",
                order: TableStore.SortOrder.SORT_ORDER_DESC
            }
        },
        {
            fieldSort: {
                fieldName: "Col_Long",
                order: TableStore.SortOrder.SORT_ORDER_DESC
            }
        }
    ]
}
```

**GeoDistanceSort**

You can use GeoDistanceSort to sort the query result by geographical location. Set index sorting fields:

```
{
    sorters: [
        {
            geoDistanceSort: {
                fieldName: "Col_Geo_Point",
                points: ["0,0"],//Set the coordinates of a central point.
                order: TableStore.SortOrder.SORT_ORDER_ASC //The query result is sorted in ascending order of the distances between geographical locations and the central point.
            }
        }
    ]
}
```

## Page scanning {#section_e4w_jcl_2gb .section}

Only fields with enableSortAndAgg set to true can be sorted. You can set multiple filtering conditions for index sorting.

**Use Limit and Offset**

When the total number of rows to be obtained is smaller than 2,000, you can set Limit and Offset to scan no more than 2,000 rows and return the result.

```

/**
 * Set Offset to 90 and Limit to 10 to scan data from Line 90 to Line 100.
 */
client.search({
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 90,
        limit: 10, 
        query: {
            queryType: TableStore.QueryType.MATCH_ALL_QUERY
        }
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

**Use a token**

If the operation does not complete reading the data that meets the filtering conditions, the server returns NextToken. You can use NextToken to continue reading the subsequent data. The sorting method cannot be set if a token is used. When the token is used, the sorting method used is the same as the previous request, regardless of the default sorting method used by the system or the sorting method defined by the user. Additionally, you cannot set Offset when a token is used. Data is scanned page by page, which results in a slow query.

```
/**
 * The following code provides an example of how to use tokens to scan pages in sync and async modes.
 */
var params = {
    tableName: TABLE_NAME,
    indexName: INDEX_NAME,
    searchQuery: {
        offset: 0,
        limit: 10,
        token: null,//Obtain a NextToken as the starting point to scan the next page.
        query: {
            queryType: TableStore.QueryType.MATCH_ALL_QUERY
        },
        getTotalCount: true
    },
    columnToGet: {
        returnType: TableStore.ColumnReturnType.RETURN_NONE,
        returnNames: ["pic_tag", "pic_description", "time_stemp", "pos"]
    }
};

/**
 * Use tokens to scan pages in the sync mode.
 */
(async () => {
  try {
    var data = await client.search(params);
    console.log(data);
    
    while (data.nextToken) { //If a NextToken exists, there is still data which is not obtained.
      params.searchQuery.token = data.nextToken;// Update the token value to continue scanning pages.
      data = await client.search(params);
      console.log(data);
    }
  } catch (error) {
      console.log(error);
  }
})()

/**
 * Use tokens to scan pages in the async mode.
 */
client.search(params, function (err, data) { //Update the token value.
    console.log('success:', JSON.stringify(data, null, 2));

    if (data.nextToken) {
        params.searchQuery.token = data.nextToken;// Update the token value to continue scanning pages.
        client.search(params, function (err, data) {
            console.log('token success:', JSON.stringify(data, null, 2));
        });
    }
});

/**
 * Implement persistent storage for tokens.
 * 1) NextTokens are of the buffer type. Encode the NextTokens as strings based on Base64 to implement persistent storage for these tokens.
 * 2) Convert the string tokens to buffer and use the buffer tokens as parameters.
 */
var stringToken = data.nextToken.toString("base64", data.nextToken.offset, data.nextToken.limit);
var bufferToken = new Buffer(nextToken, "base64");
```

