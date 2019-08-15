# Overview {#concept_226919 .concept}

This topic describes the operations, fields, queries, and billing methods of search index.

## SDKs {#section_7pa_z62_vxo .section}

You can use the following SDKs to implement search index.

-   [Java SDK](../../../../intl.en-US/SDK Reference/Java SDK/Preface.md)
-   [Python SDK](../../../../intl.en-US/SDK Reference/Python SDK/Preface.md#)
-   [Go SDK](../../../../intl.en-US/SDK Reference/Go SDK/Preface.md)
-   [Node.js SDK](../../../../intl.en-US/SDK Reference/NodeJS SDK/Preface.md)
-   [.NET SDK](../../../../intl.en-US/SDK Reference/. .NET SDK/Preface.md)

## API operations {#section_r0n_z4k_ej6 .section}

|Action|Operation|Description|
|------|---------|-----------|
|Create|[CreateSearchIndex](intl.en-US/Developer Guide/Search Index/API operations/CreateSearchIndex.md#)|Creates a search index.|
|Describe|[DescribeSearchIndex](intl.en-US/Developer Guide/Search Index/API operations/DescribeSearchIndex.md#)|Queries detailed information of a search index.|
|List|[ListSearchIndex](intl.en-US/Developer Guide/Search Index/API operations/ListSearchIndex.md#)|Queries the list of search indexes.|
|Delete|[DeleteSearchIndex](intl.en-US/Developer Guide/Search Index/API operations/DeleteSearchIndex.md#)|Deletes a specified search index.|
|Search|[Search](#section_26g_8d1_4b9)|Searches for required data.|

## Fields {#section_6wz_wq2_70x .section}

The value of a search index field in Table Store is the value of the field of the same name in the corresponding table. The types of these fields must match each other, as described in the following table.

|Field type in the search index|Field type in the table|Description|
|------------------------------|-----------------------|-----------|
|Long|Integer|64-bit long integers.|
|Double|Double|64-bit long floating-point numbers.|
|Boolean|Boolean|Boolean values.|
|Keyword|String|Character strings that cannot be tokenized.|
|Text|String| Character strings or text that can be tokenized. For more information, see [Tokenization](intl.en-US/Developer Guide/Search Index/API operations/Tokenization.md#).

 |
|Geopoint|String|Geographical coordinates in the `latitude, longitude` format. Example: 35.8,-45.91.|
|[Nested](intl.en-US/Developer Guide/Search Index/API operations/Array and Nested field types.md#)|String|Nested type fields, such as "\{\["a": 1\], \["a": 3\]\}."|

**Notice:** The types in this table must correspond to each other. Otherwise, Table Store discards the data as dirty data. Make sure fields of the Geopoint and Nested types must comply with the formats described in the preceding table. If the formats do not match, Table Store discards the data as dirty data. As a result, the data may be available in the table, but be unavailable in the search index.

Aside from the type attribute, search index fields also have additional attributes.

|Attribute|Type|Option|Description|
|---------|----|------|-----------|
|Index|Boolean|Specifies whether to create an index for a column.| -   True indicates that Table Store creates an inverted index or spatial index for the column.
-   False indicates that Table Store does not create an index for the column.
-   If no indexes exist, you cannot query by the column.

 |
|EnableSortAndAgg|Boolean|Specifies whether to enable sorting and aggregation.| -   True indicates that data can be sorted by using the column.
-   False indicates that data cannot be sorted by using the column.

 |
|Store|Boolean|Specifies whether to store original values in the index.|True indicates that Table Store stores the original values in the column to the index. Therefore, Table Store reads values of the column directly from the index, rather than from the primary table. This optimizes query performance.|
|IsArray|Boolean|Specifies whether the column is an array.| -   True indicates that the column is an array. Data written to the column must be a JSON array, such as \["a","b","c"\].
-   You do not need to explicitly specify this parameter for Nested columns because they are arrays.
-   Array type data can be used in all queries because arrays do not affect queries.

 |

For more information about the attributes that each field type supports, see the following table.

|Type|Index|EnableSortAndAgg|Store|Array|
|----|-----|----------------|-----|-----|
|Long|Supported|Supported|Supported|Supported|
|Double|Supported|Supported|Supported|Supported|
|Boolean|Supported|Supported|Supported|Supported|
|Keyword|Supported|Supported|Supported|Supported|
|Text|Supported|Not supported|Supported|Supported|
|Geopoint|Supported|Supported|Supported|Supported|
|[Nested](intl.en-US/Developer Guide/Search Index/API operations/Array and Nested field types.md#)|Required for child fields.|Required for child fields.|Required for child fields.|Nested fields are arrays.|

## Query parameters and types {#section_26g_8d1_4b9 .section}

You must specify SearchRequest in a query. The following table describes parameters that are included in SearchRequest.

|Parameter|Data type|Description|
|---------|---------|-----------|
|offset|Integer|Specifies the position from which the current query starts.|
|limit|Integer|Specifies the maximum number of items that the current query returns.|
|getTotalCount|Boolean|Specifies whether to return the total number of matched rows. This parameter is set to false by default. A value of true may affect the query performance.|
|[Sort](intl.en-US/Developer Guide/Search Index/API operations/Sort.md#)|Sort|Specifies the field and method for sorting.|
|collapse|Collapse|Specifies the name of the field that you want to collapse in the query result.|
|query|Query|Specifies the type of the current query. The following table lists the query types.|

|Name|Query|Description|
|----|-----|-----------|
|Query by matching all rows|[MatchAllQuery](intl.en-US/Developer Guide/Search Index/API operations/MatchAllQuery.md#)|You can use MatchAllQuery to check the total number of rows.|
|Query by tokenized data|[MatchQuery](intl.en-US/Developer Guide/Search Index/API operations/MatchQuery.md#)|You can use MatchQuery to tokenize the query data, and query the tokenized data. Logical operator OR applies to tokens.|
|Query by matched phrases|[MatchPhraseQuery](intl.en-US/Developer Guide/Search Index/API operations/MatchPhraseQuery.md#)|This query is similar to MatchQuery. The matched tokens must be adjacent to each other in the query data.|
|Query by exact match|[TermQuery](intl.en-US/Developer Guide/Search Index/API operations/TermQuery.md#)|You can use TermQuery to match exact strings. Table Store uses exact matches to query data in a table, and does not tokenize the query data.|
|Query by multiple terms|[TermsQuery](intl.en-US/Developer Guide/Search Index/API operations/TermsQuery.md#)|This query is similar to TermQuery. You can use TermsQuery to match multiple terms, which is similar to the SQL `IN` operator.|
|Query by prefix|[PrefixQuery](intl.en-US/Developer Guide/Search Index/API operations/PrefixQuery.md#)|You can use PrefixQuery to query data in a table by matching a specified prefix.|
|Query by range|[RangeQuery](intl.en-US/Developer Guide/Search Index/API operations/RangeQuery.md#)|You can use RangeQuery to query data within a specified range in a table.|
|Query by wildcards|[WildcardQuery](intl.en-US/Developer Guide/Search Index/API operations/WildcardQuery.md#)|You can use WildcardQuery to query data based on strings that contain one or more wildcards. This query is similar to the SQL LIKE operator.|
|Query by a combination of filtering conditions|[BoolQuery](intl.en-US/Developer Guide/Search Index/API operations/BoolQuery.md#)|You can use BoolQuery to combine multiple filtering conditions by using Logical operators, such as AND, OR, and NOT.|
|Query by matching data within a rectangular geographical area|[GeoBoundingBoxQuery](intl.en-US/Developer Guide/Search Index/API operations/GeoBoundingBoxQuery.md#)|You can use GeoBoundingBoxQuery to specify a rectangular geographical area as a filtering condition in a query. Table Store returns the rows where the value of a field falls within the rectangular geographical area.|
|Query by matching data within a circular geographical area|[GeoDistanceQuery](intl.en-US/Developer Guide/Search Index/API operations/GeoDistanceQuery.md#)|You can use GeoDistanceQuery to specify a circular geographical area as a filtering condition in a query, which consists of a central point and radius. Table Store returns the rows where the value of a field falls within the circular geographical area.|
|Query by matching data within a polygonal geographical area|[GeoPolygonQuery](intl.en-US/Developer Guide/Search Index/API operations/GeoPolygonQuery.md#)|You can use GeoPolygonQuery to specify a polygonal geographical area as a filtering condition in a query. Table Store returns the rows where the value of a field falls within the polygonal geographical area.|

## Pricing {#section_9d2_c4j_gw2 .section}

For more information, see [Billing items and pricing](../../../../intl.en-US/Pricing/Billing items and pricing.md#).

