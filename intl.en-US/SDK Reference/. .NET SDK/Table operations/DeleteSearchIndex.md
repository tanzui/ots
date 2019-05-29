# DeleteSearchIndex {#concept_en4_bqc_fgb .concept}

## DeleteSearchIndex {#section_kmh_krc_fgb .section}

You can call this operation to delete a Searchindex structure. The sample code is as follows:

```
/// <summary>
/// Delete a Searchindex structure.
/// </summary>
/// <param name="otsClient"></param>
public static void DeleteSearchIndex(OTSClient otsClient)
{
    // Set the table name and index name.
    DeleteSearchIndexRequest request = new DeleteSearchIndexRequest(TableName, IndexName);
    // Call the OTSClient to delete the target Searchindex structure.
    DeleteSearchIndexResponse response = otsClient.DeleteSearchIndex(request);
}
```

