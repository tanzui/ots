# Multiple-row operations {#reference7826 .reference}

The Table Store SDK provides the following multi-row operation interfaces: BatchGetRow, BatchWriteRow, GetRange, and GetRangeIterator.

## BatchGetRow { .section}

Batch reads several data rows from one or more tables. It is essentially a set of multiple GetRow operations. Each operation is executed, returns results, and consumes capacity units independently.

Compared to the execution of a large number of GetRow operations, the BatchGetRow operation reduces the request response time and increases the data read rate.

**API**

```language-csharp
        /// <summary>
        /// <para>The BatchGetRow operation is essentially a set of multiple GetRow operations. </para>
        /// <para> Each operation is executed, returns results, and consumes capacity units independently. </para>
        /// Compared to the execution of a large number of GetRow operations, the BatchGetRow operation reduces the request response time and increases the data read rate.
        /// </summary>
        /// <param name="request">Request instance</param>
        /// <returns>Response instance</returns>
        public BatchGetRowResponse BatchGetRow(BatchGetRowRequest request);

        /// <summary>
        /// Asynchronous form of BatchGetRow.
        /// </summary>
        public Task<BatchGetRowResponse> BatchGetRowAsync(BatchGetRowRequest request);

```

**Example**

Batch read 10 data rows.

```language-csharp
        // Construct a batch-read request object, including the primary key values of the 10 rows
        List<PrimaryKey> primaryKeys = new List<PrimaryKey>();
        for (int i = 0; i < 10; i++)
        {
            PrimaryKey primaryKey = new PrimaryKey();
            primaryKey.Add("pk0", new ColumnValue(i));
            primaryKey.Add("pk1", new ColumnValue("abc"));
            primaryKeys.Add(primaryKey);
        }

        try
        {
            BatchGetRowRequest request = new BatchGetRowRequest();
            request.Add(TableName, primaryKeys);

            // Call BatchGetRow to read 10 data rows
            var response = otsClient.BatchGetRow(request);
            var tableRows = response.RowDataGroupByTable;
            var rows = tableRows[TableName];

            // Input the data of the rows. The input data is omitted here. For more information, see the GitHub link.

            // BatchGetRow may partially fail. Therefore, the status of each row must be checked. For more information, see the GitHub link.
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Batch get row failed, exception:{0}", ex.Message);
        }

```

**Note:** BatchGetRow supports filter using conditional statements.

Code details can be found at [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).

## BatchWriteRow { .section}

BatchWriteRow inserts, modifies, or deletes several data rows in one or more tables. It is a set of multiple PutRow, UpdateRow, and DeleteRow operations. Each operation is executed, returns results independently, and consumes capacity units independently.

**API**

```language-csharp
        /// <summary>
        /// <para>Batch inserts, modifies, or deletes several data rows in one or more tables. </para>
        /// <para>The BatchWriteRow operation is essentially a set of multiple PutRow, UpdateRow, and DeleteRow operations. Each operation is executed, returns results, and consumes capacity units independently. </para>
        /// <para>Compared to the execution of a large number of write operations, the BatchWriteRow operation reduces the request response time and increases the data write rate. </para>
        /// </summary>
        /// <param name="request">Request instance</param>
        /// <returns>Response instance</returns>
        public BatchWriteRowResponse BatchWriteRow(BatchWriteRowRequest request);

        /// <summary>
        /// Asynchronous row of BatchWriteRow
        /// </summary>
        /// <param name="request"></param>
        /// <returns></returns>
        public Task<BatchWriteRowResponse> BatchWriteRowAsync(BatchWriteRowRequest request);

```

**Example**

Batch import 100 data rows.

```language-csharp
        // Construct a batch-import request object, including the primary key values of the 100 rows
        var request = new BatchWriteRowRequest();
        var rowChanges = new RowChanges();
        for (int i = 0; i < 100; i++)
        {
            PrimaryKey primaryKey = new PrimaryKey();
            primaryKey.Add("pk0", new ColumnValue(i));
            primaryKey.Add("pk1", new ColumnValue("abc"));

            // Define the attribute columns of the 100 rows
            UpdateOfAttribute attribute = new UpdateOfAttribute();
            attribute.AddAttributeColumnToPut("col0", new ColumnValue(0));
            attribute.AddAttributeColumnToPut("col1", new ColumnValue("a"));
            attribute.AddAttributeColumnToPut("col2", new ColumnValue(true));

            rowChanges.AddUpdate(new Condition(RowExistenceExpectation.IGNORE), primaryKey, attribute);
        }

        request.Add(TableName, rowChanges);

        try
        {
            // Call BatchWriteRow
            var response = otsClient.BatchWriteRow(request);
            var tableRows = response.TableRespones;
            var rows = tableRows[TableName];

            // Partial operations of BatchGetRow may fail; therefore, the status of each row must be checked. For more information, see the GitHub link.
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Batch put row failed, exception:{0}", ex.Message);
        }

```

**Note:** BatchWriteRow supports filter using conditional statements.

Code details can be found at [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).

## GetRange { .section}

Reads data in the specified primary key range.

**API**

```language-csharp
        /// <summary>
        /// Get data from multiple rows in the specified range.
        /// </summary>
        /// <param name="request">Request instance</param>
        /// <returns>Response instance</returns>
        public GetRangeResponse GetRange(GetRangeRequest request);

        /// <summary>
        /// Asynchronous form of GetRange.
        /// </summary>
        /// <param name="request"></param>
        /// <returns></returns>
        public Task<GetRangeResponse> GetRangeAsync(GetRangeRequest request);	

```

**Example**

Read data in the specified range.

```language-csharp
        // Read all rows in the range from (0, INF_MIN) to (100, INF_MAX)
        var inclusiveStartPrimaryKey = new PrimaryKey();
        inclusiveStartPrimaryKey.Add("pk0", new ColumnValue(0));
        inclusiveStartPrimaryKey.Add("pk1", ColumnValue.INF_MIN);

        var exclusiveEndPrimaryKey = new PrimaryKey();
        exclusiveEndPrimaryKey.Add("pk0", new ColumnValue(100));
        exclusiveEndPrimaryKey.Add("pk1", ColumnValue.INF_MAX);

        try
        {
            // Constructs a read request object in the specified range
            var request = new GetRangeRequest(TableName, GetRangeDirection.Forward,
                            inclusiveStartPrimaryKey, exclusiveEndPrimaryKey);

            var response = otsClient.GetRange(request);

            // Continue the read operation if only partial data is returned
            var rows = response.RowDataList;
            var nextStartPrimaryKey = response.NextPrimaryKey;
            while (nextStartPrimaryKey ! = null)
            {
                request = new GetRangeRequest(TableName, GetRangeDirection.Forward,
                                nextStartPrimaryKey, exclusiveEndPrimaryKey);
                response = otsClient.GetRange(request);
                nextStartPrimaryKey = response.NextPrimaryKey;
                foreach (RowDataFromGetRange row in response.RowDataList)
                {
                    rows.Add(row);
                }
            }

            // Output the data of the rows. The output data is omitted here. For more information, see the GitHub link.

            // Execution is successful if no exception is thrown.
            Console.WriteLine("Get range succeeded");
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Get range failed, exception:{0}", ex.Message);
        }

```

**Note:** GetRange supports filter using conditional statements.

Code details can be found at [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).

## GetRangeIterator { .section}

Gets the iterator in the specified range.

**API**

```language-csharp
        /// <summary>
        /// Get data from multiple rows in the specified range. The system returns the iterator used to iterate through each data row.
        /// </summary>
        /// <param name="request"><see cref="GetIteratorRequest"/></param>
        /// <returns>Return the <see cref="RowDataFromGetRange"/> iterator. </returns>
        public IEnumerable<RowDataFromGetRange> GetRangeIterator(GetIteratorRequest request);

```

**Example**

Get an iterator.

```language-csharp
        // Read all rows in the range from (0, "a") to (1000, "xyz")
        PrimaryKey inclusiveStartPrimaryKey = new PrimaryKey();
        inclusiveStartPrimaryKey.Add("pk0", new ColumnValue(0));
        inclusiveStartPrimaryKey.Add("pk1", new ColumnValue("a"));

        PrimaryKey exclusiveEndPrimaryKey = new PrimaryKey();
        exclusiveEndPrimaryKey.Add("pk0", new ColumnValue(1000));
        exclusiveEndPrimaryKey.Add("pk1", new ColumnValue("xyz"));

        // Construct a CapacityUnit to record the CUs consumed by iteration
        var cu = new CapacityUnit(0, 0);

        try
        {
            // Construct a GetIteratorRequest. Filter criteria are supported.
            var request = new GetIteratorRequest(TableName, GetRangeDirection.Forward, inclusiveStartPrimaryKey,
                                                exclusiveEndPrimaryKey, cu);

            var iterator = otsClient.GetRangeIterator(request);
            // Iterator that reads data in a traversal method
            foreach (var row in iterator)
            {
                // Processing logic
            }

            Console.WriteLine("Iterate row succeeded");
        } 
        catch (Exception ex)
        {
            Console.WriteLine("Iterate row failed, exception:{0}", ex.Message);
        }

```

**Note:** GetRangeIterator supports filter using conditional statements.

Code details can be found at [GetRangeIterator@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/MultiRowReadWriteSample.cs).

