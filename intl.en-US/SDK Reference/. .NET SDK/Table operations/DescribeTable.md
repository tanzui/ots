# DescribeTable {#concept_tkk_gjg_fgb .concept}

You can call this operation to query the schema and reserved read/write throughput of the specified table.

**Operation**

```language-csharp
 /// <summary>
 /// Query the schema and reserved read/write throughput of the specified table.
 /// </summary>
 /// <param name="request">Request parameter, including the table name.</param>
 /// <returns>Contain the schema and reserved read/write throughput of the specified table.</returns> </returns>
 public DescribeTableResponse DescribeTable(DescribeTableRequest request);

 /// <summary>
 /// Asynchronous form of DescribeTable.
 /// </summary>
 public Task<DescribeTableResponse> DescribeTableAsync(DescribeTableRequest request);

```

**Example**

The following code provides an example of how to obtain the descriptive information of a table:

```language-csharp
 try
 {
     var request = new DescribeTableRequest("SampleTable");
     var response = otsClient.DescribeTable(request);

     // Display the descriptive information of the specified table.
     Console.Writeline("Describe table succeeded.") ;
     Console.WriteLine("LastIncreaseTime: {0}", response.ReservedThroughputDetails.LastIncreaseTime);
     Console.WriteLine("LastDecreaseTime: {0}", response.ReservedThroughputDetails.LastDecreaseTime);
     Console.WriteLine("NumberOfDecreaseToday: {0}", response.ReservedThroughputDetails.LastIncreaseTime);
     Console.WriteLine("ReadCapacity: {0}", response.ReservedThroughputDetails.CapacityUnit.Read);
     Console.WriteLine("WriteCapacity: {0}", response.ReservedThroughputDetails.CapacityUnit.Write);
 }
 catch (Exception ex)
 {
     // If task execution fails, an exception is thrown and an error message is displayed.
     Console.WriteLine("Describe table failed, exception:{0}", ex.Message);
        }

```

For more information about the full sample code, see [DescribeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

