# DescribeSearchIndex {#concept_qhr_1qc_fgb .concept}

## DescribeSearchIndex {#section_tlw_yqc_fgb .section}

You can call this operation to obtain the details of the Searchindex structure of a table, such as the description of attributes and index configurations. The sample code is as follows:

```
/// <summary>
/// Obtain the details of the Searchindex structure.
/// </summary>
/// <param name="otsClient"></param>
public static void DescribeSearchIndex(OTSClient otsClient)
{
    // Set the table name and index name.
    DescribeSearchIndexRequest request = new DescribeSearchIndexRequest(TableName, IndexName);

    DescribeSearchIndexResponse response = otsClient.DescribeSearchIndex(request);
    string serializedObjectString = JsonConvert.SerializeObject(response);
    Console.WriteLine(serializedObjectString); // Display the details of the response.
}
```

