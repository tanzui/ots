# How can I paginate query results?

Tablestore is a distributed storage system. You can use multiple methods to paginate query results. This topic describes how to paginate query results in detail.

## Paginate query results for tables

If you create only Tablestore tables without creating search indexes, you can use one of the following methods to paginate query results:

-   Use next tokens. The response to each request of the GetRange operation contains a next token. The next token can be used in the next request to read remaining data by page.
-   Use GetRangeIterator. Use the iterator.next\(\) method to obtain next piece of data.
-   offset is not supported to paginate query results.
-   The total number of rows and the total number of pages cannot be obtained.

## Paginate query results for search indexes

If you create a search index for a table, use the following method to paginate query results:

-   Use offset and limit. The sum of the offset and limit values cannot exceed 10000. If the sum of the offset and limit values exceed 10000, use next tokens to paginate query results.
-   Use next tokens. The response to each request of the GetRange operation contains a next token. The next token can be used in the next request to read remaining data by page.
-   Use SearchIterator. Use the iterator.next\(\) method to obtain next piece of data.
-   You can obtain the total number of rows and the total number of pages \(Total number of pages = Total number of rows/Value of limit\). To obtain the total number of rows, set getTotalCount in the request to true. If getTotalCount is set to true, more resources are consumed and performance may deteriorate.

## Examples for tables

The following code provides an example on how to call an operation to paginate query results by setting offset and limit.

```
/**
     * Specify the range of data to query and return specified rows of data by setting the page size and offset.
     */
    private static Pair<List<Row>, RowPrimaryKey> readByPage(OTSClient client, String tableName, 
            RowPrimaryKey startKey, RowPrimaryKey endKey, int offset, int pageSize) {
        Preconditions.checkArgument(offset >= 0, "Offset should not be negative.") ;
        Preconditions.checkArgument(pageSize > 0, "Page size should be greater than 0.") ;
        List<Row> rows = new ArrayList<Row>(pageSize);
        int limit = pageSize;
        int skip = offset;
        RowPrimaryKey nextStart = startKey;
        // If the amount of data to query is large, only part of data is returned for one request. To query all required data, use multiple range query requests.
        while (limit > 0 && nextStart ! = null) {
            // Create query parameters for GetRange.
            // Note that startPrimaryKey must be set to the position where last reading stops. This way, you can send multiple range query requests to read remaining data.
            RangeRowQueryCriteria criteria = new RangeRowQueryCriteria(tableName);
            criteria.setInclusiveStartPrimaryKey(nextStart);
            criteria.setExclusiveEndPrimaryKey(endKey);
            // Set an appropriate limit value. limit specifies the total number of rows to return (a maximum of all rows of data on a page) from the offset value.
            criteria.setLimit(skip + limit);
            GetRangeRequest request = new GetRangeRequest();
            request.setRangeRowQueryCriteria(criteria);
            GetRangeResult response = client.getRange(request);
            for (Row row : response.getRows()) {
                if (skip > 0) {
                    skip--; // The number of rows of data to filter before the offset value. The data is filtered on the client after the data is read.
                } else {
                    rows.add(row);
                    limit--;
                }
            }
            // Set the position from which to read data next time.
            nextStart = response.getNextStartPrimaryKey();
        }
        return new Pair<List<Row>, RowPrimaryKey>(rows, nextStart);
    }
			
```

The following code provides an example on how to use the preceding operation to sequentially read all data within a specified range by page:

```
  private static void readByPage(OTSClient client, String tableName) {
        int pageSize = 8;
        int offset = 33;
        RowPrimaryKey startKey = new RowPrimaryKey();
        startKey.addPrimaryKeyColumn(COLUMN_GID_NAME, PrimaryKeyValue.INF_MIN);
        startKey.addPrimaryKeyColumn(COLUMN_UID_NAME, PrimaryKeyValue.INF_MIN);
        RowPrimaryKey endKey = new RowPrimaryKey();
        endKey.addPrimaryKeyColumn(COLUMN_GID_NAME, PrimaryKeyValue.INF_MAX);
        endKey.addPrimaryKeyColumn(COLUMN_UID_NAME, PrimaryKeyValue.INF_MAX);
        // Read the first page from the 33th line.
        Pair<List<Row>, RowPrimaryKey> result = readByPage(client, tableName, startKey, endKey, offset, pageSize);
        for (Row row : result.getKey()) {
            System.out.println(row.getColumns());
        }
        System.out.println("Total rows count: " + result.getKey().size());
        // Read all data within the range and paginate query results in sequence.
        startKey = result.getValue();
        while (startKey ! = null) {
            System.out.println("============= start read next page ==============");
            result = readByPage(client, tableName, startKey, endKey, 0, pageSize);
            for (Row row : result.getKey()) {
                System.out.println(row.getColumns());
            }
            startKey = result.getValue();
            System.out.println("Total rows count: " + result.getKey().size());
        }
    }
			
```

## Examples for search indexes

For more information, see [Sorting and paging](/intl.en-US/SDK Reference/Java SDK/Search index/Sorting and paging.md).

