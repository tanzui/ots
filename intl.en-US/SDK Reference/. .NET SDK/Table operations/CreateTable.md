# CreateTable {#concept_zlc_y3g_fgb .concept}

You can call this operation to create a table based on the given table schema.

When creating a table in Table Store, you must specify the primary key for the table. A primary key contains one to four primary key columns. Each primary key column has a name and a type.

**Operation**

```language-csharp
/// <summary>
 /// Create a table based on the given table information, including the table name, primary key, and reserved read/write throughput.
 /// </summary>
 /// <param name="request">Request parameter.</param>
 /// <returns>Information returned by CreateTable. In this example, the returned result is null and contains no specific information.
 /// </returns>
        public CreateTableResponse CreateTable(CreateTableRequest request);

        /// <summary>
        /// Asynchronous form of CreateTable.
        /// </summary>
        public Task<CreateTableResponse> CreateTableAsync(CreateTableRequest request); 

```

**Note:** After a table is created in Table Store, it takes several seconds to fully load the table. Do not perform any operations during this period.

**Example**

The following code provides an example of how to create a table with two primary key columns. Set the reserved read/write throughput to 0.

```language-csharp
 // Create a schema for the primary key columns, including the number, names, and types of the primary key columns
 // The first primary key column (partition column) is named PK0 and belongs to the integer type.
 // The second primary key column is named PK1 and belongs to the string type.
 var primaryKeySchema = new PrimaryKeySchema();
 primaryKeySchema.Add("pk0", ColumnValueType.Integer);
 primaryKeySchema.Add("pk1", ColumnValueType.String);

 // Create a tableMeta based on the table name and the schema for the primary key columns.
 var tableMeta = new TableMeta("SampleTable", primaryKeySchema);

 // Set the reserved read/write throughput to 0.
 var reservedThroughput = new CapacityUnit(0, 0);

 try
 {
     // Construct the CreateTableRequest object.
     var request = new CreateTableRequest(tableMeta, reservedThroughput);

     // Call the CreateTable operation of the OTSClient. If no exception is thrown, the table is created. Otherwise, the table fails to be created.
     otsClient.CreateTable(request);

     Console.WriteLine("Create table succeeded.") ;
 }
 // Handle exceptions.
 catch (Exception ex)
 {
     Console.WriteLine("Create table failed, exception:{0}", ex.Message);
 }

```

For more information about the full sample code, see [CreateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

