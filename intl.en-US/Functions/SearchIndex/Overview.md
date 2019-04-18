# Overview {#concept_gmr_nyf_ffb .concept}

Searchindex is an advanced index structure. Searchindex supports multiple advanced queries, including the ad-hoc query, fuzzy query, full-text retrieval, sorting, range query, nested query, and spatial query.

## Synchronize indexes {#section_mmb_y1g_ffb .section}

After you enable the Searchindex feature for a table, incremental data written to the table is synchronized to the storage module of Table Store, and then Table Store indicates the successful write operation in the response. Meanwhile, an asynchronous thread reads incremental data from the storage module and creates a Searchindex structure. This asynchronous process has a latency from several milliseconds up to a few seconds, but does not affect the write performance of Table Store.

## Restrictions {#section_elw_gbg_ffb .section}

Compared with the KeyValue storage module of Table Store, Searchindex does not support the following features.

|Feature|Description|
|:------|:----------|
|TTL|You cannot create a Searchindex structure in a table where you have specified the Time To Live \(TTL\) parameter.|
|Max Versions|You cannot create a Searchindex structure in a table where you have specified the Max Versions parameter.|
|Custom versions|You can customize the timestamp whenever writing data to an attribute that allows only one version. If you first write a large version number and then a small version number, the index of the large version number may be overwritten with the index of the small version number.|

## Features {#section_ijt_mgw_hgb .section}

Searchindex provides the following features:

-   Ad-hoc query
-   Fuzzy query
-   Range query
-   Full-text retrieval
-   Spatial query
-   Prefix query
-   Nested query

For more information, see [Features](intl.en-US/Functions/SearchIndex/Features.md).

## Usage {#section_rjw_tgc_3gb .section}

Table Store provides Searchindex API operations and SDKs in multiple languages to allow you easily use Searchindex.

## Scenarios {#section_ov1_sbg_ffb .section}

Searchindex allows Table Store to be applicable to multiple scenarios, such as metadata, spatio-temporal data, time series data, and full-text retrieval.

## Billing {#section_ubc_zfz_lgb .section}

The metering and billing of index tables is separated from primary tables.

