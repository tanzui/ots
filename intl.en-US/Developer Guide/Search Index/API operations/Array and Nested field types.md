# Array and Nested field types {#concept_227000 .concept}

Aside from basic field types, such as Long, Double, Boolean, Keyword, Text, and Geopoint, search index also provides two special field types.

One is the Array type. The Array type can be attached to the basic field types. For example, a field of Long type plus an Array type forms an integer array. This field can contain multiple long integers. If any data of a row is matched in the query, the row is returned.

The other is the Nested type, which provides more features than the Array type.

## Array type {#section_9r8_eea_bo4 .section}

Basic Array types, such as:

-   Long Array: an array of long integers. Format: "\[1000, 4, 5555\]."
-   Boolean Array: an array of Boolean values. Format: "\[true, false\]."
-   Double Array: an array of floating-point numbers. Format: "\[3.1415926, 0.99\]."
-   Keyword Array: an array of strings.
-   Text Array: an array of text. This type is not common.
-   GeoPoint Array: an array of geographical locations. Format: "\[34.2, 43.0\], \[21.4, 45.2\]."

The Array type is only supported in search index. Therefore, when the type of an index field involves Array, the field in the table must be of the String type. The basic data type in the search index remains, such as Long or Double. For example, when a price field is of the Double Array type, the field must be of the String type in the table, and of the Double type in the search index, with isArray set to true.

## Nested type {#section_1tx_8xb_zcs .section}

A Nested column contains nested documents. One document or one row can contain multiple child documents, and these child documents are saved to the same Nested column. You need to specify the schema of child documents in the Nested column. The structure includes the fields of the child documents and the property of each field. The following example defines the format of a Nested column in Java:

``` {#codeblock_fld_lj7_089}
// Specify the FieldSchema class for the child documents.
List<FieldSchema> subFieldSchemas = new ArrayList<FieldSchema>();
subFieldSchemas.add(new FieldSchema("tagName", FieldType.KEYWORD)
    .setIndex(true).setEnableSortAndAgg(true));
subFieldSchemas.add(new FieldSchema("score", FieldType.DOUBLE)
    .setIndex(true).setEnableSortAndAgg(true));

// Set FieldSchema of the child documents as subfieldSchemas of the Nested column.
FieldSchema nestedFieldSchema = new FieldSchema("tags", FieldType.NESTED)
    .setSubFieldSchemas(subFieldSchemas);
```

This example defines the format of a Nested column named tags. The child documents include two fields: one is a KEYWORD field named tagName and the other is a DOUBLE field named score.

Table Store writes Nested columns as strings in JSON arrays to the table. The following example shows the data format of a Nested column:

``` {#codeblock_v09_hx3_bgs}
[{"tagName":"tag1", "score":0.8}, {"tagName":"tag2", "score":0.2}]
```

This column contains two child documents. Even if a column contains only one child document, you must provide the strings in JSON arrays.

The Nested type has the following limits:

1.  Nested indexes do not support the IndexSort feature. However, IndexSort can improve query performance in many scenarios.
2.  The nested query provides lower performance than other types of queries.

Apart from the preceding limits, the Nested type supports all queries and sorting, and will support statistical aggregation in the future.

