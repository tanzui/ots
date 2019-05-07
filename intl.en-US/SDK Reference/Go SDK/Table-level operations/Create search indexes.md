# Create search indexes {#concept_xfn_zn5_hgb .concept}

You can call the CreateSearchIndex operation to create a search index for a table. You can create multiple search indexes for a table. When you create a search index, you must specify the name of the table as well as the name and schema of the search index.

## Parameter description {#section_bs4_zgy_ngb .section}

-   TableName: the name of the table for which you want to create a search index.
-   IndexName: the name of the search index.
-   Schema: specifies the index schema and its configurations.
    -   IndexSetting: optional. RoutingFields: an advanced feature. You can set this parameter to customize routing fields. You can specify some primary key columns as routing fields. Table Store distributes data that is written to a search index to different partitions based on the specified routing fields.
    -   IndexSort: optional. Index sorting is implemented based on the primary key column by default. You can also specify other columns for index sorting.
    -   FieldSchemas: the list of field schemas. The following table describes the parameters you can set for each field schema.

        |Name|Required|Description|
        |:---|:-------|:----------|
        |FieldName|Yes|         -   This parameter specifies the name of the field that is used for index sorting.
        -   The field can be a primary key column or an attribute key column.
        -   This parameter value is of the String type.
 |
        |FieldType|Yes|The field type that is used for index sorting. For more information, see the "Supported data types \(FieldType\)" section in SearchIndex.|
        |Index|No|         -   This parameter specifies whether to enable the search index.
        -   This parameter value is of the Boolean type.
 |
        |IndexOptions|No|The configuration item for the search index.|
        |Analyzer|No|This parameter specifies a tokenizer.|
        |EnableSortAndAgg|No|         -   This parameter specifies whether to enable index sorting and statistics collection.
        -   This parameter value is of the Boolean type.
 |
        |Store|No|         -   This parameter specifies whether to store the field value in the search index schema.
        -   This parameter value is of the Boolean type.
        -   If the parameter value is true, you can read data directly from the search index schema without the need to query the table. This configuration is used to optimize query performance.
 |
        |Array|No|         -   This parameter value is of the Boolean type.
        -   The Boolean value specifies whether the data type is Array.
        -   If this parameter value is true, the data in this column is an array. When you write data to this column, ensure that the data is a JSON array, such as \["a","b","c"\]. The Nested field value is an array. You do not need to set the Array attribute for the field value.
 |


## Examples {#section_o1y_zgy_ngb .section}

```
/**
 **Create a search index that contains Col_Keyword and Col_Long. Set the type of data in Col_Keyword to Keyword. Set the type of data in Col_Long to Long. 
 */
func CreateSearchIndex(client *tablestore.TableStoreClient, tableName string, indexName string) {
	request := &tablestore.CreateSearchIndexRequest{}
	request.TableName = tableName // Set the table name.
	request.IndexName = indexName // Set the search index name.

	schemas := []*tablestore.FieldSchema{}
	field1 := &tablestore.FieldSchema{
		FieldName: proto.String("Col_Keyword"), // Set the field name by calling the proto.String method. This method is used to request a string pointer.
		FieldType: tablestore.FieldType_KEYWORD, // Set the field type.
		Index:     proto.Bool(true), // Set Index to true.
		EnableSortAndAgg: proto.Bool(true), // Set EnableSortAndAgg to true to enable index sorting and statistics collection.
	}
	field2 := &tablestore.FieldSchema{
		FieldName: proto.String("Col_Long"),
		FieldType: tablestore.FieldType_LONG,
		Index:     proto.Bool(true),
		EnableSortAndAgg: proto.Bool(true),
	}
	schemas = append(schemas, field1, field2)

	request.IndexSchema = &tablestore.IndexSchema{
		FieldSchemas: schemas, // Set the fields that are contained in the search index.
	}
	resp, err := client.CreateSearchIndex(request) // Call a client to create the search index.
	if err ! = nil {
		fmt.Println("error :", err)
		return
	}
	fmt.Println("CreateSearchIndex finished, requestId:", resp.ResponseInfo.RequestId)
}
```

