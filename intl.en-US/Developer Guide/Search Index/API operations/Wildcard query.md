# Wildcard query

When you query data by using wildcard query, the value to be matched can be a string that contains wildcards. Asterisks \(\*\) and question marks \(?\) can be used. An asterisk \(\*\) represents any number of characters. A question marks \(?\) represents a single character. The value to be matched can start with an asterisk \(\*\) or a question mark \(?\). For example, if you search for a string "table\*e", you can retrieve query results such as "tablestore".

## Limits

A string that contains wildcards can be up to 10 bytes in length.

## API operations

You can call WildcardQuery to perform wildcard query.

## Usage

You can use the following Tablestore SDKs to implement wildcard query:

-   Tablestore SDK for Java: [Wildcard query](/intl.en-US/SDK Reference/Java SDK/Search index/Wildcard query.md)
-   Tablestore SDK for Go: [Wildcard query](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Wildcard query.md)
-   Tablestore SDK for Python: [Wildcard query](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Wildcard query.md)
-   Tablestore SDK for Node.js: [Wildcard query](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Wildcard query.md)
-   Tablestore SDK for .NET: [Wildcard query](/intl.en-US/SDK Reference/. .NET SDK/Search index operations/Wildcard query.md)
-   Tablestore SDK for PHP: [Wildcard query](/intl.en-US/SDK Reference/PHP SDK/Search index/Wildcard query.md)

## Parameters

|Parameter|Description|
|---------|-----------|
|FieldName|The name of the column.|
|Value|The string that contains wildcards. The string can be up to 10 bytes in length.|
|Query|The query type. Set the query type to WildcardQuery.|
|TABLE\_NAME|The name of the table.|
|INDEX\_NAME|The name of the search index.|
|ColumnsToGet|Specifies whether to return all columns. The default value of ReturnAll is false, which indicates that not all columns are returned. You can specify the columns to return by using ColumnsToGet. Otherwise, only the primary key columns are returned.

If ReturnAll is set to true, all columns are returned. |
|getTotalCount|Specifies whether to return the total number of matched rows. Query performance is affected when the total number of rows that match the query conditions is returned. |

## Examples

```
/**
 * Search the table for rows where the value of Col_Keyword matches "hang*u".
 * @param client
 */
private static void wildcardQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    WildcardQuery wildcardQuery = new WildcardQuery(); // Set the query type to WildcardQuery.
    wildcardQuery.setFieldName("Col_Keyword");
    wildcardQuery.setValue("hang*u"); // Specify a string that contains one or more wildcard characters in wildcardQuery.
    searchQuery.setQuery(wildcardQuery);
    SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);

    SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);

    System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

