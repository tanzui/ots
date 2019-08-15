# Limits {#concept_mtp_x5s_sfb .concept}

This topic describes the limits on using search index.

## Mapping {#section_bhn_z5s_sfb .section}

|Item|Maximum value|Description|
|----|-------------|-----------|
|Index fields|200|The number of fields that can be indexed.|
|EnableSortAndAgg fields|100|The number of fields that can be sorted and aggregated.|
|Nested levels|1|The number of nested levels.|
|Nested fields|25|The number of nested fields.|
|Total length of primary key columns|1,000|The total length of all primary key columns is up to 1,000 Bytes.|
|Total length of strings in primary key columns|1,000|The total length of strings in all primary key columns is up to 1,000 Bytes.|
|String length in each attribute column \(keyword index\)|4 KB|None|
|String length in each attribute column \(text index\)|2 MB|Same as the length limit of attribute columns in tables.|
|String length of a query that contains wildcards|10|The string length of a query that contains wildcards is up to 10 characters.|

## Search {#section_vgg_k1r_ngb .section}

|Item|Maximum value|Description|
|----|-------------|-----------|
|offset + limit|2,000|To read more than 2000 rows, you must specify the next\_token parameter.|
|timeout|10s|-|
|Capacity unit \(CU\)|100,000| -   Scanning and analysis requests do not apply.
-   If your business requirement exceeds this limit, submit a ticket.

 |
|QPS|100,000| -   The upper limit for lightweight transaction processing is 100,000 queries per second \(QPS\).
-   Each index is allowed to take up to 8-core CPU for analytical queries or text queries because each request takes a long time.
-   The preceding limits are default. If your business requirement exceeds the default limits, submit a ticket.

 |

## Index {#section_irj_k1r_ngb .section}

|Item|Maximum value|Description|
|:---|:------------|:----------|
|Rate|50,000 rows/s| -   Table Store requires several minutes for load balancing when writing data to a table for the first time, or when a large amount of data is required to be written in a short span of time.
-   Text field-based indexing is limited to 10,000 rows per second because this indexing consumes more CPU resources for tokenization.
-   If your business requirement exceeds this limit, submit a ticket.

 |
|Synchronization latency|10s| -   The value is less than 10s when the writing rate is steady.
-   The synchronization latency is within one minute in most cases.
-   New indexes need to be initialized. This process takes up to one minute.

 |
|Number of rows|10 billion|If your business requirement exceeds this limit, submit a ticket.|
|Total size|10 TB|If your business requirement exceeds this limit, submit a ticket.|

## Other limits {#section_a4d_fws_sfb .section}

|Item|Value|
|:---|:----|
|Applicable regions|China \(Beijing\), China \(Shanghai\), China \(Hangzhou\), China \(Shenzhen\), Singapore, India \(Mumbai\), China\(Hong Kong\), and China \(Zhangjiakou-Beijing Winter Olympics\)|
|Regions to be applicable|US \(Silicon Valley\)|

**Note:** If your business requirement exceeds the default limits, submit a ticket. Describe the scenario, limit item, requirement, and reason in the ticket. Your requirement will be considered in future developments.

