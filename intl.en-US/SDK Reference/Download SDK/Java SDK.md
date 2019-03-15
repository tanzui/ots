# Java SDK {#concept_27351_zh .concept}

The SDK of version 4.0.0 and later provides TTL and Max Versions, and is therefore incompatible with the SDK in Version 2.x.x.

## SDK version: 4.10.2 {#section_kzf_x1c_bhb .section}

Release date: 2019-03-11

Download:[tablestore-4.10.2](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/108096/cn_zh/1552274752739/tablestore-4.10.2-release.zip)

Updates:

-   Remove log4j2 implement and log4j2.xml.
-   TunnelWorker: fix auto retry logic when network condition is too bad.

## SDK version: 4.7.4 {#section_i1n_wlt_4fb .section}

Release date: 2018-09-27

Download: [tablestore-4.7.4-release.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/86690/cn_zh/1540433142084/tablestore-4.7.4-release.zip)

Updates:

-   Added SearchIndex:
    -   Multi-Field Search
    -   Range query
    -   Wildcard search
    -   Nested query
    -   Full-text search
    -   Ranking
-   Added global secondary index.

## SDK version: 4.1.0 {#section_bz2_rwy_lfb .section}

Release date: 11/10/2016

Download: [aliyun\_tablestore\_java\_sdk\_4.1.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1476171171854/aliyun_tablestore_java_sdk_4.1.0.zip)

Updates: The split points of partitions can be get from reponse of DescribeTable.

## SDK version: 4.0.0 { .section}

Release date: 01/08/2016

Download: [aliyun\_tablestore\_java\_sdk\_4.0.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1470024227102/aliyun_tablestore_java_sdk_4.0.0.zip) 

Updates:

-   Provides [Time To Live](../../../../../reseller.en-US/Data Models/Wide Column/Data versions and time to live.md#section_cv2_kyf_cfb).

-   Provides [Max Versions](../../../../../reseller.en-US/Data Models/Wide Column/Data versions and time to live.md#).


## SDK version: 2.2.4 { .section}

Release date: 12/05/2016

Download: [aliyun\_tablestore\_java\_sdk\_2.2.4.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/31713/cn_zh/1463131862644/aliyun-ots-java-sdk-2.2.4.zip) 

Updates:

-   Adds API condition update.

-   Adds filter.


## SDK version 2.1.0 { .section}

Release date: 12/11/2015

Download: [aliyun-ots-java-sdk-2.1.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-java-sdk-2.1.zip) 

Updates:

-   Asynchronous network transmission and performance tuning: When the CPU usage is the same, the QPS is increased by several times.

-   Flexible and easy-to-use asynchronous interfaces: Callback is introduced and Future is returned simultaneously.

-   Unbundled from OSS SDK: The new version only includes code of TableStore SDK. The directory is slightly adjusted.

-   Optimized retry logic: The default retry logic is optimized. An erroneous single row can be retried independently during batch operations. The retry logic custom method is clearer.

-   Optimized log: Logs are recorded for each step from request sending to request receiving. Logs of slow requests are recorded. Logs of the whole chain from SDK to back-end services are recorded using TraceId.

-   OTSWriter interface supporting batch data importing: This interface aims at providing easy-to-use and efficient data importing service for users.

-   Other optimized functions: Toolbox functions for various data classes are enriched and interfaces for data size computing are provided.


**Note:** 

Version 2.1.0 may be slightly incompatible with Version 2.0.4 due to the following limitations:

-   When an old SDK is replaced with the new one, you must change the import paths of a few classes, because the packages of these data classes are adjusted. For example, the package of “ClientConfiguration” is changed from “com.aliyun.openservices” to “com.aliyun.openservices.ots”. The main reason why the package is adjusted is that Table Store SDK is unbundled from OSS SDK. It is therefore more appropriate to put the classes of data commonly used into the package of Table Store.

-   When you no longer use an OTSClient instance \(for example, before the program ends\), call the shutdown method of OTSClient to release the thread and connection resources occupied by the OTSClient object.

-   Names of some configuration items in “ClientConfiguration” are adjusted. For example, a time unit is added as a configuration item.

-   The dependency between packages in the new SDK is changed. For example, “HttpAsyncClient” and “Jodatime” are used. If any problem occurs during SDK running, check whether a conflicting dependency is introduced.


## SDK version 2.0.4 { .section}

Release date: 25/09/2015

Download: [aliyun-ots-java-sdk-2.0.4.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-openservices-OTS-2.0.4.zip) 

