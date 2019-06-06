# Sort {#concept_226989 .concept}

You can use Sort to specify the method of sorting the result when you call the Search operation to search indexes.

The Search Index feature supports multiple sorting methods.

If you have not specified the sorting method for the search, the system applies the IndexSort parameter for the required indexes. By default, Table Store returns the query result in the order of primary key columns.

Table Store supports the following sorting methods:

-   **ScoreSort** 

    Sort the result by relevance score. ScoreSort is applicable to relevance scenarios such as full-text indexing.

-   **PrimaryKeySort** 

    Sort the result by the value of a primary key.

-   **FieldSort** 

    Sort the result by the value of a specified field.

-   **GeoDistanceSort** 

    Sort the result by the distance, radius, from a central point.


