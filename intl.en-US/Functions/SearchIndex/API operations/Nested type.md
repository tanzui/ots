# Nested type {#concept_227000 .concept}

A Nested column contains nested documents. One document or one row can contain multiple child documents, and these child documents are saved to the same Nested column. You need to specify the structure of child documents in the Nested column. The structure includes the fields of the child documents and the element of each field. The following example defines the format of a Nested column in Java.

``` {#codeblock_qs4_it3_3rg}
// Specify the FieldSchema class for the child documents.
List<FieldSchema> subFieldSchemas = new ArrayList<FieldSchema>(); 
subFieldSchemas.add(new FieldSchema("tagName", FieldType.KEYWORD)
    .setIndex(true).setEnableSortAndAgg(true));
subFieldSchemas.add(new FieldSchema("score", FieldType.DOUBLE)
    .setIndex(true).setEnableSortAndAgg(true));

// Set FieldSchema of the child documents as subfieldSchemas of the NESTED column.
FieldSchema nestedFieldSchema = new FieldSchema("tags", FieldType.NESTED)
    .setSubFieldSchemas(subFieldSchemas);
```

This example defines the format of a Nested column named tags. The child documents include two fields: one is a KEYWORD field named tagName and the other is a DOUBLE field named score.

Table Store writes Nested columns as strings or JSON arrays to the primary table. The following example shows the data format of a Nested column.

``` {#codeblock_wps_s29_38w}
[{"tagName":"tag1", "score":0.8}, {"tagName":"tag2", "score":0.2}]
```

This column contains two child documents. Even if a column contains only one child document, you must provide the strings as JSON arrays.

The Nested type has the following restrictions:

-   Nested indexes do not support the IndexSort feature. But IndexSort can improve query performance in many scenarios.
-   The Nested query provides lower performance than other types of queries.

