# CreateSearchIndex {#concept_226914 .concept}

You can call this operation to create a Search Index structure. To use the Search Index feature for a table, you must create a Search Index structure in the table. One table can contain multiple Search Index structures.

## Description {#section_1vv_1ib_eiy .section}

Parameters:

-   TableName: the name of the target table where you want to create a Search Index structure.
-   IndexName: the name of the target Search Index structure.
-   IndexSchema: defines the schema of the target Search Index structure.
    -   IndexSetting
        -   RoutingFields: specifies the routing fields. You can specify some primary key columns as routing fields. Table Store distributes data that is written to a Search Index structure to different partitions based on the specified routing fields.
    -   FieldSchemas
        -   FieldName: the required name of the field of String type. This field name must be a column name in the table.
        -   FieldType: the required type of the field. For more information, see [Overview](intl.en-US/Functions/SearchIndex/API operations/Overview.md#).
        -   Index: specifies whether to create an index for the field. This is an optional parameter of Boolean type. Default value: true.
        -   IndexOptions: specifies whether to store terms such as position and offset to an inverted chain. You can use the default value of the optional parameter.
        -   Analyzer: the type of the tokenizer. This is an optional parameter. Valid values: single\_word and max\_word.
        -   EnableSortAndAgg: specifies whether to enable sorting and aggregation. This is an optional parameter of Boolean type. Default value: true.
        -   Store: specifies whether Table Store stores original values in the index to accelerate queries. This is an optional parameter of Boolean type. Default value: true.

## Example {#section_sjo_d74_2xu .section}

``` {#codeblock_sd6_25b_60d}
/**
 *Create a Search Index structure that contains the Col_Keyword and Col_Long columns. Set the type of data in Col_Keyword to KEYWORD. Set the type of data in Col_Long to LONG.
 */
private static void createSearchIndex(SyncClient client) {
    CreateSearchIndexRequest request = new CreateSearchIndexRequest();
    request.setTableName(TABLE_NAME); // Set the name of the table.
    request.setIndexName(INDEX_NAME); // Set the name of the index.
    IndexSchema indexSchema = new IndexSchema();
    indexSchema.setFieldSchemas(Arrays.asList(
            new FieldSchema("Col_Keyword", FieldType.KEYWORD) // Set the field name and field type.
                    .setIndex(true) // Set the parameter to true to enable indexing.
                    .setEnableSortAndAgg(true), // Set the parameter to true to enable sorting and aggregation.
            new FieldSchema("Col_Long", FieldType.LONG)
                    .setIndex(true)
                    .setEnableSortAndAgg(true)));
    request.setIndexSchema(indexSchema);
    client.createSearchIndex(request); // Use client to create a Search Index structure.
}
```

