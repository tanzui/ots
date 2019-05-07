# Create search indexes {#concept_udr_gvk_2gb .concept}

## CreateSearchIndex {#section_fsk_3vk_2gb .section}

Before you use a search index to query a table, you must create a search index for the table. When you create the search index, you must specify the name of the table as well as the name and schema of the search index.

-   TableName: the name of the table for which you want to create a search index.
-   IndexName: the name of the search index.
-   Schema: supports FieldSchemas, IndexSetting, and IndexSort parameters.
    -   FieldSchemas: the list of field schemas. You can specify the following parameters for each field schema:
        -   FieldName: required. This parameter specifies the name of the field that is used for index sorting. The field can be a primary key column or an attribute key column. The attribute can be a primary key column or an attribute column. This parameter value is of the String type.
        -   FieldType: required. This parameter specifies The field type that is used for index sorting. For more information, see the "Supported data types \(FieldType\)" section in SearchIndex.
        -   IsArray: optional. This parameter value is of the Boolean type. The Boolean value specifies whether the data type is Array. If this parameter value is true, the data in this column is an array. When you write data to this column, ensure that the data is a JSON array, such as \["a","b","c"\]. The Nested field value is an array. You do not need to set the Array attribute for the field value.
        -   Index: optional. This parameter specifies whether to enable the search index. This parameter value is of the Boolean type.
        -   IndexOptions: optional. This parameter specifies the configuration items for the search index.
        -   Analyzer: optional. This parameter specifies a tokenizer.
        -   EnableSortAndAgg: optional. This parameter specifies whether to enable index sorting and statistics collection. This parameter value is of the Boolean type.
        -   Store: optional. This parameter specifies whether to store the field value in the search index schema. This parameter value is of the Boolean type. If the parameter value is true, you can read data directly from the search index schema without the need to query the table. This configuration is used to optimize query performance.
    -   IndexSetting: optional
        -   RoutingFields: an advanced feature. You can set this parameter to customize routing fields. You can specify some primary key columns as routing fields. Table Store distributes data that is written to a search index to different partitions based on the specified routing fields.
    -   IndexSort: optional. Index sorting is implemented based on the order of primary key columns by default.
        -   Sorters: the sorting method that is suitable for the sorting of indexes. You can set PrimaryKeySort or FieldSort for this parameter.
            -   PrimaryKeySort:
                -   Order: The default value is TableStore.SortOrder.SORT\_ORDER\_ASC, which indicates that data is sorted in ascending order.
            -   FieldSort: You can customize fields for index sorting.
                -   FieldName: Set the fields for index sorting.
                -   Order: The default value is TableStore.SortOrder.SORT\_ORDER\_ASC, which indicates that data is sorted in ascending order.

## Examples {#section_jzr_kvk_2gb .section}

```
/**
 *Create a search index that contains Col_Keyword, Col_Long, Col_Text, and Col_Nested.
 * Set the data types in these columns to Keyword, Long, Text, and Nested, respectively.
 */
client.createSearchIndex({
    tableName: TABLE_NAME, // Set the table name.
    indexName: INDEX_NAME, // Set the search index name.
    schema: {
        fieldSchemas: [
            {
                fieldName: "Col_Keyword",
                fieldType: TableStore.FieldType.KEYWORD, // Set the name and type of the field.
                index: true, // Set Index to true.
                .setEnableSortAndAgg(true), // You can set EnableSortAndAgg to true to enable index sorting and statistics collection.
                store: false,
                isAnArray: false
            },
            {
                fieldName: "Col_Long",
                fieldType: TableStore.FieldType.LONG,
                index: true,
                enableSortAndAgg: true,
                store: true,
                isAnArray: false
            },
            {
                fieldName: "Col_Text",
                fieldType: TableStore.FieldType.TEXT,
                index: true,
                enableSortAndAgg: false,
                store: true,
                isAnArray: false,
            },
            // {
            //     fieldName: "Col_Nested",
            //     fieldType: TableStore.FieldType.NESTED,
            //     index: false,
            //     enableSortAndAgg: false,
            //     store: false,
            //     fieldSchemas: [ // Set schemas for Nested fields.
            //         {
            //             fieldName: "Sub_Col_KeyWord",
            //             fieldType: TableStore.FieldType.KEYWORD,
            //             index: true,
            //             enableSortAndAgg: true,
            //             store: false,
            //         },
            //         {
            //             fieldName: "Sub_Col_Long",
            //             fieldType: TableStore.FieldType.LONG,
            //             index: true,
            //             enableSortAndAgg: true,
            //             store: false,
            //         }
            //     ]
            // }
        ],
        indexSetting: { //Configure the search index.
            "routingFields": ["Pk_Keyword"], //Only primary key columns can be set as routing fields.
            "routingPartitionSize": null
        },
        indexSort: {//Queries that contains Nested fields do not support index sorting.
            sorters: [
                // { //If indexSort is not specified, the default value is PrimaryKeySort. Data is sorted in ascending order by default.
                //     primaryKeySort: {
                //         order: TableStore.SortOrder.SORT_ORDER_ASC
                //     }
                // },
                {
                    fieldSort: {
                        fieldName: "Col_Keyword",
                        order: TableStore.SortOrder.SORT_ORDER_DESC //Set the index sorting order.
                    }
                }
            ]
        }
    }
}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', JSON.stringify(data, null, 2));
});
```

