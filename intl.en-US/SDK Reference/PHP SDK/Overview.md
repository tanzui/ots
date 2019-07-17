# Overview {#concept_31755_zh .concept}

## Description {#section_l5w_svf_2fb .section}

This topic describes how to install and use PHP SDK V4.0.0 and later for Table Store. This topic assumes that you have activated Table Store and created an AccessKey ID and an AccessKey Secret.

-   If you have not activated Table Store or want to learn more about Table Store, visit the [Table Store product page](https://www.alibabacloud.com/product/table-store).

-   If you do not have an AccessKey ID and an AccessKey Secret, create an AccessKey in the [Alibaba Cloud console](https://ak-console.aliyun.com/#/accesskey).


## Note {#section_st6_3l9_5q9 .section}

SDK V4.0.0 and later support multiple data versions and time to live \(TTL\). However, these SDK versions are not compatible with SDK V2.x.x.

-   [TTL](../../../../intl.en-US/Functions/Wide Column/Data versions and time to live.md#)

-   [Multiple data versions](../../../../intl.en-US/Functions/Wide Column/Data versions and time to live.md#)

-   [Auto-increment primary key column](intl.en-US/SDK Reference/PHP SDK/Auto-increment primary key column.md#)


## SDK download {#section_qme_k5c_b8k .section}

-   [SDK source code package](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/php/aliyun-tablestore-php-sdk-5.0.0.tar.gz)

-   [GitHub](https://github.com/aliyun/aliyun-tablestore-php-sdk)


For more information about version iterations, see [PHP SDK version history](intl.en-US/SDK Reference/Download SDK/PHP SDK.md#).

## Version {#section_mjo_h4c_h0o .section}

Latest version: 5.0.0

**Compatibility**

For SDKs V4.x.x:

-   Compatible

For SDKs V2.x.x:

-   Incompatible

**Change**

-   5.5.0
    -   New features: Local transactions are supported.
    -   New features: Search indexes are supported.
    -   New features: Secondary indexes are supported.
-   4.1.0

    Basic Stream operations are supported.

-   4.0.0
    -   PHP 5.5 and later are supported, such as 5.5, 5.6, 7.0, 7.1, and 7.2. Only 64-bit PHP systems are supported, and PHP 7 is recommended.
    -   New features: TTL setting is enabled, and the table\_options parameter is added to CreateTable and UpdateTable.
    -   New features: Multiple data versions are supported. The timestamp setting is enabled for PutRow, UpdateRow, DeleteRow, and BatchGetRow. The max\_versions parameter is added to GetRow, GetRange, and BatchGet.
    -   New features: The auto increment feature is enabled for the primary key column. The return\_type parameter is added to operations. The primary\_key parameter is added to the response to return the primary key of the corresponding operation.
    -   Changes: The underlying protobuf library is upgraded to the Google protobuf-php library.
    -   Changes: The value of the primary\_key parameter for each operation is changed to the list type to ensure the order of primary key columns.
    -   Changes: The value of the attribute\_columns parameter for each operation is changed to the list type to support multiple data versions.

