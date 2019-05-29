# DeleteTable {#concept_ikd_3jg_fgb .concept}

You can call this operation to delete a specified table from an instance.

**Operation**

```language-csharp
 /// <summary>
 /// Delete the specified table from an instance.
 /// </summary>
 /// <param name="request">Request parameter, including the table name.</param>
 /// <returns>Information returned by DeleteTable. In this example, the returned result is null and contains no specific information.
 /// </returns>
 public DeleteTableResponse DeleteTable(DeleteTableRequest request);

 /// <summary>
 /// Asynchronous form of DeleteTable.
 /// </summary>
 public Task<DeleteTableResponse> DeleteTableAsync(DeleteTableRequest request);

```

**Example**

The following code provides an example of how to delete a table:

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

For more information about the full sample code, see [DeleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk/blob/master/sample/Samples/CreateTableSample.cs).

