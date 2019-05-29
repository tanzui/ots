# ListTable {#concept_sbc_1jg_fgb .concept}

You can call this operation to obtain the names of all tables created in the current instance.

**Operation**

```language-csharp
 /// <summary>
 /// Obtain the names of all tables created in the current instance.
 /// </summary>
 /// <param name="request">Request parameter.</param>
 /// <returns>Information returned by ListTable and used to obtain the table name list.</returns> </returns>
 public ListTableResponse ListTable(ListTableRequest request);

 /// <summary> 
 /// Asynchronous form of ListTable.
 /// </summary>
 public Task<ListTableResponse> ListTableAsync(ListTableRequest request); 

```

**Example**

The following code provides an example of how to obtain the names of all tables in an instance:

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

