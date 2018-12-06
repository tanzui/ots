# Multiple-row operations {#concept_43017_zh .concept}

The Table Store SDK provides BatchGetRow, BatchWriteRow, GetRange, createRangeIterator, and other row operation APIs.

## BatchGetRow {#section_fdb_ph2_2fb .section}

The BatchGetRow API can read data from multiple rows with a single request. The parameters are the same as those of the GetRow API. Note that BatchGetRow uses the same parameter conditions for all the rows. For example, if ColumnsToGet=\[colA\], only the colA value is read for all the rows.

Similar to the BatchWriteRow API, when using the BatchGetRow API, you must check the returned values. If the operation fails for some rows, the system does not throw an exception but stores information of the failed rows in BatchGetRowResponse. You can use the BatchGetRowResponse\#getFailedRows method to get information on the failed rows, or use the BatchGetRowResponse\#isAllSucceed to determine if all rows were read.

**Example**

Set BatchGetRow to read 10 rows. Set the version conditions, the columns to read, and filters.

```language-java
        private static void batchGetRow(SyncClient client) {
            MultiRowQueryCriteria multiRowQueryCriteria = new MultiRowQueryCriteria(TABLE_NAME);
            // Add 10 rows to read
            for (int i = 0; i < 10; i++) {
                PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk" + i));
                PrimaryKey primaryKey = primaryKeyBuilder.build();
                multiRowQueryCriteria.addRow(primaryKey);
            }
            // Add conditions
            multiRowQueryCriteria.setMaxVersions(1);
            multiRowQueryCriteria.addColumnsToGet("Col0");
            multiRowQueryCriteria.addColumnsToGet("Col1");
            SingleColumnValueFilter singleColumnValueFilter = new SingleColumnValueFilter("Col0",
                    SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
            singleColumnValueFilter.setPassIfMissing(false);
            multiRowQueryCriteria.setFilter(singleColumnValueFilter);
    
            BatchGetRowRequest batchGetRowRequest = new BatchGetRowRequest();
            // BatchGetRow allows you to read data from multiple tables. A single multiRowQueryCriteria corresponds to a query condition for one table. You can add multiRowQueryCriteria.
            batchGetRowRequest.addMultiRowQueryCriteria(multiRowQueryCriteria);
    
            BatchGetRowResponse batchGetRowResponse = client.batchGetRow(batchGetRowRequest);
    
            System.out.println("Were all successful:" + batchGetRowResponse.isAllSucceed());
            if (! batchGetRowResponse.isAllSucceed()) {
                for (BatchGetRowResponse.RowResult rowResult : batchGetRowResponse.getFailedRows()) {
                    System.out.println("Failed rows:" + batchGetRowRequest.getPrimaryKey(rowResult.getTableName(), rowResult.getIndex()));
                    System.out.println("Cause of failure:" + rowResult.getError());
                }
    
                /**
                 * You can use the createRequestForRetry method to construct another request to retry failed rows. Here, we only construct part of the retry request.
                 * For the retry method, we recommend using the SDK's custom retry policy function. This function allows you to retry failed rows after batch operations. After setting the retry policy, you do not need to add retry code to the calling API.
                 */
                BatchGetRowRequest retryRequest = batchGetRowRequest.createRequestForRetry(batchGetRowResponse.getFailedRows());
            }
        }

```

## BatchWriteRow { .section}

The BatchWriteRow API allows you to perform multiple write operations with a single request. These write operations include PutRow, UpdateRow, and DeleteRow. This API also allows you to write to multiple tables at one time.

The process for constructing a single operation is the same as using the PutRow, UpdateRow, or DeleteRow API. This function also allows you to set update conditions.

When calling the BatchWriteRow API, you must be sure to check the returned values. When calling the BatchWriteRow API, you must be sure to check the returned values. During batch writing, some rows may be written while other rows fail. The failed row index and error messages are inserted into the returned BatchWriteRowResponse. If some rows fail, the system does not throw an exception. You can use the BatchWriteRowResponse’s isAllSucceed method to judge if all rows were written successfully. Some failed operations may be ignored if you do not check for them. In some situations, the BatchWriteRow API may throw an exception. For example, if the server detects that some operations have incorrect parameters, the system may throw a parameter error exception. When an exception is thrown, this means that none of the operations in the request have been completed.

**Example**

Here, a single BatchWriteRow request is sent. It includes 2 PutRow operations, 1 UpdateRow operation, and 1 DeleteRow operation.

```language-java
        private static void batchWriteRow(SyncClient client) {
            BatchWriteRowRequest batchWriteRowRequest = new BatchWriteRowRequest();

            // Construct rowPutChange1
            PrimaryKeyBuilder pk1Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            pk1Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk1"));
            RowPutChange rowPutChange1 = new RowPutChange(TABLE_NAME, pk1Builder.build());
            // Add several columns
            for (int i = 0; i < 10; i++) {
                rowPutChange1.addColumn(new Column("Col" + i, ColumnValue.fromLong(i)));
            }
            // Add to batch operation
            batchWriteRowRequest.addRowChange(rowPutChange1);

            // Construct rowPutChange2
            PrimaryKeyBuilder pk2Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            pk2Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk2"));
            RowPutChange rowPutChange2 = new RowPutChange(TABLE_NAME, pk2Builder.build());
            // Add several columns
            for (int i = 0; i < 10; i++) {
                rowPutChange2.addColumn(new Column("Col" + i, ColumnValue.fromLong(i)));
            }
            // Add to batch operation
            batchWriteRowRequest.addRowChange(rowPutChange2);

            // Construct rowUpdateChange
            PrimaryKeyBuilder pk3Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            pk3Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk3"));
            RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, pk3Builder.build());
            // Add several columns
            for (int i = 0; i < 10; i++) {
                rowUpdateChange.put(new Column("Col" + i, ColumnValue.fromLong(i)));
            }
            // Delete a column
            rowUpdateChange.deleteColumns("Col10");
            // Add to batch operation
            batchWriteRowRequest.addRowChange(rowUpdateChange);

            // Construct rowDeleteChange
            PrimaryKeyBuilder pk4Builder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            pk4Builder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk4"));
            RowDeleteChange rowDeleteChange = new RowDeleteChange(TABLE_NAME, pk4Builder.build());
            // Add to batch operation
            batchWriteRowRequest.addRowChange(rowDeleteChange);

            BatchWriteRowResponse response = client.batchWriteRow(batchWriteRowRequest);

            System.out.println("Were all successful:" + response.isAllSucceed());
            if (! response.isAllSucceed()) {
                for (BatchWriteRowResponse.RowResult rowResult : response.getFailedRows()) {
                    System.out.println("Failed rows:" + batchWriteRowRequest.getRowChange(rowResult.getTableName(), rowResult.getIndex()).getPrimaryKey());
                    System.out.println("Cause of failure:" + rowResult.getError());
                }
                /**
                 * You can use the createRequestForRetry method to construct another request to retry failed rows. Here, we only construct part of the retry request.
                 * For the retry method, we recommend using the SDK's custom retry policy function. This function allows you to retry failed rows after batch operations. After setting the retry policy, you do not need to add retry code to the calling API.
                 */
                BatchWriteRowRequest retryRequest = batchWriteRowRequest.createRequestForRetry(response.getFailedRows());
            }
        }

```

## GetRange { .section}

The GetRange API is used to read data in a certain range. In Table Store tables, all rows are sorted by their primary keys and primary keys are sequentially composed by all primary key columns. Therefore, do not assume rows are sorted by a certain column of primary keys.

The GetRange API allows you to read data in a forward or backward direction according to a given range. You can also limit the number of rows to read. If the range is large and the number of scanned rows or the volume of data exceeds the limit, the scan stops and the read rows and next primary key are returned. If not all rows are read, you can initiate a request to start from were the last operation left off and read the remaining rows based on the next primary key returned by the previous operation.

GetRange requests have the following main parameters:

-   Direction: Enumeration type, which includes the values FORWARD or BACKWARD.

-   InclusiveStartPrimaryKey: The primary key to start from \(inclusive\). If the direction is set to backward, the start primary key must be greater than the end primary key.

-   ExclusiveEndPrimaryKey: The primary key to end on \(exclusive\). If the direction is set to backward, the start primary key must be greater than the end primary key.

-   Limit: The maximum number of rows for this request.

-   ColumnsToGet: The set of columns to read. If this is not set, all columns are read.

-   MaxVersions: The maximum number of versions to read. At least one of the MaxVersions and TimeRange parameters must be set.

-   TimeRange: The range of version numbers to read. At least one of the MaxVersions and TimeRange parameters must be set.

-   Filter: The filters applied. Filters are used by the server to filter the read results again.


**Example**

The following operation reads in the forward direction, judges whether or not the NextStartPrimaryKey is null, and reads all data within the range.

```language-java
       private static void getRange(SyncClient client, String startPkValue, String endPkValue) {
            RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
    
            // Set StartPrimaryKey
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(startPkValue));
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(primaryKeyBuilder.build());
    
            // Set EndPrimaryKey
            primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(endPkValue));
            rangeRowQueryCriteria.setExclusiveEndPrimaryKey(primaryKeyBuilder.build());
    
            rangeRowQueryCriteria.setMaxVersions(1);
    
            System.out.println("GetRange result:");
            while (true) {
                GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
                for (Row row : getRangeResponse.getRows()) {
                    System.out.println(row);
                }
    
                // If the nextStartPrimaryKey is not null, continue reading.
                if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                    rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
                } else {
                    break;
                }
            }
        }

```

## createRangeIterator { .section}

The following operation reads data by iteration.

**Example**

```language-java
        private static void getRangeByIterator(SyncClient client, String startPkValue, String endPkValue) {
            RangeIteratorParameter rangeIteratorParameter = new RangeIteratorParameter(TABLE_NAME);
    
            // Set StartPrimaryKey
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(startPkValue));
            rangeIteratorParameter.setInclusiveStartPrimaryKey(primaryKeyBuilder.build());
    
            // Set EndPrimaryKey
            primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(endPkValue));
            rangeIteratorParameter.setExclusiveEndPrimaryKey(primaryKeyBuilder.build());
    
            rangeIteratorParameter.setMaxVersions(1);
    
            Iterator<Row> iterator = client.createRangeIterator(rangeIteratorParameter);
    
            System.out.println("Use Iterator to implement GetRange result:");
            while (iterator.hasNext()) {
                Row row = iterator.next();
                System.out.println(row);
            }
        }

```

