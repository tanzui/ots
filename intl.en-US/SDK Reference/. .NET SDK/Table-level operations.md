# Table-level operations {#reference6004 .reference}

The Table Store SDK provides the following table-level operation interfaces: CreateTable, ListTable, DeleteTable, UpdateTable, and DescribeTable.

## CreateTable {#section_dxz_2df_2fb .section}

Creates a table based on the given table structure information.

When creating a table in Table Store, you must specify the table’s primary keys. A primary key contains one to four primary key columns. Each primary key column has a name and a type.

**API**

```language-csharp
        /// <summary>
        /// Create a table based on table information (including the table name, primary key design, and reserved read/write throughput).
        /// </summary>
        /// <param name="request">Request parameter</param>
        /// <returns>Information returned by CreateTable. The returned instance is null, no specific information.
        /// </returns>
        public CreateTableResponse CreateTable(CreateTableRequest request);

        /// <summary>
        /// Asynchronous form of CreateTable.
        /// </summary>
        public Task<CreateTableResponse> CreateTableAsync(CreateTableRequest request);

```

**Note:** After a table is created in Table Store, it takes several seconds to fully load the table. Do not perform any operations during this time.

**Example**

Create a table with two primary key columns and a reserved read/write throughput of \(0,0\).

```language-csharp
        //Create a schema for the primary key columns, including the quantity, names, and types of primary keys
        //The first primary key column (sharding column) is named pk0 and belongs to the integer type.
        //The second primary key column is named pk1 and belongs to the string type.
        var primaryKeySchema = new PrimaryKeySchema();
        primaryKeySchema.Add("pk0", ColumnValueType.Integer);
        primaryKeySchema.Add("pk1", ColumnValueType.String);

        //Create a tableMeta based on the table name and primary key columns
        var tableMeta = new TableMeta("SampleTable", primaryKeySchema);

        // Set the reserved read throughput and reserved write throughput to 0
        var reservedThroughput = new CapacityUnit(0, 0);

        try
        {
            // Construct the CreateTableRequest object
            var request = new CreateTableRequest(tableMeta, reservedThroughput);

            // Call the CreateTable interface of the client. The table is created successfully if no exception is thrown. If an exception is thrown, the table fails to be created.
            otsClient.CreateTable(request);

            Console.WriteLine("Create table succeeded.") ;
        }
        // Handle exceptions
        catch (Exception ex)
        {
            Console.WriteLine("Create table failed, exception:{0}", ex.Message);
        }

```

Code details can be obtained at [CreateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

## ListTable { .section}

Obtains the names of all tables under the current instance.

**API**

```language-csharp
        /// <summary>
        /// Obtain the names of all tables under the current instance.
        /// </summary>
        /// <param name="request">Request parameter</param>
        /// <returns>Information returned by ListTable and used to obtain the table name list.</returns> </returns>
        public ListTableResponse ListTable(ListTableRequest request);

        /// <summary>
        /// Asynchronous form of ListTable.
        /// </summary>
        public Task<ListTableResponse> ListTableAsync(ListTableRequest request);

```

**Example**

Obtain the names of all tables under an instance.

```language-csharp
        var request = new ListTableRequest();
        try
        {
            var response = otsClient.ListTable(request);
            foreach (var tableName in response.TableNames)
            {
                Console.WriteLine("Table name:{0}", tableName);
            }
            Console.Writeline("List table succeeded.");
        }
        catch (Exception ex)
        {
            Console.WriteLine("List table failed, exception:{0}", ex.Message);
        }

```

## UpdateTable { .section}

Updates the reserved read or write throughput value of the specified table.

**API**

```language-csharp
        /// <summary>
        /// Update the reserved read or write throughput value of the specified table. The new value takes effect within 1 minute after the throughput is updated successfully.
        /// </summary>
        /// <param name="request">Request parameter, including the table name and reserved read/write throughput</param>
        /// <returns>Contain the updated reserved read/write throughput and other information</returns>
        public UpdateTableResponse UpdateTable(UpdateTableRequest request);

        /// <summary>
        /// Asynchronous form of UpdateTable.
        /// </summary>
        public Task<UpdateTableResponse> UpdateTableAsync(UpdateTableRequest request);

```

**Example**

Update the read CU and write CU of a table to 1 and 2 respectively.

```language-csharp
        // Update the reserved read throughput to 1 and the reserved write throughput to 2
        var reservedThroughput = new CapacityUnit(1, 2);

        // Construct the UpdateTableRequest object
        var request = new UpdateTableRequest("SampleTable", reservedThroughput);
        try
        {
            // Call the interface to update the reserved read/write throughput of the table
            otsClient.UpdateTable(request);

            // Execution is successful if no exception is thrown.
            Console.Writeline("Update table succeeded.");
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Update table failed, exception:{0}", ex.Message);
        }

```

Code details can be obtained at [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

## DescribeTable { .section}

Queries the structure information and the reserved read/write throughput value of the specified table.

**API**

```language-csharp
        /// <summary>
        /// Query the structure information and the reserved read/write throughput value of the specified table.
        /// </summary>
        /// <param name="request">Request parameter, including the table name</param>
        /// <returns>Contain the structure information and the reserved read/write throughput value of the specified table.</returns> </returns>
        public DescribeTableResponse DescribeTable(DescribeTableRequest request);

        /// <summary>
        /// Asynchronous form of DescribeTable.
        /// </summary>
        public Task<DescribeTableResponse> DescribeTableAsync(DescribeTableRequest request);

```

**Example**

Obtain the descriptive information of a table.

```language-csharp
        try
        {
            var request = new DescribeTableRequest("SampleTable");
            var response = otsClient.DescribeTable(request);

            // Print the descriptive information of the specified table
            Console.Writeline("Describe table succeeded.") ;
            Console.WriteLine("LastIncreaseTime: {0}", response.ReservedThroughputDetails.LastIncreaseTime);
            Console.WriteLine("LastDecreaseTime: {0}", response.ReservedThroughputDetails.LastDecreaseTime);
            Console.WriteLine("NumberOfDecreaseToday: {0}", response.ReservedThroughputDetails.LastIncreaseTime);
            Console.WriteLine("ReadCapacity: {0}", response.ReservedThroughputDetails.CapacityUnit.Read);
            Console.WriteLine("WriteCapacity: {0}", response.ReservedThroughputDetails.CapacityUnit.Write);
        }
        catch (Exception ex)
        {
            // Execution fails if an exception is thrown, and an error message is printed.
            Console.WriteLine("Describe table failed, exception:{0}", ex.Message);
        }

```

Code details can be obtained at [DescribeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

## DeleteTable { .section}

Deletes the specified table under an instance.

**API**

```language-csharp
        /// <summary>
        /// Delete the table that corresponds to the specified table name.
        /// </summary>
        /// <param name="request">Request parameter, including the table name</param>
        /// <returns>Information returned by DeleteTable. The returned instance is null, no specific information.
        /// </returns>
        public DeleteTableResponse DeleteTable(DeleteTableRequest request);

        /// <summary>
        /// Asynchronous form of DeleteTable.
        /// </summary>
        public Task<DeleteTableResponse> DeleteTableAsync(DeleteTableRequest request);

```

**Example**

Delete a table.

```language-csharp
        var request = new DeleteTableRequest("SampleTable");
        try
        {
            otsClient.DeleteTable(request);
            Console.Writeline("Delete table succeeded.");
        }
        catch (Exception ex)
        {
            Console.WriteLine("Delete table failed, exception:{0}", ex.Message);
        }

```

Code details can be obtained at [DeleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

