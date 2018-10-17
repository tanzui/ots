# Single-row operations {#reference11065 .reference}

The Table Store SDK provides the following single-row operation interfaces: PutRow, GetRow, UpdateRow, and DeleteRow.

## PutRow { .section}

Inserts data into the specified row.

**API**

```language-csharp
        /// <summary>
        /// Write a data row based on the specified table name, primary keys, and attributes. CapacityUnit consumed by the operation is returned.
        /// </summary>
        /// <param name="request">Data insertion request</param>
        /// <returns>CapacityUnit consumed by the operation</returns>
        public PutRowResponse PutRow(PutRowRequest request);

        /// <summary>
        /// Asynchronous form of PutRow
        /// </summary>
        public Task<PutRowResponse> PutRowAsync(PutRowRequest request);

```

**Example 1**

Insert a data row.

```language-csharp
        // Define the primary keys of the row, which must be consistent with the primary keys defined in TableMeta during table creation
        var primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(0));
        primaryKey.Add("pk1", new ColumnValue("abc"));

        // Define the attribute columns of the 100 rows
        var attribute = new AttributeColumns();
        attribute.Add("col0", new ColumnValue(0));
        attribute.Add("col1", new ColumnValue("a"));
        attribute.Add("col2", new ColumnValue(true));

        try
        {
            // Construct the request object for data insertion, with RowExistenceExpectation.IGNORE indicating that data is still inserted even when the specified row does not exist
            var request = new PutRowRequest("SampleTable", new Condition(RowExistenceExpectation.IGNORE),
                                    primaryKey, attribute);

            // Call PutRow to insert data
            otsClient.PutRow(request);

            // Execution is successful if no exception is thrown.
            Console.WriteLine("Put row succeeded.");
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Put row failed, exception:{0}", ex.Message);
        }

```

**Note:** 

-   Condition.IGNORE, Condition.EXPECT\_EXIST, and Condition.EXPECT\_NOT\_EXIST are deprecated starting from v3.0.0. Use new Condition \(RowExistenceExpectation.IGNORE\), new Condition \(RowExistenceExpectation.EXPECT\_EXIST\), and new Condition \(RowExistenceExpectation.EXPECT\_NOT\_EXIST\) instead.

-   RowExistenceExpectation.IGNORE indicates that new data is still inserted even when the specified row does not exist. If the inserted data is the same as the existing data, the existing data is overwritten.

-   RowExistenceExpectation.EXPECT\_EXIST indicates that new data is inserted only when the specified row exists. The existing data is overwritten.

-   RowExistenceExpectation.EXPECT\_NOT\_EXIST indicates that data is inserted only when the specified row does not exist.

-   In addition to row conditions, the Condition parameter also supports column conditions starting from v2.2.0.


Code details could be obtained at [PutRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/SingleRowReadWriteSample.cs).

**Example 2**

Insert a data row based on specified conditions.

The following code example inserts data only when the specified row exists and the value of col1 is greater than 24.

```language-csharp
        // Define the primary keys of the row, which must be consistent with the primary keys defined in TableMeta during table creation
        var primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(0));
        primaryKey.Add("pk1", new ColumnValue("abc"));

        // Define the attribute columns of the 100 rows
        AttributeColumns attribute = new AttributeColumns();
        attribute.Add("col0", new ColumnValue(0));
        attribute.Add("col1", new ColumnValue("a"));
        attribute.Add("col2", new ColumnValue(true));

        var request = new PutRowRequest(tableName, new Condition(RowExistenceExpectation.EXPECT_EXIST),
                                    primaryKey, attribute);

        // When the value of col0 is greater than 24, PutRow is executed again to overwrite the original value.
        try
        {
            request.Condition.ColumnCondition = new RelationalCondition("col0",
                                                RelationalCondition.CompareOperator.GREATER_THAN,
                                                new ColumnValue(24));
            otsClient.PutRow(request);

            Console.WriteLine("Put row succeeded.");
        }
        catch (Exception ex)
        {
            Console.WriteLine("Put row failed. error:{0}", ex.Message);
        }

```

**Note:** 

-   You can set a single condition or a combination of conditions. For example, you can set two conditions for data insertion as col1 \> 5 and pk2 < ‘xyz’.

-   Attribute columns and primary key columns support the Condition parameter.

-   When the column specified in a condition does not exist in a row, PassIfMissing in RelationCondition controls the action to be taken. The default value is true.


Code details could be obtained at [ConditionPutRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/ConditionUpdateSample.cs).

**Example 3**

Insert a data row asynchronously.

```language-csharp
        try
        {
            var putRowTaskList = new List<Task<PutRowResponse>>();
            for (int i = 0; i < 100; i++)
            {
                // Define the primary keys of the row, which must be consistent with the primary keys defined in TableMeta during table creation
                var primaryKey = new PrimaryKey();
                primaryKey.Add("pk0", new ColumnValue(i));
                primaryKey.Add("pk1", new ColumnValue("abc"));

                // Define the attribute columns of the 100 rows
                var attribute = new AttributeColumns();
                attribute.Add("col0", new ColumnValue(i));
                attribute.Add("col1", new ColumnValue("a"));
                attribute.Add("col2", new ColumnValue(true));

                var request = new PutRowRequest(TableName, new Condition(RowExistenceExpectation.IGNORE),
                                                primaryKey, attribute);

                putRowTaskList.Add(TabeStoreClient.PutRowAsync(request));
            }

            // Wait until each asynchronous call returns results and print the consumed CU
            foreach (var task in putRowTaskList)
            {
                task.Wait();
                Console.WriteLine("consumed read:{0}, write:{1}", task.Result.ConsumedCapacityUnit.Read,
                                    task.Result.ConsumedCapacityUnit.Write);
            }

            // Data is inserted successfully if no exception is thrown.
            Console.WriteLine("Put row async succeeded.");
        }
        catch (Exception ex)
        {
            // An error message is printed if an exception is thrown.
            Console.WriteLine("Put row async failed. exception:{0}", ex.Message);
        }

```

**Note:** Each asynchronous call starts a thread. A timeout error may occur if too many asynchronous calls are started consecutively and each call consumes an extended period of time.

Code details can be obtained at [PutRowAsync@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/SingleRowReadWriteSample.cs).

## GetRow { .section}

Reads a single data row based on a given primary key.

**API**

```language-csharp
        /// <summary>
        /// Read a single data row based on a given primary key.
        /// </summary>
        /// <param name="request">Data query request</param>
        /// <returns>Response returned by GetRow</returns>
        public GetRowResponse GetRow(GetRowRequest request);

        /// <summary>
        /// Asynchronous form of GetRow
        /// </summary>
        public Task<GetRowResponse> GetRowAsync(GetRowRequest request);

```

**Example 1**

Read a data row.

```language-csharp
        // Define the primary keys of the row, which must be consistent with the primary keys defined in TableMeta during table creation
        PrimaryKey primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(0));
        primaryKey.Add("pk1", new ColumnValue("abc"));

        try
        {
            // Construct a query request object. The entire row is read if no column is specified.
            var request = new GetRowRequest(TableName, primaryKey);

            // Call the GetRow interface to query data
            var response = otsClient.GetRow(request);

            // Output the data of the row. The output data is omitted here. For more information, see the GitHub link.

            // Data is read successfully if no exception is thrown.
            Console.WriteLine("Get row succeeded.");
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Update table failed, exception:{0}", ex.Message);
        }

```

**Note:** 

-   If you query a data row, the system returns the data in all columns of the row. You can use the columnsToGet parameter to read the data in specified columns. For example, the system only returns the data in col0 and col1 if col0 and col1 are inserted into columnsToGet.

-   Conditional filter is supported. For example, you can configure the system to return results only when the value of col0 is greater than 24.

-   When the columnsToGet and Condition parameters are both used, the system returns results first based on columnsToGet and then filters the returned columns based on the Condition parameter.

-   When a specified column does not exist, PassIfMissing determines the next action.


Code details could be obtained at [GetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/SingleRowReadWriteSample.cs).

**Example 2**

Read a data row using the filter feature.

The following code example queries data and configures the system to return only the data in col0 and col1 and filter the data in col0 based on the condition that the value of col0 is 24.

```language-csharp
        // Define the primary keys of the row, which must be consistent with the primary keys defined in TableMeta during table creation
        PrimaryKey primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(0));
        primaryKey.Add("pk1", new ColumnValue("abc"));

        var rowQueryCriteria = new SingleRowQueryCriteria("SampleTable");
        rowQueryCriteria.RowPrimaryKey = primaryKey;

        // Condition 1: The value of col0 is 5.
        var filter1 = new RelationalCondition("col0",
                    RelationalCondition.CompareOperator.EQUAL,
                    new ColumnValue(5));

        // Condition 2: The value of col1 is not ff.
        var filter2 = new RelationalCondition("col1", RelationalCondition.CompareOperator.NOT_EQUAL, new ColumnValue("ff"));

        // Construct a combination of Condition 1 and Condition 2 with the OR relationship
        var filter = new CompositeCondition(CompositeCondition.LogicOperator.OR);
        filter.AddCondition(filter1);
        filter.AddCondition(filter2);

        rowQueryCriteria.Filter = filter;

        // Set the row you want to query based on the condition [col0,col1], and filter the queried data based on the specified condition
        rowQueryCriteria.AddColumnsToGet("col0");
        rowQueryCriteria.AddColumnsToGet("col1");

        // Construct GetRowRequest
        var request = new GetRowRequest(rowQueryCriteria);

        try
        {
            // Query data
            var response = otsClient.GetRow(request);

            // Output data or perform the related logical operation (omitted)

            // Execution is successful if no exception is thrown.
            Console.WriteLine("Get row with filter succeeded.");
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Get row with filter failed, exception:{0}", ex.Message);
        }

```

Code details could be obtained at [GetRowWithFilter@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/SingleRowReadWriteSample.cs).

## UpdateRow { .section}

Updates the data of the specified row. If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.

**API**

```language-csharp
        /// <summary>
        /// Update the data of the specified row. If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.
        /// </summary>
        /// <param name="request">Request instance</param>
        public UpdateRowResponse UpdateRow(UpdateRowRequest request);

        /// <summary>
        /// Asynchronous form of UpdateRow.
        /// </summary>
        /// <param name="request"></param>
        /// <returns></returns>
        public Task<UpdateRowResponse> UpdateRowAsync(UpdateRowRequest request);

```

**Example**

Update the data of the specified row.

```language-csharp
        // Define the primary keys of the row, which must be consistent with the primary keys defined in TableMeta during table creation
        PrimaryKey primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(0));
        primaryKey.Add("pk1", new ColumnValue("abc"));

        // Define the attribute columns of the 100 rows
        UpdateOfAttribute attribute = new UpdateOfAttribute();
        attribute.AddAttributeColumnToPut("col0", new ColumnValue(0));
        attribute.AddAttributeColumnToPut("col1", new ColumnValue("b")); // Change the original value 'a' to 'b'
        attribute.AddAttributeColumnToPut("col2", new ColumnValue(true));

        try
        {
            // Construct the request object for row updating, with RowExistenceExpectation.IGNORE indicating that data is still updated even when the specified row does not exist
            var request = new UpdateRowRequest(TableName, new Condition(RowExistenceExpectation.IGNORE),
                                    primaryKey, attribute);
            // Call the UpdateRow interface
            otsClient.UpdateRow(request);

            // Execution is successful if no exception is thrown.
            Console.Writeline("Update row succeeded.");
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Update row failed, exception:{0}", ex.Message);
        }

```

**Note:** Row updating supports the use of conditional statements.

Code details can be obtained at [UpdateRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/SingleRowReadWriteSample.cs).

## DeleteRow { .section}

**API**

```language-csharp
        (delete)/// <summary>(delete)
        /// Delete a data row based on the specified table name and primary keys.
        /// </summary>
        /// <param name="request">Request instance</param>
        /// <returns>Response instance</returns>
        public DeleteRowResponse DeleteRow(DeleteRowRequest request);

        /// <summary>
        /// Asynchronous form of DeleteRow.
        /// </summary>
        public Task<DeleteRowResponse> DeleteRowAsync(DeleteRowRequest request);

```

**Example**

Delete a data row.

```language-csharp
        // The values of the primary key columns of the row to be deleted are 0 and "abc".
        var primaryKey = new PrimaryKey();
        primaryKey.Add("pk0", new ColumnValue(0));
        primaryKey.Add("pk1", new ColumnValue("abc"));

        try
        {
            // Construct a request, with Condition.EXPECT_EXIST indicating that the row is deleted only when it exists
            var deleteRowRequest = new DeleteRowRequest("SampleTable", Condition.EXPECT_EXIST, primaryKey);

            // Call the DeleteRow interface to delete the row
            otsClient.DeleteRow(deleteRowRequest);

            // The row is deleted successfully if no exception is thrown.
            Console.Writeline("Delete table succeeded.");
        }
        catch (Exception ex)
        {
            // The row fails to be deleted if an exception is thrown, and an error message is printed.
            Console.WriteLine("Delete table failed, exception:{0}", ex.Message);
        }

```

**Note:** Row deletion supports the use of conditional statements.

Code details can obtained at [DeleteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/SingleRowReadWriteSample.cs).

