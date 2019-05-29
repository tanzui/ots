# CreateSearchIndex {#concept_ec1_ypc_fgb .concept}

You can call this operation to create one or more Searchindex structures on a table. You can specify the index name and index structure when creating the Searchindex structure. The parameters for the CreateSearchIndex operation are as follows:

-   TableName: the name of the table on which you want to create a Searchindex structure.
-   IndexName: the name of the Searchindex structure.
-   IndexSchema: defines the index structure and attributes of an index.
    -   IndexSetting: includes the RoutingFields parameter. You can use this advanced optional feature to customize a routing. You can specify some primary keys as routing fields. Table Store distributes data that is written to an index to different partitions according to the specified routing fields. The data with the same routing field value is distributed to the same partition.
    -   FieldSchemas: the FieldSchema list. You can specify the following parameters for each FieldSchema.
        -   FieldName \(required\): a string that indicates the name of a field for indexing. The field can be a primary key column or an attribute column.
        -   FieldType \(required\): the field type. For more information about this parameter, see API reference of Searchindex.
        -   Index \(optional\): a Boolean value that indicates whether to enable indexing on the target field.
        -   IndexOptions \(optional\): the index setting.
        -   Analyzer \(optional\): the tokenizer setting.
        -   EnableSortAndAgg \(optional\): a Boolean value that indicates whether to enable sorting and statistics.
        -   Store \(optional\): a Boolean value that indicates whether to store the value of the target field to a Searchindex structure. If you set this parameter to true, you can read data directly from the index without querying the primary table. This optimizes query performance.
        -   Array \(optional\): a Boolean value that indicates whether the column is an array. A value of true indicates that the column is an array. Data written to the column must be a JSON array, such as \["a", "b", "c"\]. A NESTED column is an array, and does not require the Array parameter.
    -   IndexSort \(optional\): indicates the presorting method for indexed data. You can sort data by primary key or column. By default, you sort data by primary key.

The sample code is as follows:

```

/// <summary>
/// Create a Searchindex structure that consists of the Keyword_type_col, Long_type_col, and Text_type_col columns. Then, set the data type of these columns to KEYWORD, LONG, and TEXT, respectively.
/// </summary>
/// <param name="otsClient"></param>
public static void CreateSearchIndex(OTSClient otsClient)
{
    // Set the table name and index name.
    CreateSearchIndexRequest request = new CreateSearchIndexRequest(TableName, IndexName);
    List<FieldSchema> FieldSchemas = new List<FieldSchema>() {
        new FieldSchema(Keyword_type_col,FieldType.KEYWORD){ // Set the field name and field type.
            index =true, // Set the parameter to true to enable indexing.
            EnableSortAndAgg =true // Set the parameter to true to enable sorting and statistics.
        },
        new FieldSchema(Long_type_col,FieldType.LONG){ index=true,EnableSortAndAgg=true},
        new FieldSchema(Text_type_col,FieldType.TEXT){ index=true}
    };
    request.IndexSchame = new IndexSchema()
    {
        FieldSchemas = FieldSchemas
    };
    // Call the OTSClient to create a Searchindex structure.
    CreateSearchIndexResponse response = otsClient.CreateSearchIndex(request);
    Console.WriteLine("Searchindex is created: " + IndexName);
}
```

The following code provides an example of how to specify the IndexSort parameter:

```
/// <summary>
/// Create a Searchindex structure that consists of the Keyword_type_col, Long_type_col, and Text_type_col columns. Then, set the data type of these columns to KEYWORD, LONG, and TEXT, respectively.
/// </summary>
/// <param name="otsClient"></param>
public static void CreateSearchIndexWithIndexSort(OTSClient otsClient)
{
    // Set the table name and index name.
    CreateSearchIndexRequest request = new CreateSearchIndexRequest(TableName, IndexName);
    List<FieldSchema> FieldSchemas = new List<FieldSchema>() {
        new FieldSchema(Keyword_type_col,FieldType.KEYWORD){ // Set the field name and field type.
            index =true, // Set the parameter to true to enable indexing.
            EnableSortAndAgg =true // Set the parameter to true to enable sorting and statistics.
        },
        new FieldSchema(Long_type_col,FieldType.LONG){ index=true,EnableSortAndAgg=true},
        new FieldSchema(Text_type_col,FieldType.TEXT){ index=true}
    };
    request.IndexSchame = new IndexSchema()
    {
        FieldSchemas = FieldSchemas,
        // Presort data by the Long_type_col column. You must index the Long_type_col column and enable sorting and statistics.
        IndexSort = new DataModel.Search.Sort.Sort()
        {
            Sorters = new List<DataModel.Search.Sort.ISorter>
            {
                new DataModel.Search.Sort.FieldSort(Long_type_col, DataModel.Search.Sort.SortOrder.ASC)
            }
        }
    };

    CreateSearchIndexResponse response = otsClient.CreateSearchIndex(request);
    Console.WriteLine("Searchindex is created: " + IndexName);
}
```

