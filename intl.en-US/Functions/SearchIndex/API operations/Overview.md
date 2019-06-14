# Overview {#concept_226919 .concept}

## SDKs {#section_7pa_z62_vxo .section}

You can use the following SDKs to enable the Search Index feature.

-   [Java SDK](../../../../intl.en-US/SDK Reference/Java SDK/Preface.md)
-   [Python SDK](../../../../intl.en-US/SDK Reference/Python SDK/Preface.md#)
-   [Go SDK](../../../../intl.en-US/SDK Reference/Go SDK/Preface.md)
-   [Node.js SDK](../../../../intl.en-US/SDK Reference/NodeJS SDK/Preface.md)
-   [.NET SDK](../../../../intl.en-US/SDK Reference/. .NET SDK/Preface.md)

## API operations {#section_r0n_z4k_ej6 .section}

|Name|API operation|Description|
|----|-------------|-----------|
|Create|[CreateSearchIndex](intl.en-US/Functions/SearchIndex/API operations/CreateSearchIndex.md#)|Create a Search Index structure.|
|Describe|[DescribeSearchIndex](intl.en-US/Functions/SearchIndex/API operations/DescribeSearchIndex.md#)|Obtain the details of a Search Index structure.|
|List|[ListSearchIndex](intl.en-US/Functions/SearchIndex/API operations/ListSearchIndex.md#)|Retrieve a list of Search Index structures.|
|Delete|[DeleteSearchIndex](intl.en-US/Functions/SearchIndex/API operations/DeleteSearchIndex.md#)|Delete a specified Search Index structure.|
|Search|[Search](#section_26g_8d1_4b9)|Search for required data.|

## Fields {#section_6wz_wq2_70x .section}

The value of a Search Index field in Table Store comes from the value of the field of the same name in the corresponding table. The types of these fields must match each other as described in the following table.

|Field type in the Search Index structure|Field type in the table|Description|
|----------------------------------------|-----------------------|-----------|
|Long|Integer|64-bit long integer.|
|Double|Double|64-bit long floating-point number.|
|Boolean|Boolean|Boolean value.|
|Keyword|String|Character string that can be tokenized.|
|Text|String|Character string or text that can be tokenized.|
|Geopoint|String|Coordinates of a geographic point in the format of "`latitude,longitude`". In this field, the former value indicates the latitude and the latter value indicates the longitude, such as "35.8,-45.91".|
|[Nested](intl.en-US/Functions/SearchIndex/API operations/Nested type.md#)|String|Nested type, such as "\{\["a": 1\], \["a": 3\]\}".|

**Notice:** The types in this table must correspond to each other. Otherwise, Table Store discards the data as dirty data, especially when GeoPoint and Nested types describe data in special formats. If the formats do not match, Table Store also discards the data as dirty data. As a result, the data may be available in the table, but be unavailable in the Search Index structure.

In addition to the type of a Search Index field, you can also specify the attribute of the field:

|Attribute|Type|Name|Description|
|---------|----|----|-----------|
|Index|Boolean|Specifies whether Table Store creates an index for a column.|A value of true specifies that Table Store creates an inverted index or spatial index for the column. A value of false specifies that Table Store does not create any index for the column. If no index exists, you cannot search data by the column.|
|EnableSortAndAgg|Boolean|Specifies whether Table Store enables sorting and aggregation.|A value of true specifies that you can sort data by the column. A value of false specifies that you cannot sort data by the column.|
|Store|Boolean|Specifies whether Table Store stores original values in the index.|A value of true specifies that Table Store stores original values in the column to the index. Therefore, Table Store reads values of the column directly from the index, rather than from the primary table. This optimizes search performance.|
|IsArray|Boolean|Specifies whether the column is an array.|A value of true specifies that the column is an array. Data written to the column must be a JSON array, such as \["a","b","c"\]. A Nested column is an array, and does not require the Array attribute.|

**Note:** For more information about the differences between the Array type and the Nested type, see [Comparison between the Nested type and the Array type](https://yq.aliyun.com/articles/691880).

For more information about the attributes that each field type support, see the following table.

|Type|Index|EnableSortAndAgg|Store|Array|
|----|-----|----------------|-----|-----|
|Long|Yes|Yes|Yes|Yes|
|Double|Yes|Yes|Yes|Yes|
|Boolean|Yes|Yes|Yes|Yes|
|Keyword|Yes|Yes|Yes|Yes|
|Text|Yes|No|Yes|Yes|
|Geopoint|Yes|Yes|Yes|Yes|
|[Nested](intl.en-US/Functions/SearchIndex/API operations/Nested type.md#)|Required for child fields.|Required for child fields.|Required for child fields.|Nested fields are arrays.|

## Query parameters and types {#section_26g_8d1_4b9 .section}

You must specify SearchRequest in a query. SearchRequest includes parameters as described in the following table.

|Parameter|Type|Description|
|---------|----|-----------|
|offset|Integer|The position that the current query starts from.|
|limit|Integer|The maximum number of items that the current query returns.|
|getTotalCount|Boolean|Specifies whether to return the total number of matched rows. This parameter is set to false by default. A value of true may affect the query performance.|
|[Sort](intl.en-US/Functions/SearchIndex/API operations/Sort.md#)|Sort|Specifies the field and method for sorting.|
|collapse|Collapse|Specifies the name of the field that you want to collapse in the query result.|
|query|Query|The type of the current query. The following table lists the query types.|

|Query|Name|Description|
|-----|----|-----------|
|[MatchAllQuery](intl.en-US/Functions/SearchIndex/API operations/MatchAllQuery.md#)|Table Store matches all rows in a query.|You can use MatchAllQuery to check the total number of rows.|
|[MatchQuery](intl.en-US/Functions/SearchIndex/API operations/MatchQuery.md#)|Table Store matches required data in a query.|Table Store tokenizes the query data, and queries the tokenized data. Logical operator OR applies to the tokens.|
|[MatchPhraseQuery](intl.en-US/Functions/SearchIndex/API operations/MatchPhraseQuery.md#)|Table Store matches required phrases in a query.|This query is similar to MatchQuery. But the matched target tokens must be adjacent to each other in the query data.|
|[TermQuery](intl.en-US/Functions/SearchIndex/API operations/TermQuery.md#)|Table Store exactly matches a required term in a query.|You can use TermQuery to exactly match strings. Table Store uses exact matches to query data in a table, and does not tokenize the query data.|
|[TermsQuery](intl.en-US/Functions/SearchIndex/API operations/TermsQuery.md#)|Table Store matches multiple terms in a query.|This query is similar to TermQuery, but supports multiple terms. This query is also similar to the SQL `IN` operator.|
|[PrefixQuery](intl.en-US/Functions/SearchIndex/API operations/PrefixQuery.md#)|Table Store matches a prefix in a query.|You can use PrefixQuery to query data in a table by matching a required prefix.|
|[RangeQuery](intl.en-US/Functions/SearchIndex/API operations/RangeQuery.md#)|Table Store matches targets within a range in a query.|You can use RangeQuery to query data within a specified range in a table.|
|[WildcardQuery](intl.en-US/Functions/SearchIndex/API operations/WildcardQuery.md#)|Table Store matches targets that contain one or more wildcard characters.|You can use WildcardQuery to query data based on a string that contains one or more wildcard characters. This query is similar to the SQL LIKE operator.|
|[BoolQuery](intl.en-US/Functions/SearchIndex/API operations/BoolQuery.md#)|Table Store combines multiple query conditions in a query.|You can use BoolQuery to combine multiple query conditions by using Logical operators AND, OR, and NOT.|
|[GeoBoundingBoxQuery](intl.en-US/Functions/SearchIndex/API operations/GeoBoundingBoxQuery.md#)|Table Store matches targets within a geographic rectangular area in a query.|You can use GeoBoundingBoxQuery to specify a geographic rectangular area as a condition for a query. Table Store returns the rows where the value of a field falls within the geographic rectangular area.|
|[GeoDistanceQuery](intl.en-US/Functions/SearchIndex/API operations/GeoDistanceQuery.md#)|Table Store matches targets within an area by using a central point and a radius in a query.|You can use GeoDistanceQuery to specify a geographic circular area as a condition for a query. This circle is set of all points at a specified distance, radius, from the central point. Table Store returns the rows where the value of a field falls within the geographic circular area.|
|[GeoPolygonQuery](intl.en-US/Functions/SearchIndex/API operations/GeoPolygonQuery.md#)|Table Store matches targets within a geographic polygon area in a query.|You can use GeoPolygonQuery to specify a geographic polygon area as a condition for a query. Table Store returns the rows where the value of a field falls within the geographic polygon area.|

## Billing {#section_9d2_c4j_gw2 .section}

For more information, see [Billing items and pricing](../../../../intl.en-US/Pricing/Billing items and pricing.md#).

