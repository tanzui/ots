# Limits {#concept_mtp_x5s_sfb .concept}

## Mapping {#section_bhn_z5s_sfb .section}

|Item|Maximum value|Description|
|----|-------------|-----------|
|Index fields|200|The number of indexed fields.|
|EnableSortAndAgg fields|100|The number of fields that support sorting and aggregation.|
|Nested levels|1|The maximum number of nested levels.|
|Nested fields|25|The number of nested fields.|
|Total length of primary key columns|1,000|The total length of all primary key columns is 1,000 bytes or less.|
|String length in primary key columns|1,000|The total length of all primary key columns is 1,000 bytes or less.|
|String length in each attribute column \(Keyword index\)|4 KB|None.|
|String length in each attribute column \(Text index\)|2 MB|The same as the length of each attribute column.|
|Query field length in a wildcard query|10|The field of Query type contains 10 characters or fewer.|

## Search {#section_vgg_k1r_ngb .section}

|Item|Maximum value|Description|
|----|-------------|-----------|
|OFFSET + LIMIT|2,000|To display more than the specified number of rows on each page, specify the next\_token parameter.|
|timeout|10s|None.|
|Capacity Unit \(CU\)|100,000|Scanning and analysis requests do not apply. For the parameter out of this limit, submit a ticket.|

## Index {#section_irj_k1r_ngb .section}

|Item|Maximum value|Description|
|:---|:------------|:----------|
|Rate|50,000 rows/s| -   Table Store requires load balancing when writing data to a table for the first time or in an instantaneous and high-concurrency manner.
-   For the parameter out of this limit, submit a ticket.

 |
|Synchronization latency|10s| -   The value is less than 10s when the writing rate is steady.
-   Table Store synchronizes 99% of data in less than one minute.
-   A new index requires initialization for up to one minute.

 |
|Number of rows|10 billion|For the parameter out of this limit, submit a ticket.|
|Total size|10 TB|For the parameter out of this limit, submit a ticket.|

## Other limits {#section_a4d_fws_sfb .section}

|Item|Value|
|:---|:----|
|Applicable regions|China \(Beijing\), China \(Shanghai\), China \(Hangzhou\), China \(Shenzhen\), Singapore, India \(Mumbai\), Hong Kong, and China \(Zhangjiakou-Beijing Winter Olympics\).|

**Note:** If these limits are not applicable to your services, submit a ticket. Describe the scenario, restriction item, requirement, and reason in the ticket. Then, your requirement is considered in follow-up development.

