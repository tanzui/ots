# ListSearchIndex {#concept_gln_zpc_fgb .concept}

## ListSearchIndex {#section_bfk_kqc_fgb .section}

You can call this operation to retrieve the list of all Searchindex structures created for a table. The sample code is as follows:

```
/// <summary>
/// List Searchindex structures.
/// </summary>
/// <param name="otsClient"></param>
public static void ListSearchIndex(OTSClient otsClient)
{
    // Set the name of the table.
    ListSearchIndexRequest request = new ListSearchIndexRequest(TableName);
    ListSearchIndexResponse response = otsClient.ListSearchIndex(request);
    // Table Store returns all Searchindex structures created for the specified table.
    foreach (var index in response.IndexInfos)
    {
        Console.WriteLine("indexname:" + index.IndexName);
        Console.WriteLine("tablename:" + index.TableName);
    }
}
```

