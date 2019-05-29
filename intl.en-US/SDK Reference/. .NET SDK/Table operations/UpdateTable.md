# UpdateTable {#concept_icn_cjg_fgb .concept}

You can call this operation to update the reserved read/write throughput of the specified table.

**Operation**

```language-csharp
 /// <summary>
 /// Update the reserved read/write throughput of the specified table. The new setting takes effect within 1 minute after the throughput is updated.
 /// </summary>
 /// <param name="request">Request parameter, including the table name and reserved read/write throughput.</param>
 /// <returns>Contain the updated reserved read/write throughput and other information.</returns>
 public UpdateTableResponse UpdateTable(UpdateTableRequest request);

 /// <summary>
 /// Asynchronous form of UpdateTable.
 /// </summary>
 public Task<UpdateTableResponse> UpdateTableAsync(UpdateTableRequest request);

```

**Example**

The following code provides an example of how to update the read CU and write CU of a table to 1 and 2, respectively:

```language-csharp
 // Update the reserved read throughput to 1 and the reserved write throughput to 2.
 var reservedThroughput = new CapacityUnit(1, 2);

 // Construct the UpdateTableRequest object.
 var request = new UpdateTableRequest("SampleTable", reservedThroughput);
 try
 {
     // Call this operation to update the reserved read/write throughput of the table.
     otsClient.UpdateTable(request);

     // If no exception is thrown, the execution is successful.
     Console.Writeline("Update table succeeded.");
 }
 catch (Exception ex)
 {
     // If task execution fails, an exception is thrown and an error message is displayed.
     Console.WriteLine("Update table failed, exception:{0}", ex.Message);
 }

```

For more information about the full sample code, see [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

